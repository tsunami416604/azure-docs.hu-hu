---
title: Felhasználók bejelentkezése az Egyoldalas alkalmazásokban – Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hívhat meg egy Angular alkalmazás egy olyan API-t, amely hozzáférési jogkivonatokat igényel a Microsoft identity platform használatával.
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
ms.openlocfilehash: 61a098b92db13b8422d9cfebb19610c5de7685cd
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631752"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-spa"></a>Rövid útmutató: Jelentkezzen be a felhasználókba, és szerezzen be egy hozzáférési jogkivonatot egy Angular SPA-ban

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

Ebben a rövid útmutatóban egy kódminta segítségével megtudhatja, hogy egy egyoldalas alkalmazás (SPA) hogyan tud bejelentkezni a személyes Microsoft-fiókkal rendelkező felhasználókba, valamint munkahelyi fiókokba és iskolai fiókokba. Az Angular SPA is kaphat egy hozzáférési jogkivonatot a Microsoft Graph API vagy bármely webes API hívásához.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/)
* [A Project](https://code.visualstudio.com/download) fájlok szerkesztéséhez a Visual Studio kódja, a Project 2019 vagy a [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) a projekt futtatásához.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>A rövid útmutató alkalmazás regisztrálása és letöltése
> A rövid útmutató alkalmazás elindításához használja az alábbi lehetőségek egyikét:
>
> ### <a name="option-1-express-register-and-auto-configure-the-app-then-download-the-code-sample"></a>1. lehetőség (Expressz): Regisztrálja és konfigurálja automatikusan az alkalmazást. Ezután töltse le a kódmintát
>
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
> 1. Ha a fiók több bérlőhöz is hozzáfér, válassza ki a jobb felső sarokban lévő fiókot, majd állítsa be a portálmunkamenetet a használni kívánt Azure Active Directory (Azure AD) bérlőre.
> 1. Nyissa meg az új [Azure Portal – Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) ablaktáblát.
> 1. Adja meg az alkalmazás nevét, majd válassza a **Regisztráció**lehetőséget.
> 1. Nyissa meg a rövid útmutató ablaktáblát, és tekintse meg a Szögletes rövid útmutatót. Kövesse az utasításokat az új alkalmazás letöltéséhez és automatikus konfigurálásához.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>2. lehetőség (kézi): Regisztrálja és manuálisan konfigurálja az alkalmazást és a kódmintát
>
> #### <a name="step-1-register-the-application"></a>1. lépés: Az alkalmazás regisztrálása
>
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
>
> 1. Ha a fiók több bérlőhöz is hozzáfér, válassza ki a fiókját a jobb felső sarokban, és állítsa be a portálmunkamenetet a használni kívánt Azure AD-bérlőre.
> 1. Kövesse az utasításokat [egy egyoldalas alkalmazás regisztrálásához](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) az Azure Portalon.
> 1. Adjon hozzá egy új platformot az alkalmazásregisztráció **hitelesítési paneljén,** és regisztrálja az átirányítási URI-t:`http://localhost:4200/`
> 1. Ez a rövid útmutató az [implicit támogatási folyamatot](v2-oauth2-implicit-grant-flow.md)használja. Válassza ki az **azonosítójogtok** és **a hozzáférési jogkivonatok**implicit **támogatási** beállításait. Azonosító jogkivonatok és hozzáférési jogkivonatok szükségesek, mert ez az alkalmazás aláírja a felhasználók és egy API-t hív meg.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
> Ahhoz, hogy a gyorsindítás kódmintája működjön, hozzá kell adnia egy as-t, `redirectUri` `http://localhost:4200/` és engedélyeznie kell az Implicit **támogatást.**
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Hajtsa végre ezeket a módosításokat nekem]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-javascript/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-code-sample"></a>2. lépés: A kódminta letöltése
>[!div renderon="docs"]
>Ha a projektet webkiszolgálóval szeretné futtatni a https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular Node.js fájlokkal, klónozza vagy [töltse le](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip) az alapvető projektfájlokat. Nyissa meg a fájlokat egy szerkesztővel, például a Visual Studio-kóddal.

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [A kódminta letöltése](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip)

> [!div renderon="docs"]
>#### <a name="step-3-configure-the-javascript-app"></a>3. lépés: A JavaScript alkalmazás konfigurálása
>
> Az *src/app* mappában edit *app.module.ts* `authority` és `MsalModule.forRoot`állítsa be a `clientId` és értékek a területen.
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
> Cserélje le ezeket az értékeket, mint olyan:
>
>|Érték neve|Leírás|
>|---------|---------|
>|Enter_the_Application_Id_Here|A jelentkezési regisztráció **áttekintő lapján** ez az Ön **alkalmazás(ügyfél) azonosítója** |
>|Enter_the_Cloud_Instance_Id_Here|Ez az Azure-felhő példánya. A fő vagy globális Azure-felhőbe írja be a ( fő vagy globális Azure-felhő) mezőbe. https://login.microsoftonline.com A nemzeti felhők (például Kína), lásd: [Nemzeti felhők](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
>|Enter_the_Tenant_Info_Here| Állítsa be a következő beállítások egyikére: 1) Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat,* cserélje le ezt az értéket a **Címtár (bérlői) azonosító** vagy **bérlő nevére** (például *contoso.microsoft.com).* 2) Ha az alkalmazás támogatja a *fiókok bármely szervezeti címtárban,* cserélje le ezt az értéket **a szervezetek**. 3) Ha az alkalmazás támogatja *a fiókok at bármely szervezeti címtár és a személyes Microsoft-fiókok,* cserélje ki ezt az értéket a **közös**. 4) Ha *csak a személyes Microsoft-fiókokra*szeretné korlátozni a támogatást, cserélje le ezt az értéket a **fogyasztókra.** |
>|Enter_the_Redirect_Uri_Here|Csere erre:`http://localhost:4200`|
>|cacheLocation (gyorsítótárhely)  | (Nem kötelező) Beállítja a böngésző tárolóját az auth állapothoz. Az alapértelmezett a sessionStorage.   |
>|storeAuthStateInCookie  | (Nem kötelező) A hitelesítési kérelem állapotát tároló tár. Ez az állapot szükséges a hitelesítési folyamatok érvényesítéséhez a böngésző cookie-kat. Ez a cookie az Internet Explorer és az Edge böngészők számára van beállítva, hogy elférjen benne a két böngészőben. További részletek az [ismert problémák](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues)ban. |
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítója**, a **címtár (bérlő) azonosítója** és a **támogatott fióktípusok** értékét az alkalmazás **Áttekintés** oldalán találja az Azure Portalon.

Az elérhető konfigurálható beállításokról az [Ügyfélalkalmazások inicializálása](msal-js-initializing-client-applications.md)című témakörben talál további információt.

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>3. lépés: A projekt futtatása

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>4. lépés: A projekt futtatása

* Node.js használata esetén:

    1. Indítsa el a kiszolgálót a következő parancsok futtatásával a projekt könyvtárából:

        ```console
        npm install
        npm start
        ```

    1. Tallózás `http://localhost:4200/`.
    1. Válassza **a Bejelentkezés**lehetőséget.
    1. Válassza **a Profil** lehetőséget a Microsoft Graph hívásához.

Miután a böngésző betölti az alkalmazást, válassza **a Bejelentkezés**lehetőséget. Az első bejelentkezéskor a rendszer kéri, hogy adja meg a beleegyezését ahhoz, hogy az alkalmazás hozzáférhessen a profiljához, és bejelentkezhessen. Miután sikeresen bejelentkezett, válassza a **Profil**lehetőséget, és a felhasználói profil adatai megjelennek az oldalon.

## <a name="how-the-sample-works"></a>A minta működése

![A mintaalkalmazás működése ebben a rövid útmutatóban](media/quickstart-v2-javascript/javascriptspa-intro.svg)


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> Nyissa meg a [Szögletes oktatóanyag,](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular) hogyan jelentkezzen be a felhasználó, és szerezzen be jogkivonatokat.

Böngésszen az [MSAL tártárjában](https://github.com/AzureAD/microsoft-authentication-library-for-js) dokumentációért, gyakori kérdésekért, problémákért és egyebekért.
