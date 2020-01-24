---
title: Webes API-kat meghívó webalkalmazás konfigurálása – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan konfigurálhatja a webes API-kat meghívó webalkalmazások kódját
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7bf5256390961f4d469aa70be4b0b0c1069db4cf
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701688"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Webes API-kat meghívó webalkalmazás – kód konfigurálása

Ahogy a [webes alkalmazás bejelentkezési felhasználói forgatókönyve](scenario-web-app-sign-user-overview.md)is látható, a webalkalmazás a OAuth 2.0-s [hitelesítési kód folyamatát](v2-oauth2-auth-code-flow.md) használja a bejelentkezéshez. Ez a folyamat két részből áll:

1. Kérjen egy engedélyezési kódot. Ez a rész a Microsoft Identity platformra delegál egy privát párbeszédpanelt a felhasználóval. A felhasználó bejelentkezik, és hozzájárul a webes API-k használatához. Ha a privát párbeszédpanel sikeresen véget ér, az alkalmazás egy engedélyezési kódot kap az átirányítási URI-n.
1. Igényeljen hozzáférési jogkivonatot az API-hoz az engedélyezési kód beváltásával.

A [webalkalmazások bejelentkezési felhasználói forgatókönyve](scenario-web-app-sign-user-overview.md) csak az első lábát hajtja végre. Itt megtudhatja, hogyan módosíthatja a bejelentkezett felhasználók webes API-ját, hogy most meghívja a webes API-kat.

## <a name="libraries-supporting-web-app-scenarios"></a>Webalkalmazás-forgatókönyveket támogató könyvtárak

A webalkalmazások engedélyezési kódját támogató kódtárak a következők:

| MSAL-könyvtár | Leírás |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | A támogatott platformok a .NET-keretrendszer és a .NET Core platform (a UWP, a Xamarin. iOS és a Xamarin. Android), mivel ezek a platformok nyilvános ügyfélalkalmazások létrehozására használhatók. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | Python-webalkalmazások támogatása |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Java-webalkalmazások támogatása |

Válassza ki az Önt érdeklő platformhoz tartozó fület:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Mivel a felhasználók bejelentkezésének engedélyezése az Open ID csatlakozás (OIDC) köztes middleware-re van delegálva, a OIDC folyamaton belül szeretne összekapcsolni. Ez a módszer eltérő lehet a használt keretrendszertől függően.
ASP.NET Core esetén előfizethet a köztes OIDC eseményekre. Ennek az az elve, hogy:

- A ASP.NET Core-t egy engedélyezési kód megadásával engedélyezheti a nyílt ID-kapcsolaton keresztül. Ezzel a ASP.NET/ASP.NET mag lehetővé teszi, hogy a felhasználó bejelentkezzen és beleegyezik,
- A webalkalmazás az engedélyezési kód fogadására fog előfizetni. Ezt az előfizetést egy C# meghatalmazotton keresztül teheti meg.
- Az Auth kód fogadásakor a MSAL-kódtárakat fogja használni a kód beváltásához, és az eredményül kapott hozzáférési jogkivonatokat és a frissítési tokeneket a jogkivonat-gyorsítótárban tárolja a rendszer. Ettől kezdve a gyorsítótár az alkalmazás más részeiben, például a vezérlőkben is használható, így a többi tokent csendesen lehet beszerezni.

A cikkben szereplő kódrészletek és a következők kibontása a [ASP.net Core Web App növekményes oktatóanyagában, 2. fejezet](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Ebben az oktatóanyagban érdemes megtekinteni a teljes megvalósítás részleteit.

> [!NOTE]
> Az alábbi kódrészletek teljes megértéséhez ismernie kell [ASP.net Core alapjait](https://docs.microsoft.com/aspnet/core/fundamentals), és különösen a [függőségi injekciót](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) és a [beállításokat](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Mivel a felhasználók bejelentkezésének engedélyezése az Open ID csatlakozás (OIDC) köztes middleware-re van delegálva, a OIDC folyamaton belül szeretne összekapcsolni. Ez a módszer eltérő lehet a használt keretrendszertől függően.
A ASP.NET esetében előfizethet a köztes OIDC eseményekre. Ennek az az elve, hogy:

- A ASP.NET Core-t egy engedélyezési kód megadásával engedélyezheti a nyílt ID-kapcsolaton keresztül. Ezzel a ASP.NET/ASP.NET mag lehetővé teszi, hogy a felhasználó bejelentkezzen és beleegyezik,
- A webalkalmazás az engedélyezési kód fogadására fog előfizetni. Ez egy C# delegált.
- Az Auth kód fogadásakor a MSAL-kódtárakat fogja használni a kód beváltásához. Az eredményül kapott hozzáférési jogkivonatok és frissítési tokenek a jogkivonat-gyorsítótárban tárolódnak. Ettől kezdve a gyorsítótár az alkalmazás más részeiben, például a vezérlőkben is használható, így a többi tokent csendesen lehet beszerezni.

A cikkben szereplő kódrészletek és a következők a [ASP.net Web App mintából](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)származnak. Érdemes lehet ezt a mintát a teljes megvalósítás részleteit megtekinteni.

# <a name="javatabjava"></a>[Java](#tab/java)

A cikkben szereplő kódrészletek és az alábbiak a [Java-webalkalmazásból](https://github.com/Azure-Samples/ms-identity-java-webapp) származnak, amely a Microsoft Graph MSAL Java Web App-mintát hívja meg.
A minta jelenleg lehetővé teszi, hogy a MSAL Java előállítsák az engedélyezési kód URL-címét, és kezeli a navigációt a Microsoft Identity platform engedélyezés végpontján. A Sprint Security használatával is bejelentkezhet a felhasználóba. Érdemes lehet ezt a mintát a teljes megvalósítás részleteit megtekinteni.

# <a name="pythontabpython"></a>[Python](#tab/python)

A cikkben szereplő kódrészletek és a következők a [Microsoft Graph MSAL meghívó Python-webalkalmazásból](https://github.com/Azure-Samples/ms-identity-python-webapp) származnak. Python-webalkalmazás minta.
A minta jelenleg lehetővé teszi a MSAL. A Python előkészíti az engedélyezési kód URL-címét, és kezeli a navigációt a Microsoft Identity platform engedélyezés végpontján. Érdemes lehet ezt a mintát a teljes megvalósítás részleteit megtekinteni.

---

## <a name="code-that-redeems-the-authorization-code"></a>Az engedélyezési kódot beváltó kód

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

A ASP.NET Core a `Startup.cs` fájlban. Elő kell fizetnünk a `OnAuthorizationCodeReceived` Open ID csatlakozási eseményre, és ebből az eseményből hívja meg a MSAL. A NET metódusa `AcquireTokenFromAuthorizationCode`, amely a jogkivonat-gyorsítótárban való tárolást, a kért `scopes`hozzáférési jogkivonatát, valamint egy frissítési jogkivonatot tartalmaz, amelyet a hozzáférési jogkivonat frissítéséhez fog használni, amikor a lejárati időpontot lezárták, vagy egy másik erőforrás nevében, de egy másik erőforráshoz is.

A gyakorlatban a [ASP.net Core webalkalmazás-oktatóanyag](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) megkísérli a webalkalmazások újrafelhasználható kódjának megadását.

Itt látható a [Startup. cs # L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42) kód, amely a webalkalmazáshoz való hitelesítést `AddMsal` és a webes API-k meghívásához szükséges képességet biztosító `AddMicrosoftIdentityPlatformAuthentication` metódus meghívását adja meg. A `AddInMemoryTokenCaches` hívása a jogkivonat-gyorsítótár implementációjának kiválasztására vonatkozik a lehetséges lehetőségek közül:

```csharp
public class Startup
{
  // Code not show here

  public void ConfigureServices(IServiceCollection services)
  {
      // Token acquisition service based on MSAL.NET
      // and chosen token cache implementation
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
          .AddMsal(Configuration, new string[] { Constants.ScopeUserRead })
          .AddInMemoryTokenCaches();
  }

  // Code not show here
}
```

a `Constants.ScopeUserRead` Konstansokban van definiálva [. cs # L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5)

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Már tanulmányozta `AddMicrosoftIdentityPlatformAuthentication` tartalmát a [webalkalmazásban, amely bejelentkezik a felhasználók számára – kód konfiguráció](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code)

### <a name="the-addmsal-method"></a>A AddMsal metódus

`AddMsal` kódja a [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159)helyen található.

```csharp

/// <summary>
/// Extensions for IServiceCollection for startup initialization.
/// </summary>
public static class WebAppServiceCollectionExtensions
{
  // Code omitted here

  /// <summary>
  /// Add MSAL support to the Web App or Web API
  /// </summary>
  /// <param name="services">Service collection to which to add authentication</param>
  /// <param name="initialScopes">Initial scopes to request at sign-in</param>
  /// <returns></returns>
  public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
  {
      // Ensure that configuration options for MSAL.NET, HttpContext accessor and the Token acquisition service
      // (encapsulating MSAL.NET) are available through dependency injection
      services.Configure<ConfidentialClientApplicationOptions>(options => configuration.Bind(configSectionName, options));
      services.AddHttpContextAccessor();
      services.AddTokenAcquisition();

      services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
      {
          // Response type
          options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

          // This scope is needed to get a refresh token when users sign-in with their Microsoft personal accounts
          // (it's required by MSAL.NET and automatically provided when users sign-in with work or school accounts)
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

          // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
          // where it will be usable from Controllers later (through the TokenAcquisition service)
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

A `AddMsal` módszer biztosítja a következőket:

- a ASP.NET Core webalkalmazás a felhasználó IDToken és egy hitelesítési kódot (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`) is kér
- a `offline_access` hatókör hozzá van adva. Szükség van rá, hogy a felhasználó hozzájáruljon ahhoz, hogy az alkalmazás megkapja a frissítési tokent.
- az alkalmazás előfizet a OIDC `OnAuthorizationCodeReceived` eseményre, és beváltja a hívást a MSAL.NET használatával, amely egy újrafelhasználható, `ITokenAcquisition`t megvalósító összetevőbe van ágyazva.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>A TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync metódus

A `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` módszer a [Microsoft. Identity. Web/TokenAcquisition. cs # L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145)mappában található. A következőket biztosítja:

- A ASP.NET nem kísérli meg a hitelesítési kód beváltását párhuzamosan a MSAL.NET (`context.HandleCodeRedemption();`)
- A IDToken lévő jogcímek a MSAL számára elérhetők a felhasználó fiókjához tartozó jogkivonat-gyorsítótár kiszámításához
- Ha szükséges, a rendszer létrehozza a MSAL.NET alkalmazást.
- a MSAL.NET alkalmazás beváltja a kódot
- Az új azonosító jogkivonatot a ASP.NET Core (`context.HandleCodeRedemption(null, result.IdToken);`) hívása során megosztják. A hozzáférési token nincs megosztva ASP.NET Coreval. A felhasználóhoz társított MSAL.NET-jogkivonat-gyorsítótárban marad, ahol készen áll a ASP.NET Core vezérlőkben való használatra.

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
      // As AcquireTokenByAuthorizationCodeAsync is asynchronous we want to tell ASP.NET core that we are handing the code
      // even if it's not done yet, so that it does not concurrently call the Token endpoint. (otherwise there will be a
      // race condition ending-up in an error from Azure AD telling "code already redeemed")
      context.HandleCodeRedemption();

      // The cache will need the claims from the ID token.
      // If they are not yet in the HttpContext.User's claims, so adding them here.
      if (!context.HttpContext.User.Claims.Any())
      {
          (context.HttpContext.User.Identity as ClaimsIdentity).AddClaims(context.Principal.Claims);
      }

      var application = GetOrBuildConfidentialClientApplication();

      // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it and will not send the OAuth 2.0 request in
      // case a further call to AcquireTokenByAuthorizationCodeAsync in the future is required for incremental consent (getting a code requesting more scopes)
      // Share the ID Token though
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

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>A TokenAcquisition. BuildConfidentialClientApplication metódus

ASP.NET Core a bizalmas ügyfélalkalmazás felépítése a HttpContext található információkat használja. A `CurrentHttpContext` tulajdonságon keresztül érhető el, amely a kérelemhez társított HttpContext, ismeri a webalkalmazás URL-címét és a bejelentkezett felhasználót (`ClaimsPrincipal`). A `BuildConfidentialClientApplication` a ASP.NET Core konfigurációt is használja, amelynek "AzureAD" szakasza van, és amely a következőhöz van kötve:

- a [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet) típusú `_applicationOptions` adatstruktúra
- a ASP.NET Core `Authentication.AzureAD.UI`ban definiált [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs) típusú `azureAdOptions` példány. Végül az alkalmazásnak meg kell őriznie a jogkivonat-gyorsítótárat. Erről bővebben a következő szakaszban olvashat.

A `GetOrBuildConfidentialClientApplication()` metódus kódja a [Microsoft. Identity. Web/TokenAcquisition. cs # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). Olyan tagokat használ, amelyek függőségi befecskendezéssel lettek bevezetve (a [Microsoft. Identity. Web/TokenAcquisition. cs # L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)) a TokenAcquisition konstruktorában lett átadva.

```csharp
public class TokenAcquisition : ITokenAcquisition
{
  // Code omitted here for clarity

  // Members
  private IConfidentialClientApplication application;
  private HttpContext CurrentHttpContext => _httpContextAccessor.HttpContext;

  // The following members are set by dependency injection in the TokenAcquisition constructor
  private readonly AzureADOptions _azureAdOptions;
  private readonly ConfidentialClientApplicationOptions _applicationOptions;
  private readonly IMsalAppTokenCacheProvider _appTokenCacheProvider;
  private readonly IMsalUserTokenCacheProvider _userTokenCacheProvider;
  private readonly IHttpContextAccessor _httpContextAccessor;

  /// <summary>
  /// Creates an MSAL Confidential client application if needed
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

Az összegzéshez `AcquireTokenByAuthorizationCode` valóban beváltja a ASP.NET által kért hitelesítési kódot, és beolvassa a MSAL.NET felhasználói jogkivonat-gyorsítótárhoz hozzáadott jogkivonatokat. Innentől kezdve a ASP.NET Core vezérlőkben lesznek használatban.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET által kezelt dolgok ugyanúgy hasonlítanak a ASP.NET Corehoz, azzal a különbséggel, hogy az [App_Start \startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) -fájlban a OpenIdConnect konfigurációja és az `OnAuthorizationCodeReceived` eseményre való előfizetés történik. Hasonló fogalmakat talál, mint a ASP.NET Coreban, a ASP.NET azonban meg kell adnia a RedirectUri a [web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Ez a konfiguráció egy kicsit kevésbé robusztus, mint amit a ASP.NET Core végez, mivel az alkalmazás telepítésekor módosítania kell azt.

```csharp
public partial class Startup
{
  public void ConfigureAuth(IAppBuilder app)
  {
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    // Custom middleware initialization. This is activated when the code obtained from a code_grant is present in the querystring (&code=<code>).
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
        // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
        Authority = AuthenticationConfig.Authority,
        ClientId = AuthenticationConfig.ClientId,
        RedirectUri = AuthenticationConfig.RedirectUri,
        PostLogoutRedirectUri = AuthenticationConfig.RedirectUri,
        Scope = AuthenticationConfig.BasicSignInScopes + " Mail.Read", // a basic set of permissions for user sign in & profile access "openid profile offline_access"
        TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = false,
            // In a real application you would use IssuerValidator for additional checks, like making sure the user's organization has signed up for your app.
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
      // Upon successful sign in, get the access token & cache it using MSAL
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

# <a name="javatabjava"></a>[Java](#tab/java)

Tekintse meg a következőt: [webalkalmazás, amely bejelentkezik a felhasználóknál – a kód konfigurációjában](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) megismerheti, hogy a Java-minta hogyan kapja meg az engedélyezési kódot Az alkalmazástól kapott kérés után a [AuthFilter. Java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56) delegálja `AuthHelper.processAuthenticationCodeRedirect` a [AuthHelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97), majd meghívja `getAuthResultByAuthCode`:

```Java
class AuthHelper {
  // code omitted
  void processAuthenticationCodeRedirect(HttpServletRequest httpRequest, String currentUri, String fullUrl)
            throws Throwable {

  // code omitted
  AuthenticationResponse authResponse = AuthenticationResponseParser.parse(new URI(fullUrl), params);

  // code omitted
  IAuthenticationResult result = getAuthResultByAuthCode(
                    httpRequest,
                    oidcResponse.getAuthorizationCode(),
                    currentUri);

// code omitted
  }
}
```

A `getAuthResultByAuthCode` metódus a [AuthHelper. Java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176)van definiálva. Létrehoz egy MSAL `ConfidentialClientApplication`, és meghívja az engedélyezési kódból létrehozott `AuthorizationCodeParameters` `acquireToken()`.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Miután az engedélyezési kód megadását kéri a [webalkalmazásban, amely bejelentkezik a felhasználói kód konfigurációjában](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code), az engedélyezési kód a `authorized` függvényben érkezik, amely a lombik útvonalait a/getAToken URL-címéről kapja. Lásd: [app. a # L30-L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44)

```python
 @app.route("/getAToken")  # Its absolute URL must match your app's redirect_uri set in AAD
def authorized():
    if request.args['state'] != session.get("state"):
        return redirect(url_for("login"))
    cache = _load_cache()
    result = _build_msal_app(cache).acquire_token_by_authorization_code(
        request.args['code'],
        scopes=app_config.SCOPE,  # Misspelled scope would cause an HTTP 400 error here
        redirect_uri=url_for("authorized", _external=True))
    if "error" in result:
        return "Login failure: %s, %s" % (
            result["error"], result.get("error_description"))
    session["user"] = result.get("id_token_claims")
    _save_cache(cache)
    return redirect(url_for("index"))
```

---

Az ügyfél titkos kulcsa helyett a bizalmas ügyfélalkalmazás is igazolhatja személyazonosságát egy ügyféltanúsítvány vagy egy ügyfél-tanúsítvány használatával.
Az ügyfél-kijelentések használata egy speciális forgatókönyv, amely részletesen szerepel az [ügyfél-kijelentésekben](msal-net-client-assertions.md)

## <a name="token-cache"></a>Jogkivonat-gyorsítótár

> [!IMPORTANT]
> A Web Apps (vagy a webes API-k) esetében a jogkivonat-gyorsítótár implementációja eltér az asztali alkalmazások jogkivonat-gyorsítótárának implementációjában (amelyek gyakran [fájl-alapúak](scenario-desktop-acquire-token.md#file-based-token-cache)).
> Biztonsági és teljesítménybeli okokból fontos, hogy a webalkalmazások és a webes API-k esetében csak egy jogkivonat-gyorsítótár legyen felhasználónként (felhasználónkénti). Minden fiókhoz szerializálnia kell a jogkivonat-gyorsítótárat.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET Core oktatóanyag függőségi befecskendezést használ, hogy eldöntse a jogkivonat-gyorsítótár megvalósítását az alkalmazás Startup.cs-fájljában. A Microsoft. Identity. Web számos előre elkészített jogkivonat-gyorsítótár-szerializálást tartalmaz, amelyeket a [jogkivonat-gyorsítótár szerializálásakor](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization)ismertetett. Érdekes lehetőség a ASP.NET Core [elosztott memória-gyorsítótárak](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)kiválasztása:

```csharp
// or use a distributed Token Cache by adding
    services.AddMicrosoftIdentityPlatformAuthentication(Configuration)
            .AddMsal(new string[] { scopesToRequest })
            .AddDistributedTokenCaches();

// and then choose your implementation

// For instance the distributed in memory cache (not cleared when you stop the app)
services.AddDistributedMemoryCache()

// Or a Redis cache
services.AddStackExchangeRedisCache(options =>
{
 options.Configuration = "localhost";
 options.InstanceName = "SampleInstance";
});

// Or even a SQL Server token cache
services.AddDistributedSqlServerCache(options =>
{
 options.ConnectionString = _config["DistCache_ConnectionString"];
 options.SchemaName = "dbo";
 options.TableName = "TestCache";
});
```

További információ a jogkivonat-gyorsítótár-szolgáltatókkal kapcsolatban: a [ASP.net Core Web App oktatóanyagok | Az oktatóanyag token-gyorsítótárai](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) szakasza

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A Web Apps (vagy a webes API-k) esetében a jogkivonat-gyorsítótár implementációja eltér az asztali alkalmazások jogkivonat-gyorsítótárának implementációjában (amelyek gyakran [fájl-alapúak](scenario-desktop-acquire-token.md#file-based-token-cache). Használhatja a ASP.NET-munkamenetet vagy a kiszolgáló memóriáját. A MSAL.NET alkalmazás létrehozása után a [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51) című témakörben láthatja, hogy a gyorsítótár implementációja hogyan csatlakoztatható.

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

      // After the ConfidentialClientApplication is created, we overwrite its default UserTokenCache with our implementation
      MSALPerUserMemoryTokenCache userTokenCache = new MSALPerUserMemoryTokenCache(clientapp.UserTokenCache, currentUser ?? ClaimsPrincipal.Current);

      return clientapp;
  }
```

# <a name="javatabjava"></a>[Java](#tab/java)

A MSAL Java a jogkivonat-gyorsítótár szerializálására és deszerializálására szolgáló metódusokat biztosít. A Java-minta kezeli a szerializálást a munkamenetből, ahogy az a `getAuthResultBySilentFlow` metódusban látható a [AuthHelper. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122)

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

  //update session with latest token cache
  SessionManagementHelper.storeTokenCacheInSession(httpRequest, app.tokenCache().serialize());

  return updatedResult;
}
```

A `SessionManagementHelper` osztály részletes adatai itt vannak megadva:[](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python-mintában, a gyorsítótár szerint fiók alapján gondoskodni kell arról, hogy minden kérelemhez újból létrehoz egy bizalmas ügyfélalkalmazás-alkalmazást, és a lombik munkamenet-gyorsítótárában azt szerializálja:

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

## <a name="next-steps"></a>Következő lépések

Ezen a ponton, amikor a felhasználó bejelentkezik a jogkivonat-gyorsítótárba, a rendszer a tokent tárolja. Lássuk, hogyan használják majd a webalkalmazás más részeiben.

> [!div class="nextstepaction"]
> [Bejelentkezés a webalkalmazásba](scenario-web-app-call-api-sign-in.md)
