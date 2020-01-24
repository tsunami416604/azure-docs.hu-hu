---
title: Nyilvános ügyfélalkalmazás létrehozása (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre egy nyilvános ügyfélalkalmazás konfigurációs beállításokkal a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c07ffb77a838a91bd7eed4b9e40b39050450981c
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76695500"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Nyilvános ügyfélalkalmazás létrehozása konfigurációs beállításokkal a MSAL.NET használatával

Ez a cikk azt ismerteti, hogyan hozható létre [nyilvános ügyfélalkalmazás](msal-client-applications.md) a .net-hez készült Microsoft Authentication Library (MSAL.net) használatával.  Az alkalmazás egy beállítási fájlban megadott konfigurációs beállításokkal lett létrehozva.

Az alkalmazás inicializálásához először [regisztrálnia](quickstart-register-app.md) kell, hogy az alkalmazás integrálható legyen a Microsoft Identity platformmal. A regisztráció után a következő információkra lehet szüksége (amelyek a Azure Portalban találhatók):

- Az ügyfél-azonosító (GUID jelölő sztring)
- Az identitás-szolgáltató URL-címe (a példány neve) és az alkalmazás bejelentkezési célközönsége. Ez a két paraméter együttesen a hatóság néven ismert.
- A bérlő azonosítója, ha csak az Ön szervezete számára ír üzletági alkalmazást (más néven egybérlős alkalmazás).
- Webalkalmazások esetében, és esetenként a nyilvános ügyfélalkalmazások számára (különösen, ha az alkalmazásnak közvetítőt kell használnia), azt a redirectUri is be kell állítania, amelyben az identitás-szolgáltató felveszi a kapcsolatot az alkalmazással a biztonsági jogkivonatokkal.


Egy .NET Core Console-alkalmazáshoz a következő *appSettings. JSON* konfigurációs fájl tartozhat:

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

A következő kód a .NET konfigurációs keretrendszer használatával olvassa be ezt a fájlt:

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

A következő kód létrehozza az alkalmazást a beállítások fájljának konfigurációjának használatával:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

