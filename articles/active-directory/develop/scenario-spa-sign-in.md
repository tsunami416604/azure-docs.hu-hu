---
title: Egyoldalas alkalmazás bejelentkezési & kijelentkezés – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazást (bejelentkezés)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 53a84bd970d564411ec9a56b54159e5a96717a6e
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84558762"
---
# <a name="single-page-application-sign-in-and-sign-out"></a>Egyoldalas alkalmazás: bejelentkezés és kijelentkezés

Megtudhatja, hogyan adhat hozzá bejelentkezést az egyoldalas alkalmazás kódjához.

Ahhoz, hogy a tokenek hozzáférjenek az alkalmazás API-khoz, hitelesített felhasználói környezetre van szükség. A felhasználókat a MSAL. js fájlban kétféleképpen lehet bejelentkezni:

* [Előugró ablak](#sign-in-with-a-pop-up-window)a `loginPopup` metódus használatával
* [Átirányítás](#sign-in-with-redirect)a `loginRedirect` metódus használatával

Igény szerint átadhatja azon API-k hatóköreit, amelyekhez a felhasználónak a bejelentkezéskor hozzá kell járulnia.

> [!NOTE]
> Ha az alkalmazásnak már van hozzáférése egy hitelesített felhasználói környezethez vagy azonosító jogkivonathoz, kihagyhatja a bejelentkezési lépést, és közvetlenül is beszerezheti a jogkivonatokat. Részletekért lásd: [SSO MSAL. js bejelentkezés nélkül](msal-js-sso.md#sso-without-msaljs-login).

## <a name="choosing-between-a-pop-up-or-redirect-experience"></a>Egy előugró vagy átirányítási élmény közötti választás

Az alkalmazásban nem használhatók az előugró és az átirányítási módszerek. Az előugró vagy átirányítási élmény közötti választás az alkalmazási folyamattól függ:

* Ha nem szeretné, hogy a felhasználók a hitelesítés során elmozdulnak a fő alkalmazási lapról, javasoljuk, hogy az előugró metódust. Mivel a hitelesítés átirányítása egy előugró ablakban történik, a fő alkalmazás állapota megmarad.

* Ha a felhasználók olyan böngészőbeli korlátozásokkal vagy házirendekkel rendelkeznek, amelyekben az előugró ablakok le vannak tiltva, használhatja az átirányítási módszert. Használja az átirányítás módszert az Internet Explorer böngészővel, mert az [Internet Explorerben ismert problémák léptek fel az előugró ablakokban](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

## <a name="sign-in-with-a-pop-up-window"></a>Bejelentkezés előugró ablakkal

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new userAgentApplication(config);

myMsal.loginPopup(loginRequest)
    .then(function (loginResponse) {
        //login success
        let idToken = loginResponse.idToken;
    }).catch(function (error) {
        //login failure
        console.log(error);
    });
```

# <a name="angular"></a>[Angular](#tab/angular)

A MSAL szögletes burkolója lehetővé teszi, hogy az alkalmazásban meghatározott útvonalakat biztosítson `MsalGuard` az útvonal-definícióhoz való hozzáadással. Ez az őr meghívja a metódust, hogy bejelentkezzen az útvonal elérésekor.

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

Az előugró ablak felhasználói felületén engedélyezze a `popUp` konfigurációs beállítást. A beleegyező hatóköröket a következőképpen adhatja át:

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
            consentScopes: ["User.ReadWrite"]
        })
    ]
})
```
---

## <a name="sign-in-with-redirect"></a>Bejelentkezés átirányítással

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Az átirányítási módszerek nem adnak vissza ígéretet a fő alkalmazásból való elmozdulás miatt. A visszaadott tokenek feldolgozásához és eléréséhez regisztrálnia kell a sikeres és a sikertelen visszahívásokat az átirányítási módszerek meghívása előtt.

```javascript

const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const loginRequest = {
    scopes: ["User.ReadWrite"]
}

const myMsal = new userAgentApplication(config);

function authCallback(error, response) {
    //handle redirect response
}

myMsal.handleRedirectCallback(authCallback);

myMsal.loginRedirect(loginRequest);
```

# <a name="angular"></a>[Angular](#tab/angular)

A kód itt ugyanaz, mint az előugró ablakba való bejelentkezéssel kapcsolatos szakasz korábbi részében leírtak szerint. Az alapértelmezett folyamat átirányítása.

> [!NOTE]
> Az azonosító jogkivonat nem tartalmazza a beleegyezett hatóköröket, és csak a hitelesített felhasználót jelöli. A rendszer visszaadja a megadott hatóköröket a hozzáférési jogkivonatban, amelyet a következő lépésben fog beszerezni.

---

## <a name="sign-out"></a>Kijelentkezés

A MSAL könyvtár olyan `logout` metódust biztosít, amely törli a gyorsítótárat a böngészőbeli tárolóban, és kijelentkezési kérést küld Azure Active Directory (Azure ad) számára. A kijelentkezés után a könyvtár alapértelmezés szerint átirányítja az alkalmazás kezdőlapját.

Beállíthatja azt az URI-t, amelyre a kijelentkezést követően át kell irányítani a beállítást `postLogoutRedirectUri` . Ezt az URI-t a kijelentkezési URI-ként is regisztrálni kell az alkalmazás regisztrálásakor.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri", //defaults to application start page
        postLogoutRedirectUri: "your_app_logout_redirect_uri"
    }
}

const myMsal = new UserAgentApplication(config);

myMsal.logout();
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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Alkalmazás-jogkivonat beszerzése](scenario-spa-acquire-token.md)
