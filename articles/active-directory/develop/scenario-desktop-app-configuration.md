---
title: Webes API-kat meghívó asztali alkalmazások konfigurálása – Microsoft Identity platform | Azure
description: Útmutató a webes API-kat meghívó asztali alkalmazások kódjának konfigurálásához
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
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33b1724c25ef2d85aa8f838811864104b49576a3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423898"
---
# <a name="desktop-app-that-calls-web-apis---code-configuration"></a>Webes API-kat meghívó asztali alkalmazás – kód konfigurálása

Most, hogy létrehozta az alkalmazást, megtudhatja, hogyan konfigurálhatja a kódot az alkalmazás koordinátáival.

## <a name="msal-libraries"></a>MSAL-kódtárak

Az asztali alkalmazásokat támogató Microsoft-kódtárak a következők:

  MSAL-könyvtár | Leírás
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Támogatja az asztali alkalmazások létrehozását több platformon – Linux, Windows és MacOS
  ![Python](media/sample-v2-code/logo_python.png) <br/> MSAL Python | A támogatja az asztali alkalmazások több platformon történő összeállítását.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | A támogatja az asztali alkalmazások több platformon történő összeállítását.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Csak macOS rendszeren futó asztali alkalmazások támogatása

## <a name="public-client-application"></a>Nyilvános ügyfélalkalmazás

A kód szempontjából az asztali alkalmazások nyilvános ügyfélalkalmazások. A konfiguráció egy kicsit különbözik attól függően, hogy interaktív hitelesítést használ-e, vagy sem.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

Létre kell hoznia és módosítania kell a MSAL.NET `IPublicClientApplication`.

![IPublicClientApplication](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Kizárólag kód alapján

A következő kód egy nyilvános ügyfélalkalmazás, a Microsoft Azure nyilvános felhőben, munkahelyi vagy iskolai fiókkal, illetve személyes Microsoft-fiók való beléptetését hozza létre a felhasználók számára.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Ha az interaktív hitelesítést vagy az eszköz kódját szeretné használni, a fentiekben leírtak szerint a `.WithRedirectUri` módosítót kell használnia:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="using-configuration-files"></a>Konfigurációs fájlok használata

A következő kód egy nyilvános ügyfélalkalmazás példányát hozza létre egy konfigurációs objektumból, amely programozott módon kitölthető vagy egy konfigurációs fájlból is beolvasható

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Részletesebb konfiguráció

Az alkalmazás kiépítése több módosítóval is kiegészíthető. Ha például azt szeretné, hogy az alkalmazása több-bérlős alkalmazás legyen egy nemzeti felhőben (itt az USA kormánya), akkor a következőket írhatja:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

A MSAL.NET az ADFS 2019 módosítóját is tartalmazza:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Végül, ha jogkivonatokat szeretne beszerezni egy Azure AD B2C bérlő számára, megadhatja a bérlőt az alábbi kódrészletben látható módon:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>További információk

További információ a MSAL.NET asztali alkalmazások konfigurálásáról:

- A `PublicClientApplicationBuilder`on elérhető összes módosító listáját a dokumentáció [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods) ismertető cikk tartalmazza.
- A `PublicClientApplicationOptions`ban elérhető összes beállítás leírását lásd: [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), a hivatkozási dokumentációban

### <a name="complete-example-with-configuration-options"></a>Példa teljes konfigurációs beállításokkal

Képzeljen el egy olyan .NET Core Console-alkalmazást, amely a következő `appsettings.json` konfigurációs fájllal rendelkezik:

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
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Az alkalmazás létrehozásához most csak a következő kódot kell megírnia:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

és a `.Build()` metódus meghívása előtt felülbírálhatja a konfigurációt a korábban látott `.WithXXX` metódusok hívásával.

# <a name="javatabjava"></a>[Java](#tab/java)

A MSAL Java dev-mintákban használt osztály a következő mintákat konfigurálja: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication app = PublicClientApplication.builder(TestData.PUBLIC_CLIENT_ID)
        .authority(TestData.AUTHORITY_COMMON)
        .build();
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macostabmacos"></a>[MacOS](#tab/macOS)

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
---

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Jogkivonat beszerzése asztali alkalmazáshoz](scenario-desktop-acquire-token.md)
