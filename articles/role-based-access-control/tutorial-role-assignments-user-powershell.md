---
title: Tutorial - Grant a user access to Azure resources using RBAC and Azure PowerShell
description: Learn how to grant a user access to Azure resources using role-based access control (RBAC) and Azure PowerShell in this tutorial.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.openlocfilehash: c5570c6b1d2cdd168dbaeb0a91d80a61e171e5d1
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74418631"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-rbac-and-azure-powershell"></a>Tutorial: Grant a user access to Azure resources using RBAC and Azure PowerShell

[Role-based access control (RBAC)](overview.md) is the way that you manage access to Azure resources. Ebben az oktatóanyagban hozzáférést biztosít egy felhasználó számára, hogy mindent megtekinthessen az előfizetésben és mindent kezelhessen egy erőforráscsoportban az Azure PowerShell használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzáférés biztosítása egy felhasználó számára különböző hatókörökben
> * Hozzáférések felsorolása
> * Hozzáférés eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre van szükség:

- Engedélyek felhasználók létrehozására az Azure Active Directoryban (vagy meglévő felhasználó)
- [Azure Cloud Shell](/azure/cloud-shell/quickstart-powershell)

## <a name="role-assignments"></a>Szerepkör-hozzárendelések

Az RBAC-ben a hozzáférés biztosítása egy szerepkör-hozzárendelés létrehozásával történik. A szerepkör-hozzárendelés három elemből áll: rendszerbiztonsági tagból, szerepkör-definícióból és hatókörből. Az oktatóanyag során a következő két szerepkör-hozzárendelést fogja elvégezni:

| Rendszerbiztonsági tag | Szerepkör-definíció | Hatókör |
| --- | --- | --- |
| Felhasználó<br>(RBAC-oktatóanyagbeli felhasználó) | [Olvasó](built-in-roles.md#reader) | Előfizetés |
| Felhasználó<br>(RBAC-oktatóanyagbeli felhasználó)| [Közreműködő](built-in-roles.md#contributor) | Erőforráscsoport<br>(rbac-tutorial-resource-group) |

   ![Felhasználó szerepkör-hozzárendelései](./media/tutorial-role-assignments-user-powershell/rbac-role-assignments-user.png)

## <a name="create-a-user"></a>Felhasználó létrehozása

Szerepkör hozzárendeléséhez felhasználóra, csoportra vagy szolgáltatásnévre van szükség. Ha még nem rendelkezik felhasználóval, létrehozhat egyet.

1. Hozzon létre egy olyan jelszót az Azure Cloud Shellben, amely megfelel a jelszó összetettségére vonatkozó követelményeknek.

    ```azurepowershell
    $PasswordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile
    $PasswordProfile.Password = "Password"
    ```

1. Hozzon létre egy új felhasználót a tartományhoz a [New-AzureADUser](/powershell/module/azuread/new-azureaduser) paranccsal.

    ```azurepowershell
    New-AzureADUser -DisplayName "RBAC Tutorial User" -PasswordProfile $PasswordProfile `
      -UserPrincipalName "rbacuser@example.com" -AccountEnabled $true -MailNickName "rbacuser"
    ```
    
    ```Example
    ObjectId                             DisplayName        UserPrincipalName    UserType
    --------                             -----------        -----------------    --------
    11111111-1111-1111-1111-111111111111 RBAC Tutorial User rbacuser@example.com Member
    ```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Egy erőforráscsoport használatával bemutatjuk, hogyan rendelhet hozzá egy szerepkört erőforráscsoporti hatókörben.

1. Get a list of region locations using the [Get-AzLocation](/powershell/module/az.resources/get-azlocation) command.

   ```azurepowershell
   Get-AzLocation | select Location
   ```

1. Válasszon ki egy Önhöz közeli helyet, és rendelje hozzá egy változóhoz.

   ```azurepowershell
   $location = "westus"
   ```

1. Create a new resource group using the [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) command.

   ```azurepowershell
   New-AzResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Hozzáférés biztosítása

To grant access for the user, you use the [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) command to assign a role. Meg kell adnia a rendszerbiztonsági tagot, a szerepkör-definíciót és a hatókört.

1. Get the ID of your subscription using the [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) command.

    ```azurepowershell
    Get-AzSubscription
    ```

    ```Example
    Name     : Pay-As-You-Go
    Id       : 00000000-0000-0000-0000-000000000000
    TenantId : 22222222-2222-2222-2222-222222222222
    State    : Enabled
    ```

1. Mentse az előfizetési hatókört egy változóban.

    ```azurepowershell
    $subScope = "/subscriptions/00000000-0000-0000-0000-000000000000"
    ```

1. Rendelje hozzá az [Olvasó](built-in-roles.md#reader) szerepkört a felhasználóhoz az előfizetési hatókörben.

    ```azurepowershell
    New-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/44444444-4444-4444-4444-444444444444
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

1. Rendelje hozzá a [Közreműködő](built-in-roles.md#contributor) szerepkört a felhasználóhoz az erőforráscsoporti hatókörben.

    ```azurepowershell
    New-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

## <a name="list-access"></a>Hozzáférések felsorolása

1. To verify the access for the subscription, use the [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) command to list the role assignments.

    ```azurepowershell
    Get-AzRoleAssignment -SignInName rbacuser@example.com -Scope $subScope
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    A kimenetben láthatja, hogy az Olvasó szerepkör hozzá lett rendelve az RBAC-oktatóanyagbeli felhasználóhoz az előfizetési hatókörben.

1. To verify the access for the resource group, use the [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) command to list the role assignments.

    ```azurepowershell
    Get-AzRoleAssignment -SignInName rbacuser@example.com -ResourceGroupName "rbac-tutorial-resource-group"
    ```

    ```Example
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Contributor
    RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    
    RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
    Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
    DisplayName        : RBAC Tutorial User
    SignInName         : rbacuser@example.com
    RoleDefinitionName : Reader
    RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
    ObjectId           : 11111111-1111-1111-1111-111111111111
    ObjectType         : User
    CanDelegate        : False
    ```

    A kimenetben láthatja, hogy a Közreműködő és az Olvasó szerepkör hozzá lett rendelve az RBAC-oktatóanyagbeli felhasználóhoz. A Közreműködő szerepkör az rbac-tutorial-resource-group hatókörben van, az Olvasó szerepkör pedig örökölt az előfizetési hatókörben.

## <a name="optional-list-access-using-the-azure-portal"></a>(Választható) Hozzáférések felsorolása az Azure Portal használatával

1. Annak megtekintéséhez, hogyan jelennek meg a szerepkör-hozzárendelések az Azure Portalon, tekintse meg az előfizetés **Hozzáférés-vezérlés (IAM)** paneljét.

    ![Felhasználó szerepkör-hozzárendelései előfizetési hatókörben](./media/tutorial-role-assignments-user-powershell/role-assignments-subscription-user.png)

1. Tekintse meg az erőforráscsoport **Hozzáférés-vezérlés (IAM)** paneljét.

    ![Felhasználó szerepkör-hozzárendelései erőforráscsoporti hatókörben](./media/tutorial-role-assignments-user-powershell/role-assignments-resource-group-user.png)

## <a name="remove-access"></a>Hozzáférés eltávolítása

To remove access for users, groups, and applications, use [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) to remove a role assignment.

1. A következő paranccsal távolítsa el a felhasználó Közreműködő szerepkör-hozzárendelését az erőforráscsoporti hatókörben.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. A következő paranccsal távolítsa el a felhasználó Olvasó szerepkör-hozzárendelését az előfizetési hatókörben.

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a jelen oktatóanyag során létrehozott erőforrásokat, törölje az erőforráscsoportot és a felhasználót.

1. Delete the resource group using the [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) command.

    ```azurepowershell
    Remove-AzResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. When asked to confirm, type **Y**. It will take a few seconds to delete.

1. A felhasználó a [Remove-AzureADUser](/powershell/module/azuread/remove-azureaduser) paranccsal törölhető.

    ```azurepowershell
    Remove-AzureADUser -ObjectId "rbacuser@example.com"
    ```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Manage access to Azure resources using RBAC and Azure PowerShell](role-assignments-powershell.md)
