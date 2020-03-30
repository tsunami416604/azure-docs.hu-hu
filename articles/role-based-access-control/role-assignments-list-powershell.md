---
title: Szerepkör-hozzárendelések listázása az Azure RBAC és az Azure PowerShell használatával
description: Megtudhatja, hogyan állapíthatja meg, hogy a felhasználók, csoportok, egyszerű szolgáltatástagok és felügyelt identitások milyen erőforrásokhoz férhetnek hozzá az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure PowerShell használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0ec3153e5b1bfbe04a079d1cfc44e8e8709784d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931149"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-powershell"></a>Szerepkör-hozzárendelések listázása az Azure RBAC és az Azure PowerShell használatával

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Ez a cikk ismerteti, hogyan listázhatok szerepkör-hozzárendelések az Azure PowerShell használatával.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> Ha a szervezet kiszervezte a felügyeleti funkciókat egy olyan szolgáltatóhoz, amely [az Azure delegált erőforrás-kezelését](../lighthouse/concepts/azure-delegated-resource-management.md)használja, az adott szolgáltató által engedélyezett szerepkör-hozzárendelések itt nem jelennek meg.

## <a name="prerequisites"></a>Előfeltételek

- [PowerShell az Azure Cloud Shellben](/azure/cloud-shell/overview) vagy [az Azure PowerShellben](/powershell/azure/install-az-ps)

## <a name="list-role-assignments-for-the-current-subscription"></a>Az aktuális előfizetés szerepkör-hozzárendeléseinek listázása

Az aktuális előfizetésben lévő szerepkör-hozzárendelések listájának legegyszerűbb lefoglalása (beleértve a legfelső szintű és felügyeleti csoportoktól származó örökölt szerepkör-hozzárendeléseket is), ha paraméterek nélkül használja a [Get-AzRoleAssignment-et.](/powershell/module/az.resources/get-azroleassignment)

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>Előfizetés szerepkör-hozzárendeléseinek felsorolása

Az előfizetéshatókör összes szerepkör-hozzárendelésének listázásához használja a [Get-AzRoleAssignment (Get-AzRoleAssignment) lehetőséget.](/powershell/module/az.resources/get-azroleassignment) Az előfizetés-azonosító lekért, megtalálja azt az **Azure Portalon** az Előfizetések panelen, vagy használhatja a [Get-AzSubscription .To get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription).

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>Felhasználó szerepkör-hozzárendeléseinek felsorolása

A megadott felhasználóhoz rendelt összes szerepkör listázásához használja a [Get-AzRoleAssignment tulajdonságot.](/powershell/module/az.resources/get-azroleassignment)

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

A [Get-AzRoleAssignment (Get-AzRoleAssignment)](/powershell/module/az.resources/get-azroleassignment)segítségével listázható a megadott felhasználóhoz rendelt összes szerepkör és a felhasználóhoz tartozó csoportokhoz rendelt szerepkörök, használja a Get-AzRoleAssignment tulajdonságot.

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>Erőforráscsoport szerepkör-hozzárendeléseinek felsorolása

Az erőforráscsoport hatókörén lévő összes szerepkör-hozzárendelés listázásához használja a [Get-AzRoleAssignment (Get-AzRoleAssignment) lehetőséget.](/powershell/module/az.resources/get-azroleassignment)

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>Felügyeleti csoport szerepkör-hozzárendelésének listázása

Ha egy felügyeleticsoport hatókörén lévő összes szerepkör-hozzárendelést listázni szeretné, használja a [Get-AzRoleAssignment (Get-AzRoleAssignment) lehetőséget.](/powershell/module/az.resources/get-azroleassignment) A felügyeleti csoport azonosítójának lekért, **megtalálhatja** azt az Azure Portalfelügyeleti csoportok panelen, vagy használhatja a [Get-AzManagementGroup.To](/powershell/module/az.resources/get-azmanagementgroup)get the management group id, you can find it on the Management groups blade in the Azure portal or you can use Get-AzManagementGroup .

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>Szerepkör-hozzárendelések listázása klasszikus szolgáltatás-rendszergazdáknak és társrendszergazdáknak

A klasszikus előfizetés-rendszergazda és társadminisztrátorok szerepkör-hozzárendelések listázásához használja a [Get-AzRoleAssignment parancsot.](/powershell/module/az.resources/get-azroleassignment)

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>Felügyelt identitás szerepkör-hozzárendelésének listázása

1. A rendszerhez vagy a felhasználó által hozzárendelt felügyelt identitás objektumazonosítójának beszereznie. 

    A felhasználó által hozzárendelt felügyelt identitás objektumazonosítójának lekért ásához használhatja a [Get-AzADServicePrincipal parancsot.](/powershell/module/az.resources/get-azadserviceprincipal)

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. A szerepkör-hozzárendelések listázásához használja a [Get-AzRoleAssignment .to](/powershell/module/az.resources/get-azroleassignment)list.to list the role assignments, use Get-AzRoleAssignment .

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>További lépések

- [Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és az Azure PowerShell használatával](role-assignments-powershell.md)
