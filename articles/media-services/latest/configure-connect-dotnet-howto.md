---
title: Csatlakozás az Azure Media Services 3-as v3 API-hoz – .NET
description: Ez a cikk bemutatja, hogyan csatlakozhat a Media Services v3 API-hoz a .NET.This article this article this that to connect to Media Services v3 API with .NET.
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
ms.date: 09/18/2019
ms.author: juliako
ms.openlocfilehash: b8f4de1a5b9d8216ae2442631f5f9135c3c72d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269808"
---
# <a name="connect-to-media-services-v3-api---net"></a>Csatlakozás a Media Services 3-as v3 API-jához – .NET

Ez a cikk bemutatja, hogyan csatlakozhat az Azure Media Services v3.NET SDK szolgáltatásegyszerű bejelentkezési módszer használatával.

## <a name="prerequisites"></a>Előfeltételek

- [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md). Az erőforráscsoport nevének és a Media Services-fiók nevének megjegyzése
- Telepítse a .NET fejlesztéshez használni kívánt eszközt. A cikkben ismertetett lépések a [Visual Studio 2019 Community Edition használatát mutatják be.](https://www.visualstudio.com/downloads/) Használhatja a Visual Studio-kódot, lásd: [A C# használata.](https://code.visualstudio.com/docs/languages/csharp) Másik lehetőségként használhat másik kódszerkesztőt is.

> [!IMPORTANT]
> Tekintse át [az elnevezési konvenciókat.](media-services-apis-overview.md#naming-conventions)

## <a name="create-a-console-application"></a>Konzolalkalmazás létrehozása

1. Indítsa el a Visual Studiót. 
1. Kattintson a **Fájl** menü **Új** > **projekt parancsára.** 
1. Hozzon létre egy **.NET Core** konzolalkalmazást.

A témakörben szereplő mintaalkalmazás a célokat célozza `netcoreapp2.0`meg. A kód az "async main"-t használja, amely a C# 7.1-től kezdve érhető el. Lásd ezt a [blogot](https://blogs.msdn.microsoft.com/benwilli/2017/12/08/async-main-is-available-but-hidden/) további részletekért.

## <a name="add-required-nuget-packages"></a>A szükséges NuGet csomagok hozzáadása

1. A Visual Studio alkalmazásban válassza az **Eszközök** > **NuGet csomagkezelő** > **NuGet Kezelő konzollehetőséget.**
2. A **Csomagkezelő konzol** ablakában a következő NuGet csomagok hozzáadásához használja `Install-Package` a parancsot. Például: `Install-Package Microsoft.Azure.Management.Media`.

|Csomag|Leírás|
|---|---|
|`Microsoft.Azure.Management.Media`|Az Azure Media Services SDK-t. <br/>Ha meg szeretne győződni arról, hogy a legújabb Azure Media Services csomagot használja, ellenőrizze a [Microsoft.Azure.Management.Media](https://www.nuget.org/packages/Microsoft.Azure.Management.Media)webhelyen.|
|`Microsoft.Rest.ClientRuntime.Azure.Authentication`|ADAL hitelesítési könyvtár az Azure SDK for NET-hez|
|`Microsoft.Extensions.Configuration.EnvironmentVariables`|Konfigurációs értékek olvasása környezeti változókból és helyi JSON-fájlokból|
|`Microsoft.Extensions.Configuration.Json`|Konfigurációs értékek olvasása környezeti változókból és helyi JSON-fájlokból
|`WindowsAzure.Storage`|Tároló SDK|

## <a name="create-and-configure-the-app-settings-file"></a>Az alkalmazásbeállítási fájl létrehozása és konfigurálása

### <a name="create-appsettingsjson"></a>Appsettings.json létrehozása

1. Go go **Általános** > **szöveg fájl**.
1. Nevezd el "appsettings.json"-nak.
1. Állítsa a .json fájl "Másolás kimeneti könyvtárba" tulajdonságát "Másolás, ha újabb" értékre (hogy az alkalmazás közzétételkor hozzáférhessen).

### <a name="set-values-in-appsettingsjson"></a>Értékek beállítása az appsettings.json ban

Futtassa a parancsot az `az ams account sp create` access [API-kban leírtak szerint.](access-api-cli-how-to.md) A parancs visszaadja a json t, amelyet az "appsettings.json" programba kell másolnia.
 
## <a name="add-configuration-file"></a>Konfigurációs fájl hozzáadása

A kényelem érdekében adjon hozzá egy konfigurációs fájlt, amely az "appsettings.json" értékeinek olvasásáért felelős.

1. Adjon hozzá egy új .cs osztályt a projekthez. Nevezze el a következőképpen: `ConfigWrapper`. 
1. Illessze be a következő kódot ebbe a fájlba `ConsoleApp1`(ez a példa feltételezi, hogy a névtér a).

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

## <a name="connect-to-the-net-client"></a>Csatlakozás a .NET ügyfélhez

Ha szeretné megkezdeni a Media Services API-k használatát a .NET-tel, létre kell hoznia egy **AzureMediaServicesClient** objektumot. Az objektum létrehozásához meg kell adnia a hitelesítő adatokat, amelyekkel az ügyfél csatlakozhat az Azure-hoz az Azure AD használatával. Az alábbi kódban a GetCredentialsAsync függvény a helyi konfigurációs fájlban megadott hitelesítő adatok alapján hozza létre a ServiceClientCredentials objektumot.

1. Nyissa meg a következő fájlt: `Program.cs`.
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

            // Use ApplicationTokenProvider.LoginSilentAsync to get a token using a service principal with symmetric key
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

- [Oktatóanyag: Videók feltöltése, kódolása és streamelése - .NET](stream-files-tutorial-with-api.md) 
- [Oktatóanyag: Élő közvetítés a Media Services v3 - .NET segítségével](stream-live-tutorial-with-api.md)
- [Oktatóanyag: Videók elemzése a Media Services v3 - .NET segítségével](analyze-videos-tutorial-with-api.md)
- [Feladatbevitel létrehozása helyi fájlból – .NET](job-input-from-local-file-how-to.md)
- [Feladatbevitel létrehozása HTTPS URL-címről – .NET](job-input-from-http-how-to.md)
- [Kódolás egyéni átalakítással - .NET](customize-encoder-presets-how-to.md)
- [Az AES-128 dinamikus titkosítás és a kulcskézbesítési szolgáltatás használata - .NET](protect-with-aes128.md)
- [Drm dinamikus titkosítás és licenckézbesítési szolgáltatás használata – .NET](protect-with-drm.md)
- [Aláírási kulcs beszereznie a meglévő házirendből - .NET](get-content-key-policy-dotnet-howto.md)
- [Szűrők létrehozása a Media Services szolgáltatással - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Speciális, igény szerinti videós példák az Azure Functions v2 v2-re a Media Services v3-as ával](https://aka.ms/ams3functions)

## <a name="see-also"></a>Lásd még

* [.NET-referencia](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* További kódpéldákat a [.NET SDK-minták](https://github.com/Azure-Samples/media-services-v3-dotnet) tártárában talál.
