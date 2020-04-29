---
title: Bejelentkezési felhasználók szögletes egyoldalas alkalmazásokban – Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hívhat meg egy szögletes alkalmazás olyan API-t, amely hozzáférési jogkivonatokat igényel a Microsoft Identity platform használatával.
services: active-directory
author: jasonnutter
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:JavaScript
ms.topic: quickstart
ms.workload: identity
ms.date: 03/18/2020
ms.author: janutter
ms.openlocfilehash: 4b6a2481c18314a44470a020033ffdc4ba1d7259
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81380031"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Gyors útmutató: bejelentkezés a felhasználókba és hozzáférési jogkivonat beszerzése egy szögletes egyoldalas alkalmazásban

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány aspektusa változhat az általános elérhetősége előtt.

Ebben a rövid útmutatóban megtudhatja, hogyan jelentkezhet be egy szögletes egyoldalas alkalmazás (SPA) olyan felhasználók számára, akik személyes Microsoft-fiókkal, munkahelyi fiókkal vagy iskolai fiókkal rendelkeznek. A szögletes SPA a Microsoft Graph API vagy bármely webes API meghívására is kérhet hozzáférési jogkivonatot.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Node. js](https://nodejs.org/en/download/)-fájl.
* A Project Files vagy a [Visual studio 2019](https://visualstudio.microsoft.com/downloads/) a projekt futtatására szolgáló [Visual Studio Code](https://code.visualstudio.com/download) .

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>A Gyorsindítás alkalmazás regisztrálása és letöltése
> A gyors üzembe helyezési alkalmazás indításához használja az alábbi lehetőségek egyikét.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>1. lehetőség (expressz): regisztrálja és automatikusan konfigurálja az alkalmazást, majd töltse le a kód mintáját.
>
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz fér hozzá, válassza ki a fiókot a jobb felső sarokban, majd állítsa be a portál munkamenetét a használni kívánt Azure Active Directory (Azure AD) bérlőre.
> 1. Nyissa meg a Azure Portal új [Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) paneljét.
> 1. Adja meg az alkalmazás nevét, majd válassza a **regisztráció**lehetőséget.
> 1. Lépjen a gyors üzembe helyezés panelre, és tekintse meg a szögletes rövid útmutatót. Az új alkalmazás letöltéséhez és automatikus konfigurálásához kövesse az utasításokat.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>2. lehetőség (manuális): az alkalmazás-és a kód-minta regisztrálása és manuális konfigurálása
>
> #### <a name="step-1-register-the-application"></a>1. lépés: az alkalmazás regisztrálása
>
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz fér hozzá, válassza ki a fiókját a jobb felső sarokban, és állítsa be a portál munkamenetét a használni kívánt Azure AD-bérlőre.
> 1. Kövesse az utasításokat [egy egyoldalas alkalmazás regisztrálásához](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) a Azure Portal.
> 1. Vegyen fel egy új platformot az alkalmazás regisztrációjának **hitelesítés** ablaktábláján, és regisztrálja `http://localhost:4200/`az átirányítási URI-t:.
> 1. Ez a rövid útmutató az [implicit engedélyezési folyamatot](v2-oauth2-implicit-grant-flow.md)használja. Adja meg az **azonosító jogkivonatok** és a **hozzáférési tokenek** **implicit engedélyezési** beállításait. Az azonosító jogkivonatok és hozzáférési tokenek megadása kötelező, mert az alkalmazás bejelentkezik a felhasználókba, és meghívja az API-t.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>1. lépés: az alkalmazás konfigurálása a Azure Portalban
> Ahhoz, hogy a rövid útmutatóhoz tartozó mintakód működjön, hozzá kell adnia egy átirányítási URI **http://localhost:4200/** -t, és engedélyeznie kell az **implicit engedélyezést**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítások elvégzése]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-javascript/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-code-sample"></a>2. lépés: a mintakód letöltése
>[!div renderon="docs"]
>A projekt a Node. js használatával webkiszolgálóval való futtatásához, [a minta tárház klónozásához](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) vagy [az alapszintű projektfájlok letöltéséhez](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip). Nyissa meg a fájlokat egy szerkesztővel, például a Visual Studio Code használatával.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [A mintakód letöltése](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>3. lépés: a JavaScript-alkalmazás konfigurálása
>
> A *src/app* mappában szerkessze az *app. Module. TS* fájlt, és `clientId` állítsa `authority` be a `MsalModule.forRoot`és a értékeket.
>
>```javascript
>MsalModule.forRoot({
>    auth: {
>        clientId: 'Enter_the_Application_Id_here', // This is your client ID
>        authority: 'https://login.microsoftonline.com/Enter_the_Tenant_Info_Here', // This is your tenant info
>        redirectUri: 'Enter_the_Redirect_Uri_Here' // This is your redirect URI
>    },
>    cache: {
>        cacheLocation: 'localStorage',
>        storeAuthStateInCookie: isIE, // set to true for IE 11
>    },
>},
> //... )
>```

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Enter_the_Supported_Account_Info_Here


> [!div renderon="docs"]
>
> Cserélje le ezeket az értékeket:
>
>|Érték neve|Leírás|
>|---------|---------|
>|Enter_the_Application_Id_Here|Az alkalmazás regisztrációjának **Áttekintés** lapján ez az **alkalmazás (ügyfél) azonosítójának** értéke. |
>|Enter_the_Cloud_Instance_Id_Here|Ez az Azure-felhő példánya. A fő vagy a globális Azure-felhőhöz **https://login.microsoftonline.com**írja be a következőt:. Az országos felhők (például Kína) esetében lásd: [nemzeti felhők](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
>|Enter_the_Tenant_Info_Here| Állítsa be a következő lehetőségek egyikét: Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat*, cserélje le ezt az értéket a címtár (bérlő) vagy a bérlő nevére (például **contoso.microsoft.com**). Ha az alkalmazás *minden szervezeti címtárban támogatja a fiókokat*, cserélje le ezt az értéket **szervezetekkel**. Ha az alkalmazás *minden szervezeti címtárban és személyes Microsoft-fiókban is támogatja a fiókokat*, cserélje le ezt az értéket **közösre**. Ha *csak a személyes Microsoft-fiókok*támogatását szeretné korlátozni, cserélje le ezt az értéket a **fogyasztókkal**. |
>|Enter_the_Redirect_Uri_Here|Cserélje le **http://localhost:4200**a-t.|
>|cacheLocation  | Választható Állítsa be a böngésző tárterületét a hitelesítési állapothoz. Az alapértelmezett érték a **sessionStorage**.   |
>|storeAuthStateInCookie  | Választható Azonosítsa a hitelesítési kérelem állapotát tároló könyvtárat. Ez az állapot szükséges a böngésző cookie-jai hitelesítési folyamatainak ellenőrzéséhez. Ez a cookie be van állítva az Internet Explorer és a Edge számára a két böngésző számára. További részletekért tekintse meg az [ismert problémákat](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues). |
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítója**, a **címtár (bérlő) azonosítója** és a **támogatott fióktípusok** értékét az alkalmazás **Áttekintés** oldalán találja az Azure Portalon.

További információ az elérhető konfigurálható lehetőségekről: [ügyfélalkalmazások inicializálása](msal-js-initializing-client-applications.md). 

A MSAL. js függvénytár forráskódját a GitHubon található [AzureAD/Microsoft-Authentication-Library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) adattárban találja.

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>3. lépés: a projekt futtatása

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>4. lépés: a projekt futtatása

Ha a Node. js-t használja:

1. Indítsa el a kiszolgálót a következő parancsok futtatásával a projekt könyvtárából:

   ```console
   npm install
   npm start
   ```

1. Keresse meg **http://localhost:4200/** a következőt:.
1. Válassza a **Bejelentkezés**lehetőséget.
1. Válassza ki a **profilt** a Microsoft Graph meghívásához.

Ha a böngésző betölti az alkalmazást, válassza a **Bejelentkezés**lehetőséget. Amikor először jelentkezik be, a rendszer arra kéri, hogy adja meg az engedélyt, hogy az alkalmazás hozzáférhessen a profiljához, és jelentkezzen be. Miután sikeresen bejelentkezett, válassza a **profil**lehetőséget, és a felhasználói profil adatai megjelennek az oldalon.

## <a name="how-the-sample-works"></a>A minta működése

![Diagram, amely bemutatja, hogyan működik a jelen rövid útmutatóban szereplő minta alkalmazás](media/quickstart-v2-javascript/javascriptspa-intro.svg)


## <a name="next-steps"></a>További lépések

Következő lépésként megtudhatja, hogyan jelentkezhet be a felhasználóba, és hogyan szerezheti be a tokeneket a szögletes oktatóanyagban:

> [!div class="nextstepaction"]
> [Szögletes oktatóanyag](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular)

