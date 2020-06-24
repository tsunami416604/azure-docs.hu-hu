---
title: Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure PowerShell-Azure RBAC
description: Megtudhatja, hogyan biztosíthat hozzáférést az Azure-erőforrásokhoz a felhasználók, csoportok, egyszerű szolgáltatások és felügyelt identitások számára a Azure PowerShell és az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 46aea9ab113a0c75ed24497ee39793d08c4f7165
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/15/2020
ms.locfileid: "84790891"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása Azure PowerShell használatával

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Ez a cikk a szerepkörök Azure PowerShell használatával történő hozzárendelését ismerteti.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Szerepkör-hozzárendelések hozzáadásához vagy eltávolításához a következőket kell tennie:

- `Microsoft.Authorization/roleAssignments/write`és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például a [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy a [tulajdonos](built-in-roles.md#owner)
- [PowerShell Azure Cloud Shell](/azure/cloud-shell/overview) vagy [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Objektum-azonosítók beolvasása

A szerepkör-hozzárendelések hozzáadásához vagy eltávolításához szükség lehet egy objektum egyedi AZONOSÍTÓjának megadására. Az azonosító formátuma: `11111111-1111-1111-1111-111111111111` . Az azonosítót a Azure Portal vagy a Azure PowerShell használatával kérheti le.

### <a name="user"></a>Felhasználó

Az Azure AD-felhasználóhoz tartozó objektumazonosító beszerzéséhez használja a [Get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Csoport

Az Azure AD-csoporthoz tartozó objektumazonosító beszerzéséhez használja a [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup).

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Alkalmazás

Az Azure AD egyszerű szolgáltatásnév (az alkalmazás által használt identitás) objektumazonosító beszerzéséhez használhatja a [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal). Egyszerű szolgáltatásnév esetén használja az objektumazonosító azonosítót, **ne** pedig az alkalmazás azonosítóját.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

Az Azure RBAC a hozzáférés biztosításához hozzá kell adnia egy szerepkör-hozzárendelést.

### <a name="user-at-a-resource-group-scope"></a>Felhasználó az erőforráscsoport hatókörében

Egy erőforráscsoport-hatókörben lévő felhasználóhoz tartozó szerepkör-hozzárendelés hozzáadásához használja a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)parancsot.

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="using-the-unique-role-id"></a>Az egyedi szerepkör-azonosító használata

Néhány alkalommal, amikor a szerepkör neve változhat, például:

- Saját egyéni szerepkört használ, és úgy dönt, hogy megváltoztatja a nevet.
- Olyan előzetes verziójú szerepkört használ, amely **(előzetes verzió)** szerepel a névben. A szerepkör megjelenése után a rendszer átnevezi a szerepkört.

> [!IMPORTANT]
> Az előzetes verzió szolgáltatási szintű szerződés nélkül van megadva, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A szerepkör-azonosító nem változik, még akkor is, ha a szerepkör át lett nevezve. Ha parancsfájlokat vagy automationt használ a szerepkör-hozzárendelések létrehozásához, ajánlott az egyedi szerepkör-azonosítót használni a szerepkör neve helyett. Ezért ha egy szerepkört átneveznek, a parancsfájlok nagyobb valószínűséggel fognak működni.

Ha szerepkör-hozzárendelést szeretne hozzáadni a szerepkör neve helyett az egyedi szerepkör-azonosító használatával, használja a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)parancsot.

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

A következő példa a [virtuális gépi közreműködő](built-in-roles.md#virtual-machine-contributor) szerepkört rendeli hozzá *Alain \@ example.com* -felhasználóhoz a *Pharma-Sales* erőforráscsoport hatókörében. Az egyedi szerepkör-azonosító beszerzéséhez használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) , vagy tekintse meg az [Azure beépített szerepköreit](built-in-roles.md).

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="group-at-a-resource-scope"></a>Csoport erőforrás-hatókörben

Ha erőforrás-hatókörben szeretné hozzáadni egy szerepkör-hozzárendelést, használja a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)parancsot. További információ a csoport objektumazonosító beszerzéséről: [objektumazonosítók beolvasása](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="application-at-a-subscription-scope"></a>Alkalmazás előfizetési hatóköre

Ha egy alkalmazáshoz szerepkör-hozzárendelést szeretne hozzáadni egy előfizetési hatókörben, használja a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)parancsot. További információ az alkalmazás objektum-AZONOSÍTÓjának lekéréséről: [objektumazonosítók beolvasása](#get-object-ids).

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="user-at-a-management-group-scope"></a>Felhasználó egy felügyeleti csoport hatókörében

Egy felügyeleti csoport hatókörében lévő felhasználóhoz tartozó szerepkör-hozzárendelés hozzáadásához használja a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)parancsot. A felügyeleti csoport AZONOSÍTÓjának beszerzéséhez a Azure Portal **felügyeleti csoportok** paneljén található, vagy a [Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)is használható.

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>Szerepkör-hozzárendelés eltávolítása

Az Azure RBAC a hozzáférés eltávolításához távolítsa el a szerepkör-hozzárendelést a [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment)használatával.

A következő példa eltávolítja a *virtuálisgép-közreműködő* szerepkör-hozzárendelést az *Alain \@ example.com* -felhasználótól a *Pharma-Sales* erőforráscsoporthoz:

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

Az alábbi példa eltávolítja a <role_name> szerepkört az előfizetések hatókörének <object_id>.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

A következő példa eltávolítja a <role_name> szerepkört a felügyeleti csoport hatókörének <object_id>.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Ha a következő hibaüzenet jelenik meg: "a megadott információk nem képeznek szerepkör-hozzárendelést", győződjön meg arról, hogy a vagy a paramétereket is megadja `-Scope` `-ResourceGroupName` . További információ: az [Azure RBAC hibáinak megoldása](troubleshooting.md#role-assignments-with-identity-not-found).

## <a name="next-steps"></a>További lépések

- [Azure-beli szerepkör-hozzárendelések listázása Azure PowerShell használatával](role-assignments-list-powershell.md)
- [Oktatóanyag: csoporthoz való hozzáférés biztosítása az Azure-erőforrásokhoz Azure PowerShell használatával](tutorial-role-assignments-group-powershell.md)
- [Oktatóanyag: egyéni Azure-szerepkör létrehozása Azure PowerShell használatával](tutorial-custom-role-powershell.md)
- [Erőforrások kezelése a Azure PowerShell](../azure-resource-manager/management/manage-resources-powershell.md)
