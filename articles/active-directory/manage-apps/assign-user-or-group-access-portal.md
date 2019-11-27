---
title: Felhasználó vagy csoport társítása egy vállalati alkalmazáshoz az Azure AD-ben
description: Vállalati alkalmazás kiválasztása a felhasználók vagy csoportok hozzárendeléséhez Azure Active Directory
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a5135f97ffb7d29c9fd928382ca4344beaa654d
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274742"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Felhasználó vagy csoport társítása vállalati alkalmazáshoz Azure Active Directory

Ha felhasználót vagy csoportot szeretne hozzárendelni egy vállalati alkalmazáshoz, a következő rendszergazdai szerepkörök bármelyikét hozzá kell rendelnie: globális rendszergazda, alkalmazás-rendszergazda, Felhőbeli alkalmazás rendszergazdája, vagy hozzá kell rendelni a vállalati alkalmazás tulajdonosaként.  Microsoft-alkalmazások (például Office 365-alkalmazások) esetén a PowerShell használatával rendeljen felhasználókat a vállalati alkalmazásokhoz.

> [!NOTE]
> Az ebben a cikkben tárgyalt funkciókra vonatkozó licencelési követelményekért tekintse meg a [Azure Active Directory díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="assign-a-user-to-an-app---portal"></a>Felhasználó társítása egy alkalmazáshoz – portál

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
1. Válassza a **minden szolgáltatás**lehetőséget, írja be Azure Active Directory a szövegmezőbe, majd válassza az **ENTER billentyűt**.
1. Válassza a **vállalati alkalmazások**lehetőséget.
1. A **vállalati alkalmazások – minden alkalmazás** panelen láthatja a felügyelhető alkalmazások listáját. Válasszon ki egy alkalmazást.
1. A ***AppName*** panelen (azaz a cím alatt a kiválasztott alkalmazás nevét tartalmazó ablaktáblán) válassza a **felhasználók & csoportok**lehetőséget.
1. A ***AppName*** **– felhasználó és csoportok** panelen válassza a **felhasználó hozzáadása**elemet.
1. A **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok**lehetőséget.

   ![Felhasználó vagy csoport társítása az alkalmazáshoz](./media/assign-user-or-group-access-portal/assign-users.png)

1. A **felhasználók és csoportok** panelen válasszon ki egy vagy több felhasználót vagy csoportot a listából, majd kattintson a panel alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** panelen válassza a **szerepkör**lehetőséget. Ezután a **szerepkör kiválasztása** panelen válassza ki a kiválasztott felhasználókra vagy csoportokra alkalmazni kívánt szerepkört, majd kattintson az **OK gombra** a panel alján.
1. A **hozzárendelés hozzáadása** panelen kattintson a **hozzárendelés** gombra a panel alján. A hozzárendelt felhasználók vagy csoportok rendelkeznek a kiválasztott szerepkör által a vállalati alkalmazáshoz megadott engedélyekkel.

## <a name="allow-all-users-to-access-an-app---portal"></a>Alkalmazás-portál elérésének engedélyezése minden felhasználó számára

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy olyan fiókkal, amely a címtár globális rendszergazdája.
1. Válassza a **minden szolgáltatás**lehetőséget, írja be Azure Active Directory a szövegmezőbe, majd válassza az **ENTER billentyűt**.
1. Válassza a **vállalati alkalmazások**lehetőséget.
1. A **vállalati alkalmazások** ablaktáblán válassza a **minden alkalmazás**lehetőséget. Itt láthatja a felügyelhető alkalmazásokat.
1. A **vállalati alkalmazások – minden alkalmazás** ablaktáblán válasszon ki egy alkalmazást.
1. A ***AppName*** ablaktáblán válassza a **Tulajdonságok**lehetőséget.
1. A  ***AppName* -Properties** ablaktáblán állítsa be a **kötelező felhasználói hozzárendelést?** beállítás **nem**értékre.

A **felhasználó-hozzárendelés szükséges?** beállítás:

- Ha a beállítás értéke Igen, akkor a felhasználóknak először hozzá kell rendelniük ezt az alkalmazást ahhoz, hogy hozzáférhessenek hozzá.
- Ha ez a beállítás a nem értékre van állítva, akkor az alkalmazás részletes URL-címére vagy az alkalmazás URL-címére navigáló összes felhasználó hozzáférése lesz elérhető
- Nem befolyásolja, hogy az alkalmazás megjelenik-e az alkalmazás-hozzáférési panelen. Ahhoz, hogy megjelenjen az alkalmazás a hozzáférési panelen, hozzá kell rendelnie egy megfelelő felhasználót vagy csoportot az alkalmazáshoz.
- A csak az SAML egyszeri bejelentkezéshez konfigurált felhőalapú alkalmazásokkal, az olyan alkalmazásproxy-alkalmazásokkal működik, amelyek Azure Active Directory előhitelesítést vagy közvetlenül az Azure AD-alkalmazás platformján létrehozott alkalmazásokat használják, amelyek az OAuth 2,0/OpenID Connect hitelesítést használják, miután egy felhasználó vagy rendszergazda beleegyezett az adott alkalmazásba. Lásd: [egyszeri bejelentkezés az alkalmazásokhoz](what-is-single-sign-on.md). Lásd: [a végfelhasználók beleegyezik az alkalmazásba való beleegyezett módszer](configure-user-consent.md).
- Ez a beállítás nem lép érvénybe, ha egy alkalmazás bármely más egyszeri bejelentkezési mód esetében konfigurálva van.

## <a name="assign-a-user-to-an-app---powershell"></a>Felhasználó társítása egy alkalmazáshoz – PowerShell

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-parancssort.

   > [!NOTE]
   > Telepítenie kell a AzureAD modult (használja a parancsot `Install-Module -Name AzureAD`). Ha a rendszer kéri, hogy telepítsen egy NuGet modult vagy az új Azure Active Directory v2 PowerShell-modult, írja be az Y értéket, és nyomja le az ENTER

1. Futtasson `Connect-AzureAD`, és jelentkezzen be egy globális rendszergazdai felhasználói fiókkal.
1. A következő parancsfájl használatával rendelhet hozzá felhasználót és szerepkört egy alkalmazáshoz:

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

A felhasználók alkalmazás-szerepkörhöz való hozzárendelésével kapcsolatos további információkért látogasson el a [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0) dokumentációjában

A csoportok vállalati alkalmazásokhoz való hozzárendeléséhez le kell cserélnie a `Get-AzureADUser`t `Get-AzureADGroup`.

### <a name="example"></a>Példa

Ez a példa a Britta-felhasználót a [Microsoft munkahelyi elemzési](https://products.office.com/business/workplace-analytics) alkalmazáshoz a PowerShell használatával rendeli hozzá.

1. A PowerShellben rendelje hozzá a megfelelő értékeket a változókhoz $username, $app _name és $app _role_name.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. Ebben a példában nem tudjuk, mi a pontos neve annak az alkalmazási szerepkörnek, amelyet a Britta Simonhoz szeretne rendelni. Futtassa a következő parancsokat a felhasználó ($user) és az egyszerű szolgáltatásnév ($sp) beszerzéséhez a felhasználói UPN és a szolgáltatásnév megjelenített neveinek használatával.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Futtassa a `$sp.AppRoles` parancsot a munkahelyi elemzési alkalmazáshoz elérhető szerepkörök megjelenítéséhez. Ebben a példában a Britta Simon (korlátozott hozzáférés) szerepkört szeretnénk hozzárendelni.

   ![A munkahelyi elemzési szerepkört használó felhasználó számára elérhető szerepkörök megjelenítése](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Rendelje hozzá a szerepkör nevét a `$app_role_name` változóhoz.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. A következő parancs futtatásával rendelje hozzá a felhasználót az alkalmazás szerepkörhöz:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="next-steps"></a>További lépések

- [Összes saját csoport megjelenítése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Felhasználó vagy csoport hozzárendelésének eltávolítása vállalati alkalmazásból](remove-user-or-group-access-portal.md)
- [Vállalati alkalmazás felhasználói bejelentkezésének letiltása](disable-user-sign-in-portal.md)
- [Vállalati alkalmazás nevének vagy emblémájának módosítása](change-name-or-logo-portal.md)
