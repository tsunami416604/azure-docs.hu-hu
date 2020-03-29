---
title: Bitráta-létra automatikus generálása – Azure | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan használható a Media Encoder Standard (MES) segítségével a bemeneti felbontás és a bitráta alapján automatikusan létrehozhat egy bitráta létrát.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: b7f0b77ba11a0c9c1670ec240caf45fcf61a934d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896021"
---
#  <a name="use-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Bitráta-létra automatikus generálása a Media Encoder Standard segítségével  

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan használható a Media Encoder Standard (MES) segítségével a bemeneti felbontás és a bitráta alapján automatikusan generálhat egy bitráta-létrát (bitráta-felbontású párokat). Az automatikusan létrehozott készlet soha nem haladja meg a bemeneti felbontást és a bitrátát. Ha például a bemenet 720p 3 Mbps sebességgel, a kimenet a legjobb esetben is 720p marad, és 3 Mbps-nál alacsonyabb sebességgel indul.

### <a name="encoding-for-streaming-only"></a>Kódolás csak streameléshez

Ha a szándék az, hogy a forrásvideó kódolása csak a streameléshez, majd az "Adaptív streamelési" készletet kell használnia egy kódolási feladat létrehozásakor. **Az Adaptive Streaming** készlet használatakor a MES kódoló intelligensen lezárja a bitráta létrát. A kódolási költségekazonban nem szabályozhatók, mivel a szolgáltatás határozza meg, hogy hány réteget és milyen felbontásban kell használni. A cikk végén található **Adaptive Streaming** készlettel történő kódolás eredményeként a MES által létrehozott kimeneti rétegek példáit is láthat. A kimeneti eszköz MP4-fájlokat tartalmaz, ahol a hang és a videó nincs átrendeződve.

### <a name="encoding-for-streaming-and-progressive-download"></a>Kódolás streameléshez és progresszív letöltéshez

Ha a szándék az, hogy kódolja a forrás videó streaming, valamint a termelő MP4 fájlok progresszív letöltés, majd használja a "Content Adaptive Multiple Bitrate MP4" előre beállított kódolásához kódolási feladat. A **Content Adaptive Multiple Bitrate MP4-készlet** használatakor a MES kódoló ugyanazt a kódolási logikát alkalmazza, mint fent, de most a kimeneti eszköz MP4-fájlokat tartalmaz, ahol a hang és a videó átvan ágyazva. Használhatja az egyik ilyen MP4 fájlokat (például a legmagasabb bitráta verzió), mint egy progresszív letöltésfájlt.

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Kódolás a Media Services .NET SDK szolgáltatással

A következő kódpélda a Media Services .NET SDK segítségével hajtja végre a következő feladatokat:

- Hozzon létre egy kódolási feladatot.
- Hivatkozás a Media Encoder Standard kódolóra.
- Adjon hozzá egy kódolási feladatot a feladathoz, és adja meg az **Adaptív streamelési** készlet használatát. 
- Hozzon létre egy kimeneti eszközt, amely tartalmazza a kódolt eszközt.
- Adjon hozzá egy eseménykezelőt a feladat előrehaladásának ellenőrzéséhez.
- Küldje el a feladatot.

#### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztői környezetet, és népesítse be az app.config fájlt a kapcsolatadataival, ahogy azt a Media Services fejlesztése a [.NET fájlban leírta.](media-services-dotnet-how-to-use.md) 

#### <a name="example"></a>Példa

```
using System;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace AdaptiveStreamingMESPresest
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

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");

            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            "Adaptive Streaming",
            TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }
        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
                    break;
                case JobState.Canceling:
                case JobState.Queued:
                case JobState.Scheduled:
                case JobState.Processing:
                    Console.WriteLine("Please wait...\n");
                    break;
                case JobState.Canceled:
                case JobState.Error:

                    // Cast sender as a job.
                    IJob job = (IJob)sender;

                    // Display or log error details as needed.
                    break;
                default:
                    break;
            }
        }
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

## <a name="output"></a><a id="output"></a>Kimenet

Ez a szakasz három példát mutat be a MES által az **Adaptive Streaming** készlettel való kódolás eredményeként létrehozott kimeneti rétegekre. 

### <a name="example-1"></a>1. példa
Forrás magassága "1080" és framerate "29.970" termel 6 videó rétegek:

|Réteg|Height (Magasság)|Szélesség|Bitráta(kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>2. példa
Forrás magassága "720" és framerate "23.970" termel 5 videó rétegek:

|Réteg|Height (Magasság)|Szélesség|Bitráta(kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>3. példa
Forrás magassága "360" és framerate "29.970" termel 3 videó rétegek:

|Réteg|Height (Magasság)|Szélesség|Bitráta(kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Media Services kódolás – áttekintés](media-services-encode-asset.md)

