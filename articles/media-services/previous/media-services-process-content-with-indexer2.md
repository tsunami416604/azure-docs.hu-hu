---
title: Médiafájlok indexelése Azure Media Indexer 2 előzetes verzióval | Microsoft Docs
description: Azure Media Indexer lehetővé teszi, hogy a médiafájlokat kereshetővé tegye, és teljes szöveges átiratot hozzon a kódolt feliratok és kulcsszavak számára. Ez a témakör bemutatja, hogyan használható Media Indexer 2 előzetes verzió.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/22/2019
ms.author: juliako
ms.reviewer: adsolank
ms.openlocfilehash: 60458365fd9ed3a9a72f5c2da82431fb55792a50
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74464061"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Médiafájlok indexelése Azure Media Indexer 2 előzetes verzióval

> [!NOTE]
> A [2. Azure Media Indexer](media-services-process-content-with-indexer2.md) adathordozó-processzor 2020. január 1-től megszűnik. [Azure Media Services video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) lecseréli ezt az örökölt adathordozó-processzort. További információ: [áttelepítés Azure Media Indexerról és Azure Media Indexer 2 – Azure Media Services video Indexer](migrate-indexer-v1-v2.md).

A **Azure Media Indexer 2 előnézeti** adathordozó-feldolgozó (mp) lehetővé teszi a médiafájlok és a tartalmak kereshetővé tételét, valamint a kódolt feliratozási sávok létrehozását. A [Azure Media Indexer](media-services-index-content.md)korábbi verziójához képest **Azure Media Indexer 2 előzetes** verzió gyorsabb indexelést végez, és szélesebb körű nyelvi támogatást nyújt. A támogatott nyelvek közé tartoznak a következők: angol, spanyol, francia, német, olasz, Kínai (mandarin, egyszerűsített), portugál, Arab, orosz és Japán.

A **Azure Media Indexer 2 előzetes** verziójú mp jelenleg előzetes verzióban érhető el.

Ez a cikk bemutatja, hogyan hozhat létre indexelési feladatokat **Azure Media Indexer 2 előzetes**verzióval.

## <a name="considerations"></a>Megfontolandó szempontok

A következő szempontokat kell figyelembe venni:
 
* Az indexelő 2 nem támogatott az Azure China 21Vianet és Azure Governmentban.
* A tartalom indexelése során ügyeljen arra, hogy a nagyon egyértelmű beszédtel (háttérzene, zaj, effektusok vagy mikrofon nélkül sziszegve) adathordozó-fájlokat használjon. Néhány példa a megfelelő tartalomra: rögzített értekezletek, előadások vagy bemutatók. Előfordulhat, hogy az alábbi tartalmak nem alkalmasak indexelésre: filmek, TV-műsorok, bármilyen kevert hang-és hanghatások, a rosszul rögzített tartalom (sziszegés).
 
## <a name="input-and-output-files"></a>Bemeneti és kimeneti fájlok
### <a name="input-files"></a>Bemeneti fájlok
Hang-és videofájlok

### <a name="output-files"></a>Kimeneti fájlok
Az indexelési feladatok a következő formátumokban hozhatnak be zárt képaláírás-fájlokat:  

* **TTML**
* **WebVTT**

Az ezekben a formátumokban lezárt feliratú (CC) fájlok használhatók hang-és videofájlok elérhetővé tételéhez a fogyatékkal élők számára.

## <a name="task-configuration-preset"></a>Feladat konfigurációja (előre beállított)
**Azure Media Indexer 2 előzetes**verzióval rendelkező indexelési feladat létrehozásakor meg kell adnia egy konfigurációs beállításkészletet.

A következő JSON a rendelkezésre álló paramétereket állítja be.

```json
    {
      "version":"1.0",
      "Features":
        [
           {
           "Options": {
                "Formats":["WebVtt","ttml"],
                "Language":"enUs",
                "Type":"RecoOptions"
           },
           "Type":"SpReco"
        }]
    }
```

## <a name="supported-languages"></a>Támogatott nyelvek
Azure Media Indexer 2 az előzetes verzió támogatja a beszédfelismerést a következő nyelveken (a feladat konfigurációjában a nyelv nevének megadásakor), 4 karakterből álló kódot használjon zárójelben az alábbi módon:

* Angol [EnUs]
* Spanyol [EsEs]
* Kínai (mandarin, egyszerűsített) [ZhCn]
* Francia [FrFr]
* Német [DeDe]
* Olasz [ItIt]
* Portugál [PtBr]
* Arab (egyiptomi) [ArEg]
* Japán [JaJp]
* Orosz [Lajos]
* Brit angol [EnGb]
* Spanyol (Mexikó) [EsMx] 

## <a name="supported-file-types"></a>Támogatott fájltípusok

A támogatott fájltípusokkal kapcsolatos információkért tekintse meg a [támogatott kodekek/formátumok](media-services-media-encoder-standard-formats.md#input-containerfile-formats) szakaszt.

## <a name="net-sample-code"></a>.NET-mintakód

A következő program a következőket mutatja be:

1. Hozzon létre egy adategységet, és töltsön fel egy médiafájlt az eszközre.
2. A következő JSON-beállításkészletet tartalmazó konfigurációs fájl alapján hozzon létre egy indexelési feladattal rendelkező feladatot:

    ```json
            {
            "version":"1.0",
            "Features":
                [
                {
                "Options": {
                        "Formats":["WebVtt","ttml"],
                        "Language":"enUs",
                        "Type":"RecoOptions"
                },
                "Type":"SpReco"
                }]
            }
    ```
    
3. Töltse le a kimeneti fájlokat. 
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint. 

#### <a name="example"></a>Példa

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;
using System.Threading.Tasks;

namespace IndexContent
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

            // Run indexing job.
            var asset = RunIndexingJob(@"C:\supportFiles\Indexer\BigBuckBunny.mp4",
                                        @"C:\supportFiles\Indexer\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\Indexer\Output");
        }

        static IAsset RunIndexingJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Indexing Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Indexing Job");

            // Get a reference to Azure Media Indexer 2 Preview.
            string MediaProcessorName = "Azure Media Indexer 2 Preview";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Indexing Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset to be indexed.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Indexing Output Asset", AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);

            progressJobTask.Wait();

            // If job state is Error, the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                ErrorDetail error = job.Tasks.First().ErrorDetails.First();
                Console.WriteLine(string.Format("Error: {0}. {1}",
                                                error.Code,
                                                error.Message));
                return null;
            }

            return job.OutputMediaAssets[0];
        }

        static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
        {
            IAsset asset = _context.Assets.Create(assetName, options);

            var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
            assetFile.Upload(filePath);

            return asset;
        }

        static void DownloadAsset(IAsset asset, string outputDirectory)
        {
            foreach (IAssetFile file in asset.AssetFiles)
            {
                file.Download(Path.Combine(outputDirectory, file.Name));
            }
        }

        static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors
                .Where(p => p.Name == mediaProcessorName)
                .ToList()
                .OrderBy(p => new Version(p.Version))
                .LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor",
                                                           mediaProcessorName));

            return processor;
        }

        static private void StateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);

            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished.");
                    Console.WriteLine();
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
                    // LogJobStop(job.Id);
                    break;
                default:
                    break;
            }
        }
    }
}
```

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

[Azure Media Analytics bemutatók](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

