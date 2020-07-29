---
title: Felhasználó vagy csoport társítása egy vállalati alkalmazáshoz az Azure AD-ben
description: Vállalati alkalmazás kiválasztása a felhasználók vagy csoportok hozzárendeléséhez Azure Active Directory
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/21/2020
ms.author: kenwith
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeab9cbb68567b6af3b1381b8ec5e67e82461ec
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763737"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Felhasználók vagy csoportok hozzárendelése vállalati alkalmazáshoz az Azure Active Directoryban

Ez a cikk bemutatja, hogyan rendelhet hozzá felhasználókat vagy csoportokat Azure Active Directory (Azure AD) vállalati alkalmazásaihoz a Azure Portal vagy a PowerShell használatával. Amikor felhasználót rendel hozzá egy alkalmazáshoz, az alkalmazás a felhasználók [saját alkalmazások hozzáférési paneljén](https://myapps.microsoft.com/) jelenik meg a könnyű hozzáférés érdekében. Ha az alkalmazás szerepköröket tesz elérhetővé, akkor egy adott szerepkört is hozzárendelhet a felhasználóhoz.

A nagyobb fokú szabályozás érdekében a vállalati alkalmazások bizonyos típusai úgy konfigurálhatók, hogy [felhasználói hozzárendelést kérjenek](#configure-an-application-to-require-user-assignment). 

Ha [felhasználót vagy csoportot szeretne hozzárendelni egy vállalati alkalmazáshoz](#assign-users-or-groups-to-an-app-via-the-azure-portal), globális rendszergazdaként, alkalmazás-rendszergazdaként, felhőalapú alkalmazás-rendszergazdaként vagy a vállalati alkalmazás hozzárendelt tulajdonosával kell bejelentkeznie.

> [!NOTE]
> A csoport alapú hozzárendeléshez prémium szintű Azure Active Directory P1 vagy P2 kiadás szükséges. A csoport alapú hozzárendelés csak biztonsági csoportok esetén támogatott. A beágyazott csoporttagságok és az Office 365-csoportok jelenleg nem támogatottak. A cikkben tárgyalt funkciókra vonatkozó további licencelési követelményekért tekintse meg a [Azure Active Directory díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/active-directory). 

## <a name="configure-an-application-to-require-user-assignment"></a>Alkalmazás konfigurálása felhasználói hozzárendelés megköveteléséhez

A következő típusú alkalmazások esetében lehetősége van megkövetelni, hogy a felhasználók az alkalmazáshoz legyenek hozzárendelve ahhoz, hogy hozzáférhessenek a szolgáltatáshoz:

- Az összevont egyszeri bejelentkezéshez (SSO) SAML-alapú hitelesítéssel konfigurált alkalmazások
- Azure Active Directory előhitelesítést használó alkalmazásproxy-alkalmazások
- Az Azure AD Application platformra épülő, OAuth 2,0/OpenID Connect hitelesítést használó alkalmazások, miután egy felhasználó vagy rendszergazda beleegyezett az adott alkalmazásba.

Ha felhasználói hozzárendelésre van szükség, csak azokat a felhasználókat lehet bejelentkezni, akiket kifejezetten az alkalmazáshoz rendeltek (közvetlen felhasználói hozzárendelés vagy csoporttagság alapján). Hozzáférhetnek az alkalmazáshoz a saját alkalmazások oldalon vagy egy közvetlen hivatkozás használatával. 

Ha nincs *szükség*hozzárendelésre, mert ezt a beállítást a **nem** értékre állította be, vagy mert az alkalmazás más egyszeri bejelentkezéses módot használ, akkor bármely felhasználó hozzáférhet az alkalmazáshoz, ha az alkalmazás **Tulajdonságok** lapján közvetlen hivatkozással rendelkezik az alkalmazásra vagy a **felhasználói hozzáférési URL-címre** . 

Ez a beállítás nem befolyásolja, hogy egy alkalmazás megjelenik-e a saját alkalmazások hozzáférési paneljén. Az alkalmazások a felhasználók saját alkalmazások hozzáférési panelén jelennek meg, miután hozzárendelt egy felhasználót vagy csoportot az alkalmazáshoz. A háttérben tekintse meg az [alkalmazások hozzáférésének kezelése](what-is-access-management.md)című témakört.


Felhasználói hozzárendelés megkövetelése egy alkalmazáshoz:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) egy rendszergazdai fiókkal vagy az alkalmazás tulajdonosaként.

2. Válassza a **Azure Active Directory**lehetőséget. A bal oldali navigációs menüben válassza a **vállalati alkalmazások**lehetőséget.

3. Válassza ki az alkalmazást a listából. Ha nem látja az alkalmazást, kezdje el beírni a nevét a keresőmezőbe. Vagy használja a szűrő vezérlőelemeket az alkalmazás típusának, állapotának vagy láthatóságának kiválasztásához, majd válassza az **alkalmaz**lehetőséget.

4. A bal oldali navigációs menüben válassza a **Tulajdonságok**lehetőséget.

5. Győződjön meg arról, hogy a **felhasználó-hozzárendelés szükséges?** a váltógomb **Igen**értékre van állítva.

   > [!NOTE]
   > Ha a **felhasználó-hozzárendelés szükséges?** a váltógomb nem érhető el, a PowerShell használatával állíthatja be a appRoleAssignmentRequired tulajdonságot az egyszerű szolgáltatásnév számára.

6. Kattintson a **Save (Mentés** ) gombra a képernyő tetején.

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Felhasználók vagy csoportok társítása egy alkalmazáshoz a Azure Portal használatával

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazda, alkalmazás-rendszergazda vagy Felhőbeli alkalmazás rendszergazdai fiókjával vagy a vállalati alkalmazás hozzárendelt tulajdonosával.
2. Válassza a **Azure Active Directory**lehetőséget. A bal oldali navigációs menüben válassza a **vállalati alkalmazások**lehetőséget.
3. Válassza ki az alkalmazást a listából. Ha nem látja az alkalmazást, kezdje el beírni a nevét a keresőmezőbe. Vagy használja a szűrő vezérlőelemeket az alkalmazás típusának, állapotának vagy láthatóságának kiválasztásához, majd válassza az **alkalmaz**lehetőséget.
4. A bal oldali navigációs menüben válassza a **felhasználók és csoportok**lehetőséget.
   > [!NOTE]
   > Ha felhasználókat szeretne hozzárendelni Microsoft-alkalmazásokhoz, például az Office 365-alkalmazásokhoz, néhány ilyen alkalmazás a PowerShellt használja. 
5. Válassza a **felhasználó hozzáadása** gombot.
6. A **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok**lehetőséget.
7. Válassza ki azt a felhasználót vagy csoportot, amelyet hozzá szeretne rendelni az alkalmazáshoz, vagy kezdje el beírni a felhasználó vagy csoport nevét a keresőmezőbe. Több felhasználót és csoportot is kiválaszthat, és a **kiválasztott elemek**területen megjelennek a kiválasztások.
8. Ha elkészült, kattintson a **kiválasztás**gombra.

   ![Felhasználó vagy csoport társítása az alkalmazáshoz](./media/assign-user-or-group-access-portal/assign-users.png)

9. A **felhasználók és csoportok** panelen válasszon ki egy vagy több felhasználót vagy csoportot a listából, majd kattintson a panel alján található **kiválasztás** gombra.
10. Ha az alkalmazás támogatja azt, hozzárendelhet egy szerepkört a felhasználóhoz vagy a csoportjához. A **hozzárendelés hozzáadása** panelen válassza a **szerepkör kiválasztása**lehetőséget. Ezután a **szerepkör kiválasztása** panelen válassza ki a kiválasztott felhasználókra vagy csoportokra alkalmazni kívánt szerepkört, majd kattintson az **OK gombra** a panel alján. 

    > [!NOTE]
    > Ha az alkalmazás nem támogatja a szerepkör kiválasztását, a rendszer az alapértelmezett hozzáférési szerepkört rendeli hozzá. Ebben az esetben az alkalmazás kezeli a felhasználók hozzáférési szintjét.

2. A **hozzárendelés hozzáadása** panelen kattintson a **hozzárendelés** gombra a panel alján.

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Felhasználók vagy csoportok társítása egy alkalmazáshoz a PowerShell használatával

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-parancssort.

   > [!NOTE]
   > Telepítenie kell a AzureAD modult (használja a parancsot `Install-Module -Name AzureAD` ). Ha a rendszer kéri, hogy telepítsen egy NuGet modult vagy az új Azure Active Directory v2 PowerShell-modult, írja be az Y értéket, és nyomja le az ENTER

1. Futtassa a parancsot, `Connect-AzureAD` és jelentkezzen be egy globális rendszergazdai felhasználói fiókkal.
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

A felhasználók alkalmazás-szerepkörhöz való hozzárendelésével kapcsolatos további információkért tekintse meg a [New-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)dokumentációját.

Ha vállalati alkalmazáshoz szeretne hozzárendelni egy csoportot, a és a helyére a következőt kell cserélnie: `Get-AzureADUser` `Get-AzureADGroup` `New-AzureADUserAppRoleAssignment` `New-AzureADGroupAppRoleAssignment` .

A csoportok alkalmazás-szerepkörhöz való hozzárendelésével kapcsolatos további információkért tekintse meg a [New-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/azuread/new-azureadgroupapproleassignment?view=azureadps-2.0)dokumentációját.

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

1. Futtassa a parancsot a `$sp.AppRoles` munkahelyi elemzési alkalmazáshoz elérhető szerepkörök megjelenítéséhez. Ebben a példában a Britta Simon (korlátozott hozzáférés) szerepkört szeretnénk hozzárendelni.

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

## <a name="related-articles"></a>Kapcsolódó cikkek

- [További információ az alkalmazásokhoz való végfelhasználói hozzáférésről](end-user-experiences.md)
- [Azure AD hozzáférési panel üzembe helyezésének megtervezése](access-panel-deployment-plan.md)
- [Az alkalmazásokhoz való hozzáférés kezelése](what-is-access-management.md)
 
## <a name="next-steps"></a>További lépések

- [Összes saját csoport megjelenítése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Felhasználó vagy csoport hozzárendelésének eltávolítása vállalati alkalmazásból](remove-user-or-group-access-portal.md)
- [Vállalati alkalmazás felhasználói bejelentkezésének letiltása](disable-user-sign-in-portal.md)
- [Vállalati alkalmazás nevének vagy emblémájának módosítása](change-name-or-logo-portal.md)
