---
title: Token beszerzése egyoldalas alkalmazásokban – Microsoft Identity platform | Azure
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
ms.openlocfilehash: 9723e9a58704a583c7332db11bae7da6b045a5f7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919834"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Egyoldalas alkalmazás: token beszerzése egy API meghívásához

A MSAL. js használatával az API-k jogkivonatok beszerzésének mintája először egy csendes jogkivonat-kérést próbál meg használni a `acquireTokenSilent` metódussal. A metódus meghívásakor a függvénytár először ellenőrzi a gyorsítótárat a böngésző tárolójában, hogy létezik-e érvényes jogkivonat, és visszaadja azt. Ha nem található érvényes jogkivonat a gyorsítótárban, egy csendes jogkivonat-kérést küld Azure Active Directory (Azure AD) egy rejtett iframe-ből. Ez a módszer azt is lehetővé teszi, hogy a könyvtár megújítsa a jogkivonatokat. További információ az egyszeri bejelentkezési munkamenetről és a jogkivonat élettartamának értékéről az Azure AD-ben: [token élettartama](active-directory-configurable-token-lifetimes.md).

Az Azure AD-ba érkező csendes jogkivonat-kérések sikertelenek lehetnek olyan okokból, mint a lejárt Azure AD-munkamenet vagy a jelszó módosítása. Ebben az esetben meghívhatja az egyik interaktív metódust (amely felszólítja a felhasználót) a tokenek beszerzésére:

* [Előugró ablak](#acquire-a-token-with-a-pop-up-window), `acquireTokenPopup` használatával
* [Átirányítás](#acquire-a-token-with-a-redirect)`acquireTokenRedirect` használatával

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Válasszon egy előugró vagy átirányítási élmény közül

 Az alkalmazásban nem használhatók az előugró és az átirányítási módszerek. Az előugró vagy átirányítási élmény közötti választás az alkalmazási folyamattól függ:

* Ha nem szeretné, hogy a felhasználók a hitelesítés során elmozdulnak az alkalmazás főoldaláról, javasoljuk, hogy az előugró metódust. Mivel a hitelesítés átirányítása egy előugró ablakban történik, a fő alkalmazás állapota megmarad.

* Ha a felhasználók olyan böngészőbeli korlátozásokkal vagy házirendekkel rendelkeznek, amelyekben az előugró ablakok le vannak tiltva, használhatja az átirányítási módszert. Használja az átirányítás módszert az Internet Explorer böngészővel, mert az [Internet Explorerben ismert problémák léptek fel az előugró ablakokban](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser).

Megadhatja azokat az API-hatóköröket, amelyeknek a hozzáférési jogkivonatot fel kell vennie a hozzáférési jogkivonat-kérelem létrehozásakor. Vegye figyelembe, hogy az összes kért hatókör nem adható meg a hozzáférési jogkivonatban. Ez a felhasználó beleegyezik.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Token beszerzése előugró ablakban

### <a name="javascript"></a>JavaScript

A következő kód az előugró felület módszereivel ötvözi a korábban leírt mintát:

```javascript
const accessTokenRequest = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
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

A MSAL szögletes burkolója biztosítja a HTTP Interceptor-t, amely a hozzáférési jogkivonatokat csendes üzemmódban automatikusan beszerzi, és csatolja őket a HTTP-kérésekhez API-khoz.

A `protectedResourceMap` konfigurációs beállításban megadhatja az API-k hatóköreit. `MsalInterceptor` fogja kérni ezeket a hatóköröket a jogkivonatok automatikus beszerzéséhez.

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

A csendes jogkivonat beszerzésének sikerességéhez és meghibásodásához a MSAL szögletes visszahívásokat biztosít, amelyek előfizethetnek a szolgáltatásra. Fontos megjegyezni a leiratkozást is.

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

Azt is megteheti, hogy a tokeneket explicit módon beolvassa a beszerzési lexikális metódusok használatával, ahogy azt az alapvető MSAL. js-függvénytárban ismertetjük.

## <a name="acquire-a-token-with-a-redirect"></a>Token beszerzése átirányítás

### <a name="javascript"></a>JavaScript

A következő minta a korábban leírtaknak megfelelően, de a tokenek interaktív beszerzésére szolgáló átirányítási módszerrel látható. A korábban említettek szerint regisztrálnia kell az átirányítás visszahívását.

```javascript
function authCallback(error, response) {
    // Handle redirect response
}

userAgentApplication.handleRedirectCallback(authCallback);

const accessTokenRequest: AuthenticationParameters = {
    scopes: ["user.read"]
}

userAgentApplication.acquireTokenSilent(accessTokenRequest).then(function(accessTokenResponse) {
    // Acquire token silent success
    // Call API with token
    let accessToken = accessTokenResponse.accessToken;
}).catch(function (error) {
    //Acquire token silent failure, and send an interactive request
    console.log(error);
    if (error.errorMessage.indexOf("interaction_required") !== -1) {
        userAgentApplication.acquireTokenRedirect(accessTokenRequest);
    }
});
```

## <a name="request-optional-claims"></a>Választható jogcímek kérése
A választható jogcímeket a következő célokra használhatja:

- Adja meg az alkalmazáshoz tartozó jogkivonatok további jogcímeit.
- Módosítsa az Azure AD által a jogkivonatokban visszaadott jogcímek viselkedését.
- Egyéni jogcímek hozzáadása és elérése az alkalmazáshoz. 

Ha `IdToken`nem kötelező jogcímeket szeretne kérni, küldhet egy sztringesített jogcím-objektumot az `AuthenticationParameters.ts` osztály `claimsRequest` mezőjébe.

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
További információ: [opcionális jogcímek](active-directory-optional-claims.md).


### <a name="angular"></a>Angular

Ez a kód megegyezik a korábban leírtak szerint.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes API meghívása](scenario-spa-call-api.md)
