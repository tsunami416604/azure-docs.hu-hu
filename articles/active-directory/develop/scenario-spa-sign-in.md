---
title: Egyoldalas alkalmazásbejelentkezés & kijelentkezés – Microsoft identity platform | Azure
description: Az egyoldalas alkalmazások létrehozásáról szóló tudnivalók
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
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8dd4d1aa2423ddb48f61380a982ca256609734d6
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419643"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Egyoldalas alkalmazás: Bejelentkezés és kijelentkezés

További információ arról, hogyan adhat hozzá bejelentkezést az egyoldalas alkalmazás kódjához.

Ahhoz, hogy jogkivonatokat kapjon az alkalmazás API-jaihoz való hozzáféréshez, szüksége van egy hitelesített felhasználói környezetre. Az MSAL.js-ben kétféleképpen jelentkezhet be az alkalmazásba:

* [Előugró ablak](#sign-in-with-a-pop-up-window), a `loginPopup` módszer rel
* [Átirányítás](#sign-in-with-redirect), a `loginRedirect` módszer rel

Opcionálisan is átadhatja az API-k hatóköreit, amelyekhez a felhasználónak a bejelentkezéskor beleegyezésére van szüksége.

> [!NOTE]
> Ha az alkalmazás már rendelkezik hozzáféréssel egy hitelesített felhasználói környezethez vagy azonosító jogkivonathoz, kihagyhatja a bejelentkezési lépést, és közvetlenül beszerezheti a jogkivonatokat. További részletek: [SSO without MSAL.js login](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Váltás előugró vagy átirányítási élmény között

Nem használhatja mind az előugró és átirányítási módszereket az alkalmazásban. Az előugró vagy átirányítási élmény közötti választás az alkalmazásfolyamattól függ:

* Ha nem szeretné, hogy a felhasználók a hitelesítés során eltávolodjanak a fő alkalmazáslaptól, javasoljuk az előugró módszert. Mivel a hitelesítési átirányítás előugró ablakban történik, a fő alkalmazás állapota megmarad.

* Ha a felhasználók böngészőkorlátozásokkal vagy olyan házirendekkel rendelkeznek, amelyeknél az előugró ablakok le vannak tiltva, használhatja az átirányítási módszert. Használja az átirányítási módszert az Internet Explorer böngészővel, mert ismert problémák vannak [az Internet Explorer előugró ablakaival.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)

## <a name="sign-in-with-a-pop-up-window"></a>Bejelentkezés előugró ablakkal

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="angular"></a>[Angular](#tab/angular)

Az MSAL-szögburkoló lehetővé teszi, hogy az `MsalGuard` útvonaldefiníció hozzáadásával biztosítsa az alkalmazás bizonyos útvonalait. Ez a védő meghívja a bejelentkezési módszert, amikor az útvonal elérésekor elérhető.

```javascript
// In app-routing.module.ts
import { NgModule } from '@angular/core';
import { Routes, RouterModule } from '@angular/router';
import { ProfileComponent } from './profile/profile.component';
import { MsalGuard } from '@azure/msal-angular';
import { HomeComponent } from './home/home.component';

const routes: Routes = [
  {
    path: 'profile',
    component: ProfileComponent,
    canActivate: [
      MsalGuard
    ]
  },
  {
    path: '',
    component: HomeComponent
  }
];

@NgModule({
  imports: [RouterModule.forRoot(routes, { useHash: false })],
  exports: [RouterModule]
})
export class AppRoutingModule { }
```

Az előugró ablak élményéhez `popUp` engedélyezze a konfigurációs beállítást. A beleegyezést igénylő hatóköröket a következőképpen is átadhatja:

```javascript
// In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
            }
        }, {
            popUp: true,
            consentScopes: ["https://graph.microsoft.com/User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>Bejelentkezés átirányítással

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az átirányítási módszerek nem adnak vissza ígéretet a fő alkalmazástól való elmozdulás miatt. A visszaadott jogkivonatok feldolgozásához és eléréséhez sikeres és hibavisszahívásokat kell regisztrálnia, mielőtt meghívja az átirányítási metódusokat.

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

# <a name="angular"></a>[Angular](#tab/angular)

A kód itt ugyanaz, mint korábban leírt a szakaszban a bejelentkezés egy előugró ablak. Az alapértelmezett folyamat átirányítás.

> [!NOTE]
> Az azonosító jogkivonat nem tartalmazza a megadott hatóköröket, és csak a hitelesített felhasználót jelöli. A jóváhagyott hatókörök a hozzáférési jogkivonatban kerülnek vissza, amelyet a következő lépésben fog beszerezni.

---

## <a name="sign-out"></a>Kijelentkezés

Az MSAL-kódtár olyan `logout` módszert biztosít, amely törli a gyorsítótárat a böngésző tárolójában, és kijelentkezési kérelmet küld az Azure Active Directorynak (Azure AD). A kijelentkezés után a tár alapértelmezés szerint visszairányítja az alkalmazás kezdőlapjára.

A kijelentkezés után átirányítandó URI-t a `postLogoutRedirectUri`beállítással konfigurálhatja. Ezt az URI-t is regisztrálni kell a kijelentkezési URI-ként az alkalmazás regisztrációjában.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const userAgentApplication = new UserAgentApplication(config);
userAgentApplication.logout();

```

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id',
                postLogoutRedirectUri: "your_app_logout_redirect_uri"
            }
        })
    ]
})

// In app.component.ts
this.authService.logout();
```

---

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Alkalmazás-jogkivonat beszerzése](scenario-spa-acquire-token.md)
