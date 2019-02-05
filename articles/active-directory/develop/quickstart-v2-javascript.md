---
title: Azure AD v2 JavaScript rövid útmutató |} A Microsoft Docs
description: Ismerje meg, hogyan a JavaScript-alkalmazások hívhatják egy API, amely szerint az Azure Active Directory v2.0-végpont hozzáférési jogkivonatok szükséges
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: c10666b72902451a278837696a1ab58daa008be5
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55696115"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>Gyors útmutató: A felhasználók és a egy JavaScript-alkalmazását a hozzáférési jogkivonat beszerzése

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ebben a rövid útmutatóban fog megtudhatja, hogyan használhat egy kódmintát, amely bemutatja, hogyan egyoldalas alkalmazás (SPA) is jelentkezzen be a személyes fiókokat, munkahelyi és iskolai fiókok, a Microsoft Graph API vagy minden olyan webes API hívása hozzáférési jogkivonatot kapjon a JavaScript.

![A rövid útmutató által létrehozott mintaalkalmazás működése](media/quickstart-v2-javascript/javascriptspa-intro.png)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>Regisztráljon, és letöltheti a rövid útmutató
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Express] [1. lehetőség: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Manuális] [2. lehetőség: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Option 1: Regisztráció és az automatikus állítsa be alkalmazását, és töltse le a kódminta
>
> 1. Nyissa meg az [Azure Portal – Alkalmazásregisztráció (előzetes verzió)](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) szakaszt.
> 1. Adja meg az alkalmazás nevét, és kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Option 2: Regisztráljon, és manuális konfigurálása az alkalmazás és a kód a minta
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
>
> 1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) kell regisztrálni egy alkalmazást.
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. A bal oldali navigációs panelen válassza az **Azure Active Directory** szolgáltatást, majd az **Alkalmazásregisztrációk (előzetes verzió) > Új alkalmazás regisztrálása** lehetőséget.
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
* [A core projektfájlok - webkiszolgáló, például a Node.js letöltése](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
* [A Visual Studio-projekt letöltése](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

Bontsa ki a zip-fájlt egy helyi mappába, például **C:\Azure-Samples**.

#### <a name="step-3-configure-your-javascript-app"></a>3. lépés: A JavaScript-alkalmazás konfigurálása

> [!div renderon="docs"]
> Szerkesztés `index.html` és állítsa be a `clientID` és `authority` értékei alapján `applicationConfig`.

> [!div class="sxs-lookup" renderon="portal"]
> Szerkesztés `index.html` , és cserélje le `applicationConfig` együtt:

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
> [!div renderon="docs"]
>
> Az elemek magyarázata:
> - `Enter_the_Application_Id_here` – ez a regisztrált alkalmazáshoz tartozó **Alkalmazás (ügyfél) azonosítója** érték.
> - `Enter_the_Tenant_Info_Here` – az alábbi lehetőségek egyike lesz:
>   - Ha az alkalmazás **az adott szervezeti címtárban lévő fiókokat** támogatja, ezt az értéket a **Bérlőazonosítóra** vagy a **Bérlő nevére** cserélje le (például contoso.microsoft.com)
>   - Ha az alkalmazás **bármely szervezeti címtárban lévő fiókot** támogat, ezt az értéket az `organizations` értékre cserélje le
>   - Ha az alkalmazás **bármely szervezeti címtárban lévő fiókot és a személyes Microsoft-fiókokat** támogatja, ezt az értéket a `common` értékre cserélje le
>
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítója**, a **címtár (bérlő) azonosítója** és a **támogatott fióktípusok** értékét az alkalmazás **Áttekintés** oldalán találja az Azure Portalon.

> [!NOTE]
> A kiszolgáló port 30662 figyelésére van konfigurálva a *server.js* fájlt [Node.js](https://nodejs.org/en/download/) projekt és a *.csproj* fájlt [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)projekt.
>

#### <a name="step-4-run-the-project"></a>4. lépés: A projekt futtatása

* Node.js használata:

    1. Futtassa a következő parancsot a projektmappából, indítsa el a kiszolgálót:

        ```batch
        npm install
        node server.js
        ```

    1. Nyisson meg egy webböngészőt, és navigáljon a `http://localhost:30662/`.
    1. Kattintson a **bejelentkezés** gombra kattintva indítsa el a bejelentkezési és majd a Microsoft Graph API meghívása.


* Ha a Visual Studio használata esetén ügyeljen arra, hogy válassza ki a projekt megoldás, és nyomja le az **F5** , futtassa a projektet.

## <a name="more-information"></a>További információ

### <a name="msaljs"></a>*msal.js*

Az MSAL a könyvtárban, a felhasználók és a kérelem API-k a Microsoft Azure Active Directory (Azure AD) által védett eléréséhez használt jogkivonatok aláírásához használt. Ez a rövid útmutató *index.html* tartalmaz egy hivatkozást a könyvtárhoz:

```html
<script src="https://secure.aadcdn.microsoftonline-p.com/lib/0.2.3/js/msal.min.js"></script>
```

Azt is megteheti Ha csomópont telepítve, letöltheti az npm segítségével:

```batch
npm install msal
```

### <a name="msal-initialization"></a>Az MSAL inicializálása

A rövid útmutató kódját is bemutatja, hogyan inicializálása a tár:

```javascript
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, applicationConfig.authority, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
```

> |Ahol  |  |
> |---------|---------|
> |`ClientId`     |Az Azure Portalon regisztrált alkalmazás azonosítója|
> |`authority`    |A szolgáltató URL-címe. Átmenő *null* állítja be az alapértelmezett szolgáltató `https://login.microsoftonline.com/common`. Ha az alkalmazás egybérlős (csak egy címtárban célcsoport-kezelési fiókok), ezt az értéket `https://login.microsoftonline.com/<tenant name or ID>`|
> |`tokenReceivedCallback`| Visszahívási metódus nevű után térjen vissza az alkalmazás átirányítja a felhasználókat a hitelesítés. Itt `acquireTokenRedirectCallBack` átadott. Ez a NULL értékű, ha loginPopup használja.|
> |`options`  |Választható paraméterek: gyűjteménye. Ebben az esetben `storeAuthStateInCookie` és `cacheLocation` opcionális konfigurációs van. Tekintse meg a [wiki](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/MSAL-basics#configuration-options) a beállításokkal kapcsolatos további részletekért. |

### <a name="sign-in-users"></a>A felhasználók

A következő kódrészletet bemutatja, hogyan a felhasználók:

```javascript
myMSALObj.loginPopup(applicationConfig.graphScopes).then(function (idToken) {
    //Callback code here
})
```

> |Ahol  |  |
> |---------|---------|
> | `scopes`   | (Nem kötelező) A kért felhasználói beleegyezés bejelentkezési időpontban hatókörök tartalmazza. Például `[ "user.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni Web API-k (azaz `api://<Application ID>/access_as_user` ). Itt `applicationConfig.graphScopes` átadott. |

> [!TIP]
> Azt is megteheti, előfordulhat, hogy szeretné használni a `loginRedirect` metódus az aktuális oldal átirányítása a bejelentkezési oldal helyett egy másik előugró ablak.

### <a name="request-tokens"></a>Jogkivonatok kérelmezésére

Az MSAL jogkivonatok beszerzésére használható három módszer van: `acquireTokenRedirect`, `acquireTokenPopup` és `acquireTokenSilent`

#### <a name="get-a-user-token-silently"></a>Felhasználói jogkivonat csendes beszerzése

A `acquireTokenSilent` metódus kezeli a token beszerzését és -megújítás, felhasználói beavatkozás nélkül. Miután a `loginRedirect` vagy `loginPopup` metódus végrehajtása az első alkalommal `acquireTokenSilent` későbbi hívások védett erőforrások eléréséhez használt tokenek beszerzése érdekében a gyakran használt módszer. Hívások újítsa meg a tokenek vagy kérheti a beavatkozás nélkül történik.

```javascript
myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> |Ahol  |  |
> |---------|---------|
> | `scopes`   | A hozzáférési jogkivonatot adott vissza API-hoz, a kért hatóköröket tartalmazza. Például `[ "user.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni Web API-k (azaz `api://<Application ID>/access_as_user`). Itt `applicationConfig.graphScopes` átadott.|

#### <a name="get-a-user-token-interactively"></a>Felhasználói jogkivonat interaktív lekérése

Vannak helyzetek, ahol meg kell a felhasználókat az Azure AD v2.0-végpont interakcióba. Példa:
* Felhasználók előfordulhat, hogy meg kell adnia a hitelesítő adataikat, mert lejárt
* Az alkalmazás további erőforrás hatókörhöz, amely a felhasználónak van szüksége, hogy engedélyt adjanak az access
* Kétfaktoros hitelesítésre van szükség

A legtöbb alkalmazás a szokásos javasolt minta, hogy a hívás `acquireTokenSilent` először a findlogin majd, és ezután hívja meg `acquireTokenRedirect` (vagy `acquireTokenPopup`) egy interaktív kérést.

Hívása a `acquireTokenPopup(scope)` bejelentkezni egy másik előugró ablak eredményez (vagy `acquireTokenRedirect(scope)` eredményez a felhasználók átirányítása az Azure AD v2.0-végpont), ha engedélyezi a hogy a szükséges erőforrást, vagy erősítse meg a hitelesítő adataikat, hogy a felhasználóknak kell vagy a kéttényezős hitelesítés elvégzése.

```javascript
myMSALObj.acquireTokenPopup(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> [!NOTE]
> Ebben a rövid útmutatóban használt a `loginRedirect` és `acquireTokenRedirect` módszerek, ha a használt böngésző oka az, hogy az Internet Explorer egy [ismert hiba](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) kapcsolatos kezelési előugró Windows Internet Explorer böngészőben.

## <a name="next-steps"></a>További lépések

Részletesebb lépésenkénti útmutató az ebben a rövid útmutatóban az alkalmazás létrehozása próbálja ki az alábbi JavaScript-oktatóanyag.

### <a name="learn-the-steps-to-create-the-application-for-this-quickstart"></a>Ismerje meg, ez a rövid útmutató az alkalmazás létrehozásának lépéseit

> [!div class="nextstepaction"]
> [A Graph API meghívása – oktatóanyag](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Keresse meg a MSAL-adattárat a dokumentáció, GYIK, problémák és egyéb

> [!div class="nextstepaction"]
> [msal.js GitHub-adattár](https://github.com/AzureAD/microsoft-authentication-library-for-js)
