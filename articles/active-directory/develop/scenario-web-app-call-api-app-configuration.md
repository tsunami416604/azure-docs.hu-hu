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
ms.openlocfilehash: 374b215a737efbe3d421b6dc49af01303ec54473
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759160"
---
# <a name="a-web-app-that-calls-web-apis-code-configuration"></a>Webes API-kat meghívó webalkalmazás: kód konfigurálása

Ahogy [azt a webalkalmazásban](scenario-web-app-sign-user-overview.md) is láthatja, amely a felhasználók forgatókönyvét jelzi, a webalkalmazás a [OAuth 2,0 engedélyezési kód folyamatát](v2-oauth2-auth-code-flow.md) használja a felhasználó aláírásához a alkalmazásban. A folyamat két lépésből áll:

1. Kérjen egy engedélyezési kódot. Ez a rész privát párbeszédet delegál a felhasználóval a Microsoft Identity platformon. A párbeszéd során a felhasználó bejelentkezik, és hozzájárul a webes API-k használatához. A privát párbeszéd sikeres befejezése után a webalkalmazás egy engedélyezési kódot kap az átirányítási URI-n.
1. Igényeljen hozzáférési jogkivonatot az API-hoz az engedélyezési kód beváltásával.

A [felhasználói forgatókönyvekben bejelentkező webalkalmazás](scenario-web-app-sign-user-overview.md) csak az első lépést tárgyalja. Itt megtudhatja, hogyan módosíthatja a webalkalmazást úgy, hogy ne csak a felhasználókat írja alá, hanem a webes API-kat is meghívja.

## <a name="libraries-that-support-web-app-scenarios"></a>Webalkalmazás-forgatókönyveket támogató kódtárak

A Microsoft Authentication Library (MSAL) következő kódtárai támogatják a webes alkalmazások engedélyezési kódjának áramlását:

| MSAL-könyvtár | Leírás |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | A .NET-keretrendszer és a .NET Core platform támogatása. A Univerzális Windows-platform (UWP), a Xamarin. iOS és a Xamarin. Android nem támogatott, mivel ezek a platformok nyilvános ügyfélalkalmazások létrehozására használhatók. |
| ![MSAL Python](media/sample-v2-code/logo_python.png) <br/> Pythonhoz készült MSAL | Python-webalkalmazások támogatása. |
| ![MSAL Java](media/sample-v2-code/logo_java.png) <br/> Javához készült MSAL | Java-webalkalmazások támogatása. |

Válassza ki az Önt érdeklő platform lapját:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Mivel a felhasználói bejelentkezés delegálva van az Open ID kapcsolódási (OIDC) köztes, a OIDC folyamattal kell kommunikálni. Az interakció módja a használt keretrendszertől függ.

ASP.NET Core esetében előfizethet a köztes OIDC eseményekre:

- Lehetővé teszi, hogy ASP.NET Core igényeljen egy engedélyezési kódot az Open ID-kapcsolat közbenső hálózatának segítségével. A ASP.NET vagy a ASP.NET Core lehetővé teszi a felhasználó számára a bejelentkezést és a beleegyezik.
- Előfizet a webalkalmazásnak az engedélyezési kód fogadásához. Ezt az előfizetést C# delegált használatával végezheti el.
- Az engedélyezési kód fogadásakor a MSAL-kódtárakat fogja használni a beváltáshoz. Az eredményül kapott hozzáférési jogkivonatok és frissítési tokenek a jogkivonat-gyorsítótárban tárolódnak. A gyorsítótár az alkalmazás más részeiben, például a vezérlőkben is használható, hogy a többi tokent csendesen szerezzen.

A cikkben szereplő példákat és az alábbi kódrészletet a [ASP.net Core Web App növekményes oktatóanyaga ismerteti, 2. fejezet](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph). Ebben az oktatóanyagban a teljes körű megvalósítás részleteit érdemes megtekinteni.

> [!NOTE]
> Az itt található programkódok teljes megértéséhez ismernie kell [ASP.net Core alapjait](https://docs.microsoft.com/aspnet/core/fundamentals), és különösen a [függőségi befecskendezést](https://docs.microsoft.com/aspnet/core/fundamentals/dependency-injection) és a [beállításokat](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/options).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

Mivel a felhasználói bejelentkezés delegálva van az Open ID kapcsolódási (OIDC) köztes, a OIDC folyamattal kell kommunikálni. Az interakció módja a használt keretrendszertől függ.

A ASP.NET esetében előfizethet a köztes OIDC eseményekre:

- Lehetővé teszi, hogy ASP.NET Core igényeljen egy engedélyezési kódot az Open ID-kapcsolat közbenső hálózatának segítségével. A ASP.NET vagy a ASP.NET Core lehetővé teszi a felhasználó számára a bejelentkezést és a beleegyezik.
- Előfizet a webalkalmazásnak az engedélyezési kód fogadásához. Ezt az előfizetést C# delegált használatával végezheti el.
- Az engedélyezési kód fogadásakor a MSAL-kódtárakat fogja használni a beváltáshoz. Az eredményül kapott hozzáférési jogkivonatok és frissítési tokenek a jogkivonat-gyorsítótárban tárolódnak. A gyorsítótár az alkalmazás más részeiben, például a vezérlőkben is használható, hogy a többi tokent csendesen szerezzen.

A cikkben szereplő példák a [ASP.net Web App mintából](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)származnak. A teljes körű megvalósítási részletekért érdemes lehet erre a példára hivatkozni.

# <a name="javatabjava"></a>[Java](#tab/java)

A cikkben szereplő példákat és a következőt a Java-webalkalmazásból nyeri ki, [amely meghívja a Microsoft Grapht](https://github.com/Azure-Samples/ms-identity-java-webapp), a MSAL for Java-t használó webalkalmazási mintát.
A minta jelenleg lehetővé teszi, hogy a Java MSAL az engedélyezési kód URL-címét, és kezeli a navigációt a Microsoft Identity platform engedélyezési végpontján. A Sprint Security használatával is aláírhatja a felhasználót a alkalmazásban. Előfordulhat, hogy a teljes megvalósítás részleteit a mintában érdemes megtekinteni.

# <a name="pythontabpython"></a>[Python](#tab/python)

A cikkben szereplő példákat és az alábbi kódot a [Python-webalkalmazás hívja le Microsoft Graph](https://github.com/Azure-Samples/ms-identity-python-webapp), egy webalkalmazási mintát, amely a MSAL-t használja. Python.
A minta jelenleg lehetővé teszi a MSAL. A Python előkészíti az engedélyezési kód URL-címét, és kezeli a navigációt a Microsoft Identity platform engedélyezési végpontján. Előfordulhat, hogy a teljes megvalósítás részleteit a mintában érdemes megtekinteni.

---

## <a name="code-that-redeems-the-authorization-code"></a>Az engedélyezési kódot beváltó kód

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

### <a name="startupcs"></a>Startup.cs

ASP.NET Core a `Startup.cs` fájlban előfizet az `OnAuthorizationCodeReceived` OpenID Connect eseményre. Ebből az eseményből hívja meg a MSAL.NET `AcquireTokenFromAuthorizationCode` metódust. Ez a metódus a következő jogkivonatokat tárolja a jogkivonat-gyorsítótárban:

- A kért `scopes`*hozzáférési jogkivonata* .
- *Frissítési jogkivonat*. Ennek a tokennek a használatával frissül a hozzáférési token, ha a lejárati ideje lezárult, vagy egy másik tokent kap ugyanazon felhasználó nevében, de egy másik erőforráshoz.

A [ASP.net Core Web App oktatóanyag](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2) a webalkalmazások újrafelhasználható kódját biztosítja.

A következő a kód a [Startup. cs # L40-L42](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Startup.cs#L40-L42). A szolgáltatás a következőket hívja meg:

- A `AddMicrosoftIdentityPlatformAuthentication` metódus, amely a webalkalmazáshoz való hitelesítést adja hozzá.
- A `AddMsal` metódus, amely felteszi a webes API-k meghívásának képességét.
- A `AddInMemoryTokenCaches` metódus, amely a jogkivonat-gyorsítótár implementációjának kiválasztására szolgál.

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

a `Constants.ScopeUserRead` Konstansokban van definiálva [. cs # L5](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/2-WebApp-graph-user/2-1-Call-MSGraph/Infrastructure/Constants.cs#L5):

```csharp
public static class Constants
{
    public const string ScopeUserRead = "User.Read";
}
```

Már tanulmányozta `AddMicrosoftIdentityPlatformAuthentication` a [webalkalmazásban, amely a felhasználói kódok konfigurációjában jelentkezik](scenario-web-app-sign-user-app-configuration.md?tabs=aspnetcore#initialization-code).

### <a name="the-addmsal-method"></a>A AddMsal metódus

`AddMsal` kódja a [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L108-L159](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L108-L159)helyen található.

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

A `AddMsal` módszer biztosítja a következőket:

- A ASP.NET Core webalkalmazás egy azonosító jogkivonatot kér a felhasználóhoz és egy hitelesítési kódot (`options.ResponseType = OpenIdConnectResponseType.CodeIdToken`).
- A `offline_access` hatókör hozzá van adva. Ez a hatókör felhasználói beleegyezett az alkalmazáshoz, hogy frissítési jogkivonatot kapjon.
- Az alkalmazás előfizet a OIDC `OnAuthorizationCodeReceived` eseményre, és beváltja a hívást a MSAL.NET használatával, amely egy újrafelhasználható, `ITokenAcquisition`t megvalósító összetevőbe van ágyazva.

### <a name="the-tokenacquisitionaddaccounttocachefromauthorizationcodeasync-method"></a>A TokenAcquisition. AddAccountToCacheFromAuthorizationCodeAsync metódus

A `TokenAcquisition.AddAccountToCacheFromAuthorizationCodeAsync` módszer a [Microsoft. Identity. Web/TokenAcquisition. cs # L101-L145](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L101-L145)mappában található. A következőket biztosítja:

- A ASP.NET nem kísérli meg a hitelesítési kód MSAL.NET (`context.HandleCodeRedemption();`) párhuzamos beváltását.
- Az azonosító jogkivonatban található jogcímek elérhetők a MSAL számára, hogy a felhasználó fiókjához tartozó jogkivonat-gyorsítótárat számítsanak.
- Szükség esetén létrejön a MSAL.NET alkalmazás egy példánya.
- A MSAL.NET alkalmazás beváltja a kódot.
- Az új azonosító jogkivonatot a rendszer megosztja ASP.NET Core a `context.HandleCodeRedemption(null, result.IdToken);`hívása során. A hozzáférési token nincs megosztva ASP.NET Coreval. A felhasználóhoz társított MSAL.NET-jogkivonat-gyorsítótárban marad, ahol készen áll a ASP.NET Core vezérlőkben való használatra.

A `TokenAcquisition`vonatkozó kód a következő:

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

### <a name="the-tokenacquisitionbuildconfidentialclientapplication-method"></a>A TokenAcquisition. BuildConfidentialClientApplication metódus

ASP.NET Core a bizalmas ügyfélalkalmazás felépítése a `HttpContext`található információk alapján működik. A kéréshez társított `HttpContext` a `CurrentHttpContext` tulajdonsággal érhető el. `HttpContext` a webalkalmazás URL-címével és a bejelentkezett felhasználóval (egy `ClaimsPrincipal`) kapcsolatos információkkal rendelkezik. 

A `BuildConfidentialClientApplication` metódus a ASP.NET Core konfigurációt is használja. A konfigurációhoz tartozik egy "AzureAD" szakasz is, amely a következő elemekhez is kötve van:

- A [ConfidentialClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationoptions?view=azure-dotnet)típusú `_applicationOptions` adatstruktúra.
- A ASP.NET Core `Authentication.AzureAD.UI`ban definiált [AzureAdOptions](https://github.com/aspnet/AspNetCore/blob/master/src/Azure/AzureAD/Authentication.AzureAD.UI/src/AzureADOptions.cs)típusú `azureAdOptions` példány.

Végül az alkalmazásnak meg kell őriznie a jogkivonat-gyorsítótárat. Erről bővebben a következő szakaszban olvashat.

A `GetOrBuildConfidentialClientApplication()` metódus kódja a [Microsoft. Identity. Web/TokenAcquisition. cs # L290-L333](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L290-L333). `TokenAcquisition` a függőségi befecskendezéssel Beinjektált tagokat használ (a [Microsoft. Identity. Web/TokenAcquisition. cs # L47-L59](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/4b12ba02e73f62e3e3137f5f4b9ef43cec7c14fd/Microsoft.Identity.Web/TokenAcquisition.cs#L47-L59)).

A `GetOrBuildConfidentialClientApplication`kódja:

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

`AcquireTokenByAuthorizationCode` valóban az a módszer, amely beváltja a ASP.NET kérelmeket, és lekéri a MSAL.NET felhasználói jogkivonat-gyorsítótárba felvett jogkivonatokat. A gyorsítótárból a tokeneket a rendszer a ASP.NET Core-vezérlőkben használja.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET a ASP.NET Core hasonlóan kezeli a dolgokat, azzal a különbséggel, hogy az OpenID Connect konfigurációja és a `OnAuthorizationCodeReceived` eseményre való előfizetés a [App_Start \startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs) -fájlban történik. A fogalmak a ASP.NET Corehoz hasonlóan is hasonlóak, de a ASP.NET-ben meg kell adnia a `RedirectUri` a [web. config # L15](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/master/WebApp/Web.config#L15). Ez a konfiguráció egy kicsit kevésbé robusztus, mint ASP.NET Core, mert az alkalmazás telepítésekor módosítania kell azt.

A Startup.Auth.cs kódja:

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

# <a name="javatabjava"></a>[Java](#tab/java)

Tekintse [meg a felhasználók által használt webalkalmazást: a kód konfigurációjában](scenario-web-app-sign-user-app-configuration.md?tabs=java#initialization-code) megismerheti, hogy a Java-minta hogyan kapja meg az engedélyezési kódot. Miután az alkalmazás megkapja a kódot, a [AuthFilter. Java # L51-L56](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java#L51-L56):

1. Delegálja a `AuthHelper.processAuthenticationCodeRedirect` metódusnak a [AuthHelper. Java # L67-L97](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L67-L97).
1. Meghívja a `getAuthResultByAuthCode`.

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

A `getAuthResultByAuthCode` metódus a [AuthHelper. Java # L176](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L176)van definiálva. Létrehoz egy MSAL `ConfidentialClientApplication`, majd meghívja a `acquireToken()`t a `AuthorizationCodeParameters` létrehozott engedélyezési kódból.

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

A rendszer az engedélyezési kód folyamatát kéri a [webalkalmazásban, amely aláírja a felhasználókat: kód konfigurálása](scenario-web-app-sign-user-app-configuration.md?tabs=python#initialization-code). A kód ekkor megjelenik a `authorized` függvényben, amely a lombikot a `/getAToken` URL-címről irányítja. Lásd: [app. a # L30 – L44](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L30-L44) a kód teljes kontextusához:

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

Az ügyfél titkos kulcsa helyett a bizalmas ügyfélalkalmazás is igazolhatja személyazonosságát egy ügyféltanúsítvány vagy egy ügyfél-tanúsítvány használatával.
Az ügyfél-kijelentések használata egy speciális forgatókönyv, amely részletesen szerepel az [ügyfél-kijelentésekben](msal-net-client-assertions.md).

## <a name="token-cache"></a>Jogkivonat-gyorsítótár

> [!IMPORTANT]
> A webalkalmazások vagy webes API-k jogkivonat-gyorsítótárazási implementációja eltér az asztali alkalmazások megvalósításán, ami gyakran [fájl alapú](scenario-desktop-acquire-token.md#file-based-token-cache).
> Biztonsági és teljesítménybeli okokból fontos, hogy a webalkalmazások és a webes API-k esetében a felhasználói fiókok esetében egy jogkivonat-gyorsítótár legyen. Minden fiókhoz szerializálnia kell a jogkivonat-gyorsítótárat.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET Core oktatóanyag függőségi befecskendezést használ, hogy eldöntse a jogkivonat-gyorsítótár megvalósítását az alkalmazás Startup.cs-fájljában. A Microsoft. Identity. Web előre elkészített jogkivonat-gyorsítótárazási szerializálók szerepelnek a [jogkivonat-gyorsítótár szerializálása](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/README.md#token-cache-serialization)című témakörben. Érdekes lehetőség a ASP.NET Core [elosztott memória-gyorsítótárak](https://docs.microsoft.com/aspnet/core/performance/caching/distributed#distributed-memory-cache)kiválasztása:

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

További információ a jogkivonat-gyorsítótár szolgáltatókról: a [ASP.net Core Web App oktatóanyagok | Az oktatóanyag jogkivonat-gyorsítótárazási](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache) fázisa.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A webalkalmazások vagy webes API-k jogkivonat-gyorsítótárazási implementációja eltér az asztali alkalmazások megvalósításán, ami gyakran [fájl alapú](scenario-desktop-acquire-token.md#file-based-token-cache).

A webes alkalmazás implementációja a ASP.NET-munkamenetet vagy a kiszolgáló memóriáját használhatja. Tekintse meg például a gyorsítótár implementációját a MSAL.NET alkalmazás létrehozása után a [MsalAppBuilder. cs # L39-L51](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Utils/MsalAppBuilder.cs#L39-L51):

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

# <a name="javatabjava"></a>[Java](#tab/java)

A MSAL Java a jogkivonat-gyorsítótár szerializálására és deszerializálására szolgáló metódusokat biztosít. A Java-minta kezeli a szerializálást a munkamenetből, ahogy az a `getAuthResultBySilentFlow` metódusban látható a [AuthHelper. Java # L99-L122](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/AuthHelper.java#L99-L122):

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

A `SessionManagementHelper` osztály részletes adatai a [Java MSAL-mintájában](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/java/com/microsoft/azure/msalwebsample/SessionManagementHelper.java)vannak megadva.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python-mintában egy fiókon belül egy gyorsítótárat biztosítunk, amely minden kérelemhez létrehoz egy bizalmas ügyfélalkalmazás-alkalmazást, majd a lombik-munkamenet gyorsítótárában szerializálja azt:

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

Ezen a ponton, amikor a felhasználó bejelentkezik, a rendszer a jogkivonat-gyorsítótárban tárolja a tokent. Lássuk, hogyan használják majd a webalkalmazás más részeiben.

> [!div class="nextstepaction"]
> [Webes API-kat meghívó webalkalmazás: fiókok eltávolítása a gyorsítótárból a globális kijelentkezéskor](scenario-web-app-call-api-sign-in.md)
