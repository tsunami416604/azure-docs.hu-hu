---
title: Felhasználók számára bejelentkező webes alkalmazás (kód konfigurációja) – Microsoft Identity platform
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
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1453821561ab7bb361fbb3e5d57634cf23a7be2c
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310059"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Felhasználói bejelentkezést használó webalkalmazás – kód konfigurálása

Megtudhatja, hogyan konfigurálhatja a webalkalmazáshoz tartozó kódot, amely bejelentkezik a felhasználók számára.

## <a name="libraries-used-to-protect-web-apps"></a>A Web Apps védeleméhez használt könyvtárak

<!-- This section can be in an include for Web App and Web APIs -->
A webalkalmazások (és a webes API-k) elleni védelemhez használt kódtárak a következők:

| Platform | Erőforrástár | Leírás |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [A .NET-hez készült Identity Model-bővítmények](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | A ASP.NET és a ASP.NET Core által közvetlenül használt Microsoft Identity Extensions for .NET azt javasolja, hogy a .NET-keretrendszer és a .NET Core rendszerű DLL-eket is használják. Egy ASP.NET/ASP.NET Core-webalkalmazásból a jogkivonat-érvényesítés a **TokenValidationParameters** osztály használatával szabályozható (különösen bizonyos ISV-forgatókönyvekben). |
| ![Java](media/sample-v2-code/small_logo_java.png) | [msal4j](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | Java-MSAL – jelenleg nyilvános előzetes verzióban érhető el |
| ![Python](media/sample-v2-code/small_logo_python.png) | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | MSAL for Python – jelenleg nyilvános előzetes verzióban érhető el |

Válassza ki az Önt érdeklő platformhoz tartozó fület:

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

A cikkben szereplő kódrészletek és a következők a [ASP.net Core Web App növekményes oktatóanyagból, 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg). fejezetből származnak.

Ebben az oktatóanyagban érdemes megtekinteni a teljes megvalósítás részleteit.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A cikkben szereplő kódrészletek és a következők a [ASP.net Web App Sample](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect) -ből származnak

Érdemes lehet ezt a mintát a teljes megvalósítás részleteit megtekinteni.

# <a name="javatabjava"></a>[Java](#tab/java)

A cikkben szereplő kódrészletek és a következők a [Java-webalkalmazásból](https://github.com/Azure-Samples/ms-identity-java-webapp) származnak, amely a Microsoft Graph msal4j Web App-mintát hívja meg

Érdemes lehet ezt a mintát a teljes megvalósítás részleteit megtekinteni.

# <a name="pythontabpython"></a>[Python](#tab/python)

A cikkben szereplő kódrészletek és a következők a [Microsoft Graph MSAL meghívó Python-webalkalmazásból](https://github.com/Azure-Samples/ms-identity-python-webapp) származnak. Python Web App-minta

Érdemes lehet ezt a mintát a teljes megvalósítás részleteit megtekinteni.

---

## <a name="configuration-files"></a>Konfigurációs fájlok

A felhasználókat a Microsoft Identity platformmal bejelentkező webalkalmazások általában konfigurációs fájlokon keresztül konfigurálhatók. A kitöltendő beállítások a következők:

- a felhőben `Instance` , ha azt szeretné, hogy az alkalmazás fusson (például az országos felhőkben)
- a célközönség a`tenantId`
- az `clientId` alkalmazáshoz a Azure Portalból másolt.

Előfordulhat, hogy az alkalmazásokat a `authority`parametrized, amely a `instance` és az összefűzése`tenantId`

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core ezek a beállítások a [appSettings. JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/appsettings.json#L2-L8) fájlban találhatók, a "AzureAD" szakaszban.

```Json
{
  "AzureAd": {
    // Azure Cloud instance among:
    // "https://login.microsoftonline.com/" for Azure Public cloud.
    // "https://login.microsoftonline.us/" for Azure US government.
    // "https://login.microsoftonline.de/" for Azure AD Germany
    // "https://login.chinacloudapi.cn/" for Azure AD China operated by 21Vianet
    "Instance": "https://login.microsoftonline.com/",

    // Azure AD Audience among:
    // - the tenant Id as a GUID obtained from the azure portal to sign-in users in your organization
    // - "organizations" to sign-in users in any work or school accounts
    // - "common" to sign-in users with any work and school account or Microsoft personal account
    // - "consumers" to sign-in users with Microsoft personal account only
    "TenantId": "[Enter the tenantId here]]",

    // Client Id (Application ID) obtained from the Azure portal
    "ClientId": "[Enter the Client Id]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc"
  }
}
```

ASP.net Core egy másik [properties\launchSettings.JSON](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/bc564d68179c36546770bf4d6264ce72009bc65a/1-WebApp-OIDC/1-1-MyOrg/Properties/launchSettings.json#L6-L7) , amely tartalmazza az URL-címet (`applicationUrl`) és az SSL-portot (`sslPort`) az alkalmazáshoz és a különböző profilokhoz.

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

A Azure Portal az alkalmazás **hitelesítési** lapján regisztrálni kívánt válasz URI-azonosítóknak meg kell egyezniük az URL-címekkel. Ez azt eredményezi `https://localhost:44321/signin-oidc` , hogy a fenti két konfigurációs fájl esetében a `http://localhost:3110` applicationUrl, de a `/signin-oidc` `sslPort` megadott érték (44321) `appsettings.json`, a `CallbackPath` pedig a.
  
Ugyanígy a kijelentkezési URI is a következőre lesz beállítva `https://localhost:44321/signout-callback-oidc`:.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET-ben az alkalmazás az 12-15 [web. config](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/Web.config#L12-L15) fájlon keresztül lesz konfigurálva.

```XML
<?xml version="1.0" encoding="utf-8"?>
<!--
  For more information on how to configure your ASP.NET application, please visit
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

A Azure Portal az alkalmazás **hitelesítési** lapján regisztrálni kívánt válasz URI-azonosítóknak meg kell egyezniük az URL-címekkel. Ez a `https://localhost:44326/`.

# <a name="javatabjava"></a>[Java](#tab/java)

A Java-ban a konfiguráció az [alkalmazás. properties](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/d55ee4ac0ce2c43378f2c99fd6e6856d41bdf144/src/main/resources/application.properties) fájljában található.`src/main/resources`

```Java
aad.clientId=Enter_the_Application_Id_here
aad.authority=https://login.microsoftonline.com/Enter_the_Tenant_Info_Here/
aad.secretKey=Enter_the_Client_Secret_Here
aad.redirectUriSignin=http://localhost:8080/msal4jsample/secure/aad
aad.redirectUriGraphUsers=http://localhost:8080/msal4jsample/graph/users
```

A Azure Portal az alkalmazás **hitelesítési** lapján regisztrálni kívánt válasz URI azonosítónak meg kell egyeznie az alkalmazás által meghatározott redirectUris, azaz `http://localhost:8080/msal4jsample/secure/aad` a következővel:`http://localhost:8080/msal4jsample/graph/users`

# <a name="pythontabpython"></a>[Python](#tab/python)

Itt látható a Python konfigurációs fájlja a [app_config.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/0.1.0/app_config.py) fileban.

```Python
CLIENT_SECRET = "Enter_the_Client_Secret_Here"
AUTHORITY = "https://login.microsoftonline.com/common""
CLIENT_ID = "Enter_the_Application_Id_here"
ENDPOINT = 'https://graph.microsoft.com/v1.0/users'
SCOPE = ["User.ReadBasic.All"]
SESSION_TYPE = "filesystem"  # So token cache will be stored in server-side session
```

> [!NOTE]
> Ez a rövid útmutató azt javasolja, hogy az egyszerűség kedvéért a konfigurációs fájlban tárolja az ügyfél titkos kulcsát. Az éles alkalmazásban más módszerekkel is elvégezheti a titkos kulcs (például a kulcstartó vagy egy környezeti változó) tárolását a lombik dokumentációjában leírtak szerint: https://flask.palletsprojects.com/en/1.1.x/config/#configuring-from-environment-variables
>
> ```python
> CLIENT_SECRET = os.getenv("CLIENT_SECRET")
> if not CLIENT_SECRET:
>     raise ValueError("Need to define CLIENT_SECRET environment variable")
> ```

---

## <a name="initialization-code"></a>Inicializálási kód

Az inicializálási kód a platformtól függően eltérő. ASP.NET Core és ASP.NET esetében a bejelentkezés a felhasználók számára a OpenIDConnect middleware-re van delegálva. Napjainkban a ASP.NET/ASP.NET Core sablon webalkalmazásokat állít elő az Azure AD v 1.0 végponthoz. Ezért egy kis konfigurációra van szükség a Microsoft Identity platform (v 2.0) végponthoz való alkalmazkodáshoz. Java esetén a rugó az alkalmazás együttműködésével van kezelve.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.net Core Web Apps (és webes API-k) esetében az alkalmazás védett, mert a vezérlőn vagy a vezérlő műveletein van egy `[Authorize]` attribútuma. Ez az attribútum ellenőrzi, hogy a felhasználó hitelesítése megtörtént-e. Az alkalmazás inicializálását végző kód a `Startup.cs` fájlban található, és a Microsoft Identity platformmal (korábban az Azure ad v 2.0-val) való hitelesítéshez hozzá kell adnia a következő kódot. A kódban szereplő megjegyzéseknek magától értetődőnek kell lenniük.

  > [!NOTE]
  > Ha a projektet a Visual Studióban lévő alapértelmezett ASP.net Core webes projekttel indítja el `dotnet new mvc` , vagy `AddAzureAD` a metódust használja, alapértelmezés szerint elérhető, mert a kapcsolódó csomagok automatikusan betöltődik.
  > Ha azonban teljesen új projektet hoz létre, és az alábbi kódot próbálja használni, javasoljuk, hogy a `AddAzureAD` metódus elérhetővé tételéhez adja hozzá a **"Microsoft. AspNetCore. Authentication. AzureAD. UI"** NuGet-csomagot a projekthez.
  
A következő kód érhető el az [indításból. cs # L33-L34](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/1-WebApp-OIDC/1-1-MyOrg/Startup.cs#L33-L34)

```CSharp
public class Startup
{
 ...

  // This method gets called by the runtime. Use this method to add services to the container.
  public void ConfigureServices(IServiceCollection services)
  {
    ...
      // Sign-in users with the Microsoft identity platform
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

A `AddMicrosoftIdentityPlatformAuthentication` a [Microsoft. Identity. Web/WebAppServiceCollectionExtensions. cs # L23](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/faa94fd49c2da46b22d6694c4f5c5895795af26d/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L23)definiált kiterjesztési módszer. Ez

- hozzáadja a hitelesítési szolgáltatást
- a konfigurációs fájl olvasásához szükséges beállítások konfigurálása
- az OpenID Connect beállításainak konfigurálása, hogy a használt szolgáltató a Microsoft Identity platform (korábbi nevén Azure AD v 2.0) végpont
- a jogkivonat kiállítója érvényesítve van
- a névnek megfelelő jogcímek a "preferred_username" jogcím alapján vannak leképezve az azonosító jogkivonatban. 

A konfiguráció mellett a következő hívásakor `AddMicrosoftIdentityPlatformAuthentication`is megadhatja:

- a konfigurációs szakasz neve (alapértelmezés szerint AzureAD)
- Ha nyomon szeretné követni a OpenIdConnect köztes eseményeket, amelyek segíthetnek a webalkalmazás hibaelhárításában, ha a hitelesítés nem működik `subscribeToOpenIdConnectMiddlewareDiagnosticsEvents` : `true` a beállítással megtudhatja, hogyan dolgozza ki az adatokat a ASP.net Core készlete alapján. a köztes közbenső megoldás a HTTP-válaszból a felhasználó `HttpContext.User`identitására irányul.

```CSharp
/// <summary>
/// Add authentication with Microsoft identity platform.
/// This method expects the configuration file will have a section named "AzureAd" with the necessary settings to initialize authentication options.
/// </summary>
/// <param name="services">Service collection to which to add this authentication scheme</param>
/// <param name="configuration">The Configuration object</param>
/// <param name="subscribeToOpenIdConnectMiddlewareDiagnosticsEvents">
/// Set to true if you want to debug, or just understand the OpenIdConnect events.
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
      // Per the code below, this application signs in users in any Work and School
      // accounts and any Microsoft Personal Accounts.
      // If you want to direct Azure AD to restrict the users that can sign-in, change
      // the tenant value of the appsettings.json file in the following way:
      // - only Work and School accounts => 'organizations'
      // - only Microsoft Personal accounts => 'consumers'
      // - Work and School and Personal accounts => 'common'
      // If you want to restrict the users that can sign-in to only one tenant
      // set the tenant value in the appsettings.json file to the tenant ID
      // or domain of this organization
      options.Authority = options.Authority + "/v2.0/";

      // If you want to restrict the users that can sign-in to several organizations
      // Set the tenant value in the appsettings.json file to 'organizations', and add the
      // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection
      options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;

      // Set the nameClaimType to be preferred_username.
      // This change is needed because certain token claims from Azure AD V1 endpoint
      // (on which the original .NET core template is based) are different than Microsoft identity platform endpoint.
      // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
      // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
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

Az `AadIssuerValidator` osztály lehetővé teszi, hogy a jogkivonat kiállítója sok esetben (v 1.0 vagy v 2.0 token, egybérlős vagy több-bérlős alkalmazás vagy alkalmazás) legyen érvényesítve a felhasználók személyes Microsoft-fiókjaival, az Azure nyilvános felhőben, vagy nemzeti felhők). A [Microsoft. Identity. Web/Resource/AadIssuerValidator. cs](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs) fájlból érhető el

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET Web App/web API-k hitelesítéséhez kapcsolódó kód a [App_Start/Startup. auth. cs](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/blob/a2da310539aa613b77da1f9e1c17585311ab22b7/WebApp/App_Start/Startup.Auth.cs#L17-L61) fájlban található.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the identity platform endpoint - https://login.microsoftonline.com/common/v2.0
     // The `Scope` describes the initial permissions that your app will need.
     //  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
     ClientId = clientId,
     Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
     RedirectUri = redirectUri,
     Scope = "openid profile",
     PostLogoutRedirectUri = redirectUri,
    });
 }
```

# <a name="javatabjava"></a>[Java](#tab/java)

A Java-minta a Spring Framework-t használja. Az alkalmazás védett, mert egy olyan `Filter`alkalmazást valósít meg, amely elfogja az egyes http-válaszokat. A Java-webalkalmazás rövid útmutatójában ez a `AuthFilter` szűrő `src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java`található. A szűrő dolgozza fel a OAuth 2,0 engedélyezési kód folyamatát, így:

- ellenőrzi, hogy a felhasználó hitelesítése megtörtént-e (`isAuthenticated()` metódus)
- Ha a felhasználó nincs hitelesítve, kiszámítja az Azure AD engedélyezési végpontok URL-címét, és átirányítja a böngészőt erre az URI-ra.
- a válasz megérkezése után az Auth Code folyamatát tartalmazó msal4j a jogkivonat beszerzését teszi lehetővé.
- Amikor végül megkapja a tokent a jogkivonat-végponttól (az átirányítási URI-n), a felhasználó bejelentkezett.

A részletekért lásd `doFilter()` a [AuthFilter. Java](https://github.com/Azure-Samples/ms-identity-java-webapp/blob/master/src/main/java/com/microsoft/azure/msalwebsample/AuthFilter.java) metódusát.

> [!NOTE]
> A kódja `doFilter()` némileg eltérő sorrendben van megírva, de a folyamat az egyik leírása.

Lásd: a [Microsoft Identity platform és a OAuth 2,0 engedélyezési kód folyamatábrája](v2-oauth2-auth-code-flow.md) az engedélyezési kód folyamatával kapcsolatos részletekért, amelyet ez a módszer aktivál.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python-minta a lombikot használja. A lombik és a MSAL inicializálása. A Python elkészült az [alkalmazásban. a # L1 – L28](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/e03be352914bfbd58be0d4170eba1fb7a4951d84/app.py#L1-L28)

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

> [!div class="nextstepaction"]
> [Bejelentkezés és kijelentkezés](scenario-web-app-sign-user-sign-in.md)
