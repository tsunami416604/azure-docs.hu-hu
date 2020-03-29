---
title: Médiafájlok indexelése az Azure Media Indexer 2 előzetes verzióval | Microsoft dokumentumok
description: Az Azure Media Indexer lehetővé teszi, hogy a médiafájlok tartalmát kereshetővé tegye, és teljes szöveges átiratot hozzon létre a feliratozáshoz és a kulcsszavakhoz. Ez a témakör a Media Indexer 2 Preview használatát mutatja be.
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
ms.openlocfilehash: c24218dc116803ca0e0a1f166b7b54b24fc4d5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78163794"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Médiafájlok indexelése az Azure Media Indexer 2 előzetes verzióval

> [!NOTE]
> Az **Azure Media Indexer 2** médiaprocesszor kilesz vonva. A nyugdíjazási dátumokat lásd az [örökölt összetevők](legacy-components.md) témakörben. [Az Azure Media Services videoindexelője](https://docs.microsoft.com/azure/media-services/video-indexer/) lecseréli ezt az örökölt médiaprocesszort. További információ: Áttelepítés az [Azure Media Indexerről és az Azure Media Indexer 2-ről az Azure Media Services videoindexelőre](migrate-indexer-v1-v2.md)című témakörben.

Az **Azure Media Indexer 2 Előzetes** médiaprocesszor (MP) lehetővé teszi a médiafájlok és tartalmak kereshetővé tévául, valamint a feliratozási sávok létrehozásának. Az Azure Media [Indexer](media-services-index-content.md)korábbi verziójához képest az **Azure Media Indexer 2 Preview** gyorsabb indexelést hajt végre, és szélesebb nyelvi támogatást nyújt. A támogatott nyelvek közé tartozik az angol, a spanyol, a francia, a német, az olasz, a kínai (mandarin, az egyszerűsített), a portugál, az arab, az orosz és a japán.

Az **Azure Media Indexer 2 előzetes verzió mp** jelenleg előzetes verzióban.

Ez a cikk bemutatja, hogyan hozhat létre indexelési feladatokat az **Azure Media Indexer 2 Preview**segítségével.

## <a name="considerations"></a>Megfontolandó szempontok

A következő szempontokat kell figyelembe venni:
 
* Az Azure China 21Vianet és az Azure Government nem támogatja az Indexer 2-t.
* Tartalom indexelésekor ügyeljen arra, hogy nagyon tiszta beszéddel rendelkező médiafájlokat használjon (háttérzene, zaj, effektusok vagy mikrofonsziták nélkül). Néhány példa a megfelelő tartalomra: rögzített értekezletek, előadások vagy előadások. Előfordulhat, hogy a következő tartalom nem alkalmas indexelésre: filmek, tv-műsorok, vegyes hang- és hangeffektusokkal rendelkező tartalmak, rosszul rögzített, háttérzajú tartalom (szimás).
 
## <a name="input-and-output-files"></a>Bemeneti és kimeneti fájlok
### <a name="input-files"></a>Bemeneti fájlok
Hang- vagy videofájlok

### <a name="output-files"></a>Kimeneti fájlok
Az indexelési feladat a következő formátumokban hozhat létre feliratfájlokat:  

* **TTML**
* **WebVTT**

Az ilyen formátumú feliratfájlok segítségével a hang- és videofájlok hozzáférhetők lehetnek a hallássérült ek számára.

## <a name="task-configuration-preset"></a>Feladat konfigurációja (készlet)
Indexelési feladat létrehozásakor az **Azure Media Indexer 2 Preview,** meg kell adnia egy konfigurációs készlet.

A következő JSON beállítja az elérhető paramétereket.

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
Az Azure Media Indexer 2 Preview támogatja a következő nyelvek beszéd-szöveg szöveggé válását (a nyelv nevének megadásakor a feladatkonfigurációban a négykarakteres kódot az alábbi módon mutatja):

* Angol [Enus]
* Spanyol [ESEs]
* Kínai (mandarin, egyszerűsített) [ZhCn]
* Francia [FrFr]
* Német [Dede]
* Olasz [ItIt]
* Portugál [PtBr]
* Arab (egyiptomi) [ArEg]
* Japán [Jajp]
* Orosz [RuRu]
* Brit angol [EnGb]
* Spanyol (Mexikó) [EsMx] 

## <a name="supported-file-types"></a>Támogatott fájltípusok

A támogatott fájltípusokról a [támogatott kodekek/formátumok](media-services-media-encoder-standard-formats.md#input-containerfile-formats) című részben talál további információt.

## <a name="net-sample-code"></a>.NET mintakód

A következő program bemutatja, hogyan:

1. Hozzon létre egy eszközt, és töltsön fel egy médiafájlt az eszközbe.
2. Hozzon létre egy feladatot egy indexelő feladattal egy konfigurációs fájl alapján, amely a következő jsonkészletet tartalmazza:

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

Állítsa be a fejlesztői környezetet, és népesítse be az app.config fájlt a kapcsolatadataival, ahogy azt a Media Services fejlesztése a [.NET fájlban leírta.](media-services-dotnet-how-to-use.md) 

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

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Az Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

[Az Azure Media Analytics bemutatói](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

