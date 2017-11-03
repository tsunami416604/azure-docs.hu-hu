---
title: "Egy videó összegzésének létrehozása az Azure Media videó miniatűrök használatával |} Microsoft Docs"
description: "Videóösszegzés segítségével létre videók kijelölésével automatikusan érdekes kódtöredékek a forrás-videót. Ez akkor hasznos, ha lehetővé szeretné tenni, mi történik, a hosszú videó gyors áttekintést."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/18/2017
ms.author: milanga;juliako;
ms.openlocfilehash: 5d5afdaf22ffea8f3b77a154acb5d0a8dda74405
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Az Azure Media videó miniatűrök segítségével egy videó összegzésének létrehozása
## <a name="overview"></a>Áttekintés
A **Azure Media videó miniatűrök** media processzor (MP) hozhat létre, amely hasznos a felhasználóknak, akik csak az előzetes hosszú videó összegzését videó összegzését. Például az ügyfelek hogy szeretnénk egy rövid "összefoglaló videó" Ha azok rámutat egy miniatűr. Által paramétereinek tökéletesítse **Azure Media Videoindexképek** keresztül konfigurációs készlet, használhatja a felügyeleti csomag hatékony hibaüzenetet felderítését és a kapott technológia leíró subclip algorithmically létrehozásához.  

A **Azure Media videó miniatűr** felügyeleti csomag jelenleg előzetes verzió.

Ez a témakör kapcsolatos részleteket nyújt **Azure Media videó miniatűr** és a .NET-keretrendszerhez készült Media Services SDK-val való használatát ismerteti.

## <a name="limitations"></a>Korlátozások

Néhány esetben, ha a videó különböző színfalak nem áll a kimeneti csak akkor egyetlen hibaüzenetet.

## <a name="video-summary-example"></a>Videó összefoglaló – példa
Íme néhány példa arra, mi az Azure Media Videoindexképek media processzor teheti:

### <a name="original-video"></a>Eredeti videó
[Eredeti videó](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>Videó miniatűr eredménye
[Videó miniatűr eredménye](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-preset"></a>A feladat konfigurációja (beállítás)
A videó miniatűr feladat létrehozásakor **Azure Media videó miniatűrök**, meg kell adnia egy konfigurációs készletet. A fenti miniatűr minta a következő alapkonfiguráció JSON hozták létre:

    {"version":"1.0"}

Jelenleg módosíthatja a következő paraméterekkel:

| Param | Leírás |
| --- | --- |
| outputAudio |Itt adhatja meg, függetlenül attól, az eredményül kapott videó tartalmaz hangot. <br/>Engedélyezett értékek: True vagy False. Alapértelmezett érték a True. |
| fadeInFadeOut |Meghatározza a különálló mozgásérzékelési miniatűrök között használt-e halványítási átmenetek.  <br/>Engedélyezett értékek: True vagy False.  Alapértelmezett érték a True. |
| maxMotionThumbnailDurationInSecs |Egész szám, amely meghatározza, hogy mennyi ideig kell a teljes eredő videó.  Alapértelmezett eredeti videó időtartama függ. |

A következő táblázat ismerteti az alapértelmezett időtartamot, amikor **maxMotionThumbnailInSecs** nem használatos.

|  |  |  |
| --- | --- | --- | --- | --- |
| Videó időtartama |d < 3 perc |3 perc < d < 15 perc |
| A miniatűr időtartama |15 másodperc (2-3 színfalak) |30 másodperc (3-5 színfalak) |

A következő JSON azokat a rendelkezésre álló paramétereket állítja be.

    {
        "version": "1.0",
        "options": {
            "outputAudio": "true",
            "maxMotionThumbnailDurationInSecs": "10",
            "fadeInFadeOut": "true"
        }
    }

## <a name="net-sample-code"></a>.NET mintakód

A következő program bemutatja hogyan:

1. Hozzon létre egy eszközt, és adathordozó-fájl feltöltése az objektumba.
2. Létrehoz egy feladatot a következő json-készletet tartalmazó konfigurációs fájl alapján videó miniatűr feladatokkal. 
   
        {                
            "version": "1.0",
            "options": {
                "outputAudio": "true",
                "maxMotionThumbnailDurationInSecs": "30",
                "fadeInFadeOut": "false"
            }
        }
3. A kimeneti fájlokat tölti le. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint. 

#### <a name="example"></a>Példa

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

### <a name="video-thumbnail-output"></a>A miniatűr videokimenetéhez
[A miniatűr videokimenetéhez](http://ampdemo.azureedge.net/azuremediaplayer.html?url=http%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Azure Media Services elemző áttekintése](media-services-analytics-overview.md)

[Az Azure Médiaelemzés bemutatók](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

