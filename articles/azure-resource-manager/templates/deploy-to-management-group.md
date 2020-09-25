---
title: Erőforrások központi telepítése a felügyeleti csoportba
description: Ismerteti, hogyan lehet erőforrásokat telepíteni a felügyeleti csoport hatókörében egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 0c5ed8d2427a9e0329db6ebd7f0aa48aa4912a48
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91284822"
---
# <a name="create-resources-at-the-management-group-level"></a>Erőforrások létrehozása a felügyeleti csoport szintjén

Amikor a szervezete leállt, üzembe helyezhet egy Azure Resource Manager sablont (ARM-sablon) az erőforrások létrehozásához a felügyeleti csoport szintjén. Előfordulhat például, hogy meg kell adnia és hozzá kell rendelnie egy felügyeleti csoport [szabályzatait](../../governance/policy/overview.md) vagy az [Azure szerepköralapú hozzáférés-vezérlését (Azure RBAC)](../../role-based-access-control/overview.md) . A felügyeleti csoport szintű sablonok használatával a felügyeleti csoport szintjén deklarálhatja a házirendeket, és rendelhet hozzá szerepköröket.

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

## <a name="schema"></a>Séma

A felügyeleti csoportok központi telepítéséhez használt séma eltér az erőforráscsoport-telepítések sémájától.

Sablonok esetén használja a következőt:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

A paraméterérték sémája megegyezik az összes központi telepítési hatókörnél. A paraméter fájljaihoz használja a következőt:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-scopes"></a>Központi telepítési hatókörök

Felügyeleti csoportba való telepítéskor megcélozhatja a telepítési parancsban megadott felügyeleti csoportot, vagy kijelölhet egy másik felügyeleti csoportot is a bérlőben.

A sablon erőforrások szakaszában meghatározott erőforrások a központi telepítési paranccsal lesznek alkalmazva a felügyeleti csoportra.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-mg.json" highlight="5":::

Egy másik felügyeleti csoport megcélzásához adjon hozzá egy beágyazott központi telepítést, és adja meg a `scope` tulajdonságot. Állítsa a `scope` tulajdonságot értékre a következő formátumban: `Microsoft.Management/managementGroups/<mg-name>` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/scope-mg.json" highlight="10,17,22":::

Egy felügyeleti csoportban lévő előfizetéseket vagy erőforráscsoportokat is megcélozhat. A sablont telepítő felhasználónak hozzáféréssel kell rendelkeznie a megadott hatókörhöz.

A felügyeleti csoporton belüli előfizetés megcélzásához használjon beágyazott központi telepítést és a `subscriptionId` tulajdonságot.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-subscription.json" highlight="10,18":::

Ha az adott előfizetésen belül egy erőforráscsoportot szeretne célozni, adjon hozzá egy másik beágyazott üzembe helyezést és a `resourceGroup` tulajdonságot.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/mg-to-resource-group.json" highlight="10,21,25":::

Ha egy felügyeleti csoport központi telepítését szeretné használni az előfizetésen belüli erőforráscsoport létrehozásához és a Storage-fiók üzembe helyezéséhez az adott erőforráscsoporthoz, tekintse meg az [előfizetés és az erőforráscsoport üzembe helyezése](#deploy-to-subscription-and-resource-group)című témakört.

## <a name="deployment-commands"></a>Üzembe helyezési parancsok

A felügyeleti csoportok központi telepítésére vonatkozó parancsok eltérnek az erőforráscsoport-telepítések parancsaitól.

Az Azure CLI esetében használja az [az Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create):

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

## <a name="use-template-functions"></a>A Template functions használata

A felügyeleti csoportok központi telepítéséhez néhány fontos szempontot kell figyelembe venni a sablon funkcióinak használatakor:

* A [resourceGroup ()](template-functions-resource.md#resourcegroup) függvény **nem** támogatott.
* Az [előfizetés ()](template-functions-resource.md#subscription) függvény **nem** támogatott.
* A [Reference ()](template-functions-resource.md#reference) és a [List ()](template-functions-resource.md#list) függvények támogatottak.
* Ne használja a [resourceId ()](template-functions-resource.md#resourceid) függvényt a felügyeleti csoportba központilag telepített erőforrásokhoz.

  Ehelyett használja a [extensionResourceId ()](template-functions-resource.md#extensionresourceid) függvényt a felügyeleti csoport bővítményeiként megvalósított erőforrásokhoz. A felügyeleti csoportba központilag telepített egyéni házirend-definíciók a felügyeleti csoport bővítményei.

  Ha egy egyéni házirend-definíció erőforrás-AZONOSÍTÓját szeretné lekérni a felügyeleti csoport szintjén, használja a következőt:
  
  ```json
  "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

  Használja a [tenantResourceId](template-functions-resource.md#tenantresourceid) függvényt a felügyeleti csoporton belül elérhető bérlői erőforrásokhoz. A beépített szabályzat-definíciók a bérlői szintű erőforrások.

  Egy beépített szabályzat-definíció erőforrás-AZONOSÍTÓjának lekéréséhez használja a következőt:
  
  ```json
  "policyDefinitionId": "[tenantResourceId('Microsoft.Authorization/policyDefinitions', parameters('policyDefinitionID'))]"
  ```

## <a name="azure-policy"></a>Azure Policy

Az alábbi példa bemutatja, hogyan [határozhat meg](../../governance/policy/concepts/definition-structure.md) egy házirendet a felügyeleti csoport szintjén, és hogyan rendelheti hozzá.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "targetMG": {
            "type": "string",
            "metadata": {
                "description": "Target Management Group"
            }
        },
        "allowedLocations": {
            "type": "array",
            "defaultValue": [
                "australiaeast",
                "australiasoutheast",
                "australiacentral"
            ],
            "metadata": {
                "description": "An array of the allowed locations, all other locations will be denied by the created policy."
            }
        }
    },
    "variables": {
        "mgScope": "[tenantResourceId('Microsoft.Management/managementGroups', parameters('targetMG'))]",
        "policyDefinition": "LocationRestriction"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "[variables('policyDefinition')]",
            "apiVersion": "2019-09-01",
            "properties": {
                "policyType": "Custom",
                "mode": "All",
                "parameters": {
                },
                "policyRule": {
                    "if": {
                        "not": {
                            "field": "location",
                            "in": "[parameters('allowedLocations')]"
                        }
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2019-09-01",
            "dependsOn": [
                "[variables('policyDefinition')]"
            ],
            "properties": {
                "scope": "[variables('mgScope')]",
                "policyDefinitionId": "[extensionResourceId(variables('mgScope'), 'Microsoft.Authorization/policyDefinitions', variables('policyDefinition'))]"
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

## <a name="next-steps"></a>Következő lépések

* A szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [Azure szerepkör-hozzárendelések hozzáadása Azure Resource Manager-sablonok használatával](../../role-based-access-control/role-assignments-template.md).
* A Azure Security Center munkaterület-beállításainak központi telepítésére példát a következő témakörben talál: [deployASCwithWorkspaceSettings.js](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* A sablonokat [előfizetési szinten](deploy-to-subscription.md) és [bérlői szinten](deploy-to-tenant.md)is üzembe helyezheti.
