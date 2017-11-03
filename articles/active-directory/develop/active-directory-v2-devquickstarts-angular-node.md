---
title: "Az Azure AD v2.0 NodeJS AngularJS egylapos alkalmazások első lépések |} Microsoft Docs"
description: "Hogyan hozhat létre, hogy mindkét személyes Microsoft-fiókkal rendelkező felhasználók bejelentkezésekor szögben kifejezett JS egylapos alkalmazások és a munkahelyi vagy iskolai fiókját."
services: active-directory
documentationcenter: 
author: navyasric
manager: mbaldwin
editor: 
ms.assetid: d286aa33-8a94-452f-beb7-ddc6c6daa5c8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: javascript
ms.topic: article
ms.date: 01/23/2017
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 0e90171afd9c4c782fbb18375ab2d147497ef442
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="add-sign-in-to-an-angularjs-single-page-app---nodejs"></a>Bejelentkezés hozzáadása egy AngularJS egylapos alkalmazás - NodeJS
Ebben a cikkben fel kell venni jelentkezzen be az alkalmazás bekapcsolja Microsoft-fiókok AngularJS alkalmazásokhoz az Azure Active Directory v2.0-végponttól használatával. a v2.0-végpontra lehetővé teszik egy egyetlen integrációt elvégzéséhez az alkalmazáson belüli és a személyes és munkahelyi vagy iskolai fiókkal rendelkező felhasználók hitelesítéséhez.

Ez a minta egy egyszerű Tennivalólista egylapos alkalmazást, amely tárolja a feladatokat az olyan háttér REST API-t NodeJS nyelven írt, és az Azure AD OAuth tulajdonosi jogkivonatok használatával biztonságossá.  Az AngularJS alkalmazás fog használni a nyílt forráskódú JavaScript hitelesítési kódtár [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js) a teljes bejelentkezési folyamathoz, és a REST API hívásának jogkivonatainak szerezni.  Ugyanilyen mintájú alkalmazható felé történő hitelesítésre más REST API-k, például a [Microsoft Graph](https://graph.microsoft.com) vagy az Azure Resource Manager API-kat.

> [!NOTE]
> Nem minden Azure Active Directory forgatókönyvek és funkciók támogatják a v2.0-végponttól.  Annak meghatározásához, ha a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
> 
> 

## <a name="download"></a>Letöltés
A kezdéshez kell letöltése és telepítése [node.js](https://nodejs.org).  Majd átmásolhatja vagy [letöltése](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/skeleton.zip) egy üres alkalmazást:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS.git
```

Az üres alkalmazás egyszerű AngularJS alkalmazások bolierplate kódot tartalmaz, de hiányzik az identitás-kapcsolódó darab mindegyikét.  Ha nem szeretné követéséhez, hanem klónozhat vagy [letöltése](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/complete.zip) az elkészült mintát.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-NodeJS.git
```

## <a name="register-an-app"></a>Alkalmazás regisztrálása
Először hozzon létre egy alkalmazást, az a [App regisztrációs portál](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList), vagy kövesse az alábbi [részletes lépéseket](active-directory-v2-app-registration.md).  Győződjön meg arról, hogy:

* Adja hozzá a **webes** platform az alkalmazásra vonatkozóan.
* Adja meg a megfelelő **átirányítási URI-**. Ez a minta alapértelmezés szerint `http://localhost:8080`.
* Hagyja a **Implicit Flow engedélyezése** engedélyezve jelölőnégyzetet. 

Másolja le a **Alkalmazásazonosító** , amely hozzá van rendelve az alkalmazáshoz, hamarosan kell azt. 

## <a name="install-adaljs"></a>Adal.js telepítése
Indítsa el, navigáljon a letöltött projektre, és telepítse a adal.js.  Ha rendelkezik [bower](http://bower.io/) telepítve, ugyanúgy futtathatja ezt a parancsot.  Semmilyen függőségi verzió eltérést csak adja meg az újabb verziót.

```
bower install adal-angular#experimental
```

Azt is megteheti, manuálisan letöltheti [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) és [adal angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js).  Mindkét fájlok hozzáadása a `app/lib/adal-angular-experimental/dist` könyvtár.

Most a kedvenc szövegszerkesztőben nyissa meg a projektet, és a lap törzsében végén adal.js betöltése:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## <a name="set-up-the-rest-api"></a>A REST API beállítása
Során beállítás folyamatban van, lehetővé teszi, hogy a háttér REST API működéséhez.  Egy parancssori ablakot, telepítse a szükséges csomagokat futtatásával (gondoskodjon arról, hogy a projekt legfelső szintű könyvtárában található):

```
npm install
```

Most nyissa meg a `config.js` , és cserélje le a `audience` érték:

```js
exports.creds = {

     // TODO: Replace this value with the Application ID from the registration portal
     audience: '<Your-application-id>',

     ...
}
```

A REST API-t használja ezt az értéket érvényesítse a szögben kifejezett alkalmazást, az AJAX-kérelmek megkapja.  Vegye figyelembe, hogy az egyszerű REST API tárolja az adatokat a memóriában - így minden alkalommal, amikor a kiszolgáló leállítására, elveszíti az összes korábban létrehozott feladatok.

Ez egy, a REST API működése megvitatása fogjuk folyamatosan.  Nyugodtan poke, a kódban, de ha szeretné megtudni a további információk védelme webes API-kat az Azure ad-vel, tekintse meg [Ez a cikk](active-directory-v2-devquickstarts-node-api.md). 

## <a name="sign-users-in"></a>A felhasználók beléptetése
Identitás kódírást idő.  Észrevette, hogy már adott adal.js egy AngularJS szolgáltatót tartalmaz, amely lehetőségben szépen szögben kifejezett útválasztási mechanizmusokat.  Indítsa el az alkalmazás az adal modul hozzáadásával:

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

Most töltse a `adalProvider` az alkalmazás azonosítójú:

```js
// app/scripts/app.js

...

adalProvider.init({

        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 

        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',

        // Your application id from the registration portal
        clientId: '<Your-application-id>',

        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',

    }, $httpProvider);
```

Nagyszerű, adal.js már az alkalmazás biztonságos és felhasználói bejelentkezéshez szükséges összes adatot.  Jelentkezzen be az alkalmazás az adott útvonal kényszerítéséhez tart egy kódsort:

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

Most amikor a felhasználó rákattint a `TodoList` hivatkozásra a adal.js automatikusan átirányítja az Azure AD-hez bejelentkezési szükség esetén.  Be- és kijelentkezési kérések, a vezérlők adal.js hívja közvetlenül is küldhet:

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {

        // Redirect the user to sign in
        adalService.login();

    };
    $scope.logout = function () {

        // Redirect the user to log out    
        adalService.logOut();

    };
...
```

## <a name="display-user-info"></a>Felhasználói adatok megjelenítése
Most, hogy a felhasználó jelentkezett be, valószínűleg szüksége a bejelentkezett felhasználók hitelesítési adatait az alkalmazás eléréséhez.  Adal.js elérhetővé teszi ezeket az adatokat a a `userInfo` objektum.  Az objektumot a nézet megnyitásához először adja hozzá adal.js a legfelső szintű vezérlő hatókörébe, a megfelelő:

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

Ezt követően közvetlenül meg lehet oldani a `userInfo` objektumot a nézet: 

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

Használhatja a `userInfo` objektumot határozza meg, ha a felhasználó bejelentkezett-e.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## <a name="call-the-rest-api"></a>A REST API hívása
Végül néhány jogkivonatok lekérésére, és az REST API létrehozása, olvasása, frissítése és törölheti a feladatokat.  Jól kitalálni a Mi?  Ehhez nincs *egy dolog*.  Adal.js automatikusan kezeli, gyorsítótárazás, és a jogkivonatok frissítése.  Azt is kezeli a jogkivonatok csatolása a REST API-t küldünk kimenő AJAX-kérelmek.  

Pontosan hogyan működik ez? Az összes köszönhetően a Bűvös van [AngularJS elfogókat](https://docs.angularjs.org/api/ng/service/$http), amely lehetővé teszi, hogy a kimenő és bejövő HTTP-üzenetek átalakítására adal.js.  Ezenkívül adal.js feltételezi, hogy minden kérést küldeni ugyanabban a tartományban, az ablak jogkivonatok szánt alkalmazás azonosítója megegyezik az AngularJS alkalmazást kell használnia.  Ezért a mindkét szögben kifejezett alkalmazás és a NodeJS REST API azonos Alkalmazásazonosító használtuk.  Természetesen bírálja felül ezt a viselkedést, és kérje meg a jogkivonatok lekérésére más REST API-kat, ha szükséges – adal.js, de a egyszerű forgatókönyv az alapértelmezett beállításokat fog tenni.

Íme egy kódrészletet, amely azt mutatja, milyen egyszerűen azt tulajdonosi jogkivonatok kérések küldése az Azure AD:

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Gratulálunk!  Az Azure AD integrált egylapos alkalmazás most már befejeződött.  Lépjen tovább, meghajolni igénybe vehet.  Képes hitelesíti a felhasználókat, biztonságosan hívható meg a háttérrendszer REST API használatával OpenID Connect, és a felhasználó alapszintű adatainak beolvasása.  Alapesetben minden személyes Microsoft-Account vagy az Azure AD egy munkahelyi vagy iskolai fiókkal rendelkező felhasználó támogatja.  Az alkalmazás adjon meg egy try futtatásával:

```
node server.js
```

A böngészőben navigáljon `http://localhost:8080`.  Jelentkezzen be személyes Microsoft-fiókkal vagy a munkahelyi vagy iskolai fiókkal.  Feladatok hozzáadása a felhasználói feladatlistában, és jelentkezzen ki.  Próbáljon meg, a más típusú fiókot bejelentkezni. Ha az Azure AD-bérlő létrehozása a munkahelyi vagy iskolai felhasználók kell [beszerzéséről egy itt](active-directory-howto-tenant.md) (szabad).

És folytathatja az a a v2.0-végpontra, központi vissza a [v2.0 – útmutató fejlesztőknek](active-directory-appmodel-v2-overview.md).  További forrásokért tekintse meg:

* [Azure-minták a Githubon >>](https://github.com/Azure-Samples)
* [Az Azure AD a veremtúlcsordulás >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
* Az Azure AD-dokumentációja [Azure.com webhelyre >>](https://azure.microsoft.com/documentation/services/active-directory/)

## <a name="get-security-updates-for-our-products"></a>Biztonsági frissítések termékeinkhez
Javasoljuk, hogy kérjen értesítést a bekövetkező biztonsági incidensekről. Látogasson el [erre a lapra](https://technet.microsoft.com/security/dd252948), és fizessen elő a biztonsági tanácsadói riasztásokra.

