---
title: Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz az Azure AD-ben
description: Egy nagyvállalati alkalmazás kiválasztása felhasználó vagy csoport hozzárendeléséhez az Azure Active Directoryban
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: mimart
ms.reviewer: luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 186e36e4625a60362c54972b16b53f0f3e6753fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409192"
---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory"></a>Felhasználó vagy csoport hozzárendelése vállalati alkalmazáshoz az Azure Active Directoryban

Ez a cikk bemutatja, hogyan rendelhet hozzá felhasználókat vagy csoportokat a vállalati alkalmazásokhoz az Azure Active Directoryban (Azure AD), akár az Azure Portalon belül, akár a PowerShell használatával. Amikor egy felhasználót hozzárendel egy alkalmazáshoz, az alkalmazás megjelenik a felhasználó [Saját alkalmazások hozzáférési paneljén](https://myapps.microsoft.com/) a könnyű hozzáférés érdekében. Ha az alkalmazás szerepköröket tár fel, adott szerepkört is hozzárendelhet a felhasználóhoz.

A nagyobb ellenőrzés érdekében bizonyos típusú vállalati alkalmazások beállíthatók úgy, hogy [felhasználói hozzárendelést igényeljenek.](#configure-an-application-to-require-user-assignment) 

Ha [egy felhasználót vagy csoportot egy vállalati alkalmazáshoz szeretne hozzárendelni,](#assign-users-or-groups-to-an-app-via-the-azure-portal)globális rendszergazdaként, alkalmazás-rendszergazdaként, felhőalkalmazás-rendszergazdaként vagy a vállalati alkalmazás hozzárendelt tulajdonosaként kell bejelentkeznie.

> [!NOTE]
> A csoportalapú hozzárendeléshez Az Azure Active Directory Premium P1 vagy P2 kiadás szükséges. A csoportalapú hozzárendelés csak biztonsági csoportok esetén támogatott. A beágyazott csoporttagságok és az Office 365-csoportok jelenleg nem támogatottak. A cikkben tárgyalt funkciók további licencelési követelményeit az [Azure Active Directory díjszabási lapján](https://azure.microsoft.com/pricing/details/active-directory)találja. 

## <a name="configure-an-application-to-require-user-assignment"></a>Alkalmazás konfigurálása felhasználói hozzárendelés megkövetelése érdekében

A következő típusú alkalmazások esetében megkövetelheti a felhasználóktól, hogy az alkalmazáshoz rendeljék őket, mielőtt hozzáférhetnének hozzájuk:

- SAML-alapú hitelesítéssel rendelkező egyszeri bejelentkezésre (SSO) konfigurált alkalmazások
- Az Azure Active Directory előhitelesítését használó alkalmazásproxy-alkalmazások
- Az Azure AD alkalmazásplatformra épülő alkalmazások, amelyek OAuth 2.0 / OpenID Connect hitelesítést használnak, miután egy felhasználó vagy rendszergazda hozzájárult az adott alkalmazáshoz.

Ha felhasználói hozzárendelésre van szükség, csak azok a felhasználók tudnak bejelentkezni, akiket kifejezetten hozzárendel az alkalmazáshoz. Az alkalmazást a Saját alkalmazások oldalon vagy közvetlen hivatkozással érhetik el. 

Ha a hozzárendelés *nem szükséges*, vagy azért, mert ezt a beállítást **Nem-re** állította, vagy mert az alkalmazás egy másik SSO módot használ, bármely felhasználó hozzáférhet az alkalmazáshoz, ha közvetlen hivatkozással rendelkezik az alkalmazásra vagy a felhasználói **hozzáférés URL-címére** az alkalmazás **tulajdonságai** lapján. 

Ez a beállítás nincs hatással arra, hogy egy alkalmazás megjelenik-e a Saját alkalmazások hozzáférési panelen. Az alkalmazások akkor jelennek meg a felhasználók Saját alkalmazások hozzáférési paneljein, ha egy felhasználót vagy csoportot rendelt az alkalmazáshoz. A háttérről az [Alkalmazásokhoz való hozzáférés kezelése](what-is-access-management.md)témakörben található.


Felhasználó-hozzárendelés megkövetelése egy alkalmazáshoz:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) rendszergazdai fiókkal vagy az alkalmazás tulajdonosaként.

2. Válassza az **Azure Active Directory**lehetőséget. A bal oldali navigációs menüben válassza a **Vállalati alkalmazások lehetőséget.**

3. Válassza ki az alkalmazást a listából. Ha nem látja az alkalmazást, írja be a nevét a keresőmezőbe. Vagy használja a szűrővezérlőket az alkalmazás típusának, állapotának vagy láthatóságának kiválasztásához, majd válassza **az Alkalmaz**lehetőséget.

4. A bal oldali navigációs menüben válassza a **Tulajdonságok lehetőséget.**

5. Győződjön meg arról, hogy a **szükséges felhasználói hozzárendelés** van bejelölve: **Igen**.

   > [!NOTE]
   > Ha a **felhasználói hozzárendelés szükséges?** váltás nem érhető el, a PowerShell segítségével beállíthatja az appRoleAssignmentRequired tulajdonságot az egyszerű szolgáltatáson.

6. Válassza a **Mentés** gombot a képernyő tetején.

## <a name="assign-users-or-groups-to-an-app-via-the-azure-portal"></a>Felhasználók vagy csoportok hozzárendelése egy alkalmazáshoz az Azure Portalon keresztül

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) egy globális rendszergazdával, alkalmazás-rendszergazdával vagy felhőalkalmazás-rendszergazdai fiókkal, vagy a vállalati alkalmazás hozzárendelt tulajdonosaként.
2. Válassza az **Azure Active Directory**lehetőséget. A bal oldali navigációs menüben válassza a **Vállalati alkalmazások lehetőséget.**
3. Válassza ki az alkalmazást a listából. Ha nem látja az alkalmazást, írja be a nevét a keresőmezőbe. Vagy használja a szűrővezérlőket az alkalmazás típusának, állapotának vagy láthatóságának kiválasztásához, majd válassza **az Alkalmaz**lehetőséget.
4. A bal oldali navigációs menüben válassza a **Felhasználók és csoportok**lehetőséget.
   > [!NOTE]
   > Ha felhasználókat szeretne hozzárendelni a Microsoft-alkalmazásokhoz, például az Office 365-alkalmazásokhoz, ezek közül néhány a PowerShellt használja. 
5. Válassza a **Felhasználó hozzáadása** gombot.
6. A **Hozzárendelés hozzáadása** ablaktáblán válassza a **Felhasználók és csoportok**lehetőséget.
7. Jelölje ki az alkalmazáshoz rendelni kívánt felhasználót vagy csoportot, vagy kezdje el beírni a felhasználó vagy csoport nevét a keresőmezőbe. Több felhasználót és csoportot is kiválaszthat, és a beállítások a **Kijelölt elemek**területen jelennek meg.
8. Ha végzett, kattintson a **Kijelölés gombra.**

   ![Felhasználó vagy csoport hozzárendelése az alkalmazáshoz](./media/assign-user-or-group-access-portal/assign-users.png)

9. A **Felhasználók és csoportok** ablaktáblán jelöljön ki egy vagy több felhasználót vagy csoportot a listából, majd válassza a **Kijelölés** gombot az ablaktábla alján.
10. Ha az alkalmazás támogatja, szerepkört rendelhet a felhasználóhoz vagy csoporthoz. A **Hozzárendelés hozzáadása** ablaktáblán válassza a **Szerepkör kiválasztása lehetőséget.** Ezután a **Szerepkör kiválasztása** ablaktáblán válassza ki a kijelölt felhasználókra vagy csoportokra alkalmazni kívánt szerepkört, majd az ablaktábla alján válassza az **OK** gombot. 

    > [!NOTE]
    > Ha az alkalmazás nem támogatja a szerepkör kiválasztását, az alapértelmezett hozzáférési szerepkör van hozzárendelve. Ebben az esetben az alkalmazás kezeli a felhasználók hozzáférési szintjét.

2. A **Hozzárendelés hozzáadása** ablaktáblán válassza a **Hozzárendelés** gombot az ablaktábla alján.

## <a name="assign-users-or-groups-to-an-app-via-powershell"></a>Felhasználók vagy csoportok hozzárendelése egy alkalmazáshoz a PowerShellen keresztül

1. Nyisson meg egy emelt szintű Windows PowerShell parancssort.

   > [!NOTE]
   > Telepítenie kell az AzureAD modult `Install-Module -Name AzureAD`(használja a parancsot). Ha a rendszer kéri egy NuGet modul vagy az új Azure Active Directory V2 PowerShell-modul telepítését, írja be az Y értéket, és nyomja le az ENTER billentyűt.

1. Globális `Connect-AzureAD` rendszergazdai felhasználói fiókkal futtasson és jelentkezzen be.
1. A következő parancsfájl segítségével rendeljen hozzá egy felhasználót és egy szerepkört egy alkalmazáshoz:

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

Ha többet szeretne tudni arról, hogy miként rendelhet hozzá egy felhasználót egy alkalmazásszerepkörhöz, látogasson el a [New-AzureADUserAppRoleAssignment dokumentációjára.](https://docs.microsoft.com/powershell/module/azuread/new-azureaduserapproleassignment?view=azureadps-2.0)

Ha csoportot szeretne hozzárendelni egy vállalati `Get-AzureADUser` alkalmazáshoz, le kell cserélnie a alkalmazásra. `Get-AzureADGroup`

### <a name="example"></a>Példa

Ez a példa britta Simon felhasználót rendeli hozzá a [Microsoft Workplace Analytics](https://products.office.com/business/workplace-analytics) alkalmazáshoz a PowerShell használatával.

1. A PowerShellben rendelje hozzá a megfelelő értékeket a $username, $app_name és $app_role_name változókhoz.

    ```powershell
    # Assign the values to the variables
    $username = "britta.simon@contoso.com"
    $app_name = "Workplace Analytics"
    ```

1. Ebben a példában nem tudjuk, mi a pontos neve az alkalmazás szerepkör t szeretnénk rendelni Britta Simon. Futtassa a következő parancsokat a felhasználó ($user) és a szolgáltatásnév ($sp) leolvasásához a felhasználó egyszerű felhasználónév és a szolgáltatásnév megjelenítendő nevei használatával.

    ```powershell
    # Get the user to assign, and the service principal for the app to assign to
    $user = Get-AzureADUser -ObjectId "$username"
    $sp = Get-AzureADServicePrincipal -Filter "displayName eq '$app_name'"
    ```

1. Futtassa `$sp.AppRoles` a parancsot a Workplace Analytics alkalmazáshoz elérhető szerepkörök megjelenítéséhez. Ebben a példában azt szeretnénk, hogy hozzá Britta Simon az elemző (korlátozott hozzáférésű) szerepkör.

   ![A Workplace Analytics szerepkörrel a felhasználó számára elérhető szerepkörök megjelenítése](./media/assign-user-or-group-access-portal/workplace-analytics-role.png)

1. Rendelje hozzá a `$app_role_name` szerepkör nevét a változóhoz.

    ```powershell
    # Assign the values to the variables
    $app_role_name = "Analyst (Limited access)"
    $appRole = $sp.AppRoles | Where-Object { $_.DisplayName -eq $app_role_name }
    ```

1. Futtassa a következő parancsot, és rendelje hozzá a felhasználót az alkalmazásszerepkörhöz:

    ```powershell
    # Assign the user to the app role
    New-AzureADUserAppRoleAssignment -ObjectId $user.ObjectId -PrincipalId $user.ObjectId -ResourceId $sp.ObjectId -Id $appRole.Id
    ```

## <a name="related-articles"></a>Kapcsolódó cikkek

- [További információ az alkalmazásokhoz való végfelhasználói hozzáférésről](end-user-experiences.md)
- [Azure AD hozzáférési panel telepítésének megtervezése](access-panel-deployment-plan.md)
- [Alkalmazásokhoz való hozzáférés kezelése](what-is-access-management.md)
 
## <a name="next-steps"></a>További lépések

- [Az összes csoport megtekintése](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Felhasználó- vagy csoporthozzárendelés eltávolítása vállalati alkalmazásból](remove-user-or-group-access-portal.md)
- [Vállalati alkalmazás felhasználói bejelentkezésének letiltása](disable-user-sign-in-portal.md)
- [Vállalati alkalmazás nevének vagy emblémájának módosítása](change-name-or-logo-portal.md)
