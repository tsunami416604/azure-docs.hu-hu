---
title: Webes API-kat meghívó asztali alkalmazás (kód konfigurációja) – Microsoft Identity platform
description: Ismerje meg, hogyan hozhat létre webes API-kat meghívó asztali alkalmazást (az alkalmazás kódjának konfigurációja)
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
ms.openlocfilehash: 0926e6800dbcd81d2e542e27afe3afb1240cff22
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268417"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Webes API-kat meghívó asztali alkalmazás – kód konfigurálása

Most, hogy létrehozta az alkalmazást, megtudhatja, hogyan konfigurálhatja a kódot az alkalmazás koordinátáival.

## <a name="msal-libraries"></a>MSAL-kódtárak

Az egyetlen MSAL-függvénytár, amely több platformon támogatja az asztali alkalmazásokat, jelenleg a MSAL.NET.

Az iOS-és macOS-MSAL csak macOS rendszeren futó asztali alkalmazásokat támogat. 

## <a name="public-client-application-with-msalnet"></a>Nyilvános ügyfélalkalmazás MSAL.NET

A kód szempontjából az asztali alkalmazások nyilvános ügyfélalkalmazások, ezért a MSAL.NET `IPublicClientApplication`létrehozása és kezelése. A dolgok újbóli megegyeznek, függetlenül attól, hogy interaktív hitelesítést használ-e, vagy sem.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Kizárólag kód alapján

A következő kód egy nyilvános ügyfélalkalmazás, a Microsoft Azure nyilvános felhőben, munkahelyi vagy iskolai fiókkal, illetve személyes Microsoft-fiók való beléptetését hozza létre a felhasználók számára.

```CSharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Ha az interaktív hitelesítést vagy az eszköz kódját szeretné használni, a fentieknek megfelelően a `.WithRedirectUri` módosítót kell használnia:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>Konfigurációs fájlok használata

A következő kód egy nyilvános ügyfélalkalmazás példányát hozza létre egy konfigurációs objektumból, amely programozott módon kitölthető vagy egy konfigurációs fájlból is beolvasható

```CSharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Részletesebb konfiguráció

Az alkalmazás kiépítése több módosítóval is kiegészíthető. Ha például azt szeretné, hogy az alkalmazása több-bérlős alkalmazás legyen egy nemzeti felhőben (itt az USA kormánya), akkor a következőket írhatja:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

A MSAL.NET az ADFS 2019 módosítóját is tartalmazza:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Végül, ha jogkivonatokat szeretne beszerezni egy Azure AD B2C bérlő számára, megadhatja a bérlőt az alábbi kódrészletben látható módon:

```CSharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Tudnivalók a modellalapú alkalmazások létrehozásáról

További információ a MSAL.NET asztali alkalmazások konfigurálásáról:

- Az összes elérhető módosító listájának megtekintéséhez tekintse `PublicClientApplicationBuilder`meg a Reference dokumentáció [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- A `PublicClientApplicationOptions` [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions)-ben elérhető összes beállítás leírását a dokumentációban találja

## <a name="complete-example-with-configuration-options"></a>Példa teljes konfigurációs beállításokkal

Képzeljünk el egy .net Core Console-alkalmazást, amely `appsettings.json` a következő konfigurációs fájllal rendelkezik:

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

A fájlnak a .NET által megadott konfigurációs keretrendszer használatával történő olvasásához kevés a kód.

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

Az alkalmazás létrehozásához most csak a következő kódot kell megírnia:

```CSharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

a `.Build()` metódus hívása előtt pedig felülbírálhatja a konfigurációt a korábban látott `.WithXXX` metódusok hívásával.

## <a name="public-client-application-with-msal-for-ios-and-macos"></a>Nyilvános ügyfélalkalmazás iOS és macOS rendszerű MSAL

A következő kód egy nyilvános ügyfélalkalmazás, a Microsoft Azure nyilvános felhőben, munkahelyi vagy iskolai fiókkal, illetve személyes Microsoft-fiók való beléptetését hozza létre a felhasználók számára.

### <a name="quick-configuration"></a>Gyors konfigurálás

Objective-C:

```objc
NSError *msalError = nil;
    
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Részletesebb konfiguráció

Az alkalmazás kiépítése több módosítóval is kiegészíthető. Ha például azt szeretné, hogy az alkalmazása több-bérlős alkalmazás legyen egy nemzeti felhőben (itt az USA kormánya), akkor a következőket írhatja:

Objective-C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];
                                                          
MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];
                                                                  
NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Jogkivonat beszerzése asztali alkalmazáshoz](scenario-desktop-acquire-token.md)
