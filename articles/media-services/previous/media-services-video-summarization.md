---
title: Videóösszegzés létrehozása az Azure Media videominiatűrök keléseihez | Microsoft dokumentumok
description: A videóösszegzés segítségével a forrásvideóból automatikusan érdekes kódrészleteket választhat ki a hosszú videók összegzéséhez. Ez akkor hasznos, ha gyors áttekintést szeretne adni arról, hogy mire számíthat egy hosszú videóban.
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
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: a79e718c04f81b1552d63ab98b6dcd6bb428fb50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918314"
---
# <a name="use-azure-media-video-thumbnails-to-create-a-video-summarization"></a>Videóösszegzés létrehozása az Azure Media videominiatűrök használatával  

> [!NOTE]
> Az **Azure Media Video Thumbnails** médiaprocesszor kilesz vonva. A kivonási dátum, lásd a [régi összetevők](legacy-components.md) témakörben.

## <a name="overview"></a>Áttekintés

Az **Azure Media Video Thumbnails** médiafeldolgozó (MP) lehetővé teszi, hogy egy videó összegzését hozza létre, amely hasznos az ügyfelek számára, akik csak egy hosszú videó összegzését szeretnék megtekinteni. Előfordulhat például, hogy az ügyfelek egy rövid "összefoglaló videót" szeretnének látni, amikor egy miniatűr fölé viszik az egérmutatót. Az **Azure Media Video Thumbnails** paramétereinek egy konfigurációs készleten keresztültörténő módosításával a rendszerrendelkező nagy teljesítményű felvételészlelési és összefűzési technológiájával algoritmikusan létrehozhat egy leíró alklipet.  

Az **Azure Media Video Miniatűr** MP jelenleg előzetes verzióban van.

Ez a cikk az **Azure Media Video miniatűrjét** ismerteti, és bemutatja, hogyan használható a Media Services SDK-val .

## <a name="limitations"></a>Korlátozások

Bizonyos esetekben, ha a videó dúl különböző jelenetekből, a kimenet csak egy lövés lesz.

## <a name="video-summary-example"></a>Példa videó összegzése
Íme néhány példa arra, hogy az Azure Media Video Thumbnails médiaprocesszormit tehet:

### <a name="original-video"></a>Eredeti videó
[Eredeti videó](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Faed33834-ec2d-4788-88b5-a4505b3d032c%2FMicrosoft%27s%20HoloLens%20Live%20Demonstration.ism%2Fmanifest)

### <a name="video-thumbnail-result"></a>A videó miniatűrje
[A videó miniatűrje](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Ff5c91052-4232-41d4-b531-062e07b6a9ae%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="task-configuration-preset"></a>Feladat konfigurációja (készlet)
Amikor az **Azure Media Video Thumbnails**segítségével videobélyegkép-készítési feladatot hoz létre, meg kell adnia egy konfigurációs készletet. A fenti miniatűr minta a következő alapvető JSON-konfigurációval jött létre:

```json
    {
        "version":"1.0"
    }
```

Jelenleg a következő paraméterek módosíthatók:

| Param | Leírás |
| --- | --- |
| kimenetAudio |Itt adható meg, hogy az eredményül bevezető videó tartalmaz-e hangot. <br/>Az engedélyezett értékek a következők: Igaz vagy Hamis. Az alapértelmezett érték igaz. |
| fadeInFadeOut |Itt adható meg, hogy a különböző mozgóbélyegképek között a kószaátmenetek legyenek-e használatban.  <br/>Az engedélyezett értékek a következők: Igaz vagy Hamis.  Az alapértelmezett érték igaz. |
| maxMotionThumbnailDurationInSecs |Egész szám, amely meghatározza, hogy a teljes eredményül szerzett videó mennyi ideig legyen.  Az alapértelmezett érték az eredeti videó időtartamától függ. |

Az alábbi táblázat az alapértelmezett időtartamot ismerteti, ha a **maxMotionThumbnailInSecs** nincs használatban.

|  |  |  |
| --- | --- | --- |
| Videó időtartama |d < 3 min |3 min < d < 15 min |
| Miniatűr időtartama |15 másodperc (2-3 jelenet) |30 másodperc (3-5 jelenet) |

A következő JSON beállítja az elérhető paramétereket.

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

A következő program bemutatja, hogyan:

1. Hozzon létre egy eszközt, és töltsön fel egy médiafájlt az eszközbe.
2. A következő jsonkészletet tartalmazó konfigurációs fájlon alapuló videominiatűr-feladattal rendelkező feladatot hoz létre: 
    
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

3. Letölti a kimeneti fájlokat. 

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

### <a name="video-thumbnail-output"></a>Videó miniatűr kimenete
[Videó miniatűr kimenete](https://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Fnimbuscdn-nimbuspm.streaming.mediaservices.windows.net%2Fd06f24dc-bc81-488e-a8d0-348b7dc41b56%2FHololens%2520Demo_VideoThumbnails_MotionThumbnail.mp4)

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Az Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

[Az Azure Media Analytics bemutatói](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

