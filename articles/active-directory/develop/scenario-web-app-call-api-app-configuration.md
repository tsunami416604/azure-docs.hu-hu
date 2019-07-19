---
title: Webes API-kat meghívó webalkalmazás (kód konfigurációja) – Microsoft Identity platform
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó webalkalmazást (az alkalmazás kódjának konfigurációja)
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
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15c12aebccf34957db8442034ebbcd6ac7c107e1
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68276729"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Webes API-kat meghívó webalkalmazás – kód konfigurálása

Ahogy az a [webalkalmazás-bejelentkezési felhasználók](scenario-web-app-sign-user-overview.md)esetében is látható, mivel a bejelentkezett felhasználó delegálva van az Open ID csatlakozás (OIDC) köztes közbenső kapcsolatra, a OIDC folyamatba kívánja csatlakoztatni. Ez a módszer eltérő lehet a használt keretrendszertől függően (itt ASP.NET és ASP.NET Core), de a végén előfizethet a köztes OIDC eseményekre. Ennek az az elve, hogy:

- Engedélyezheti a ASP.NET vagy a ASP.NET Core kérelmének engedélyezési kódját. Ezzel a ASP.NET/ASP.NET mag lehetővé teszi, hogy a felhasználó bejelentkezzen és beleegyezik,
- A webalkalmazás az engedélyezési kód fogadására fog előfizetni.
- Az Auth kód fogadásakor a MSAL-kódtárak használatával válthatja be a kódot és az eredményül kapott hozzáférési jogkivonatokat, valamint a tokenek gyorsítótárában található frissítési tokeneket. Ettől kezdve a gyorsítótár az alkalmazás más részeiben is használható, hogy csendesen szerezzen más jogkivonatokat.

## <a name="libraries-supporting-web-app-scenarios"></a>Webalkalmazás-forgatókönyveket támogató könyvtárak

A Web Apps engedélyezési kódját támogató kódtárak a következők:

| MSAL-könyvtár | Leírás |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | A támogatott platformok a .NET-keretrendszer és a .NET Core platform (a UWP, a Xamarin. iOS és a Xamarin. Android), mivel ezek a platformok nyilvános ügyfélalkalmazások létrehozására használhatók. |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Fejlesztés folyamatban – nyilvános előzetes verzió |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Fejlesztés folyamatban – nyilvános előzetes verzió |

## <a name="aspnet-core-configuration"></a>ASP.NET Core konfiguráció

ASP.net Core a dolgok a `Startup.cs` fájlban történnek. Elő kell fizetnie az `OnAuthorizationCodeReceived` Open ID csatlakozási eseményre, és ebből az eseményből hívja meg a MSAL. A net metódusa `AcquireTokenFromAuthorizationCode` , amely a jogkivonat-gyorsítótárban való tárolást, a kért hatókörök hozzáférési jogkivonatát, valamint egy frissítési jogkivonatot tartalmaz, amelyet a hozzáférési jogkivonat frissítéséhez fog használni a lejárati időponthoz képest, vagy ha egy jogkivonatot kap ugyanazon felhasználó nevében , de egy másik erőforráshoz.

Az alábbi kódban szereplő megjegyzések segítenek megérteni a MSAL.NET és a ASP.NET Core szövésének néhány trükkös aspektusát. Részletes információk a [ASP.net Core Web App növekményes oktatóanyagában, 2. fejezet](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)

```CSharp
  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Response type. We ask ASP.NET to request an Auth Code, and an IDToken
   options.ResponseType = OpenIdConnectResponseType.CodeIdToken;

   // This "offline_access" scope is needed to get a refresh token when users sign in with
   // their Microsoft personal accounts
   // (it's required by MSAL.NET and automatically provided by Azure AD when users
   // sign in with work or school accounts, but not with their Microsoft personal accounts)
   options.Scope.Add(OidcConstants.ScopeOfflineAccess);
   options.Scope.Add("user.read"); // for instance

   // Handling the auth redemption by MSAL.NET so that a token is available in the token cache
   // where it will be usable from Controllers later (through the TokenAcquisition service)
   var handler = options.Events.OnAuthorizationCodeReceived;
   options.Events.OnAuthorizationCodeReceived = async context =>
   {
    // As AcquireTokenByAuthorizationCode is asynchronous we want to tell ASP.NET core
    // that we are handing the code even if it's not done yet, so that it does 
    // not concurrently call the Token endpoint.
    context.HandleCodeRedemption();

    // Call MSAL.NET AcquireTokenByAuthorizationCode
    var application = BuildConfidentialClientApplication(context.HttpContext,
                                                         context.Principal);
    var result = await application.AcquireTokenByAuthorizationCode(scopes.Except(scopesRequestedByMsalNet), context.ProtocolMessage.Code)
                                  .ExecuteAsync();

    // Do not share the access token with ASP.NET Core otherwise ASP.NET will cache it
    // and will not send the OAuth 2.0 request in case a further call to
    // AcquireTokenByAuthorizationCodeAsync in the future for incremental consent 
    // (getting a code requesting more scopes)
    // Share the ID Token so that the identity of the user is known in the application (in 
    // HttpContext.User)
    context.HandleCodeRedemption(null, result.IdToken);

    // Call the previous handler if any
    await handler(context);
   };
```

ASP.NET Core a bizalmas ügyfélalkalmazás felépítése a HttpContext található információkat használja. Ez a HttpContext ismeri a webalkalmazás URL-címét és a bejelentkezett felhasználót (a-ben `ClaimsPrincipal`). A ASP.net Core konfigurációt is használja, amely "AzureAD" szakasszal rendelkezik, és amely az `_applicationOptions` adatstruktúrához van kötve. Végül az alkalmazásnak meg kell őriznie a jogkivonat-gyorsítótárat.

```CSharp
/// <summary>
/// Creates an MSAL Confidential client application
/// </summary>
/// <param name="httpContext">HttpContext associated with the OIDC response</param>
/// <param name="claimsPrincipal">Identity for the signed-in user</param>
/// <returns></returns>
private IConfidentialClientApplication BuildConfidentialClientApplication(HttpContext httpContext, ClaimsPrincipal claimsPrincipal)
{
 var request = httpContext.Request;

 // Find the URI of the application)
 string currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, azureAdOptions.CallbackPath ?? string.Empty);

 // Updates the authority from the instance (including national clouds) and the tenant
 string authority = $"{azureAdOptions.Instance}{azureAdOptions.TenantId}/";

 // Instantiates the application based on the application options (including the client secret)
 var app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
               .WithRedirectUri(currentUri)
               .WithAuthority(authority)
               .Build();

 // Initialize token cache providers. In the case of Web applications, there must be one
 // token cache per user (here the key of the token cache is in the claimsPrincipal which
 // contains the identity of the signed-in user)
 if (this.UserTokenCacheProvider != null)
 {
  this.UserTokenCacheProvider.Initialize(app.UserTokenCache, httpContext, claimsPrincipal);
 }
 if (this.AppTokenCacheProvider != null)
 {
  this.AppTokenCacheProvider.Initialize(app.AppTokenCache, httpContext);
 }
 return app;
}
```

`AcquireTokenByAuthorizationCode`valóban beváltja a ASP.NET által kért hitelesítési kódot, és beolvassa a MSAL.NET felhasználói jogkivonat-gyorsítótárhoz hozzáadott jogkivonatokat. Innentől kezdve a ASP.NET Core vezérlőkben lesznek használatban.

## <a name="aspnet-configuration"></a>ASP.NET-konfiguráció

A ASP.net kezelési módja hasonló, azzal a különbséggel, hogy a OpenIdConnect és az `OnAuthorizationCodeReceived` eseményre való előfizetés a `App_Start\Startup.Auth.cs` fájlban történik. Hasonló fogalmakat talál, kivéve, hogy itt meg kell adnia a RedirectUri a konfigurációs fájlban, amely egy kicsit kevésbé robusztus:

```CSharp
private void ConfigureAuth(IAppBuilder app)
{
 app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

 app.UseCookieAuthentication(new CookieAuthenticationOptions { });

 app.UseOpenIdConnectAuthentication(
 new OpenIdConnectAuthenticationOptions
 {
  Authority = Globals.Authority,
  ClientId = Globals.ClientId,
  RedirectUri = Globals.RedirectUri,
  PostLogoutRedirectUri = Globals.RedirectUri,
  Scope = Globals.BasicSignInScopes, // a basic set of permissions for user sign in & profile access
  TokenValidationParameters = new TokenValidationParameters
  {
   NameClaimType = "name",
  },
  Notifications = new OpenIdConnectAuthenticationNotifications()
  {
   AuthorizationCodeReceived = OnAuthorizationCodeReceived,
  }
 });
}
```

```CSharp
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification context)
{
 // Upon successful sign in, get & cache a token using MSAL
 string userId = context.AuthenticationTicket.Identity.FindFirst(ClaimTypes.NameIdentifier).Value;
 IConfidentialClientApplication clientapp;
 clientApp = ConfidentialClientApplicationBuilder.Create(Globals.ClientId)
                  .WithClientSecret(Globals.ClientSecret)
                  .WithRedirectUri(Globals.RedirectUri)
                  .WithAuthority(new Uri(Globals.Authority))
                  .Build();

  EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);

 AuthenticationResult result = await clientapp.AcquireTokenByAuthorizationCode(scopes, context.Code)
                                              .ExecuteAsync();
}
```

Végül, az ügyfél titkos kulcsa helyett a bizalmas ügyfélalkalmazás is igazolhatja személyazonosságát egy ügyféltanúsítvány vagy egy ügyfél-tanúsítvány használatával.
Az ügyfél-kijelentések használata egy speciális forgatókönyv, amely részletesen szerepel az [ügyfél](msal-net-client-assertions.md) -kijelentésekben

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>MSAL.NET-jogkivonat gyorsítótára egy ASP.NET (Core) webalkalmazáshoz

A Web Apps (vagy a webes API-k) esetében a jogkivonat-gyorsítótár implementációja eltér az asztali alkalmazások jogkivonat-gyorsítótárának implementációjában (amelyek gyakran [fájl](scenario-desktop-acquire-token.md#file-based-token-cache)-alapúak. Használhatja a ASP.NET/ASP.NET Core-munkamenetet, vagy egy Redis-gyorsítótárat, vagy egy adatbázist, vagy akár az Azure Blob Storage-ot is. A fenti kódrészletben ez a `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` metódus hívásának objektuma, amely egy gyorsítótár-szolgáltatást köti össze. Az itt megjelenő részletek a forgatókönyvre vonatkozó útmutató hatókörén kívül esnek, de az alábbi hivatkozások is elérhetők.

> [!IMPORTANT]
> Fontos megjegyezni, hogy a webalkalmazások és a webes API-k esetében felhasználónként egy jogkivonat-gyorsítótárnak kell lennie (felhasználónként). Minden fiókhoz szerializálnia kell a jogkivonat-gyorsítótárat.

Példák a Web Apps és a webes API-k jogkivonat-gyorsítótárának használatára a [ASP.net Core webalkalmazás](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) -oktatóanyagban az 2-2-os [token gyorsítótárában](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). A megvalósítások esetében tekintse meg a következő mappát a [Microsoft-Authentication-Extensions-for-DotNet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) Library ( [Microsoft. Identity. Client. Extensions. Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) mappában) [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) .

## <a name="next-steps"></a>További lépések

Ezen a ponton, amikor a felhasználó bejelentkezik a jogkivonat-gyorsítótárba, a rendszer a tokent tárolja. Lássuk, hogyan használják majd a webalkalmazás más részeiben.

> [!div class="nextstepaction"]
> [Bejelentkezés a webalkalmazásba](scenario-web-app-call-api-sign-in.md)
