---
title: Az Azure Media Encoder Standard használatával skála automatikus létrehozását |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan bementi felbontás és átviteli sebesség alapján a skála automatikus létrehozása a Media Encoder Standard (MES) használatával. A bementi felbontás és sávszélességű soha nem lehet túllépni. Például ha a bemenet 720p, 3 MB/s, a kimeneti lesz 720p legjobb maradnak, és elkezdi alacsonyabb, mint 3 MB/s díjakat.
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
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: 5ccd53ac03235bd114218135488bef25891754be
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004061"
---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Az Azure Media Encoder Standard használatával skála automatikus létrehozása  

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan skála (sávszélességű felbontású párok) a bementi felbontás és átviteli sebesség alapján automatikus létrehozása a Media Encoder Standard (MES) használatával. A bementi felbontás és átviteli sebesség nem haladhatja meg az automatikusan generált beállításkészlet. Például ha a bemeneti 3 Mbps sebességnél 720p, kimeneti 720p legjobb marad, és elkezdi alacsonyabb, mint 3 MB/s díjakat.

### <a name="encoding-for-streaming-only"></a>Csak folyamatos átviteli kódolás

Ha a leképezés kódolnia a forrásvideókat, csak a streaming, inkább a "adaptív Streamelés" előbeállítást kódolási tevékenység létrehozásakor. Használatakor a **adaptív Streamelés** készletet, a MES kódoló rendszer intelligensen költségplafont, skála. Azonban nem lesz a kódolási költségek, mivel a szolgáltatás határozza meg, hány rétegek használatával szabályozhatja, és milyen felbontásban. Eredményeként kódolás MES által előállított kimeneti rétegek példa látható a **adaptív Streamelés** Ez a cikk végén található előbeállítást. Az Eszközintelligencia MP4-fájlokat tartalmazza, ahol az audio- és kimeneti nem időosztásos.

### <a name="encoding-for-streaming-and-progressive-download"></a>Adatfolyam-továbbítási és progresszív letöltéshez kódolás

Ha a leképezés kódolnia a forrásvideókat streameléshez valamint MP4-fájlok progresszív letöltéshez szükséges, inkább a "tartalom adaptív több sávszélességű MP4" előbeállítást kódolási tevékenység létrehozásakor. Használatakor a **tartalom adaptív több sávszélességű MP4** készletet, a MES kódoló alkalmazza, a fenti kódolási ugyanazt a logikát, de most a kimeneti adategység fogja tartalmazni MP4-fájlokat, hang és videó közbeékeléses van. A progresszív letöltés fájlként a MP4-fájlok (például a legmagasabb bitsebességű verziót) egyikét használhatja.

## <a id="encoding_with_dotnet"></a>Kódolás a Media Services .NET SDK használatával

Az alábbi példakód a Media Services .NET SDK-t használja a következő feladatokat:

- Hozzon létre egy kódolási feladatot.
- A Media Encoder Standard kódolóval mutató hivatkozás beolvasása.
- Adja hozzá a kódolási tevékenység a feladathoz, és adja meg, hogy használja a **adaptív Streamelés** beállításkészletet. 
- A kódolt objektumhoz tartalmazó kimeneti adategység létrehozása.
- Adjon hozzá egy eseménykezelőt a feladat állapotának ellenőrzése.
- A feladat elküldéséhez.

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

## <a id="output"></a>Output

Ez a szakasz eredményeként kódolás MES által előállított kimeneti rétegek három példákat mutat be a **adaptív Streamelés** beállításkészletet. 

### <a name="example-1"></a>1. példa
Forrás magasság "1080" és "29.970" képkockasebesség 6 videó rétegek hoz létre:

|Réteg|Magasság|Szélesség|Bitrate(kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>2. példa
Forrás magasság "720" és "23.970" képkockasebesség 5 videó rétegek hoz létre:

|Réteg|Magasság|Szélesség|Bitrate(kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>3. példa
Forrás magasság "360" és "29.970" képkockasebesség 3 videó rétegek hoz létre:

|Réteg|Magasság|Szélesség|Bitrate(kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[A Media Services kódolási áttekintése](media-services-encode-asset.md)

