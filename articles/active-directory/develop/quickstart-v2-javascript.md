---
title: Azure AD v2 JavaScript rövid útmutató |} A Microsoft Docs
description: Ismerje meg, hogyan a JavaScript-alkalmazások hívhatják egy API, amely szerint az Azure Active Directory v2.0-végpont hozzáférési jogkivonatok szükséges
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 1b884571707aab71e8a8d124ba68f938e5a63a43
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063744"
---
# <a name="quickstart-sign-in-users-and-acquire-an-access-token-from-a-javascript-application"></a>Gyors útmutató: A felhasználók Bejelentkeztetéséhez és a egy JavaScript-alkalmazását a hozzáférési jogkivonat beszerzése

[!INCLUDE [active-directory-develop-applies-v2-msal](../../../includes/active-directory-develop-applies-v2-msal.md)]

Ebben a rövid útmutatóban fog megtudhatja, hogyan használhat egy kódmintát, amely bemutatja, hogyan egyoldalas alkalmazás (SPA) is jelentkezzen be a személyes fiókokat, munkahelyi és iskolai fiókok, a Microsoft Graph API vagy minden olyan webes API hívása hozzáférési jogkivonatot kapjon a JavaScript.

![Ez a rövid útmutató által létrehozott mintaalkalmazás működése](media/quickstart-v2-javascript/javascriptspa-intro.png)

> [!div renderon="docs"]
> ## <a name="register-your-application-and-download-your-quickstart-app"></a>Regisztrálja az alkalmazást, és töltse le a gyorsindítási alkalmazás
>
> ### <a name="step-1-register-your-application"></a>1. lépés: Az alkalmazás regisztrálása
>
> 1. Nyissa meg a [Microsoft alkalmazásregisztrációs portálon](https://apps.dev.microsoft.com/portal/register-app) kell regisztrálni egy alkalmazást.
> 1. Az a **alkalmazásnév** mezőbe írjon be egy nevet az alkalmazásnak.
> 1. Ügyeljen arra, hogy a **interaktív telepítés** üres, és adja meg a jelölőnégyzet nincs **létrehozás**.
> 1. Kattintson a **hozzáadása Platform**, majd **webes**.
> 1. Győződjön meg arról, hogy **Implicit folyamat engedélyezése** van *be van jelölve*.
> 1. A **átirányítási URL-címeket** hozzáadása `http://localhost:30662/`.
> 1. Kattintson a **Save** (Mentés) gombra.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
> A kódminta működjön ez a rövid útmutató, meg kell adjon hozzá egy átirányítási URI-t, `http://localhost:30662/` , és engedélyezze **típusú Implicit engedélyezés**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Ezt a módosítást a számomra]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már be van állítva](media/quickstart-v2-javascript/green-check.png) az alkalmazás ezekkel az attribútumokkal van konfigurálva.

#### <a name="step-2-download-the-project"></a>2. lépés: Töltse le a projekt

Alkalmas ezen beállítások valamelyikét is válassza ki a fejlesztői környezetbe.
* [A core projektfájlok - webkiszolgáló, például a Node.js letöltése](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip)
* [A Visual Studio-projekt letöltése](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip)

Bontsa ki a zip-fájlt egy helyi mappába (például **C:\Azure-Samples**).

#### <a name="step-3-configure-your-javascript-app"></a>3. lépés: A JavaScript-alkalmazás konfigurálása

> [!div renderon="docs"]
> Szerkesztés `index.html` , és cserélje le `Enter_the_Application_Id_here` alatt `applicationConfig` az imént regisztrált alkalmazást, az alkalmazás azonosítójával.

> [!div class="sxs-lookup" renderon="portal"]
> Szerkesztés `index.html` , és cserélje le `applicationConfig` együtt:

```javascript
var applicationConfig = {
    clientID: "Enter_the_Application_Id_here",
    graphScopes: ["user.read"],
    graphEndpoint: "https://graph.microsoft.com/v1.0/me"
};
```
> [!NOTE]
>Ha [Node.js](https://nodejs.org/en/download/), a *server.js* fájlt a kiszolgáló indítása 30662 porton van konfigurálva.
> Ha [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), a kódminta *.csproj* fájlt a kiszolgáló indítása 30662 porton van konfigurálva.
>

#### <a name="step-4-run-the-project"></a>4. lépés: A projekt futtatása

Node.js használata esetén a parancssorban futtathatja a következő, a projekt könyvtárában, indítsa el a kiszolgálót:
 ```batch
 npm install
 node server.js
 ```
Nyisson meg egy webböngészőt, és navigáljon a `http://localhost:30662/`. Kattintson a **bejelentkezés** gombra kattintva indítsa el a bejelentkezési és majd a Microsoft Graph API meghívása.

Ha a Visual Studio használata esetén ügyeljen arra, hogy válassza ki a projekt megoldás, és nyomja le az **F5** , futtassa a projektet.

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
var myMSALObj = new Msal.UserAgentApplication(applicationConfig.clientID, null, acquireTokenRedirectCallBack, {storeAuthStateInCookie: true, cacheLocation: "localStorage"});
```

> |Ahol  |  |
> |---------|---------|
> |`ClientId`     |Az alkalmazásazonosító az alkalmazás regisztrálva az Azure Portalon|
> |`authority`    |A szolgáltató URL-címe. Átmenő *null* állítja be az alapértelmezett szolgáltató `https://login.microsoftonline.com/common`. Ha az alkalmazás egybérlős (csak egy címtárban célcsoport-kezelési fiókok), ezt az értéket `https://login.microsoftonline.com/<tenant name or ID>`|
> |`tokenReceivedCallback`| Vissza az alkalmazás átirányítja a felhasználókat a hitelesítés után nevű visszahívási metódus. Itt `acquireTokenRedirectCallBack` átadott. Ez a NULL értékű, ha loginPopup használja.|
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
> | `scopes`   | (Nem kötelező) Tartalmazza a kért felhasználói beleegyezés bejelentkezési időpontban hatókörök (például: `[ "user.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni Web API-k (azaz `api://<Application ID>/access_as_user` ). Itt `applicationConfig.graphScopes` átadott. |

> [!TIP]
> Azt is megteheti, előfordulhat, hogy szeretné használni a `loginRedirect` metódus az aktuális oldal átirányítása a bejelentkezési oldal helyett egy másik előugró ablak.


### <a name="request-tokens"></a>Jogkivonatok kérelmezésére

Az MSAL jogkivonatok beszerzésére használható három módszer van: `acquireTokenRedirect`, `acquireTokenPopup` és `acquireTokenSilent`:

#### <a name="get-a-user-token-silently"></a>A felhasználói beavatkozás nélkül token beszerzése

A `acquireTokenSilent` metódus kezeli a token beszerzését és -megújítás, felhasználói beavatkozás nélkül. Miután a `loginRedirect` vagy `loginPopup` metódus végrehajtása az első alkalommal `acquireTokenSilent` későbbi hívások védett erőforrások eléréséhez használt tokenek beszerzése érdekében a gyakran használt módszer. Hívások újítsa meg a tokenek vagy kérheti a beavatkozás nélkül történik.

```javascript
myMSALObj.acquireTokenSilent(applicationConfig.graphScopes).then(function (accessToken) {
    // Callback code here
})
```

> |Ahol  |  |
> |---------|---------|
> | `scopes`   | A hozzáférési jogkivonatot adott vissza API-hoz, a kért hatóköröket tartalmazza (például: `[ "user.read" ]` Microsoft Graph vagy `[ "<Application ID URL>/scope" ]` egyéni Web API-k (azaz `api://<Application ID>/access_as_user` ). Itt `applicationConfig.graphScopes` átadott.|

#### <a name="get-a-user-token-interactively"></a>A felhasználó interaktív token beszerzése

 Vannak helyzetek, ahol meg kell a felhasználókat az Azure AD v2.0-végpont interakcióba. Példa:
* Felhasználók előfordulhat, hogy meg kell adnia a hitelesítő adataikat, mert lejárt
* Az alkalmazás további erőforrás hatókörhöz, amely a felhasználónak van szüksége, hogy engedélyt adjanak az access
* Kéttényezős hitelesítés megadása kötelező

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
> [Hívja a Graph API-oktatóanyag](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-javascriptspa)

### <a name="browse-the-msal-repo-for-documentation-faq-issues-and-more"></a>Keresse meg a MSAL-adattárat a dokumentáció, GYIK, problémák és egyéb

> [!div class="nextstepaction"]
> [msal.js GitHub-adattár](https://github.com/AzureAD/microsoft-authentication-library-for-js)


[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
