---
title: Hozza létre egy nyilvános ügyfélalkalmazás lehetőségek (Microsoft-hitelesítési tár .NET) |} Az Azure
description: Ismerje meg, hogyan hozható létre egy nyilvános ügyfélalkalmazás konfigurációs lehetőségek a Microsoft-hitelesítési tár .NET (MSAL.NET) használatával.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
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
ms.openlocfilehash: 3a483cd6e22ce3c9fafb9b2b4d839e22c248f020
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158700"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Hozza létre egy nyilvános ügyfélalkalmazás az MSAL.NET használatával, a konfigurációs beállítások

Ez a cikk bemutatja, hogyan hozhat létre egy [ügyfélalkalmazás nyilvános](msal-client-applications.md) Microsoft-hitelesítési tár .NET (MSAL.NET) használatával.  Az alkalmazás beállításainak fájlban meghatározott konfigurációs beállításokkal jön létre.

Alkalmazás inicializálása, előtt először létre kell [regisztrálása](quickstart-register-app.md) azt, hogy az alkalmazás integrálható a Microsoft identity platform. A regisztrációt követően szükség lehet a következő információkat (amely az Azure Portalon található):

- Az ügyfél-Azonosítót (GUID képviselő karakterláncot)
- Az identitásszolgáltató szolgáltató URL-címe (a példány neve) és az alkalmazás bejelentkezési célközönség. E két paraméter együttesen hatóságként ismert.
- A bérlő Azonosítóját, ha egy üzletági alkalmazás kizárólag a szervezetben (egybérlős alkalmazás elnevezett is ismert).
- A web apps, és egyes esetekben a nyilvános ügyfélalkalmazások (különösen ha az alkalmazás egy közvetítő) fog is beállította a redirectUri, lépjen kapcsolatba az identitásszolgáltató vissza az alkalmazás a biztonsági jogkivonatokkal.


Egy .NET Core-konzolalkalmazást az alábbiakra van szükség *appsettings.json* konfigurációs fájlban:

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

A következő kódot a használatával a .NET-keretrendszer konfigurációs fájl beolvasása:

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

A következő kódot az alkalmazás, a konfiguráció használatával a beállítások fájlból hoz létre:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

