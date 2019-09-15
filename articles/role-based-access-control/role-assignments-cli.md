---
title: Az Azure-erőforrásokhoz való hozzáférés kezelése a RBAC és az Azure CLI használatával | Microsoft Docs
description: Ismerje meg, hogyan kezelheti az Azure-erőforrásokhoz való hozzáférést a felhasználók, csoportok és alkalmazások számára szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure CLI használatával. Ez tartalmazza a hozzáférés felsorolásának, a hozzáférés adásának és a hozzáférés eltávolításának módját.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 1b898f42fa6f66fba7c84daa67769249642bd986
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996479"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-cli"></a>Azure-erőforrásokhoz való hozzáférés kezelése a RBAC és az Azure CLI használatával

A [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) az Azure-erőforrásokhoz való hozzáférés kezelésének módja. Ez a cikk azt ismerteti, hogyan kezelheti a felhasználók, csoportok és alkalmazások hozzáférését a RBAC és az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

A hozzáférés kezeléséhez a következők egyikére van szükség:

* [Bash Azure Cloud Shell](/azure/cloud-shell/overview)
* [Azure CLI](/cli/azure)

## <a name="list-roles"></a>Szerepkörök felsorolása

Az összes elérhető szerepkör-definíció listázásához használja [az az role definition List](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list
```

Az alábbi példa felsorolja az összes elérhető szerepkör-definíció nevét és leírását:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.roleName, "description":.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

Az alábbi példa felsorolja az összes beépített szerepkör-definíciót:

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "roleType": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "roleType": "BuiltInRole"
}

...
```

## <a name="list-a-role-definition"></a>Szerepkör-definíció listázása

A szerepkör-definíciók listázásához használja [az az role definition List](/cli/azure/role/definition#az-role-definition-list):

```azurecli
az role definition list --name <role_name>
```

Az alábbi példa a *közreműködő* szerepkör definícióját sorolja fel:

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "dataActions": [],
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
        "notDataActions": []
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="list-actions-of-a-role"></a>Szerepkör műveleteinek listázása

Az alábbi példa csak a közreműködő szerepkör műveleteit és a *hozzájuk* tartozó *tevékenységeket* sorolja fel:

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.permissions[0].actions, "notActions":.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

A következő példa csak a *virtuális gép közreműködői* szerepkörének műveleteit sorolja fel:

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>Hozzáférések felsorolása

A RBAC-ben a hozzáférés listázásához listázhatja a szerepkör-hozzárendeléseket.

### <a name="list-role-assignments-for-a-user"></a>Felhasználó szerepkör-hozzárendeléseinek felsorolása

Egy adott felhasználó szerepkör-hozzárendeléseinek listázásához használja az [az role hozzárendelés List](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --assignee <assignee>
```

Alapértelmezés szerint csak az előfizetésre hatókörű közvetlen hozzárendelések jelennek meg. Ha erőforrás vagy csoport alapján szeretné megtekinteni a hozzárendeléseket, használja `--all` a és az örökölt hozzárendelések megjelenítését, használja `--include-inherited`a (z) lehetőséget.

Az alábbi példa felsorolja azokat a szerepkör-hozzárendeléseket, amelyek közvetlenül *a\@patlong contoso.com* -felhasználóhoz vannak rendelve:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

### <a name="list-role-assignments-at-a-resource-group-scope"></a>Szerepkör-hozzárendelések listázása erőforráscsoport-hatókörben

Az erőforráscsoport-hatókörben létező szerepkör-hozzárendelések listázásához használja az [az role-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list):

```azurecli
az role assignment list --resource-group <resource_group>
```

A következő példa a *Pharma-Sales* erőforráscsoport szerepkör-hozzárendeléseit sorolja fel:

```azurecli
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

### <a name="list-role-assignments-at-a-subscription-scope"></a>Szerepkör-hozzárendelések listázása előfizetési hatókörben

Az előfizetési hatókör összes szerepkör-hozzárendelésének listázásához használja [az az role-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list)lehetőséget. Az előfizetés-azonosító lekéréséhez a Azure Portal az **előfizetések** panelén található, vagy használhatja az [az Account List](/cli/azure/account#az-account-list)lehetőséget.

```azurecli
az role assignment list --subscription <subscription_name_or_id>
```

```Example
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

### <a name="list-role-assignments-at-a-management-group-scope"></a>Szerepkör-hozzárendelések listázása egy felügyeleti csoport hatókörében

A felügyeleti csoport hatókörében lévő összes szerepkör-hozzárendelés felsorolásához használja [az az role-hozzárendelési lista](/cli/azure/role/assignment#az-role-assignment-list)lehetőséget. A felügyeleti csoport AZONOSÍTÓjának lekéréséhez a Azure Portal **felügyeleti csoportok** paneljén található, vagy használhatja az [az Account Management-Group listát](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="grant-access"></a>Hozzáférés biztosítása

Az RBAC-ben a hozzáférés biztosítása egy szerepkör-hozzárendelés létrehozásával történik.

### <a name="create-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>Szerepkör-hozzárendelés létrehozása egy felhasználóhoz erőforráscsoport-hatókörben

Egy erőforráscsoport-hatókörben lévő felhasználó hozzáférésének megadásához használja [az az role-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create)parancsot.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

A következő példa a *virtuális gépi közreműködő* szerepkört rendeli hozzá *a\@patlong contoso.com* -felhasználóhoz a *Pharma-Sales* erőforráscsoport hatókörében:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-using-the-unique-role-id"></a>Szerepkör-hozzárendelés létrehozása az egyedi szerepkör-azonosító használatával

Néhány alkalommal, amikor a szerepkör neve változhat, például:

- Saját egyéni szerepkört használ, és úgy dönt, hogy megváltoztatja a nevet.
- Olyan előzetes verziójú szerepkört használ, amely **(előzetes verzió)** szerepel a névben. A szerepkör megjelenése után a rendszer átnevezi a szerepkört.

> [!IMPORTANT]
> Az előzetes verzió szolgáltatási szintű szerződés nélkül van megadva, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A szerepkör-azonosító nem változik, még akkor is, ha a szerepkör át lett nevezve. Ha parancsfájlokat vagy automationt használ a szerepkör-hozzárendelések létrehozásához, ajánlott az egyedi szerepkör-azonosítót használni a szerepkör neve helyett. Ezért ha egy szerepkört átneveznek, a parancsfájlok nagyobb valószínűséggel fognak működni.

Ha szerepkör-hozzárendelést a szerepkör neve helyett az egyedi szerepkör-AZONOSÍTÓval szeretne létrehozni, használja az [az role-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create)parancsot.

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

A következő példa a [virtuális gép közreműködői](built-in-roles.md#virtual-machine-contributor) szerepkört rendeli hozzá a *patlong\@contoso.com* felhasználóhoz a *Pharma-Sales erőforráscsoport-* hatókörben. Az egyedi szerepkör-azonosító beszerzéséhez használja az [az role definition List](/cli/azure/role/definition#az-role-definition-list) vagy [Az Azure-erőforrások beépített szerepköreit](built-in-roles.md).

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-group"></a>Szerepkör-hozzárendelés létrehozása egy csoport számára

Ha hozzáférést szeretne adni egy csoporthoz, használja az [az role-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create)lehetőséget. A csoport AZONOSÍTÓjának lekéréséhez az [az ad Group List](/cli/azure/ad/group#az-ad-group-list) vagy [az ad Group show](/cli/azure/ad/group#az-ad-group-show)lehetőséget használhatja.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

A következő példa hozzárendeli az *olvasó* szerepkört az *Ann Mack Team* csoporthoz, amelynek azonosítója 22222222-2222-2222-2222-222222222222, előfizetési hatókörben.

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

A következő példa hozzárendeli a *virtuálisgép-közreműködő* szerepkört az *Ann Mack Team* csoporthoz a 22222222-2222-2222-2222-222222222222 azonosítóval, amely egy *Pharma-Sales-Project-Network*nevű virtuális hálózat erőforrás-hatóköre.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="create-a-role-assignment-for-an-application-at-a-resource-group-scope"></a>Szerepkör-hozzárendelés létrehozása egy adott alkalmazáshoz erőforráscsoport-hatókörben

Az alkalmazáshoz való hozzáférés biztosításához használja [az az role-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create)lehetőséget. Az alkalmazás objektum-AZONOSÍTÓjának lekéréséhez az [az AD App List](/cli/azure/ad/app#az-ad-app-list) vagy [az AD App show](/cli/azure/ad/app#az-ad-app-show)lehetőséget használhatja.

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

Az alábbi példa a virtuálisgép- *közreműködő* szerepkört hozzárendeli egy 44444444-4444-4444-4444-444444444444-es azonosítójú alkalmazáshoz a *Pharma-Sales* erőforráscsoport hatókörében.

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="create-a-role-assignment-for-a-user-at-a-subscription-scope"></a>Szerepkör-hozzárendelés létrehozása egy felhasználóhoz előfizetési hatókörben

Ha hozzáférést szeretne adni egy felhasználónak az előfizetési hatókörben, használja az [az role-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create)parancsot. Az előfizetés-azonosító lekéréséhez a Azure Portal az **előfizetések** panelén található, vagy használhatja az [az Account List](/cli/azure/account#az-account-list)lehetőséget.

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

Az alábbi példa hozzárendeli az *olvasó* szerepkört a *annm\@example.com* -felhasználóhoz egy előfizetési hatókörben.

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="create-a-role-assignment-for-a-user-at-a-management-group-scope"></a>Szerepkör-hozzárendelés létrehozása egy felhasználóhoz egy felügyeleti csoport hatókörében

Ahhoz, hogy hozzáférést biztosítson egy felhasználónak egy felügyeleti csoport hatókörében, használja az [az role-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create)parancsot. A felügyeleti csoport AZONOSÍTÓjának lekéréséhez a Azure Portal **felügyeleti csoportok** paneljén található, vagy használhatja az [az Account Management-Group listát](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

A következő példa hozzárendeli a *Számlázási olvasó* szerepkört az *Alain\@example.com* -felhasználóhoz egy felügyeleti csoport hatókörében.

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="remove-access"></a>Hozzáférés megszüntetése

A RBAC a hozzáférés eltávolításához távolítsa el a szerepkör-hozzárendelést az [az szerepkör-hozzárendelés törlése](/cli/azure/role/assignment#az-role-assignment-delete)paranccsal:

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

Az alábbi példa eltávolítja a *virtuálisgép-közreműködő* szerepkör-hozzárendelést a *patlong\@contoso.com* -felhasználótól a *Pharma-Sales* erőforráscsoporthoz:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

Az alábbi példa eltávolítja az *olvasó* szerepkört az *Ann Mack Team* CSOPORTból az 22222222-2222-2222-2222-222222222222 azonosítóval egy előfizetési hatókörben. A csoport AZONOSÍTÓjának lekéréséhez az [az ad Group List](/cli/azure/ad/group#az-ad-group-list) vagy [az ad Group show](/cli/azure/ad/group#az-ad-group-show)lehetőséget használhatja.

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

A következő példa eltávolítja a *Számlázási olvasó* szerepkört a felügyeleti csoport hatókörében lévő *Alain\@example.com* -felhasználótól. A felügyeleti csoport AZONOSÍTÓjának lekéréséhez használhatja az [az Account Management-Group listát](/cli/azure/ext/managementgroups/account/management-group#ext-managementgroups-az-account-management-group-list).

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Egyéni szerepkör létrehozása Azure-erőforrásokhoz az Azure CLI használatával](tutorial-custom-role-cli.md)
- [Azure-erőforrások és-erőforráscsoportok kezelése az Azure CLI használatával](../azure-resource-manager/cli-azure-resource-manager.md)
