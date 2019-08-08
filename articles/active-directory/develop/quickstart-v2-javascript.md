---
title: Microsoft Identity platform JavaScript rövid útmutató – Azure
description: Ismerje meg, hogy a JavaScript-alkalmazások hogyan hívhatnak meg olyan API-t, amely hozzáférési jogkivonatokat igényel a Microsoft Identity platform használatával.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: 59b5ddbff646104b3d4a35c26c1ecf3968dea31d
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852909"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application"></a>Gyors útmutató: Bejelentkezés a felhasználókba és hozzáférési jogkivonat beszerzése egy JavaScript egyoldalas alkalmazásból

Ebből a rövid útmutatóból megtudhatja, hogyan használhat egy kód mintát, amely bemutatja, hogyan jelentkezhet be a JavaScript egyoldalas alkalmazás (SPA) a személyes fiókok, a munkahelyi fiókok és az iskolai fiókok felhasználói számára. A JavaScript SPA a Microsoft Graph API vagy bármely webes API meghívására is kérhet hozzáférési jogkivonatot.

![A jelen rövid útmutatóban szereplő minta alkalmazás működése](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következő beállítás szükséges:
* A projekt Node. js-kiszolgálóval való futtatásához töltse le és telepítse a [Node. js](https://nodejs.org/en/download/)-t.
* A projektfájlok szerkesztéséhez töltse le és telepítse a [Visual Studio Code](https://code.visualstudio.com/download)-ot.
* A projekt Visual Studio-megoldásként való futtatásához töltse le és telepítse a [Visual studio 2019](https://visualstudio.microsoft.com/downloads/)alkalmazást.

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>A gyors üzembe helyezési alkalmazás regisztrálása és letöltése
> A gyors üzembe helyezési alkalmazás indításához használja az alábbi lehetőségek egyikét.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1\. lehetőség (expressz): Regisztráljon és automatikusan konfigurálja az alkalmazást, majd töltse le a kód mintáját
>
> 1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
> 1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókot a jobb felső sarokban, majd állítsa be a portál munkamenetét a használni kívánt Azure Active Directory (Azure AD) bérlőre.
> 1. Nyissa meg az új [Azure Portal-Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) ablaktáblát.
> 1. Adja meg az alkalmazás nevét, és válassza a **regisztráció**lehetőséget.
> 1. Az új alkalmazás letöltéséhez és automatikus konfigurálásához kövesse az utasításokat.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>2\. lehetőség (manuális): Alkalmazás-és kód-minta regisztrálása és manuális konfigurálása
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
> 1. Az **átirányítási URI** szakasz legördülő listájában válassza ki a webplatformot , majd állítsa be `http://localhost:30662/`az értéket a következőre:.
> 1. Kattintson a **Register** (Regisztrálás) elemre. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét későbbi használatra.
> 1. Ez a rövid útmutató az [implicit engedélyezési folyamat](v2-oauth2-implicit-grant-flow.md) engedélyezését igényli. A regisztrált alkalmazás bal oldali ablaktábláján válassza a **hitelesítés**lehetőséget.
> 1. A **Speciális beállítások** szakasz **implicit támogatás**területén jelölje be az **azonosító** jogkivonatok és **hozzáférési tokenek** jelölőnégyzetet. AZONOSÍTÓ jogkivonatok és hozzáférési tokenek szükségesek, mert az alkalmazásnak be kell jelentkeznie a felhasználókba, és hívnia kell egy API-t.
> 1. A panel tetején kattintson a **Mentés**gombra.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1\. lépés: Az alkalmazás konfigurálása a Azure Portalban
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, hozzá kell adnia egy átirányítási URI `http://localhost:30662/` -t, és engedélyeznie kell az **implicit engedélyezést**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-javascript/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-project"></a>2\. lépés: A projekt letöltése

Válassza ki a fejlesztési környezetének megfelelő lehetőséget:

* Ha a projektet a Node. js használatával webkiszolgálóval szeretné futtatni, [töltse le az alapprojekt fájljait](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip). A fájlok megnyitásához használjon egy szerkesztőt, például a [Visual Studio Code](https://code.visualstudio.com/)-ot.

* Választható A projekt IIS-kiszolgálóval való futtatásához [töltse le a Visual Studio-projektet](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip). Bontsa ki a zip-fájlt egy helyi mappába (például *C:\Azure-Samples*).



#### <a name="step-3-configure-your-javascript-app"></a>3\. lépés: A JavaScript-alkalmazás konfigurálása

> [!div renderon="docs"]
> A *JavaScriptSPA* mappában szerkessze az *index. html*fájlt, és állítsa `clientID` be `authority` a és `msalConfig`a értékeket a alatt.

> [!div class="sxs-lookup" renderon="portal"]
> A *JavaScriptSPA* mappában szerkessze az *index. html*fájlt, és `msalConfig` cserélje le a következő kódra:

```javascript
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_info_here"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

```
> [!div renderon="portal"]
> > [!NOTE]
> > Ez a rövid útmutató támogatja a Enter_the_Supported_Account_Info_Here.


> [!div renderon="docs"]
>
> Az elemek magyarázata:
> - A Enter_the_Application_Id_here > a regisztrált alkalmazáshoz tartozó **alkalmazás (ügyfél) azonosítója** .  *\<*
> - A Enter_the_Tenant_info_here > az alábbi lehetőségek egyikére van beállítva:  *\<*
>    - Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a **bérlői azonosítóra** vagy a **bérlő nevére** (például *contoso.microsoft.com*).
>    - Ha az alkalmazás *minden szervezeti címtárban*támogatja a fiókokat, cserélje leezt az értéket szervezetekkel.
>    - Ha az alkalmazás *minden szervezeti címtárban és személyes Microsoft-fiókban is*támogatja a fiókokat, cserélje le ezt az értéket közösre. Ha *csak a személyes Microsoft-fiókok*támogatását szeretné korlátozni, cserélje le ezt az értéket a **fogyasztókkal**.
>
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítója**, a **címtár (bérlő) azonosítója** és a **támogatott fióktípusok** értékét az alkalmazás **Áttekintés** oldalán találja az Azure Portalon.
>

#### <a name="step-4-run-the-project"></a>4\. lépés: A projekt futtatása

* Ha a [Node. js](https://nodejs.org/en/download/)-t használja:

    1. A kiszolgáló elindításához futtassa a következő parancsot a projekt könyvtárából:

        ```batch
        npm install
        node server.js
        ```

    1. Nyisson meg egy webböngészőt, `http://localhost:30662/`és lépjen a következőre:.
    1. A bejelentkezés elindításához válassza a **Bejelentkezés** lehetőséget, majd hívja meg Microsoft Graph API-t.


* Ha a [Visual studiót](https://visualstudio.microsoft.com/downloads/)használja, válassza ki a Project megoldást, majd a projekt futtatásához nyomja le az F5 billentyűt.

Ha a böngésző betölti az alkalmazást, válassza a **Bejelentkezés**lehetőséget. Amikor először jelentkezik be, a rendszer arra kéri, hogy adja meg a beleegyezik, hogy engedélyezze az alkalmazásnak a profil elérését és a bejelentkezést. Miután sikeresen bejelentkezett, a felhasználói profil adatai megjelennek az oldalon.

## <a name="more-information"></a>További információ

### <a name="msaljs"></a>msal.js

A MSAL-könyvtár bejelentkezik a felhasználók számára, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kéri. A gyors útmutató *index. html* fájlja a könyvtárra mutató hivatkozást tartalmaz:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
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
var msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
    },
    cache: {
        cacheLocation: "localStorage",
        storeAuthStateInCookie: true
    }
};

var myMSALObj = new Msal.UserAgentApplication(msalConfig);
```

> |Ahol (a(z)  |  |
> |---------|---------|
> |`ClientId`     | A Azure Portalban regisztrált alkalmazás alkalmazás-azonosítója.|
> |`authority`    | Választható A fiók típusát támogató szolgáltatói URL-cím, a konfiguráció szakaszban korábban leírtak szerint. Az alapértelmezett szolgáltató `https://login.microsoftonline.com/common`:. |
> |`cacheLocation`  | Választható Beállítja a böngésző tárolóját a hitelesítési állapothoz. Az alapértelmezett érték a sessionStorage.   |
> |`storeAuthStateInCookie`  | Választható A böngésző cookie-jai hitelesítési folyamatainak érvényesítéséhez szükséges hitelesítési kérési állapotot tároló könyvtár. Ez a cookie az IE és a Edge böngészők számára van beállítva, hogy bizonyos [ismert problémákkal](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues)enyhíthető legyen. |

További információ az elérhető konfigurálható lehetőségekről: [ügyfélalkalmazások inicializálása](msal-js-initializing-client-applications.md).

### <a name="sign-in-users"></a>Bejelentkezési felhasználók

A következő kódrészlet bemutatja, hogyan jelentkezhet be a felhasználókba:

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
    //Login Success callback code here
}).catch(function (error) {
    console.log(error);
});
```

> |Ahol (a(z)  |  |
> |---------|---------|
> | `scopes`   | Választható Olyan hatóköröket tartalmaz, amelyeket a rendszer a bejelentkezési időben a felhasználói beleküldéshez kér. Például `[ "user.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni webes `api://<Application ID>/access_as_user`API-khoz (azaz). |

> [!TIP]
> Azt is megteheti, hogy a `loginRedirect` metódus használatával átirányítja az aktuális oldalt a bejelentkezési lapra a felugró ablak helyett.

### <a name="request-tokens"></a>Kérelmek jogkivonatai

A MSAL három módszert használ a tokenek beszerzésére: `acquireTokenRedirect`, és `acquireTokenPopup``acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `acquireTokenSilent` metódus felhasználói beavatkozás nélkül kezeli a tokenek beszerzését és megújítását. `loginRedirect` A vagy `loginPopup` a metódus első `acquireTokenSilent` futtatása után a metódus általában a védett erőforrásokhoz való hozzáféréshez használt jogkivonatok beszerzésére szolgál a további hívásokhoz. A jogkivonatok kérése vagy megújítása csendesen történik.

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

> |Ahol (a(z)  |  |
> |---------|---------|
> | `scopes`   | Az API hozzáférési jogkivonatában visszaadott hatóköröket tartalmaz. Például `[ "user.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni webes `api://<Application ID>/access_as_user`API-khoz (azaz).|

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

Vannak olyan helyzetek, amikor kényszeríteni kell a felhasználókat, hogy együttműködjön a Microsoft Identity platform-végponttal. Példa:
* Előfordulhat, hogy a felhasználóknak újra meg kell adniuk a hitelesítő adataikat, mert a jelszavuk lejárt.
* Az alkalmazása olyan további erőforrás-hatókörökhöz kér hozzáférést, amelyeknek a felhasználónak hozzá kell járulnia.
* Kétfaktoros hitelesítés szükséges.

A legtöbb alkalmazás esetében a szokásos ajánlott minta az első `acquireTokenSilent` meghívása, majd a kivétel elfogása `acquireTokenPopup` , majd `acquireTokenRedirect`a (vagy) hívása egy interaktív kérelem elindításához.

Az `acquireTokenPopup` eredmények felugró ablakban való meghívása a bejelentkezéshez. (Vagy `acquireTokenRedirect` a felhasználók átirányítása a Microsoft Identity platform végpontba.) Ebben az ablakban a felhasználóknak kapcsolatba kell lépniük a hitelesítő adataik jóváhagyásával, a szükséges erőforrás megadásával vagy a kétfaktoros hitelesítés végrehajtásával.

```javascript
var requestObj = {
    scopes: ["user.read"]
};

myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
    // Callback code here
    console.log(tokenResponse.accessToken);
}).catch(function (error) {
    console.log(error);
});
```

> [!NOTE]
> Ez a rövid útmutató `loginRedirect` a `acquireTokenRedirect` és a metódusokat használja a Microsoft Internet Explorerrel, mert az Internet Explorer felugró ablakok kezelésére vonatkozó [ismert probléma](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) miatt.

## <a name="next-steps"></a>További lépések

A rövid útmutatóhoz tartozó alkalmazás létrehozásával kapcsolatos részletes útmutatót a következő témakörben talál:

> [!div class="nextstepaction"]
> [Oktatóanyag a bejelentkezéshez és az MS Graph meghívásához](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

A MSAL-tárházban a dokumentáció, a gyakori kérdések és a problémák megtekintéséhez lásd:

> [!div class="nextstepaction"]
> [MSAL. js GitHub-tárház](https://github.com/AzureAD/microsoft-authentication-library-for-js)

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával.

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)