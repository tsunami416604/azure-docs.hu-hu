---
title: Webes API-kat meghívjaó webalkalmazás konfigurálása – Microsoft identity platform | Azure
description: A webes API-kat meghívjaó webalkalmazások kódjának konfigurálása
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b0f60e13ca4bc5115f9a49885c3c659ad1147fcc
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881893"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Webes API-kat meghívjaó webalkalmazás: Kódkonfiguráció

Ahogy az a [webalkalmazásban látható, amely a felhasználók forgatókönyvében jelentkezik,](scenario-web-app-sign-user-overview.md) a webalkalmazás az [OAuth 2.0 engedélyezési kódfolyamatot](v2-oauth2-auth-code-flow.md) használja a felhasználó bejelentkezéséhez. Ennek a folyamatnak két lépése van:

1. Kérjen engedélyezési kódot. Ez a rész a felhasználóval folytatott privát párbeszédet a Microsoft identitásplatformra delegálja. A párbeszéd során a felhasználó bejelentkezik, és hozzájárul a webes API-k használatához. Ha a privát párbeszéd sikeresen befejeződik, a webalkalmazás kap egy engedélyezési kódot az átirányítási URI-n.
1. Kérjen hozzáférési jogkivonatot az API-hoz az engedélyezési kód beváltásával.

A [felhasználói forgatókönyvekben bejelentkező webalkalmazás](scenario-web-app-sign-user-overview.md) csak az első lépést fedte le. Itt megtudhatja, hogyan módosíthatja a webalkalmazást úgy, hogy az ne csak a felhasználókat, hanem most antól webes API-kat is meghívja.

## <a name="libraries-that-support-web-app-scenarios"></a>Webalkalmazás-forgatókönyveket támogató tárak

A Microsoft Authentication Library (MSAL) következő tárak támogatják a webalkalmazások engedélyezési kódfolyamatát:

| MSAL könyvtár | Leírás |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | A . Nem támogatott az univerzális Windows platform (UWP), a Xamarin.iOS és a Xamarin.Android, mivel ezeket a platformokat nyilvános ügyfélalkalmazások készítésére használják. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> Pythonhoz készült MSAL | Python-webalkalmazások támogatása. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> Javához készült MSAL | Java webes alkalmazások támogatása. |

Válassza ki annak a platformnak a lapját, amely érdekli:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Mivel a felhasználói bejelentkezés delegálása az Open ID connect (OIDC) köztes szoftverhez van delegálva, az OIDC-folyamattal kell együttműködnie. Az interakció módja a használt keretrendszertől függ.

A ASP.NET Core, akkor iratkozz fel middleware OIDC események:

- Az Open ID Connect köztes szoftver segítségével engedélyezheti a ASP.NET Core-t. ASP.NET vagy ASP.NET Core lehetővé teszi a felhasználó számára, hogy jelentkezzen be és járul hozzá.
- Elő kell fizetnie a webalkalmazásra, hogy megkapja az engedélyezési kódot. Ez az előfizetés egy C# delegálás használatával történik.
- Az engedélyezési kód beérkezésekor az MSAL-könyvtárakat fogja használni a beváltáshoz. Az eredményül kapott hozzáférési jogkivonatok és a frissítési jogkivonatok a jogkivonat-gyorsítótárban tárolódnak. A gyorsítótár az alkalmazás más részein, például a vezérlőkben is használható más jogkivonatok csendes beszerzésére.

Kód példák ebben a cikkben, és a következő egy kivont [a ASP.NET Core web app növekményes bemutató, 2.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph) Előfordulhat, hogy a teljes megvalósítási részletekért olvassa el az oktatóanyagot.

> [!NOTE]
> Ahhoz, hogy teljes mértékben megértsük a kód példák itt, meg kell [ismernie ASP.NET alapvető alapjait](https://docs.microsoft.com/aspnet/core/fundamentals), és különösen a [függőség injekció](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) és [lehetőségek](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Mivel a felhasználói bejelentkezés delegálása az Open ID connect (OIDC) köztes szoftverhez van delegálva, az OIDC-folyamattal kell együttműködnie. Az interakció módja a használt keretrendszertől függ.

A ASP.NET, akkor iratkozz fel middleware OIDC események:

- Az Open ID Connect köztes szoftver segítségével engedélyezheti a ASP.NET Core-t. ASP.NET vagy ASP.NET Core lehetővé teszi a felhasználó számára, hogy jelentkezzen be és járul hozzá.
- Elő kell fizetnie a webalkalmazásra, hogy megkapja az engedélyezési kódot. Ez az előfizetés egy C# delegálás használatával történik.
- Az engedélyezési kód beérkezésekor az MSAL-könyvtárakat fogja használni a beváltáshoz. Az eredményül kapott hozzáférési jogkivonatok és a frissítési jogkivonatok a jogkivonat-gyorsítótárban tárolódnak. A gyorsítótár az alkalmazás más részein, például a vezérlőkben is használható más jogkivonatok csendes beszerzésére.

A cikkben szereplő kódpéldák és az alábbi példák a [ASP.NET webalkalmazás-mintából származnak.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) Előfordulhat, hogy a teljes megvalósítási részletekért tekintse meg ezt a mintát.

# <a name="java"></a>[Java](#tab/java)

A cikkben szereplő kódpéldák és a következő kódak a [Microsoft Graph webalkalmazásnak nevezett Java webalkalmazásból](https://github.com/Azure-Samples/ms-identity-java-webapp)származnak, amely az MSAL java-t használó webalkalmazás-minta.
A minta jelenleg lehetővé teszi, hogy az MSAL java-hoz létre az engedélyezési kód URL-címét, és kezeli a microsoftos identitásplatform engedélyezési végpontjára történő navigációt. A Sprint biztonsági szolgálatával is bejelentkeztetheti a felhasználót. Előfordulhat, hogy a teljes megvalósítási részletekért tekintse meg a mintát.

# <a name="python"></a>[Python](#tab/python)

A cikkben szereplő kódpéldák és a következő példák a [Python webalkalmazásból](https://github.com/Azure-Samples/ms-identity-python-webapp)származnak, amelyben a Microsoft Graph, egy MSAL-t használó webalkalmazás-minta. Python.
A minta jelenleg lehetővé teszi MSAL. A Python létrehozza az engedélyezési kód URL-címét, és kezeli a Microsoft identity platform engedélyezési végpontjára történő navigációt. Előfordulhat, hogy a teljes megvalósítási részletekért tekintse meg a mintát.

---

## <a name="code-that-redeems-the-authorization-code"></a>Az engedélyezési kódot visszaváltó kód

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

A ASP.NET Core `Startup.cs` fájlban előfizet `OnAuthorizationCodeReceived` az OpenID Connect eseményre. Ebből az eseményből `AcquireTokenFromAuthorizationCode` hívja meg a MSAL.NET metódust. Ez a módszer a következő jogkivonatokat tárolja a jogkivonat-gyorsítótárban:

- A kért `scopes` *hozzáférési jogkivonat.*
- Frissítési *token*. Ez a jogkivonat a rendszer a hozzáférési jogkivonat frissítésére szolgál, ha közel van a lejárathoz, vagy egy másik jogkivonatot kap ugyanannak a felhasználónak a nevében, de egy másik erőforráshoz.

A [ASP.NET Core webalkalmazás oktatóanyaga](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) újrafelhasználható kódot biztosít a webalkalmazásokhoz.

A következőkben a [Startup.cs#L40-L42 kódja látható.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42) Ez jellegét meghatározza kéri, hogy:

- A `AddMicrosoftIdentityPlatformAuthentication` módszer, amely hitelesítést ad a webalkalmazáshoz.
- A `AddMsal` módszer, amely hozzáadja a webes API-k hívásának képességét.
- A `AddInMemoryTokenCaches` módszer, amely a token-cache implementáció kiválasztásáról szól.

```csharp
public class Startup
{
  // Code not shown here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token-cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not shown here
}
```

`Constants.ScopeUserRead`az [Állandók.cs#L5:](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Már tanulmányozta a `AddMicrosoftIdentityPlatformAuthentication` [webalkalmazás tartalmát, amely a felhasználókban jelentkezik - kódkonfiguráció](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code).

### <a name="the-addmsal-method"></a>Az AddMsal módszer

A kód `AddMsal` a [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159)webhelyen található.

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the web app or web API.
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection.
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign in by using their personal Microsoft accounts.
          // (It's required by MSAL.NET and automatically provided when users sign in by using work or school accounts.)
          options.Scope.Add("offline_access");
          if (initialScopes != null)
          {
              foreach (string scope in initialScopes)
              {
                  if (!options.Scope.Contains(scope))
                  {
                      options.Scope.Add(scope);
                  }
              }
          }

          // Handle the auth redemption by MSAL.NET so that a token is available in the token cache,
          // where it will be usable from controllers later (by means of the TokenAcquisition service).
          var handler = options.Events.OnAuthorizationCodeReceived;
          options.Events.OnAuthorizationCodeReceived = async context =>
          {
              var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
              await tokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync(context, options.Scope).ConfigureAwait(false);
              await handler(context).ConfigureAwait(false);
          };
      });
      return services;
  }
}
```

A `AddMsal` módszer biztosítja, hogy:

- A ASP.NET Core webalkalmazás egy azonosító jogkivonatot és`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`egy hitelesítési kódot ( ) is kér.
- A `offline_access` hatókör hozzáadódik. Ez a hatókör felhasználói jóváhagyást kap az alkalmazás hoz egy frissítési jogkivonatot.
- Az alkalmazás előfizet az `OnAuthorizationCodeReceived` OIDC eseményre, és a hívást MSAL.NET használatával váltja be, amely `ITokenAcquisition`itt egy újrafelhasználható összetevőbe van beágyazva.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>A TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync metódus

A `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` metódus a [Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145)webhelyen található. Biztosítja, hogy:

- ASP.NET nem próbálja meg a hitelesítési kódot`context.HandleCodeRedemption();`a MSAL.NET ( )
- Az azonosító jogkivonatban lévő jogcímek az MSAL számára érhetők el a felhasználó fiókjához egy token gyorsítótárkulcs kiszámításához.
- Szükség esetén létrejön a MSAL.NET alkalmazás egy példánya.
- A kódot a MSAL.NET váltja be.
- Az új azonosító jogkivonat meg van osztva ASP.NET Core-szal a hívás `context.HandleCodeRedemption(null, result.IdToken);`során. A hozzáférési jogkivonat nincs megosztva ASP.NET Core.The access token is not shared with ASP.NET Core. A felhasználóhoz társított MSAL.NET tokengyorsítótárban marad, ahol készen áll a ASP.NET Core vezérlőkben való használatra.

Itt a megfelelő `TokenAcquisition`kódot:

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  string[] scopesRequestedByMsalNet = new string[]{ "openid", "profile", "offline_access" };

  // Code omitted here for clarity


  public async Task AddAccountToCacheFromAuthorizationCodeAsync(AuthorizationCodeReceivedContext context, IEnumerable<string> scopes)
  {
   // Code omitted here for clarity

    try
    {
      // Because AcquireTokenByAuthorizationCodeAsync is asynchronous, we tell ASP.NET core that we're handing the code
      // even if it's not done yet, so that it doesn't concurrently call the token endpoint. Otherwise, there will be a
      // race condition that causes an Azure AD error message ("code already redeemed").
      context.HandleCodeRedemption();

      // The cache needs the claims from the ID token.
      // If they're not yet in the HttpContext.User's claims, add them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Don't share the access token with ASP.NET Core. If we share it, ASP.NET will cache it and won't send the OAuth 2.0 request if
      // a further call to AcquireTokenByAuthorizationCodeAsync is required later for incremental consent (getting a code requesting more scopes).
      // Do share the ID token, however.
      var result = await application
          .AcquireTokenByAuthorizationCode(scopes.Except(_scopesRequestedByMsalNet), context.ProtocolMessage.Code)
          .ExecuteAsync()
          .ConfigureAwait(false);

      context.HandleCodeRedemption(null, result.IdToken);
  }
  catch (MsalException ex)
  {
      Debug.WriteLine(ex.Message);
      throw;
  }
 }
```

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>A TokenAcquisition.BuildConfidentialClientApplication metódus

A ASP.NET Core-ban a bizalmas ügyfélalkalmazás létrehozása `HttpContext`a ban található információkat használja fel. A `HttpContext` kérelemhez társított a `CurrentHttpContext` tulajdonság használatával érhető el. `HttpContext`információkat tartalmaz a webalkalmazás URL-címéről és a bejelentkezett felhasználóról (a a-ban). `ClaimsPrincipal` 

A `BuildConfidentialClientApplication` módszer a ASP.NET Core konfigurációt is használja. A konfiguráció "AzureAD" szakaszt tartalmaz, és mindkét alábbi elemhez kötődik:

- A `_applicationOptions` [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet)típusú adatstruktúra.
- Az `azureAdOptions` [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs)típusú példány, amelyet `Authentication.AzureAD.UI`ASP.NET Core definiál.

Végül az alkalmazásnak fenn kell tartania a jogkivonat-gyorsítótárakat. Erről a következő részben olvashat bővebben.

A metódus `GetOrBuildConfidentialClientApplication()` kódja a [Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). A függőségi injektálás által befecskendezett tagokat `TokenAcquisition` használja (a [Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)konstruktorában halad át).

Itt a `GetOrBuildConfidentialClientApplication`kód:

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor.
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL confidential client application, if needed.
  /// </summary>
  private IConfidentialClientApplication GetOrBuildConfidentialClientApplication()
  {
    if (application == null)
    {
        application = BuildConfidentialClientApplication();
    }
    return application;
  }

  /// <summary>
  /// Creates an MSAL Confidential client application
  /// </summary>
  /// <param name="claimsPrincipal"></param>
  /// <returns></returns>
  private IConfidentialClientApplication BuildConfidentialClientApplication()
  {
    var request = CurrentHttpContext.Request;
    var azureAdOptions = _azureAdOptions;
    var applicationOptions = _applicationOptions;
    string currentUri = UriHelper.BuildAbsolute(
        request.Scheme,
        request.Host,
        request.PathBase,
        azureAdOptions.CallbackPath ?? string.Empty);

    string authority = $"{applicationOptions.Instance}{applicationOptions.TenantId}/";

    var app = ConfidentialClientApplicationBuilder
        .CreateWithApplicationOptions(applicationOptions)
        .WithRedirectUri(currentUri)
        .WithAuthority(authority)
        .Build();

    // Initialize token cache providers
    _appTokenCacheProvider?.InitializeAsync(app.AppTokenCache);
    _userTokenCacheProvider?.InitializeAsync(app.UserTokenCache);

    return app;
  }

```

### <a name="summary"></a>Összefoglalás

`AcquireTokenByAuthorizationCode`valójában az a módszer, amely beváltja az engedélyezési kódot, amely ASP.NET kéréseket, és amely lekéri a MSAL.NET felhasználói token gyorsítótárába hozzáadott tokeneket. A gyorsítótárból a jogkivonatok at a ASP.NET Core vezérlők.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET a dolgokat ASP.NET Core-hoz hasonlóan kezeli, azzal a különbséggel, hogy az OpenID Connect konfigurációja és az `OnAuthorizationCodeReceived` eseményre való előfizetés a [App_Start\Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) fájlban történik. A fogalmak is hasonlóak ASP.NET Core, kivéve, `RedirectUri` hogy ASP.NET meg kell adnia a [web.config#L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Ez a konfiguráció egy kicsit kevésbé robusztus, mint a ASP.NET Core, mert meg kell változtatni, amikor telepíti az alkalmazást.

Itt a kód Startup.Auth.cs:

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the query string (&code=<code>).
    app.UseOAuth2CodeRedeemer(
        new OAuth2CodeRedeemerOptions
        {
            ClientId = AuthenticationConfig.ClientId,
            ClientSecret = AuthenticationConfig.ClientSecret,
            RedirectUri = AuthenticationConfig.RedirectUri
        }
      );

  app.UseOpenIdConnectAuthentication(
      new OpenIdConnectAuthenticationOptions
      {
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0.
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // A basic set of permissions for user sign-in and profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application, you would use IssuerValidator for additional checks, such as making sure the user's organization has signed up for your app.
            //     IssuerValidator = (issuer, token, tvp) =>
            //     {
            //        //if(MyCustomTenantValidation(issuer))
            //        return issuer;
            //        //else
            //        //    throw new SecurityTokenInvalidIssuerException("Invalid issuer");
            //    },
            //NameClaimType = "name",
        },
        Notifications = new OpenIdConnectAuthenticationNotifications()
        {
            AuthorizationCodeReceived = OnAuthorizationCodeReceived,
            AuthenticationFailed = OnAuthenticationFailed,
        }
      });
  }

  private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
  {
      // Upon successful sign-in, get the access token and cache it by using MSAL.
      IConfidentialClientApplication clientApp = MsalAppBuilder.BuildConfidentialClientApplication(new ClaimsPrincipal(context.AuthenticationTicket.Identity));
      AuthenticationResult result = await clientApp.AcquireTokenByAuthorizationCode(new[] { "Mail.Read" }, context.Code).ExecuteAsync();
  }

  private Task OnAuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
  {
      notification.HandleResponse();
      notification.Response.Redirect("/Error?message=" + notification.Exception.Message);
      return Task.FromResult(0);
  }
}
```

# <a name="java"></a>[Java](#tab/java)

Lásd: [Web app, amely aláírja a felhasználók: Kód konfiguráció](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) annak megértéséhez, hogy a Java minta leolvassa az engedélyezési kódot. Miután az alkalmazás megkapta a kódot, az [AuthFilter.java#L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Az `AuthHelper.processAuthenticationCodeRedirect` [AuthHelper.java#L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97)metódus delegálása .
1. A `getAuthResultByAuthCode`hívások .

```Java
class AuthHelper {
  // Code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // Code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // Code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// Code omitted
  }
}
```

A `getAuthResultByAuthCode` metódus az [AuthHelper.java#L176-ban](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176)van definiálva. Létrehoz egy MSAL- `ConfidentialClientApplication`t, majd az engedélyezési kódból létrehozott hívásokat. `acquireToken()` `AuthorizationCodeParameters`

```Java
   private IAuthenticationResult getAuthResultByAuthCode(
            HttpServletRequest httpServletRequest,
            AuthorizationCode authorizationCode,
            String currentUri) throws Throwable {

        IAuthenticationResult result;
        ConfidentialClientApplication app;
        try {
            app = createClientApplication();

            String authCode = authorizationCode.getValue();
            AuthorizationCodeParameters parameters = AuthorizationCodeParameters.builder(
                    authCode,
                    new URI(currentUri)).
                    build();

            Future<IAuthenticationResult> future = app.acquireToken(parameters);

            result = future.get();
        } catch (ExecutionException e) {
            throw e.getCause();
        }

        if (result == null) {
            throw new ServiceUnavailableException("authentication result was null");
        }

        SessionManagementHelper.storeTokenCacheInSession(httpServletRequest, app.tokenCache().serialize());

        return result;
    }

    private ConfidentialClientApplication createClientApplication() throws MalformedURLException {
        return ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(clientSecret)).
                authority(authority).
                build();
    }
```

# <a name="python"></a>[Python](#tab/python)

Az engedélyezési kód folyamata a [felhasználókban bejelentkező webalkalmazásban](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code)látható módon történik: Kód konfiguráció . A kód ezután `authorized` a függvényen érkezik, `/getAToken` amelyet Flask az URL-címből vezet. A kód teljes környezetét lásd az [app.py#L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) című témakörben:

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD.
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here.
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Az ügyféltitok helyett a bizalmas ügyfélalkalmazás ügyféltanúsítvánnyal vagy ügyfélállítással is bizonyíthatja az identitását.
Az ügyfélállítások használata egy speciális forgatókönyv, amely az [Ügyfél-állításokban részletező.](msal-net-client-assertions.md)

## <a name="token-cache"></a>Token gyorsítótár

> [!IMPORTANT]
> A webes alkalmazások vagy webes API-k tokengyorsítótár-megvalósítása eltér az asztali alkalmazások megvalósításától, amely gyakran [fájlalapú.](scenario-desktop-acquire-token.md#file-based-token-cache)
> Biztonsági és teljesítménybeli okokból fontos annak biztosítása, hogy a webalkalmazások és a webes API-k felhasználói fiókonként egy jogkivonat-gyorsítótártalálható. Minden fiókhoz szerializálnia kell a tokengyorsítótárat.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET alapvető oktatóanyag függőségi injektálás segítségével döntheti el a jogkivonat-gyorsítótár implementációját az alkalmazás Startup.cs fájljában. A Microsoft.Identity.Web a [tokengyorsítótár-szerializálásban](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization)ismertetett, előre elkészített tokengyorsítótár-szerializálószerezést is beépíti. Egy érdekes lehetőség az, hogy válasszon ASP.NET Core [elosztott memória cache:](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)

```csharp
// Use a distributed token cache by adding:
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// Then, choose your implementation.

// For instance, the distributed in-memory cache (not cleared when you stop the app):
services.AddDistributedMemoryCache()

// Or a Redis cache:
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache:
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

A token-cache szolgáltatókkal kapcsolatos részletekért tekintse meg a [ASP.NET Core Web app oktatóanyagait | Token gyorsítótárak](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) fázisában az oktatóanyag.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A webes alkalmazások vagy webes API-k tokengyorsítótár-megvalósítása eltér az asztali alkalmazások megvalósításától, amely gyakran [fájlalapú.](scenario-desktop-acquire-token.md#file-based-token-cache)

A webalkalmazás implementációja használhatja a ASP.NET munkamenetet vagy a kiszolgáló memóriáját. Például tekintse meg, hogyan kapcsolódik a gyorsítótár implementációja a [msalappbuilder.cs#L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51)MSAL.NET alkalmazás létrehozása után:

```csharp
public static class MsalAppBuilder
{
 // Omitted code
    public static IConfidentialClientApplication BuildConfidentialClientApplication(ClaimsPrincipal currentUser)
    {
      IConfidentialClientApplication clientapp = ConfidentialClientApplicationBuilder.Create(AuthenticationConfig.ClientId)
            .WithClientSecret(AuthenticationConfig.ClientSecret)
            .WithRedirectUri(AuthenticationConfig.RedirectUri)
            .WithAuthority(new Uri(AuthenticationConfig.Authority))
            .Build();

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation.
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="java"></a>[Java](#tab/java)

A MSAL Java módszereket biztosít a token-gyorsítótár szerializálására és deszerializálására. A Java minta kezeli a munkamenet szerializálását, ahogy az `getAuthResultBySilentFlow` [az AuthHelper.java#L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)metódusban látható:

```Java
IAuthenticationResult getAuthResultBySilentFlow(HttpServletRequest httpRequest, HttpServletResponse httpResponse)
      throws Throwable {

  IAuthenticationResult result =  SessionManagementHelper.getAuthSessionObject(httpRequest);

  IConfidentialClientApplication app = createClientApplication();

  Object tokenCache = httpRequest.getSession().getAttribute("token_cache");
  if (tokenCache != null) {
      app.tokenCache().deserialize(tokenCache.toString());
  }

  SilentParameters parameters = SilentParameters.builder(
          Collections.singleton("User.Read"),
          result.account()).build();

  CompletableFuture<IAuthenticationResult> future = app.acquireTokenSilently(parameters);
  IAuthenticationResult updatedResult = future.get();

  // Update session with latest token cache.
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

Az osztály `SessionManagementHelper` részleteit a [Java MSAL minta](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)biztosítja.

# <a name="python"></a>[Python](#tab/python)

A Python-mintában fiókonként egy gyorsítótár biztosítva van azáltal, hogy minden kéréshez létrehoz egy bizalmas ügyfélalkalmazást, majd szerializálja azt a Flask munkamenet-gyorsítótárában:

```python
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)

# Code omitted here for simplicity

def _load_cache():
    cache = msal.SerializableTokenCache()
    if session.get("token_cache"):
        cache.deserialize(session["token_cache"])
    return cache

def _save_cache(cache):
    if cache.has_state_changed:
        session["token_cache"] = cache.serialize()

def _build_msal_app(cache=None):
    return msal.ConfidentialClientApplication(
        app_config.CLIENT_ID, authority=app_config.AUTHORITY,
        client_credential=app_config.CLIENT_SECRET, token_cache=cache)
```

---

## <a name="next-steps"></a>További lépések

Ezen a ponton, amikor a felhasználó bejelentkezik, egy jogkivonat ot tárol a jogkivonat-gyorsítótárban. Lássuk, hogyan használják majd a webalkalmazás más részein.

> [!div class="nextstepaction"]
> [Webes API-kat meghívjaó webalkalmazás: Fiókok eltávolítása a gyorsítótárból globális kijelentkezéskor](scenario-web-app-call-api-sign-in.md)
