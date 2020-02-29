---
title: Bejelentkezési felhasználók JavaScript egyoldalas alkalmazásokban | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hívhat meg egy JavaScript-alkalmazás olyan API-t, amely a Microsoft Identity platform használatával hozzáférési jogkivonatokat igényel.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.openlocfilehash: 9fc77b876474c89014998ce789f91f098fb3735c
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161049"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Gyors útmutató: bejelentkezés a felhasználókba és hozzáférési jogkivonat beszerzése egy JavaScript SPA-ban

Ebben a rövid útmutatóban megtudhatja, hogyan jelentkezhet be a JavaScript egyoldalas alkalmazás (SPA) a személyes fiókok, a munkahelyi fiókok és az iskolai fiókok felhasználói számára. A JavaScript SPA a Microsoft Graph API vagy bármely webes API meghívására is kérhet hozzáférési jogkivonatot. (Lásd: [Hogyan működik a minta](#how-the-sample-works) egy ábrán.)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/).
* [Visual Studio Code](https://code.visualstudio.com/download) (projektfájlok szerkesztése)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>A gyors üzembe helyezési alkalmazás regisztrálása és letöltése
> A gyors üzembe helyezési alkalmazás indításához használja az alábbi lehetőségek egyikét.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1\. lehetőség (expressz): regisztrálja és állítsa be automatikusan az alkalmazást, majd töltse le a kód mintáját
>
> 1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
> 1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókot a jobb felső sarokban, majd állítsa be a portál munkamenetét a használni kívánt Azure Active Directory (Azure AD) bérlőre.
> 1. Nyissa meg az új [Azure Portal-Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) ablaktáblát.
> 1. Adja meg az alkalmazás nevét. 
> 1. A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
> 1. Kattintson a **Register** (Regisztrálás) elemre.
> 1. Az új alkalmazás letöltéséhez és automatikus konfigurálásához kövesse az utasításokat.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>2\. lehetőség (manuális): az alkalmazás-és a kód-minta regisztrálása és manuális konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása
>
> 1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
>
> 1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókját a jobb felső sarokban, majd állítsa be a portál munkamenetét a használni kívánt Azure AD-bérlőre.
> 1. Nyissa meg a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapját.
> 1. Válassza az **új regisztráció**lehetőséget.
> 1. Amikor megjelenik az **alkalmazás regisztrálása** lap, adja meg az alkalmazás nevét.
> 1. A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
> 1. Kattintson a **Register** (Regisztrálás) elemre. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét későbbi használatra.
> 1. Ez a rövid útmutató az [implicit engedélyezési folyamat](v2-oauth2-implicit-grant-flow.md) engedélyezését igényli. A regisztrált alkalmazás bal oldali ablaktábláján válassza a **hitelesítés**lehetőséget.
> 1. A **platform-konfigurációk**területen válassza **a platform hozzáadása**lehetőséget. Ekkor megnyílik egy panel a bal oldalon. Itt válassza ki a **webalkalmazások** régiót.
> 1. Továbbra is a bal oldalon adja meg az **átirányítási URI** értékét `http://localhost:3000/`. Ezután válassza a **hozzáférési jogkivonat** és az **azonosító token**lehetőséget.
> 1. Válassza a **Konfigurálás**lehetőséget.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1\. lépés: az alkalmazás konfigurálása a Azure Portalban
> Ahhoz, hogy az ebben a rövid útmutatóban szereplő kód minta legyen, hozzá kell adnia egy `redirectUri` `http://localhost:3000/`ként, és engedélyezni kell az **implicit engedélyezést**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-javascript/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-project"></a>2\. lépés: A projekt letöltése

Válassza ki a fejlesztési környezetének megfelelő lehetőséget:

* Ha a projektet a Node. js használatával webkiszolgálóval szeretné futtatni, [töltse le az alapprojekt fájljait](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip). A fájlok megnyitásához használjon egy szerkesztőt, például a [Visual Studio Code](https://code.visualstudio.com/)-ot.

#### <a name="step-3-configure-your-javascript-app"></a>3\. lépés: a JavaScript-alkalmazás konfigurálása

> [!div renderon="docs"]
> A *JavaScriptSPA* mappában szerkessze a *authConfig. js*fájlt, és állítsa be a `clientID` és `authority` értékeket a `msalConfig`alatt.

> [!div class="sxs-lookup" renderon="portal"]
> A *JavaScriptSPA* mappában szerkessze a *authConfig. js*fájlt, és cserélje le a `msalConfig`t a következő kódra:

```javascript

  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "Enter_the_Application_Id_Here",
      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
      redirectUri: "Enter_the_Redirect_Uri_Here",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
      forceRefresh: false // Set this to "true" to skip a cached token and go to the server to get a new
    }
  };  

```
> [!div renderon="portal"]
> > [!NOTE]
> > Ez a rövid útmutató támogatja a Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
>
> Az elemek magyarázata:
> - *\<Enter_the_Application_Id_Here >* a regisztrált alkalmazáshoz tartozó **alkalmazás (ügyfél) azonosítója** .
> - *\<Enter_the_Cloud_Instance_Id_Here >* az Azure-felhő példánya. A fő vagy a globális Azure-felhőhöz egyszerűen írja be a *https://login.microsoftonline.com* . Az **országos** felhők (például Kína) esetében lásd: [nemzeti felhők](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
> - *\<Enter_the_Tenant_info_here >* a következő lehetőségek egyikére van beállítva:
>    - Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a **bérlői azonosítóra** vagy a **bérlő nevére** (például *contoso.microsoft.com*).
>    - Ha az alkalmazás *minden szervezeti címtárban támogatja a fiókokat*, cserélje le ezt az értéket **szervezetekkel**.
>    - Ha az alkalmazás *minden szervezeti címtárban és személyes Microsoft-fiókban is támogatja a fiókokat*, cserélje le ezt az értéket **közösre**. Ha *csak a személyes Microsoft-fiókok*támogatását szeretné korlátozni, cserélje le ezt az értéket a **fogyasztókkal**.
>
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítója**, a **címtár (bérlő) azonosítója** és a **támogatott fióktípusok** értékét az alkalmazás **Áttekintés** oldalán találja az Azure Portalon.
>

#### <a name="step-4-run-the-project"></a>4\. lépés: a projekt futtatása

Ha a [Node. js](https://nodejs.org/en/download/)-t használja:

1. A kiszolgáló elindításához futtassa a következő parancsot a projekt könyvtárából:

   ```batch
   npm install
   npm start
   ```

1. Nyisson meg egy webböngészőt, és lépjen a `http://localhost:3000/`.
1. A bejelentkezés elindításához válassza a **Bejelentkezés** lehetőséget, majd hívja meg Microsoft Graph API-t.

Ha a böngésző betölti az alkalmazást, válassza a **Bejelentkezés**lehetőséget. Amikor először jelentkezik be, a rendszer arra kéri, hogy adja meg a beleegyezik, hogy engedélyezze az alkalmazásnak a profil elérését és a bejelentkezést. Miután sikeresen bejelentkezett, a felhasználói profil adatai megjelennek az oldalon.

## <a name="more-information"></a>További információ

### <a name="how-the-sample-works"></a>A minta működése

![A jelen rövid útmutatóban szereplő minta alkalmazás működése](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

A MSAL-könyvtár bejelentkezik a felhasználók számára, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kéri. A gyors útmutató *index. html* fájlja a könyvtárra mutató hivatkozást tartalmaz:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> Az előző verziót lecserélheti a legújabb, a [MSAL. js kiadásban](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)megjelent verzióra.


Ha a Node. js telepítve van, a legújabb verziót a Node. js csomagkezelő (NPM) használatával töltheti le:

```batch
npm install msal
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

A gyors üzembe helyezési kód azt is bemutatja, hogyan inicializálható a MSAL-könyvtár:

```javascript
  // Config object to be passed to Msal on creation
  const msalConfig = {
    auth: {
      clientId: "75d84e7a-40bx-f0a2-91b9-0c82d4c556aa", // this is a fake id
      authority: "https://login.microsoftonline.com/common",
      redirectUri: "http://localhost:3000/",
    },
    cache: {
      cacheLocation: "sessionStorage", // This configures where your cache will be stored
      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
      forceRefresh: false // Set this to "true" to skip a cached token and go to the server to get a new
    }
  };  

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Ahol  |  |
> |---------|---------|
> |`clientId`     | A Azure Portalban regisztrált alkalmazás alkalmazás-azonosítója.|
> |`authority`    | Választható A fiók típusát támogató szolgáltatói URL-cím, a konfiguráció szakaszban korábban leírtak szerint. Az alapértelmezett szolgáltató `https://login.microsoftonline.com/common`. |
> |`redirectUri`     | Az alkalmazás regisztrációja konfigurált válasz-/redirectUri. Ebben az esetben `http://localhost:3000/`. |
> |`cacheLocation`  | Választható Beállítja a böngésző tárolóját a hitelesítési állapothoz. Az alapértelmezett érték a sessionStorage.   |
> |`storeAuthStateInCookie`  | Választható A böngésző cookie-jai hitelesítési folyamatainak érvényesítéséhez szükséges hitelesítési kérési állapotot tároló könyvtár. Ez a cookie az IE és a Edge böngészők számára van beállítva, hogy bizonyos [ismert problémákkal](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)enyhíthető legyen. |

További információ az elérhető konfigurálható lehetőségekről: [ügyfélalkalmazások inicializálása](msal-js-initializing-client-applications.md).

### <a name="sign-in-users"></a>Bejelentkezési felhasználók

A következő kódrészlet bemutatja, hogyan jelentkezhet be a felhasználókba:

```javascript
// Add scopes for the id token to be used at Microsoft identity platform endpoints.
const loginRequest = {
    scopes: ["openid", "profile", "User.Read"],
};

myMSALObj.loginPopup(loginRequest)
    .then((loginResponse) => {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Ahol  |  |
> |---------|---------|
> | `scopes`   | Választható Olyan hatóköröket tartalmaz, amelyeket a rendszer a bejelentkezési időben a felhasználói beleküldéshez kér. Például `[ "user.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni webes API-khoz (azaz `api://<Application ID>/access_as_user`). |

> [!TIP]
> Azt is megteheti, hogy a `loginRedirect` metódussal szeretné átirányítani az aktuális lapot a bejelentkezési oldalra a felugró ablak helyett.

### <a name="request-tokens"></a>Kérelmek jogkivonatai

A MSAL három módszert használ a tokenek beszerzésére: `acquireTokenRedirect`, `acquireTokenPopup`és `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `acquireTokenSilent` metódus felhasználói beavatkozás nélkül kezeli a tokenek beszerzését és megújítását. A `loginRedirect` vagy `loginPopup` metódus első végrehajtása után a rendszer a következő metódust használja a védett erőforrások eléréséhez használt jogkivonatok beszerzéséhez: `acquireTokenSilent`. A jogkivonatok kérése vagy megújítása csendesen történik.

```javascript
// Add scopes for the access token to be used at Microsoft Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenSilent(tokenRequest)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> |Ahol  |  |
> |---------|---------|
> | `scopes`   | Az API hozzáférési jogkivonatában visszaadott hatóköröket tartalmaz. Például `[ "mail.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni webes API-khoz (azaz `api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

Vannak olyan helyzetek, amikor kényszeríteni kell a felhasználókat, hogy együttműködjön a Microsoft Identity platform-végponttal. Például:
* Előfordulhat, hogy a felhasználóknak újra meg kell adniuk a hitelesítő adataikat, mert a jelszavuk lejárt.
* Az alkalmazása olyan további erőforrás-hatókörökhöz kér hozzáférést, amelyeknek a felhasználónak hozzá kell járulnia.
* Kétfaktoros hitelesítés szükséges.

A legtöbb alkalmazás esetében a szokásos ajánlott minta a `acquireTokenSilent` első meghívása, majd a kivétel elfogása, majd a `acquireTokenPopup` (vagy `acquireTokenRedirect`) meghívása egy interaktív kérelem elindításához.

A `acquireTokenPopup` az eredményeket felugró ablakban hívja meg a bejelentkezéshez. (Vagy `acquireTokenRedirect` a felhasználókat a Microsoft Identity platform-végpontra irányítja át.) Ebben az ablakban a felhasználóknak kapcsolatba kell lépniük a hitelesítő adataik jóváhagyásával, a szükséges erőforrás megadásával vagy a kétfaktoros hitelesítés végrehajtásával.

```javascript
// Add here scopes for access token to be used at MS Graph API endpoints.
const tokenRequest = {
    scopes: ["Mail.Read"]
};

myMSALObj.acquireTokenPopup(requestObj)
    .then((tokenResponse) => {
        // Callback code here
        console.log(tokenResponse.accessToken);
    }).catch((error) => {
        console.log(error);
    });
```

> [!NOTE]
> Ez a rövid útmutató a `loginRedirect` és a `acquireTokenRedirect` metódusokat használja a Microsoft Internet Explorerrel, mert az Internet Explorer felugró ablakok kezelésére vonatkozó [ismert probléma](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) miatt.

## <a name="next-steps"></a>Következő lépések

A rövid útmutatóhoz tartozó alkalmazás létrehozásával kapcsolatos részletes útmutatót a következő témakörben talál:

> [!div class="nextstepaction"]
> [Oktatóanyag a bejelentkezéshez és az MS Graph meghívásához](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

A MSAL-tárházban a dokumentáció, a gyakori kérdések és a problémák megtekintéséhez lásd:

> [!div class="nextstepaction"]
> [MSAL. js GitHub-tárház](https://github.com/AzureAD/microsoft-authentication-library-for-js)

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával.

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
