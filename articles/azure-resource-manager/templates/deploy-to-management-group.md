---
title: Erőforrások telepítése a felügyeleti csoportba
description: Bemutatja, hogyan helyezhet üzembe erőforrásokat a felügyeleti csoport hatókörén egy Azure Resource Manager-sablonban.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 863d1330412fa238b820eb0f1f05351fc723de6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460313"
---
# <a name="create-resources-at-the-management-group-level"></a>Erőforrások létrehozása a felügyeleti csoport szintjén

A szervezet érlelődése során előfordulhat, hogy [házirendeket](../../governance/policy/overview.md) vagy [szerepköralapú hozzáférés-vezérléseket](../../role-based-access-control/overview.md) kell definiálnia és hozzárendelnie egy felügyeleti csoporthoz. A felügyeleticsoport-szintű sablonokkal deklaratív módon alkalmazhatházirendeket, és szerepköröket rendelhet hozzá a felügyeleti csoport szintjén.

## <a name="supported-resources"></a>Támogatott erőforrások

A felügyeleti csoport szintjén a következő erőforrástípusokat telepítheti:

* [központi telepítések](/azure/templates/microsoft.resources/deployments) – az előfizetések vagy erőforráscsoportok üzembe helyezésére szolgáló beágyazott sablonok.
* [házirend-hozzárendelések](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions (policyDefinitions)](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions (policySetDefinitions)](/azure/templates/microsoft.authorization/policysetdefinitions)
* [szerepkör-hozzárendelések](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions (szerepkördefiníciók)](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Séma

A felügyeleti csoport központi telepítéseihez használt séma eltér az erőforráscsoport-telepítések sémájáttól.

Sablonok esetén használja a következőket:

```json
https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#
```

A paraméterfájl sémája minden telepítési hatóköresetében megegyezik. Paraméterfájlok esetén használja a következőket:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="deployment-commands"></a>Telepítési parancsok

A felügyeleti csoportok központi telepítéséhez szükséges parancsok eltérnek az erőforráscsoport-telepítések parancsaitól.

Az Azure CLI esetében használja [az üzembe helyezési mg create:For](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)Azure CLI, use az deployment mg create:

```azurecli-interactive
az deployment mg create \
  --name demoMGDeployment \
  --location WestUS \
  --management-group-id myMG \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

Az Azure PowerShell esetén használja a [New-AzManagementGroupDeployment szolgáltatást.](/powershell/module/az.resources/new-azmanagementgroupdeployment)

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name demoMGDeployment `
  -Location "West US" `
  -ManagementGroupId "myMG" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/management-level-deployment/azuredeploy.json"
```

REST API-hoz használja [a Központi telepítéseket – Létrehozás a felügyeleti csoport hatókörén.](/rest/api/resources/deployments/createorupdateatmanagementgroupscope)

## <a name="deployment-location-and-name"></a>A telepítés helye és neve

A felügyeleti csoport szintű telepítések esetén meg kell adnia a központi telepítés helyét. A központi telepítés helye nem áll meg a telepített erőforrások helyétől. A központi telepítési hely határozza meg, hogy hol kell tárolni a telepítési adatokat.

Megadhat nevet a központi telepítésnek, vagy használhatja az alapértelmezett központi telepítés nevét. Az alapértelmezett név a sablonfájl neve. Például egy **azuredeploy.json** nevű sablon üzembe helyezése létrehoz egy alapértelmezett üzembe helyezési nevet az **azuredeploy.**

Az egyes központi telepítés neve immutalitása nem módosítható. Nem hozhat létre központi telepítést egy helyen, ha egy másik helyen azonos nevű meglévő központi telepítés van. Ha a hibakódot `InvalidDeploymentLocation`kapja, használjon másik nevet vagy ugyanazon a helyet, mint az előző központi telepítésaz adott névhez.

## <a name="use-template-functions"></a>Sablonfüggvények használata

A felügyeleti csoport telepítések, vannak néhány fontos szempont, amikor a sablon függvények használata:

* A [resourceGroup()](template-functions-resource.md#resourcegroup) függvény **nem** támogatott.
* Az [subscription()](template-functions-resource.md#subscription) függvény **nem** támogatott.
* [A(z) reference()](template-functions-resource.md#reference) és [a list()](template-functions-resource.md#list) függvények támogatottak.
* A [resourceId()](template-functions-resource.md#resourceid) függvény támogatott. Segítségével lekéri a felügyeleti csoport szintjén telepített telepítések erőforrás-azonosítóját. Ne adjon meg értéket az erőforráscsoport paraméterhez.

  Ha például egy házirend-definíció erőforrásazonosítóját szeretné lekérni, használja a következőket:
  
  ```json
  resourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  A visszaadott erőforrás-azonosító formátuma a következő:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-policies"></a>Szabályzatok létrehozása

### <a name="define-policy"></a>Házirend meghatározása

A következő példa [bemutatja,](../../governance/policy/concepts/definition-structure.md) hogyan definiálhatja a szabályzatot a felügyeleti csoport szintjén.

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

### <a name="assign-policy"></a>Házirend hozzárendelése

A következő példa egy meglévő házirend-definíciót rendel a felügyeleti csoporthoz. Ha a házirend paramétereket vesz igénybe, adja meg azokat objektumként. Ha a házirend nem vesz igénybe paramétereket, használja az alapértelmezett üres objektumot.

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

## <a name="template-sample"></a>Sablonminta

* [Hozzon létre egy erőforráscsoportot, egy házirendet és egy házirend-hozzárendelést.](https://github.com/Azure/azure-docs-json-samples/blob/master/management-level-deployment/azuredeploy.json)

## <a name="next-steps"></a>További lépések

* A szerepkörök hozzárendeléséről az [Azure-erőforrásokhoz való hozzáférés kezelése RBAC és Azure Resource Manager-sablonok használatával](../../role-based-access-control/role-assignments-template.md)című témakörben olvashat.
* Az Azure Security Center munkaterületi beállításainak üzembe helyezését lásd: [deployASCwithWorkspaceSettings.json.](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)
* A sablonokat [előfizetési és](deploy-to-subscription.md) [bérlői szinten](deploy-to-tenant.md)is telepítheti.
