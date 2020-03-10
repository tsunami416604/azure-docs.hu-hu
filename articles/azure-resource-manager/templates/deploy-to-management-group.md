---
title: Erőforrások központi telepítése a felügyeleti csoportba
description: Ismerteti, hogyan lehet erőforrásokat telepíteni a felügyeleti csoport hatókörében egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: dc46762755718c798b4a7eed6f2dc6b8afce9b98
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2020
ms.locfileid: "78942752"
---
# <a name="create-resources-at-the-management-group-level"></a>Erőforrások létrehozása a felügyeleti csoport szintjén

A szervezete leállása esetén előfordulhat, hogy meg kell adnia és hozzá kell rendelnie egy felügyeleti csoport [szabályzatait](../../governance/policy/overview.md) vagy [szerepköralapú hozzáférés-vezérlését](../../role-based-access-control/overview.md) . A felügyeleti csoport szintű sablonok használatával a felügyeleti csoport szintjén deklarálhatja a házirendeket, és rendelhet hozzá szerepköröket.

## <a name="supported-resources"></a>Támogatott erőforrások

A felügyeleti csoport szintjén a következő erőforrástípusok helyezhetők üzembe:

* [központi telepítések](/azure/templates/microsoft.resources/deployments) – előfizetések vagy erőforráscsoportok számára üzembe helyezett beágyazott sablonok esetén.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

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

Azure PowerShell esetén használja a [New-AzManagementGroupDeployment](/powershell/module/az.resources/new-azmanagementgroupdeployment). 

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -ManagementGroupId "myMG" `
  -Location "West US" `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json
```

REST API esetén használja a [központi telepítéseket – hozzon létre egy felügyeleti csoport hatókörét](/rest/api/resources/deployments/createorupdateatmanagementgroupscope).

## <a name="deployment-location-and-name"></a>Központi telepítés helye és neve

Felügyeleti csoport szintű központi telepítések esetén meg kell adnia egy helyet a központi telepítéshez. A központi telepítés helye nem azonos a telepített erőforrások helyétől. A központi telepítés helye határozza meg, hogy hol tárolja a telepítési adatforrásokat.

Megadhatja a központi telepítés nevét, vagy használhatja az alapértelmezett központi telepítési nevet is. Az alapértelmezett név a sablonfájl neve. Egy **azuredeploy. JSON** nevű sablon üzembe helyezése például létrehoz egy alapértelmezett központi telepítési nevet a **azuredeploy**.

Az egyes központi telepítési nevek esetében a hely nem módosítható. A központi telepítést nem lehet az egyik helyen létrehozni, ha egy másik helyen már van ilyen nevű üzemelő példány. Ha `InvalidDeploymentLocation`hibakódot kap, vagy más nevet vagy azonos helyet használ a korábbi üzembe helyezéshez a névben.

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

## <a name="create-policies"></a>Szabályzatok létrehozása

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

## <a name="template-sample"></a>Sablon minta

* [Hozzon létre egy erőforráscsoportot, egy házirendet és egy házirend-hozzárendelést](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json).

## <a name="next-steps"></a>További lépések

* A szerepkörök hozzárendelésével kapcsolatos további tudnivalókért lásd: [Az Azure-erőforrásokhoz való hozzáférés kezelése RBAC és Azure Resource Manager sablonok használatával](../../role-based-access-control/role-assignments-template.md).
* A Azure Security Center munkaterület-beállításainak üzembe helyezésére példát a következő témakörben talál: [deployASCwithWorkspaceSettings. JSON](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* A sablonokat [előfizetési szinten](deploy-to-subscription.md) és [bérlői szinten](deploy-to-tenant.md)is üzembe helyezheti.
