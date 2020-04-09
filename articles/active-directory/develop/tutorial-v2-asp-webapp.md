---
title: Bejelentkezés hozzáadása a Microsoft identity platformhoz ASP.NET webalkalmazáshoz
titleSuffix: Microsoft identity platform
description: A Microsoft bejelentkezésének megvalósítása ASP.NET megoldáson egy hagyományos webböngésző-alapú alkalmazás és az OpenID Connect szabvány használatával
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 08/28/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 017cc886b3d47a97a29f45979cb49215de31ae0a
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880975"
---
# <a name="add-sign-in-to-microsoft-to-an-aspnet-web-app"></a>Bejelentkezés hozzáadása a Microsofthoz egy ASP.NET webalkalmazáshoz

Ez az útmutató bemutatja, hogyan valósítható meg a Microsoftba való bejelentkezés egy ASP.NET MVC-megoldáson keresztül egy hagyományos webböngésző-alapú alkalmazás és az OpenID Connect használatával.

Miután befejezte ezt az útmutatót, az alkalmazás képes lesz elfogadni a bejelentkezések a személyes fiókok a szeret-ból outlook.com és live.com. Emellett a Microsoft identity platformmal integrált bármely vállalat vagy szervezet munkahelyi és iskolai fiókjai is be tudnak jelentkezni az alkalmazásba.

> Ez az útmutató a Microsoft Visual Studio 2019-et igényli.  Nincs telepítve?  [Töltse le ingyen a Visual Studio 2019-et.](https://www.visualstudio.com/downloads/)

>[!NOTE]
> Ha most ismeri a Microsoft identitásplatformot, javasoljuk, hogy kezdje a [Microsoft identity platform hozzáadása bejelentkezéssel egy ASP.NET webalkalmazásba.](quickstart-v2-aspnet-webapp.md)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Az útmutató által létrehozott mintaalkalmazás működése

![Bemutatja, hogyan működik az oktatóanyag által létrehozott mintaalkalmazás](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

A létrehozott mintaalkalmazás egy olyan forgatókönyvön alapul, amelyben a böngésző segítségével érheti el a ASP.NET webhelyet, amely egy bejelentkezési gombon keresztül kéri a felhasználót a hitelesítésre. A forgatókönyvben a webhely renderelésének nagy része a kiszolgálói oldalon történik.

## <a name="libraries"></a>Kódtárak

Ez az útmutató a következő könyvtárakat használja:

|Erőforrástár|Leírás|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Közbenső szoftver, amely lehetővé teszi az alkalmazások számára az OpenIdConnect hitelesítésre való használatát|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Middleware, amely lehetővé teszi, hogy egy alkalmazás cookie-k használatával fenntartsa a felhasználói munkamenetet|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Köztes szoftver, amely lehetővé teszi az OWIN-alapú alkalmazások futtatását az Internet Information Services (IIS) szolgáltatásban a ASP.NET kérelemfolyamat használatával|

## <a name="set-up-your-project"></a>A projekt beállítása

Ez a szakasz azt ismerteti, hogy miként telepítheti és konfigurálhatja a hitelesítési folyamatot az OWIN köztes szoftveren keresztül egy ASP.NET projekten az OpenID Connect használatával.

> Inkább a minta Visual Studio-projektjét szeretné letölteni? [Töltsön le egy projektet,](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) és ugorjon az [alkalmazás regisztrálása](#register-your-application) elemre a kódminta konfigurálásához a végrehajtás előtt.

### <a name="create-your-aspnet-project"></a>A ASP.NET projekt létrehozása

1. A Visual Studio: Ugrás az**Új** > **projekt fájljára.** **File** > 
2. A **Visual C#\Web** területen válassza az **ASP.NET Web Application (.NET Framework)** (ASP.NET-webalkalmazás (.NET-keretrendszer)) lehetőséget.
3. Nevezze el az alkalmazást, és kattintson az **OK** gombra.
4. Válassza **az Üres**lehetőséget, majd jelölje be a jelölőnégyzetet az **MVC-hivatkozások** hozzáadásához.

## <a name="add-authentication-components"></a>Hitelesítési összetevők hozzáadása

1. A Visual Studio: Go to Tools Nuget Package Manager Package Manager Console **(Eszközök** > **Nuget Csomagkezelő** > **csomagkezelő konzol) lapon.**
2. Az *OWIN közbenső szoftver NuGet-csomagjai* hozzáadásához írja az alábbiakat a Package Manager Console (Csomagkezelő konzol) ablakba:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Ezekről a könyvtárakról
> Ezek a tárak lehetővé teszik az egyszeri bejelentkezést (SSO) az OpenID Connect használatával cookie-alapú hitelesítéssel. Ha befejeződött a hitelesítés, és a rendszer elküldte a felhasználóhoz tartozó jogkivonatot az alkalmazásnak, az OWIN közbenső szoftver létrehoz egy munkameneti cookie-t. A böngésző ezután ezt a cookie-t használja a későbbi kéréseknél, így a felhasználónak nem kell újra beírnia a jelszót, és nincs szükség további ellenőrzésre.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>A hitelesítési folyamat konfigurálása

Az alábbi lépésekkel owin köztes szoftver indítási osztályt hozhat létre az OpenID Connect hitelesítés konfigurálásához. Ez az osztály automatikusan végrehajtásra kerül az IIS-folyamat indításakor.

> [!TIP]
> Ha a projekt gyökérmappája nem tartalmazza a `Startup.cs` fájlt:
> 1. Kattintson a jobb gombbal a projekt gyökérmappájára, majd válassza az > **Új elem** > hozzáadása**OWIN indítási** **osztályt**.<br/>
> 2. Nevezd **meg Startup.cs.**
>
>> Győződjön meg arról, hogy a kijelölt osztály egy OWIN indítási osztály, és nem egy szabványos C# osztály. Ezt erősítse meg a [assembly: OwinStartup(typeof({NameSpace}) című témakör ben. Indítás)]] a névtér felett.

1. *OwIN* és *Microsoft.IdentityModel* hivatkozások hozzáadása a Startup.cs:

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
        // The Client ID is used by the application to uniquely identify itself to Microsoft identity platform.
        string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

        // RedirectUri is the URL where the user will be redirected to after they sign in.
        string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

        // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
        static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

        // Authority is the URL for authority, composed by Microsoft identity platform endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
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
> A `ValidateIssuer = false` beállítás a rövid útmutató egyszerűsítése. A valós alkalmazásokban ellenőriznie kell a kibocsátót.
> Tekintse meg a mintákat, hogy megtanulják, hogyan kell csinálni.

<!--start-collapse-->
> ### <a name="more-information"></a>További információ
> Az *OpenIDConnectAuthenticationOptions* paraméterei koordinátákként szolgálnak az alkalmazás nak a Microsoft identity platformmal való kommunikációjához. Mivel az OpenID Connect köztes szoftver cookie-kat használ a háttérben, be kell állítania a cookie-hitelesítést is, ahogy az előző kód mutatja. A *ValidateIssuer* érték arra utasítja az OpenIdConnect-et, hogy ne korlátozza a hozzáférést egy adott szervezetre.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Vezérlő hozzáadása a bejelentkezési és kijelentkezési kérelmek kezeléséhez

Ha új vezérlőt szeretne létrehozni a bejelentkezési és kijelentkezési módszerek felfedéséhez, kövesse az alábbi lépéseket:

1.  Kattintson a jobb gombbal a Vezérlők mappára, és válassza a**Vezérlő** **hozzáadása parancsot.** >  **Controllers**
2.  Válassza az **MVC (.NET version) Controller – Empty** (MVC (.NET verzió) vezérlő – Üres) elemet.
3.  Válassza a **Hozzáadás** lehetőséget.
4.  Nevezze el **HomeControllernek,** majd válassza **a Hozzáadás lehetőséget.**
5.  OWIN-hivatkozások hozzáadása az osztályhoz:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```

6. Adja hozzá az alábbi két módszert a bejelentkezés és a kijelentkezés kezeléséhez a vezérlőhöz egy hitelesítési kihívás megakadásával:

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

## <a name="create-the-apps-home-page-for-user-sign-in"></a>Az alkalmazás kezdőlapjának létrehozása a felhasználói bejelentkezéshez

A Visual Studióban hozzon létre egy új nézetet a bejelentkezési gomb hozzáadásához és a felhasználói adatok hitelesítés utáni megjelenítéséhez:

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
>  Ez a lap hozzáad egy SVG formátumú bejelentkezés gombot, fekete háttérrel:<br/>![Bejelentkezés a Microsofttal](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> További bejelentkezési gombokért látogasson el a [Márkajelzési irányelvek be.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "Védjegyzési útmutató")
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Vezérlő hozzáadása a felhasználó jogcímének megjelenítéséhez
Ez a vezérlő bemutatja, hogy hogyan védheti meg a vezérlőt az `[Authorize]` attribútummal. Ez az attribútum korlátozza a hozzáférést a vezérlőhöz azáltal, hogy csak hitelesített felhasználók számára engedélyezi. A következő kód az attribútumot használja a bejelentkezés részeként lekért felhasználói jogcímek megjelenítéséhez:

1.  Kattintson a jobb gombbal a **Vezérlők** mappára, és válassza a**Vezérlő** **hozzáadása parancsot.** > 
2.  Válassza az **MVC {version} Controller – Empty** (MVC {verzió} vezérlő – Üres) elemet.
3.  Válassza a **Hozzáadás** lehetőséget.
4.  Adja neki a **ClaimsController** nevet.
5.  Cserélje le a vezérlőosztály kódját a következő kódra. Ezzel hozzáadja az `[Authorize]` attribútumot az osztályhoz:

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
> Az attribútum használata `[Authorize]` miatt a vezérlő összes metódusa csak akkor hajtható végre, ha a felhasználó hitelesítve van. Ha a felhasználó nincs hitelesítve, és megpróbál hozzáférni a vezérlőhöz, az OWIN hitelesítési kihívást kezdeményez, és a felhasználót hitelesítésre kényszeríti. Az előző kód a felhasználó azonosító jogkivonatában szereplő bizonyos felhasználói attribútumok jogcímeinek listáját vizsgálja. Ilyen attribútum például a felhasználó teljes neve és felhasználóneve, valamint a globális felhasználóazonosító tárgya. Emellett tartalmazza a *bérlőazonosítót* is, amely a felhasználó szervezetének azonosítóját jelöli. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Nézet létrehozása a felhasználó jogcímének megjelenítéséhez

A Visual Studióban hozzon létre egy új nézetet a felhasználói jogcímek weboldalon való megjelenítéséhez:

1.  Kattintson a jobb gombbal a **Nézetek\Jogcímek** mappára, és válassza **a Nézet hozzáadása parancsot.**
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

Az alkalmazás regisztrálásához és az alkalmazásregisztrációs adatok megoldáshoz való hozzáadásához két lehetősége van:

### <a name="option-1-express-mode"></a>1. lehetőség: Expressz mód

Az alkalmazás gyors regisztrálásához kövesse az alábbi lépéseket:

1. Nyissa meg az új [Azure Portal – Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) ablaktáblát.
1. Adja meg az alkalmazás nevét, majd kattintson a **Regisztráció** elemre.
1. Kövesse az utasításokat, hogy töltse le, és automatikusan konfigurálja az új alkalmazás egyetlen kattintással.

### <a name="option-2-advanced-mode"></a>2. lehetőség: Speciális mód

Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg a Visual Studio alkalmazást, majd:
   1. a Megoldáskezelőben jelölje ki a projektet, és tekintse meg a Tulajdonságok ablakot (ha nem látható a Tulajdonságok ablak, nyomja le az F4 billentyűt).
   1. Módosítsa az SSL-t engedélyezve `True`.
   1. Kattintson a jobb gombbal a projektre a Visual Studio alkalmazásban, válassza a **Tulajdonságok parancsot,** majd a **Web** lapot. A **Kiszolgálók szakaszban** módosítsa a **Projekt URL-címét** az **SSL URL-címére.**
   1. Másolja az SSL URL-címét. Ezt az URL-címet hozzá adja az átirányítási URL-ek listájához a regisztrációs portál átirányítási URL-ek listájában a következő lépésben.<br/><br/>![Projekt tulajdonságai](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiókkal.
1. Ha a fiók hozzáférést biztosít egynél több bérlőhöz, válassza ki a fiókját a jobb felső sarokban, és állítsa be a portálmunkamenetet a kívánt Azure AD-bérlőre.
1. Nyissa meg a Microsoft identity platform ot a [fejlesztőknek Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lapon.
1. Válassza **az Új regisztráció lehetőséget.**
1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
   1. A **Név szakaszban** adjon meg egy értelmes alkalmazásnevet, amely megjelenik az alkalmazás felhasználói számára, például **az ASPNET-tutorial**.
   1. Adja hozzá a Visual Studio programból másolt SSL-URL-címet (például `https://localhost:44368/`) a Válasz **URL-címében,** és válassza a **Regisztráció**lehetőséget.
1. Válassza a **Hitelesítés** menüt, válassza az **Azonosító tokenek** lehetőséget az **Implicit támogatás**csoportban, majd kattintson a **Mentés gombra.**
1. Adja hozzá a következőket a we.config fájlhoz, amely a `configuration\appSettings` szakasz gyökérmappájában található:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Cserélje `ClientId` le az imént regisztrált alkalmazásazonosítót.
1. Cserélje `redirectUri` le a projekt SSL URL-címére.

## <a name="test-your-code"></a>A kód tesztelése

Az alkalmazás Visual Studióban való teszteléséhez nyomja le az F5 billentyűt a projekt futtatásához. A böngésző a<span></span>http:// localhost:{port} helyre nyílik meg, és megjelenik a Bejelentkezés a **Microsofttal** gomb. Válassza ki a gombot a bejelentkezési folyamat elindításához.

Ha készen áll a teszt futtatására, használjon Azure AD-fiókot (munkahelyi vagy iskolai fiókot) vagy egy személyes Microsoft-fiókot (<span>élő.</span> com vagy <span>outlook.</span> com) a bejelentkezéshez.

![Bejelentkezés a Microsofttal](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Bejelentkezés Microsoft-fiókjába](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

<!--start-collapse-->
> ###  <a name="permissions-and-consent-in-the-microsoft-identity-platform-endpoint"></a>Engedélyek és hozzájárulás a Microsoft identity platform végpontjában
>  A Microsoft identity platformmal integrálható alkalmazások olyan engedélyezési modellt követnek, amely lehetővé teszi a felhasználók és a rendszergazdák számára az adatok elérésének szabályozását. Miután egy felhasználó hitelesíti magát a Microsoft identity platformmal az alkalmazás eléréséhez, a rendszer felkéri, hogy járuljon hozzá az alkalmazás által kért engedélyekhez ("Az alapprofil megtekintése" és a "Hozzáférés fenntartása az adatokhoz, amelyekhez hozzáférést adott neki"). Az engedélyek elfogadása után a felhasználó folytatja az alkalmazás eredményeit. A felhasználónak azonban a Felhasználó **szükséges hozzájárulási** lapja jelenik meg, ha az alábbi jelenségek valamelyike bekövetkezik:
>  > - Az alkalmazás fejlesztője hozzáadja a rendszergazdai jóváhagyást igénylő további **engedélyeket.**
>  > - Vagy a bérlő konfigurálva van **(az Enterprise Applications -> Felhasználói beállítások)** beállításban, ahol a felhasználók nem járulhatnak hozzá a vállalati adatokhoz való hozzáféréshez a nevükben.
>
> További információt a [Microsoft identity platform végpontján található Engedélyek és hozzájárulás című részben](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent)talál.
<!--end-collapse-->

#### <a name="view-application-results"></a>Alkalmazáseredmények megtekintése

A bejelentkezés után a rendszer átirányítja a felhasználót a webhely kezdőlapjára. A kezdőlap a Microsoft alkalmazásregisztrációs portálon az alkalmazásregisztrációs adatokban megadott HTTPS-URL-cím. A kezdőlap tartalmaz egy *"Hello \<user>"* üdvözlőüzenetet, egy kijelentkezésre mutató hivatkozást, valamint egy hivatkozást a felhasználó jogcímeinek megtekintéséhez. A felhasználó jogcímének hivatkozása a korábban létrehozott jogcímvezérlőhöz kapcsolódik.

### <a name="view-the-users-claims"></a>A felhasználó jogcímének megtekintése

A felhasználó jogcímének megtekintéséhez válassza a hivatkozást, amely entallózhat a vezérlőnézetben, amely csak a hitelesített felhasználók számára érhető el.

#### <a name="view-the-claims-results"></a>A jogcímek eredményeinek megtekintése

A vezérlőnézet megtallózása után meg kell jelennie egy táblázatnak, amely a felhasználó alapvető tulajdonságait tartalmazza:

|Tulajdonság |Érték |Leírás |
|---|---|---|
|**Név** |A felhasználó teljes neve | A felhasználó vezeték- és utóneve
|**Felhasználónév** |Felhasználó<span>@domain.com</span> | A felhasználó azonosítására használt felhasználónév|
|**Tárgy** |Tárgy |A felhasználót a weben keresztül egyedileg azonosító karakterlánc|
|**Bérlőazonosító** |Guid | A felhasználó Azure AD-szervezetét egyedileg képviselő **guid**|

Emellett meg kell jelennie egy táblázatot a hitelesítési kérelemben szereplő összes jogcímről. További információt az [azonosító jogkivonatban lévő jogcímek listájában talál.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Hozzáférés tesztelése olyan metódushoz, amely nek engedélyező attribútuma van (nem kötelező)

Ha névtelen felhasználóként szeretné tesztelni a hozzáférést egy `Authorize` attribútum által védett vezérlőhöz, kövesse az alábbi lépéseket:

1. Válassza ki a hivatkozást a felhasználó kijelentkezéséhez, és fejezze be a kijelentkezési folyamatot.
2. A böngészőben írja<span></span>be http:// localhost:{port}/claims azonosítót az `Authorize` attribútum által védett vezérlő eléréséhez.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>A védett vezérlőhöz való hozzáférés után várt eredmények

A rendszer kéri, hogy hitelesítse magát a védett vezérlőnézet használatához.

## <a name="advanced-options"></a>Speciális beállítások

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>A teljes webhely védelme
A teljes webhely védelme érdekében a **Global.asax** fájlban adja hozzá az `AuthorizeAttribute` attribútumot a `GlobalFilters` szűrőhöz a `Application_Start` módszerben:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Annak korlátozása, hogy ki jelentkezhet be az alkalmazásba

Alapértelmezés szerint az útmutató által létrehozott alkalmazás létrehozásakor az alkalmazás elfogadja a személyes fiókok (beleértve a outlook.com, live.com és másokat), valamint a Microsoft identity platformmal integrált vállalatok vagy szervezetek munkahelyi és iskolai fiókjait. Ez egy ajánlott beállítás SaaS-alkalmazásokhoz.

Az alkalmazás felhasználói bejelentkezési hozzáférésének korlátozásához több lehetőség is rendelkezésre áll.

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>1. lehetőség: A felhasználók korlátozása csak egy szervezet Active Directory-példányából az alkalmazásba való bejelentkezéshez (egybérlős)

Ezt a beállítást gyakran használják az *ÜZLETÁG-alkalmazásokhoz:* Ha azt szeretné, hogy az alkalmazás csak egy adott Azure AD-példányhoz tartozó fiókokból fogadja el a bejelentkezéseket (beleértve az adott példány *vendégfiókjait* is), kövesse az alábbi lépéseket:

1. A web.config fájlban módosítsa a `Tenant` paraméter `Common` értékét a szervezet bérlői `contoso.onmicrosoft.com`nevére, például .
2. Az [OWIN indítási osztályában](#configure-the-authentication-pipeline)állítsa az `ValidateIssuer` argumentumot a-ra. `true`

#### <a name="option-2-restrict-access-to-users-in-a-specific-list-of-organizations"></a>2. lehetőség: A szervezetek adott listájában szereplő felhasználók hozzáférésének korlátozása

A bejelentkezési hozzáférést csak azokra a felhasználói fiókokra korlátozhatja, amelyek az engedélyezett szervezetek listáján szereplő Azure AD-szervezetben találhatók:
1. Az [OWIN indítási osztályában](#configure-the-authentication-pipeline)állítsa az `ValidateIssuer` argumentumot a-ra. `true`
2. Állítsa be a `ValidIssuers` paraméter értékét az engedélyezett szervezetek listájára.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>3. lehetőség: Egyéni módszer használata a kibocsátók érvényesítéséhez

A **Kiállítóérvényesítő** paraméter használatával egyéni módszert valósíthat meg a kibocsátók érvényesítésére. A paraméter használatáról a [TokenValidationParameters osztály ban](/previous-versions/visualstudio/dn464192(v=vs.114))talál további információt.

## <a name="next-steps"></a>További lépések

További információ arról, hogy a webalkalmazások hogyan hívhatják meg a webes API-kat.

### <a name="learn-how-to-create-the-application-used-in-this-quickstart-guide"></a>Ismerje meg, hogyan hozhat létre az ebben a rövid útmutatóban használt alkalmazást

További információ a webes API-kat hívó webalkalmazásokról a Microsoft identitásplatformjával:

> [!div class="nextstepaction"]
> [Webes API-kat hívó webalkalmazások](scenario-web-app-sign-user-overview.md)

További információ a Microsoft Graph programot hívó webalkalmazások létrehozásáról:

> [!div class="nextstepaction"]
> [A Microsoft Graph ASP.NET oktatóanyag](https://docs.microsoft.com/graph/tutorials/aspnet)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Segítsen nekünk a Microsoft identitásplatformjának fejlesztésében. Mondja el, mit gondol egy kétkérdésű felmérés kitöltésével:

> [!div class="nextstepaction"]
> [Microsoft-identitásplatform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
