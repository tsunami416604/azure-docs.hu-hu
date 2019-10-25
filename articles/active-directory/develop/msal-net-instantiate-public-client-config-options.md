---
title: Nyilvános ügyfélalkalmazás létrehozása lehetőségekkel (Microsoft Authentication Library for .NET)
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan hozhat létre egy nyilvános ügyfélalkalmazás konfigurációs beállításokkal a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdc6d65d811c09ffae94fe6394f8ae7e61cf0a6f
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802866"
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

