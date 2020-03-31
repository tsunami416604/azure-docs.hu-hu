---
title: Szerepkör-definíciók listázása az Azure RBAC-ban az Azure Portal, az Azure PowerShell, az Azure CLI vagy a REST API használatával | Microsoft dokumentumok
description: Megtudhatja, hogyan listázhat beépített és egyéni szerepköröket az Azure RBAC-ban az Azure Portal, az Azure PowerShell, az Azure CLI vagy a REST API használatával.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062990"
---
# <a name="list-role-definitions-in-azure-rbac"></a>Szerepkör-definíciók listázása az Azure RBAC-ban

A szerepkör-definíció olyan engedélyek gyűjteménye, amelyek elvégezhetők, például olvasás, írás és törlés. Általában csak szerepnek hívják. [Az Azure szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) több mint 120 [beépített szerepkörrel](built-in-roles.md) rendelkezik, vagy létrehozhat saját egyéni szerepköröket. Ez a cikk ismerteti, hogyan sorolhatja fel a beépített és egyéni szerepköröket, amelyek segítségével hozzáférést biztosítaz Azure-erőforrásokhoz.

Az Azure Active Directory rendszergazdai szerepköreinek listáját a [Rendszergazdai szerepkör-engedélyek az Azure Active Directoryban című témakörben láthatja.](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="azure-portal"></a>Azure portál

### <a name="list-all-roles"></a>Az összes szerepkör listázása

Kövesse az alábbi lépéseket az Azure Portalon az összes szerepkör listázásához.

1. Az Azure Portalon kattintson a **Minden szolgáltatás** elemre, majd válasszon ki egy hatókört. Kiválaszthatja például a **Felügyeleti csoportok**, **Az Előfizetések**, **az Erőforráscsoportok**vagy egy erőforrás lehetőséget.

1. Kattintson az adott erőforrásra.

1. Kattintson a **Hozzáférés-vezérlés (IAM)** elemre.

1. A **Szerepkörök** fülre kattintva megtekintheti az összes beépített és egyéni szerepkör listáját.

   Megtekintheti az egyes szerepkörhöz rendelt felhasználók és csoportok számát az aktuális hatókörben.

   ![Szerepkörök listája](./media/role-definitions-list/roles-list.png)

## <a name="azure-powershell"></a>Azure PowerShell

### <a name="list-all-roles"></a>Az összes szerepkör listázása

Az Azure PowerShell összes szerepköre listázásához használja a [Get-AzRoleDefinition .to](/powershell/module/az.resources/get-azroledefinition)list a összes szerepkört az Azure PowerShellben.

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

### <a name="list-a-role-definition"></a>Szerepkör-definíció felsorolása

Egy adott szerepkör részleteinek listázásához használja a [Get-AzRoleDefinition programot.](/powershell/module/az.resources/get-azroledefinition)

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

### <a name="list-a-role-definition-in-json-format"></a>Szerepkör-definíció listázása JSON formátumban

Ha JSON formátumban szeretne egy szerepkört felsorolni, használja a [Get-AzRoleDefinition parancsot.](/powershell/module/az.resources/get-azroledefinition)

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

Egy adott szerepkör engedélyeinek listázásához használja a [Get-AzRoleDefinition parancsot.](/powershell/module/az.resources/get-azroledefinition)

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

Az Azure CLI összes szerepköre listázásához használja [az az szerepkör-definíciós listát.](/cli/azure/role/definition#az-role-definition-list)

```azurecli
az role definition list
```

A következő példa az összes elérhető szerepkör-definíció nevét és leírását sorolja fel:

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

A következő példa az összes beépített szerepkört felsorolja.

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

### <a name="list-a-role-definition"></a>Szerepkör-definíció felsorolása

Egy szerepkör részleteinek listázásához használja [az az szerepkör-definíciós listát.](/cli/azure/role/definition#az-role-definition-list)

```azurecli
az role definition list --name <role_name>
```

A következő példa a *közreműködői* szerepkör-definíciót sorolja fel:

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

A következő példa csak a *közreműködői* szerepkör *műveleteket* és *nemműveleteket* sorolja fel.

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

A következő példa csak a *virtuálisgép közreműködői* szerepkör feladatait sorolja fel.

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

A szerepkör-definíciók listázásához használja a [szerepkör-definíciók – REST-lista](/rest/api/authorization/roledefinitions/list) API-t. Az eredmények finomításához adjon meg egy hatókört és egy választható szűrőt.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions?$filter={$filter}&api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le *a(z) {scope}* értéket arra a hatókörre, amelynek szerepkör-definícióit fel szeretné sorolni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Erőforrás |

    Az előző példában a microsoft.web egy olyan erőforrás-szolgáltató, amely egy App Service-példányra hivatkozik. Hasonlóképpen bármely más erőforrás-szolgáltatót is használhat, és megadhatja a hatókört. További információ: [Azure Resource providers and types](../azure-resource-manager/management/resource-providers-and-types.md) and supported Azure Resource Manager resource provider [operations.](resource-provider-operations.md)  
     
1. Cserélje le *a(z) {filter}* elemet arra a feltételre, amelyet a szerepkör-definíciós lista szűrésére alkalmazni kíván.

    > [!div class="mx-tableFixed"]
    > | Szűrés | Leírás |
    > | --- | --- |
    > | `$filter=atScopeAndBelow()` | A megadott hatókör és az alhatótávok szerepkör-definícióinak listája. |
    > | `$filter=type+eq+'{type}'` | A megadott típusú szerepkör-definíciók listája. A szerepkör típusa `CustomRole` `BuiltInRole`lehet vagy . |

### <a name="list-a-role-definition"></a>Szerepkör-definíció felsorolása

Egy adott szerepkör részleteinek listázásához használja a [szerepkör-definíciók – leget](/rest/api/authorization/roledefinitions/get) vagy [szerepkör-definíciók – Get By Id](/rest/api/authorization/roledefinitions/getbyid) REST API.

1. Kezdje a következő kéréssel:

    ```http
    GET https://management.azure.com/{scope}/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

    Címtárszintű szerepkör-definíció esetén a következő kérést használhatja:

    ```http
    GET https://management.azure.com/providers/Microsoft.Authorization/roleDefinitions/{roleDefinitionId}?api-version=2015-07-01
    ```

1. Az URI-n belül cserélje le *a(z) {scope}* értéket arra a hatókörre, amelynek a szerepkör-definícióját fel szeretné sorolni.

    > [!div class="mx-tableFixed"]
    > | Hatókör | Típus |
    > | --- | --- |
    > | `providers/Microsoft.Management/managementGroups/{groupId1}` | Felügyeleti csoport |
    > | `subscriptions/{subscriptionId1}` | Előfizetés |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1` | Erőforráscsoport |
    > | `subscriptions/{subscriptionId1}/resourceGroups/myresourcegroup1/providers/Microsoft.Web/sites/mysite1` | Erőforrás |
     
1. Cserélje le *a(z) {roleDefinitionId}* programot a szerepkör-definíciós azonosítóra.

## <a name="next-steps"></a>További lépések

- [Beépített szerepkörök Azure-erőforrásokhoz](built-in-roles.md)
- [Egyéni szerepkörök Azure-erőforrásokhoz](custom-roles.md)
- [Szerepkör-hozzárendelések listázása az Azure RBAC és az Azure Portal használatával](role-assignments-list-portal.md)
- [Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és az Azure Portal használatával](role-assignments-portal.md)
