---
title: Egy videó-összefoglalás létrehozásához használja az Azure Media Videóminiatűrök |} A Microsoft Docs
description: Videó-összefoglalás segíthet a hosszú videókról összefoglalókat készíthet az a forrás videó érdekes kódrészletek automatikusan kiválasztásával. Ez akkor hasznos, ha lehetővé szeretné tenni mire számítson hosszú videó gyors áttekintést.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/08/2019
ms.author: milanga;juliako;
ms.openlocfilehash: c0a6feb1eba1e409c29a650741eadc31f1017342
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004808"
---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Az Azure Media Videóminiatűrök használatával hozzon létre egy videó-összefoglalás  
## <a name="overview"></a>Áttekintés
A **Azure Media Videóminiatűrök** médiafeldolgozót. (pont) lehetővé teszi, hogy hozzon létre egy videót, amely hasznos a felhasználóknak, akik csak az előzetes verzióra hosszú videó összegzését összegzését. Ha például ügyfeleknek érdemes egy rövid "összefoglaló videó" Lásd: amikor rámutatnak miniatűr. Által paramétereinek finomhangolása **Azure Media Videóindexképek** keresztül egy konfigurációs készletet használhatja a felügyeleti csomag hatékony képernyőkép észleléséhez és az összefűzés technológiát leíró részklip algorithmically létrehozásához.  

A **Azure Media Video miniatűr** felügyeleti csomag jelenleg előzetes verzióban érhető el.

Ez a cikk kapcsolatos részleteket nyújt **Azure Media Video miniatűr** , és bemutatja, hogyan használja a Media Services SDK a .NET-hez.

## <a name="limitations"></a>Korlátozások

Bizonyos esetekben a videóhoz nem áll, különböző jelenetek, ha a kimenet csak akkor egyetlen jóváhagyást.

## <a name="video-summary-example"></a>Videó összefoglaló példa
Íme néhány példa az Azure Media Videóindexképek médiafeldolgozót teendők:

### <a name="original-video"></a>Eredeti videó
[Eredeti videó](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>Miniatűr videó eredménye
[Miniatűr videó eredménye](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-preset"></a>A feladat konfigurációs (előre)
A miniatűr videó feladat létrehozásakor **Azure Media Videóminiatűrök**, meg kell adnia egy konfigurációs készletet. A fenti miniatűr mintát a következő alapszintű JSON-konfigurációval lett létrehozva:

```json
    {
        "version":"1.0"
    }
```

Jelenleg a következő paraméterek módosíthatja:

| Param | Leírás |
| --- | --- |
| outputAudio |Itt adhatja meg, e eredő videó bármely hangot tartalmaz. <br/>Engedélyezett értékek a következők: IGAZ vagy hamis. Alapértelmezett érték az igaz. |
| fadeInFadeOut |Meghatározza a különálló mozgásban lévő adatoknak egyaránt miniatűrök között használt e fade értékre vált.  <br/>Engedélyezett értékek a következők: IGAZ vagy hamis.  Alapértelmezett érték az igaz. |
| maxMotionThumbnailDurationInSecs |Egész szám, amely meghatározza, hogy mennyi ideig kell lennie a teljes eredő videó.  Alapértelmezett eredeti videó időtartama függ. |

A következő táblázat ismerteti az alapértelmezett időtartamot, hogy amikor **maxMotionThumbnailInSecs** nincs használatban.

|  |  |  |
| --- | --- | --- | --- | --- |
| Videó időtartama |d < 3 perc |3 perccel < d < 15 perc |
| Miniatűr időtartama |15 másodperc (2-3 jelenetek) |30 másodperc (3-5 jelenetek) |

A következő JSON paramétereket állítja be.

```json
    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }
```

## <a name="net-sample-code"></a>.NET mintakód

A következő program mutat be, hogyan:

1. Hozzon létre egy objektumot, és a egy médiafájlt feltöltése az objektumba.
2. Létrehoz egy feladatot a következő json-készletet tartalmazó konfigurációs fájl alapján miniatűr videó feladatokkal: 
    
    ```json
            {                
                "version": "1.0",
                "options": {
                    "outputAudio": "true",
                    "maxMotionThumbnailDurationInSecs": "30",
                    "fadeInFadeOut": "false"
                }
            }
    ```

3. A kimeneti fájlokat tölti le. 

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

    namespace VideoSummarization
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


                // Run the thumbnail job.
                var asset = RunVideoThumbnailJob(@"C:\supportFiles\VideoThumbnail\BigBuckBunny.mp4",
                                            @"C:\supportFiles\VideoThumbnail\config.json");

                // Download the job output asset.
                DownloadAsset(asset, @"C:\supportFiles\VideoThumbnail\Output");
            }

            static IAsset RunVideoThumbnailJob(string inputMediaFilePath, string configurationFile)
            {
                // Create an asset and upload the input media file to storage.
                IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                    "My Video Thumbnail Input Asset",
                    AssetCreationOptions.None);

                // Declare a new job.
                IJob job = _context.Jobs.Create("My Video Thumbnail Job");

                // Get a reference to Azure Media Video Thumbnails.
                string MediaProcessorName = "Azure Media Video Thumbnails";

                var processor = GetLatestMediaProcessorByName(MediaProcessorName);

                // Read configuration from the specified file.
                string configuration = File.ReadAllText(configurationFile);

                // Create a task with the encoding details, using a string preset.
                ITask task = job.Tasks.AddNew("My Video Thumbnail Task",
                    processor,
                    configuration,
                    TaskOptions.None);

                // Specify the input asset.
                task.InputAssets.Add(asset);

                // Add an output asset to contain the results of the job.
                task.OutputAssets.AddNew("My Video Thumbnail Output Asset", AssetCreationOptions.None);

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

### <a name="video-thumbnail-output"></a>Miniatűr videó kimenet
[Miniatűr videó kimenet](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Az Azure Media Services analitikai funkcióinak áttekintése](media-services-analytics-overview.md)

[Az Azure Médiaelemzés bemutatók](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

