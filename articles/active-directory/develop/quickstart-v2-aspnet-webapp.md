---
title: Microsoft Identity platform ASP.NET webkiszolgálói útmutató | Azure
description: Megismerheti, hogyan valósíthatja meg a Microsoft-bejelentkezéseket egy ASP.NET-webalkalmazásban az OpenID Connect segítségével.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1b4d33ca5f195d7b4aabc1bc905bd8db1406e8f
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964099"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Rövid útmutató: Microsoft-bejelentkezés hozzáadása egy ASP.NET-webalkalmazáshoz

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

Ebben a rövid útmutatóban megtudhatja, hogyan használhatók egy ASP.NET-webalkalmazásba való bejelentkezéshez személyes fiókok (hotmail.com, outlook.com stb.) és munkahelyi és iskolai fiókok bármilyen Azure Active Directory- (Azure AD-) példányból.

![Bemutatja, hogyan működik a rövid útmutatóban létrehozott minta alkalmazás](media/quickstart-v2-aspnet-webapp/aspnetwebapp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>A rövid útmutató mintaalkalmazásának regisztrálása és letöltése
> A rövid útmutató mintaalkalmazását kétféleképpen indíthatja el:
> * [Expressz] [1. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Kézi] [2. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>1\. lehetőség: Az alkalmazás regisztrálása és automatikus konfigurálása, majd a kódminta letöltése
>
> 1. Nyissa meg az új [Azure Portal-Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) ablaktáblát.
> 1. Adja meg az alkalmazás nevét, és kattintson a **Regisztráció** elemre.
> 1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>2\. lehetőség: Az alkalmazás és a kódminta regisztrálása és kézi konfigurálása
>
> #### <a name="step-1-register-your-application"></a>1\. lépés: Alkalmazás regisztrálása
> Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:
>
> 1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
> 1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
> 1. Navigáljon a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldalára.
> 1. Válassza az **új regisztráció**lehetőséget.
> 1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
>      - A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `ASPNET-Quickstart`).
>      - Adja hozzá `http://localhost:44368/` az **átirányítási URI**-ban, majd kattintson a **regisztráció**elemre.
>      - A kezelés szakasz bal oldali navigációs paneljén válassza a **hitelesítés** lehetőséget.
>          - Az **implicit támogatás** alszakasza alatt válassza az **azonosító tokenek**lehetőséget.
>          - Majd válassza a **Mentés**lehetőséget.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>1\. lépés: Az alkalmazás konfigurálása az Azure Portalon
> Ahhoz, hogy a rövid útmutatóhoz tartozó kódminta működjön, hozzá kell adnia egy válasz URL-t a következő formában: `https://localhost:44368/`.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [A módosítás alkalmazása]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Már konfigurált](media/quickstart-v2-aspnet-webapp/green-check.png) Az alkalmazása már konfigurálva van ezzel az attribútummal

#### <a name="step-2-download-your-project"></a>2\. lépés: A projekt letöltése

[A Visual Studio 2019-megoldás letöltése](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>3\. lépés: A Visual Studio-projekt konfigurálása

1. Csomagolja ki a zip-fájlt egy helyi mappába a gyökérmappa közelében (például: **C:\Azure-Samples**)
1. Nyissa meg a megoldást (AppModelv2-WebApp-OpenIDConnect-DotNet.sln) a Visual Studióban.
1. Előfordulhat, hogy a Visual Studio verziójától függően jobb gombbal a projektre kell kattintania `AppModelv2-WebApp-OpenIDConnect-DotNet` és **vissza kell állítania a NuGet-csomagokat**
1. Nyissa meg a Package Manager konzolját (View-> Egyéb Windows-> csomagkezelő konzol), és futtassa a következőt: `Update-Package Microsoft.CodeDom.Providers.DotNetCompilerPlatform -r`
1. Szerkessze a **Web.config** fájlt, és cserélje le a `ClientId` és `Tenant` paramétereket az alábbiakkal:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="Tenant" value="Enter_the_Tenant_Info_Here" />
    ```
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Ez a rövid útmutató támogatja a Enter_the_Supported_Account_Info_Here. 

> [!div renderon="docs"]
> Helyszín:
> - `Enter_the_Application_Id_here` – ez a regisztrált alkalmazás alkalmazásazonosítója.
> - `Enter_the_Tenant_Info_Here` – ez az alábbi lehetőségek egyike:
>   - Ha az alkalmazás **csak a saját szervezetet**támogatja, cserélje le ezt az értéket a **bérlői azonosító** vagy a **bérlő nevére** (például contoso.onmicrosoft.com).
>   - Ha az alkalmazás **bármely szervezeti címtárban lévő fiókot** támogat, ezt az értéket az `organizations` értékre cserélje le
>   - Ha az alkalmazás **minden Microsoft-fiókfelhasználót** támogat, ezt az értéket a `common` értékre cserélje le
>
> > [!TIP]
> > - Az *alkalmazásazonosító*, a *címtár (bérlő) azonosítója* és a *támogatott fióktípusok* értékét az **Áttekintés** oldalon találja
> > - Győződjön meg arról, hogy a **web. config** `redirectUri` értékének értéke megfelel az alkalmazás regisztrálásához az Azure ad-ben megadott **átirányítási URI** -nak (ha nem, navigáljon az alkalmazás regisztrálásához használt **hitelesítési** menüre, és frissítse az **átirányítási URI** -t egyeztetéshez)

## <a name="more-information"></a>További információ

Ez a szakasz a felhasználók bejelentkeztetéséhez szükséges kód áttekintését tartalmazza. Ez az Áttekintés hasznos lehet a kód működésének, a fő argumentumok, valamint a meglévő ASP.NET-alkalmazáshoz való bejelentkezés hozzáadásának megismeréséhez.

### <a name="owin-middleware-nuget-packages"></a>OWIN közbenső NuGet-csomagok

Az ASP.NET-ben az OpenID Connecttel végzett, cookie-alapú hitelesítési folyamatot OWIN közbenső csomagokkal valósíthatja meg. E csomagok telepítéséhez futtassa a következő parancsot a Visual Studio **csomagkezelő konzolján**:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb  
```

### <a name="owin-startup-class"></a>OWIN indítási osztály

A OWIN middleware olyan *indítási osztályt* használ, amely a üzemeltetési folyamat inicializálásakor fut. Ebben a rövid útmutatóban a gyökérmappa mappában található *Startup.cs* -fájl szerepel. Az alábbi kódban a jelen rövid útmutató által használt paraméter látható:

```csharp
public void Configuration(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());
    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            // Sets the ClientId, authority, RedirectUri as obtained from web.config
            ClientId = clientId,
            Authority = authority,
            RedirectUri = redirectUri,
            // PostLogoutRedirectUri is the page that users will be redirected to after sign-out. In this case, it is using the home page
            PostLogoutRedirectUri = redirectUri,
            Scope = OpenIdConnectScope.OpenIdProfile,
            // ResponseType is set to request the id_token - which contains basic information about the signed-in user
            ResponseType = OpenIdConnectResponseType.IdToken,
            // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
            // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
            // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter
            TokenValidationParameters = new TokenValidationParameters()
            {
                ValidateIssuer = false // Simplification (see note below)
            },
            // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = OnAuthenticationFailed
            }
        }
    );
}
```

> |Ahol  |  |
> |---------|---------|
> | `ClientId`     | Az Azure Portalon regisztrált alkalmazás azonosítója |
> | `Authority`    | A felhasználó által hitelesítendő STS-végpont. Általában <https://login.microsoftonline.com/{tenant}/v2.0> a nyilvános felhőknél, ahol a {tenant} a bérlő neve, a bérlő azonosítója, vagy *common* a közös végpontra való hivatkozáskor (több-bérlős alkalmazások esetében) |
> | `RedirectUri`  | URL-cím, amelyben a felhasználók a Microsoft Identity platform végpontjának hitelesítése után továbbítódnak |
> | `PostLogoutRedirectUri`     | Az URL-cím, ahová a rendszer átirányítja a felhasználókat kijelentkezés után |
> | `Scope`     | A kért hatókörök felsorolása, szóközökkel elválasztva |
> | `ResponseType`     | Arra vonatkozó kérés, hogy a hitelesítés válasza tartalmazzon egy azonosító jogkivonatot |
> | `TokenValidationParameters`     | A jogkivonatok érvényesítéséhez használatos paraméterek listája. Ebben az esetben a `ValidateIssuer` értéke `false`, ami azt jelzi, hogy a bejelentkezés bármilyen személyes, illetve munkahelyi vagy iskolai típusú fiókkal elfogadott. |
> | `Notifications`     | A különböző *OpenIdConnect* üzenetek esetében végrehajtható delegáltak listája |


> [!NOTE]
> A `ValidateIssuer = false` beállítása a rövid útmutató egyszerűsítése. A valós alkalmazásokban ellenőriznie kell a kiállítót.
> Ennek megértéséhez tekintse meg a mintákat.

### <a name="initiate-an-authentication-challenge"></a>Hitelesítő kérdés kezdeményezése

Előírhatja a felhasználóknak a bejelentkezést, ha a vezérlőben egy hitelesítő kérdést kezdeményez:

```csharp
public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties{ RedirectUri = "/" },
            OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

> [!TIP]
> A hitelesítő kérdés fenti módszerrel történő kérése nem kötelező, és jellemzően arra szokták használni, hogy egy nézetet a hitelesített és nem hitelesített felhasználóknak is elérhetővé tegyenek. Másik megoldásként a vezérlők is védhetők a következő szakaszban leírt módon.

### <a name="protect-a-controller-or-a-controllers-method"></a>Egy vezérlő vagy egy vezérlő metódusának védelme

A vezérlők vagy a vezérlők műveletei az `[Authorize]` attribútummal védhetők. Ez az attribútum úgy korlátozza a vezérlőhöz és a műveleteihez való hozzáférést, hogy csak a hitelesített felhasználóknak engedélyezi a vezérlő műveleteinek elérését. Ezt azt jelenti, hogy a hitelesítő kérdés automatikusan megjelenik, ha egy *nem hitelesített* felhasználó megpróbál hozzáférni az `[Authorize]` attribútummal jelölt bármely művelethez vagy vezérlőhöz.

## <a name="next-steps"></a>Következő lépések

Próbálja ki az ASP.NET-hez készült oktatóanyagot, amelyben teljes körű, részletes útmutatót talál az alkalmazások és új szolgáltatások létrehozásához, valamint megtalálja ennek a rövid útmutatónak a teljes magyarázatát is.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>A jelen rövid útmutatóban használt alkalmazás létrehozási lépéseinek ismertetése

> [!div class="nextstepaction"]
> [Bejelentkezési útmutató](./tutorial-v2-asp-webapp.md)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával.

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
