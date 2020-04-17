---
title: Felhasználókba bejelentkező webalkalmazás konfigurálása – Microsoft identity platform | Azure
description: Megtudhatja, hogyan hozhat létre olyan webalkalmazást, amely bejelentkezik a felhasználókba (kódkonfiguráció)
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
ms.openlocfilehash: 3cb51a57baa87849e97f7b05762dc4d6eba787a6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537111"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>A felhasználókban bejelentkező webalkalmazás: Kódkonfiguráció

Ismerje meg, hogyan konfigurálhatja a felhasználókat bejelentkező webalkalmazás kódját.

## <a name="libraries-for-protecting-web-apps"></a>Tárak a webalkalmazások védelméhez

<!-- This section can be in an include for web app and web APIs -->
A webalkalmazások (és a webes API- k) védelmére használt tárak a következők:

| Platform | Erőforrástár | Leírás |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Identity Model Extensions for .NET (A. net identitásmodell-bővítményei)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | A ASP.NET és ASP.NET Core által közvetlenül használt Microsoft Identity Model Extensions for . Egy ASP.NET vagy ASP.NET Core webalkalmazásból a **TokenValidationParameters** osztály használatával (különösen bizonyos partnerforgatókönyvekben) vezérelheti a tokenérvényesítési érvényesítést. |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Java webes alkalmazások támogatása |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Python-webalkalmazások támogatása |

Válassza ki azt a lapot, amely megfelel az Önt érdeklő platformnak:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Kódrészletek ebben a cikkben, és a következő kivont [a ASP.NET Core web app növekményes bemutató, 1.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)

Előfordulhat, hogy a teljes megvalósítási részletekért tekintse meg ezt az oktatóanyagot.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Ebben a cikkben és az alábbiakban szereplő kódrészletek a [ASP.NET webalkalmazás-mintából származnak.](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)

Előfordulhat, hogy a teljes megvalósítási részletekért tekintse meg ezt a mintát.

# <a name="java"></a>[Java](#tab/java)

A cikkben szereplő kódrészletek és az alábbiak a Microsoft graph minta MSAL Java-ban [történő hívását hívó Java webalkalmazásból](https://github.com/Azure-Samples/ms-identity-java-webapp) származnak.

Előfordulhat, hogy a teljes megvalósítási részletekért tekintse meg ezt a mintát.

# <a name="python"></a>[Python](#tab/python)

Kódrészletek ebben a cikkben, és az alábbiak kivonatok a [Python webalkalmazás, amelyben](https://github.com/Azure-Samples/ms-identity-python-webapp) a Microsoft graph minta MSAL Pythonban.

Előfordulhat, hogy a teljes megvalósítási részletekért tekintse meg ezt a mintát.

---

## <a name="configuration-files"></a>Konfigurációs fájlok

A Microsoft identity platformsegítségével felhasználókat bejelentkező webalkalmazások általában konfigurációs fájlokon keresztül vannak konfigurálva. A kitöltandó beállítások a következők:

- A felhőpéldány`Instance`( ) ha azt szeretné, hogy alkalmazása nemzeti felhőkben fusson, például
- A bérlői azonosító (`TenantId`) célközönsége
- Az alkalmazás ügyfélazonosítója (`ClientId`) az Azure Portalról másolt

Néha az alkalmazásokat parametrizálhatja `Authority`, ami a `Instance` `TenantId`és a összefűzése.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET Core,ezek a beállítások találhatók az [appsettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) fájlban, az "AzureAd" szakaszban.

```Json
{
  "AzureAd": {
    // Azure cloud instance among:
    // - "https://login.microsoftonline.com/" for Azure public cloud
    // - "https://login.microsoftonline.us/" for Azure US government
    // - "https://login.microsoftonline.de/" for Azure AD Germany
    // - "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD audience among:
    // - "TenantId" as a GUID obtained from the Azure portal to sign in users in your organization
    // - "organizations" to sign in users in any work or school account
    // - "common" to sign in users with any work or school account or Microsoft personal account
    // - "consumers" to sign in users with a Microsoft personal account only
    "TenantId": "[Enter the tenantId here]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

A Core ASP.NET egy másik fájl ([properties\launchSettings.json](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) tartalmazza az alkalmazás URL-címét (`applicationUrl`) és a TLS/SSL portot (`sslPort`) az alkalmazáshoz és a különböző profilokhoz.

```Json
{
  "iisSettings": {
    "windowsAuthentication": false,
    "anonymousAuthentication": true,
    "iisExpress": {
      "applicationUrl": "http://localhost:3110/",
      "sslPort": 44321
    }
  },
  "profiles": {
    "IIS Express": {
      "commandName": "IISExpress",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    },
    "webApp": {
      "commandName": "Project",
      "launchBrowser": true,
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      },
      "applicationUrl": "http://localhost:3110/"
    }
  }
}
```

Az Azure Portalon a válasz URI-k, amelyek regisztrálnia kell a **hitelesítési** lapon az alkalmazás kell egyeznie ezek az URL-címek. Az előző két konfigurációs fájl `https://localhost:44321/signin-oidc`esetében a . Ennek az `applicationUrl` az `http://localhost:3110`oka, de `sslPort` meg van adva (44321). `CallbackPath`a `/signin-oidc`. `appsettings.json`

Ugyanígy a kijelentkezési URI-t `https://localhost:44321/signout-callback-oidc`a ..

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

ASP.NET az alkalmazás a [Web.config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) fájlon keresztül, a 12–15.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, visit
  https://go.microsoft.com/fwlink/?LinkId=301880
  -->
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:ClientId" value="[Enter your client ID, as obtained from the app registration portal]" />
    <add key="ida:ClientSecret" value="[Enter your client secret, as obtained from the app registration portal]" />
    <add key="ida:AADInstance" value="https://login.microsoftonline.com/{0}{1}" />
    <add key="ida:RedirectUri" value="https://localhost:44326/" />
    <add key="vs:EnableBrowserLink" value="false" />
  </appSettings>
```

Az Azure Portalon a válasz URI-k, amelyek regisztrálnia kell a **hitelesítési** lapon az alkalmazás kell egyeznie ezek az URL-címek. Azaz, hogy `https://localhost:44326/`kell .

# <a name="java"></a>[Java](#tab/java)

Java-ban a konfiguráció a [alkalmazás.properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) fájlban található, amely a területen `src/main/resources`található.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

Az Azure Portalon a válasz URI-k, amelyek regisztrálva kell az `redirectUri` alkalmazás **hitelesítési** lapján kell egyeznie az alkalmazás által meghatározott példányok. Azaz, hogy `http://localhost:8080/msal4jsample/secure/aad` kell, és `http://localhost:8080/msal4jsample/graph/me`.

# <a name="python"></a>[Python](#tab/python)

Itt a Python konfigurációs fájl [app_config.py:](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Ez a rövid útmutató azt javasolja, hogy az ügyféltitkos kulcsot az egyszerűség kedvéért tárolja a konfigurációs fájlban. Az éles alkalmazásban a titkos kulcs tárolására más módokat is szeretne használni, például egy key vaultot vagy egy környezeti változót a [Flask dokumentációjában](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)leírtak szerint.
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Inicializálási kód

Az inicializációs kód a platformtól függően eltérő. A ASP.NET Core és ASP.NET esetében a bejelentkezésa felhasználókat delegálja az OpenID Connect köztes szoftvernek. A ASP.NET vagy ASP.NET Core sablon webes alkalmazásokat hoz létre az Azure Active Directory (Azure AD) 1.0-s-as v1.0-s végpontjához. Néhány konfiguráció szükséges a Microsoft identity platform (2.0-s verzió) végpontjához való igazításhoz. Java esetén a Spring kezeli az alkalmazás együttműködésével.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET Core webalkalmazások (és webes API-k), az `[Authorize]` alkalmazás védett, mert rendelkezik egy attribútum a vezérlők vagy a vezérlő műveletek. Ez az attribútum ellenőrzi, hogy a felhasználó hitelesítve van-e. Az alkalmazást inicializáló kód a Startup.cs fájlban található.

Hitelesítés hozzáadása a Microsoft identitásplatform (korábban Azure AD v2.0), hozzá kell adnia a következő kódot. A kódban található megjegyzéseknek magától értetődőnek kell lenniük.

> [!NOTE]
> Ha a projektet a Visual studio-n belüli alapértelmezett `dotnet new mvc`ASP.NET `AddAzureAD` Core webprojekttel vagy a használatával indítja, a módszer alapértelmezés szerint elérhető. Ennek az az oka, hogy a kapcsolódó csomagok automatikusan betöltődnek.
>
> Ha teljesen új projektet hoz létre, és a következő kódot próbálja használni, javasoljuk, hogy adja hozzá a **Microsoft.AspNetCore.Authentication.AzureAD.UI** NuGet csomagot a projekthez a `AddAzureAD` módszer elérhetővé tételéhez.

A következő kód a [Startup.cs#L33-L34 oldalon érhető](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)el.

```csharp
public class Startup
{
 ...

  // This method is called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign in users with the Microsoft identity platform
      services.AddMicrosoftIdentityPlatformAuthentication(Configuration);

      services.AddMvc(options =>
      {
          var policy = new AuthorizationPolicyBuilder()
              .RequireAuthenticatedUser()
              .Build();
            options.Filters.Add(new AuthorizeFilter(policy));
            })
        .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
    }
```

A `AddMicrosoftIdentityPlatformAuthentication` bővítménymetódus a [Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23)nyelven van definiálva. Ez:

- Hozzáadja a hitelesítési szolgáltatást.
- A konfigurációs fájl olvasási beállításainak megadása.
- Az OpenID Connect beállításait úgy konfigurálja, hogy a használt jogosultsága a Microsoft identity platform (korábban Azure AD v2.0) végpont.
- Ellenőrzi a jogkivonat kibocsátóját.
- Biztosítja, hogy a névnek megfelelő jogcímek `preferred_username` le vannak képezve a jogcímből az azonosító jogkivonatban.

A konfiguráció mellett megadhatja a konfigurációs szakasz nevét `AddMicrosoftIdentityPlatformAuthentication`is a híváskor. Alapértelmezés szerint ez `AzureAd`.

Az OpenId Connect köztes szoftveresemények nyomon követése segíthet a webalkalmazás hibaelhárításában, ha a hitelesítés nem működik. Beállítás megmutatja, `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` hogyan dolgozza ki az információkat a ASP.NET Core köztes szoftver készlete, ahogy halad a HTTP-válaszból a felhasználó identitására a rendszerben. `HttpContext.User` `true`

```csharp
/// <summary>
/// Add authentication with the Microsoft identity platform.
/// This method expects the configuration file to have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenID Connect events.
/// </param>
/// <returns></returns>
public static IServiceCollection AddMicrosoftIdentityPlatformAuthentication(
  this IServiceCollection services,
  IConfiguration configuration,
  string configSectionName = "AzureAd",
  bool subscribeToOpenIdConnectMiddlewareDiagnosticsEvents = false)
{
  services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
      .AddAzureAD(options => configuration.Bind(configSectionName, options));
  services.Configure<AzureADOptions>(options => configuration.Bind(configSectionName, options));

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
      // Per the following code, this application signs in users in any work or school
      // accounts and any Microsoft personal accounts.
      // If you want to direct Azure AD to restrict the users who can sign in, change
      // the tenant value of the appsettings.json file in the following way:
      // - Only work or school accounts => 'organizations'
      // - Only Microsoft personal accounts => 'consumers'
      // - Work or school and personal accounts => 'common'
      // If you want to restrict the users who can sign in to only one tenant,
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization.
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users who can sign in to several organizations,
      // set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to the options.TokenValidationParameters.ValidIssuers collection.
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set nameClaimType to be preferred_username.
      // This change is needed because certain token claims from the Azure AD v1 endpoint
      // (on which the original .NET Core template is based) are different from the Microsoft identity platform endpoint.
      // For more details, see [ID tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens).
      options.TokenValidationParameters.NameClaimType = "preferred_username";

      // ...

      if (subscribeToOpenIdConnectMiddlewareDiagnosticsEvents)
      {
          OpenIdConnectMiddlewareDiagnostics.Subscribe(options.Events);
      }
  });
  return services;
}
  ...
```

Az `AadIssuerValidator` osztály lehetővé teszi a jogkivonat kibocsátójának érvényesítését sok esetben. Ez az osztály egy 1.0-s vagy 2.0-s verziós jogkivonattal, egy egy- vagy több-bérlős alkalmazással, vagy egy olyan alkalmazással működik, amely az Azure nyilvános felhőben vagy nemzeti felhőben lévő személyes Microsoft-fiókjaikkal jelentkezik be a felhasználókba. Elérhető a [Microsoft.Identity.Web/Resource/AadIssuerValidator.cs.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET webappokban és webes API-kban a hitelesítéshez kapcsolódó kód a [App_Start/Startup.Auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) fájlban található.

```csharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0.
     // `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/.
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="java"></a>[Java](#tab/java)

A Java minta a tavaszi keretrendszert használja. Az alkalmazás védett, mert egy szűrőt valósít meg, amely elfogja az egyes HTTP-válaszokat. A Java webalkalmazások rövid útmutatójában `AuthFilter` ez `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`a szűrő a.

A szűrő feldolgozza az OAuth 2.0 engedélyezési kód folyamatát, és ellenőrzi, hogy a felhasználó hitelesített`isAuthenticated()` (metódus). Ha a felhasználó nincs hitelesítve, kiszámítja az Azure AD engedélyezési végpontok URL-címét, és átirányítja a böngészőt erre az URI-ra.

Amikor a válasz megérkezik, amely tartalmazza az engedélyezési kódot, beszerzi a jogkivonatot az MSAL Java használatával. Amikor végül megkapja a jogkivonatot a jogkivonat-végpontról (az átirányítási URI-n), a felhasználó be van jelentkezve.

További részletek az `doFilter()` [AuthFilter.java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java).

> [!NOTE]
> A kód `doFilter()` van írva egy kicsit más sorrendben, de a folyamat a leírt.

A módszer által aktivált engedélyezési kódfolyamatról a Microsoft identity platform és az [OAuth 2.0 engedélyezési kódfolyamat című témakörben talál.](v2-oauth2-auth-code-flow.md)

# <a name="python"></a>[Python](#tab/python)

A Python minta flaskát használ. A Flask és az MSAL Python inicializálása az [app.py#L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28)nyelven történik.

```Python
import uuid
import requests
from flask import Flask, render_template, session, request, redirect, url_for
from flask_session import Session  # https://pythonhosted.org/Flask-Session
import msal
import app_config


app = Flask(__name__)
app.config.from_object(app_config)
Session(app)
```

---

## <a name="next-steps"></a>További lépések

A következő cikkben megtudhatja, hogyan aktiválhatja a bejelentkezést és a kijelentkezést.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Be- és kijelentkezés](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Be- és kijelentkezés](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Be- és kijelentkezés](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Be- és kijelentkezés](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
