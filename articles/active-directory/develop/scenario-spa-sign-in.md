---
title: Egyoldalas alkalmazás bejelentkezési & kijelentkezés – Microsoft Identity platform | Azure
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
ms.openlocfilehash: b3d7afdc634649ca3baf5f43d5e2376fb5d8b05d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423642"
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

A MSAL szögletes burkolója lehetővé teszi, hogy az alkalmazás adott útvonalait az útvonal-definícióhoz `MsalGuard` hozzáadásával biztosítsa. Ez az őr meghívja a metódust, hogy bejelentkezzen az útvonal elérésekor.

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

## <a name="sign-in-with-redirect"></a>Bejelentkezés átirányítással

### <a name="javascript"></a>JavaScript

Az átirányítási módszerek nem adnak vissza ígéretet a fő alkalmazásból való elmozdulás miatt. A visszaadott tokenek feldolgozásához és eléréséhez regisztrálnia kell a sikeres és a sikertelen visszahívásokat az átirányítási módszerek meghívása előtt.

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

A kód itt ugyanaz, mint az előugró ablakba való bejelentkezéssel kapcsolatos szakasz korábbi részében leírtak szerint. Az alapértelmezett folyamat átirányítása.

> [!NOTE]
> Az azonosító jogkivonat nem tartalmazza a beleegyezett hatóköröket, és csak a hitelesített felhasználót jelöli. A rendszer visszaadja a megadott hatóköröket a hozzáférési jogkivonatban, amelyet a következő lépésben fog beszerezni.

## <a name="sign-out"></a>Kijelentkezés

A MSAL könyvtár olyan `logout` metódust biztosít, amely törli a gyorsítótárat a böngészőalapú tárolóban, és kijelentkezési kérést küld Azure Active Directory (Azure AD) számára. A kijelentkezés után a könyvtár alapértelmezés szerint átirányítja az alkalmazás kezdőlapját.

Beállíthatja azt az URI-t, amelyre a kijelentkezést követően át kell irányítani a `postLogoutRedirectUri`beállításával. Ezt az URI-t a kijelentkezési URI-ként is regisztrálni kell az alkalmazás regisztrálásakor.

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Token beszerzése az alkalmazáshoz](scenario-spa-acquire-token.md)
