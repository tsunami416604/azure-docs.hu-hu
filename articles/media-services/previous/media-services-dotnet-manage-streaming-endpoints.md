---
title: A streamelési végpontok kezelése a .NET SDK segítségével. | Microsoft Docs
description: Ez a cikk bemutatja, hogyan kezelheti a streamelési végpontok az Azure Portalon.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.assetid: 0da34a97-f36c-48d0-8ea2-ec12584a2215
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b6c6fccd473ae57139c0b46bf32dc9468a4ba1a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74901272"
---
# <a name="manage-streaming-endpoints-with-net-sdk"></a>Streamelési végpontok kezelése .NET SDK-val  

>[!NOTE]
>Győződjön meg róla, hogy tekintse át az [áttekintő](media-services-streaming-endpoints-overview.md) cikket. Is, felülvizsgálat [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

A cikkben található kód bemutatja, hogyan lehet elvégezni a következő feladatokat az Azure Media Services .NET SDK használatával:

- Vizsgálja meg az alapértelmezett streamelési végpontot.
- Új streamelési végpont létrehozása/hozzáadása.

    Előfordulhat, hogy több streamelési végpontot szeretne, ha azt tervezi, hogy különböző CDN-ekkel vagy CDN-vel és közvetlen hozzáféréssel rendelkezik.

    > [!NOTE]
    > A számlázás csak akkor történik meg, ha a streamelési végpont futó állapotban van.
    
- Frissítse a streamelési végpontot.
    
    Győződjön meg arról, hogy hívja a Update() függvényt.

- Törölje a streamelési végpontot.

    >[!NOTE]
    >Az alapértelmezett streamelési végpont nem törölhető.

A streamelési végpont méretezéséről ebben a [cikkben](media-services-portal-scale-streaming-endpoints.md) olvashat.

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztői környezetet, és népesítse be az app.config fájlt a kapcsolatadataival, ahogy azt a Media Services fejlesztése a [.NET fájlban leírta.](media-services-dotnet-how-to-use.md) 

## <a name="add-code-that-manages-streaming-endpoints"></a>A streamelési végpontokat kezelő kód hozzáadása
    
Cserélje le a Program.cs kódot a következő kódra:

```csharp
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.MediaServices.Client.Live;

namespace AMSStreamingEndpoint
{
    class Program
    {
        // Read values from the App.config file.

        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var defaultStreamingEndpoint = _context.StreamingEndpoints.Where(s => s.Name.Contains("default")).FirstOrDefault();
            ExamineStreamingEndpoint(defaultStreamingEndpoint);

            IStreamingEndpoint newStreamingEndpoint = AddStreamingEndpoint();
            UpdateStreamingEndpoint(newStreamingEndpoint);
            DeleteStreamingEndpoint(newStreamingEndpoint);
        }

        static public void ExamineStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            Console.WriteLine(streamingEndpoint.Name);
            Console.WriteLine(streamingEndpoint.StreamingEndpointVersion);
            Console.WriteLine(streamingEndpoint.FreeTrialEndTime);
            Console.WriteLine(streamingEndpoint.ScaleUnits);
            Console.WriteLine(streamingEndpoint.CdnProvider);
            Console.WriteLine(streamingEndpoint.CdnProfile);
            Console.WriteLine(streamingEndpoint.CdnEnabled);
        }

        static public IStreamingEndpoint AddStreamingEndpoint()
        {
            var name = "StreamingEndpoint" + DateTime.UtcNow.ToString("hhmmss");
            var option = new StreamingEndpointCreationOptions(name, 1)
            {
                StreamingEndpointVersion = new Version("2.0"),
                CdnEnabled = true,
                CdnProfile = "CdnProfile",
                CdnProvider = CdnProviderType.PremiumVerizon
            };

            var streamingEndpoint = _context.StreamingEndpoints.Create(option);

            return streamingEndpoint;
        }

        static public void UpdateStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            if (streamingEndpoint.StreamingEndpointVersion == "1.0")
                streamingEndpoint.StreamingEndpointVersion = "2.0";

            streamingEndpoint.CdnEnabled = false;
            streamingEndpoint.Update();
        }

        static public void DeleteStreamingEndpoint(IStreamingEndpoint streamingEndpoint)
        {
            streamingEndpoint.Delete();
        }
    }
}
```

## <a name="next-steps"></a>További lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

