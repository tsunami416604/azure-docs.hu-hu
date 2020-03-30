---
title: Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az RBAC-kal és az Azure PowerShell-lel
description: Megtudhatja, hogyan adhat hozzáférést az Azure-erőforrásokhoz felhasználók, csoportok, egyszerű szolgáltatástagok vagy felügyelt identitások számára az Azure szerepköralapú hozzáférés-vezérlés (RBAC) és az Azure PowerShell használatával.
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 68a73f622dc69b70870ddc1db16edcf406b63800
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283211"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-powershell"></a>Szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure RBAC és az Azure PowerShell használatával

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Ez a cikk ismerteti, hogyan rendelhet szerepköröket az Azure PowerShell használatával.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Szerepkör-hozzárendelések hozzáadásához vagy eltávolításához a következőkre van szükség:

- `Microsoft.Authorization/roleAssignments/write`és `Microsoft.Authorization/roleAssignments/delete` engedélyek, például [a Felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator) vagy [a tulajdonos](built-in-roles.md#owner)
- [PowerShell az Azure Cloud Shellben](/azure/cloud-shell/overview) vagy [az Azure PowerShellben](/powershell/azure/install-az-ps)

## <a name="get-object-ids"></a>Objektumazonosítók beszerezése

Szerepkör-hozzárendelések hozzáadásához vagy eltávolításához előfordulhat, hogy meg kell adnia egy objektum egyedi azonosítóját. Az azonosító formátuma: `11111111-1111-1111-1111-111111111111`. Az Azure Portalon vagy az Azure PowerShellben lejuthat az azonosítóra.

### <a name="user"></a>Felhasználó

Az Azure AD-felhasználó objektumazonosítójának lekérnie használhatja a [Get-AzADUser .To get-AzADUser](/powershell/module/az.resources/get-azaduser).

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>Csoport

Egy Azure AD-csoport objektumazonosítójának lekérnie használhatja a [Get-AzADGroup csoportot.](/powershell/module/az.resources/get-azadgroup)

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Alkalmazás

Az Azure AD egyszerű szolgáltatásnév (egy alkalmazás által használt identitás) objektumazonosítójának lekérnie használhatja a [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal)szolgáltatást. Egyszerű szolgáltatás esetén az objektumazonosítót használja, **ne** az alkalmazásazonosítót.

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>Szerepkör-hozzárendelés hozzáadása

Az RBAC-ban a hozzáférés engedélyezéséhez egy szerepkör-hozzárendelést kell hozzáadnia.

### <a name="user-at-a-resource-group-scope"></a>Felhasználó egy erőforráscsoport hatókörén

Ha egy felhasználóhoz szerepkör-hozzárendelést szeretne hozzáadni egy erőforráscsoport hatóköréhez, használja a [New-AzRoleAssignment .](/powershell/module/az.resources/new-azroleassignment)

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

Néhány alkalommal, amikor egy szerepkör neve megváltozhat, például:

- Saját egyéni szerepkört használ, és úgy dönt, hogy módosítja a nevet.
- Olyan előnézeti szerepkört használ, amelynek **neve (előnézet)** szerepel. A szerepkör megjelenésekor a szerepkör átnevezésre kerül.

> [!IMPORTANT]
> Az előzetes verzió szolgáltatásiszint-szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információt a Microsoft Azure előzetes verziók kiegészítő használati feltételei című [témakörben talál.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Még ha egy szerepkör t átnevezik is, a szerepkör-azonosító nem változik. Ha parancsfájlokat vagy automatizálást használ a szerepkör-hozzárendelések létrehozásához, ajánlott az egyedi szerepkör-azonosítót használni a szerepkör neve helyett. Ezért ha egy szerepkör tátlonlás, a parancsfájlok nagyobb valószínűséggel működnek.

Ha a szerepkörnév helyett az egyedi szerepkörazonosítót használó szerepkör-hozzárendelést szeretne hozzáadni, használja a [New-AzRoleAssignment lehetőséget.](/powershell/module/az.resources/new-azroleassignment)

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

A következő példa a [Virtuálisgép közreműködőszerepkört](built-in-roles.md#virtual-machine-contributor) rendeli *hozzá az\@alain example.com* felhasználóhoz a *pharma-sales* erőforráscsoport hatókörén. Az egyedi szerepkör-azonosító lekérni, [használhatja a Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) vagy tekintse [meg a beépített szerepkörök az Azure-erőforrások.](built-in-roles.md)

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

### <a name="group-at-a-resource-scope"></a>Csoportosítás erőforráshatókörben

Ha egy erőforráshatókörben lévő csoporthoz szerepkör-hozzárendelést szeretne hozzáadni, használja a [New-AzRoleAssignment .](/powershell/module/az.resources/new-azroleassignment) A csoport objektumazonosítójának beszerzéséről az [Objektumazonosítók beszedése](#get-object-ids)című témakörben talál további információt.

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

### <a name="application-at-a-subscription-scope"></a>Alkalmazás előfizetési hatókörön

Ha egy előfizetési hatókörben lévő alkalmazáshoz szerepkör-hozzárendelést szeretne hozzáadni, használja a [New-AzRoleAssignment .to](/powershell/module/az.resources/new-azroleassignment)add a role assignment a application to a subscription scope, use New-AzRoleAssignment . Az alkalmazás objektumazonosítójának beszerzéséről az [Objektumazonosítók beszedése](#get-object-ids)című témakörben talál további információt.

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

### <a name="user-at-a-management-group-scope"></a>Felhasználó a felügyeleti csoport hatókörén

Ha egy felügyeleticsoport hatókörén lévő felhasználóhoz szerepkör-hozzárendelést szeretne hozzáadni, használja a [New-AzRoleAssignment .to](/powershell/module/az.resources/new-azroleassignment)add a role assignment a user on a management group scope, use New-AzRoleAssignment . A felügyeleti csoport azonosítójának lekért, **megtalálhatja** azt az Azure Portalfelügyeleti csoportok panelen, vagy használhatja a [Get-AzManagementGroup.To](/powershell/module/az.resources/get-azmanagementgroup)get the management group id, you can find it on the Management groups blade in the Azure portal or you can use Get-AzManagementGroup .

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

Az RBAC programban a hozzáférés eltávolításához távolítsa el a szerepkör-hozzárendelést az [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment)használatával.

A következő példa eltávolítja a *Virtuálisgép közreműködő* szerepkör-hozzárendelését az *\@alain example.com* felhasználótól a *pharma-sales* erőforráscsoportban:

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

A következő példa eltávolítja a <role_name> szerepkört <előfizetési hatókör object_id>.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

A következő példa eltávolítja a <role_name> szerepkört <object_id> a felügyeleti csoport hatókörén.

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

Ha a következő hibaüzenet jelenik meg: "A megadott információ nem felel meg `-Scope` szerepkör-hozzárendelésnek", győződjön meg arról, hogy megadja a vagy `-ResourceGroupName` a paramétereket is. További információ: [RBAC for Azure resources.](troubleshooting.md#role-assignments-with-unknown-security-principal)

## <a name="next-steps"></a>További lépések

- [Szerepkör-hozzárendelések listázása az Azure RBAC és az Azure PowerShell használatával](role-assignments-list-powershell.md)
- [Oktatóanyag: Csoportos hozzáférés biztosítása az Azure-erőforrásokhoz az RBAC és az Azure PowerShell használatával](tutorial-role-assignments-group-powershell.md)
- [Oktatóanyag: Hozzon létre egy egyéni szerepkört az Azure-erőforrásokhoz az Azure PowerShell használatával](tutorial-custom-role-powershell.md)
- [Erőforrások kezelése az Azure PowerShell segítségével](../azure-resource-manager/management/manage-resources-powershell.md)
