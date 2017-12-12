---
title: "Ismerkedés az Azure AD AngularJS |} Microsoft Docs"
description: "Megtudhatja, hogyan hozható létre egy AngularJS egyoldalas alkalmazás, amely integrálható az Azure ad-val bejelentkezhet, és meghívja az Azure AD-védelemmel ellátott API-OAuth használatával."
services: active-directory
documentationcenter: 
author: jmprieur
manager: mtillman
editor: 
ms.assetid: f2991054-8146-4718-a5f7-59b892230ad7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/07/2017
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5f2bf857d2618a8cfe7094c7f3124be20db3ff39
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="help-secure-angularjs-single-page-apps-by-using-azure-ad"></a>Számítógépek biztonságossá tétele AngularJS egyoldalas alkalmazások az Azure AD segítségével

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

Az Azure Active Directory (Azure AD) teszi egyszerű és magától értetődő, amelyen felvehet bejelentkezési, kijelentkezési és biztonságos OAuth API meghívja az egyoldalas alkalmazásokhoz.  Ez lehetővé teszi az alkalmazások a Windows Server Active Directory-fiókkal rendelkező felhasználók hitelesítéséhez, és a webes API-t, amely az Azure AD védi, például az Office 365 API-k vagy az Azure API-t használ.

Böngészőjében futó JavaScript-alkalmazások esetében az Azure AD az Active Directory Authentication Library (ADAL), vagy adal.js biztosít. A kizárólagos adal.js célja megkönnyíti a hozzáférési jogkivonatok lekérésére, az alkalmazás. Annak bemutatásához, milyen könnyű van, itt azt fogja AngularJS teendőlista alkalmazás létrehozásához, amely:

* A felhasználó alkalmazásba jelentkezik be az Azure AD használatával az identitás-szolgáltatóként.

* A felhasználó bizonyos információkat jelenít meg.
* Biztonságosan hívja az alkalmazás számára tegye lista API-t az Azure AD tulajdonosi jogkivonatok segítségével.
* Az alkalmazásból a felhasználó bejelentkezésekor.

A teljes, működő alkalmazást készítéséhez kell:

1. Regisztrálja az alkalmazást az Azure ad-val.
2. Adal-t telepíti és konfigurálja az alkalmazás.
3. Biztonságos oldalán az alkalmazás adal-t használja.

A kezdéshez [töltse le az alkalmazás vázat](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) vagy [töltse le az elkészült mintát](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Akkor is, amelyben felhasználók létrehozása és egy alkalmazás regisztrálása az Azure AD-bérlő kell. Ha még nem rendelkezik a bérlő [beszerzéséről egy](active-directory-howto-tenant.md).

## <a name="step-1-register-the-directorysearcher-application"></a>1. lépés: A DirectorySearcher alkalmazás regisztrálása
Ahhoz, hogy az alkalmazás hitelesíti a felhasználókat, és a jogkivonatok lekérésére, először regisztrálnia kell azt az Azure AD-bérlőben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Ha be van jelentkezve több könyvtárak, szükség lehet annak érdekében, hogy a megfelelő könyvtárban megtekintésekor. Ehhez a felső eszköztáron kattintson a fiókját. Az a **Directory** menüben válassza ki az Azure AD-bérlőt, ahová az alkalmazás regisztrálásához.
3. Kattintson a **több szolgáltatások** a bal oldali ablaktáblán, és válassza a **Azure Active Directory**.
4. Kattintson a **App regisztrációk**, majd válassza ki **Hozzáadás**.
5. Kövesse az utasításokat, és hozzon létre egy új webalkalmazás és/vagy webes API:
  * **Név** az alkalmazás a felhasználók számára ismerteti.
  * **Átirányítási Uri** az a hely, amelyhez az Azure AD jogkivonatok ad vissza. Az alapértelmezett helye Ez a minta `https://localhost:44326/`.
6. Regisztráció befejezése után az Azure AD egy egyedi alkalmazás Azonosítót rendel az alkalmazáshoz.  Ez az érték kell a következő szakaszokban lévő, másolja az alkalmazás lapján.
7. Adal.js az OAuth implicit engedélyezési folyamat használatával kommunikálni az Azure AD. Az alkalmazás engedélyeznie kell a implicit engedélyezési folyamat:
  1. Kattintson az alkalmazás, és válassza **Manifest** a jegyzék beágyazott-szerkesztő megnyitásához.
  2. Keresse meg a `oauth2AllowImplicitFlow` tulajdonság. Állítsa be az értékét `true`.
  3. Kattintson a **mentése** a jegyzékfájl mentése.
8. Az alkalmazás a bérlő jogosultságot adni. Lépjen **beállítások** > **tulajdonságok** > **szükséges engedélyek**, és kattintson a **engedélyt adjon** a felső eszköztáron gombra. Kattintson a **Yes** (Igen) gombra a megerősítéshez.

## <a name="step-2-install-adal-and-configure-the-single-page-app"></a>2. lépés: Telepítse adal-t és az alkalmazás konfigurálása
Most, hogy az Azure AD-alkalmazás, adal.js telepítse, és az identitás-kapcsolódó kód írása.

### <a name="configure-the-javascript-client"></a>A JavaScript-ügyfél konfigurálása
Kezdje az adal.js hozzáadása a TodoSPA projekt a Csomagkezelő konzol segítségével:
  1. Töltse le [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) és hozzá kell adnia a `App/Scripts/` projekt könyvtárában.
  2. Töltse le [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) és hozzá kell adnia a `App/Scripts/` projekt könyvtárában.
  3. Minden parancsprogram végéig betölteni a `</body>` a `index.html`:

    ```js
    ...
    <script src="App/Scripts/adal.js"></script>
    <script src="App/Scripts/adal-angular.js"></script>
    ...
    ```

### <a name="configure-the-back-end-server"></a>A háttér-kiszolgáló konfigurálása
A egyoldalas alkalmazások háttér-való tegye lista API-t fogadni a böngészőből a háttér kell konfigurációs adatait az alkalmazás regisztrálása. A TodoSPA projektben nyissa meg a `web.config`. Cserélje le az értékeket az elemek a `<appSettings>` szakaszban az Azure portálon használt értékeknek megfelelően. A kód minden alkalommal ADAL hivatkozik ezeket az értékeket.
  * `ida:Tenant`az Azure AD bérlője – például a contoso.onmicrosoft.com tartomány.
  * `ida:Audience`az ügyfél-Azonosítót, az alkalmazás a portálról másolt van.

## <a name="step-3-use-adal-to-help-secure-pages-in-the-single-page-app"></a>3. lépés: Használja az alkalmazás biztonságos lapja segítségével ADAL
Adal.js jól integrálható az AngularJS útvonal és a HTTP-szolgáltatók, az alkalmazás egyoldalas segíthet a biztonságos egyéni nézeteket.

1. A `App/Scripts/app.js`, kapcsolja a adal.js modulban:

    ```js
    angular.module('todoApp', ['ngRoute','AdalAngular'])
    .config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
     function ($routeProvider, $httpProvider, adalProvider) {
    ...
    ```
2. Inicializálni `adalProvider` segítségével a konfigurációs értékeket az alkalmazás-regisztrációk is `App/Scripts/app.js`:

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
3. Számítógépek biztonságossá tétele a `TodoList` nézet használatával egyetlen kódsort az alkalmazásban: `requireADLogin`.

    ```js
    ...
    }).when("/TodoList", {
            controller: "todoListCtrl",
            templateUrl: "/App/Views/TodoList.html",
            requireADLogin: true,
    ...
    ```

## <a name="summary"></a>Összefoglalás
Most már rendelkezik egy biztonságos alkalmazás, amely a felhasználók bejelentkezhetnek és tulajdonosi jogkivonat-védelemmel ellátott kérelmeket kiadni a háttér-API. Amikor a felhasználó rákattint a **TodoList** hivatkozásra a adal.js automatikusan átirányítja az Azure AD-hez bejelentkezési szükség esetén. Ezenkívül adal.js automatikusan kapcsolódni fog olyan hozzáférési jogkivonatot, bármely Ajax-kérelmek az alkalmazás háttér küldött.  

Az előző lépéseket kell a operációs rendszer minimálisan szükséges adal.js használatával egy egyoldalas alkalmazás elkészítésére. Azonban néhány egyéb szolgáltatásokat az alkalmazás hasznos információkat:

* Be- és kijelentkezési kérések explicit módon ki, a tartományvezérlőn, amely a adal.js meghívása funkciók is meghatározhatja.  A `App/Scripts/homeCtrl.js`:

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
* Felhasználói adatokat az alkalmazás Kezelőfelületén megjeleníteni kívánt. Az ADAL szolgáltatás már szerepel az a `userDataCtrl` vezérlő, hogy hozzáférhessen a `userInfo` a társított nézetben objektum `App/Views/UserData.html`:

    ```js
    <p>{{userInfo.userName}}</p>
    <p>aud:{{userInfo.profile.aud}}</p>
    <p>iss:{{userInfo.profile.iss}}</p>
    ...
    ```

* Nincsenek számos forgatókönyv, ahol érdemes tudni, hogy a felhasználó van bejelentkezve. Használhatja a `userInfo` ehhez objektum.  Például a `index.html`, vagy megjelenítheti a **bejelentkezési** vagy **kijelentkezési** gomb a hitelesítési állapot alapján:

    ```js
    <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
    <li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    ```

Az Azure Active Directoryba integrált alkalmazás is hitelesíti a felhasználókat, biztonságosan OAuth 2.0 használatával hívható meg a háttér és a felhasználó alapszintű adatainak beolvasása. Ha még nem tette meg, most már az egyes felhasználóival a bérlő feltölti idő. Futtassa a teendőlista alkalmazás, és jelentkezzen be valamelyik azoknak a felhasználóknak. Feladatok hozzáadása a felhasználói feladatlistában, jelentkezzen ki, és jelentkezzen be.

Adal.js megkönnyíti, hogy az általános identitás-szolgáltatások átfogó az alkalmazásba. Ez gondoskodik a dirty munkát meg: gyorsítótár kezelése, az OAuth-protokoll támogatása, a felhasználó egy bejelentkezési felhasználói felület, lejárt jogkivonatokat, és több frissítése a bemutató.

Referenciaként az elkészült mintát (a konfigurációs értékek nélkül) érhető el a [GitHub](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip).

## <a name="next-steps"></a>Következő lépések
Most már továbbléphet további helyzeteket is. Akkor célszerű: [CORS webes API meghívása egy egyoldalas alkalmazásból](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet).

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
