---
title: Bitráta-létrák automatikus létrehozása a Media Encoder Standard használatával – Azure | Microsoft Docs
description: Ez a témakör bemutatja, hogyan használható a Media Encoder Standard (MES) a bitráta-létrák automatikus létrehozásához a bemeneti felbontás és a bitráta alapján. A bemeneti felbontás és a bitráta soha nem fog meghaladni. Ha például a bemenet 720p a 3Mbps-on, a kimenet 720p marad a legjobb esetben, és a 3Mbps alacsonyabb díjszabással kezdődik.
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
ms.openlocfilehash: 14575e0c95acf1345fc3358b323083d86d8eedee
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543547"
---
#  <a name="use-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Bitráta-létrák automatikus létrehozása a Media Encoder Standard használatával  

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan használható a Media Encoder Standard (MES) a bitráta-létrák (bitráta-feloldási párok) automatikus létrehozásához a bemeneti felbontás és a bitráta alapján. Az automatikusan létrehozott beállításkészlet soha nem lépi túl a bemeneti felbontást és a bitrátát. Például ha a bemeneti 3 Mbps sebességnél 720p, kimeneti 720p legjobb marad, és elkezdi alacsonyabb, mint 3 MB/s díjakat.

### <a name="encoding-for-streaming-only"></a>Csak adatfolyam kódolása

Ha a forrásként szolgáló videó csak a folyamatos átvitelhez van kódolva, akkor a kódolási feladat létrehozásakor az "adaptív streaming" készletet kell használnia. Az **adaptív adatfolyam** -készlet használatakor a MES kódoló intelligens módon kap egy bitráta-létrát. Azonban nem fogja tudni szabályozni a kódolási költségeket, mivel a szolgáltatás meghatározza, hogy hány réteget használ, és milyen felbontásban. A MES által előállított kimeneti rétegekre példákat tekinthet meg a jelen cikk végén található **adaptív streaming** -beállításkészlet kódolása miatt. A kimeneti eszköz olyan MP4-fájlokat tartalmaz, amelyekben a hang és a videó nincs átfedésben.

### <a name="encoding-for-streaming-and-progressive-download"></a>Kódolás a folyamatos átvitelhez és a progresszív letöltéshez

Ha arra törekszik, hogy a forrásként szolgáló videót a streaminghez kódolja, és MP4-fájlokat készítsen a progresszív letöltéshez, akkor a kódolási feladat létrehozásakor a "tartalom adaptív többszörös sávszélességű MP4" készletet kell használnia. Ha a **tartalom Adaptív átviteli** sebességű MP4-készletét használja, a MES kódoló ugyanazt a kódolási logikát alkalmazza, mint a fenti, de most a kimeneti eszköz olyan MP4-fájlokat fog tartalmazni, amelyekben a hang és a videó összekapcsolt marad. Az alábbi MP4-fájlok (például a legmagasabb sávszélességű verziók) egyikét használhatja progresszív letöltési fájlként.

## <a id="encoding_with_dotnet"></a>Kódolás Media Services .NET SDK-val

A következő kódrészlet a Media Services .NET SDK-t használja a következő feladatok elvégzéséhez:

- Hozzon létre egy kódolási feladatot.
- A Media Encoder Standard kódolóra mutató hivatkozás beszerzése.
- Adjon hozzá egy kódolási feladatot a feladathoz, és adja meg, hogy az **adaptív streaming** -készletet használja. 
- Hozzon létre egy kimeneti eszközt, amely tartalmazza a kódolt objektumot.
- Adjon hozzá egy eseménykezelőt a feladatok előrehaladásának ellenőrzéséhez.
- Küldje el a feladatot.

#### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint. 

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

## <a id="output"></a>Kimeneti

Ez a szakasz három példát mutat be a MES által előállított kimeneti rétegekre az **adaptív streaming** -beállításkészlet kódolása miatt. 

### <a name="example-1"></a>1\. példa
Forrás magasság "1080" és "29.970" képkockasebesség 6 videó rétegek hoz létre:

|Réteg|Magasság|Szélesség|Bitráta (Kbit/s)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>2\. példa
Forrás magasság "720" és "23.970" képkockasebesség 5 videó rétegek hoz létre:

|Réteg|Magasság|Szélesség|Bitráta (Kbit/s)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>3\. példa
Forrás magasság "360" és "29.970" képkockasebesség 3 videó rétegek hoz létre:

|Réteg|Magasság|Szélesség|Bitráta (Kbit/s)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Media Services kódolás áttekintése](media-services-encode-asset.md)

