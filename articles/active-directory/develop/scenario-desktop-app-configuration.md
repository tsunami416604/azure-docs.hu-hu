---
title: Asztali alkalmazás, hogy a hívások webes API-k (kód konfiguráció) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy asztali alkalmazás, hogy a hívások webes API-k (alkalmazás kód konfigurálása)
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
ms.date: 05/o7/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: eadcae393128d9721f2c988f713af07913c5fd1d
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545503"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Asztali alkalmazás, hogy a hívások webes API-k – helykódot

Most, hogy létrehozta az alkalmazást, a kód konfigurálásához az alkalmazás koordinátákkal megadott fog mutatja.

## <a name="msal-libraries"></a>Az MSAL kódtárak

A csak az MSAL műsortár, asztali alkalmazásokat támogató még ma az MSAL.NET

## <a name="public-client-application"></a>Nyilvános ügyfélalkalmazás

A kód szempontjából asztali alkalmazások nyilvános ügyfélalkalmazások számára, és ezért fog hozhat létre és kezelheti az MSAL.NET `IPublicClientApplication`. Újra dolgot egy kicsit eltérőek lesznek-e protokollt használja az interaktív hitelesítéshez.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Kizárólag a code szerint

Az alábbi kód példányosít egy nyilvános ügyfélalkalmazás aláírási a felhasználók a Microsoft Azure nyilvános felhő, a munkahelyi és iskolai fiókokhoz vagy személyes Microsoft-fiókjukkal.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Ha szeretne használni az interaktív hitelesítéshez, ahogy fent látható, szeretné-e használni a `.WithRedirectUri` módosító:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithRedirectUri(PublicClientApplicationBuilder.DefaultInteractiveDesktopRedirectUri)
         .Build();
```

### <a name="using-configuration-files"></a>Konfigurációs fájlok használatával

Az alábbi kód példányosít egy nyilvános ügyfélalkalmazást egy konfigurációs objektumot, amelyek ki vannak töltve a programozás útján vagy a konfigurációs fájl olvasásakor:

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="more-elaborated-configuration"></a>További konfigurációs kidolgozott

Az alkalmazás készítése dostupnosti számos hozzáadásával is dolgoznak ki. Például ha azt szeretné, hogy az alkalmazás egy több-bérlős alkalmazás országos felhőben (Itt USA kormányzati szerveinek biztosított), is írható:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET is tartalmaz a módosító az AD FS 2019:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Ha szeretné az Azure AD B2C-bérlő szerzi be a jogkivonatokat, végül adhatja meg a bérlő az alábbi kódrészletben látható módon:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Tudnivalók a modellalapú alkalmazások létrehozásáról

További az MSAL.NET asztali alkalmazás konfigurálása:

- Az összes elérhető dostupnosti listáját `PublicClientApplicationBuilder`, tekintse meg a dokumentációja [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Az elérhető lehetőségekről leírását `PublicClientApplicationOptions` lásd [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), a referencia-dokumentáció

## <a name="complete-example-with-configuration-options"></a>A konfigurációs beállítások teljes példát

Egy .NET Core-konzolalkalmazást, amely rendelkezik a következő Imagine `appsettings.json` konfigurációs fájlban:

```JSon
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

A használatával a .NET-keretrendszer konfigurációs; a megadott fájl olvasásához készítéséhez kevés programozási munkával rendelkezik

```CSharp
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
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Most az alkalmazás létrehozásához, csak szüksége írása a következő kódot:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

és hívása előtt a `.Build()` metódus, felülbírálhatja a konfiguráció meghívásával `.WithXXX` módszerek, mint korábban.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Asztali alkalmazások egy token beszerzése](scenario-desktop-acquire-token.md)
