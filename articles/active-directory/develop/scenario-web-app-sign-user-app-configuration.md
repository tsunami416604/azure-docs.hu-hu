---
title: Webes alkalmazás, amely képes bejelentkeztetni a felhasználókat (kód konfiguráció) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy webalkalmazást, amely képes bejelentkeztetni a felhasználókat (kód konfigurálása)
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
ms.openlocfilehash: 82e6cbcd01c87ddffb7eac8d0ea0faef85f41a13
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254004"
---
# <a name="web-app-that-signs-in-users---code-configuration"></a>Webes alkalmazás, hogy jelentkezik be felhasználókat - kód-konfiguráció

Ismerje meg, hogy a webalkalmazás kódját, hogy bejelentkezik felhasználók konfigurálása.

## <a name="libraries-used-to-protect-web-apps"></a>Webes alkalmazások védelmére használt kódtárak

<!-- This section can be in an include for Web App and Web APIs -->
A Web App (és a egy webes API-t) védelmére használt kódtárak a következők:

| Platform | Erőforrástár | Leírás |
|----------|---------|-------------|
| ![.NET](media/sample-v2-code/logo_net.png) | [Identitás modellbővítményeket a .NET-hez](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) | Közvetlenül az ASP.NET és az ASP.NET Core, .NET-hez készült Microsoft identitás-bővítmények javaslatot tesz fut, mind a .NET-keretrendszer és a .NET Core dll-fájlok készlete. Egy ASP.NET/ASP.NET Core-webalkalmazást, a szabályozhatja jogkivonat érvényesítésére használatával a **TokenValidationParameters** osztály (különösen egyes Szoftverszállítói forgatókönyvekben) |

## <a name="aspnet-core-configuration"></a>ASP.NET Core-konfiguráció

### <a name="application-configuration-files"></a>Alkalmazás-konfigurációs fájlok

Az ASP.NET Core, a webes alkalmazás bejelentkezés rendelkező felhasználók a Microsoft identity platform keresztül lehet konfigurálni a `appsettings.json` fájlt. Meg kell adni a beállítások a következők:

- a felhő `Instance` Ha azt szeretné, hogy az alkalmazás futtatásához a nemzeti felhőkben
- a célközönség `tenantId`
- a `clientId` az alkalmazáshoz, mint az Azure Portalról másolt.

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
    // - the tenant Id as a a GUID obtained from the azure portal to sign-in users in your organization
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

Az ASP.NET Core, van egy másik fájlba, amely tartalmazza az URL-cím (`applicationUrl`) és az SSL-portot (`sslPort`) az alkalmazását, valamint a különböző profilok.

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

Az Azure Portalon, a válasz URI-k, amelyek a regisztrálnia kell a **hitelesítési** oldalon az alkalmazás meg kell felelnie az alábbi URL-címek; azaz a fenti két konfigurációs fájlok lennének `https://localhost:44321/signin-oidc` , a applicationUrl van `http://localhost:3110` , de a `sslPort` van megadott (44321), és a `CallbackPath` van `/signin-oidc` meghatározottak szerint a `appsettings.json`.
  
Ugyanúgy, kijelentkezési URI-t kellene állítani `https://localhost:44321/signout-callback-oidc`.

### <a name="initialization-code"></a>Inicializálási kódot

Az ASP.NET Core Web Apps (és a webes API-k) az alkalmazás inicializálása során a kód található a `Startup.cs` fájlt, és adja hozzá a következő kódot a kell a Microsoft Identity platform (korábbi nevén az Azure AD) 2.0-s hitelesítés hozzáadásához. A Megjegyzések a kódban magától értetődő.

  > [!NOTE]
  > Ha először a projekt alapértelmezett ASP.NET core webes projekt a Visual studio vagy a használatával a `dotnet new mvc` módszer `AddAzureAD` állnak rendelkezésre, alapértelmezés szerint, mert a kapcsolódó csomagok automatikusan töltődnek be. Azonban ha egy teljesen új projekt buildjének elkészítéséhez, és használni kívánó az alábbi kód javasoljuk, hogy a NuGet-csomag hozzáadása **"Microsoft.AspNetCore.Authentication.AzureAD.UI"** a projekthez, hogy a `AddAzureAD` módszer érhető el.
  
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

Az ASP.NET, az alkalmazás keresztül lehet konfigurálni a `Web.Config` fájl

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

A kód az ASP.NET-webalkalmazásban hitelesítéshez kapcsolódó / webes API-k található a `App_Start/Startup.Auth.cs` fájlt.

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
