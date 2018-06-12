---
title: Egy felhasználó vagy csoport hozzárendelése az Azure Active Directory vállalati alkalmazások |} Microsoft Docs
description: Egy vállalati alkalmazás hozzárendelése egy felhasználóhoz vagy csoporthoz, az Azure Active Directory kiválasztása
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: barbkess
ms.reviewer: luleon
ms.openlocfilehash: 487312cb79c5c278849668a472acfda7823e4bc1
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303573"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás Azure Active Directoryban
Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás, a vállalati alkalmazások kezelésére a megfelelő engedélyekkel kell rendelkeznie, és a címtár globális rendszergazdának kell lennie.

> [!NOTE]
> A cikkben említett funkciók egy Azure Active Directory Premium P1 vagy Premium P2-licenc szükséges. További információkért lásd: a [Azure Active Directory árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/active-directory).

> [!NOTE]
> A Microsoft Applications (például az Office 365-alkalmazásokhoz) a PowerShell használatával felhasználók hozzárendelése egy vállalati alkalmazás.


## <a name="how-do-i-assign-user-access-to-an-enterprise-app-in-the-azure-portal"></a>Hogyan oszthatok ki a felhasználói hozzáférés a vállalati alkalmazásokhoz az Azure-portálon?
1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **minden szolgáltatás**, a mezőben adja meg Azure Active Directoryban, és válassza **Enter**.
3. Az a **Azure Active Directory - *directoryname***  (Ez azt jelenti, hogy az Azure AD panelen a kezelt könyvtár) panelen válassza ki **vállalati alkalmazások**.

    ![Vállalati alkalmazások megnyitásakor](./media/assign-user-or-group-access-portal/open-enterprise-apps.png)
4. Az a **vállalati alkalmazások** panelen válassza **összes alkalmazás**. A parancs megjeleníti az kezelheti az alkalmazásokat.
5. Az a **vállalati alkalmazások – összes alkalmazás** panelen, jelöljön ki egy alkalmazást.
6. Az a ***appname*** (Ez azt jelenti, hogy a panel nevű, a kijelölt alkalmazást a címben) panelen válassza ki **felhasználók és csoportok**.

    ![Az összes alkalmazások paranccsal](./media/assign-user-or-group-access-portal/select-app-users.png)
7. Az a ***appname*** **-felhasználó & csoport-hozzárendelés** panelen válassza a **Hozzáadás** parancsot.
8. Az a **hozzáadása hozzárendelés** panelen válassza **felhasználók és csoportok**.

    ![Az alkalmazás egy felhasználó vagy csoport hozzárendelése](./media/assign-user-or-group-access-portal/assign-users.png)
9. Az a **felhasználók és csoportok** panelen válasszon ki egy vagy több felhasználót vagy csoportot a listából, és válassza ki a **válasszon** gomb a panel alján.
10. Az a **hozzáadása hozzárendelés** panelen válassza **szerepkör**. Ezt követően a **Szerepkörválasztás** panelen válassza ki a megfelelő szerepkört a kiválasztott felhasználókra vagy csoportokra alkalmazza, és válassza ki a **OK** gomb a panel alján.
11. Az a **hozzáadása hozzárendelés** panelen válassza a **hozzárendelése** gomb a panel alján. A kijelölt felhasználók vagy csoportok engedélye a kiválasztott vállalati alkalmazás által meghatározott.

## <a name="how-do-i-assign-a-user-to-an-enterprise-app-using-powershell"></a>Hogyan oszthatok ki az a felhasználó a vállalati alkalmazásokhoz PowerShell használatával

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-parancssort.

    >[!NOTE] 
    > Telepítenie kell a AzureAD modul (a parancs `Install-Module -Name AzureAD`). Ha a NuGet-modulok vagy az új Azure Active Directory V2 PowerShell modul telepítésére kéri, írja be az I, és nyomja le az ENTER.

2. Futtatás `Connect-AzureAD` , és jelentkezzen be egy globális rendszergazdai felhasználói fiókkal.
3. Használja a következő parancsfájl egy felhasználó és a szerepkör hozzárendelése egy alkalmazás:

    ```powershell
    # Assign the values to the variables
    $username = "<You user's UPN>"
    $app_name = "<Your App's display name>"
    $app_role_name = "<App role display name>"
    
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```     

További információ a felhasználó hozzárendelése egy alkalmazás-szerepkör látogasson el a dokumentációja [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Egy csoport hozzárendelése egy vállalati alkalmazás, ki kell cserélni `Get-AzureADUser` rendelkező `Get-AzureADGroup`.

### <a name="example"></a>Példa

Ebben a példában a felhasználó Britta Simon rendeli hozzá a a [Microsoft munkahelyi Analytics](https://products.office.com/business/workplace-analytics) alkalmazás PowerShell-lel.

1. PowerShell rendelje hozzá a megfelelő értékeket a változók $username, $Alkalmazás_neve és $app_role_name. 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

2. Ebben a példában nem állapítható meg, mi az az alkalmazás-szerepkör hozzárendelése Britta Simon szeretnénk pontos nevét. A felhasználó ($user) segítségével a következő parancsokat, és a felhasználó egyszerű szolgáltatásnév ($sp) és a szolgáltatás egyszerű nevének megjelenítése.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
3. Futtassa a parancsot `$sp.AppRoles` elérhetővé válik a munkahelyi Analytics alkalmazás szerepkörök megjelenítéséhez. Ebben a példában szeretnénk Britta Simon az elemző (korlátozott hozzáférés) szerepkör hozzárendelése.
    
    ![Munkahelyi Analytics szerepkör](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

4. Rendelje hozzá a szerepkör nevét a `$app_role_name` változó.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

5. A következő parancsot a felhasználó hozzárendelése az alkalmazás-szerepkör:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>További lépések
* [Összes saját csoportok](../active-directory-groups-view-azure-portal.md)
* [Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazások](remove-user-or-group-access-portal.md)
* [Tiltsa le a felhasználói bejelentkezéseket a vállalati alkalmazás](disable-user-sign-in-portal.md)
* [Módosítja a nevét, vagy egy vállalati alkalmazás embléma](change-name-or-logo-portal.md)
