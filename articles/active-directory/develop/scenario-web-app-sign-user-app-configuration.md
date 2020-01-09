---
title: Felhasználói bejelentkezést használó webalkalmazás konfigurálása – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan hozhat létre egy webalkalmazást, amely a felhasználók számára jelentkezik (kód konfigurálása)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe845fca4a50828cabbf6c360cb9bc65dd20ae7b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423529"
---
# <a name="web-app-that-signs-in-users-code-configuration"></a>Felhasználók számára bejelentkező webalkalmazás: kód konfigurálása

Megtudhatja, hogyan konfigurálhatja a webalkalmazáshoz tartozó kódot, amely a felhasználóknál jelentkezik.

## <a name="libraries-for-protecting-web-apps"></a>A webalkalmazások védelméhez használható kódtárak

<!-- This section can be in an include for Web App and Web APIs -->
A webalkalmazások (és webes API-k) elleni védelemhez használt kódtárak a következők:

| Platform | Részletes ismertetés | Leírás |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [A .NET-hez készült Identity Model-bővítmények](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | A ASP.NET és a ASP.NET Core által közvetlenül használt Microsoft Identity Model Extensions for .NET azt javasolja, hogy a .net-keretrendszerben és a .NET Core-ban is fusson a DLL-fájlok összessége. Egy ASP.NET vagy ASP.NET Core webalkalmazásból a jogkivonat-érvényesítést a **TokenValidationParameters** osztály használatával (különösen bizonyos partneri forgatókönyvekben) lehet szabályozni. |
| ![Java](media/sample-v2-code/small_logo_java.png) | [MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Java-webalkalmazások támogatása |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | Python-webalkalmazások támogatása |

Válassza ki az Önt érdeklő platformhoz tartozó lapot:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

A cikkben szereplő kódrészletek és a következők a [ASP.net Core Web App növekményes oktatóanyagból, 1. fejezetből](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg)származnak.

Ebben az oktatóanyagban érdemes megtekinteni a teljes megvalósítás részleteit.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A cikkben szereplő kódrészletek és a következők a [ASP.net Web App mintából](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)származnak.

Érdemes lehet ezt a mintát a teljes megvalósítás részleteit megtekinteni.

# <a name="javatabjava"></a>[Java](#tab/java)

Ebben a cikkben a kódrészleteket és a következő, a Java- [webalkalmazásban a Microsoft Graph mintát hívó Java-webalkalmazásból](https://github.com/Azure-Samples/ms-identity-java-webapp) kinyert MSAL.

Érdemes lehet ezt a mintát a teljes megvalósítás részleteit megtekinteni.

# <a name="pythontabpython"></a>[Python](#tab/python)

A cikkben szereplő kódrészletek és a következők a Python [webalkalmazásból](https://github.com/Azure-Samples/ms-identity-python-webapp) származnak, amely a MSAL Pythonban hívja meg a Microsoft Graph mintát.

Érdemes lehet ezt a mintát a teljes megvalósítás részleteit megtekinteni.

---

## <a name="configuration-files"></a>Konfigurációs fájlok

A felhasználókat a Microsoft Identity platform használatával bejelentkező webalkalmazások általában konfigurációs fájlokon keresztül konfigurálhatók. A kitöltendő beállítások a következők:

- A Cloud instance (`Instance`), ha azt szeretné, hogy az alkalmazás a nemzeti felhőkben fusson, például:
- A bérlő AZONOSÍTÓjának célközönsége (`TenantId`)
- Az alkalmazás ügyfél-azonosítója (`ClientId`), amelyet a rendszer a Azure Portalból másolt

Előfordulhat, hogy az alkalmazásokat a `Authority`parametrized, amely `Instance` és `TenantId`összefűzése.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core ezek a beállítások a [appSettings. JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) fájlban találhatók, a "AzureAd" szakaszban.

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
    "TenantId": "[Enter the tenantId here]]",

    // Client ID (application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

ASP.NET Core egy másik fájl ([properties\launchSettings.JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7)) tartalmazza az alkalmazáshoz tartozó URL-címet (`applicationUrl`) és az SSL-portot (`sslPort`) és a különböző profilokat.

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

A Azure Portal az alkalmazás **hitelesítési** lapján regisztrálni kívánt válasz URI-azonosítóknak meg kell egyezniük ezekkel az URL-címekkel. A két előző konfigurációs fájl esetében `https://localhost:44321/signin-oidc`. Ennek az az oka, hogy `applicationUrl` `http://localhost:3110`, de `sslPort` van megadva (44321). a `CallbackPath` `/signin-oidc`a `appsettings.json`ban meghatározottak szerint.

Ugyanígy a kijelentkezési URI-t is `https://localhost:44321/signout-callback-oidc`értékre kell állítani.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET-ben az alkalmazás a [web. config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) fájlban van konfigurálva, a 12 – 15. sorokban.

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

A Azure Portal az alkalmazás **hitelesítési** lapján regisztrálni kívánt válasz URI-azonosítóknak meg kell egyezniük ezekkel az URL-címekkel. Tehát `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

A Java-ban a konfiguráció a `src/main/resources`alatt található [Application. properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) fájlban található.

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraph=http://localhost:8080/msal4jsample/graph/me
```

A Azure Portal az alkalmazás **hitelesítési** lapján regisztrálni kívánt válasz URI-azonosítóknak meg kell egyezniük az alkalmazás által definiált `redirectUri` példányokkal. Tehát `http://localhost:8080/msal4jsample/secure/aad` és `http://localhost:8080/msal4jsample/graph/me`kell őket.

# <a name="pythontabpython"></a>[Python](#tab/python)

Itt látható a Python konfigurációs fájlja [app_config.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py)a következőben:

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So the token cache will be stored in a server-side session
```

> [!NOTE]
> Ez a rövid útmutató azt javasolja, hogy az egyszerűség kedvéért a konfigurációs fájlban tárolja az ügyfél titkos kulcsát. Az éles alkalmazásban más módszerekkel is elvégezheti a titkos kulcs (például kulcstartó vagy környezeti változó) tárolását a [lombik dokumentációjában](https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables)leírtak szerint.
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Inicializálási kód

Az inicializálási kód a platformtól függően eltérő. ASP.NET Core és ASP.NET esetében a bejelentkezést a felhasználók az OpenID Connect middleware-be delegálják. A ASP.NET vagy a ASP.NET Core sablon webalkalmazásokat hoz létre a Azure Active Directory (Azure AD) 1.0-s verziójának végpontján. Néhány konfigurációra van szükség a Microsoft Identity platform (v 2.0) végponthoz való alkalmazkodáshoz. Java esetén a rugó az alkalmazás együttműködésével van kezelve.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core Web Apps (és webes API-k) esetében az alkalmazás védett, mert `[Authorize]` attribútuma van a vezérlőkön vagy a vezérlő műveletein. Ez az attribútum ellenőrzi, hogy a felhasználó hitelesítése megtörtént-e. Az alkalmazást inicializáló kód a Startup.cs fájlban található.

A Microsoft Identity platform (korábbi nevén Azure AD v 2.0) használatával történő hitelesítés hozzáadásához hozzá kell adnia a következő kódot. A kódban szereplő megjegyzéseknek magától értetődőnek kell lenniük.

> [!NOTE]
> Ha elindítja a projektet a Visual Studióban lévő alapértelmezett ASP.NET Core webes projekttel, vagy a `dotnet new mvc`használatával, akkor a metódus `AddAzureAD` alapértelmezés szerint elérhető. Ennek az az oka, hogy a kapcsolódó csomagokat a rendszer automatikusan betölti.
>
> Ha teljesen új projektet hoz létre, és a következő kódot próbálja használni, javasoljuk, hogy a `AddAzureAD` metódus elérhetővé tételéhez adja hozzá a **Microsoft. AspNetCore. Authentication. AzureAD. UI** NuGet-csomagot a projekthez.

A következő kód érhető el a [Startup. cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34).

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

A `AddMicrosoftIdentityPlatformAuthentication` Extension metódus a [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23)van definiálva. Ez

- Hozzáadja a hitelesítési szolgáltatást.
- A konfigurációs fájl olvasásához szükséges beállításokat konfigurálja.
- Az OpenID Connect beállításainak konfigurálása, hogy a használt szolgáltató a Microsoft Identity platform (korábbi nevén Azure AD v 2.0) végpont.
- Ellenőrzi a jogkivonat kiállítóját.
- Gondoskodik arról, hogy a névnek megfelelő jogcímek a `preferred_username` jogcím alapján legyenek leképezve az azonosító jogkivonatban.

A konfiguráció mellett a `AddMicrosoftIdentityPlatformAuthentication`hívásakor megadhatja a konfigurációs szakasz nevét is. Alapértelmezés szerint a `AzureAd`.

Az OpenId Connect middleware-események nyomon követése segíthet a webalkalmazások hibakeresésében, ha a hitelesítés nem működik. A `true` `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` beállítása megmutatja, hogyan történik az információk kifejlesztése a ASP.NET Core köztes middleware-készlet alapján, mivel a HTTP-válaszból a `HttpContext.User`felhasználó identitására kerül sor.

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

A `AadIssuerValidator` osztály lehetővé teszi, hogy a jogkivonat kiállítója sok esetben érvényesíthető legyen. Ez az osztály egy v 1.0 vagy v 2.0 jogkivonattal, egy egybérlős vagy több-bérlős alkalmazással, vagy egy olyan alkalmazással működik, amely a felhasználók személyes Microsoft-fiókjait az Azure nyilvános felhőben vagy az országos felhőkben aláírja. A [Microsoft. Identity. Web/Resource/AadIssuerValidator. cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs)fájlból érhető el.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET webalkalmazás és webes API-k hitelesítéséhez kapcsolódó kód a [App_Start/Startup.auth.cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) fájlban található.

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

# <a name="javatabjava"></a>[Java](#tab/java)

A Java-minta a Spring Framework-t használja. Az alkalmazás védett, mert egy szűrőt valósít meg, amely elfogja az egyes HTTP-válaszokat. A Java-webalkalmazások rövid útmutatójában ez a szűrő `AuthFilter` `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`.

A szűrő feldolgozza a OAuth 2,0 engedélyezési kód folyamatát, és ellenőrzi, hogy a felhasználó hitelesítve van-e (`isAuthenticated()` metódus). Ha a felhasználó nincs hitelesítve, kiszámítja az Azure AD-engedélyezési végpontok URL-címét, és átirányítja a böngészőt erre az URI-ra.

Ha a válasz érkezik, amely az engedélyezési kódot tartalmazza, a MSAL Java használatával szerzi be a jogkivonatot. Amikor végül megkapja a tokent a jogkivonat-végponttól (az átirányítási URI-n), a felhasználó bejelentkezett.

Részletekért tekintse meg a [AuthFilter. java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java)`doFilter()` metódusát.

> [!NOTE]
> A `doFilter()` kódja némileg eltérő sorrendben van megírva, de a folyamat az egyik leírása.

Az ezzel a módszerrel aktiválható engedélyezési kód folyamatával kapcsolatos részleteket lásd: [Microsoft Identity platform és OAuth 2,0 engedélyezési kód folyamata](v2-oauth2-auth-code-flow.md).

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python-minta a lombikot használja. A lombik és a MSAL Python inicializálása az [app. # L1-L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28).

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

## <a name="next-steps"></a>Következő lépések

A következő cikkben megtudhatja, hogyan aktiválhatja a bejelentkezést és a kijelentkezést.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Be- és kijelentkezés](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Be- és kijelentkezés](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Be- és kijelentkezés](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Be- és kijelentkezés](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-sign-user-sign-in?tabs=python)

---
