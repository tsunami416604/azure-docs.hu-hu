---
title: Erőforrások üzembe helyezése a bérlőn
description: Ismerteti, hogyan lehet erőforrásokat telepíteni a bérlői hatókörben egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 854ccbd43509b6c0b5a04357844c78c32b7e6396
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92668694"
---
# <a name="tenant-deployments-with-arm-templates"></a>Bérlői üzemelő példányok ARM-sablonokkal

A szervezete leállása esetén előfordulhat, hogy az Azure AD-bérlőn belül meg kell adnia és hozzá kell rendelnie a [szabályzatokat](../../governance/policy/overview.md) vagy az [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../../role-based-access-control/overview.md) . A bérlői szintű sablonok használatával a szabályzatok deklaratív alkalmazása és a szerepkörök globális szinten való hozzárendelését végezheti el.

## <a name="supported-resources"></a>Támogatott erőforrások

Nem minden erőforrástípust lehet központilag telepíteni a bérlői szintre. Ez a szakasz felsorolja, hogy milyen típusú erőforrástípusok támogatottak.

Azure-szabályzatok esetén használja a következőt:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata esetén használja a következőt:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

A felügyeleti csoportokra, előfizetésekre vagy erőforráscsoportokre telepítendő beágyazott sablonok esetében használja a következőt:

* [központi telepítések](/azure/templates/microsoft.resources/deployments)

Felügyeleti csoportok létrehozásához használja a következőt:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

A költségek kezeléséhez használja a következőt:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [utasításokat](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

## <a name="schema"></a>Séma

A bérlői központi telepítésekhez használt séma eltér az erőforráscsoport-telepítések sémájától.

Sablonok esetén használja a következőt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    ...
}
```

A paraméterérték sémája megegyezik az összes központi telepítési hatókörnél. A paraméter fájljaihoz használja a következőt:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="required-access"></a>Szükséges hozzáférés

A sablon központi telepítésének jogosultsággal kell rendelkeznie ahhoz, hogy erőforrásokat hozzon létre a bérlői hatókörben. A rendszerbiztonsági tag számára engedéllyel kell rendelkeznie a telepítési műveletek végrehajtásához `Microsoft.Resources/deployments/*` , valamint a sablonban definiált erőforrások létrehozásához. Egy felügyeleti csoport létrehozásához például a résztvevőnek közreműködői engedéllyel kell rendelkeznie a bérlői hatókörben. A szerepkör-hozzárendelések létrehozásához a rendszerbiztonsági tag tulajdonosi engedéllyel kell rendelkeznie.

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az Azure CLI esetén használja az [az Deployment bérlő Create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure PowerShell esetén használja a [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

---

További információt az üzembe helyezési parancsokról és az ARM-sablonok üzembe helyezési lehetőségeiről a következő témakörben talál:

* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Portal](deploy-portal.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure CLI-vel](deploy-cli.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](deploy-powershell.md)
* [Erőforrások üzembe helyezése ARM-sablonokkal és Azure Resource Manager REST API](deploy-rest.md)
* [Sablonok üzembe helyezése a GitHub-tárházból a központi telepítés gomb használatával](deploy-to-azure-button.md)
* [ARM-sablonok üzembe helyezése Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Központi telepítési hatókörök

Felügyeleti csoportba való központi telepítés esetén az erőforrások a következőre helyezhetők:

* a bérlő
* a bérlőn belüli felügyeleti csoportok
* előfizetések
* erőforráscsoportok (két beágyazott üzemelő példányon keresztül)
* a [bővítmény erőforrásai](scope-extension-resources.md) alkalmazhatók az erőforrásokra

A sablont telepítő felhasználónak hozzáféréssel kell rendelkeznie a megadott hatókörhöz.

Ez a szakasz bemutatja, hogyan határozhat meg különböző hatóköröket. Ezeket a különböző hatóköröket egyetlen sablonban kombinálhatja.

### <a name="scope-to-tenant"></a>Hatókör a bérlőre

A sablon erőforrások szakaszában meghatározott erőforrások a bérlőre lesznek alkalmazva.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-tenant.json" highlight="5":::

### <a name="scope-to-management-group"></a>Hatókör a felügyeleti csoportnak

Egy felügyeleti csoportnak a bérlőn belüli célzásához adjon hozzá egy beágyazott központi telepítést, és adja meg a `scope` tulajdonságot.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-mg.json" highlight="10,17,22":::

### <a name="scope-to-subscription"></a>Hatókör az előfizetéshez

Az előfizetéseket a bérlőn belül is megcélozhatja. A sablont telepítő felhasználónak hozzáféréssel kell rendelkeznie a megadott hatókörhöz.

A bérlőn belüli előfizetés célzásához használjon egy beágyazott telepítést és a `subscriptionId` tulajdonságot.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/tenant-to-subscription.json" highlight="10,18":::

## <a name="deployment-location-and-name"></a>Központi telepítés helye és neve

Bérlői szintű központi telepítések esetén meg kell adnia egy helyet a központi telepítéshez. A központi telepítés helye nem azonos a telepített erőforrások helyétől. A központi telepítés helye határozza meg, hogy hol tárolja a telepítési adatforrásokat.

Megadhatja a központi telepítés nevét, vagy használhatja az alapértelmezett központi telepítési nevet is. Az alapértelmezett név a sablonfájl neve. Egy **azuredeploy.js** nevű sablon üzembe helyezése például a **azuredeploy** alapértelmezett központi telepítési nevét hozza létre.

Az egyes központi telepítési nevek esetében a hely nem módosítható. A központi telepítést nem lehet az egyik helyen létrehozni, ha egy másik helyen már van ilyen nevű üzemelő példány. Ha a hibakódot kapja `InvalidDeploymentLocation` , használjon más nevet vagy ugyanazt a helyet, mint az adott név előző üzembe helyezését.

## <a name="create-management-group"></a>Felügyeleti csoport létrehozása

A [következő sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/new-mg) létrehoz egy felügyeleti csoportot.

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

A [következő sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/tenant-deployments/tenant-role-assignment) egy szerepkört rendel a bérlői hatókörhöz.

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

## <a name="next-steps"></a>Következő lépések

* A szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [Azure szerepkör-hozzárendelések hozzáadása Azure Resource Manager-sablonok használatával](../../role-based-access-control/role-assignments-template.md).
* A sablonokat [előfizetési szinten](deploy-to-subscription.md) vagy [felügyeleti csoport szintjén](deploy-to-management-group.md)is üzembe helyezheti.
