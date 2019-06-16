---
title: A Microsoft identity platform JavaScript rövid útmutató – Azure
description: Ismerje meg, hogyan a JavaScript alkalmazások hívhatják a Microsoft identity platform használatával hozzáférési jogkivonatok igénylő API.
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
ms.openlocfilehash: 305479c8872883e434731b5062b408f97f68cc43
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67055921"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-single-page-application"></a>Gyors útmutató: A felhasználók és a JavaScript-egyoldalas alkalmazás hozzáférési jogkivonat beszerzése

Ez a rövid útmutatóban megismerheti, hogyan használhat egy kódmintát, amely bemutatja, hogyan egy JavaScript egyoldalas alkalmazás (SPA) személyes fiókoknak, munkahelyi és iskolai fiókokat a felhasználók bejelentkezhetnek a. Egy JavaScript SPA-ALKALMAZÁSOKBA is beszerezheti a Microsoft Graph API vagy minden olyan webes API hívása egy hozzáférési jogkivonatot.

![Ebben a rövid útmutatóban a mintaalkalmazás működése](media/quickstart-v2-javascript/javascriptspa-intro.svg)

## <a name="prerequisites"></a>Előfeltételek

Ehhez a rövid útmutatóhoz a következő beállítás:
* Egy Node.js-kiszolgáló futtatja a projektet, töltse le és telepítse [Node.js](https://nodejs.org/en/download/).
* Szerkessze a projektfájlokat, töltse le és telepítse [Visual Studio Code](https://code.visualstudio.com/download).
* Futtassa a projektet a Visual Studio megoldás, töltse le és telepítse [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Regisztráljon, és letöltheti a rövid útmutató
> A rövid útmutató alkalmazás indításához használja az alábbi lehetőségek közül választhat.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>(Express) 1. lehetőség: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta
>
> 1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiók használatával.
> 1. Ha a fiók figyelmébe, amely egynél több bérlő számára, jelölje be a fiók jobb felső sarokban, és állítsa a portál munkamenet, az Azure Active Directory (Azure AD) bérlő használni kívánt.
> 1. Nyissa meg az új [az Azure portal - alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) ablaktáblán.
> 1. Adja meg az alkalmazás nevét, és válassza ki **regisztrálása**.
> 1. Kövesse az utasításokat, töltse le, és automatikusan konfigurálja az új alkalmazás.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>(Manuális) 2. lehetőség: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta
>
> #### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása
>
> 1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiók használatával.
>
> 1. Ha a fiók figyelmébe, amely egynél több bérlő számára, válassza ki a fiókját, jobb felső sarokban, majd állítsa be a portál munkamenet az Azure AD-bérlőhöz használni kívánt.
> 1. Nyissa meg a fejlesztők a Microsoft identity platform [alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapot.
> 1. Válassza ki **új regisztrációs**.
> 1. Ha a **alkalmazás regisztrálása** lap, adja meg az alkalmazás nevét.
> 1. A **támogatott fióktípusok**válassza **fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok**.
> 1. Alatt a **átirányítási URI-t** szakaszban, a legördülő listában válassza ki a **webes** platformot, majd állítsa az értékét, és `http://localhost:30662/`.
> 1. Kattintson a **Register** (Regisztrálás) elemre. Az alkalmazás **áttekintése** lapon, vegye figyelembe a **Alkalmazásazonosítót (ügyfél)** értéket későbbi használatra.
> 1. Ez a rövid útmutatóhoz a [Implicit folyamat megadása](v2-oauth2-implicit-grant-flow.md) engedélyezni kell. A regisztrált alkalmazás bal oldali panelén válassza **hitelesítési**.
> 1. Az a **speciális beállítások** szakasz **típusú Implicit engedélyezés**, jelölje be a **azonosító-jogkivonatokat** és **hozzáférési jogkivonatokat** jelölőnégyzeteket. Azonosító-jogkivonatokat és hozzáférési tokenek szükség, mert ez az alkalmazás a felhasználók és a egy API-t.
> 1. Válassza a panel tetején lévő **mentése**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1\. lépés: Az alkalmazás konfigurálása az Azure Portalon
> A kódminta működjön ez a rövid útmutató, meg kell adjon hozzá egy átirányítási URI-t, `http://localhost:30662/` , és engedélyezze **típusú Implicit engedélyezés**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Ezt a módosítást a számomra]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-javascript/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-project"></a>2\. lépés: Töltse le a projekt

Válassza ki azt a beállítást, amely lehetővé teszi a fejlesztői környezetbe:

* A projekt futtatása a webkiszolgáló Node.js, használatával [töltse le a core projektfájlok](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip). Nyissa meg a fájlokat, használjon egy szerkesztőben például [Visual Studio Code](https://code.visualstudio.com/).

* (Nem kötelező) Futtassa a projektet az IIS-kiszolgálóval való [töltse le a Visual Studio-projekt](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip). Bontsa ki a zip-fájlt egy helyi mappába (például *C:\Azure-Samples*).



#### <a name="step-3-configure-your-javascript-app"></a>3\. lépés: A JavaScript-alkalmazás konfigurálása

> [!div renderon="docs"]
> Az a *JavaScriptSPA* mappában szerkesztése *index.html*, és állítsa be a `clientID` és `authority` értékei alapján `msalConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> Az a *JavaScriptSPA* mappában szerkesztése *index.html*, és cserélje le `msalConfig` a következő kóddal:

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
> [!div renderon="docs"]
>
> Az elemek magyarázata:
> - *\<Enter_the_Application_Id_here >* van a **Alkalmazásazonosítót (ügyfél)** a regisztrált alkalmazás.
> - *\<Enter_the_Tenant_info_here >* értéke a következők egyikét:
>    - Ha az alkalmazás támogatja a *ebben a könyvtárban szervezeti fiókok*, cserélje le ezt az értéket a **Bérlőazonosító** vagy **bérlőnevet** (például  *contoso.microsoft.com*).
>    - Ha az alkalmazás támogatja a *bármely szervezeti directory fiókok*, cserélje le ezt az értéket **szervezetek**.
>    - Ha az alkalmazás támogatja a *fiókok minden olyan szervezeti directory és személyes Microsoft-fiókok*, cserélje le ezt az értéket **közös**. A támogatási korlátozása *személyes Microsoft-fiókok csak*, cserélje le ezt az értéket **fogyasztók**.
>
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítója**, a **címtár (bérlő) azonosítója** és a **támogatott fióktípusok** értékét az alkalmazás **Áttekintés** oldalán találja az Azure Portalon.
>

#### <a name="step-4-run-the-project"></a>4\. lépés: A projekt futtatása

* Ha használ [Node.js](https://nodejs.org/en/download/):

    1. Indítsa el a kiszolgálót, hogy futtassa a következő parancsot a projektmappából:

        ```batch
        npm install
        node server.js
        ```

    1. Nyisson meg egy webböngészőt, majd `http://localhost:30662/`.
    1. Válassza ki **bejelentkezés** elindításához a bejelentkezés, és ezután hívja meg a Microsoft Graph API-t.


* Ha használ [Visual Studio](https://visualstudio.microsoft.com/downloads/), jelölje ki a projekt megoldást, és válassza le az F5 billentyűt a projekt futtatásához.

Miután a böngésző betölti az alkalmazást, válassza ki **bejelentkezés**. Az első alkalommal jelentkezik be, amely kéri, hogy Ön hozzájárul ahhoz, hogy az alkalmazás eléréséhez a profil és a bejelentkezéshez adja meg. Miután jelentkezett sikeres, a felhasználói profil adatait az oldalon kell jeleníthető meg.

## <a name="more-information"></a>További információ

### <a name="msaljs"></a>msal.js

A MSAL erőforrástár bejelentkezteti a felhasználókat, és kéri a jogkivonatokat, amelyek egy API-t a Microsoft identity platform által védett eléréséhez. Ez a rövid útmutató *index.html* fájl tartalmaz egy hivatkozást a könyvtárhoz:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.min.js"></script>
```
> [!TIP]
> Az előző verzióját a legújabb elérhető verzió alatt lecserélheti [MSAL.js kiadások](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).


Azt is megteheti Ha telepítve van a Node.js, letöltheti a legújabb verzió keresztül Node.js Package Managerrel (npm):

```batch
npm install msal
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

A rövid útmutató kódját is bemutatja, hogyan inicializálni az MSAL könyvtár:

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
> |`ClientId`     | Az Alkalmazásazonosító az alkalmazás, amely regisztrálva van az Azure Portalon.|
> |`authority`    | (Nem kötelező) A szolgáltató URL-CÍMÉT, amely támogatja a, a konfigurációs szakaszban korábban leírtak szerint. Az alapértelmezett szolgáltató van `https://login.microsoftonline.com/common`. |
> |`cacheLocation`  | (Nem kötelező) A böngésző tárolót, a hitelesítés állapotának beállítása. Az alapértelmezett érték sessionStorage.   |
> |`storeAuthStateInCookie`  | (Nem kötelező) A könyvtár, amely tárolja a hitelesítési kérelem állapota a hitelesítési folyamatok, a böngésző cookie-érvényesítés szükséges. A cookie-k van állítva az Internet Explorer és a Microsoft Edge böngésző egyes [ismert problémák](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues). |

Elérhető konfigurálható lehetőségekkel kapcsolatos további információkért lásd: [ügyfélalkalmazások inicializálása](msal-js-initializing-client-applications.md).

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
> | `scopes`   | (Nem kötelező) Bejelentkezési időpontban felhasználói beleegyezést kérő hatóköröket tartalmazza. Például `[ "user.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni Web API-k (azaz `api://<Application ID>/access_as_user`). |

> [!TIP]
> Azt is megteheti, hogy érdemes használni a `loginRedirect` metódus az aktuális oldal átirányítása a bejelentkezési oldal helyett egy másik előugró ablak.

### <a name="request-tokens"></a>Jogkivonatok kérelmezésére

Az MSAL három módszer használatával szerzi be a jogkivonatokat: `acquireTokenRedirect`, `acquireTokenPopup`, és `acquireTokenSilent`

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
* Írja be újra a hitelesítő adatait, mert lejárt szükség lehet felhasználók.
* Az alkalmazás további erőforrás hatókörök, amelyet a felhasználó beleegyezik abba, hogy a hozzáférést.
* A kétfaktoros hitelesítés kötelező megadni.

A legtöbb alkalmazás a szokásos javasolt minta, hogy a hívás `acquireTokenSilent` először, majd a findlogin, és ezután hívja meg `acquireTokenPopup` (vagy `acquireTokenRedirect`) egy interaktív kérést.

Hívása a `acquireTokenPopup` bejelentkezési előugró ablakban eredményez. (Vagy `acquireTokenRedirect` felhasználók átirányítása a Microsoft identity platform végpont eredményez.) Ezt az ablakot, a felhasználóknak kell megerősítése hitelesítő adataikat, engedélyezi a hogy a szükséges erőforrás vagy a kétfaktoros hitelesítés elvégzése kommunikálhatnak egymással.

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
> Ebben a rövid útmutatóban használja a `loginRedirect` és `acquireTokenRedirect` módszereit, valamint a Microsoft Internet Explorer miatt egy [ismert hiba](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) kapcsolódó kezelésekor a helyi menü windows Internet Explorer által.

## <a name="next-steps"></a>További lépések

Részletesebb lépésenkénti útmutató az ebben a rövid útmutatóban az alkalmazás létrehozása lásd:

> [!div class="nextstepaction"]
> [Jelentkezzen be, és hívja az MS Graph oktatóanyag](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

Keresse meg a MSAL-adattárat a dokumentáció, GYIK, problémák és további információkért lásd:

> [!div class="nextstepaction"]
> [MSAL.js GitHub-adattár](https://github.com/AzureAD/microsoft-authentication-library-for-js)
