---
title: Bizalmas ügyfélalkalmazás (MSAL.NET) példányos példányosítása | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogy miként hozhat hozzá egy bizalmas ügyfélalkalmazást konfigurációs beállításokkal a Microsoft Authentication Library for .NET (MSAL.NET) segítségével.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1a520c5a1002e401f880fba84f8fc02a0a678133
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084730"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Bizalmas ügyfélalkalmazás példányosítása a konfigurációs beállításokkal MSAL.NET

Ez a cikk azt ismerteti, hogy miként lehet [egy bizalmas ügyfélalkalmazást](msal-client-applications.md) a Microsoft Authentication Library for .NET (MSAL.NET) használatával példányosítani.  Az alkalmazás példányosított konfigurációs beállítások at egy beállításfájlt.

Az alkalmazás inicializálása előtt először [regisztrálnia](quickstart-register-app.md) kell azt, hogy az alkalmazás integrálható legyen a Microsoft identitásplatformral. A regisztráció után a következő információkra lehet szüksége (amelyek az Azure Portalon találhatók):

- Az ügyfélazonosító (guid azonosítót képviselő karakterlánc)
- Az identitásszolgáltató URL-címe (a példány neve) és az alkalmazás bejelentkezési közönsége. Ezt a két paramétert együttesen hatóságnak nevezzük.
- A bérlőazonosító, ha egy üzleti alkalmazást kizárólag a szervezet (más néven egy-bérlős alkalmazás) ír.
- Az alkalmazás titkos (ügyféltitkos karakterlánc) vagy tanúsítvány (X509Certificate2 típus), ha bizalmas ügyfélalkalmazásról van szó.
- A webalkalmazások, és néha a nyilvános ügyfélalkalmazások (különösen, ha az alkalmazás kell használni a bróker), akkor is be kell állítania a redirectUri, ahol az identitásszolgáltató kapcsolatba lép az alkalmazás a biztonsági jogkivonatok.

## <a name="configure-the-application-from-the-config-file"></a>Az alkalmazás konfigurálása a konfigurációs fájlból
A beállítások tulajdonságainak neve MSAL.NET megegyezik a `AzureADOptions` ASP.NET Core tulajdonságainak nevével, így nem kell kapcsolási kódot írnia.

A ASP.NET Core alkalmazáskonfigurációt egy *appsettings.json* fájl ismerteti:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

A MSAL.NET v3.x-es fájltól kezdve a bizalmas ügyfélalkalmazást a konfigurációs fájlból konfigurálhatja.

Abban az osztályban, ahol konfigurálni és példányosítani `ConfidentialClientApplicationOptions` szeretné az alkalmazást, deklarálnia kell egy objektumot.  A forrásból (beleértve az appconfig.json fájlt is) beolvasott konfigurációt `IConfigurationRoot.Bind()` az alkalmazásbeállítások példányához köti a [Microsoft.Extensions.Configuration.Binder nuget csomag](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder)metódusával:

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Ez lehetővé teszi, hogy az *appsettings.json* fájl "AzureAD" szakaszának `ConfidentialClientApplicationOptions` tartalma az objektum megfelelő tulajdonságaihoz legyen kötve.  Ezután hozzon létre egy `ConfidentialClientApplication` objektumot:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Futásidejű konfiguráció hozzáadása
Egy bizalmas ügyfélalkalmazásban általában felhasználónként van gyorsítótár. Ezért be kell szereznie a felhasználóhoz társított gyorsítótárat, és tájékoztatnia kell az alkalmazásszerkesztőt a rról, hogy használni szeretné azt. Ugyanígy előfordulhat, hogy egy dinamikusan kiszámított átirányítási URI-val rendelkezik. Ebben az esetben a kód a következő:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

