---
title: A Microsoft identity platform JavaScript rövid |} Az Azure
description: Ismerje meg, hogyan a JavaScript alkalmazások hívhatják a Microsoft identity platform hozzáférési jogkivonatok igénylő API.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 605206682cb70d430773cdbf9ff746eabf594103
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190845"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application-spa"></a>Gyors útmutató: A felhasználók és a JavaScript-egyoldalas alkalmazás (SPA) hozzáférési jogkivonat beszerzése

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ez a rövid útmutatóban megismerheti, hogyan használhat egy kódmintát, amely bemutatja, hogyan egy JavaScript egyoldalas alkalmazás (SPA) jelentkezzen be személyes, munkahelyi és iskolai fiókok és a Microsoft Graph API vagy minden olyan webes API hívása hozzáférési jogkivonatot kapjon a lesz.

![Ez a rövid útmutató által létrehozott mintaalkalmazás működését mutatja](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban a következő beállítás lesz szüksége:
* Egy node.js-kiszolgálóval, a projekt futtatása
    * [Node.js](https://nodejs.org/en/download/) telepítése
    * Telepítés [Visual Studio Code](https://code.visualstudio.com/download) a projektfájlok szerkesztése
* Futtassa a projektet a Visual Studio megoldás, telepítse a [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Regisztráljon, és letöltheti a rövid útmutató
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Express] [1. lehetőség: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuális] [2. lehetőség: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta
>
> 1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. Nyissa meg az új [az Azure portal - alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) ablaktáblán.
> 1. Adja meg az alkalmazás nevét, és kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
>
> 1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. Keresse meg a fejlesztők a Microsoft identity platform [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapot.
> 1. Válassza ki **új regisztrációs**.
> 1. Ha a **alkalmazás regisztrálása** lap, adja meg az alkalmazás nevét.
> 1. A **támogatott fióktípusok**válassza **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok**.
> 1. Válassza ki a **webes** platform alapján a **átirányítási URI-t** szakaszt, és állítsa az értékét `http://localhost:30662/`.
> 1. Miután végzett, válassza a **Regisztrálás** lehetőséget.  Az alkalmazás **áttekintése** lapon, jegyezze fel a **Alkalmazásazonosítót (ügyfél)** értéket.
> 1. Ez a rövid útmutatóhoz a [Implicit folyamat megadása](v2-oauth2-implicit-grant-flow.md) engedélyezni kell. A regisztrált alkalmazás bal oldali navigációs panelén válassza **hitelesítési**.
> 1. A **speciális beállítások**alatt **típusú Implicit engedélyezés**, mindkettő engedélyezéséhez **azonosító-jogkivonatokat** és **hozzáférési jogkivonatokat** jelölőnégyzeteket. Azonosító-jogkivonatokat és hozzáférési tokenek szükség, mivel ez az alkalmazás a felhasználók és a egy API-t kell.
> 1. Kattintson a **Mentés** gombra.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
> A kódminta működjön ez a rövid útmutató, meg kell adjon hozzá egy átirányítási URI-t, `http://localhost:30662/` , és engedélyezze **típusú Implicit engedélyezés**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Ezt a módosítást a számomra]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-javascript/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-project"></a>2. lépés: Töltse le a projekt

Alkalmas ezen beállítások valamelyikét is válassza ki a fejlesztői környezetbe.
* [Töltse le a core projektfájlok](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip), futtathat egy webkiszolgálót Node.js használatával. Nyissa meg a fájlokat, használja a-szerkesztő, például [Visual Studio Code](https://code.visualstudio.com/).

* (Nem kötelező) [Töltse le a Visual Studio-projekt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip), az IIS kiszolgáló futtatásához. Bontsa ki a zip-fájlt egy helyi mappába, például **C:\Azure-Samples**.



#### <a name="step-3-configure-your-javascript-app"></a>3. lépés: A JavaScript-alkalmazás konfigurálása

> [!div renderon="docs"]
> A mappában *JavaScriptSPA*, Szerkesztés `index.html` és állítsa be a `clientID` és `authority` értékei alapján `msalConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> A mappában *JavaScriptSPA*, Szerkesztés `index.html` , és cserélje le `msalConfig` együtt:

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

```
> [!div renderon="docs"]
>
> Az elemek magyarázata:
> - `Enter_the_Application_Id_here` – ez a regisztrált alkalmazáshoz tartozó **Alkalmazás (ügyfél) azonosítója** érték.
> - `Enter_the_Tenant_Info_Here` – az alábbi lehetőségek egyike lesz:
>   - Ha az alkalmazás **az adott szervezeti címtárban lévő fiókokat** támogatja, ezt az értéket a **Bérlőazonosítóra** vagy a **Bérlő nevére** cserélje le (például contoso.microsoft.com)
>   - Ha az alkalmazás **bármely szervezeti címtárban lévő fiókot** támogat, ezt az értéket az `organizations` értékre cserélje le
>   - Ha az alkalmazás támogatja a **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok**, cserélje le ezt az értéket `common`. A támogatási korlátozása *személyes Microsoft-fiókok csak*, cserélje le ezt az értéket `consumers`.
>
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítója**, a **címtár (bérlő) azonosítója** és a **támogatott fióktípusok** értékét az alkalmazás **Áttekintés** oldalán találja az Azure Portalon.
>

#### <a name="step-4-run-the-project"></a>4. lépés: A projekt futtatása

* Ha használ [Node.js](https://nodejs.org/en/download/):

    1. Futtassa a következő parancsot a projektmappából, indítsa el a kiszolgálót:

        ```batch
        npm install
        node server.js
        ```

    1. Nyisson meg egy webböngészőt, és navigáljon a `http://localhost:30662/`.
    1. Kattintson a **bejelentkezés** gombra kattintva indítsa el a bejelentkezési és majd a Microsoft Graph API meghívása.


* Ha használ [Visual Studio](https://visualstudio.microsoft.com/downloads/), mindenképpen jelölje ki a projekt megoldást, és nyomja le az **F5** a projekt futtatása.

Miután a böngésző betölti az alkalmazást, kattintson a **bejelentkezés**.  Az első alkalommal jelentkezik be, amely kéri, hogy Ön hozzájárul ahhoz, hogy az alkalmazás eléréséhez a profil és a bejelentkezéshez adja meg. A felhasználói profil adatait az oldalon megjelenített megtekintheti a sikeres bejelentkezés.

## <a name="more-information"></a>További információ

### <a name="msaljs"></a>*msal.js*

Az MSAL a könyvtárban, a felhasználók és a kérelem API-k a Microsoft identity platform által védett eléréséhez használt jogkivonatok aláírásához használt. Ez a rövid útmutató *index.html* tartalmaz egy hivatkozást a könyvtárhoz:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
```
> [!TIP]
> A fenti verzió lecserélheti a legújabb elérhető verzió alatt [MSAL.js kiadások](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


Azt is megteheti Ha a csomópont telepítve van, letöltheti a legújabb verziót az npm segítségével:

```batch
npm install msal
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

A rövid útmutató kódját is bemutatja, hogyan inicializálása a tár:

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

> |Ahol  |  |
> |---------|---------|
> |`ClientId`     |Az Azure Portalon regisztrált alkalmazás azonosítója|
> |`authority`    | (Nem kötelező) A szolgáltató URL-CÍMÉT a fenti konfigurációs szakaszban leírtak szerint fióktípusok támogatására. Az alapértelmezett szolgáltató van `https://login.microsoftonline.com/common`. |
> |`cacheLocation`  | (Nem kötelező) Ez a beállítás a hitelesítési állapot böngésző tárolóhelyét. Az alapértelmezett érték sessionStorage.   |
> |`storeAuthStateInCookie`  | (Nem kötelező) A könyvtárban tárolja a hitelesítési kérelem állapotának a hitelesítési folyamatok, a böngésző cookie-érvényesítés szükséges. Ez egyes Internet Explorer és a Microsoft Edge böngészőhöz értéke [ismert problémák](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

 Tekintse meg a [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options) elérhető konfigurálható lehetőségekkel kapcsolatos részletekért.

### <a name="sign-in-users"></a>A felhasználók

A következő kódrészletet bemutatja, hogyan a felhasználók:

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

> |Ahol  |  |
> |---------|---------|
> | `scopes`   | (Nem kötelező) A kért felhasználói beleegyezés bejelentkezési időpontban hatókörök tartalmazza. Például `[ "user.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni Web API-k (azaz `api://<Application ID>/access_as_user` ). |

> [!TIP]
> Azt is megteheti, előfordulhat, hogy szeretné használni a `loginRedirect` metódus az aktuális oldal átirányítása a bejelentkezési oldal helyett egy másik előugró ablak.

### <a name="request-tokens"></a>Jogkivonatok kérelmezésére

Az MSAL jogkivonatok beszerzésére használható három módszer van: `acquireTokenRedirect`, `acquireTokenPopup` és `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `acquireTokenSilent` metódus kezeli a token beszerzését és -megújítás, felhasználói beavatkozás nélkül. Miután a `loginRedirect` vagy `loginPopup` metódus végrehajtása az első alkalommal `acquireTokenSilent` későbbi hívások védett erőforrások eléréséhez használt tokenek beszerzése érdekében a gyakran használt módszer. Hívások újítsa meg a tokenek vagy kérheti a beavatkozás nélkül történik.

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

> |Ahol  |  |
> |---------|---------|
> | `scopes`   | A hozzáférési jogkivonatot adott vissza API-hoz, a kért hatóköröket tartalmazza. Például `[ "user.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni Web API-k (azaz `api://<Application ID>/access_as_user`).|

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

Vannak helyzetek, ahol együttműködhet a Microsoft identity platform végpont a felhasználókat kell. Példa:
* Felhasználók előfordulhat, hogy meg kell adnia a hitelesítő adataikat, mert lejárt
* Az alkalmazás további erőforrás hatókörhöz, amely a felhasználónak van szüksége, hogy engedélyt adjanak az access
* Kétfaktoros hitelesítésre van szükség

A legtöbb alkalmazás a szokásos javasolt minta, hogy a hívás `acquireTokenSilent` először a findlogin majd, és ezután hívja meg `acquireTokenPopup` (vagy `acquireTokenRedirect`) egy interaktív kérést.

Hívása a `acquireTokenPopup` bejelentkezni egy másik előugró ablak eredményez (vagy `acquireTokenRedirect` eredményez a felhasználók átirányítása a Microsoft identity platform végpont) felhasználók kell interaktív vagy erősítse meg a hitelesítő adataikat, ha engedélyezi a hogy a szükséges erőforrás vagy a kéttényezős hitelesítés elvégzése.

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
> Ebben a rövid útmutatóban használt a `loginRedirect` és `acquireTokenRedirect` módszerek, ha a használt böngésző oka az, hogy az Internet Explorer egy [ismert hiba](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) kapcsolatos kezelési előugró Windows Internet Explorer böngészőben.

## <a name="next-steps"></a>További lépések

Részletesebb lépésenkénti útmutató az ebben a rövid útmutatóban az alkalmazás létrehozása próbálja ki az alábbi JavaScript-oktatóanyag.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Ismerje meg, ez a rövid útmutató az alkalmazás létrehozásának lépéseit

> [!div class="nextstepaction"]
> [Jelentkezzen be, és hívja az MS Graph oktatóanyag](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Keresse meg a MSAL-adattárat a dokumentáció, GYIK, problémák és egyéb

> [!div class="nextstepaction"]
> [MSAL.js GitHub-adattár](https://github.com/AzureAD/microsoft-authentication-library-for-js)
