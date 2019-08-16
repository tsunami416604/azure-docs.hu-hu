---
title: Azure AD v 2.0 ASP.NET webkiszolgáló – első lépések | Microsoft Docs
description: A Microsoft bejelentkezésének megvalósítása egy ASP.NET-megoldásban hagyományos webböngésző-alapú alkalmazással az OpenID Connect standard használatával
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
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: fce6ab2c5068d1628860356a9df0dd33c73948b3
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2019
ms.locfileid: "69511904"
---
# <a name="add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Bejelentkezés felvétele a Microsofttal egy ASP.NET-webalkalmazásba

Ez az útmutató bemutatja, hogyan valósítható meg a bejelentkezés a Microsofttal egy olyan ASP.NET MVC megoldással, amely egy hagyományos webböngésző-alapú alkalmazást használ az OpenID Connect használatával.

Az útmutató végén az alkalmazás képes lesz fogadni a személyes fiókok (például outlook.com, live.com és mások) bejelentkezési adatait. Ezek a fiókok minden olyan vállalattól vagy szervezettől származó munkahelyi és iskolai fiókot is tartalmaznak, amely integrálva van a Azure Active Directory.

> Ehhez az útmutatóhoz a Visual Studio 2019 szükséges.  Nincs telepítve?  [A Visual Studio 2019 ingyenes letöltése](https://www.visualstudio.com/downloads/)

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Az útmutató által létrehozott minta alkalmazás működése

![Bemutatja, hogyan működik az oktatóanyag által generált minta alkalmazás](media/active-directory-develop-guidedsetup-aspnetwebapp-intro/aspnetbrowsergeneral.svg)

A létrehozott minta alkalmazás azon forgatókönyv alapján érhető el, ahol a böngészővel hozzáfér egy olyan ASP.NET-webhelyhez, amely a felhasználó hitelesítését kéri egy bejelentkezési gomb használatával. A forgatókönyvben a webhely renderelésének nagy része a kiszolgálói oldalon történik.

## <a name="libraries"></a>Kódtárak

Ez az útmutató a következő könyvtárakat használja:

|Erőforrástár|Leírás|
|---|---|
|[Microsoft.Owin.Security.OpenIdConnect](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/)|Közbenső szoftver, amely lehetővé teszi az alkalmazások számára az OpenIdConnect hitelesítésre való használatát|
|[Microsoft.Owin.Security.Cookies](https://www.nuget.org/packages/Microsoft.Owin.Security.Cookies)|Közbenső szoftver, amely lehetővé teszi az alkalmazások számára a felhasználói munkamenet fenntartását cookie-k segítségével|
|[Microsoft.Owin.Host.SystemWeb](https://www.nuget.org/packages/Microsoft.Owin.Host.SystemWeb)|Lehetővé teszi az OWIN-alapú alkalmazások számára, hogy az IIS-en fussanak az ASP.NET kérési folyamat használatával|

## <a name="set-up-your-project"></a>A projekt beállítása

Ez a szakasz azokat a lépéseket mutatja be, amelyekkel telepítheti és konfigurálhatja a hitelesítési folyamatot a OWIN middleware használatával egy ASP.NET-projektben az OpenID Connect segítségével.

> Inkább a minta Visual Studio-projektjét szeretné letölteni? [Töltsön le egy projektet](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) , és ugorjon a [konfigurációs lépésre](#register-your-application) , hogy a végrehajtás előtt konfigurálja a kód mintát.

### <a name="create-your-aspnet-project"></a>ASP.NET-projekt létrehozása

1. A Visual Studióban:`File` > `New` > `Project`
2. A *vizuális C#\* területen válassza `ASP.NET Web Application (.NET Framework)`a elemet.
3. Nevezze el az alkalmazást, és kattintson *az OK* gombra.
4. A `Empty` hivatkozások hozzáadásához `MVC` jelölje be a jelölőnégyzetet, és jelölje be

## <a name="add-authentication-components"></a>Hitelesítési összetevők hozzáadása

1. A Visual Studióban:`Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Az *OWIN közbenső szoftver NuGet-csomagjai* hozzáadásához írja az alábbiakat a Package Manager Console (Csomagkezelő konzol) ablakba:

    ```powershell
    Install-Package Microsoft.Owin.Security.OpenIdConnect
    Install-Package Microsoft.Owin.Security.Cookies
    Install-Package Microsoft.Owin.Host.SystemWeb
    ```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Tudnivalók ezekről a tárakról
> A fenti kódtárak engedélyezik az egyszeri bejelentkezést (SSO) az OpenID Connecttel, cookie-alapú hitelesítés használatával. Ha befejeződött a hitelesítés, és a rendszer elküldte a felhasználóhoz tartozó jogkivonatot az alkalmazásnak, az OWIN közbenső szoftver létrehoz egy munkameneti cookie-t. A böngésző ezután ezt a cookie-t használja a további kérésekhez, így a felhasználónak nem kell újraírnia a jelszót, és nincs szükség további ellenőrzésre.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>A hitelesítési folyamat konfigurálása

Az alábbi lépések egy OWIN middleware indítási osztály létrehozásához használhatók az OpenID Connect-hitelesítés konfigurálásához. Ezt az osztályt az IIS-folyamat indításakor automatikusan végrehajtja a rendszer.

> [!TIP]
> Ha a projekt gyökérmappája nem tartalmazza a `Startup.cs` fájlt:
> 1. Kattintson a jobb gombbal a projekt legfelső mappájára: >`Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Adja neki a `Startup.cs` nevet.
>
>> Győződjön meg arról, hogy a kiválasztott osztály egy OWIN indítási osztály, és nem egy szabványos C#-osztály. Ehhez ellenőrizze, hogy látja-e a következőt a névtér fölött: `[assembly: OwinStartup(typeof({NameSpace}.Startup))]`.

1. Adja hozzá a *OWIN* és a *Microsoft. IdentityModel* hivatkozásait a `Startup.cs`következőhöz:

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

2. Cserélje le az indítási osztályt az alábbi kódra:

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
> A `ValidateIssuer = false` beállítás a rövid útmutató egyszerűsítése. A valós alkalmazásokban ellenőriznie kell a kiállítót, hogy megtudja, hogyan teheti meg a mintákat.

<!--start-collapse-->
> ### <a name="more-information"></a>További információ
> Az *OpenIDConnectAuthenticationOptions* alatt megadott paraméterek koordinátaként szolgálnak az alkalmazás számára az Azure AD-vel való kommunikációhoz. Mivel az OpenID Connect middleware a háttérben cookie-kat használ, a fenti programkódhoz is be kell állítania a cookie-hitelesítést. A *ValidateIssuer* értéke arra utasítja az OpenIdConnectet, hogy ne korlátozza a hozzáférést egy adott szervezetre.
<!--end-collapse-->

## <a name="add-a-controller-to-handle-sign-in-and-sign-out-requests"></a>Vezérlő hozzáadása a bejelentkezési és kijelentkezési kérelmek kezeléséhez

Ez a lépés bemutatja, hogyan hozhat létre új vezérlőt a bejelentkezéshez és a kijelentkezési módszerekhez.

1.  Kattintson a jobb `Controllers` gombbal a mappára, és válassza a`Add` > `Controller`
2.  Válassza a(z) `MVC (.NET version) Controller – Empty` lehetőséget.
3.  Kattintson a *Hozzáadás* gombra
4.  Nevezze el `HomeController` , majd kattintson a *Hozzáadás* gombra.
5.  *OWIN* -hivatkozások hozzáadása a osztályhoz:

    ```csharp
    using Microsoft.Owin.Security;
    using Microsoft.Owin.Security.Cookies;
    using Microsoft.Owin.Security.OpenIdConnect;
    ```
    
6. Adja hozzá az alábbi két módszert a bejelentkezéshez és a vezérlőhöz való kijelentkezéshez a következő programkódon keresztül:
    
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

## <a name="create-the-apps-home-page-to-sign-in-users-via-a-sign-in-button"></a>Az alkalmazás kezdőlapjának létrehozása a felhasználók bejelentkezési gombon keresztüli bejelentkezéséhez

A Visual Studióban hozzon létre egy új nézetet a bejelentkezés gomb hozzáadásához és a felhasználói adatok hitelesítés utáni megjelenítéséhez:

1.  Kattintson a jobb `Views\Home` gombbal a mappára, és válassza a`Add View`
2.  Nevezze el a következőképpen: `Index`.
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
> Ezen az oldalon egy fekete háttérrel rendelkező bejelentkezési gomb található az SVG formátumban:<br/>![Bejelentkezés Microsoft-fiókkal](media/active-directory-develop-guidedsetup-aspnetwebapp-use/aspnetsigninbuttonsample.png)<br/> További bejelentkezési gombokért lépjen a [jelen oldal](https://docs.microsoft.com/azure/active-directory/develop/active-directory-branding-guidelines "arculati iránymutatásaira").
<!--end-collapse-->

## <a name="add-a-controller-to-display-users-claims"></a>Vezérlő hozzáadása a felhasználói jogcímek megjelenítéséhez
Ez a vezérlő bemutatja, hogy hogyan védheti meg a vezérlőt az `[Authorize]` attribútummal. Ez az attribútum oly módon korlátozza a hozzáférést, hogy csak a hitelesített felhasználókat engedi hozzáférni a vezérlőhöz. Az alábbi kód az attribútum használatával jeleníti meg a bejelentkezés részeként beolvasott felhasználói jogcímeket.

1.  Kattintson a jobb `Controllers` gombbal a mappára:`Add` > `Controller`
2.  Válassza a(z) `MVC {version} Controller – Empty` lehetőséget.
3.  Kattintson a *Hozzáadás* gombra
4.  Adja neki a `ClaimsController` nevet.
5.  Cserélje le a vezérlő osztály kódját az alábbi kódra – ezzel hozzáadja az `[Authorize]` attribútumot az osztályhoz:

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
> Az `[Authorize]` attribútum használata miatt a vezérlő metódusait csak akkor lehet végrehajtani, ha a felhasználó hitelesítve van. Ha a felhasználó nincs hitelesítve, és megpróbál hozzáférni a vezérlőhöz, a OWIN hitelesítési kihívást kezdeményez, és kényszeríti a felhasználót a hitelesítésre. A fenti kód a felhasználó azonosító jogkivonatában szereplő adott felhasználói attribútumok jogcímeinek listáját vizsgálja. Ilyen attribútum például a felhasználó teljes neve és felhasználóneve, valamint a globális felhasználóazonosító tárgya. Emellett tartalmazza a *bérlőazonosítót* is, amely a felhasználó szervezetének azonosítóját jelöli. 
<!--end-collapse-->

## <a name="create-a-view-to-display-the-users-claims"></a>Nézet létrehozása a felhasználó jogcímeinek megjelenítéséhez

A Visual Studióban hozzon létre egy új nézetet a felhasználói jogcímek weboldalon való megjelenítéséhez:

1.  Kattintson a jobb `Views\Claims` gombbal a mappára, és:`Add View`
2.  Nevezze el a következőképpen: `Index`.
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

### <a name="option-1-express-mode"></a>1\. lehetőség: Expressz mód

Az alkalmazást gyorsan regisztrálhatja a következő módon:

1. Nyissa meg az új [Azure Portal-Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) ablaktáblát.
1. Adja meg az alkalmazás nevét, és kattintson a **Regisztráció** elemre.
1. Kövesse az új alkalmazás egy kattintással való letöltésére és automatikus konfigurálására vonatkozó utasításokat.

### <a name="option-2-advanced-mode"></a>2\. lehetőség: Speciális mód

Az alkalmazás regisztrálásához és az alkalmazás regisztrációs információinak a megoldáshoz való kézi hozzáadásához kövesse az alábbi lépéseket:

1. Nyissa meg a Visual studiót, és:
   1. Megoldáskezelő válassza ki a projektet, és tekintse meg a Tulajdonságok ablak (ha nem lát Tulajdonságok ablak, nyomja le az F4 billentyűt).
   1. Az SSL módosításának `True`engedélyezése a következőre:.
   1. Kattintson a jobb gombbal a projektre a Visual Studióban, majd válassza a **Tulajdonságok menüpontot**, majd a **web** (megjelenítés) lapot. A *kiszolgálók* szakaszban módosítsa a *projekt URL* -címét az SSL URL-címére.
   1. Másolja az SSL-URL-címet. A következő lépésben hozzáadja ezt az URL-címet a regisztrációs portál átirányítási URL-címeinek listájához:<br/><br/>![Projekt tulajdonságai](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
1. Navigáljon a Microsoft Identity platform for Developers [Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldalára.
1. Válassza az **új regisztráció**lehetőséget.
1. Amikor megjelenik az **Alkalmazás regisztrálása** lap, adja meg az alkalmazás regisztrációs adatait:
   1. A **Név** szakaszban adja meg az alkalmazás felhasználói számára megjelenített, jelentéssel bíró alkalmazásnevet (például `ASPNET-Tutorial`).
   1. Adja hozzá a Visual studióból másolt SSL URL-címet az 1 `https://localhost:44368/`. lépésben (például) a **Válasz URL-címében**, majd kattintson a **regisztráció**elemre.
1. Válassza a **Hitelesítés** menüt, állítsa be az **Azonosító jogkivonatok** értékét az **Implicit engedélyezés** területen, majd válassza a **Mentés** elemet.
1. Adja hozzá a következőt `web.config` a gyökérmappa alatt `configuration\appSettings`található gyökérkönyvtárban:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Cserélje `ClientId` le az t az imént regisztrált alkalmazás-azonosítóra.
1. Cserélje `redirectUri` le a értékét a projekt SSL URL-címére.

## <a name="test-your-code"></a>A kód tesztelése

Az alkalmazás Visual Studióban való teszteléséhez nyomja le az **F5** billentyűt a projekt futtatásához. A böngésző megnyitja a http://<span></span>localhost: {Port} helyet, és megjelenik a **Bejelentkezés Microsoft-fiókkal** gomb. A bejelentkezési folyamat elindításához kattintson a gombra.

Ha készen áll a teszt futtatására, használjon egy Microsoft Azure Active Directory (Azure AD) fiókot (munkahelyi vagy iskolai fiók) vagy egy személyes Microsoft-fiók (<span>élő.</span> com vagy <span>Outlook.</span> com) a bejelentkezéshez.

![Bejelentkezés Microsoft-fiókkal](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Jelentkezzen be a Microsoft-fiókba](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>Alkalmazás eredményeinek megtekintése

A bejelentkezést követően a rendszer átirányítja a felhasználót a webhely kezdőlapján. A Kezdőlap a Microsoft Application Registration Portalon az alkalmazás regisztrációs adataiban megadott HTTPS URL-cím. A kezdőlapon egy üdvözlő üzenet jelenik meg: *"Hello \<User >,"* a kijelentkezésre mutató hivatkozás, valamint egy hivatkozás, amelyen megtekintheti a felhasználó jogcímeit. A felhasználó jogcímeinek hivatkozása a korábban létrehozott *jogcím* -vezérlőre keres.

### <a name="browse-to-see-the-users-claims"></a>Tallózással megtekintheti a felhasználó jogcímeit

Ha szeretné megtekinteni a felhasználó jogcímeit, válassza ki azt a vezérlő nézetet, amely csak a hitelesített felhasználók számára érhető el.

#### <a name="view-the-claims-results"></a>A jogcímek eredményeinek megtekintése

A vezérlő nézet megkeresése után a felhasználó alapszintű tulajdonságait tartalmazó táblát kell látnia:

|Tulajdonság |Érték |Leírás |
|---|---|---|
|**Name** |Felhasználó teljes neve | A felhasználó vezetékneve és vezetékneve.
|**Felhasználónév** |felhasználói<span>@domain.com</span> | A felhasználó azonosítására szolgáló Felhasználónév.
|**Subject** |Subject |Egy karakterlánc, amely egyedileg azonosítja a felhasználót a weben keresztül.|
|**Bérlő azonosítója** |Guid | A felhasználó Azure AD-szervezetét egyedileg jelképező **GUID** .|

Emellett meg kell jelennie a hitelesítési kérelemben szereplő összes jogcím táblájának. További információkért tekintse meg az [Azure ad ID-tokenben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)található jogcímek listáját.

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Hozzáférés tesztelése egy engedélyező attribútummal rendelkező metódushoz (nem kötelező)

Ha a hozzáférést névtelen felhasználóként szeretné tesztelni az `Authorize` attribútummal védett vezérlőn, kövesse az alábbi lépéseket:

1. Válassza ki a felhasználó kijelentkezésére szolgáló hivatkozást, és fejezze be a kijelentkezési folyamatot.
2. A böngészőben írja be a http://<span></span>localhost: {Port}/jogcím kifejezést az `Authorize` attribútummal védett vezérlő eléréséhez.

#### <a name="expected-results-after-access-to-a-protected-controller"></a>Várt eredmények egy védett vezérlőhöz való hozzáférés után

A rendszer a védett vezérlő nézet használatára kéri a hitelesítést.

## <a name="advanced-options"></a>Speciális beállítások

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>A teljes webhely megóvása
A teljes webhely védelemmel való ellátásához a **Global. asax** fájlban adja hozzá az `AuthorizeAttribute` attribútumot a `GlobalFilters` szűrőhöz a `Application_Start` metódusban:

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>Annak korlátozása, hogy ki jelentkezhet be az alkalmazásba

Alapértelmezés szerint az útmutató alapján létrehozott alkalmazás létrehozásakor az alkalmazás elfogadja a személyes fiókok (például a outlook.com, a live.com és mások) bejelentkezési adatait, valamint a munkahelyi és iskolai fiókokat bármely olyan vállalattól vagy szervezettől, amely integrálva van a következővel: Azure Active Directory. Ez egy ajánlott megoldás az SaaS-alkalmazásokhoz.

Az alkalmazás felhasználói bejelentkezési hozzáférésének korlátozásához több lehetőség is rendelkezésre áll:

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>1\. lehetőség: Csak egy adott szervezet Active Directory-példányának felhasználói jelentkezhetnek be az alkalmazásba (egybérlős)

Ez a beállítás a *LOB-alkalmazások*gyakori forgatókönyve: Ha azt szeretné, hogy az alkalmazás csak olyan fiókokból fogadja el a bejelentkezéseket, amelyek egy adott Azure Active Directory-példányhoz tartoznak (beleértve az adott példányhoz tartozó *vendég fiókokat* is), tegye a következőket:

1. A **web. config** fájlban módosítsa a `Tenant` paraméter `Common` értékét a szervezet bérlői `contoso.onmicrosoft.com`nevére, például:.
2. A [OWIN indítási osztályában](#configure-the-authentication-pipeline)állítsa be az `ValidateIssuer` argumentumot `true`a következőre:.

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>2\. lehetőség: Az alkalmazáshoz való hozzáférés korlátozása a szervezetek adott listáján lévő felhasználók számára

A bejelentkezési hozzáférést csak olyan felhasználói fiókokra korlátozhatja, amelyek egy olyan Azure AD-szervezetben találhatók, amely az engedélyezett szervezetek listáján található:
1. A [OWIN indítási osztályában](#configure-the-authentication-pipeline)állítsa be az `ValidateIssuer` argumentumot `true`a következőre:.
2. Állítsa be a `ValidIssuers` paraméter értékét az engedélyezett szervezetek listájára.

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>3\. lehetőség: Egyéni módszer használata a kibocsátók érvényesítéséhez

A **IssuerValidator** paraméter használatával egyéni módszert alkalmazhat a kiállítók érvényesítésére. A paraméter használatáról további információt a [TokenValidationParameters osztályról](/previous-versions/visualstudio/dn464192(v=vs.114))szóló cikkben olvashat.

## <a name="next-steps"></a>További lépések

Tudnivalók a webes API-kat hívó webalkalmazásokról:.

### <a name="learn-the-steps-to-create-the-application-used-in-this-quickstart"></a>A jelen rövid útmutatóban használt alkalmazás létrehozási lépéseinek ismertetése

> [!div class="nextstepaction"]
> [Webes API-kat hívó webalkalmazások]( https://aka.ms/msal-net-authorization-code)

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Segítsen nekünk a Microsoft Identity platform fejlesztésében. Mondja el, mit gondol egy rövid, kétkérdéses felmérés végrehajtásával.

> [!div class="nextstepaction"]
> [Microsoft Identity platform-felmérés](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)