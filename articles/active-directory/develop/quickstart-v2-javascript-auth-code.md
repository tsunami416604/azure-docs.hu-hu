---
title: Felhasználói bejelentkezés a JavaScript-alapú egyoldalas alkalmazásokban (SPA) hitelesítési kóddal | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hívhat meg egy JavaScript-alkalmazás olyan API-t, amely a Microsoft Identity platform használatával hozzáférési jogkivonatokat igényel.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 05/19/2020
ms.author: hahamil
ms.custom: aaddev, scenarios:getting-started, languages:JavaScript
ms.openlocfilehash: 0eaa4a828716f1a52161726e768acc817d1fde17
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86134180"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>Gyors útmutató: bejelentkezés a felhasználókba és hozzáférési token beszerzése egy JavaScript SPA-ban az Auth Code flow használatával

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány aspektusa változhat az általános elérhetősége előtt.

Ebben a rövid útmutatóban egy kódrészletet fog futtatni, amely bemutatja, hogyan jelentkezhet be a JavaScript egyoldalas alkalmazás (SPA) a személyes fiókok, a munkahelyi fiókok és az iskolai fiókok felhasználói számára az engedélyezési kód folyamatának használatával. A kód minta azt is bemutatja, hogyan szerezhet be egy hozzáférési jogkivonatot a webes API meghívásához, ebben az esetben a Microsoft Graph API-t. Nézze meg, [Hogyan működik a minta](#how-the-sample-works) egy ábrán.

Ez a rövid útmutató a MSAL.js 2,0-et használja az engedélyezési kód folyamatával. A MSAL.js 1,0-et az implicit folyamattal használó gyors útmutató: gyors üzembe helyezés a [felhasználók között JavaScript egyoldalas alkalmazásokban](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [ingyenes Azure-előfizetés létrehozása](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) vagy más Kódszerkesztő

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-application"></a>A gyors üzembe helyezési alkalmazás regisztrálása és letöltése
> A gyors üzembe helyezési alkalmazás indításához használja az alábbi lehetőségek egyikét.
>
> ### <a name="option-1-express-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1. lehetőség (expressz): regisztrálja és állítsa be automatikusan az alkalmazást, majd töltse le a kód mintáját
>
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókot a jobb felső sarokban, majd állítsa be a portál munkamenetét a használni kívánt Azure Active Directory (Azure AD) bérlőre.
> 1. Válassza a [Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs)lehetőséget.
> 1. Adja meg az alkalmazás nevét.
> 1. A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
> 1. Kattintson a **Register** (Regisztrálás) elemre.
> 1. Lépjen a gyors üzembe helyezés panelre, és kövesse az utasításokat az új alkalmazás letöltéséhez és automatikus konfigurálásához.
>
> ### <a name="option-2-manual-register-and-manually-configure-your-application-and-code-sample"></a>2. lehetőség (manuális): az alkalmazás-és a kód-minta regisztrálása és manuális konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1. lépés: Alkalmazás regisztrálása
>
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókját a jobb felső sarokban, majd állítsa be a portál munkamenetét a használni kívánt Azure AD-bérlőre.
> 1. Válassza a [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908)lehetőséget.
> 1. Válassza az **új regisztráció**lehetőséget.
> 1. Ha megjelenik az **Alkalmazás regisztrálása** oldal, adjon nevet az alkalmazásnak.
> 1. A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
> 1. Kattintson a **Register** (Regisztrálás) elemre. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét későbbi használatra.
> 1. A regisztrált alkalmazás bal oldali ablaktábláján válassza a **hitelesítés**lehetőséget.
> 1. A **platform-konfigurációk**területen válassza **a platform hozzáadása**lehetőséget. A megnyíló panelen válassza az **egylapos alkalmazás**lehetőséget.
> 1. Állítsa be az **átirányítási URI** értékét a következőre: `http://localhost:3000/` .
> 1. Válassza a **Konfigurálás** lehetőséget.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: az alkalmazás konfigurálása a Azure Portalban
> Ahhoz, hogy a kód mintája ebben a rövid útmutatóban működjön, hozzá kell adnia a `redirectUri` as-t `http://localhost:3000/` .
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-javascript/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-project"></a>2. lépés: A projekt letöltése

> [!div renderon="docs"]
> Ha Node.js használatával szeretné futtatni a projektet egy webkiszolgálóval, [töltse le az alapprojekt fájljait](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip).

> [!div renderon="portal" class="sxs-lookup"]
> A projekt futtatása webkiszolgálóval Node.js használatával

> [!div renderon="portal" class="sxs-lookup" id="autoupdate" class="nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/master.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>3. lépés: a JavaScript-alkalmazás konfigurálása
>
> Az *alkalmazás* mappájában nyissa meg a *authConfig.js* fájlt, és frissítse a `clientID` , `authority` , és `redirectUri` értékeket az `msalConfig` objektumban.
>
> ```javascript
> // Config object to be passed to Msal on creation
> const msalConfig = {
>   auth: {
>     clientId: "Enter_the_Application_Id_Here",
>     authority: "Enter_the_Cloud_Instance_Id_HereEnter_the_Tenant_Info_Here",
>     redirectUri: "Enter_the_Redirect_Uri_Here",
>   },
>   cache: {
>     cacheLocation: "sessionStorage", // This configures where your cache will be stored
>     storeAuthStateInCookie: false, // Set this to "true" if you are having issues on IE11 or Edge
>   }
> };
> ```

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
>
> Módosítsa a szakasz értékeit az `msalConfig` itt leírtak szerint:
>
> - `Enter_the_Application_Id_Here`a regisztrált alkalmazáshoz tartozó **alkalmazás (ügyfél) azonosítója** .
> - `Enter_the_Cloud_Instance_Id_Here`Az Azure-felhő példánya. A fő vagy a globális Azure-felhőhöz írja be a következőt: `https://login.microsoftonline.com/` . Az **országos** felhők (például Kína) esetében lásd: [nemzeti felhők](authentication-national-cloud.md).
> - `Enter_the_Tenant_info_here`értéke a következők egyike lehet:
>   - Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a **bérlői azonosítóra** vagy a **bérlő nevére**. Például: `contoso.microsoft.com`.
>   - Ha az alkalmazás *minden szervezeti címtárban támogatja a fiókokat*, cserélje le ezt az értéket a következőre: `organizations` .
>   - Ha az alkalmazás a *szervezeti címtárban és a személyes Microsoft-fiókokban is támogatja a fiókokat*, cserélje le ezt az értéket a következőre: `common` . Ehhez a rövid útmutatóhoz használja **a következőt:** `common` .
>   - A *személyes Microsoft-fiókok*támogatásának korlátozásához cserélje le ezt az értéket a következőre: `consumers` .
> - A `Enter_the_Redirect_Uri_Here` értéke `http://localhost:3000/`.
>
> `authority`Ha a fő (globális) Azure-felhőt használja, a *authConfig.jsban* a következőhöz hasonló értéknek kell megjelennie:
>
> ```javascript
> authority: "https://login.microsoftonline.com/common",
> ```
>
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítójának**, **könyvtárának (BÉRLŐi) azonosítójának**és a **támogatott fióktípus**értékének megkereséséhez nyissa meg az alkalmazás regisztrációjának **Áttekintés** lapját a Azure Portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. lépés: az alkalmazás konfigurálva van, és készen áll a futtatásra
> A projektet az alkalmazás tulajdonságainak értékeivel konfiguráltuk.

> [!div renderon="docs"]
>
> Ezután továbbra is ugyanabban a mappában szerkessze a *graphConfig.js* fájlt, és frissítse `graphMeEndpoint` az `graphMailEndpoint` objektumot és az értékeket `apiConfig` .
>
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
> `Enter_the_Graph_Endpoint_Here`a végpont az API-hívásokat fogja elvégezni. A fő (globális) Microsoft Graph API szolgáltatásnál adja meg a `https://graph.microsoft.com/` következőt: (a záró továbbítási perjelet is beleértve). Az országos felhők Microsoft Graphával kapcsolatos további információkért lásd: [országos Felhőbeli üzembe helyezés](https://docs.microsoft.com/graph/deployments).
>
> `graphMeEndpoint`Ha a `graphMailEndpoint` fő (globális) Microsoft Graph API-szolgáltatást használja, a *graphConfig.js* fájlban a következőhöz hasonló értéknek kell szerepelnie:
>
> ```javascript
> graphMeEndpoint: "https://graph.microsoft.com/v1.0/me",
> graphMailEndpoint: "https://graph.microsoft.com/v1.0/me/messages"
> ```
>
> #### <a name="step-4-run-the-project"></a>4. lépés: a projekt futtatása

A projekt futtatása webkiszolgálóval Node.js használatával:

1. A kiszolgáló elindításához futtassa a következő parancsokat a projekt könyvtárából:
    ```console
    npm install
    npm start
    ```
1. Nyissa meg a következő címet: `http://localhost:3000/`.

1. Válassza a **Bejelentkezés** lehetőséget a bejelentkezési folyamat elindításához, majd hívja meg a Microsoft Graph API-t.

    Amikor először jelentkezik be, a rendszer arra kéri, hogy adja meg az engedélyt, hogy az alkalmazás hozzáférhessen a profiljához, és jelentkezzen be. Miután sikeresen bejelentkezett, a felhasználói profil adatai megjelennek az oldalon.

## <a name="more-information"></a>További információ

### <a name="how-the-sample-works"></a>A minta működése

![Egy egyoldalas alkalmazás engedélyezési kódjának folyamatát ábrázoló diagram.](media/quickstart-v2-javascript-auth-code/diagram-01-auth-code-flow.png)

### <a name="msaljs"></a>msal.js

A MSAL.js kódtár bejelentkezik a felhasználók számára, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kéri. A minta *index.html* fájl a könyvtárra mutató hivatkozást tartalmaz:

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```

Ha Node.js van telepítve, a legújabb verziót a Node.js Package Manager (NPM) segítségével töltheti le:

```console
npm install @azure/msal-browser
```

## <a name="next-steps"></a>Következő lépések

Az ebben a rövid útmutatóban használt alkalmazás létrehozásával kapcsolatos részletes útmutatásért tekintse meg a következő oktatóanyagot:

> [!div class="nextstepaction"]
> [Oktatóanyag a bejelentkezéshez és az MS Graph meghívásához >](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code)
