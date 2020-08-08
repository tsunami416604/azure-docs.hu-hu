---
title: Erőforrások üzembe helyezése a bérlőn
description: Ismerteti, hogyan lehet erőforrásokat telepíteni a bérlői hatókörben egy Azure Resource Manager sablonban.
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 2f5249eb54a62e4df082a18b22625bb93a0f09f8
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "88002774"
---
# <a name="create-resources-at-the-tenant-level"></a>Erőforrások létrehozása a bérlői szinten

A szervezete leállása esetén előfordulhat, hogy az Azure AD-bérlőn belül meg kell adnia és hozzá kell rendelnie a [szabályzatokat](../../governance/policy/overview.md) vagy az [Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC)](../../role-based-access-control/overview.md) . A bérlői szintű sablonok használatával a szabályzatok deklaratív alkalmazása és a szerepkörök globális szinten való hozzárendelését végezheti el.

## <a name="supported-resources"></a>Támogatott erőforrások

Nem minden erőforrástípust lehet központilag telepíteni a bérlői szintre. Ez a szakasz felsorolja, hogy milyen típusú erőforrástípusok támogatottak.

Azure-szabályzatok esetén használja a következőt:

* [policyAssignments](/azure/templates/microsoft.authorization/policyassignments)
* [policyDefinitions](/azure/templates/microsoft.authorization/policydefinitions)
* [policySetDefinitions](/azure/templates/microsoft.authorization/policysetdefinitions)

Szerepköralapú hozzáférés-vezérléshez használja a következőt:

* [roleAssignments](/azure/templates/microsoft.authorization/roleassignments)

A felügyeleti csoportokra, előfizetésekre vagy erőforráscsoportokre telepítendő beágyazott sablonok esetében használja a következőt:

* [központi telepítések](/azure/templates/microsoft.resources/deployments)

Felügyeleti csoportok létrehozásához használja a következőt:

* [managementGroups](/azure/templates/microsoft.management/managementgroups)

A költségek kezeléséhez használja a következőt:

* [billingProfiles](/azure/templates/microsoft.billing/billingaccounts/billingprofiles)
* [utasításokat](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/instructions)
* [invoiceSections](/azure/templates/microsoft.billing/billingaccounts/billingprofiles/invoicesections)

### <a name="schema"></a>Séma

A bérlői központi telepítésekhez használt séma eltér az erőforráscsoport-telepítések sémájától.

Sablonok esetén használja a következőt:

```json
https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#
```

A paraméterérték sémája megegyezik az összes központi telepítési hatókörnél. A paraméter fájljaihoz használja a következőt:

```json
https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#
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

Az Azure CLI esetén használja az [az Deployment bérlő Create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create \
  --name demoTenantDeployment \
  --location WestUS \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

Azure PowerShell esetén használja a [New-AzTenantDeployment](/powershell/module/az.resources/new-aztenantdeployment).

```azurepowershell-interactive
New-AzTenantDeployment `
  -Name demoTenantDeployment `
  -Location "West US" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/tenant-deployments/new-mg/azuredeploy.json"
```

REST API esetén használjon [központi telepítéseket – létrehozás vagy frissítés a bérlői hatókörben](/rest/api/resources/deployments/createorupdateattenantscope).

## <a name="deployment-location-and-name"></a>Központi telepítés helye és neve

Bérlői szintű központi telepítések esetén meg kell adnia egy helyet a központi telepítéshez. A központi telepítés helye nem azonos a telepített erőforrások helyétől. A központi telepítés helye határozza meg, hogy hol tárolja a telepítési adatforrásokat.

Megadhatja a központi telepítés nevét, vagy használhatja az alapértelmezett központi telepítési nevet is. Az alapértelmezett név a sablonfájl neve. Egy **azuredeploy.js** nevű sablon üzembe helyezése például a **azuredeploy**alapértelmezett központi telepítési nevét hozza létre.

Az egyes központi telepítési nevek esetében a hely nem módosítható. A központi telepítést nem lehet az egyik helyen létrehozni, ha egy másik helyen már van ilyen nevű üzemelő példány. Ha a hibakódot kapja `InvalidDeploymentLocation` , használjon más nevet vagy ugyanazt a helyet, mint az adott név előző üzembe helyezését.

## <a name="deployment-scopes"></a>Központi telepítési hatókörök

Bérlőn való üzembe helyezéskor a bérlő vagy a felügyeleti csoportok, az előfizetések és az erőforráscsoportok megcélzása is megcélozható a bérlőben. A sablont telepítő felhasználónak hozzáféréssel kell rendelkeznie a megadott hatókörhöz.

A sablon erőforrások szakaszában meghatározott erőforrások a bérlőre lesznek alkalmazva.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        tenant-level-resources
    ],
    "outputs": {}
}
```

Egy felügyeleti csoportnak a bérlőn belüli célzásához adjon hozzá egy beágyazott központi telepítést, és adja meg a `scope` tulajdonságot.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/tenantDeploymentTemplate.json#",
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
            "apiVersion": "2020-06-01",
            "name": "nestedMG",
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

## <a name="next-steps"></a>További lépések

* A szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [Azure szerepkör-hozzárendelések hozzáadása Azure Resource Manager-sablonok használatával](../../role-based-access-control/role-assignments-template.md).
* A sablonokat [előfizetési szinten](deploy-to-subscription.md) vagy [felügyeleti csoport szintjén](deploy-to-management-group.md)is üzembe helyezheti.
