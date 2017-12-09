
## <a name="set-up-your-project"></a>A projekt beállítása

Ez a szakasz ismerteti a lépéseket, telepítését és konfigurálását a hitelesítési folyamatot OWIN köztes keresztül egy ASP.NET-projekt OpenID Connect használatával. 

> Ez a minta Visual Studio-projekt letöltése helyette inkább? [Töltse le a projekt](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-DotNet/archive/master.zip) és ugorjon a [konfigurációs lépés](#create-an-application-express) konfigurálása a kódminta végrehajtása előtt.

<!--start-collapse-->
> ### <a name="create-your-aspnet-project"></a>Az ASP.NET-projekt létrehozása

> 1. A Visual Studio:`File` > `New` > `Project`<br/>
> 2. A *Visual C# \Web*, jelölje be `ASP.NET Web Application (.NET Framework)`.
> 3. Az alkalmazás neve, és kattintson a *OK*
> 4. Válassza ki `Empty` hozzáadása jelölőnégyzet bejelölésével és `MVC` hivatkozások
<!--end-collapse-->

## <a name="add-authentication-components"></a>Hitelesítés-összetevők hozzáadása

1. A Visual Studio:`Tools` > `Nuget Package Manager` > `Package Manager Console`
2. Adja hozzá *OWIN köztes NuGet-csomagok* , a Package Manager Console ablakban írja be a következő:

```powershell
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
```

<!--start-collapse-->
> ### <a name="about-these-libraries"></a>Ezek a kódtárak kapcsolatos

>A fenti könyvtárak engedélyezése az egyszeri bejelentkezés (SSO) használata az OpenID Connect cookie-alapú hitelesítéssel. Miután a hitelesítés végbemegy, és a felhasználó jelképező jogkivonatot kap az alkalmazáshoz, OWIN köztes egy munkamenetcookie-t hoz létre. A böngésző a felhasználónak nem kell írnia a jelszavát, és nincs további ellenőrzés van szükség, majd használja a cookie későbbi kérelmeket.
<!--end-collapse-->

## <a name="configure-the-authentication-pipeline"></a>A hitelesítési folyamatot konfigurálása
Az alábbi lépéseket egy OWIN indítási osztály konfigurálása az OpenID Connect hitelesítési köztes létrehozásához használt. Ez az osztály a lesz automatikusan végre, amikor az IIS-folyamat elindul.

> Ha a projekt nem rendelkezik egy `Startup.cs` fájl a gyökérmappában található:<br/>
> 1. Kattintson jobb gombbal a projekt gyökérmappához: >`Add` > `New Item...` > `OWIN Startup class`<br/>
> 2. Nevezze el`Startup.cs`

> Győződjön meg arról, hogy a kiválasztott osztály egy OWIN indítási osztályt és a nem szabványos C#-osztály. Ellenőrizheti, ha ellenőrzésével `[assembly: OwinStartup(typeof({NameSpace}.Startup))]` fent a névteret.


1. Adja hozzá *OWIN* és *Microsoft.IdentityModel* hivatkozása `Startup.cs`:

```csharp
using Microsoft.Owin;
using Owin;
using Microsoft.IdentityModel.Protocols;
using Microsoft.Owin.Security;
using Microsoft.Owin.Security.Cookies;
using Microsoft.Owin.Security.OpenIdConnect;
using Microsoft.Owin.Security.Notifications;
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
<li>
Indítási osztály cserélje le az alábbi kódot:
</li>
</ol>

```csharp
public class Startup
{        
    // The Client ID is used by the application to uniquely identify itself to Azure AD.
    string clientId = System.Configuration.ConfigurationManager.AppSettings["ClientId"];

    // RedirectUri is the URL where the user will be redirected to after they sign in.
    string redirectUri = System.Configuration.ConfigurationManager.AppSettings["RedirectUri"];

    // Tenant is the tenant ID (e.g. contoso.onmicrosoft.com, or 'common' for multi-tenant)
    static string tenant = System.Configuration.ConfigurationManager.AppSettings["Tenant"];

    // Authority is the URL for authority, composed by Azure Active Directory v2 endpoint and the tenant name (e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0)
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
                Scope = OpenIdConnectScopes.OpenIdProfile,
                // ResponseType is set to request the id_token - which contains basic information about the signed-in user
                ResponseType = OpenIdConnectResponseTypes.IdToken,
                // ValidateIssuer set to false to allow personal and work accounts from any organization to sign in to your application
                // To only allow users from a single organizations, set ValidateIssuer to true and 'tenant' setting in web.config to the tenant name
                // To allow users from only a list of specific organizations, set ValidateIssuer to true and use ValidIssuers parameter 
                TokenValidationParameters = new System.IdentityModel.Tokens.TokenValidationParameters() { ValidateIssuer = false },
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
<!--start-collapse-->
> ### <a name="more-information"></a>További információ

> Megadja a paraméterek *OpenIDConnectAuthenticationOptions* kommunikálni az Azure AD alkalmazás-koordináták szolgálnak. Az OpenID Connect köztes cookie-kat használ, a háttérben, mert akkor is be kell állítania cookie-hitelesítés, a fent látható kódot. A *ValidateIssuer* értéke közli OpenIdConnect úgy nem korlátozza a hozzáférést egy adott szervezet számára.
<!--end-collapse-->

