---
title: Erőforrások üzembe helyezése a bérlőre
description: Bemutatja, hogyan helyezhet i erőforrásokat a bérlői hatókörben egy Azure Resource Manager-sablonban.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: fcdfc5b1c4333a0d7eeec80a09ad85579a1f8b77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460262"
---
# <a name="create-resources-at-the-tenant-level"></a>Erőforrások létrehozása bérlői szinten

A szervezet érlelődése, előfordulhat, hogy [definiálnia](../../governance/policy/overview.md) és hozzákell rendelnie a szabályzatokat vagy [a szerepköralapú hozzáférés-vezérléseket](../../role-based-access-control/overview.md) az Azure AD-bérlő között. A bérlői szintű sablonok segítségével deklaratív módon alkalmazhatházirendeket, és globális szinten rendelhet hozzá szerepköröket.

## <a name="supported-resources"></a>Támogatott erőforrások

A következő erőforrástípusokat telepítheti bérlői szinten:

* [központi telepítések](/azure/templates/microsoft.resources/deployments) – a felügyeleti csoportokra vagy előfizetésekbe üzembe helyező beágyazott sablonokhoz.
* [házirend-hozzárendelések](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions (policyDefinitions)](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions (policySetDefinitions)](/azure/templates/microsoft.authorization/policysetdefinitions)
* [szerepkör-hozzárendelések](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions (szerepkördefiníciók)](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Séma

A bérlői központi telepítésekhez használt séma eltér az erőforráscsoport-telepítések sémájátétól.

Sablonok esetén használja a következőket:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

A paraméterfájl sémája minden telepítési hatóköresetében megegyezik. Paraméterfájlok esetén használja a következőket:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
```

## <a name="required-access"></a>Szükséges hozzáférés

A sablont üzembe helyező egyszerű telepítéshez engedéllyel kell rendelkeznie a bérlői hatókörben lévő erőforrások létrehozásához. A főtagnak engedéllyel kell`Microsoft.Resources/deployments/*`rendelkeznie a telepítési műveletek ( ) végrehajtásához és a sablonban definiált erőforrások létrehozásához. Például egy felügyeleti csoport létrehozásához a megbízónak közreműködői engedéllyel kell rendelkeznie a bérlői hatókörben. Szerepkör-hozzárendelések létrehozásához a megbízónak tulajdonosi engedéllyel kell rendelkeznie.

Az Azure Active Directory globális rendszergazdája nem rendelkezik automatikusan szerepkörök hozzárendelésére vonatkozó engedéllyel. A sablontelepítések bérlői hatókörben történő engedélyezéséhez a globális rendszergazdának a következő lépéseket kell tennie:

1. Növelje a fiókhoz való hozzáférést, hogy a globális rendszergazda szerepköröket rendelhet. További információ: [Access for all Azure subscriptions and management groups](../../role-based-access-control/elevate-access-global-admin.md).

1. Tulajdonos vagy közreműködő hozzárendelése a sablonok üzembe helyezéséhez szükséges egyszerű taghoz.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

A főtag most már rendelkezik a sablon telepítéséhez szükséges engedélyekkel.

## <a name="deployment-commands"></a>Telepítési parancsok

A bérlői központi telepítések parancsai eltérnek az erőforráscsoport-telepítések parancsaitól.

Az Azure CLI esetében használja [az üzembe helyezési bérlői létrehozását:](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

Az Azure PowerShell esetén használja a [New-AzTenantDeployment szolgáltatást.](/powershell/module/az.resources/new-aztenantdeployment)

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json"
```

REST API-hoz használja [a központi telepítéseket – létrehozás vagy frissítés a bérlői hatókörben.](/rest/api/resources/deployments/createorupdateattenantscope)

## <a name="deployment-location-and-name"></a>A telepítés helye és neve

A bérlői szintű központi telepítések esetén meg kell adnia a központi telepítés helyét. A központi telepítés helye nem áll meg a telepített erőforrások helyétől. A központi telepítési hely határozza meg, hogy hol kell tárolni a telepítési adatokat.

Megadhat nevet a központi telepítésnek, vagy használhatja az alapértelmezett központi telepítés nevét. Az alapértelmezett név a sablonfájl neve. Például egy **azuredeploy.json** nevű sablon üzembe helyezése létrehoz egy alapértelmezett üzembe helyezési nevet az **azuredeploy.**

Az egyes központi telepítés neve immutalitása nem módosítható. Nem hozhat létre központi telepítést egy helyen, ha egy másik helyen azonos nevű meglévő központi telepítés van. Ha a hibakódot `InvalidDeploymentLocation`kapja, használjon másik nevet vagy ugyanazon a helyet, mint az előző központi telepítésaz adott névhez.

## <a name="use-template-functions"></a>Sablonfüggvények használata

A bérlői telepítések esetében a sablonfüggvények használata során néhány fontos szempont ot figyelembe kell venni:

* A [resourceGroup()](template-functions-resource.md#resourcegroup) függvény **nem** támogatott.
* Az [subscription()](template-functions-resource.md#subscription) függvény **nem** támogatott.
* [A(z) reference()](template-functions-resource.md#reference) és [a list()](template-functions-resource.md#list) függvények támogatottak.
* A [tenantResourceId()](template-functions-resource.md#tenantresourceid) függvény használatával lekéri a bérlői szinten üzembe helyezett erőforrások erőforrás-azonosítójának lekérni.

  Ha például egy házirend-definíció erőforrásazonosítóját szeretné lekérni, használja a következőket:
  
  ```json
  tenantResourceId('Microsoft.Authorization/policyDefinitions/', parameters('policyDefinition'))
  ```
  
  A visszaadott erőforrás-azonosító formátuma a következő:
  
  ```json
  /providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
  ```

## <a name="create-management-group"></a>Felügyeleti csoport létrehozása

A [következő sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/new-mg) létrehoz egy felügyeleti csoportot.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "mgName": {
      "type": "string",
      "defaultValue": "[concat('mg-', uniqueString(newGuid()))]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Management/managementGroups",
      "apiVersion": "2019-11-01",
      "name": "[parameters('mgName')]",
      "properties": {
      }
    }
  ]
}
```

## <a name="assign-role"></a>Szerepkör hozzárendelése

A [következő sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) szerepkört rendel a bérlői hatókörhöz.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "principalId if the user that will be given contributor access to the resourceGroup"
      }
    },
    "roleDefinitionId": {
      "type": "string",
      "defaultValue": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
      "metadata": {
        "description": "roleDefinition for the assignment - default is owner"
      }
    }
  },
  "variables": {
    // This creates an idempotent guid for the role assignment
    "roleAssignmentName": "[guid('/', parameters('principalId'), parameters('roleDefinitionId'))]"
  },
  "resources": [
    {
      "name": "[variables('roleAssignmentName')]",
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2019-04-01-preview",
      "properties": {
        "roleDefinitionId": "[tenantResourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "/"
      }
    }
  ]
}
```

## <a name="next-steps"></a>További lépések

* A szerepkörök hozzárendeléséről az [Azure-erőforrásokhoz való hozzáférés kezelése RBAC és Azure Resource Manager-sablonok használatával](../../role-based-access-control/role-assignments-template.md)című témakörben olvashat.
* A sablonokat [előfizetési vagy](deploy-to-subscription.md) [felügyeleti csoport szinten](deploy-to-management-group.md)is telepítheti.
