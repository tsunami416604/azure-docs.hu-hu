---
title: Beszerezni egy token hívni egy webes API (egyoldalas alkalmazások) - Microsoft identity platform | Azure
description: Ismerje meg, hogyan hozhat létre egy egyoldalas alkalmazást (szerezzen be egy jogkivonatot egy API hívásához)
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/20/2019
ms.author: negoe
ms.custom: aaddev
ms.openlocfilehash: eeba01a609a1a21ed564c0b9cb78a28a4ad5c95a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882318"
---
# <a name="single-page-application-acquire-a-token-to-call-an-api"></a>Egyoldalas alkalmazás: Jogkivonat beszerzése API-hívásához

Az MSAL.js-rel rendelkező API-k tokenek beszerzésének mintája az, `acquireTokenSilent` hogy először kíséreljen meg egy csendes jogkivonat-kérelmet a metódus használatával. Ha ezt a metódust hívják meg, a tár először ellenőrzi a gyorsítótárat a böngésző tárolójában, hogy létezik-e érvényes jogkivonat, és visszaadja azt. Ha nincs érvényes jogkivonat a gyorsítótárban, egy csendes jogkivonat-kérelmet küld az Azure Active Directorynak (Azure AD) egy rejtett iframe-ből. Ez a módszer lehetővé teszi a kódtár számára a jogkivonatok megújítását is. Az Azure AD egyszeri bejelentkezési munkamenetről és jogkivonat élettartamának [értékeiről a Token élettartama című témakörben](active-directory-configurable-token-lifetimes.md)talál további információt.

A csendes jogkivonat-kérelmek az Azure AD sikertelen lehet okok miatt, például egy lejárt Azure AD-munkamenet vagy a jelszó módosítása. Ebben az esetben meghívhatja az interaktív módszerek egyikét (amely a felhasználót kéri a jogkivonatok beszerzésére:

* [Előugró ablak](#acquire-a-token-with-a-pop-up-window), a`acquireTokenPopup`
* [Átirányítás](#acquire-a-token-with-a-redirect), a`acquireTokenRedirect`

## <a name="choose-between-a-pop-up-or-redirect-experience"></a>Válasszon az előugró vagy átirányítási élmény közül

 Nem használhatja mind az előugró és átirányítási módszereket az alkalmazásban. Az előugró vagy átirányítási élmény közötti választás az alkalmazásfolyamattól függ:

* Ha nem szeretné, hogy a felhasználók a hitelesítés során eltávolodjanak a fő alkalmazáslaptól, az előugró módszert javasoljuk. Mivel a hitelesítési átirányítás előugró ablakban történik, a fő alkalmazás állapota megmarad.

* Ha a felhasználók böngészőkorlátozásokkal vagy olyan házirendekkel rendelkeznek, amelyeknél az előugró ablakok le vannak tiltva, használhatja az átirányítási módszert. Használja az átirányítási módszert az Internet Explorer böngészővel, mert ismert problémák vannak [az Internet Explorer előugró ablakaival.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser)

Beállíthatja az API-hatókörök, amelyek a hozzáférési jogkivonatot, hogy tartalmazza a hozzáférési jogkivonat-kérelem létrehozásakor. Vegye figyelembe, hogy az összes kért hatókörök nem adható meg a hozzáférési jogkivonat. Ez a felhasználó beleegyezésétől függ.

## <a name="acquire-a-token-with-a-pop-up-window"></a>Token beszerzése előugró ablakkal

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A következő kód egyesíti a korábban leírt mintát az előugró felület módszereivel:

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

# <a name="angular"></a>[Angular](#tab/angular)

Az MSAL-gyűrűs biztosítja a HTTP-elfogó, amely automatikusan beszerzi a hozzáférési jogkivonatokat csendben, és csatolja őket a HTTP-kérelmek API-khoz.

Az API-k hatóköreit a `protectedResourceMap` konfigurációs beállításban adhatja meg. `MsalInterceptor`kérni fogja ezeket a hatóköröket, amikor automatikusan beszerzi a jogkivonatokat.

```javascript
// app.module.ts
@NgModule({
  declarations: [
    // ...
  ],
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_Here',
      }
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ]
    })
  ],
  providers: [
    {
      provide: HTTP_INTERCEPTORS,
      useClass: MsalInterceptor,
      multi: true
    }
  ],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

A néma token-beszerzés sikeréhez és sikertelenségéhez az MSAL Angular olyan visszahívásokat biztosít, amelyekre előfizethet. Az is fontos, hogy ne feledje, hogy leiratkozni.

```javascript
// In app.component.ts
 ngOnInit() {
    this.subscription=  this.broadcastService.subscribe("msal:acquireTokenFailure", (payload) => {
    });
}

ngOnDestroy() {
   this.broadcastService.getMSALSubject().next(1);
   if (this.subscription) {
     this.subscription.unsubscribe();
   }
 }
```

Azt is megteheti, hogy explicit módon szerez jogkivonatokat a beszerzés-token metódusok használatával az alapvető MSAL.js könyvtárban leírtak szerint.

---

## <a name="acquire-a-token-with-a-redirect"></a>Token beszerzése átirányítással

# <a name="javascript"></a>[JavaScript](#tab/javascript)

A következő minta a korábban leírt, de látható egy átirányítási módszerrel a jogkivonatok interaktív megszerzéséhez. Regisztrálnia kell az átirányításvisszahívást, ahogy azt korábban említettük.

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

## <a name="request-optional-claims"></a>Opcionális jogcímek kérése

A választható jogcímek a következő célokra használhatók:

- További jogcímeket az alkalmazás jogkivonataiban.
- Módosíthatja bizonyos jogcímek viselkedését, amelyeket az Azure AD jogkivonatokban ad vissza.
- Egyéni jogcímek hozzáadása és elérése az alkalmazáshoz.

Ha nem kötelező `IdToken`jogcímeket szeretne kérni a `claimsRequest` alkalmazásban, `AuthenticationParameters.ts` akkor az osztály mezőjébe karakterláncba kötött jogcímobjektumot is küldhet.

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

További információ: [Választható jogcímek](active-directory-optional-claims.md).

# <a name="angular"></a>[Angular](#tab/angular)

Ez a kód megegyezik a korábban leírt.

---

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API hívása](scenario-spa-call-api.md)
