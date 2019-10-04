---
title: Egyoldalas alkalmazás (bejelentkezés) – Microsoft Identity platform
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazást (bejelentkezés)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7bf614a5523e78fc72918db973ef8d738a171fff
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69031780"
---
# <a name="single-page-application---sign-in"></a>Egyoldalas alkalmazás – bejelentkezés

Megtudhatja, hogyan adhat hozzá bejelentkezést az egyoldalas alkalmazás kódjához.

Ahhoz, hogy a tokenek hozzáférjenek az API-khoz az alkalmazásban, szüksége lesz egy hitelesített felhasználói környezetre. A felhasználókat a MSAL. js fájlban kétféleképpen lehet bejelentkezni:

* [Bejelentkezés előugró ablakban](#sign-in-with-a-pop-up-window) a metódus használatával `loginPopup`
* [Bejelentkezés az átirányítás](#sign-in-with-redirect) használatával `loginRedirect` metódussal

Igény szerint átadhatja azon API-k hatóköreit, amelyekhez a felhasználó beleegyezik a bejelentkezés időpontjában.

> [!NOTE]
> Ha az alkalmazásnak már van hozzáférése egy hitelesített felhasználói környezethez vagy azonosító jogkivonathoz, kihagyhatja a bejelentkezési lépést, és közvetlenül is beszerezheti a jogkivonatokat. További részletekért lásd: [SSO msal. js bejelentkezés nélkül](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Egy előugró vagy átirányítási élmény közötti választás

Az előugró és az átirányítási módszerek kombinációja nem használható az alkalmazásban. Az előugró vagy átirányítási élmény közötti választás az alkalmazási folyamattól függ.

* Ha nem szeretné, hogy a felhasználó az alkalmazás főoldaláról navigáljon a hitelesítés során, javasoljuk, hogy használja az előugró metódusokat. Mivel a hitelesítés átirányítása egy előugró ablakban történik, a fő alkalmazás állapota megmarad.

* Bizonyos esetekben előfordulhat, hogy az átirányítás módszereit kell használnia. Ha az alkalmazás felhasználói olyan böngészőbeli korlátozásokkal vagy házirendekkel rendelkeznek, amelyekben az előugró ablakok le vannak tiltva, akkor használhatja az átirányítási módszereket. Használja az átirányítás módszereit az Internet Explorer böngészőben, mivel az előugró ablakok kezelése során bizonyos [ismert problémák merültek fel az Internet Explorerben](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) .

## <a name="sign-in-with-a-pop-up-window"></a>Bejelentkezés előugró ablakkal

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginPopup(loginRequest).then(function (loginResponse) {
    //login success
    let idToken = loginResponse.idToken;
}).catch(function (error) {
    //login failure
    console.log(error);
});
```

### <a name="angular"></a>Angular

A MSAL szögletes burkolója lehetővé teszi, hogy az alkalmazásban meghatározott útvonalakat az útvonal definíciójának hozzáadásával `MsalGuard` biztosítsa. Ez az őr meghívja a metódust, hogy bejelentkezzen az útvonal elérésekor.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Az előugró ablak felhasználói felületén engedélyezze a `popUp` konfigurációs beállítást. A beleegyező hatóköröket a következőképpen adhatja át:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Bejelentkezés átirányítás

### <a name="javascript"></a>JavaScript

Az átirányítási módszerek nem adnak vissza ígéretet a fő alkalmazástól érkező Navigálás miatt. A visszaadott tokenek feldolgozásához és eléréséhez regisztrálnia kell a sikeres és a sikertelen visszahívásokat az átirányítási módszerek meghívása előtt.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["https://graph.microsoft.com/User.ReadWrite"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

A kód itt megegyeznek a bejelentkezés előugró ablakban című szakaszban leírtak szerint. Az alapértelmezett folyamat átirányítása.

> [!NOTE]
> Az azonosító jogkivonat nem tartalmazza a beleegyezett hatóköröket, és csak a hitelesített felhasználót jelöli. A rendszer a következő lépésben megszerzett hozzáférési jogkivonatban adja vissza a megegyezett hatóköröket.

## <a name="sign-out"></a>Kijelentkezés

A MSAL könyvtár olyan `logout` metódust biztosít, amely törli a gyorsítótárat a böngésző tárolójában, és kijelentkezési kérést küld az Azure ad-nek. A kijelentkezés után a rendszer alapértelmezés szerint átirányítja az alkalmazás kezdőlapját.

Beállíthatja azt az URI-t, amelyre a `postLogoutRedirectUri`kijelentkezés után át kell irányítani. Ezt az URI-t a kijelentkezési URI-ként is regisztrálni kell az alkalmazás regisztrálásakor.

### <a name="javascript"></a>JavaScript

```javascript
const config = {

    auth: {
        clientID: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

### <a name="angular"></a>Angular

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            })]
         })

// In app.component.ts
this.authService.logout();
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Token beszerzése az alkalmazáshoz](scenario-spa-acquire-token.md)
