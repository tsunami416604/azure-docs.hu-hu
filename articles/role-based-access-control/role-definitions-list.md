---
title: Az Azure RBAC szerepkör-definícióinak listázása Azure Portal, Azure PowerShell, Azure CLI vagy REST API használatával | Microsoft Docs
description: Ismerje meg, hogyan listázhatja az Azure RBAC beépített és egyéni szerepköreit Azure Portal, Azure PowerShell, Azure CLI vagy REST API használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: aa888eedc81ceb3188f801e273c70722207bf512
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80062990"
---
# <a name="list-role-definitions-in-azure-rbac"></a>Szerepkör-definíciók listázása az Azure RBAC

A szerepkör-definíció az engedélyek olyan gyűjteménye, amely elvégezhető, például olvasás, írás és törlés. Általában csak szerepkörnek nevezik. Az [Azure szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) több mint 120 [beépített szerepkörrel](built-in-roles.md) rendelkezik, vagy létrehozhat saját egyéni szerepköröket is. Ez a cikk az Azure-erőforrásokhoz való hozzáférés biztosításához használható beépített és egyéni szerepkörök listázását ismerteti.

A Azure Active Directory rendszergazdai szerepköreinek megtekintéséhez tekintse [meg a Azure Active Directory rendszergazdai szerepkör engedélyei](../active-directory/users-groups-roles/directory-assign-admin-roles.md)című témakört.

## <a name="azure-portal"></a>Azure Portal

### <a name="list-all-roles"></a>Az összes szerepkör listázása

Kövesse az alábbi lépéseket a Azure Portal összes szerepkörének listázásához.

1. A Azure Portal kattintson a **minden szolgáltatás** elemre, majd válasszon ki egy hatókört. Kiválaszthatja például a **felügyeleti csoportokat**, **előfizetéseket**, **erőforráscsoportokat**vagy egy erőforrást.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. Kattintson a **szerepkörök** fülre az összes beépített és egyéni szerepkör listájának megtekintéséhez.

   Az aktuális hatókörben az egyes szerepkörökhöz hozzárendelt felhasználók és csoportok számát láthatja.

   ![Szerepkörök listája](./media/role-definitions-list/roles-list.png)

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Az összes szerepkör listázása

Azure PowerShell összes szerepkörének listázásához használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition | FT Name, Description
```

```Example
AcrImageSigner                                    acr image signer
AcrQuarantineReader                               acr quarantine data reader
AcrQuarantineWriter                               acr quarantine data writer
API Management Service Contributor                Can manage service and the APIs
API Management Service Operator Role              Can manage service but not the APIs
API Management Service Reader Role                Read-only access to service and APIs
Application Insights Component Contributor        Can manage Application Insights components
Application Insights Snapshot Debugger            Gives user permission to use Application Insights Snapshot Debugge...
Automation Job Operator                           Create and Manage Jobs using Automation Runbooks.
Automation Operator                               Automation Operators are able to start, stop, suspend, and resume ...
...
```

### <a name="list-a-role-definition"></a>Szerepkör-definíció listázása

Egy adott szerepkör részleteinek listázásához használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name>
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor"

Name             : Contributor
Id               : b24988ac-6180-42a0-ab88-20f7382dd24c
IsCustom         : False
Description      : Lets you manage everything except access to resources.
Actions          : {*}
NotActions       : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
                   Microsoft.Authorization/elevateAccess/Action}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
```

### <a name="list-a-role-definition-in-json-format"></a>Szerepkör-definíciók listázása JSON formátumban

Ha JSON formátumú szerepkört szeretne listázni, használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | ConvertTo-Json
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | ConvertTo-Json

{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action",
    "Microsoft.Blueprint/blueprintAssignments/write",
    "Microsoft.Blueprint/blueprintAssignments/delete"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

### <a name="list-permissions-of-a-role-definition"></a>Szerepkör-definíció engedélyeinek listázása

Egy adott szerepkör engedélyeinek listázásához használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition).

```azurepowershell
Get-AzRoleDefinition <role_name> | FL Actions, NotActions
```

```Example
PS C:\> Get-AzRoleDefinition "Contributor" | FL Actions, NotActions

Actions    : {*}
NotActions : {Microsoft.Authorization/*/Delete, Microsoft.Authorization/*/Write,
             Microsoft.Authorization/elevateAccess/Action,
             Microsoft.Blueprint/blueprintAssignments/write...}
```

```azurepowershell
(Get-AzRoleDefinition <role_name>).Actions
```

```Example
PS C:\> (Get-AzRoleDefinition "Virtual Machine Contributor").Actions

Microsoft.Authorization/*/read
Microsoft.Compute/availabilitySets/*
Microsoft.Compute/locations/*
Microsoft.Compute/virtualMachines/*
Microsoft.Compute/virtualMachineScaleSets/*
Microsoft.DevTestLab/schedules/*
Microsoft.Insights/alertRules/*
Microsoft.Network/applicationGateways/backendAddressPools/join/action
Microsoft.Network/loadBalancers/backendAddressPools/join/action
...
```

## <a name="azure-cli"></a>Azure CLI

### <a name="list-all-roles"></a>Az összes szerepkör listázása

Az Azure CLI összes szerepkörének listázásához használja [az az role definition List](/cli/azure/role/definition#az-role-definition-list).

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

Az alábbi példa felsorolja az összes beépített szerepkört.

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

### <a name="list-a-role-definition"></a>Szerepkör-definíció listázása

Egy szerepkör részleteinek listázásához használja [az az role definition List](/cli/azure/role/definition#az-role-definition-list)lehetőséget.

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

### <a name="list-permissions-of-a-role-definition"></a>Szerepkör-definíció engedélyeinek listázása

Az alábbi példa csak a közreműködő szerepkör *műveleteit* *és a* *hozzájuk* tartozó tevékenységeket sorolja fel.

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

A következő példa csak a *virtuális gép közreműködői* szerepkörének műveleteit sorolja fel.

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

## <a name="rest-api"></a>REST API

### <a name="list-role-definitions"></a>Szerepkör-definíciók felsorolása

A szerepkör-definíciók listázásához használja a [szerepkör-definíciók – lista](/rest/api/authorization/roledefinitions/list) REST API. Az eredmények pontosításához meg kell adnia egy hatókört és egy opcionális szűrőt.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le a *{scope}* elemet arra a hatókörre, amelyre a szerepkör-definíciókat szeretné listázni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Erőforrás |

    Az előző példában a Microsoft. web egy olyan erőforrás-szolgáltató, amely egy App Service példányra hivatkozik. Hasonlóképpen használhatja bármely más erőforrás-szolgáltatót, és megadhatja a hatókört. További információ: [Azure erőforrás-szolgáltatók és típusok](../azure-resource-manager/management/resource-providers-and-types.md) és támogatott [Azure Resource Manager erőforrás-szolgáltatói műveletek](resource-provider-operations.md).  
     
1. Cserélje le a *{Filter}* helyére azt a feltételt, amelyet alkalmazni szeretne a szerepkör-definíciós lista szűréséhez.

    > [!div class="mx-tableFixed"]
    > | Szűrés | Leírás |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | Felsorolja a megadott hatókör és az alhatókörek szerepkör-definícióit. |
    > | `$filter=type+eq+'{type}'` | Felsorolja a megadott típusú szerepkör-definíciókat. A szerepkör típusa lehet `CustomRole` vagy. `BuiltInRole` |

### <a name="list-a-role-definition"></a>Szerepkör-definíció listázása

Egy adott szerepkör részleteinek listázásához használja a [Get](/rest/api/authorization/roledefinitions/get) vagy a szerepkör-definíciókat – [get by id](/rest/api/authorization/roledefinitions/getbyid) REST API.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    A címtár-szintű szerepkör-definícióhoz a következő kérelem használható:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le a *{scope}* elemet arra a hatókörre, amelyre a szerepkör-definíciót szeretné listázni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Erőforrás |
     
1. Cserélje le a *{roleDefinitionId}* helyére a szerepkör-definíció azonosítóját.

## <a name="next-steps"></a>További lépések

- [Beépített szerepkörök Azure-erőforrásokhoz](built-in-roles.md)
- [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
- [Szerepkör-hozzárendelések listázása az Azure RBAC és a Azure Portal használatával](role-assignments-list-portal.md)
- [Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és a Azure Portal használatával](role-assignments-portal.md)
