---
title: Egyoldalas alkalmazásoktatóanyag – Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hívhatnak meg az Angular SPA-alkalmazások egy olyan API-t, amely hozzáférési jogkivonatokat igényel a Microsoft identity platform végpontjáról.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 03/05/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c3c12f78118734c31641b90e6fcb8469ddda30b0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678010"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-api-from-an-angular-single-page-application"></a>Oktatóanyag: Jelentkezzen be a felhasználókhoz, és hívja fel a Microsoft Graph API-t egy egyoldalas alkalmazásból

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás egyes aspektusai az általános elérhetőség (GA) előtt változhatnak.

Ez az oktatóanyag bemutatja, hogy egy egyoldalas alkalmazás (SPA) hogyan teheti ki:
- Jelentkezzen be személyes fiókokba, munkahelyi fiókokba vagy iskolai fiókokba.
- Szerezzen be egy hozzáférési jogkivonatot.
- Hívja meg a Microsoft Graph API-t vagy más API-kat, amelyek hozzáférési jogkivonatokat igényelnek a *Microsoft identity platform végpontjáról.*

>[!NOTE]
>Ez az oktatóanyag bemutatja, hogyan hozhat létre új Angular SPA-t a Microsoft authentication library (MSAL) használatával. Ha mintaalkalmazást szeretne letölteni, olvassa el a [rövid útmutatót.](quickstart-v2-angular.md)

## <a name="how-the-sample-app-works"></a>A mintaalkalmazás működése

![Az oktatóanyagban létrehozott mintaalkalmazás működését bemutató diagram](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>További információ

Az oktatóanyagban létrehozott mintaalkalmazás lehetővé teszi, hogy egy Angular SPA lekérdezze a Microsoft Graph API-t vagy egy webes API-t, amely elfogadja a Microsoft identity platform végponttokenjeit. Az MSAL az Angular könyvtár egy wrapper a mag MSAL.js könyvtár. Lehetővé teszi, hogy az Angular (6+) alkalmazások hitelesítsék a vállalati felhasználókat a Microsoft Azure Active Directory, a Microsoft-fiók felhasználói és a közösségi identitás felhasználói (például a Facebook, a Google és a LinkedIn) használatával. A tár lehetővé teszi, hogy az alkalmazások hozzáférjenek a Microsoft felhőszolgáltatásaihoz vagy a Microsoft Graphhoz.

Ebben a forgatókönyvben, miután egy felhasználó bejelentkezik, egy hozzáférési jogkivonatot kér, és hozzáadja a HTTP-kérelmek az engedélyezési fejlécen keresztül. A tokenek beszerzését és megújítását az MSAL kezeli.

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Kódtárak

Ez az oktatóanyag a következő könyvtárat használja:

|Erőforrástár|Leírás|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Microsoft Hitelesítési könyvtár JavaScript-háromszögburkolóhoz|

Az MSAL.js tár forráskódját a GitHub [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) tártárában találja.

<!--end-collapse-->


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag futtatásához a következőkre van szükség:

* Egy helyi webkiszolgáló, például [a Node.js](https://nodejs.org/en/download/). Az oktatóanyag utasításai a Node.js-en alapulnak.
* A projektfájlok szerkesztéséhez integrált fejlesztői környezet (IDE), például [Visual Studio-kód.](https://code.visualstudio.com/download)

## <a name="create-your-project"></a>A projekt létrehozása

Hozzon létre egy új Szögletes alkalmazást a következő npm parancsokkal:

```Bash
npm install -g @angular/cli@8                    # Install the Angular CLI
npm install @angular/material@8 @angular/cdk@8   # Install the Angular Material component library (optional, for UI)
ng new my-application --routing=true --style=css # Generate a new Angular app
npm install msal @azure/msal-angular             # Install MSAL and MSAL Angular in your application
ng generate component page-name                  # To add a new page (such as a home or profile page)
```

## <a name="register-your-application"></a>Alkalmazás regisztrálása

Kövesse az [utasításokat egy egyoldalas alkalmazás regisztrálásához](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) az Azure Portalon.

A regisztráció **alkalmazásáttekintése** lapján jegyezze fel az **alkalmazás (ügyfél) azonosító** értékét későbbi használatra.

Regisztrálja az **átirányítási URI-értéket,** **http://localhost:4200/** és engedélyezze az implicit támogatási beállításokat.

## <a name="configure-the-application"></a>Az alkalmazás konfigurálása

1. Az *src/app* mappában edit *app.module.ts* és add, `MSALModule` `imports` valamint az `isIE` állandó:

    ```javascript
    const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;
    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        AppRoutingModule,
        MsalModule.forRoot({
          auth: {
            clientId: 'Enter_the_Application_Id_here', // This is your client ID
            authority: 'Enter_the_Cloud_Instance_Id_Here'/'Enter_the_Tenant_Info_Here', // This is your tenant ID
            redirectUri: 'Enter_the_Redirect_Uri_Here'// This is your redirect URI
          },
          cache: {
            cacheLocation: 'localStorage',
            storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
          },
        }, {
          popUp: !isIE,
          consentScopes: [
            'user.read',
            'openid',
            'profile',
          ],
          unprotectedResources: [],
          protectedResourceMap: [
            ['https://graph.microsoft.com/v1.0/me', ['user.read']]
          ],
          extraQueryParameters: {}
        })
      ],
      providers: [],
      bootstrap: [AppComponent]
    })
    ```

    Cserélje le ezeket az értékeket:

    |Érték neve|Névjegy|
    |---------|---------|
    |Enter_the_Application_Id_Here|Az **alkalmazásregisztráció Áttekintés lapján** ez az **Alkalmazás (ügyfél) azonosító** értéke. |
    |Enter_the_Cloud_Instance_Id_Here|Ez az Azure-felhő példánya. A fő vagy globális Azure-felhőbe írja be a ( fő vagy globális Azure-felhő) mezőbe. **https://login.microsoftonline.com** A nemzeti felhők (például Kína), lásd: [Nemzeti felhők](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
    |Enter_the_Tenant_Info_Here| Állítsa be a következő beállítások egyikét: Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat,* cserélje le ezt az értéket a címtár (bérlői) azonosító vagy a bérlő nevére (például **contoso.microsoft.com).** Ha az alkalmazás *bármely szervezeti címtárban támogatja a fiókokat,* cserélje le ezt az értéket **szervezetekre.** Ha az alkalmazás támogatja *a szervezeti címtárban és a személyes Microsoft-fiókokban lévő fiókokat,* cserélje le ezt az értéket a **közös értékre.** Ha csak *a személyes Microsoft-fiókokra*szeretné korlátozni a támogatást, cserélje le ezt az értéket **a fogyasztókra**. |
    |Enter_the_Redirect_Uri_Here|Csere **http://localhost:4200**a következőre:|

    Az elérhető konfigurálható beállításokról az [Ügyfélalkalmazások inicializálása](msal-js-initializing-client-applications.md)című témakörben talál további információt.

2. Ugyanannak a fájlnak a tetején adja hozzá a következő importálási utasítást:

    ```javascript
    import { MsalModule, MsalInterceptor } from '@azure/msal-angular';
    ```

3. Adja hozzá a következő importálási `src/app/app.component.ts`nyilatkozatokat a következő szövegéhez:

    ```javascript
    import { MsalService } from '@azure/msal-angular';
    import { Component, OnInit } from '@angular/core';
    ```
## <a name="sign-in-a-user"></a>Felhasználó bejelentkezése

Adja hozzá a `AppComponent` következő kódot a felhasználó bejelentkezéséhez:

```javascript
export class AppComponent implements OnInit {
    constructor(private broadcastService: BroadcastService, private authService: MsalService) { }

    login() {
        const isIE = window.navigator.userAgent.indexOf('MSIE ') > -1 || window.navigator.userAgent.indexOf('Trident/') > -1;

        if (isIE) {
          this.authService.loginRedirect({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        } else {
          this.authService.loginPopup({
            extraScopesToConsent: ["user.read", "openid", "profile"]
          });
        }
    }
}
```

> [!TIP]
> Javasoljuk, `loginRedirect` hogy az Internet Explorer felhasználói számára használja.

## <a name="acquire-a-token"></a>Jogkivonat beszerzése

### <a name="angular-interceptor"></a>Szögelfogó

Az MSAL Angular egy olyan osztályt `Interceptor` biztosít, amely automatikusan `http` beszerzi a jogkivonatokat az olyan kimenő kérelmekhez, amelyek az Angular ügyfelet használják az ismert védett erőforrásokhoz.

Először is, adja meg az `Interceptor` osztályt, mint az alkalmazás szolgáltatóját:

```javascript
import { MsalInterceptor, MsalModule } from "@azure/msal-angular";
import { HTTP_INTERCEPTORS, HttpClientModule } from "@angular/common/http";

@NgModule({
    // ...
    providers: [
        {
            provide: HTTP_INTERCEPTORS,
            useClass: MsalInterceptor,
            multi: true
        }
    ]
}
```

Ezután adja meg a védett `MsalModule.forRoot()` `protectedResourceMap` erőforrások térképét, `consentScopes`és tartalmazza ezeket a hatóköröket a következőben:

```javascript
@NgModule({
  // ...
  imports: [
    // ...
    MsalModule.forRoot({
      auth: {
        clientId: 'Enter_the_Application_Id_here', // This is your client ID
        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
      },
      cache: {
        cacheLocation: 'localStorage',
        storeAuthStateInCookie: isIE, // Set to true for Internet Explorer 11
      },
    },
    {
      popUp: !isIE,
      consentScopes: [
        'user.read',
        'openid',
        'profile',
      ],
      unprotectedResources: [],
      protectedResourceMap: [
        ['https://graph.microsoft.com/v1.0/me', ['user.read']]
      ],
      extraQueryParameters: {}
    })
  ],
});
```

Végül kérje le egy felhasználó profilját HTTP-kérelemmel:

```JavaScript
const graphMeEndpoint = "https://graph.microsoft.com/v1.0/me";

getProfile() {
  this.http.get(graphMeEndpoint).toPromise()
    .then(profile => {
      this.profile = profile;
    });
}
```

### <a name="acquiretokensilent-acquiretokenpopup-acquiretokenredirect"></a>acquireTokenSilent, acquireTokenPopup, acquireTokenRedirect
Az MSAL három módszert használ `acquireTokenRedirect` `acquireTokenPopup`a `acquireTokenSilent`tokenek beszerzésére: , , és . Azonban azt javasoljuk, `MsalInterceptor` hogy az osztály helyett az Angular alkalmazások, az előző szakaszban látható módon.

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `acquireTokenSilent` módszer kezeli a tokenek beszerzése és megújítása felhasználói beavatkozás nélkül. Miután `loginRedirect` `loginPopup` a vagy metódus végrehajtása `acquireTokenSilent` az első alkalommal, gyakran használják a jogkivonatok eléréséhez használt védett erőforrások későbbi hívásokban. A tokenek kérelmezésére vagy megújítására irányuló hívások csendesen történik.

```javascript
const requestObj = {
    scopes: ["user.read"]
};

this.authService.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

Ebben a `scopes` kódban tartalmazza a rendszerkérést az API hozzáférési jogkivonatában visszaadandó hatóköröket.

Például:

* `["user.read"]`Microsoft Graph-hoz
* `["<Application ID URL>/scope"]`egyéni webes API-khoz `api://<Application ID>/access_as_user`(azaz)

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

Néha szükség van a felhasználónak, hogy a Microsoft identity platform végpont. Például:

* Előfordulhat, hogy a felhasználóknak újra meg kell adniuk a hitelesítő adataikat, mert a jelszavuk lejárt.
* Az alkalmazás további erőforrás-hatókörökhöz való hozzáférést kér, amelyekhez a felhasználónak hozzá kell járulnia.
* Kétfaktoros hitelesítés szükséges.

Az ajánlott minta a `acquireTokenSilent` legtöbb alkalmazás számára az, hogy `acquireTokenPopup` először hívja, majd elkapni a kivételt, majd hívja meg (vagy `acquireTokenRedirect`) egy interaktív kérelem indításához.

A `acquireTokenPopup` hívás eredménye előugró bejelentkezési ablak. Másik lehetőségként `acquireTokenRedirect` átirányítja a felhasználókat a Microsoft identity platform végpontjára. Ebben az ablakban a felhasználóknak meg kell erősíteniük a hitelesítő adataikat, meg kell adniuk a szükséges erőforrás beleegyezését, vagy el kell végezniük a kétlépéses hitelesítést.

```javascript
  const requestObj = {
      scopes: ["user.read"]
  };

  this.authService.acquireTokenPopup(requestObj).then(function (tokenResponse) {
      // Callback code here
      console.log(tokenResponse.accessToken);
  }).catch(function (error) {
      console.log(error);
  });
```

> [!NOTE]
> Ez a rövid `loginRedirect` `acquireTokenRedirect` útmutató a Microsoft Internet Explorer programot használja, mert [ismert probléma](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) van az előugró ablakok Internet Explorer általi kezelésével kapcsolatban.

## <a name="log-out"></a>Kijelentkezés

Adja hozzá a következő kódot a felhasználó kijelentkeztetéséhez:

```javascript
logout() {
  this.authService.logout();
}
```

## <a name="add-ui"></a>Felhasználói felület hozzáadása
A felhasználói felület hozzáadásának például a Szögletes anyag összetevőtár használatával a [mintaalkalmazás ban](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular)található.

## <a name="test-your-code"></a>A kód tesztelése

1.  Indítsa el a webkiszolgálót a port meghallgatásához úgy, hogy az alkalmazásmappából parancssori parancssori parancssorban futtatja a következő parancsokat:

    ```bash
    npm install
    npm start
    ```
1. A böngészőben **http://localhost:4200** adja **http://localhost:{port}** meg vagy a , ahol a *port* az a port, amelyet a webkiszolgáló figyel.


### <a name="provide-consent-for-application-access"></a>Hozzájárulás hozzájárulása az alkalmazáshoz való hozzáféréshez

Amikor először kezd bejelentkezni az alkalmazásba, a rendszer kéri, hogy adjon hozzáférést a profiljához, és engedélyezze a bejelentkezést:

![A "Kért engedélyek" ablak](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)



<!--start-collapse-->
### <a name="add-scopes-and-delegated-permissions"></a>Hatókörök és delegált engedélyek hozzáadása

A Microsoft Graph API-nak a *user.read* hatókörre van szüksége a felhasználói profil olvasásához. Alapértelmezés szerint ez a hatókör automatikusan hozzáadódik a regisztrációs portálon regisztrált minden alkalmazáshoz. A Microsoft Graph egyéb API-k, valamint a háttérkiszolgáló egyéni API-jai további hatóköröket igényelhetnek. A Microsoft Graph API-nak például a *Naptárak.Read* hatókörre van szükség a felhasználó naptárai listázásához.

Ha egy alkalmazás környezetében szeretné elérni a felhasználó naptárait, adja hozzá a *Calendars.Read* delegált engedélyt az alkalmazás regisztrációs adataihoz. Ezután adja hozzá a *Calendars.Read* hatókört a `acquireTokenSilent` híváshoz.

>[!NOTE]
>A rendszer további jóváhagyásokat kérhet a felhasználótól, ahogy növeli a hatókörök számát.

Ha egy háttér-API nem igényel hatókört (nem ajánlott), *használhatja az ügyfélazonosító* t, mint a jogkivonatok beszerzéséhez a hívások hatóköre.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>További lépések

Ezután megtudhatja, hogyan jelentkeztetheti be a felhasználót, és hogyan szerezhet tokeneket az Angular oktatóanyagban:

> [!div class="nextstepaction"]
> [Szögletes bemutató](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular)
