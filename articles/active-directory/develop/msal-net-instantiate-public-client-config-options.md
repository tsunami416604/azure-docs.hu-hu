---
title: Nyilvános ügyfélalkalmazás (MSAL.NET) példányos példányosítása | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogy miként hozhat hozzá egy nyilvános ügyfélalkalmazást konfigurációs beállításokkal a Microsoft Authentication Library for .NET (MSAL.NET) segítségével.
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
ms.openlocfilehash: 1dd06e139f931bbf8554f05f05c5d9b9ccf200e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083597"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Nyilvános ügyfélalkalmazás példányosítása a konfigurációs beállításokkal MSAL.NET

Ez a cikk azt ismerteti, hogy miként lehet példányosítani egy [nyilvános ügyfélalkalmazást](msal-client-applications.md) a Microsoft Authentication Library for .NET (MSAL.NET) használatával.  Az alkalmazás példányosított konfigurációs beállítások at egy beállításfájlt.

Az alkalmazás inicializálása előtt először [regisztrálnia](quickstart-register-app.md) kell azt, hogy az alkalmazás integrálható legyen a Microsoft identitásplatformral. A regisztráció után a következő információkra lehet szüksége (amelyek az Azure Portalon találhatók):

- Az ügyfélazonosító (guid azonosítót képviselő karakterlánc)
- Az identitásszolgáltató URL-címe (a példány neve) és az alkalmazás bejelentkezési közönsége. Ezt a két paramétert együttesen hatóságnak nevezzük.
- A bérlőazonosító, ha egy üzleti alkalmazást kizárólag a szervezet (más néven egy-bérlős alkalmazás) ír.
- A webalkalmazások, és néha a nyilvános ügyfélalkalmazások (különösen, ha az alkalmazás kell használni a bróker), akkor is be kell állítania a redirectUri, ahol az identitásszolgáltató kapcsolatba lép az alkalmazás a biztonsági jogkivonatok.


A .NET Core konzolalkalmazás a következő *appsettings.json konfigurációs* fájllal rendelkezhet:

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

A következő kód a .

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
    /// </summary>
    /// <param name="path">Path to the configuration json file</param>
    /// <returns>SampleConfiguration as read from the json file</returns>
    public static SampleConfiguration ReadFromJsonFile(string path)
    {
        // .NET configuration
        IConfigurationRoot Configuration;
        var builder = new ConfigurationBuilder()
          .SetBasePath(Directory.GetCurrentDirectory())
        .AddJsonFile(path);
        Configuration = builder.Build();

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

A következő kód hozza létre az alkalmazást a beállításfájl konfigurációjával:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

