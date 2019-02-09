---
title: Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazást az Azure Active Directoryban |} A Microsoft Docs
description: Vállalati alkalmazás hozzárendelése egy felhasználóhoz vagy csoporthoz, az Azure Active Directory kiválasztása
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: celested
ms.reviewer: luleon
ms.openlocfilehash: 6238ffa75cc664e153025b76a5f70d9e4a4c4c43
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55964849"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Egy felhasználó vagy csoport hozzárendelése az Azure Active Directory vállalati alkalmazás
Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazást, a vállalati alkalmazások kezelésére a megfelelő engedélyekkel kell rendelkeznie, és a címtár globális rendszergazdának kell lennie.

> [!NOTE]
> Ebben a cikkben tárgyalt funkciók licenckövetelmények, lásd: a [Azure Active Directory díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/active-directory).

> [!NOTE]
> A Microsoft Applications (például az Office 365-alkalmazások) a PowerShell használatával felhasználók hozzárendelése egy vállalati alkalmazást.


## <a name="assign-a-user-to-an-app---portal"></a>Felhasználó hozzárendelése alkalmazáshoz – portál
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **minden szolgáltatás**, a szövegmezőbe írja be az Azure Active Directory, és válassza **Enter**.
3. Válassza ki **vállalati alkalmazások**.

    ![Vállalati alkalmazások megnyitása](./media/assign-user-or-group-access-portal/open-enterprise-apps.png)
4. Az a **vállalati alkalmazások** panelen válassza ki **minden alkalmazás**. Ez felsorolja az alkalmazásokat, kezelheti.
5. Az a **nagyvállalati alkalmazások – minden alkalmazás** panelen jelöljön ki egy alkalmazást.
6. Az a ***appname*** (azaz a panelen címében szerepel a kijelölt alkalmazás nevét) panelen adja meg **felhasználók és csoportok**.

    ![Az alkalmazások minden parancs kiválasztása](./media/assign-user-or-group-access-portal/select-app-users.png)
7. Az a ***appname*** **-felhasználó és csoport-hozzárendelés** panelen válassza ki a **Hozzáadás** parancsot.
8. Az a **hozzárendelés hozzáadása** panelen válassza ki **felhasználók és csoportok**.

    ![Egy felhasználó vagy csoport hozzárendelése az alkalmazáshoz](./media/assign-user-or-group-access-portal/assign-users.png)
9. Az a **felhasználók és csoportok** panelen válasszon ki egy vagy több felhasználót vagy csoportot a listából, és válassza ki a **kiválasztása** gombra a panel alján.
10. Az a **hozzárendelés hozzáadása** panelen válassza ki **szerepkör**. Ezután a a **Szerepkörválasztás** panelen válassza ki a megfelelő szerepkört a kiválasztott felhasználókra vagy csoportokra vonatkoznak, és válassza ki a **OK** gombra a panel alján.
11. Az a **hozzárendelés hozzáadása** panelen válassza ki a **hozzárendelése** gombra a panel alján. A hozzárendelt felhasználók vagy csoportok rendelkezik a kijelölt szerepkört a vállalati alkalmazás által meghatározott engedélyekkel.

## <a name="allow-all-users-to-access-an-app---portal"></a>Engedélyezése minden felhasználó számára a hozzáférést egy alkalmazáshoz – portál
Minden felhasználó egy alkalmazáshoz való hozzáférés engedélyezése:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
2. Válassza ki **minden szolgáltatás**, a szövegmezőbe írja be az Azure Active Directory, és válassza **Enter**.
3. Válassza ki **vállalati alkalmazások**.
4. Az a **vállalati alkalmazások** panelen válassza ki **minden alkalmazás**. Ez felsorolja az alkalmazásokat, kezelheti.
5. Az a **nagyvállalati alkalmazások – minden alkalmazás** panelen jelöljön ki egy alkalmazást.
6. Az a ***appname*** panelen válassza ki **tulajdonságok**.
7. Az a  ***appname* -tulajdonságok** panelen adja meg a **kell felhasználó-hozzárendelés?** beállítást **nem**. 

A **kell felhasználó-hozzárendelés?** lehetőséget:

- Nem érinti-e egy alkalmazás az alkalmazás-hozzáférési panel jelenik meg. Az alkalmazás a hozzáférési panel megjelenítéséhez, hozzá kell rendelni egy megfelelő felhasználót vagy csoportot az alkalmazás.
- Csak a felhőbeli alkalmazásokkal, az SAML egyszeri bejelentkezésre konfigurált funkciók, és a helyszíni alkalmazások konfigurálása az alkalmazásproxy használatával. Lásd: [egyszeri bejelentkezési alkalmazások](what-is-single-sign-on.md).
- Megköveteli, hogy felhasználók beleegyezik abba, hogy egy alkalmazás. Rendszergazdai jóváhagyás minden felhasználó számára biztosíthat.  Lásd: [konfigurálása a módon végfelhasználók beleegyezik abba, hogy egy alkalmazás](configure-user-consent.md).


## <a name="assign-a-user-to-an-app---powershell"></a>Felhasználó hozzárendelése alkalmazáshoz – PowerShell

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-parancssort.

    >[!NOTE] 
    > Az Azure ad-modul telepítéséhez szüksége (a parancs használata `Install-Module -Name AzureAD`). Ha kéri, telepítse a NuGet-modult vagy az új Azure Active Directory V2 PowerShell modul, írja be az Y, és nyomja le az ENTER billentyűt.

2. Futtatás `Connect-AzureAD` , és jelentkezzen be egy globális rendszergazdai felhasználói fiókkal.
3. A következő szkript használatával egy felhasználó és szerepkör hozzárendelése egy alkalmazáshoz:

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

Felhasználók hozzárendelése egy alkalmazás-szerepkör kapcsolatos további részletekért látogasson el a dokumentációját [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Egy csoport hozzárendelése egy vállalati alkalmazást, ki kell cserélni `Get-AzureADUser` a `Get-AzureADGroup`.

### <a name="example"></a>Példa

Ebben a példában a felhasználó Britta Simon rendeli, a [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) alkalmazás PowerShell-lel.

1. A PowerShell rendelje hozzá a megfelelő értékeket a változók $username, $app_name és $app_role_name. 

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

2. Ebben a példában nem tudjuk Mi az az alkalmazás-szerepkör hozzárendelése a Britta Simon szeretnénk pontos nevét. A felhasználó ($user) beolvasni a következő parancsokat, és a használatával a felhasználó egyszerű szolgáltatásnév ($sp) és az egyszerű szolgáltatás nevének megjelenítéséhez.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```
        
3. Futtassa a parancsot `$sp.AppRoles` megjelenítéséhez a Workplace Analytics alkalmazás számára elérhető szerepköröket. Ebben a példában szeretnénk Britta Simon az elemző (korlátozott hozzáférés) szerepkör hozzárendelését.
    
    ![Workplace Analytics szerepkör](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

4. A szerepkör nevét rendelje hozzá a `$app_role_name` változó.
        
    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

5. A következő paranccsal rendelje hozzá a felhasználót az alkalmazás-szerepkör:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>További lépések
* [Megjelenik az összes saját csoportok](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Egy felhasználó vagy csoport-hozzárendelés eltávolítása a vállalati alkalmazás](remove-user-or-group-access-portal.md)
* [Tiltsa le a felhasználók bejelentkezési folyamatába egy vállalati alkalmazás](disable-user-sign-in-portal.md)
* [A name vagy a vállalati alkalmazás emblémájának módosítása](change-name-or-logo-portal.md)
