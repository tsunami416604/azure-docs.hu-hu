---
title: Csatlakozás az Azure Media Services v3 API – .NET
description: Ismerje meg, hogyan csatlakozhat a Media Services v3 API .NET-tel.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2019
ms.author: juliako
ms.openlocfilehash: 8f8a1434af768180e34afcaacd6e92ab402ad8cd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59361233"
---
# <a name="connect-to-media-services-v3-api---net"></a>Csatlakozás a Media Services v3 API – .NET

Ez a cikk bemutatja, hogyan csatlakozhat az Azure Media Services v3 .NET SDK használatával a kiszolgálószintű bejelentkezési módszerrel.

## <a name="prerequisites"></a>Előfeltételek

- [A Media Services-fiók létrehozása](create-account-cli-how-to.md). Ügyeljen arra, hogy ne felejtse el az erőforráscsoport nevét és a Media Services-fiók neve
- Olyan eszköz, amely a .NET-fejlesztés szeretné telepíteni. A jelen cikkben ismertetett lépések bemutatják használata [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Akkor használja a Visual Studio Code, lásd: [használata C# ](https://code.visualstudio.com/docs/languages/csharp). Vagy egy másik Kódszerkesztő is használhatja.

## <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

1. Indítsa el a Visual Studiót. 
1. Az a **fájl** menüben kattintson a **új** > **projekt**. 
1. Hozzon létre egy **.NET Core** Konzolalkalmazás.

Ez a témakör a mintaalkalmazás célozza `netcoreapp2.0`. A kód használja "aszinkron fő", azaz kezdődően elérhető C# 7.1-es. Ez [blog](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) további részletekért.

## <a name="add-required-nuget-packages"></a>Adja hozzá a szükséges NuGet-csomagok

1. A Visual Studióban válassza ki a **eszközök** > **NuGet-Csomagkezelő** > **NuGet-kezelő konzol**.
2. Az a **Package Manager Console** ablakban `Install-Package` parancs használatával adja hozzá a következő NuGet-csomagokat. Például: `Install-Package Microsoft.Azure.Management.Media`.

|Csomag|Leírás|
|---|---|
|`Microsoft.Azure.Management.Media`|Azure Media Services SDK. <br/>Győződjön meg arról, hogy a legfrissebb Azure Media Services-csomagot használ, ellenőrizze a következőket [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media).|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|NET-hez készült Azure SDK ADAL-hitelesítési tár|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Olvassa el a konfigurációs értékeket a környezeti változókat és a helyi JSON-fájlok|
|`Microsoft.Extensions.Configuration.Json`|Olvassa el a konfigurációs értékeket a környezeti változókat és a helyi JSON-fájlok
|`WindowsAzure.Storage`|Storage SDK|

## <a name="create-and-configure-the-app-settings-file"></a>Az alkalmazás beállításait tartalmazó fájl létrehozása és konfigurálása

### <a name="create-appsettingsjson"></a>Create appsettings.json

1. Nyissa meg a go **általános** > **szövegfájl**.
1. Nevezze el "appsettings.json".
1. A "Másolás a kimeneti könyvtár" tulajdonsága "Másolás, ha újabb".JSON kiterjesztésű fájl (úgy, hogy az alkalmazás akkor férhet hozzá a közzétett).

### <a name="set-values-in-appsettingsjson"></a>Az appSettings.JSON fájl értékek beállítása

Futtassa a `az ams account sp create` leírtak szerint parancs [API-k elérése](access-api-cli-how-to.md). A parancs visszaadja a json-t, a "appsettings.json" át kell másolnia.
 
## <a name="add-configuration-file"></a>Konfigurációs fájl hozzáadása

Az egyszerűség kedvéért felelős "appsettings.json" értékeinek olvasása konfigurációs fájl hozzáadásához.

1. Adjon hozzá egy új .cs osztályt a projekthez. Nevezze el a következőképpen: `ConfigWrapper`. 
1. Illessze be a következő kódot a fájl (Ez a példa feltételezi, hogy a névtér van `ConsoleApp1`).

```csharp
using System;

using Microsoft.Extensions.Configuration;

namespace ConsoleApp1
{
    public class ConfigWrapper
    {
        private readonly IConfiguration _config;

        public ConfigWrapper(IConfiguration config)
        {
            _config = config;
        }

        public string SubscriptionId
        {
            get { return _config["SubscriptionId"]; }
        }

        public string ResourceGroup
        {
            get { return _config["ResourceGroup"]; }
        }

        public string AccountName
        {
            get { return _config["AccountName"]; }
        }

        public string AadTenantId
        {
            get { return _config["AadTenantId"]; }
        }

        public string AadClientId
        {
            get { return _config["AadClientId"]; }
        }

        public string AadSecret
        {
            get { return _config["AadSecret"]; }
        }

        public Uri ArmAadAudience
        {
            get { return new Uri(_config["ArmAadAudience"]); }
        }

        public Uri AadEndpoint
        {
            get { return new Uri(_config["AadEndpoint"]); }
        }

        public Uri ArmEndpoint
        {
            get { return new Uri(_config["ArmEndpoint"]); }
        }

        public string Region
        {
            get { return _config["Region"]; }
        }
    }
}
```

## <a name="connect-to-the-net-client"></a>A .NET-ügyfél csatlakozhat.

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. Az alábbi kódot a GetCredentialsAsync függvényt hoz létre a helyi konfigurációs fájlban megadott hitelesítő adatok alapján ServiceClientCredentials objektum.

1. Nyissa meg `Program.cs`.
1. Illessze be a következő kódot:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Threading.Tasks;

using Microsoft.Azure.Management.Media;
using Microsoft.Azure.Management.Media.Models;
using Microsoft.Extensions.Configuration;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;

namespace ConsoleApp1
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            
            ConfigWrapper config = new ConfigWrapper(new ConfigurationBuilder()
                .SetBasePath(Directory.GetCurrentDirectory())
                .AddJsonFile("appsettings.json", optional: true, reloadOnChange: true)
                .AddEnvironmentVariables()
                .Build());

            try
            {
                IAzureMediaServicesClient client = await CreateMediaServicesClientAsync(config);
                Console.WriteLine("connected");
            }
            catch (Exception exception)
            {
                if (exception.Source.Contains("ActiveDirectory"))
                {
                    Console.Error.WriteLine("TIP: Make sure that you have filled out the appsettings.json file before running this sample.");
                }

                Console.Error.WriteLine($"{exception.Message}");

                ApiErrorException apiException = exception.GetBaseException() as ApiErrorException;
                if (apiException != null)
                {
                    Console.Error.WriteLine(
                        $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
                }
            }

            Console.WriteLine("Press Enter to continue.");
            Console.ReadLine();
        }
 
        private static async Task<ServiceClientCredentials> GetCredentialsAsync(ConfigWrapper config)
        {
            // Use ApplicationTokenProvider.LoginSilentWithCertificateAsync or UserTokenProvider.LoginSilentAsync to get a token using service principal with certificate
            //// ClientAssertionCertificate
            //// ApplicationTokenProvider.LoginSilentWithCertificateAsync

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symetric key
            ClientCredential clientCredential = new ClientCredential(config.AadClientId, config.AadSecret);
            return await ApplicationTokenProvider.LoginSilentAsync(config.AadTenantId, clientCredential, ActiveDirectoryServiceSettings.Azure);
        }

        private static async Task<IAzureMediaServicesClient> CreateMediaServicesClientAsync(ConfigWrapper config)
        {
            var credentials = await GetCredentialsAsync(config);

            return new AzureMediaServicesClient(config.ArmEndpoint, credentials)
            {
                SubscriptionId = config.SubscriptionId,
            };
        }

    }
}
```

## <a name="next-steps"></a>További lépések

- [Oktatóanyag: Feltöltése, kódolása és streamelése videók – .NET](stream-files-tutorial-with-api.md) 
- [Oktatóanyag: A Media Services v3 - .NET élő Stream](stream-live-tutorial-with-api.md)
- [Oktatóanyag: Elemezhet videókat a Media Services v3 – .NET](analyze-videos-tutorial-with-api.md)
- [Hozzon létre egy feladat bemenete egy helyi fájlból – .NET](job-input-from-local-file-how-to.md)
- [Hozzon létre egy feladat bemenete HTTPS URL-címet – .NET](job-input-from-http-how-to.md)
- [Az egyéni átalakítási – .NET kódolása](customize-encoder-presets-how-to.md)
- [AES-128, a dinamikus titkosítás és a kulcstovábbítást – .NET](protect-with-aes128.md)
- [DRM a dinamikus titkosítás és a licenc kézbesítési szolgáltatás használata – .NET](protect-with-drm.md)
- [A meglévő házirend - .NET aláíró kulcs lekérése](get-content-key-policy-dotnet-howto.md)
- [Szűrők létrehozása a Media Services – .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Az Azure Functions v2 a Media Services v3 igény szerinti videó példák speciális](https://aka.ms/ams3functions)

## <a name="see-also"></a>Lásd még

[.NET-referencia](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
