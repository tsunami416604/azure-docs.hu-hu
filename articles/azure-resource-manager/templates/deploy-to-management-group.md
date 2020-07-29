---
title: Erőforrások központi telepítése a felügyeleti csoportba
description: Ismerteti, hogyan lehet erőforrásokat telepíteni a felügyeleti csoport hatókörében egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: a17387aef4d35c042d1fe0b02f1c6fd447e4a918
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321802"
---
# <a name="create-resources-at-the-management-group-level"></a>Erőforrások létrehozása a felügyeleti csoport szintjén

Amikor a szervezete leállt, üzembe helyezhet egy Azure Resource Manager sablont (ARM-sablon) az erőforrások létrehozásához a felügyeleti csoport szintjén. Előfordulhat például, hogy meg kell adnia és hozzá kell rendelnie egy felügyeleti csoport [szabályzatait](../../governance/policy/overview.md) vagy [szerepköralapú hozzáférés-vezérlését](../../role-based-access-control/overview.md) . A felügyeleti csoport szintű sablonok használatával a felügyeleti csoport szintjén deklarálhatja a házirendeket, és rendelhet hozzá szerepköröket.

## <a name="supported-resources"></a>Támogatott erőforrások

Nem minden erőforrástípust lehet központilag telepíteni a felügyeleti csoport szintjére. Ez a szakasz felsorolja, hogy milyen típusú erőforrástípusok támogatottak.

Az Azure-tervezetek esetében használja az alábbiakat:

* [leletek](/azure/templates/microsoft.blueprint/blueprints/artifacts)
* [tervrajzok](/azure/templates/microsoft.blueprint/blueprints)
* [blueprintAssignments](/azure/templates/microsoft.blueprint/blueprintassignments)
* [verziók](/azure/templates/microsoft.blueprint/blueprints/versions)

Azure-szabályzatok esetén használja a következőt:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [szervizelések](/azure/templates/microsoft.policyinsights/remediations)

Szerepköralapú hozzáférés-vezérléshez használja a következőt:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

Az előfizetések vagy erőforráscsoportok számára üzembe helyezett beágyazott sablonok esetében használja a következőt:

* [központi telepítések](/azure/templates/microsoft.resources/deployments)

Az erőforrások kezeléséhez használja a következőt:

* [Címkék](/azure/templates/microsoft.resources/tags)

### <a name="schema"></a>Séma

A felügyeleti csoportok központi telepítéséhez használt séma eltér az erőforráscsoport-telepítések sémájától.

Sablonok esetén használja a következőt:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

A paraméterérték sémája megegyezik az összes központi telepítési hatókörnél. A paraméter fájljaihoz használja a következőt:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Üzembe helyezési parancsok

A felügyeleti csoportok központi telepítésére vonatkozó parancsok eltérnek az erőforráscsoport-telepítések parancsaitól.

Az Azure CLI esetében használja az [az Deployment mg Create](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Azure PowerShell esetén használja a [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment).

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

REST API esetén használja a [központi telepítéseket – hozzon létre egy felügyeleti csoport hatókörét](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Központi telepítés helye és neve

Felügyeleti csoport szintű központi telepítések esetén meg kell adnia egy helyet a központi telepítéshez. A központi telepítés helye nem azonos a telepített erőforrások helyétől. A központi telepítés helye határozza meg, hogy hol tárolja a telepítési adatforrásokat.

Megadhatja a központi telepítés nevét, vagy használhatja az alapértelmezett központi telepítési nevet is. Az alapértelmezett név a sablonfájl neve. Egy **azuredeploy.js** nevű sablon üzembe helyezése például a **azuredeploy**alapértelmezett központi telepítési nevét hozza létre.

Az egyes központi telepítési nevek esetében a hely nem módosítható. A központi telepítést nem lehet az egyik helyen létrehozni, ha egy másik helyen már van ilyen nevű üzemelő példány. Ha a hibakódot kapja `InvalidDeploymentLocation` , használjon más nevet vagy ugyanazt a helyet, mint az adott név előző üzembe helyezését.

## <a name="deployment-scopes"></a>Központi telepítési hatókörök

Felügyeleti csoport telepítésekor a központi telepítési parancsban vagy a bérlő más felügyeleti csoportjaiban megadott felügyeleti csoportot is megcélozhatja. Egy felügyeleti csoportban lévő előfizetéseket vagy erőforráscsoportokat is megcélozhat. A sablont telepítő felhasználónak hozzáféréssel kell rendelkeznie a megadott hatókörhöz.

A sablon erőforrások szakaszában meghatározott erőforrások a központi telepítési paranccsal lesznek alkalmazva a felügyeleti csoportra.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        management-group-level-resources
    ],
    "outputs": {}
}
```

Egy másik felügyeleti csoport megcélzásához adjon hozzá egy beágyazott központi telepítést, és adja meg a `scope` tulajdonságot.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mgName": {
            "type": "string"
        }
    },
    "variables": {
        "mgId": "[concat('Microsoft.Management/managementGroups/', parameters('mgName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2019-10-01",
            "name": "nestedDeployment",
            "scope": "[variables('mgId')]",
            "location": "eastus",
            "properties": {
                "mode": "Incremental",
                "template": {
                    nested-template
                }
            }
        }
    ],
    "outputs": {}
}
```

A felügyeleti csoporton belüli előfizetés megcélzásához használjon beágyazott központi telepítést és a `subscriptionId` tulajdonságot. Ha az adott előfizetésen belül egy erőforráscsoportot szeretne célozni, adjon hozzá egy másik beágyazott üzembe helyezést és a `resourceGroup` tulajdonságot.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "westus2",
      "subscriptionId": "00000000-0000-0000-0000-000000000000",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedRG",
              "resourceGroup": "rg2",
              "properties": {
                "mode": "Incremental",
                "template": {
                  nested-template
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="use-template-functions"></a>A Template functions használata

A felügyeleti csoportok központi telepítéséhez néhány fontos szempontot kell figyelembe venni a sablon funkcióinak használatakor:

* A [resourceGroup ()](template-functions-resource.md#resourcegroup) függvény **nem** támogatott.
* Az [előfizetés ()](template-functions-resource.md#subscription) függvény **nem** támogatott.
* A [Reference ()](template-functions-resource.md#reference) és a [List ()](template-functions-resource.md#list) függvények támogatottak.
* A [resourceId ()](template-functions-resource.md#resourceid) függvény támogatott. Ezzel a beállítással lekérheti a felügyeleti csoport szintjén üzemelő példányokon használt erőforrások erőforrás-AZONOSÍTÓját. Ne adjon meg értéket az erőforráscsoport paraméter számára.

  Ha például egy házirend-definíció erőforrás-AZONOSÍTÓját szeretné lekérni, használja a következőt:
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  A visszaadott erőforrás-azonosító formátuma a következő:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="azure-policy"></a>Azure Policy

### <a name="define-policy"></a>Házirend megadása

Az alábbi példa bemutatja, hogyan [határozhat meg](../../governance/policy/concepts/definition-structure.md) házirendet a felügyeleti csoport szintjén.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyDefinitions",
      "apiVersion": "2018-05-01",
      "name": "locationpolicy",
      "properties": {
        "policyType": "Custom",
        "parameters": {},
        "policyRule": {
          "if": {
            "field": "location",
            "equals": "northeurope"
          },
          "then": {
            "effect": "deny"
          }
        }
      }
    }
  ]
}
```

### <a name="assign-policy"></a>Házirend kiosztása

A következő példa egy meglévő szabályzat-definíciót rendel hozzá a felügyeleti csoporthoz. Ha a házirend paramétereket fogad, adja meg őket objektumként. Ha a házirend nem fogad paramétereket, használja az alapértelmezett üres objektumot.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "policyDefinitionID": {
      "type": "string"
    },
    "policyName": {
      "type": "string"
    },
    "policyParameters": {
      "type": "object",
      "defaultValue": {}
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Authorization/policyAssignments",
      "apiVersion": "2018-03-01",
      "name": "[parameters('policyName')]",
      "properties": {
        "policyDefinitionId": "[parameters('policyDefinitionID')]",
        "parameters": "[parameters('policyParameters')]"
      }
    }
  ]
}
```

## <a name="deploy-to-subscription-and-resource-group"></a>Üzembe helyezés az előfizetésben és az erőforráscsoporthoz

A felügyeleti csoport szintjén üzemelő példányok esetében a felügyeleti csoporton belül is megcélozhat egy előfizetést. A következő példa létrehoz egy erőforráscsoportot egy előfizetésen belül, és üzembe helyez egy Storage-fiókot az adott erőforráscsoporthoz.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "nestedsubId": {
      "type": "string"
    },
    "nestedRG": {
      "type": "string"
    },
    "storageAccountName": {
      "type": "string"
    },
    "nestedLocation": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "nestedSub",
      "location": "[parameters('nestedLocation')]",
      "subscriptionId": "[parameters('nestedSubId')]",
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
          },
          "variables": {
          },
          "resources": [
            {
              "type": "Microsoft.Resources/resourceGroups",
              "apiVersion": "2020-06-01",
              "name": "[parameters('nestedRG')]",
              "location": "[parameters('nestedLocation')]",
            },
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2020-06-01",
              "name": "nestedSubRG",
              "resourceGroup": "[parameters('nestedRG')]",
              "dependsOn": [
                "[parameters('nestedRG')]"
              ],
              "properties": {
                "mode": "Incremental",
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "resources": [
                    {
                      "type": "Microsoft.Storage/storageAccounts",
                      "apiVersion": "2019-04-01",
                      "name": "[parameters('storageAccountName')]",
                      "location": "[parameters('nestedLocation')]",
                      "sku": {
                        "name": "Standard_LRS"
                      }
                    }
                  ]
                }
              }
            }
          ]
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

* A szerepkörök hozzárendelésével kapcsolatos további tudnivalókért lásd: [Az Azure-erőforrásokhoz való hozzáférés kezelése RBAC és Azure Resource Manager sablonok használatával](../../role-based-access-control/role-assignments-template.md).
* A Azure Security Center munkaterület-beállításainak központi telepítésére példát a következő témakörben talál: [deployASCwithWorkspaceSettings.js](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* A sablonokat [előfizetési szinten](deploy-to-subscription.md) és [bérlői szinten](deploy-to-tenant.md)is üzembe helyezheti.
