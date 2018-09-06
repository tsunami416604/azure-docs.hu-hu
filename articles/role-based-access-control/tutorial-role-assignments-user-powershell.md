---
title: Oktatóanyag – Hozzáférés biztosítása egy felhasználó számára az RBAC és az Azure PowerShell használatával | Microsoft Docs
description: A szerepköralapú hozzáférés-vezérlés (RBAC) segítségével hozzáférést biztosíthat egy felhasználó számára, hogy mindent megtekinthessen az előfizetésben és mindent kezelhessen egy erőforráscsoportban az Azure PowerShell használatával.
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
ms.date: 06/11/2018
ms.author: rolyon
ms.openlocfilehash: cac585b36c3b5969a18c941215b623443850cd4c
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301728"
---
# <a name="tutorial-grant-access-for-a-user-using-rbac-and-azure-powershell"></a>Oktatóanyag: Hozzáférés biztosítása egy felhasználó számára az RBAC és az Azure PowerShell használatával

A [szerepköralapú hozzáférés-vezérlés (RBAC)](overview.md) az erőforrásokhoz való hozzáférés kezelésének a módja az Azure-ban. Ebben az oktatóanyagban hozzáférést biztosít egy felhasználó számára, hogy mindent megtekinthessen az előfizetésben és mindent kezelhessen egy erőforráscsoportban az Azure PowerShell használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hozzáférés biztosítása egy felhasználó számára különböző hatókörökben
> * Hozzáférések felsorolása
> * Hozzáférés eltávolítása

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

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

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Egy erőforráscsoport használatával bemutatjuk, hogyan rendelhet hozzá egy szerepkört erőforráscsoporti hatókörben.

1. Kérje le a régiók helyeit a [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation) paranccsal.

   ```azurepowershell
   Get-AzureRmLocation | select Location
   ```

1. Válasszon ki egy Önhöz közeli helyet, és rendelje hozzá egy változóhoz.

   ```azurepowershell
   $location = "westus"
   ```

1. Hozzon létre egy új erőforráscsoportot a [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) paranccsal.

   ```azurepowershell
   New-AzureRmResourceGroup -Name "rbac-tutorial-resource-group" -Location $location
   ```

   ```Example
   ResourceGroupName : rbac-tutorial-resource-group
   Location          : westus
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/rbac-tutorial-resource-group
   ```

## <a name="grant-access"></a>Hozzáférés biztosítása

A felhasználó hozzáférésének biztosításához használja a [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) parancsot egy szerepkör hozzárendelésére. Meg kell adnia a rendszerbiztonsági tagot, a szerepkör-definíciót és a hatókört.

1. Kérje le az előfizetése azonosítóját a [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) paranccsal.

    ```azurepowershell
    Get-AzureRmSubscription
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
    New-AzureRmRoleAssignment -SignInName rbacuser@example.com `
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
    New-AzureRmRoleAssignment -SignInName rbacuser@example.com `
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

1. Az előfizetéshez való hozzáférés ellenőrzéséhez használja a [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) parancsot a szerepkör-hozzárendelések felsorolásához.

    ```azurepowershell
    Get-AzureRmRoleAssignment -SignInName rbacuser@example.com -Scope $subScope
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

1. Az erőforráscsoporthoz való hozzáférés ellenőrzéséhez használja a [Get-AzureRmRoleAssignment](/powershell/module/azurerm.resources/get-azurermroleassignment) parancsot a szerepkör-hozzárendelések felsorolásához.

    ```azurepowershell
    Get-AzureRmRoleAssignment -SignInName rbacuser@example.com -ResourceGroupName "rbac-tutorial-resource-group"
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

Felhasználók, csoportok és alkalmazások hozzáférésének eltávolításához használja, a [Remove-AzureRmRoleAssignment](/powershell/module/azurerm.resources/remove-azurermroleassignment) parancsot a szerepkör-hozzárendelés eltávolításához.

1. A következő paranccsal távolítsa el a felhasználó Közreműködő szerepkör-hozzárendelését az erőforráscsoporti hatókörben.

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Contributor" `
      -ResourceGroupName "rbac-tutorial-resource-group"
    ```

1. A következő paranccsal távolítsa el a felhasználó Olvasó szerepkör-hozzárendelését az előfizetési hatókörben.

    ```azurepowershell
    Remove-AzureRmRoleAssignment -SignInName rbacuser@example.com `
      -RoleDefinitionName "Reader" `
      -Scope $subScope
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a jelen oktatóanyag során létrehozott erőforrásokat, törölje az erőforráscsoportot és a felhasználót.

1. Az erőforráscsoport a [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) paranccsal törölhető.

    ```azurepowershell
    Remove-AzureRmResourceGroup -Name "rbac-tutorial-resource-group"
    ```

    ```Example
    Confirm
    Are you sure you want to remove resource group 'rbac-tutorial-resource-group'
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```
    
1. Ha rendszer megerősítést kér, írja be a következőt: **Y**. A törlés néhány másodpercet vesz igénybe.

1. A felhasználó a [Remove-AzureADUser](/powershell/module/azuread/remove-azureaduser) paranccsal törölhető.

    ```azurepowershell
    Remove-AzureADUser -ObjectId "rbacuser@example.com"
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hozzáférés kezelése az RBAC és a PowerShell használatával](role-assignments-powershell.md)
