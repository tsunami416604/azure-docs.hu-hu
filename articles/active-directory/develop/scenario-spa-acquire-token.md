---
title: Egyoldalas alkalmazás (token beszerzése egy API meghívásához) – Microsoft Identity platform
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazást (token beszerzése API meghívásához)
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f49a6093194ef76a895f2a54f8a78a55da73e7e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135712"
---
# <a name="single-page-application---acquire-a-token-to-call-an-api"></a>Egyoldalas alkalmazás – jogkivonat beszerzése egy API meghívásához

A MSAL. js használatával az API-k jogkivonatok beszerzésének mintája először egy csendes jogkivonat-kérést próbál meg használni a `acquireTokenSilent` metódussal. A metódus meghívásakor a függvénytár először ellenőrzi a gyorsítótárat a böngésző tárolójában, hogy létezik-e érvényes jogkivonat, és visszaadja azt. Ha nincs érvényes jogkivonat a gyorsítótárban, egy csendes jogkivonat-kérést küld Azure Active Directory (Azure AD) egy rejtett iframe-ből. Ez a módszer azt is lehetővé teszi, hogy a könyvtár megújítsa a jogkivonatokat. További információ az egyszeri bejelentkezési munkamenetről és a jogkivonat élettartamának értékéről az Azure AD-ben: [token élettartama](active-directory-configurable-token-lifetimes.md).

Előfordulhat, hogy az Azure AD-ba érkező csendes jogkivonat-kérelmek bizonyos okokból sikertelenek lehetnek, például lejárt Azure AD-munkamenet vagy jelszó-módosítás. Ebben az esetben meghívhatja az egyik interaktív metódust (amely felszólítja a felhasználót) a tokenek beszerzésére.

* [Token beszerzése egy előugró ablak](#acquire-token-with-a-pop-up-window) használatával`acquireTokenPopup`
* [Token beszerzése átirányítás](#acquire-token-with-redirect) használatával`acquireTokenRedirect`

**Egy előugró vagy átirányítási élmény közötti választás**

 Az előugró és az átirányítási módszerek kombinációja nem használható az alkalmazásban. Az előugró vagy átirányítási élmény közötti választás az alkalmazási folyamattól függ.

* Ha nem szeretné, hogy a felhasználó az alkalmazás főoldaláról navigáljon a hitelesítés során, javasoljuk, hogy használja az előugró metódusokat. Mivel a hitelesítés átirányítása egy előugró ablakban történik, a fő alkalmazás állapota megmarad.

* Bizonyos esetekben előfordulhat, hogy az átirányítás módszereit kell használnia. Ha az alkalmazás felhasználói olyan böngészőbeli korlátozásokkal vagy házirendekkel rendelkeznek, amelyekben az előugró ablakok le vannak tiltva, akkor használhatja az átirányítási módszereket. Azt is javasoljuk, hogy az átirányítás módszereit az Internet Explorer böngészőben is használja, mivel az előugró ablakok kezelése során bizonyos [ismert problémák merültek fel az Internet Explorerben](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser) .

Megadhatja azokat az API-hatóköröket, amelyeknek a hozzáférési jogkivonatot fel kell vennie a hozzáférési jogkivonat-kérelem létrehozásakor. Vegye figyelembe, hogy az összes kért hatókört nem lehet megadni a hozzáférési jogkivonatban, és a felhasználó beleegyezik.

## <a name="acquire-token-with-a-pop-up-window"></a>Token beszerzése előugró ablakban

### <a name="javascript"></a>JavaScript

A fenti minta az előugró élmény módszereinek használatával:

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

A MSAL szögletes burkolója biztosítja a HTTP-Interceptor hozzáadásának kényelmét, amely automatikusan lekéri a hozzáférési tokeneket, és csatolja őket a HTTP-kérésekhez API-khoz.

A `protectedResourceMap` konfigurációs beállításban megadhatja az API-k hatóköreit, amelyeket a MsalInterceptor a jogkivonatok automatikus beszerzése esetén kér le.

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

A csendes jogkivonat beszerzésének sikeressége és meghibásodása esetén a MSAL szögletes visszahívásokat biztosít, amelyekre előfizethet. Fontos megjegyezni a leiratkozást is.

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

Azt is megteheti, hogy explicit módon megvásárolja a jogkivonatokat a beszerzési token metódusok használatával, ahogy azt az alapvető MSAL. js függvénytárban leírtak

## <a name="acquire-token-with-redirect"></a>Token beszerzése átirányítás esetén

### <a name="javascript"></a>JavaScript

A minta a fent leírtak szerint van leírva, de a jogkivonat interaktív beszerzésére szolgáló átirányítási módszerrel jelenik meg. Regisztrálnia kell az átirányítás visszahívását a fentiekben leírtak szerint.

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

## <a name="request-for-optional-claims"></a>Választható jogcímek kérése
Az alkalmazásban választható jogcímeket is megadhat, amelyekkel meghatározhatja, hogy mely további jogcímek szerepeljenek az alkalmazás jogkivonatában. Ha opcionális jogcímeket szeretne kérni a id_token, küldhet egy sztringesített jogcím-objektumot a AuthenticationParameters. TS osztály claimsRequest mezőjébe.

A következő célra választható jogcímeket használhat:

- Az alkalmazáshoz tartozó jogkivonatokban lévő további jogcímek felvételéhez.
- Módosítsa az Azure AD által a jogkivonatokban visszaadott jogcímek viselkedését.
- Egyéni jogcímek hozzáadása és elérése az alkalmazáshoz.


### <a name="javascript"></a>JavaScript
```javascript
"optionalClaims":  
   {
      "idToken": [
            {
                  "name": "auth_time", 
                  "essential": true
             }
      ],

var request = {
    scopes: ["user.read"],
    claimsRequest: JSON.stringify(claims)
};

myMSALObj.acquireTokenPopup(request);
```
További információ a választható jogcímek, a pénztári [választható](active-directory-optional-claims.md) jogcímek


### <a name="angular"></a>Angular

Ez ugyanaz, mint a fentiekben leírtak szerint.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API meghívása](scenario-spa-call-api.md)
