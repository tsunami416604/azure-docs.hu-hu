---
title: Webalkalmazás, hogy a hívások webes API-k (kód konfiguráció) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy webalkalmazást, hogy a hívások webes API-k (alkalmazás kód konfigurálása)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd7f393f889facf147cf25625d5c3b20f886ddf5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784945"
---
# <a name="web-app-that-calls-web-apis---code-configuration"></a>Webalkalmazás, hogy a hívások webes API-k – helykódot

Látható módon a [webalkalmazás-jelentkezik be a felhasználók forgatókönyv](scenario-web-app-sign-user-overview.md), tekintve, hogy felhasználói bejelentkezés delegált az Open ID connect (OIDC) közbenső szoftverek, a OIDC folyamat hook fel szeretné. Az, hogy módja attól függően változik, a keretrendszert, használt (ide az ASP.NET, ASP.NET Core), azonban a végén, közbenső OIDC események fog előfizetni. Az egyszerű, hogy:

- Fogja hagyja, hogy az ASP.NET- vagy ASP.NET core-engedélyezési kódot kér. Foglalkozások ASP.NET/ASP.NET core ezzel lehetővé teszik a felhasználó jelentkezzen be, és a jóváhagyás,
- Az engedélyezési kód küldése a webalkalmazás fog előfizetni.
- A hitelesítési kódot fogadásakor a kód és az eredményül kapott hozzáférési jogkivonatokkal történő beváltása és frissítési jogkivonatok tár jogkivonat a gyorsítótárban MSAL kódtárak fogja használni. Itt a gyorsítótár használható az alkalmazás más részein más jogkivonatok beszerzésére beavatkozás nélkül.

## <a name="libraries-supporting-web-app-scenarios"></a>Webalkalmazás-forgatókönyveket támogató kódtárak

A Web Apps a hitelesítési kódfolyamat támogató könyvtárak a következők:

| Az MSAL könyvtár | Leírás |
|--------------|-------------|
| ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | A támogatott platformok azok .NET-keretrendszer és a .NET Core platform (UWP-nem Xamarin.iOS és Xamarin.Android platformokhoz, használnak nyilvános ügyfél alkalmazásokat hozhat létre) |
| ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL.Python | Folyamat – nyilvános előzetes verzióban érhető el a fejlesztést |
| ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL.Java | Folyamat – nyilvános előzetes verzióban érhető el a fejlesztést |

## <a name="aspnet-core-configuration"></a>ASP.NET Core-konfiguráció

Az ASP.NET Core, dolog történik, az a `Startup.cs` fájlt. Fizessen elő szeretné a `OnAuthorizationCodeReceived` nyithatja meg: connect-esemény, és ebben az esetben az MSAL hívja. A NET metódus `AcquireTokenFromAuthorizationCode` amelyek tárolása a jogkivonat a gyorsítótárban, a hozzáférési jogkivonatot a kért hatókörök és a egy frissítési jogkivonat lejárati közelében van a hozzáférési jogkivonat frissítéséhez, vagy ugyanaz a felhasználó nevében egy token beszerzéséhez használt hatása , de egy másik erőforrás számára.

Az alábbi kódot a megjegyzéseket segít megérteni az egyes megkülönböztetni funkcióit Szövödei MSAL.NET és az ASP.NET Core

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

ASP.NET Core használja a bizalmas ügyfél application készítése a HttpContext elemhez kapcsolt található adatok. A HttpContext a Web App és a bejelentkezett felhasználó ismer az URL-cím (az egy `ClaimsPrincipal`). Azt is használja az ASP.NET Core-konfiguráció, amelynek az "Azure ad" szakaszt, és amely kapcsolódik a `_applicationOptions` adatok szerkezetét. Végül pedig az alkalmazásnak kell token gyorsítótárak kezelése.

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

`AcquireTokenByAuthorizationCode` valójában az engedélyezési kódot, az ASP.NET által kért visszaváltja, és lekérdezi a jogkivonatokat, a felhasználói jogkivonatok gyorsítótárát MSAL.NET hozzáadott. Itt zajlik majd, amelyet az ASP.NET Core-tartományvezérlők.

## <a name="aspnet-configuration"></a>ASP.NET-konfiguráció

Az ASP.NET kezeli a dolgok módja hasonló, azzal a különbséggel, hogy OpenIdConnect és az előfizetés konfigurációja a `OnAuthorizationCodeReceived` esemény történik, a `App_Start\Startup.Auth.cs` fájlt. Megtalálhatja hasonló fogalmakat, azzal a különbséggel, hogy itt kell a konfigurációs fájlban, amely egy kicsit kevesebb adja meg a RedirectUri robusztus:

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
  // We'll inject our own issuer validation logic below.
  ValidateIssuer = false,
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

### <a name="msalnet-token-cache-for-a-aspnet-core-web-app"></a>MSAL.NET jogkivonat-gyorsítótár-(mag) az ASP.NET-webalkalmazás

Web apps (vagy webes API-k csupán néhány (tény)), a token gyorsítótár megvalósítási eltér az asztali alkalmazások tokengyorsítótárral megvalósításokhoz (amely általában [fájlalapú](scenario-desktop-acquire-token.md#file-based-token-cache). A ASP.NET/ASP.NET Core munkamenet vagy egy redis Cache gyorsítótárhoz, vagy egy adatbázis, vagy még akkor is, az Azure Blob storage képes használni. A kód a fenti kódrészletben olyan objektum, az a `EnablePersistence(HttpContext, clientApp.UserTokenCache, clientApp.AppTokenCache);` metódushívás, amely összeköti a gyorsítótár-szolgáltatás. Mi az a részletességi történik itt van ez a forgatókönyv az útmutató hatókörén kívül esik, de a hivatkozások az alábbiakban találhatók.

> [!IMPORTANT]
> Egy nagyon fontos, vegye figyelembe, hogy webalkalmazások és webes API-kat, nem kell egy tokengyorsítótárral felhasználónként (fiók). Az egyes fiókok számára tokengyorsítótárral szerializálni kell.

Példák a jogkivonat-gyorsítótárakat használ a webalkalmazásokhoz és webes API-kat érhetők el a [ASP.NET Core-webalkalmazás létrehozására vonatkozó oktatóanyagra](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) fázisában [2-2 Tokengyorsítótárral](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-2-TokenCache). Megvalósításokhoz rendelkeznie a következő mappát egy pillantást [TokenCacheProviders](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web/TokenCacheProviders) a a [microsoft-hitelesítési-bővítmények-az-dotnet](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet) könyvtár (az a [ Microsoft.Identity.Client.Extensions.Web](https://github.com/AzureAD/microsoft-authentication-extensions-for-dotnet/tree/master/src/Microsoft.Identity.Client.Extensions.Web) mappát.

## <a name="next-steps"></a>További lépések

Ezen a ponton a felhasználó bejelentkezik egy token tárolja a jogkivonat a gyorsítótárban. Nézzük meg, majd felhasználásáról a webes alkalmazás más részein.

> [!div class="nextstepaction"]
> [Jelentkezzen be a webalkalmazás](scenario-web-app-call-api-sign-in.md)
