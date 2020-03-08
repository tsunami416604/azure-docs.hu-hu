---
title: Erőforrások üzembe helyezése a bérlőn
description: Ismerteti, hogyan lehet erőforrásokat telepíteni a bérlői hatókörben egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: d63697f3c140b5ad374607f1ecb00dad20e697de
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899138"
---
# <a name="create-resources-at-the-tenant-level"></a>Erőforrások létrehozása a bérlői szinten

Az Azure-erőforrásokat általában az Azure-előfizetésében lévő erőforráscsoporthoz helyezheti üzembe. Létrehozhat azonban erőforrásokat is a következő helyeken:

* [előfizetés szintje](deploy-to-subscription.md)
* [felügyeleti csoport szintje](deploy-to-management-group.md)
* bérlői szint (ebben a cikkben szerepel)

A bérlői szintű központi telepítések használatával olyan műveleteket hajthat végre, amelyek az adott szinten ésszerűek, például [szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/overview.md) hozzárendelésével vagy [házirendek](../../governance/policy/overview.md)alkalmazásával.

## <a name="supported-resources"></a>Támogatott erőforrások

A következő erőforrástípusok a bérlői szinten helyezhetők üzembe:

* [központi telepítések](/azure/templates/microsoft.resources/deployments) – a felügyeleti csoportokra vagy előfizetésekre telepítendő beágyazott sablonokhoz.
* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)
* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)
* [roleDefinitions](/azure/templates/microsoft.authorization/roledefinitions)

### <a name="schema"></a>Séma

A bérlői központi telepítésekhez használt séma eltér az erőforráscsoport-telepítések sémájától.

Sablonok esetén használja a következőt:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

A paraméter fájljaihoz használja a következőt:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentParameters.json#
```

## <a name="required-access"></a>Szükséges hozzáférés

A sablon központi telepítésének jogosultsággal kell rendelkeznie ahhoz, hogy erőforrásokat hozzon létre a bérlői hatókörben. A rendszerbiztonsági tag számára engedéllyel kell rendelkeznie a telepítési műveletek végrehajtásához (`Microsoft.Resources/deployments/*`), valamint a sablonban definiált erőforrások létrehozásához. Egy felügyeleti csoport létrehozásához például a résztvevőnek közreműködői engedéllyel kell rendelkeznie a bérlői hatókörben. A szerepkör-hozzárendelések létrehozásához a rendszerbiztonsági tag tulajdonosi engedéllyel kell rendelkeznie.

A Azure Active Directory globális rendszergazdája nem rendelkezik automatikusan jogosultsággal a szerepkörök hozzárendeléséhez. Ha engedélyezni szeretné a sablonok központi telepítését a bérlői hatókörben, a globális rendszergazdának a következő lépéseket kell elvégeznie:

1. Fiók-hozzáférés emelése, hogy a globális rendszergazda szerepköröket rendeljen hozzá. További információ: jogosultságszint- [emelési hozzáférés az összes Azure-előfizetés és-felügyeleti csoport kezeléséhez](../../role-based-access-control/elevate-access-global-admin.md).

1. Rendeljen tulajdonost vagy közreműködőt a sablonok üzembe helyezéséhez szükséges rendszerbiztonsági tag számára.

   ```azurepowershell-interactive
   New-AzRoleAssignment -SignInName "[userId]" -Scope "/" -RoleDefinitionName "Owner"
   ```

   ```azurecli-interactive
   az role assignment create --assignee "[userId]" --scope "/" --role "Owner"
   ```

A rendszerbiztonsági tag most már rendelkezik a sablon üzembe helyezéséhez szükséges engedélyekkel.

## <a name="deployment-commands"></a>Üzembe helyezési parancsok

A bérlők központi telepítésére vonatkozó parancsok eltérnek az erőforráscsoport-telepítések parancsaitól.

Azure PowerShell esetén használja a [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Location "West US" `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-level-deployments/new-mg/azuredeploy.json
```

REST API esetén használjon [központi telepítéseket – létrehozás vagy frissítés a bérlői hatókörben](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Központi telepítés helye és neve

Bérlői szintű központi telepítések esetén meg kell adnia egy helyet a központi telepítéshez. A központi telepítés helye nem azonos a telepített erőforrások helyétől. A központi telepítés helye határozza meg, hogy hol tárolja a telepítési adatforrásokat.

Megadhatja a központi telepítés nevét, vagy használhatja az alapértelmezett központi telepítési nevet is. Az alapértelmezett név a sablonfájl neve. Egy **azuredeploy. JSON** nevű sablon üzembe helyezése például létrehoz egy alapértelmezett központi telepítési nevet a **azuredeploy**.

Az egyes központi telepítési nevek esetében a hely nem módosítható. A központi telepítést nem lehet az egyik helyen létrehozni, ha egy másik helyen már van ilyen nevű üzemelő példány. Ha `InvalidDeploymentLocation`hibakódot kap, vagy más nevet vagy azonos helyet használ a korábbi üzembe helyezéshez a névben.

## <a name="use-template-functions"></a>A Template functions használata

A bérlői központi telepítések esetén fontos szempont a sablon funkcióinak használata:

* A [resourceGroup ()](template-functions-resource.md#resourcegroup) függvény **nem** támogatott.
* Az [előfizetés ()](template-functions-resource.md#subscription) függvény **nem** támogatott.
* A [Reference ()](template-functions-resource.md#reference) és a [List ()](template-functions-resource.md#list) függvények támogatottak.
* Használja a [tenantResourceId ()](template-functions-resource.md#tenantresourceid) függvényt a bérlői szinten üzembe helyezett erőforrások erőforrás-azonosítójának lekéréséhez.

  Ha például egy házirend-definíció erőforrás-AZONOSÍTÓját szeretné lekérni, használja a következőt:
  
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

## <a name="assign-role"></a>Szerepkör kiosztása

A [következő sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-level-deployments/tenant-role-assignment) egy szerepkört rendel a bérlői hatókörhöz.

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

* A szerepkörök hozzárendelésével kapcsolatos további tudnivalókért lásd: [Az Azure-erőforrásokhoz való hozzáférés kezelése RBAC és Azure Resource Manager sablonok használatával](../../role-based-access-control/role-assignments-template.md).
* Azure Resource Manager sablonok létrehozásával kapcsolatos további tudnivalókért lásd: [sablonok készítése](template-syntax.md).
* A sablonban elérhető függvények listáját itt tekintheti meg: [sablon függvények](template-functions.md).