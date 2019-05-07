---
title: Egyoldalas alkalmazás (API-k hívásához jogkivonat beszerzése) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazás (egy token. beolvasási hívni az API-k)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4c842db8a0874d3619e0dc59b90aa12226cb984
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138812"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Egyoldalas alkalmazás – az API hívása egy jogkivonat beszerzése

A minta-jogkivonatok beszerzésének az API-k az MSAL.js először megpróbálhat egy csendes jogkivonat kérése a `acquireTokenSilent` metódust. Ha ezt a metódust meghívják, a tár először ellenőrzi a gyorsítótárban, hogy érvényes token létezik, és visszaadja a böngésző storage-ban. Nem érvényes jogkivonat nem található a gyorsítótárban, azt csendes token kérést küld az Azure Active Directory (Azure AD) származó egy rejtett iframe. Ez a módszer lehetővé teszi a jogkivonatok újítsa meg a könyvtár is. Egyszeri bejelentkezés munkamenetet, és az Azure ad-ben a jogkivonat élettartama értékek kapcsolatos további információkért lásd: [jogkivonat élettartama](active-directory-configurable-token-lifetimes.md).

Az Azure AD a beavatkozás nélküli jogkivonat-kérelmeket bizonyos okokból lejárt Azure AD-munkamenet vagy a jelszó módosítása sikertelen lehet. Ebben az esetben is hívhat meg az interaktív módszerek (amely felszólítja a felhasználót) jogkivonatok beszerzésére.

* [Egy előugró ablak token beszerzéséhez](#acquire-token-with-a-pop-up-window) használatával `acquireTokenPopup`
* [Az átirányítási token beszerzéséhez](#acquire-token-with-redirect) használatával `acquireTokenRedirect`

**Egy előugró ablak vagy átirányítási felület közötti választáshoz**

 Az előugró és az átirányítási módszerek kombinációját az alkalmazásban nem használhat. Egy előugró ablak vagy átirányítási felület közötti választás attól függ, hogy az alkalmazás folyamata.

* Ha nem szeretné a felhasználó fő alkalmazásoldalon elhagyni a hitelesítés során, ajánlott az előugró módszerekkel. Mivel a hitelesítés-átirányítási történik, az előugró ablakban, a rendszer megőrzi a fő alkalmazás állapota.

* Vannak bizonyos esetekben, ahol lehetséges, hogy az átirányítási módszer használatához. Ha az alkalmazást böngészőben korlátozások vagy házirendek ahol előugró ablakok le vannak tiltva, az átirányítás módszerekkel. Azt is javasoljuk, hogy az átirányítási metódusok használata az Internet Explorer böngészőt, mivel bizonyos [ismert problémák az Internet Explorerrel](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) előugró ablakok kezelésekor.

Beállíthatja a hozzáférési jogkivonat létrehozását, a hozzáférési jogkivonat kérése felvenni kívánt API-hatóköröket. Vegye figyelembe, hogy minden kért hatókör nem adható a hozzáférési jogkivonatot, és függ, hogy a felhasználó beleegyezésével.

## <a name="acquire-token-with-a-pop-up-window"></a>Egy előugró ablak jogkivonat beszerzése

### <a name="javascript"></a>JavaScript

A fenti minta a módszerekkel előugró élményt:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenPopup(accessTokenRequest).then(function(accessTokenResponse) {
            // Acquire token interactive success
        }).catch(function(error) {
            // Acquire token interactive failure
            console.log(error);
        });
    }
    console.log(error);
});
```

### <a name="angular"></a>Angular

Az MSAL Angular burkolót biztosít a kényelem érdekében a HTTP-elfogó hozzáadásának `MsalInterceptor` amely automatikusan csendes szerzi be a hozzáférési jogkivonatokat, és csatolja őket az API-k HTTP-kérelmekre.

Az API-k a hatókörök is megadhat a `protectedResourceMap` konfigurációs beállítás, amely a MsalInterceptor kérni fogja, ha automatikusan-jogkivonatok beszerzésének.

```javascript
//In app.module.ts
@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id',
                protectedResourceMap: {"https://graph.microsoft.com/v1.0/me", ["user.read", "mail.send"]}
            })]
         })

providers: [ ProductService, {
        provide: HTTP_INTERCEPTORS,
        useClass: MsalInterceptor,
        multi: true
    }
   ],
```

A sikeres és sikertelen, a beavatkozás nélküli token beszerzéséhez MSAL Angular itt iratkozhat fel visszahívásokat. Fontos továbbá, hogy az előfizetés lemondása.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if(this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Másik megoldásként közvetlenül is lekérheti a jogkivonatokat a beolvasási jogkivonat módszerek az MSAL.js Alapkönyvtár leírtak szerint.

## <a name="acquire-token-with-redirect"></a>Az átirányítási jogkivonat beszerzése

### <a name="javascript"></a>JavaScript

Az egyik a fent leírtak szerint azonban látható egy átirányítási módszerrel interaktív módon a token beszerzéséhez. Vegye figyelembe, hogy kell regisztrálni az átirányítási visszahívási, ahogy korábban említettük.

```javascript
function authCallback(error, response) {
    //handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, send an interactive request.
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

### <a name="angular"></a>Angular

Ez megegyezik a fent leírt módon.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A webes API meghívása](scenario-spa-call-api.md)
