---
title: "Azure Media Encoder Standard segítségével automatikusan előállítja a sávszélességű létra |} Microsoft Docs"
description: "Ez a témakör bemutatja, hogyan egy bemeneti feloldási és átviteli sebesség alapján sávszélességű létra automatikus létrehozása a Media Encoder Standard (MES) segítségével. A bemeneti megoldás és sávszélességű soha nem túllépése. Például ha a bemeneti 720p, 3 MB/s, kimeneti lesz 720p legjobb maradnak, és alacsonyabb, mint 3 MB/s sebesség időpontban fog elindulni."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 63ed95da-1b82-44b0-b8ff-eebd535bc5c7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako
ms.openlocfilehash: b5616aa9f8b15ab576d914fbae89a56f64c27f4a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
#  <a name="use-azure-media-encoder-standard-to-auto-generate-a-bitrate-ladder"></a>Azure Media Encoder Standard segítségével egy sávszélességű létra automatikus létrehozása

## <a name="overview"></a>Áttekintés

Ez a témakör bemutatja, hogyan sávszélességű létra (sávszélességű felbontású párok) a bemeneti feloldási és átviteli sebesség alapján automatikus létrehozása a Media Encoder Standard (MES) segítségével. Az automatikusan generált előre definiált nem haladhatja meg a bemeneti megoldás és átviteli sebesség. Például ha a bemeneti 720p, 3 MB/s, kimeneti lesz 720p legjobb maradnak, és alacsonyabb, mint 3 MB/s sebesség időpontban fog elindulni.

### <a name="encoding-for-streaming-only"></a>Az adatfolyamként történő csak kódolás

Ha a szándéka az, a forrás videó csak az adatfolyamként történő kódolásához, akkor %d használja, a "adaptív Streameléshez" egy kódolási feladat létrehozásakor beállított. Használatakor a **adaptív Streameléshez** előzetesen létrehozott, a MES kódoló fog intelligens módon cap egy sávszélességű létra. Azonban nem lesz szabályozhatja a kódolás költségek, mivel a szolgáltatás határozza meg, hány rétegek használata, és milyen felbontásban. Példa látható miatt kódolás MES által előállított kimeneti rétegek a **adaptív Streameléshez** beállított Ez a témakör végén. Az eszköz fogja tartalmazni MP4-fájlokat, ahol a hang- és kimeneti nem időosztásos.

### <a name="encoding-for-streaming-and-progressive-download"></a>Adatfolyam-továbbításhoz és progresszív letöltés kódolása

Ha a szándéka az, a forrás videó az adatfolyamként történő kódolásához, valamint a progresszív letöltés MP4-fájlok előállításához, akkor %d használja a "tartalom adaptív több sávszélességű MP4" egy kódolási feladat létrehozásakor beállított. Használatakor a **tartalom adaptív több sávszélességű MP4** előzetesen létrehozott, a MES kódoló a fenti kódolási logikák hatálya alá, de most a kimeneti adategységen fogja tartalmazni MP4-fájlokat, ahol hang és videó időosztásos. A MP4-fájlok (például a legmagasabb sávszélességű verzió) valamelyikét használhatja a progresszív letöltés fájlként.

## <a id="encoding_with_dotnet"></a>A Media Services .NET SDK kódolás

Az alábbi példakód Media Services .NET SDK-t használja a következő feladatok végezhetők el:

- Hozzon létre egy kódolási feladat.
- A Media Encoder Standard encoder mutató hivatkozás beszerzése.
- Adja hozzá a feladatot egy kódolási feladatot, és adja meg, hogy használja a **adaptív Streameléshez** előre. 
- Hozzon létre egy kimeneti eszközt, amely tartalmazza majd a kódolt objektumhoz.
- Adjon hozzá egy eseménykezelő, ellenőrizze a feladat előrehaladását.
- A feladat elküldéséhez.

#### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint. 

#### <a name="example"></a>Példa

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
        ConfigurationManager.AppSettings["AADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
        ConfigurationManager.AppSettings["MediaServiceRESTAPIEndpoint"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
            var tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain, AzureEnvironments.AzureCloudEnvironment);
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

## <a id="output"></a>Kimeneti

Ez a szakasz ismerteti a kimeneti rétegek MES miatt kódolás három példái a **adaptív Streameléshez** előre. 

### <a name="example-1"></a>1. példa
A magasság "1080" és "29.970" képkockasebességhez forrás 6 videó rétegek hoz létre:

|Réteg|Magassága|Szélessége|Bitrate(kbps)|
|---|---|---|---|
|1|1080|1920|6780|
|2|720|1280|3520|
|3|540|960|2210|
|4|360|640|1150|
|5|270|480|720|
|6|180|320|380|

### <a name="example-2"></a>2. példa
A magasság "720" és "23.970" képkockasebességhez forrás 5 videó rétegek hoz létre:

|Réteg|Magassága|Szélessége|Bitrate(kbps)|
|---|---|---|---|
|1|720|1280|2940|
|2|540|960|1850|
|3|360|640|960|
|4|270|480|600|
|5|180|320|320|

### <a name="example-3"></a>3. példa
A magasság "360" és "29.970" képkockasebességhez forrás 3 videó rétegek hoz létre:

|Réteg|Magassága|Szélessége|Bitrate(kbps)|
|---|---|---|---|
|1|360|640|700|
|2|270|480|440|
|3|180|320|230|
## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Media Services kódolási áttekintése](media-services-encode-asset.md)

