---
title: Felhasználók bejelentkezése JavaScript egyoldalas alkalmazásokban | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hívhat meg egy JavaScript-alkalmazás olyan API-t, amely hozzáférési jogkivonatokat igényel a Microsoft identity platform használatával.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ms.openlocfilehash: 2649d885d31bcd0fef403e7e4eddbc828fd17b93
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80050260"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa"></a>Rövid útmutató: Bejelentkezés a felhasználókközött, és kap egy hozzáférési jogkivonatot a JavaScript SPA

Ebben a rövid útmutatóban egy kódminta segítségével megtudhatja, hogy a JavaScript egyoldalas alkalmazások (SPA) hogyan jelentkezhetnek be a személyes fiókok, munkahelyi fiókok és iskolai fiókok felhasználói. A JavaScript SPA is kaphat egy hozzáférési jogkivonatot a Microsoft Graph API vagy bármely webes API hívásához. (Lásd: [Hogyan működik a minta](#how-the-sample-works) egy illusztrációhoz.)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio-kód](https://code.visualstudio.com/download) (projektfájlok szerkesztéséhez)


> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>A rövid útmutató alkalmazás regisztrálása és letöltése
> A rövid útmutató alkalmazás elindításához használja az alábbi lehetőségek egyikét.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség (Expressz): Regisztrálja és automatikusan konfigurálja az alkalmazást, majd töltse le a kódmintát
>
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
> 1. Ha a fiók hozzáférést biztosít egynél több bérlőhöz, válassza ki a jobb felső sarokban lévő fiókot, majd állítsa be a portálmunkamenetet a használni kívánt Azure Active Directory (Azure AD) bérlőre.
> 1. Nyissa meg az új [Azure Portal – Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) ablaktáblát.
> 1. Adja meg az alkalmazás nevét. 
> 1. A **Támogatott fióktípusok csoportban**válassza a Fiókok lehetőséget **bármely szervezeti címtárban és személyes Microsoft-fiókban.**
> 1. Kattintson a **Register** (Regisztrálás) elemre.
> 1. Kövesse az utasításokat az új alkalmazás letöltéséhez és automatikus konfigurálásához.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség (manuális): Regisztrálja és manuálisan konfigurálja az alkalmazást és a kódmintát
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
>
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
>
> 1. Ha a fiók hozzáférést biztosít egynél több bérlőhöz, válassza ki a fiókját a jobb felső sarokban, majd állítsa be a portálmunkamenetet a használni kívánt Azure AD-bérlőre.
> 1. Nyissa meg a Microsoft identity platform ot a [fejlesztőknek Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapon.
> 1. Válassza **az Új regisztráció lehetőséget.**
> 1. Ha megjelenik az **Alkalmazás regisztrálása** oldal, adjon nevet az alkalmazásnak.
> 1. A **Támogatott fióktípusok csoportban**válassza a Fiókok lehetőséget **bármely szervezeti címtárban és személyes Microsoft-fiókban.**
> 1. Kattintson a **Register** (Regisztrálás) elemre. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosító** értékét későbbi használatra.
> 1. Ehhez a rövid útmutatóhoz engedélyezni kell az [Implicit támogatási folyamatot.](v2-oauth2-implicit-grant-flow.md) A regisztrált alkalmazás bal oldali ablaktáblájában válassza a **Hitelesítés**lehetőséget.
> 1. A **Platformkonfigurációk csoportban**válassza **a Platform hozzáadása**lehetőséget. A bal oldalon megnyílik egy panel. Itt válassza ki a **Web Applications régiót.**
> 1. Még mindig a bal oldalon állítsa `http://localhost:3000/`az **Átirányítás URI** értékét . Ezután válassza **a Hozzáférési jogkivonat** és **az Azonosító jogkivonat lehetőséget.**
> 1. Válassza a **Konfigurálás** lehetőséget.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
> Ahhoz, hogy a kódmintát ebben a rövid `redirectUri` `http://localhost:3000/` útmutatóban, hozzá kell adnia egy as és implicit **támogatás**engedélyezése.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Hajtsa végre ezeket a módosításokat nekem]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-javascript/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-project"></a>2. lépés: A projekt letöltése

> [!div renderon="docs"]
> Ha a projektet webkiszolgálóval szeretné futtatni a Node.js fájlokkal, [töltse le az alapvető projektfájlokat.](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="portal"]
> A projekt futtatása webkiszolgálóval a Node.js használatával

> [!div renderon="portal" id="autoupdate" class="nextstepaction"]
> [A kódminta letöltése](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>3. lépés: A JavaScript-alkalmazás konfigurálása
>
> A *JavaScriptSPA* mappában írja be az *authConfig.js fájlt,* és állítsa be `clientID`a és `authority` az `redirectUri` értékeket a területen. `msalConfig`
>
> ```javascript
>
>  // Config object to be passed to Msal on creation
>  const msalConfig = {
>    auth: {
>      clientId: "Enter_the_Application_Id_Here",
>      authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>      redirectUri: "Enter_the_Redirect_Uri_Here",
>    },
>    cache: {
>      cacheLocation: "sessionStorage", // This configures where your cache will be stored
>      storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>    }
>  };  
>
>```

> [!div renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here

> [!div renderon="docs"]
>
> Az elemek magyarázata:
> - Enter_the_Application_Id_Here>a regisztrált alkalmazás **alkalmazásazonosítója.** * \<*
> - Enter_the_Cloud_Instance_Id_Here>az Azure-felhő példánya. * \<* A fő vagy globális Azure-felhőhöz egyszerűen írja be a beírást. *https://login.microsoftonline.com* **A nemzeti** felhők (például Kína), lásd: [Nemzeti felhők](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
> - Enter_the_Tenant_info_here>az alábbi beállítások egyikére van beállítva: * \<*
>    - Ha az alkalmazás támogatja a *fiókok ebben a szervezeti címtárban,* cserélje le ezt az értéket a **bérlői azonosító** vagy bérlő **neve** (például *contoso.microsoft.com).*
>    - Ha az alkalmazás *bármely szervezeti címtárban támogatja a fiókokat,* cserélje le ezt az értéket **szervezetekre.**
>    - Ha az alkalmazás támogatja *a szervezeti címtárban és a személyes Microsoft-fiókokban lévő fiókokat,* cserélje le ezt az értéket a **közös értékre.** Ha csak *a személyes Microsoft-fiókokra*szeretné korlátozni a támogatást, cserélje le ezt az értéket **a fogyasztókra**.
>
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítója**, a **címtár (bérlő) azonosítója** és a **támogatott fióktípusok** értékét az alkalmazás **Áttekintés** oldalán találja az Azure Portalon.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. lépés: Az alkalmazás konfigurálva van, és készen áll a futtatásra
> A projektet az alkalmazás tulajdonságainak értékeivel konfiguráltuk. 

> [!div renderon="docs"]
> 
> Ezután még ugyanabban a mappában, edit *graphConfig.js* fájlt, hogy állítsa be a `graphMeEndpoint` és `graphMeEndpoint` az `apiConfig` objektum.
> ```javascript
>   // Add here the endpoints for MS Graph API services you would like to use.
>   const graphConfig = {
>     graphMeEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me",
>     graphMailEndpoint: "Enter_the_Graph_Endpoint_Herev1.0/me/messages"
>   };
>
>   // Add here scopes for access token to be used at MS Graph API endpoints.
>   const tokenRequest = {
>       scopes: ["Mail.Read"]
>   };
> ```
>

> [!div renderon="docs"]
>
> Az elemek magyarázata:
> - Enter_the_Graph_Endpoint_Here>az a végpont, amely ellen API-hívások at. * \<* A fő vagy globális Microsoft Graph `https://graph.microsoft.com`API-szolgáltatáshoz egyszerűen írja be a be című t. További információ: [National cloud deployment](https://docs.microsoft.com/graph/deployments)
>
> #### <a name="step-4-run-the-project"></a>4. lépés: A projekt futtatása

A projekt futtatása webkiszolgálóval a [Node.js](https://nodejs.org/en/download/)használatával:

1. A kiszolgáló indításához futtassa a következő parancsot a projekt könyvtárából:
    ```batch
    npm install
    npm start
    ```
1. Nyisson meg egy `http://localhost:3000/`webböngészőt, és nyissa meg a megnyitását.

1. Válassza **a Bejelentkezés** lehetőséget a bejelentkezés elindításához, majd hívja meg a Microsoft Graph API-t.

Miután a böngésző betölti az alkalmazást, válassza **a Bejelentkezés**lehetőséget. Amikor először jelentkezik be, a rendszer kéri, hogy adja meg hozzájárulását ahhoz, hogy az alkalmazás hozzáférhessen a profiljához, és bejelentkezhessen. Miután sikeresen bejelentkezett, a felhasználói profil adatainak meg kell jelenniük az oldalon.

## <a name="more-information"></a>További információ

### <a name="how-the-sample-works"></a>A minta működése

![A mintaalkalmazás működése ebben a rövid útmutatóban](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal.js

Az MSAL-kódtár bejelentkezik a felhasználók, és kéri a jogkivonatokat, amelyek a Microsoft identity platform által védett API eléréséhez használt. A rövid útmutató *index.html* fájl a tárra mutató hivatkozást tartalmaz:

```html
<script type="text/javascript" src="https://alcdn.msftauth.net/lib/1.2.1/js/msal.js" integrity="sha384-9TV1245fz+BaI+VvCjMYL0YDMElLBwNS84v3mY57pXNOt6xcUYch2QLImaTahcOP" crossorigin="anonymous"></script>
```
> [!TIP]
> Az előző verziót lecserélheti a legújabb verzióra az [MSAL.js kiadások](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)alatt.

Ha telepítve van a Node.js fájl, a node.js csomagkezelőn (npm) keresztül töltheti le a legújabb verziót:

```batch
npm install msal
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

A rövid útmutató kódja azt is bemutatja, hogyan inicializálhatja az MSAL-könyvtárat:

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
    }
  };  

const myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Ahol  |  |
> |---------|---------|
> |`clientId`     | Az Azure Portalon regisztrált alkalmazás azonosítója.|
> |`authority`    | (Nem kötelező) A fióktípusokat támogató hatóság URL-címe, akonfigurációs szakaszban leírtak szerint. Az alapértelmezett `https://login.microsoftonline.com/common`szolgáltató a . |
> |`redirectUri`     | Az alkalmazásregisztráció konfigurált válasz/redirectUri. Ebben az `http://localhost:3000/`esetben, . |
> |`cacheLocation`  | (Nem kötelező) Beállítja a böngésző tárolóját az auth állapothoz. Az alapértelmezett a sessionStorage.   |
> |`storeAuthStateInCookie`  | (Nem kötelező) A könyvtár, amely tárolja a hitelesítési kérelem állapotát, amely a hitelesítési folyamatok érvényesítéséhez szükséges a böngésző cookie-kban. Ez a cookie az IE és edge böngészők számára van beállítva bizonyos [ismert problémák](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)enyhítésére. |

Az elérhető konfigurálható beállításokról az [Ügyfélalkalmazások inicializálása](msal-js-initializing-client-applications.md)című témakörben talál további információt.

### <a name="sign-in-users"></a>Bejelentkezés, felhasználók

A következő kódrészlet bemutatja, hogyan jelentkeztetési a felhasználókhoz:

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
> | `scopes`   | (Nem kötelező) Olyan hatóköröket tartalmaz, amelyek et bejelentkezéskor kérnek a felhasználó hozzájárulásához. Például `[ "user.read" ]` a Microsoft `[ "<Application ID URL>/scope" ]` Graph vagy az egyéni webes `api://<Application ID>/access_as_user`API-k (azaz. |

> [!TIP]
> Azt is megteheti, hogy `loginRedirect` a módszerrel az aktuális oldalt a bejelentkezési oldalra szeretné átirányítani előugró ablak helyett.

### <a name="request-tokens"></a>Jogkivonatok kérése

Az MSAL három módszert használ `acquireTokenRedirect` `acquireTokenPopup`a tokenek megszerzésére: , , és`acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `acquireTokenSilent` módszer kezeli a tokenek beszerzése és megújítása felhasználói beavatkozás nélkül. Miután `loginRedirect` `loginPopup` a vagy metódus végrehajtása `acquireTokenSilent` az első alkalommal, a módszer általánosan használt jogkivonatok, amelyek a védett erőforrások eléréséhez használt későbbi hívások. A tokenek kérelmezésére vagy megújítására irányuló hívások csendesen történik.

```javascript

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
> | `scopes`   | Tartalmazza az API hozzáférési jogkivonatában visszaadandó hatóköröket. Például `[ "mail.read" ]` a Microsoft `[ "<Application ID URL>/scope" ]` Graph vagy az egyéni webes `api://<Application ID>/access_as_user`API-k (azaz.|

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

Vannak olyan helyzetek, amikor a felhasználókat a Microsoft identity platform végpontjával való interakcióra kell kényszerítenie. Példa:
* Előfordulhat, hogy a felhasználóknak újra meg kell adniuk a hitelesítő adataikat, mert a jelszavuk lejárt.
* Az alkalmazás további erőforrás-hatókörökhöz való hozzáférést kér, amelyekhez a felhasználónak hozzá kell járulnia.
* Kétfaktoros hitelesítés szükséges.

A legtöbb alkalmazás szokásos ajánlott `acquireTokenSilent` mintája az, hogy először `acquireTokenPopup` hívja, majd elkapja a kivételt, majd hívja meg (vagy `acquireTokenRedirect`) az interaktív kérelem elindítását.

Az `acquireTokenPopup` eredmények hívása egy előugró ablakban a bejelentkezéshez. (Vagy `acquireTokenRedirect` eredmények a felhasználók átirányítása a Microsoft identity platform végpontjára.) Ebben az ablakban a felhasználóknak a hitelesítő adataik megerősítésével, a szükséges erőforrás hozad hozzájárulásával vagy a kétfaktoros hitelesítés befejezésével kell együttműködniük.

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
> Ez a rövid `loginRedirect` `acquireTokenRedirect` útmutató a Microsoft Internet Explorer programot használja, mert [ismert probléma](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) van az internetböngésző előugró ablakainak kezelésével kapcsolatban.

## <a name="next-steps"></a>További lépések

Az alkalmazás létrehozásáról a rövid útmutatóhoz részletes, részletes útmutatót a következő témakörökben talál:

> [!div class="nextstepaction"]
> [Oktatóanyag a bejelentkezéshez és az MS Graph hívásához](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

Az MSAL tártárjában dokumentációt, gyakori kérdéseket, problémákat és egyebeket talál, lásd:

> [!div class="nextstepaction"]
> [MSAL.js GitHub tárcsa](https://github.com/AzureAD/microsoft-authentication-library-for-js)

Segítsen nekünk a Microsoft identitásplatformjának fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdésű felmérés kitöltésével.

> [!div class="nextstepaction"]
> [Microsoft-identitásplatform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
