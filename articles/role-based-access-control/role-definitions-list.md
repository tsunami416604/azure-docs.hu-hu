---
title: Szerepkör-definíciók listázása az Azure RBAC Azure Portal, Azure PowerShell vagy Azure CLI használatával | Microsoft Docs
description: Ismerje meg, hogyan listázhatja az Azure RBAC beépített és egyéni szerepköreit Azure Portal, Azure PowerShell vagy Azure CLI használatával.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 839393d7535de530a27752f77e311c87c75825d9
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74710346"
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

## <a name="azure-cli"></a>Azure parancssori felület (CLI)

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

## <a name="next-steps"></a>Következő lépések

- [Beépített szerepkörök Azure-erőforrásokhoz](built-in-roles.md)
- [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
- [Szerepkör-hozzárendelések listázása az Azure RBAC és a Azure Portal használatával](role-assignments-list-portal.md)
- [Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és a Azure Portal használatával](role-assignments-portal.md)
