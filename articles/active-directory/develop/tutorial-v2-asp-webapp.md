---
title: Bejelentkezés hozzáadása az Azure AD ASP.NET-webalkalmazáshoz
titleSuffix: Microsoft identity platform
description: Microsoft-bejelentkezés megvalósítása ASP.NET-megoldásban hagyományos webböngésző-alapú alkalmazás és OpenID Connect standard használatával
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ff89d3c11ca88db14d2efd772be44aef7165a8a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964735"
---
# <a name="add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Bejelentkezés felvétele a Microsoftba ASP.NET-webalkalmazásba

Ez az útmutató bemutatja, hogyan valósítható meg a bejelentkezés a Microsoftnak egy ASP.NET MVC-megoldással egy hagyományos webböngésző-alapú alkalmazás és az OpenID Connect használatával.

Az útmutató elvégzése után az alkalmazás el tudja fogadni a személyes fiókok bejelentkezési adatait a outlook.com és a live.com. Továbbá a Azure Active Directory (Azure AD) szolgáltatással integrált vállalat vagy szervezet munkahelyi és iskolai fiókjai is bejelentkezhetnek az alkalmazásba.

> Ehhez az útmutatóhoz a Microsoft Visual Studio 2019 szükséges.  Nincs telepítve?  [Töltse le ingyen a Visual Studio 2019](https://www.visualstudio.com/downloads/)-es verzióját.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Az útmutató által létrehozott minta alkalmazás működése

![Bemutatja, hogyan működik az oktatóanyag által generált minta alkalmazás](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

A létrehozott minta alkalmazás egy olyan forgatókönyvön alapul, ahol a böngészővel hozzáférhet egy ASP.NET-webhelyhez, amely felszólítja a felhasználót, hogy jelentkezzen be a bejelentkezési gomb használatával. A forgatókönyvben a webhely renderelésének nagy része a kiszolgálói oldalon történik.

## <a name="libraries"></a>Kódtárak

Ez az útmutató a következő könyvtárakat használja:

|Részletes ismertetés|Leírás|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Közbenső szoftver, amely lehetővé teszi az alkalmazások számára az OpenIdConnect hitelesítésre való használatát|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware, amely lehetővé teszi az alkalmazások számára a felhasználói munkamenetek cookie-k használatával történő fenntartását|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Middleware, amely lehetővé teszi, hogy a OWIN-alapú alkalmazások Internet Information Services (IIS) fussanak a ASP.NET-kérési folyamat használatával|

## <a name="set-up-your-project"></a>A projekt beállítása

Ez a szakasz azt ismerteti, hogyan telepítheti és konfigurálhatja a hitelesítési folyamatot a OWIN middleware-n keresztül egy ASP.NET-projektben az OpenID Connect használatával.

> Inkább a minta Visual Studio-projektjét szeretné letölteni? [Töltsön le egy projektet](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) , és ugorjon az [alkalmazás regisztrálása](#register-your-application) a kód konfigurálásához a végrehajtás előtt.

### <a name="create-your-aspnet-project"></a>ASP.NET-projekt létrehozása

1. A Visual Studióban: lépjen a **fájl** > **új** > **projekt**elemre.
2. A **Visual C#\Web** területen válassza az **ASP.NET Web Application (.NET Framework)** (ASP.NET-webalkalmazás (.NET-keretrendszer)) lehetőséget.
3. Nevezze el az alkalmazást, és kattintson az **OK** gombra.
4. Válassza az **üres**lehetőséget, majd jelölje be a jelölőnégyzetet **MVC** -hivatkozások hozzáadásához.

## <a name="add-authentication-components"></a>Hitelesítési összetevők hozzáadása

1. A Visual Studióban: Nyissa meg a **Tools** > **Nuget Package Manager** > **csomagkezelő konzolját**.
2. Az *OWIN közbenső szoftver NuGet-csomagjai* hozzáadásához írja az alábbiakat a Package Manager Console (Csomagkezelő konzol) ablakba:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Tudnivalók ezekről a tárakról
> Ezek a kódtárak lehetővé teszik az egyszeri bejelentkezést (SSO) az OpenID Connect használatával, cookie-alapú hitelesítéssel. Ha befejeződött a hitelesítés, és a rendszer elküldte a felhasználóhoz tartozó jogkivonatot az alkalmazásnak, az OWIN közbenső szoftver létrehoz egy munkameneti cookie-t. A böngésző ezután ezt a cookie-t használja a későbbi kérelmekhez, így a felhasználónak nem kell újraírnia a jelszót, és nincs szükség további ellenőrzésre.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>A hitelesítési folyamat konfigurálása

A következő lépésekkel hozhat létre egy OWIN middleware indítási osztályt az OpenID Connect-hitelesítés konfigurálásához. Ezt az osztályt az IIS-folyamat indításakor automatikusan végrehajtja a rendszer.

> [!TIP]
> Ha a projekt gyökérmappája nem tartalmazza a `Startup.cs` fájlt:
> 1. Kattintson a jobb gombbal a projekt gyökérkönyvtárára, majd válassza a **hozzáadás** > **új elem** > **OWIN indítási osztály**elemet.<br/>
> 2. Nevezze el **Startup.cs**.
>
>> Győződjön meg arról, hogy a kiválasztott osztály egy OWIN indítási osztály, nem C# pedig egy standard osztály. Győződjön meg arról, hogy a következőt látja: [Assembly: OwinStartup (typeof ({névtér}). Indítás)] a névtér fölött.

1. Adja hozzá a *OWIN* és a *Microsoft. IdentityModel* hivatkozásokat a Startup.cs-hez:

    ```csharp
    using Microsoft.Owin;
    using Owin;
    using Microsoft.IdentityModel.Protocols.OpenIdConnect;
    using Microsoft.IdentityModel.Tokens;
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    using Microsoft.Owin.Security.Notifications;
    ```

2. Cserélje le az indítási osztályt a következő kódra:

    ```csharp
    public class Startup
    {
        // The Client ID is used by the application to uniquely identify itself to Azure AD.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Azure Active Directory v2.0 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
        string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, System.Configuration.ConfigurationManager.AppSettings["Authority"], tenant);

        /// <summary>
        /// Configure OWIN to use OpenIdConnect 
        /// </summary>
        /// <param name="app"></param>
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
                        ValidateIssuer = false // This is a simplification
                    },
                    // OpenIdConnectAuthenticationNotifications configures OWIN to send notification of failed authentications to OnAuthenticationFailed method
                    Notifications = new OpenIdConnectAuthenticationNotifications
                    {
                        AuthenticationFailed = OnAuthenticationFailed
                    }
                }
            );
        }

        /// <summary>
        /// Handle failed authentication requests by redirecting the user to the home page with an error in the query string
        /// </summary>
        /// <param name="context"></param>
        /// <returns></returns>
        private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> context)
        {
            context.HandleResponse();
            context.Response.Redirect("/?errormessage=" + context.Exception.Message);
            return Task.FromResult(0);
        }
    }
    ```

> [!NOTE]
> A `ValidateIssuer = false` beállítása a rövid útmutató egyszerűsítése. A valós alkalmazásokban ellenőriznie kell a kiállítót.
> Ennek megismeréséhez tekintse meg a mintákat.

<!--start-collapse-->
> ### <a name="more-information"></a>További információ
> Az *OpenIDConnectAuthenticationOptions* alatt megadott paraméterek koordinátaként szolgálnak az alkalmazás számára az Azure AD-vel való kommunikációhoz. Mivel az OpenID Connect middleware cookie-kat használ a háttérben, be kell állítania a cookie-hitelesítést is, mivel az előző kód jelenik meg. A *ValidateIssuer* érték azt jelzi, hogy a OpenIdConnect nem korlátozza a hozzáférést egy adott szervezethez.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Vezérlő hozzáadása a bejelentkezési és kijelentkezési kérelmek kezeléséhez

Ha új vezérlőt szeretne létrehozni a bejelentkezéshez és a kijelentkezési módszerekhez, kövesse az alábbi lépéseket:

1.  Kattintson a jobb gombbal a **vezérlők** mappára, majd válassza a > **vezérlő** **hozzáadása** elemet.
2.  Válassza az **MVC (.NET version) Controller – Empty** (MVC (.NET verzió) vezérlő – Üres) elemet.
3.  Válassza a **Hozzáadás** lehetőséget.
4.  Nevezze el **HomeController** , majd válassza a **Hozzáadás**lehetőséget.
5.  OWIN-hivatkozások hozzáadása a osztályhoz:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Adja hozzá a következő két módszert a bejelentkezéshez és a vezérlőhöz való kijelentkezéshez a hitelesítési kérdés elindításával:

    ```csharp
    /// <summary>
    /// Send an OpenID Connect sign-in request.
    /// Alternatively, you can just decorate the SignIn method with the [Authorize] attribute
    /// </summary>
    public void SignIn()
    {
        if (!Request.IsAuthenticated)
        {
            HttpContext.GetOwinContext().Authentication.Challenge(
                new AuthenticationProperties{ RedirectUri = "/" },
                OpenIdConnectAuthenticationDefaults.AuthenticationType);
        }
    }
    
    /// <summary>
    /// Send an OpenID Connect sign-out request.
    /// </summary>
    public void SignOut()
    {
        HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
    }
    ```

## <a name="create-the-apps-home-page-for-user-sign-in"></a>Az alkalmazás kezdőlapjának létrehozása felhasználói bejelentkezéshez

A Visual Studióban hozzon létre egy új nézetet a bejelentkezési gomb hozzáadásához és a felhasználói adatok megjelenítéséhez a hitelesítés után:

1.  Kattintson a jobb gombbal a **Views\Home** mappára, és válassza az **Add View** (Nézet hozzáadása) lehetőséget.
2.  Nevezze el az új nézet **indexét**.
3.  Adja hozzá a fájlhoz a következő, bejelentkezés gombot tartalmazó HTML-kódot:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Guide</title>
    </head>
    <body>
    @if (!Request.IsAuthenticated)
    {
        <!-- If the user is not authenticated, display the sign-in button -->
        <a href="@Url.Action("SignIn", "Home")" style="text-decoration: none;">
            <svg xmlns="http://www.w3.org/2000/svg" xml:space="preserve" width="300px" height="50px" viewBox="0 0 3278 522" class="SignInButton">
            <style type="text/css">.fil0:hover {fill: #4B4B4B;} .fnt0 {font-size: 260px;font-family: 'Segoe UI Semibold', 'Segoe UI'; text-decoration: none;}</style>
            <rect class="fil0" x="2" y="2" width="3174" height="517" fill="black" />
            <rect x="150" y="129" width="122" height="122" fill="#F35325" />
            <rect x="284" y="129" width="122" height="122" fill="#81BC06" />
            <rect x="150" y="263" width="122" height="122" fill="#05A6F0" />
            <rect x="284" y="263" width="122" height="122" fill="#FFBA08" />
            <text x="470" y="357" fill="white" class="fnt0">Sign in with Microsoft</text>
            </svg>
        </a>
    }
    else
    {
        <span><br/>Hello @System.Security.Claims.ClaimsPrincipal.Current.FindFirst("name").Value;</span>
        <br /><br />
        @Html.ActionLink("See Your Claims", "Index", "Claims")
        <br /><br />
        @Html.ActionLink("Sign out", "SignOut", "Home")
    }
    @if (!string.IsNullOrWhiteSpace(Request.QueryString["errormessage"]))
    {
        <div style="background-color:red;color:white;font-weight: bold;">Error: @Request.QueryString["errormessage"]</div>
    }
    </body>
    </html>
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>További információ
> Ezen az oldalon egy fekete háttérrel rendelkező bejelentkezési gomb található az SVG formátumban:<br/>![Bejelentkezés Microsoft-fiókkal](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> További bejelentkezési gombokért lépjen a [branding (védjegyezés) irányelvre](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Ba randing iránymutatásai ").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Vezérlő hozzáadása a felhasználói jogcímek megjelenítéséhez
Ez a vezérlő bemutatja, hogy hogyan védheti meg a vezérlőt az `[Authorize]` attribútummal. Ez az attribútum csak a hitelesített felhasználók engedélyezésével korlátozza a hozzáférést a vezérlőhöz. A következő kód az attribútum használatával jeleníti meg a bejelentkezés részeként beolvasott felhasználói jogcímeket:

1.  Kattintson a jobb gombbal a **vezérlők** mappára, majd válassza a > **vezérlő** **hozzáadása** elemet.
2.  Válassza az **MVC {version} Controller – Empty** (MVC {verzió} vezérlő – Üres) elemet.
3.  Válassza a **Hozzáadás** lehetőséget.
4.  Adja neki a **ClaimsController** nevet.
5.  Cserélje le a vezérlő osztály kódját a következő kódra. Ezzel hozzáadja a `[Authorize]` attribútumot a osztályhoz:

    ```csharp
    [Authorize]
    public class ClaimsController : Controller
    {
        /// <summary>
        /// Add user's claims to viewbag
        /// </summary>
        /// <returns></returns>
        public ActionResult Index()
        {
            var userClaims = User.Identity as System.Security.Claims.ClaimsIdentity;
    
            //You get the user’s first and last name below:
            ViewBag.Name = userClaims?.FindFirst("name")?.Value;
    
            // The 'preferred_username' claim can be used for showing the username
            ViewBag.Username = userClaims?.FindFirst("preferred_username")?.Value;
    
            // The subject/ NameIdentifier claim can be used to uniquely identify the user across the web
            ViewBag.Subject = userClaims?.FindFirst(System.Security.Claims.ClaimTypes.NameIdentifier)?.Value;
    
            // TenantId is the unique Tenant Id - which represents an organization in Azure AD
            ViewBag.TenantId = userClaims?.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid")?.Value;
    
            return View();
        }
    }
    ```

<!--start-collapse-->
> ### <a name="more-information"></a>További információ
> A `[Authorize]` attribútum használata miatt a vezérlő összes metódusát csak akkor lehet végrehajtani, ha a felhasználó hitelesítése megtörtént. Ha a felhasználó nincs hitelesítve, és megpróbál hozzáférni a vezérlőhöz, a OWIN hitelesítési kihívást kezdeményez, és kényszeríti a felhasználót a hitelesítésre. Az előző kód a felhasználó azonosító jogkivonatában szereplő adott felhasználói attribútumok jogcímeinek listáját vizsgálja. Ilyen attribútum például a felhasználó teljes neve és felhasználóneve, valamint a globális felhasználóazonosító tárgya. Emellett tartalmazza a *bérlőazonosítót* is, amely a felhasználó szervezetének azonosítóját jelöli. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Nézet létrehozása a felhasználó jogcímeinek megjelenítéséhez

A Visual Studióban hozzon létre egy új nézetet a felhasználói jogcímek weboldalon való megjelenítéséhez:

1.  Kattintson a jobb gombbal a **Views\Claims** mappára, majd válassza a **Nézet hozzáadása**elemet.
2.  Nevezze el az új nézet **indexét**.
3.  Adja hozzá a következő HTML-kódot a fájlhoz:

    ```html
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Sign in with Microsoft Sample</title>
        <link href="@Url.Content("~/Content/bootstrap.min.css")" rel="stylesheet" type="text/css" />
    </head>
    <body style="padding:50px">
        <h3>Main Claims:</h3>
        <table class="table table-striped table-bordered table-hover">
            <tr><td>Name</td><td>@ViewBag.Name</td></tr>
            <tr><td>Username</td><td>@ViewBag.Username</td></tr>
            <tr><td>Subject</td><td>@ViewBag.Subject</td></tr>
            <tr><td>TenantId</td><td>@ViewBag.TenantId</td></tr>
        </table>
        <br />
        <h3>All Claims:</h3>
        <table class="table table-striped table-bordered table-hover table-condensed">
        @foreach (var claim in System.Security.Claims.ClaimsPrincipal.Current.Claims)
        {
            <tr><td>@claim.Type</td><td>@claim.Value</td></tr>
        }
        </table>
        <br />
        <br />
        @Html.ActionLink("Sign out", "SignOut", "Home", null, new { @class = "btn btn-primary" })
    </body>
    </html>
    ```

## <a name="register-your-application"></a>Alkalmazás regisztrálása

Az alkalmazás regisztrálásához és az alkalmazás regisztrációs adatainak a megoldáshoz való hozzáadásához két lehetőség közül választhat:

### <a name="option-1-express-mode"></a>1\. lehetőség: expressz mód

Az alkalmazás gyors regisztrálásához kövesse az alábbi lépéseket:

1. Nyissa meg az új [Azure Portal-Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) ablaktáblát.
1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
1. Az új alkalmazás egyetlen kattintással való letöltéséhez és automatikus konfigurálásához kövesse az utasításokat.

### <a name="option-2-advanced-mode"></a>2\. lehetőség: speciális mód

Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg a Visual studiót, majd:
   1. Megoldáskezelő válassza ki a projektet, és tekintse meg a Tulajdonságok ablak (ha nem lát Tulajdonságok ablak, nyomja le az F4 billentyűt).
   1. Módosítsa az SSL-t a `True`re.
   1. Kattintson a jobb gombbal a projektre a Visual Studióban, válassza a **Tulajdonságok**lehetőséget, majd válassza a **Web (weblap** ) lapot. A **kiszolgálók** szakaszban módosítsa a **projekt URL** -címét az **SSL URL-címére**.
   1. Másolja az SSL-URL-címet. A következő lépésben hozzáadja ezt az URL-címet a regisztrációs portál átirányítási URL-címeinek listájához.<br/><br/>![Projekt tulajdonságai](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, illetve személyes Microsoft-fiók használatával.
1. Ha a fiókja több bérlőhöz biztosít hozzáférést, válassza ki a fiókját a jobb felső sarokban, és állítsa be a portál munkamenetét a kívánt Azure AD-bérlőre.
1. Nyissa meg a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapját.
1. Válassza az **új regisztráció**lehetőséget.
1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
   1. A **név** szakaszban adjon meg egy értelmezhető nevet, amely megjelenik az alkalmazás felhasználói számára, például az **ASPNET-tutorial**.
   1. Adja hozzá a Visual studióból másolt SSL-URL-címet az 1. lépésben (például `https://localhost:44368/`) a **Válasz URL-címében**, és válassza a **regisztráció**lehetőséget.
1. Válassza a **hitelesítés** menüt, válassza az **azonosító tokenek** lehetőséget az **implicit engedélyezés**területen, majd válassza a **Mentés**lehetőséget.
1. Adja hozzá a következőt a web. config fájlhoz, amely a `configuration\appSettings` szakasz gyökérkönyvtárában található:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Cserélje le a `ClientId`t az imént regisztrált alkalmazás-AZONOSÍTÓra.
1. Cserélje le a `redirectUri`t a projekt SSL URL-címére.

## <a name="test-your-code"></a>A kód tesztelése

Az alkalmazás Visual Studióban való teszteléséhez nyomja le az F5 billentyűt a projekt futtatásához. A böngésző megnyitja a http://<span></span>localhost: {Port} helyet, és megjelenik a **Bejelentkezés Microsoft-fiókkal** gomb. A bejelentkezési folyamat elindításához kattintson a gombra.

Ha készen áll a teszt futtatására, használjon Azure AD-fiókot (munkahelyi vagy iskolai fiókot) vagy egy személyes Microsoft-fiók (<span>élő.</span> com vagy <span>Outlook.</span> com) a bejelentkezéshez.

![Bejelentkezés Microsoft-fiókkal](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![jelentkezzen be a Microsoft-fiókba](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

<!--start-collapse-->
> ###  <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Engedélyek és beleegyezett a Microsoft Identity platform végpontjában
>  A Microsoft Identity platformmal integrált alkalmazások olyan engedélyezési modellt követnek, amely lehetővé teszi a felhasználók és a rendszergazdák számára az adatok elérésének szabályozását. Miután egy felhasználó az Azure AD-val az alkalmazáshoz fér hozzá, a rendszer kérni fogja, hogy az alkalmazás által kért engedélyekkel ("az alapszintű Profil megtekintése" és a "hozzáférés fenntartása az Ön által megadott adatokhoz") való hozzáférést kérje. Az engedélyek elfogadása után a felhasználó folytatja az alkalmazás eredményét. Előfordulhat azonban, hogy a felhasználót a **szükséges rendszergazdai engedélyekkel** rendelkező oldalon kell megadnia, ha a következők valamelyike történik:
>  > - Az alkalmazás fejlesztője olyan további engedélyeket ad hozzá, amelyekhez **rendszergazdai**jogosultság szükséges.
>  > - Vagy a bérlő konfigurálva van (a **vállalati alkalmazások – > felhasználói beállítások**), ahol a felhasználók nem engedélyezhetik az alkalmazások számára a vállalati adatok elérését a nevükben.
>
> További információkért tekintse meg [a Microsoft Identity platform végpontjának engedélyeit és](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)a hozzá tartozó hozzájárulásukat.
<!--end-collapse-->

#### <a name="view-application-results"></a>Alkalmazás eredményeinek megtekintése

A bejelentkezést követően a rendszer átirányítja a felhasználót a webhely kezdőlapján. A Kezdőlap a Microsoft Application Registration Portalon az alkalmazás regisztrációs adataiban megadott HTTPS URL-cím. A kezdőlapon szerepel a *"Hello \<user >"* üdvözlő üzenet, egy hivatkozás a kijelentkezéshez, valamint egy hivatkozás a felhasználó jogcímeinek megtekintéséhez. A felhasználó jogcímeinek hivatkozása a korábban létrehozott jogcím-vezérlőhöz csatlakozik.

### <a name="view-the-users-claims"></a>A felhasználó jogcímeinek megtekintése

A felhasználó jogcímeinek megtekintéséhez kattintson a hivatkozásra, és keresse meg a vezérlő nézetet, amely csak a hitelesített felhasználók számára érhető el.

#### <a name="view-the-claims-results"></a>A jogcímek eredményeinek megtekintése

A vezérlő nézet megkeresése után a felhasználó alapszintű tulajdonságait tartalmazó táblát kell látnia:

|Tulajdonság |Value (Díj) |Leírás |
|---|---|---|
|**Name (Név)** |Felhasználó teljes neve | A felhasználó vezeték- és utóneve
|**Felhasználónév** |felhasználói<span>@domain.com</span> | A felhasználó azonosítására használt Felhasználónév|
|**Tárgy** |Tárgy |Egy karakterlánc, amely egyedileg azonosítja a felhasználót a weben keresztül|
|**Bérlő azonosítója** |GUID | A felhasználó Azure AD-szervezetét egyedileg jelképező **GUID**|

Emellett meg kell jelennie a hitelesítési kérelemben szereplő összes jogcím táblájának. További információkért tekintse meg az [Azure ad ID-tokenben található jogcímek listáját](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Hozzáférés tesztelése egy engedélyező attribútummal rendelkező metódushoz (nem kötelező)

Ha névtelen felhasználóként szeretné tesztelni a hozzáférést a `Authorize` attribútum által védett vezérlőhöz, kövesse az alábbi lépéseket:

1. Válassza ki a felhasználó kijelentkezésére szolgáló hivatkozást, és fejezze be a kijelentkezési folyamatot.
2. A böngészőben írja be a http://<span></span>localhost: {Port}/jogcím elemet a `Authorize` attribútum által védett vezérlő eléréséhez.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Várt eredmények egy védett vezérlőhöz való hozzáférés után

A rendszer a védett vezérlő nézet használatára kéri a hitelesítést.

## <a name="advanced-options"></a>Speciális lehetőségek

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>A teljes webhely megóvása
A teljes webhely védelemmel való ellátásához a **Global. asax** fájlban adja hozzá a `AuthorizeAttribute` attribútumot a `GlobalFilters` szűrőhöz a `Application_Start` metódusban:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Annak korlátozása, hogy ki jelentkezhet be az alkalmazásba

Alapértelmezés szerint az útmutató alapján létrehozott alkalmazás létrehozásakor az alkalmazás elfogadja a személyes fiókok (például a outlook.com, a live.com és mások) bejelentkezési adatait, valamint a munkahelyi és iskolai fiókokat bármely olyan vállalattól vagy szervezettől, amely integrálva van Azure AD. Ez egy ajánlott megoldás az SaaS-alkalmazásokhoz.

Az alkalmazás felhasználói bejelentkezési hozzáférésének korlátozásához több lehetőség is rendelkezésre áll.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>1\. lehetőség: korlátozza, hogy a felhasználók csak egy szervezet Active Directory példányáról jelentkezzenek be az alkalmazásba (egy bérlő)

Ezt a beállítást gyakran használják *LOB-alkalmazásokhoz*: Ha azt szeretné, hogy az alkalmazás csak az adott Azure ad-példányhoz tartozó fiókokból fogadja a bejelentkezéseket (beleértve az adott példány *vendég fiókjait* is), kövesse az alábbi lépéseket:

1. A web. config fájlban módosítsa a `Tenant` paraméter értékét `Common`ról a szervezet bérlői nevére, például `contoso.onmicrosoft.com`.
2. A [OWIN indítási osztályában](#configure-the-authentication-pipeline)állítsa a `ValidateIssuer` argumentumot `true`re.

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>2\. lehetőség: a felhasználók hozzáférésének korlátozása a szervezetek egy adott listájában

A bejelentkezési hozzáférést csak azokra a felhasználói fiókokra korlátozhatja, amelyek egy olyan Azure AD-szervezetben találhatók, amely az engedélyezett szervezetek listáján található:
1. A [OWIN indítási osztályában](#configure-the-authentication-pipeline)állítsa a `ValidateIssuer` argumentumot `true`re.
2. Állítsa a `ValidIssuers` paraméter értékét az engedélyezett szervezetek listájára.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>3\. lehetőség: egyéni módszer használata a kibocsátók érvényesítéséhez

A **IssuerValidator** paraméter használatával egyéni módszert alkalmazhat a kiállítók érvényesítésére. További információ a paraméter használatáról: [TokenValidationParameters Class](/previous-versions/visualstudio/dn464192(v=vs.114)).

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogy a Web Apps hogyan hívhatja meg a webes API-kat.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart-guide"></a>Ismerje meg, hogyan hozhatja létre az ebben a rövid útmutatóban használt alkalmazást

További információ a webes API-kat hívó Microsoft Identity platformmal rendelkező webalkalmazásokról:

> [!div class="nextstepaction"]
> [Webes API-kat hívó webalkalmazások](scenario-web-app-sign-user-overview.md)

Megtudhatja, hogyan hozhat létre Microsoft Graph hívó webalkalmazásokat:

> [!div class="nextstepaction"]
> [Microsoft Graph ASP.NET-oktatóanyag](https://docs.microsoft.com/graph/tutorials/aspnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol a két kérdéses felmérés elvégzésével:

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
