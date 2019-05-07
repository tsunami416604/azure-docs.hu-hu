---
title: Egyoldalas alkalmazás (bejelentkezés) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazás (bejelentkezés)
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
ms.openlocfilehash: fc9c46ae28960387e6f8efc1ade20afa1c77ef55
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138801"
---
# <a name="single-page-application---sign-in"></a>Egyoldalas alkalmazás – bejelentkezés

Ismerje meg, jelentkezzen be a kódot az egyoldalas alkalmazás hozzáadása.

Ehhez előbb jogkivonatok API-k elérése az alkalmazásban, szüksége lesz egy hitelesített felhasználói környezetet. Bejelentkezhet a felhasználók az alkalmazását az MSAL.js kétféle módon:

* [Jelentkezzen be egy előugró ablak](#sign-in-with-a-pop-up-window) használatával `loginPopup` metódus
* [Jelentkezzen be az átirányítási](#sign-in-with-redirect) használatával `loginRedirect` metódus

Akkor is lehetősége van átadni a API-kat, amelynek szüksége jóváhagyást bejelentkezés alkalmával a felhasználó a hatókörök.

> [!NOTE]
> Ha az alkalmazás már hozzáfér egy hitelesített felhasználó környezetére vagy azonosító jogkivonat, a bejelentkezési lépést kihagyhatja, és közvetlenül a jogkivonatok beszerzéséhez. További részletekért lásd: [msal.js bejelentkezési azonosító nélküli egyszeri bejelentkezési](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Egy előugró ablak vagy átirányítási felület közötti választáshoz

Az előugró és az átirányítási módszerek kombinációját az alkalmazásban nem használhat. Egy előugró ablak vagy átirányítási felület közötti választás attól függ, hogy az alkalmazás folyamata.

* Ha nem szeretné a felhasználó fő alkalmazásoldalon elhagyni a hitelesítés során, ajánlott az előugró módszerekkel. Mivel a hitelesítés-átirányítási történik, az előugró ablakban, a rendszer megőrzi a fő alkalmazás állapota.

* Vannak bizonyos esetekben, ahol lehetséges, hogy az átirányítási módszer használatához. Ha az alkalmazást böngészőben korlátozások vagy házirendek ahol előugró ablakok le vannak tiltva, az átirányítás módszerekkel. Az átirányítási metódusok használata az Internet Explorer böngészőt, mivel bizonyos [ismert problémák az Internet Explorerrel](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) előugró ablakok kezelésekor.

## <a name="sign-in-with-a-pop-up-window"></a>Jelentkezzen be egy előugró ablak

### <a name="javascript"></a>JavaScript

```javascript
const loginRequest = {
    scopes: ["user.read", "user.write"]
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

Az MSAL Angular burkoló lehetővé teszi, hogy biztonságos meghatározott útvonalakat az alkalmazásban csak hozzáadásával a `MsalGuard` az útvonal-definícióhoz. Az alkalmazásőr fogja meghívni a metódust bejelentkezni, ha azt a módot.

```javascript
// In app.routes.ts
{ path: 'product', component: ProductComponent, canActivate : [MsalGuard],
    children: [
      { path: 'detail/:id', component: ProductDetailComponent  }
    ]
   },
  { path: 'myProfile' ,component: MsGraphComponent, canActivate : [MsalGuard] },
```

Egy előugró ablak élmény engedélyezése a `popUp` konfigurációs beállítást. Is átadhat a hatókörök, amelyeket a jóváhagyás szükséges a következőképpen:

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                popUp: true,
                consentScopes: ["user.read", "user.write"]
            })]
         })
```

## <a name="sign-in-with-redirect"></a>Jelentkezzen be az átirányítási

### <a name="javascript"></a>JavaScript

Az átirányítási módszerek ígéret a forrásadatok a fő alkalmazás navigációs miatt nem adott vissza. Dolgozza fel, és hozzáférési a visszakapott jogkivonatokat, szüksége lesz sikerességről és hibáról visszahívások regisztrálja az átirányítási metódusok meghívása előtt.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const loginRequest = {
    scopes: ["user.read", "user.write"]
}

userAgentApplication.loginRedirect(loginRequest);
```

### <a name="angular"></a>Angular

A kód itt megegyezik a jelentkezzen be egy előugró ablak szakasz a fent leírtak szerint. Az alapértelmezett folyamat átirányítási.

> [!NOTE]
> Az azonosító jogkivonat nem tartalmazza a jóváhagyott hatókörök, és csak a hitelesített felhasználó jelöli. A jóváhagyott hatókörök rendszer a hozzáférési jogkivonatot, amely akkor arányban fogják beszerezni a következő lépésben adja vissza.

## <a name="sign-out"></a>Kijelentkezés

Az MSAL kódtára egy `logout` metódus törölni fogja a böngészőben a tárterületen található a gyorsítótárban, és a egy kijelentkezési kérés küldése az Azure AD. Miután kijelentkezett átirányítja a vissza a application start lap alapértelmezés szerint.

Az URI-t, amelyhez kell átirányítania jel után ki úgy konfigurálhatja a `postLogoutRedirectUri`. Ez az URI is, az alkalmazás regisztrálása az Logout URI-t kell regisztrálni.

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
> [Az alkalmazás egy token beszerzése](scenario-spa-acquire-token.md)
