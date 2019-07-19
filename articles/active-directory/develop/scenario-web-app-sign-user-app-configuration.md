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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c962e95b3d213c4089b51f58139cab17a3332cbd
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853070"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Felhasználói bejelentkezést használó webalkalmazás – kód konfigurálása

Megtudhatja, hogyan konfigurálhatja a webalkalmazáshoz tartozó kódot, amely bejelentkezik a felhasználók számára.

## <a name="libraries-used-to-protect-web-apps"></a>A Web Apps védeleméhez használt könyvtárak

<!-- This section can be in an include for Web App and Web APIs -->
A webalkalmazások (és a webes API-k) elleni védelemhez használt kódtárak a következők:

| Platform | Erőforrástár | Leírás |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [A .NET-hez készült Identity Model-bővítmények](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | A ASP.NET és a ASP.NET Core által közvetlenül használt Microsoft Identity Extensions for .NET azt javasolja, hogy a .NET-keretrendszer és a .NET Core rendszerű DLL-eket is használják. Egy ASP.NET/ASP.NET Core-webalkalmazásból a jogkivonat-érvényesítés a **TokenValidationParameters** osztály használatával szabályozható (különösen bizonyos ISV-forgatókönyvekben). |

## <a name="aspnet-core-configuration"></a>ASP.NET Core konfiguráció

A cikkben szereplő kódrészletek és a következők a [ASP.net Core Web App növekményes oktatóanyagból, 1](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-1-MyOrg). fejezetből származnak. Ebben az oktatóanyagban a teljes körű megvalósítás részleteit érdemes megtekinteni.

### <a name="application-configuration-files"></a>Alkalmazás konfigurációs fájljai

ASP.net Core a Microsoft Identity platformmal rendelkező webalkalmazás-bejelentkezést használó felhasználók a `appsettings.json` fájlon keresztül konfigurálhatók. A kitöltendő beállítások a következők:

- a felhőben `Instance` , ha azt szeretné, hogy az alkalmazás a nemzeti felhőkben fusson
- a célközönség a`tenantId`
- az `clientId` alkalmazáshoz a Azure Portalból másolt.

```JSon
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

ASP.net Coreban van egy másik fájl, amely tartalmazza az alkalmazás URL`applicationUrl`-címét () és az`sslPort`SSL-portot (), valamint a különböző profilokat.

```JSon
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

### <a name="initialization-code"></a>Inicializálási kód

ASP.net Core Web Apps (és webes API-k) esetében az alkalmazás inicializálását végző kód a `Startup.cs` fájlban található, és a Microsoft Identity platform (korábban Azure ad) 2.0-s verziójának használatával történő hitelesítéshez hozzá kell adnia a következő kódot. A kódban szereplő megjegyzéseknek magától értetődőnek kell lenniük.

  > [!NOTE]
  > Ha a projektet a Visual Studióban lévő alapértelmezett ASP.net Core webes projekttel indítja el `dotnet new mvc` , vagy `AddAzureAD` a metódust használja, alapértelmezés szerint elérhető, mert a kapcsolódó csomagok automatikusan betöltődik. Ha azonban teljesen új projektet hoz létre, és az alábbi kódot próbálja használni, javasoljuk, hogy a `AddAzureAD` metódus elérhetővé tételéhez adja hozzá a **"Microsoft. AspNetCore. Authentication. AzureAD. UI"** NuGet-csomagot a projekthez.
  
```CSharp
 services.AddAuthentication(AzureADDefaults.AuthenticationScheme)
         .AddAzureAD(options => configuration.Bind("AzureAd", options));

 services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
 {
  // The ASP.NET core templates are currently using Azure AD v1.0, and compute
  // the authority (as {Instance}/{TenantID}). We want to use the Microsoft Identity Platform v2.0 endpoint
  options.Authority = options.Authority + "/v2.0/";

  // If you want to restrict the users that can sign-in to specific organizations
  // Set the tenant value in the appsettings.json file to 'organizations', and add the
  // issuers you want to accept to options.TokenValidationParameters.ValidIssuers collection.
  // Otherwise validate the issuer
  options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ForAadInstance(options.Authority).ValidateAadIssuer;

  // Set the nameClaimType to be preferred_username.
  // This change is needed because certain token claims from Azure AD v1.0 endpoint
  // (on which the original .NET core template is based) are different in Azure AD v2.0 endpoint.
  // For more details see [ID Tokens](https://docs.microsoft.com/azure/active-directory/develop/id-tokens)
  // and [Access Tokens](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)
  options.TokenValidationParameters.NameClaimType = "preferred_username";
  ...
```

## <a name="aspnet-configuration"></a>ASP.NET-konfiguráció

A ASP.net-ben az alkalmazás a `Web.Config` fájlon keresztül van konfigurálva.

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

A ASP.net Web App/web API-k hitelesítéséhez kapcsolódó kód a `App_Start/Startup.Auth.cs` fájlban található.

```CSharp
 public void ConfigureAuth(IAppBuilder app)
 {
  app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

  app.UseCookieAuthentication(new CookieAuthenticationOptions());

  app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
     // The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bejelentkezés és kijelentkezés](scenario-web-app-sign-user-sign-in.md)
