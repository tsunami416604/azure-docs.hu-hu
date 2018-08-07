---
title: Ismerkedés az Azure AD AngularJS |} A Microsoft Docs
description: Hogyan hozhat létre egy AngularJS egyoldalas alkalmazás, amely integrálható az Azure AD-be, és meghívja az Azure AD-védelemmel ellátott API-k OAuth használatával.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 11/30/2017
ms.author: celested
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0c7f6a0e447e3b48cdd1df684dc105ece1e98f66
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39581597"
---
# <a name="azure-ad-angularjs-getting-started"></a>Ismerkedés az Azure AD AngularJS

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Az Azure Active Directory (Azure AD) révén egyszerű és könnyen érthető megjegyzésblokkok írására is hozzáadhat a bejelentkezési, kijelentkezési és biztonságos OAuth API meghívja az egyoldalas alkalmazásokat. Lehetővé teszi az alkalmazások számára a Windows Server Active Directory-fiókkal rendelkező felhasználók hitelesítése, és minden olyan webes API, amely az Azure AD megvédi, például az Office 365 API-k vagy az Azure API felhasználását.

A böngészőben futó JavaScript-alkalmazások Azure ad-ben az Active Directory Authentication Library (ADAL) vagy adal.js biztosít. Adal.js kizárólagos célja az, hogy megkönnyítik az alkalmazás hozzáférési. Használatával mutatja be, milyen egyszerűen, itt létrehozunk egy AngularJS teendőlista-alkalmazás, amely:

* Bejelentkezik a felhasználó az alkalmazás által az Azure AD Identitásszolgáltatóként.

* Néhány felhasználó információit jeleníti meg.
* Biztonságosan meghívja az alkalmazást, tegye lista API-t az Azure ad-ből tulajdonosi jogkivonatok segítségével.
* A felhasználó ki az alkalmazásból jelentkezik.

A teljes, működő alkalmazást készíthet, kell tennie:

1. Az alkalmazás regisztrálása az Azure ad-ben.
2. Adal-t telepítse és konfigurálja az egyoldalas alkalmazást.
3. Adal-t használó egyoldalas alkalmazás biztonságos lapok segítségével.

Első lépésként [töltse le az alkalmazás skeleton](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) vagy [töltse le az elkészült mintát](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Emellett ahol felhasználók létrehozása és regisztrálni egy alkalmazást az Azure AD-bérlővel. Ha még nem rendelkezik egy bérlő [megtudhatja, hogyan tehet szert egy](quickstart-create-new-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>1. lépés: A DirectorySearcher alkalmazás regisztrálása
Ahhoz, hogy a felhasználók hitelesítéséhez és a jogkivonatok lekérésére, az alkalmazást, akkor először az Azure AD-bérlő regisztrálásához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha több címtár jelentkezett be, szükség lehet annak érdekében, hogy a megfelelő címtár jelenik meg. Ehhez a felső sávon található, kattintson a fiók. Alatt a **Directory** menüben válassza ki az Azure AD-bérlőt, ahová az alkalmazás regisztrálásához.
3. Kattintson a **minden szolgáltatás** a bal oldali panelen, és válassza ki a **Azure Active Directory**.
4. Kattintson a **alkalmazásregisztrációk**, majd válassza ki **Hozzáadás**.
5. Kövesse az utasításokat, és hozzon létre egy új webalkalmazás és/vagy webes API-hoz:
  * **Név** ismerteti az alkalmazást a felhasználók számára.
  * **Bejelentkezés URL-cím** az a hely, amelyhez Azure ad-ben jogkivonatokat ad vissza. Az alapértelmezett helye ehhez a mintához `https://localhost:44326/`.
6. Regisztráció után az Azure AD az alkalmazáshoz rendeli hozzá egy egyedi Alkalmazásazonosítója. Ez az érték kell a következő szakaszokban, ezért másolja ki az alkalmazások lapon.
7. Adal.js az implicit OAuth-folyamat használatával kommunikálni az Azure ad-ben. Az alkalmazás engedélyeznie kell az implicit folyamatot:
  1. Kattintson az alkalmazást, és válassza ki **Manifest** , nyissa meg a beágyazott alkalmazásjegyzék-szerkesztőben.
  2. Keresse meg a `oauth2AllowImplicitFlow` tulajdonság. Az értékét állítsa `true`.
  3. Kattintson a **mentése** a jegyzékfájl mentése.
8. Engedélyek megadása az alkalmazás a bérlőn belüli. Lépjen a **beállítások** > **szükséges engedélyek**, majd kattintson a **engedélyek megadása** gombot a felső sávon található. Kattintson a **Yes** (Igen) gombra a megerősítéshez.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>2. lépés: Telepítés adal-t és az egyoldalas alkalmazás konfigurálása
Most, hogy egy alkalmazás az Azure ad-ben, adal.js telepítse, és az identitással kapcsolatos kód írása.

### <a name="configure-the-javascript-client"></a>A JavaScript-ügyfél konfigurálása
Első lépésként adja hozzá adal.js a TodoSPA projekthez a Package Manager konzol használatával:
  1. Töltse le [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) , és adja azt hozzá a `App/Scripts/` projekt könyvtárában.
  2. Töltse le [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) , és adja azt hozzá a `App/Scripts/` projekt könyvtárában.
  3. Minden parancsprogram vége előtt betölteni a `</body>` a `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>A háttér-kiszolgáló konfigurálása
Az egyoldalas alkalmazás háttér-, tegye lista API-t fogadni a böngészőből a háttér konfigurációs adatait az alkalmazás regisztrációját kell rendelkeznie. A TodoSPA projektben nyissa meg a `web.config`. Cserélje le az értékeket az elemek a `<appSettings>` szakaszban, hogy az értékeket, amelyeket Ön az Azure Portalon. A kód ezeket az értékeket hivatkozik, amikor az adal-t használ.
  * `ida:Tenant` az a tartomány az Azure AD-bérlő – például contoso.onmicrosoft.com.
  * `ida:Audience` a portálról kimásolt az alkalmazás ügyfél-azonosító van.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>3. lépés: Használja az egyoldalas alkalmazás biztonságos oldalainak segítségével adal-t
Adal.js integrálható az AngularJS útvonal és a HTTP-szolgáltatók, így az egyoldalas alkalmazás biztonságos egyes nézetek segíthet.

1. A `App/Scripts/app.js`, a adal.js modul használata:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Inicializálása `adalProvider` használatával, az alkalmazás regisztrációja a konfigurációs értékeket is `App/Scripts/app.js`:

    ```js
    adalProvider.init(
      {
          instance: 'https://login.microsoftonline.com/',
          tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
          clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
          extraQueryParameter: 'nux=1',
          //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
      },
      $httpProvider
    );
    ```
3. Biztonságossá tétele a `TodoList` megtekintése az alkalmazásban csak egyetlen sor kód használatával: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Összegzés
Most már rendelkezik egy biztonságos egyoldalas alkalmazás, amely a felhasználók és a háttérrendszeri API tulajdonosi jogkivonat-védelemmel ellátott küldött kérések kiadására. Amikor a felhasználó rákattint a **TodoList** hivatkozás, adal.js automatikusan átirányítja az Azure AD-hez bejelentkezési szükség esetén. Emellett adal.js lesz automatikusan csatolja egy hozzáférési jogkivonatot bármely Ajax-kérelmeket az alkalmazás háttérrendszere küldött. 

A fenti lépéseket a csupasz minimális szükséges hozhat létre egy egyoldalas alkalmazást adal.js használatával. Azonban bizonyos funkciók egyoldalas alkalmazás hasznos:

* Az explicit módon a bejelentkezési és kijelentkezési kérések kiállítása, meghatározhatja a tartományvezérlők, amelyek aktiválják az adal.js funkciók. A `App/Scripts/homeCtrl.js`:

    ```js
    ...
    $scope.login = function () {
        adalService.login();
    };
    $scope.logout = function () {
        adalService.logOut();
    };
    ...
    ```
* Érdemes a felhasználói adatok szerepelnek az alkalmazás felhasználói felületén. Az ADAL szolgáltatás már korábban a `userDataCtrl` vezérlő, hogy hozzáférhessen a `userInfo` objektum a társított nézetben `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Nincsenek számos forgatókönyv, amelyben meg szeretné tudni, hogy a felhasználó jelentkezett be, vagy nem. Is használhatja a `userInfo` objektum az információk gyűjtésére. Például a `index.html`, vagy megjelenítheti a **bejelentkezési** vagy **kijelentkezési** gomb a hitelesítési állapot alapján:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Az Azure Active Directoryba integrált egyoldalas alkalmazás is hitelesítheti a felhasználókat, biztonságos hívása a háttérrendszerének OAuth 2.0 használatával, és alapvető információkat szeretne a felhasználó első. Ha még nem tette, most már az az idő, az egyes felhasználók a bérlő feltölti. A teendőlista egyoldalas alkalmazás futtatása, és jelentkezzen be egy ezen felhasználók. Tevékenységek hozzáadása a felhasználók feladatlistáit, jelentkezzen ki, és jelentkezzen be újra.

Adal.js egyszerűen építhet közös identitás-funkciók az alkalmazásba. Ez gondoskodik dirty munka az Ön számára: gyorsítótár kezeléséhez, OAuth protokolltámogatás bemutatása a felhasználó egy bejelentkezési felhasználói felületén, lejárt, és további frissítésekor.

Referenciaként az elkészült mintát (a konfigurációs értékek) nélkül érhető el a [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>További lépések
Most már továbbléphet további forgatókönyvek. Előfordulhat, hogy szeretné próbálni: [CORS webes API-hívás egy egyoldalas alkalmazásból](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
