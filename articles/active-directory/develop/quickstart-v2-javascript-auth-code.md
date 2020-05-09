---
title: Felhasználói bejelentkezés a JavaScript-alapú egyoldalas alkalmazásokban hitelesítési kóddal | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hívhat meg egy JavaScript-alkalmazás olyan API-t, amely a Microsoft Identity platform használatával hozzáférési jogkivonatokat igényel.
services: active-directory
author: hahamil
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 04/22/2020
ms.author: hahamil
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ROBOTS: NOINDEX
ms.openlocfilehash: 9663c11508b0478a67f528cb301d705a3125e4f6
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82871521"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-a-javascript-spa-using-the-auth-code-flow"></a>Gyors útmutató: bejelentkezés a felhasználókba és hozzáférési token beszerzése egy JavaScript SPA-ban az Auth Code flow használatával 

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány aspektusa változhat az általános elérhetősége előtt.


Ez a rövid útmutató a MSAL. js 2,0-et használja az engedélyezési kód folyamatával. Ha a MSAL. js 1,0-et az implicit folyamattal szeretné használni, tekintse meg [ezt](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript)a rövid útmutatót.

Ebben a rövid útmutatóban megtudhatja, hogyan jelentkezhet be a JavaScript egyoldalas alkalmazás (SPA) a személyes fiókok, a munkahelyi fiókok és az iskolai fiókok felhasználói számára. A JavaScript SPA a Microsoft Graph API vagy bármely webes API meghívására is kérhet hozzáférési jogkivonatot. Nézze meg, [Hogyan működik a minta](#how-the-sample-works) egy ábrán.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [ingyenes Azure-előfizetés létrehozása](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) (projektfájlok szerkesztése)


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
>
> 1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókját a jobb felső sarokban, majd állítsa be a portál munkamenetét a használni kívánt Azure AD-bérlőre.
> 1. Válassza a [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908)lehetőséget.
> 1. Válassza az **új regisztráció**lehetőséget.
> 1. Ha megjelenik az **Alkalmazás regisztrálása** oldal, adjon nevet az alkalmazásnak.
> 1. A **támogatott fiókok típusai**területen válassza a **fiókok lehetőséget bármely szervezeti címtárban és személyes Microsoft-fiókban**.
> 1. Kattintson a **Register** (Regisztrálás) elemre. Az alkalmazás **áttekintése** lapon jegyezze fel az **alkalmazás (ügyfél) azonosítójának** értékét későbbi használatra.
> 1. A regisztrált alkalmazás bal oldali ablaktábláján válassza a **hitelesítés**lehetőséget.
> 1. A **platform-konfigurációk**területen válassza **a platform hozzáadása**lehetőséget. Ekkor megnyílik egy panel a bal oldalon. Itt válassza ki az **egyoldalas alkalmazások** régiót.
> 1. Továbbra is a bal oldalon adja meg az **átirányítási URI** értékét `http://localhost:3000/`. 
> 1. Válassza a **Konfigurálás** lehetőséget.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1. lépés: az alkalmazás konfigurálása a Azure Portalban
> Ahhoz, hogy a kód mintája ebben a rövid útmutatóban működjön, hozzá `redirectUri` kell `http://localhost:3000/`adnia a as-t.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-javascript/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-project"></a>2. lépés: A projekt letöltése

> [!div renderon="docs"]
> Ha a projektet a Node. js használatával webkiszolgálóval szeretné futtatni, [töltse le az alapprojekt fájljait](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip).

> [!div renderon="portal" class="sxs-lookup"]
> A projekt futtatása webkiszolgálóval a Node. js használatával

> [!div renderon="portal" id="autoupdate" class="nextstepaction" class="sxs-lookup"]
> [A mintakód letöltése](https://github.com/Azure-Samples/ms-identity-javascript-v2/archive/quickstart.zip)

> [!div renderon="docs"]
> #### <a name="step-3-configure-your-javascript-app"></a>3. lépés: a JavaScript-alkalmazás konfigurálása
>
> Az *alkalmazás* mappájában szerkessze a *authConfig. js fájlt*, és `clientID`állítsa `authority` be `redirectUri` a és `msalConfig`a értékeket a alatt.
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

> [!div renderon="portal" class="sxs-lookup"]
> > [!NOTE]
> > :::no-loc text="Enter_the_Supported_Account_Info_Here":::

> [!div renderon="docs"]
>
> Az elemek magyarázata:
> - Enter_the_Application_Id_Here>a regisztrált alkalmazáshoz tartozó **alkalmazás (ügyfél) azonosítója** . * \<*
> - Enter_the_Cloud_Instance_Id_Here>az Azure-felhő példánya. * \<* A fő vagy a globális Azure-felhőhöz egyszerűen *https://login.microsoftonline.com/* írja be a következőt:. Az **országos** felhők (például Kína) esetében lásd: [nemzeti felhők](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).
> - Enter_the_Tenant_info_here>az alábbi lehetőségek egyikére van beállítva: * \<*
>    - Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a **bérlői azonosítóra** vagy a **bérlő nevére** (például *contoso.microsoft.com*).
>    - Ha az alkalmazás *minden szervezeti címtárban támogatja a fiókokat*, cserélje le ezt az értéket **szervezetekkel**.
>    - Ha az alkalmazás *minden szervezeti címtárban és személyes Microsoft-fiókban is támogatja a fiókokat*, cserélje le ezt az értéket **közösre**. Ha *csak a személyes Microsoft-fiókok*támogatását szeretné korlátozni, cserélje le ezt az értéket a **fogyasztókkal**.
> - * \<Enter_the_Redirect_Uri_Here>*`http://localhost:3000`
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítójának**, **könyvtárának (BÉRLŐi) azonosítójának**és a **támogatott fióktípus**értékének megkereséséhez nyissa meg az alkalmazás regisztrációjának **Áttekintés** lapját a Azure Portal.
>
> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-your-app-is-configured-and-ready-to-run"></a>3. lépés: az alkalmazás konfigurálva van, és készen áll a futtatásra
> A projektet az alkalmazás tulajdonságainak értékeivel konfiguráltuk.

> [!div renderon="docs"]
>
> Ezután még ugyanabban a mappában szerkessze a *graphConfig. js* fájlt, `graphMeEndpoint` és állítsa be a `graphMailEndpoint` és az `apiConfig` objektumot.
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
> Enter_the_Graph_Endpoint_Here>a végpont, amelyet az API-hívások fognak végezni. * \<* A fő vagy a globális Microsoft Graph API szolgáltatásnál adja `https://graph.microsoft.com`meg a következőt:. További információ: [belföldi Felhőbeli üzembe helyezés](https://docs.microsoft.com/graph/deployments).
>
> #### <a name="step-4-run-the-project"></a>4. lépés: a projekt futtatása

Futtassa a projektet egy webkiszolgálóval a [Node. js](https://nodejs.org/en/download/)használatával:

1. A kiszolgáló elindításához futtassa a következő parancsokat a projekt könyvtárából:
    ```bash
    npm install
    npm start
    ```
1. Nyissa meg a következő címet: `http://localhost:3000/`.

1. Válassza a **Bejelentkezés** lehetőséget a bejelentkezési folyamat elindításához, majd a Microsoft Graph API meghívásához.

    Amikor először jelentkezik be, a rendszer arra kéri, hogy adja meg az engedélyt, hogy az alkalmazás hozzáférhessen a profiljához, és jelentkezzen be. Miután sikeresen bejelentkezett, a felhasználói profil adatai megjelennek az oldalon.

## <a name="more-information"></a>További információ

### <a name="how-the-sample-works"></a>A minta működése

![A minta JavaScript SPA működése: 1. A SPA kezdeményezi a bejelentkezést. 2. A fürdő azonosító jogkivonatot vásárol a Microsoft Identity platformon. 3. A SPA-hívások jogkivonat beszerzése. 4. A Microsoft Identity platform egy hozzáférési jogkivonatot ad vissza a SPA-nak. 5. A SPA a Microsoft Graph API-val a hozzáférési jogkivonattal és a HTTP GET kéréssel végzi el a hozzáférést. 6. A Graph API egy HTTP-választ ad vissza a SPA-nak.](media/quickstart-v2-javascript/javascriptspa-intro.svg)

### <a name="msaljs"></a>msal. js

A MSAL. js függvénytár aláírja a felhasználókat, és a Microsoft Identity platform által védett API eléréséhez használt jogkivonatokat kéri. A minta *index. html* fájlja a könyvtárra mutató hivatkozást tartalmaz:

```html
<script type="text/javascript" src="https://alcdn.msauth.net/browser/2.0.0-beta.0/js/msal-browser.js" integrity=
"sha384-r7Qxfs6PYHyfoBR6zG62DGzptfLBxnREThAlcJyEfzJ4dq5rqExc1Xj3TPFE/9TH" crossorigin="anonymous"></script>
```
> [!TIP]
> Az előző verziót lecserélheti a legújabb, a [MSAL. js kiadásban](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)megjelent verzióra.

Ha a Node. js telepítve van, a legújabb verziót a Node. js csomagkezelő (NPM) használatával töltheti le:

```batch
npm install @azure/msal-browser
```

## <a name="next-steps"></a>További lépések

A [MSAL. js GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js) -tárház további könyvtári dokumentációt, GYIK-t tartalmaz, és támogatást biztosít.

A rövid útmutatóhoz tartozó alkalmazás létrehozásával kapcsolatos részletes útmutatót a következő témakörben talál:

> [!div class="nextstepaction"]
> [Oktatóanyag a bejelentkezéshez és az MS Graph meghívásához](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-javascript-auth-code)
