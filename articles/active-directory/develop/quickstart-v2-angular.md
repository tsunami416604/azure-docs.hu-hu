---
title: Felhasználók bejelentkezése az Egyoldalas alkalmazásokban – Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hívhat meg egy Angular-alkalmazás hozzáférési jogkivonatokat igénylő API-t a Microsoft identity platform használatával.
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
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380031"
---
# <a name="quickstart-sign-in-users-and-get-an-access-token-in-an-angular-single-page-application"></a>Rövid útmutató: Bejelentkezés a felhasználók között, és kap egy hozzáférési jogkivonatot egy egyoldalas alkalmazásban

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás egyes aspektusai az általános elérhetőség (GA) előtt változhatnak.

Ebben a rövid útmutatóban egy kódminta segítségével megtudhatja, hogy egy egyoldalas alkalmazás (SPA) hogyan tud bejelentkezni olyan felhasználókba, akik személyes Microsoft-fiókkal, munkahelyi fiókkal vagy iskolai fiókkal rendelkeznek. Az Angular SPA is kaphat egy hozzáférési jogkivonatot a Microsoft Graph API vagy bármely webes API hívásához.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetés. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Node.js](https://nodejs.org/en/download/).
* [A Project](https://code.visualstudio.com/download) fájlok szerkesztéséhez a Visual Studio kódja, a Project 2019 vagy a [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) a projekt futtatásához.

> [!div renderon="docs"]
> ## <a name="register-and-download-the-quickstart-app"></a>A rövid útmutató alkalmazás regisztrálása és letöltése
> A rövid útmutató alkalmazás elindításához használja az alábbi lehetőségek egyikét.
>
> ### <a name="option-1-express-register-and-automatically-configure-the-app-and-then-download-the-code-sample"></a>1. lehetőség (expressz): Regisztrálja és automatikusan konfigurálja az alkalmazást, majd töltse le a kódmintát
>
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiók több bérlőhöz is hozzáfér, válassza ki a jobb felső sarokban lévő fiókot, majd állítsa be a portálmunkamenetet a használni kívánt Azure Active Directory (Azure AD) bérlőre.
> 1. Nyissa meg az új [alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType/JavascriptSpaQuickstartPage/sourceType/docs) ablaktáblát az Azure Portalon.
> 1. Adja meg az alkalmazás nevét, majd válassza a **Regisztráció**lehetőséget.
> 1. Nyissa meg a rövid útmutató ablaktáblát, és tekintse meg a Szögletes rövid útmutatót. Kövesse az utasításokat az új alkalmazás letöltéséhez és automatikus konfigurálásához.
>
> ### <a name="option-2-manual-register-and-manually-configure-the-application-and-code-sample"></a>2. lehetőség (manuális): Regisztrálja és manuálisan konfigurálja az alkalmazást és a kódmintát
>
> #### <a name="step-1-register-the-application"></a>1. lépés: Az alkalmazás regisztrálása
>
> 1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiók több bérlőhöz is hozzáfér, válassza ki a jobb felső sarokban lévő fiókot, és állítsa be a portálmunkamenetet a használni kívánt Azure AD-bérlőre.
> 1. Kövesse az utasításokat [egy egyoldalas alkalmazás regisztrálásához](https://docs.microsoft.com/azure/active-directory/develop/scenario-spa-app-registration) az Azure Portalon.
> 1. Adjon hozzá egy új platformot az alkalmazásregisztráció Hitelesítés ablaktábláján, és regisztrálja az átirányítási URI-t: **Authentication** `http://localhost:4200/`.
> 1. Ez a rövid útmutató az [implicit támogatási folyamatot](v2-oauth2-implicit-grant-flow.md)használja. Válassza ki az **azonosítójogtok** és az **Access-jogkivonatok** **implicit támogatási** beállításait. Azonosító jogkivonatok és hozzáférési jogkivonatok szükségesek, mert ez az alkalmazás aláírja a felhasználók és egy API-t hív meg.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-the-application-in-the-azure-portal"></a>1. lépés: Az alkalmazás konfigurálása az Azure Portalon
> Ahhoz, hogy a gyorsindítás kódmintája működjön, hozzá **http://localhost:4200/** kell adnia egy átirányítási URI-t, és engedélyeznie kell **az Implicit támogatást.**
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Hajtsa végre ezeket a módosításokat nekem]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-javascript/green-check.png) Az alkalmazása már konfigurálva van ezekkel az attribútumokkal.

#### <a name="step-2-download-the-code-sample"></a>2. lépés: A kódminta letöltése
>[!div renderon="docs"]
>Ha a projektet webkiszolgálóval szeretné futtatni a Node.js fájlhasználatával, [klónozza a mintatárházat,](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) vagy [töltse le az alapvető projektfájlokat.](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular/archive/master.zip) Nyissa meg a fájlokat egy szerkesztő, például a Visual Studio-kód használatával.

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
> Cserélje le ezeket az értékeket:
>
>|Érték neve|Leírás|
>|---------|---------|
>|Enter_the_Application_Id_Here|Az **alkalmazásregisztráció Áttekintés lapján** ez az **Alkalmazás(ügyfél) azonosítóértéke.** |
>|Enter_the_Cloud_Instance_Id_Here|Ez az Azure-felhő példánya. A fő vagy globális Azure-felhőbe írja be a ( fő vagy globális Azure-felhő) mezőbe. **https://login.microsoftonline.com** A nemzeti felhők (például Kína), lásd: [Nemzeti felhők](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud).|
>|Enter_the_Tenant_Info_Here| Állítsa be a következő beállítások egyikét: Ha az alkalmazás támogatja a *szervezeti címtárban lévő fiókokat,* cserélje le ezt az értéket a címtár (bérlői) azonosító vagy a bérlő nevére (például **contoso.microsoft.com).** Ha az alkalmazás *bármely szervezeti címtárban támogatja a fiókokat,* cserélje le ezt az értéket **szervezetekre.** Ha az alkalmazás támogatja *a szervezeti címtárban és a személyes Microsoft-fiókokban lévő fiókokat,* cserélje le ezt az értéket a **közös értékre.** Ha csak *a személyes Microsoft-fiókokra*szeretné korlátozni a támogatást, cserélje le ezt az értéket **a fogyasztókra**. |
>|Enter_the_Redirect_Uri_Here|Csere **http://localhost:4200**a következőre:|
>|cacheLocation (gyorsítótárhely)  | (Nem kötelező) Állítsa be a böngésző tárolóját a hitelesítési állapothoz. Az alapértelmezett a **sessionStorage**.   |
>|storeAuthStateInCookie  | (Nem kötelező) Azonosítsa a hitelesítési kérelem állapotát tároló könyvtárat. Ez az állapot szükséges a hitelesítési folyamatok érvényesítéséhez a böngésző cookie-kat. Ez a cookie az Internet Explorer és az Edge böngészőszámára van beállítva, hogy elférjen benne a két böngészőben. További részletek az [ismert problémákban.](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues->on-IE-and-Edge-Browser#issues) |
> > [!TIP]
> > Az **alkalmazás (ügyfél) azonosítója**, a **címtár (bérlő) azonosítója** és a **támogatott fióktípusok** értékét az alkalmazás **Áttekintés** oldalán találja az Azure Portalon.

Az elérhető konfigurálható beállításokról az [Ügyfélalkalmazások inicializálása](msal-js-initializing-client-applications.md)című témakörben talál további információt. 

Az MSAL.js tár forráskódját a GitHub [AzureAD/microsoft-authentication-library-for-js](https://github.com/AzureAD/microsoft-authentication-library-for-js) tártárában találja.

>[!div class="sxs-lookup" renderon="portal"]
>#### <a name="step-3-run-the-project"></a>3. lépés: A projekt futtatása

>[!div renderon="docs"]
>#### <a name="step-4-run-the-project"></a>4. lépés: A projekt futtatása

Node.js használata esetén:

1. Indítsa el a kiszolgálót a következő parancsok futtatásával a projekt könyvtárából:

   ```console
   npm install
   npm start
   ```

1. Tallózás a tallózáshoz. **http://localhost:4200/**
1. Válassza **a Bejelentkezés**lehetőséget.
1. Válassza **a Profil** lehetőséget a Microsoft Graph hívásához.

Miután a böngésző betölti az alkalmazást, válassza **a Bejelentkezés**lehetőséget. Amikor először kezd el bejelentkezni, a rendszer kéri, hogy adja meg a beleegyezését ahhoz, hogy az alkalmazás hozzáférhessen a profiljához, és bejelentkezhessen. Miután sikeresen bejelentkezett, válassza a **Profil**lehetőséget, és a felhasználói profil adatai megjelennek az oldalon.

## <a name="how-the-sample-works"></a>A minta működése

![A mintaalkalmazás működését bemutató diagram ebben a rövid útmutatóban](media/quickstart-v2-javascript/javascriptspa-intro.svg)


## <a name="next-steps"></a>További lépések

Ezután megtudhatja, hogyan jelentkeztetheti be a felhasználót, és hogyan szerezhet tokeneket az Angular oktatóanyagban:

> [!div class="nextstepaction"]
> [Szögletes bemutató](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-angular)

