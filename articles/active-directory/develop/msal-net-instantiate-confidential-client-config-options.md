---
title: Hozza létre a bizalmas ügyfélalkalmazás lehetőségek (Microsoft-hitelesítési tár .NET) |} Az Azure
description: Ismerje meg, hogyan hozható létre egy bizalmas ügyfélalkalmazás konfigurációs lehetőségek a Microsoft-hitelesítési tár .NET (MSAL.NET) használatával.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f935b1b2815501710444e3f921a157ba02e3215
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544086"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Hozza létre a bizalmas ügyfélalkalmazás az MSAL.NET használatával, a konfigurációs beállítások

Ez a cikk bemutatja, hogyan hozhat létre egy [ügyfélalkalmazás bizalmas](msal-client-applications.md) Microsoft-hitelesítési tár .NET (MSAL.NET) használatával.  Az alkalmazás beállításainak fájlban meghatározott konfigurációs beállításokkal jön létre.

Alkalmazás inicializálása, előtt először létre kell [regisztrálása](quickstart-register-app.md) azt, hogy az alkalmazás integrálható a Microsoft identity platform. A regisztrációt követően szükség lehet a következő információkat (amely az Azure Portalon található):

- Az ügyfél-Azonosítót (GUID képviselő karakterláncot)
- Az identitásszolgáltató szolgáltató URL-címe (a példány neve) és az alkalmazás bejelentkezési célközönség. E két paraméter együttesen hatóságként ismert.
- A bérlő Azonosítóját, ha egy üzletági alkalmazás kizárólag a szervezetben (egybérlős alkalmazás elnevezett is ismert).
- Az alkalmazás titkos kulcs (ügyfél titkos karakterlánc) vagy a tanúsítvány (típusú X509Certificate2) egy bizalmas ügyfél alkalmazás esetén.
- A web apps, és egyes esetekben a nyilvános ügyfélalkalmazások (különösen ha az alkalmazás egy közvetítő) fog is beállította a redirectUri, lépjen kapcsolatba az identitásszolgáltató vissza az alkalmazás a biztonsági jogkivonatokkal.

## <a name="configure-the-application-from-the-config-file"></a>A konfigurációs fájlból az alkalmazás konfigurálása
A lehetőségek a MSAL.NET tulajdonságainak neve egyezik tulajdonságainak neve a `AzureADOptions` az ASP.NET Core, így nem kell minden olyan integrációs kódot írnia.

Az ASP.NET Core-konfigurációjához leírt egy *appsettings.json* fájlt:

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

A MSAL.NET v3.x-től kezdődően a konfigurációs fájlból bizalmas ügyfélalkalmazás konfigurálása. Az alkalmazáskonfigurációt kapcsolódó osztályok találhatók az `Microsoft.Identity.Client.AppConfig` névtér.

Deklarálni kell az osztályban, ahol azt szeretné, konfigurálja és hozza létre az alkalmazás egy `ConfidentialClientApplicationOptions` objektum.  A konfiguráció olvasása a forrásból (beleértve a appconfig.json fájl) kötési példányhoz, az alkalmazás beállításai:

```csharp
using Microsoft.Identity.Client.AppConfig;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Ez lehetővé teszi, hogy az "Azure ad" szakaszában tartalmát a *appsettings.json* fájl megfelelő tulajdonságainak kell kötni a `ConfidentialClientApplicationOptions` objektum.  Ezután hozhat létre egy `ConfidentialClientApplication` objektum:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Futásidejű konfigurációjának hozzáadása
A bizalmas ügyfélalkalmazás felhasználónként gyorsítótár általában rendelkezik. Ezért kell a gyorsítótárban, a felhasználóhoz társított, és tájékoztatja az alkalmazást a jelentéskészítő, hogy szeretné-e használni. Ugyanúgy lehetséges, hogy dinamikusan számított átirányítási URI-t. Ebben az esetben a kódja: a következő:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

