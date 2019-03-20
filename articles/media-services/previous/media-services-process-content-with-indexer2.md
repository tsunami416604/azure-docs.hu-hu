---
title: Médiafájlokat az Azure Media Indexer 2 előzetes verzió |} A Microsoft Docs
description: Az Azure Media Indexer lehetővé teszi a médiafájlok tartalom kereshetővé és a egy lezárt a feliratozás és a kulcsszavak teljes szövegű átiratot létrehozása érdekében. Ez a témakör bemutatja, hogyan használja a Media Indexer 2 megtekintése.
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
ms.date: 02/10/2019
ms.author: adsolank;juliako;
ms.openlocfilehash: 12d188b39375bce88d3adc244d217854c75f436d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861288"
---
# <a name="indexing-media-files-with-azure-media-indexer-2-preview"></a>Médiafájlokat az Azure Media Indexer 2 előzetes verzió
## <a name="overview"></a>Áttekintés
A **Azure Media Indexer 2 előzetes** médiafeldolgozót. (pont) lehetővé teszi, hogy az adathordozó-fájlok és tartalmak kereshetővé, valamint a készítése a lezárt feliratozási nyomon követi. Az előző verziójú képest [Azure Media Indexer](media-services-index-content.md), **Azure Media Indexer 2 – előzetes** végzi a gyorsabb indexelés és a szélesebb körű támogatást biztosít. Támogatott nyelvek angol, spanyol, francia, német, olasz, kínai (Mandarin, egyszerűsített), portugál, arab, spanyol és japán.

A **Azure Media Indexer 2 előzetes** felügyeleti csomag jelenleg előzetes verzióban érhető el.

Ez a cikk bemutatja, hogyan hozhat létre az indexelési feladatok **Azure Media Indexer 2 előzetes**.

> [!NOTE]
> A következő szempontokat kell figyelembe venni:
> 
> Azure China és az Azure Government az Indexer 2 nem támogatott.
> 
> Amikor a tartalom indexelése, ügyeljen arra, hogy egyértelmű speech (nélkül háttér zene, zaj, hatások vagy mikrofon hiss) ellátott adathordozó-fájlok használata. Néhány példa a megfelelő tartalom: értekezletek, előadások vagy bemutatók rögzíti. A következő tartalmat nem lehet megfelelő, az indexelés: filmek, tévéműsorok karaktereket a vegyes hang és a hatást, rosszul rögzíti a tartalmat, a háttérzaj (hiss).
> 
> 

Ez a cikk kapcsolatos részleteket nyújt **Azure Media Indexer 2 előzetes** , és bemutatja, hogyan használja a Media Services SDK for .NET

## <a name="input-and-output-files"></a>Bemeneti és kimeneti fájlok
### <a name="input-files"></a>Bemeneti fájlok
Hang- vagy fájlok

### <a name="output-files"></a>Kimeneti fájlok
Egy indexelési feladat feliratfájlok hozhat létre a következő formátumban:  

* **SAMI**
* **TTML**
* **WebVTT**

Ezek a formátumok a fájlok használhatók elérhetővé a hang- és videofájlok, a hallás fogyatékkal személyek (CC) felirat lezárva.

## <a name="task-configuration-preset"></a>A feladat konfigurációs (előre)
A feladat létrehozása egy indexelő **Azure Media Indexer 2 előzetes**, meg kell adnia egy konfigurációs készletet.

A következő JSON paramétereket állítja be.

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
Az Azure Media Indexer 2 előzetes hang-szöveg transzformációs (amikor a nyelv nevének megadása a feladat a konfigurációban zárójelben használata 4 karakterből álló kódot alább látható módon) a következő nyelveket támogatja:

* Angol nyelvű [EnUs]
* Spanyol [EsEs]
* Kínai (Mandarin, egyszerűsített) [ZhCn]
* Francia [FrFr]
* Német [DeDe]
* Olasz [ItIt]
* Portugál [PtBr]
* Arab (egyiptomi) [ArEg]
* Japán [JaJp]
* Orosz [RuRu]
* Brit angol [EnGb]
* Mexikói spanyol [EsMx] 

## <a name="supported-file-types"></a>Támogatott fájltípusok

Támogatott fájlok típusaival kapcsolatos információkért lásd: a [kodekek/formátum támogatott](media-services-media-encoder-standard-formats.md#input-containerfile-formats) szakaszban.

## <a name="net-sample-code"></a>.NET mintakód

A következő program mutat be, hogyan:

1. Hozzon létre egy objektumot, és a egy médiafájlt feltöltése az objektumba.
2. Hozzon létre egy feladatot egy indexelési feladat a következő json-készletet tartalmazó konfigurációs fájl alapján:

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
[Az Azure Media Services analitikai funkcióinak áttekintése](media-services-analytics-overview.md)

[Az Azure Médiaelemzés bemutatók](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

