---
title: Arcfelismerés és az Azure Media Analytics Érzelemfelismerési észleléséhez |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan észleli az arcokat és érzelmeket azonosíthat az Azure Media Analytics.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/10/2019
ms.author: milanga;juliako;
ms.openlocfilehash: fa51290623f68e94db9cb1b28cd69c88b5d5cf18
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56000064"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Arcfelismerés és az Azure Media Analytics Érzelemfelismerési észlelése
## <a name="overview"></a>Áttekintés
A **Azure Media Face detector használatával** médiafeldolgozót. (pont) lehetővé teszi a száma, nyomon követheti a áthelyezések száma – és még mérőműszer célközönség való részvétel és reagálás arckifejezések keresztül. Ez a szolgáltatás két funkciókat tartalmazza: 

* **Arcfelismerés**
  
    Arcfelismerés megkeresi és nyomon követi a videó emberi arcokat. Több arcok észlelése, és ezt követően a visszaadott JSON-fájlban időt és tartózkodási helyet metaadatokkal mozgás körül, követni. Során nyomon követése, megkísérli a konzisztens ID adhat az azonos face, bár a személy van Navigálás a képernyőn, még akkor is, ha azok kényszerítő, vagy röviden hagyja a keret.
  
  > [!NOTE]
  > Ez a szolgáltatás nem végez arcfelismerési. Olyan személy, akinek elhagyja a keretet, vagy a válik fedhetik túl sokáig kap egy új azonosító Ha vissza.
  > 
  > 
* **Érzelemfelismerő**
  
    Érzelemfelismerő adja vissza elemzési több érzelmi attribútumok az arcok észlelése esetén például boldogság, szomorúság, félelem, düh, és egyéb arcok észlelése médiafeldolgozó választható összetevője. 

A **Azure Media Face detector használatával** felügyeleti csomag jelenleg előzetes verzióban érhető el.

Ez a cikk kapcsolatos részleteket nyújt **Azure Media Face detector használatával** , és bemutatja, hogyan használja a Media Services SDK a .NET-hez.

## <a name="face-detector-input-files"></a>A Face detector használatával bemeneti fájlok
Videofájlok. Jelenleg a következő formátumok támogatottak: MP4 MOV és WMV.

## <a name="face-detector-output-files"></a>A Face detector használatával kimeneti fájlok
Észlelés és követési face API biztosít a nagy pontosságú arcfelismeréssel és követés segítségével, amely egy videóban akár 64 emberi arcokat észleli. Elülső arcok, adja meg a legjobb eredmények elérése érdekében, ügyféloldali arcok közben, és kis arcok (kisebb vagy egyenlő 24 x 24 képpont) előfordulhat, hogy nem olyan pontos.

Az észlelt, és a nyomon követett arcok visszaadott koordinátákkal megadott (balra, felső, szélesség és magasság) jelző arcok a képen képpont, valamint a nyomon követheti, hogy egyes jelző face ID szám helyét. Face ID számok során gyakran fordul elő alaphelyzetbe körülmények között, ha a elülső face elvesztése vagy a keretbe átfedett bizonyos felhasználók több ID első hozzárendelt eredményez.

## <a id="output_elements"></a>A kimenet JSON-fájl elemeinek

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Arcérzékelő töredezettsége (ahol a metaadatok időalapú tömbökben is osztható fel és letöltheti, csak a szükséges), és a szegmentálási módszereket használ a (ahol az események vannak osztva abban az esetben, ha túl nagy kapnak). Néhány egyszerű számítás segítségére lehet az adatok átalakításában. Például, ha egy esemény lépései 6300 (órajelben), egy időskálára 2997 (órajel során végbemenő/másodperc), és képkockasebesség, majd 29,97 (keretek/másodperc):

* kezdési időpont/időskála = 2,1 másodperc
* Másodperc x Framerate 63 keretek =

## <a name="face-detection-input-and-output-example"></a>Face észlelési bemeneti és kimeneti példa
### <a name="input-video"></a>A bemeneti videó
[A bemeneti videó](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>A feladat konfigurációs (előre)
A feladat létrehozásakor **Azure Media Face detector használatával**, meg kell adnia egy konfigurációs készletet. A következő konfigurációs beállítás van, és az arcfelismerés is használható.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Attribútum leírása
| Attribútum neve | Leírás |
| --- | --- |
| Mód |Gyors - feldolgozó gyors sebességét, de kevésbé pontos (alapértelmezett).|

### <a name="json-output"></a>JSON-kimenet
Az alábbi példa JSON-kimenet csonkolva lettek.

```json
    {
    "version": 1,
    "timescale": 30000,
    "offset": 0,
    "framerate": 29.97,
    "width": 1280,
    "height": 720,
    "fragments": [
        {
        "start": 0,
        "duration": 60060
        },
        {
        "start": 60060,
        "duration": 60060,
        "interval": 1001,
        "events": [
            [
            {
                "id": 0,
                "x": 0.519531,
                "y": 0.180556,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517969,
                "y": 0.181944,
                "width": 0.0867188,
                "height": 0.154167
            }
            ],
            [
            {
                "id": 0,
                "x": 0.517187,
                "y": 0.183333,
                "width": 0.0851562,
                "height": 0.151389
            }
            ],
```


## <a name="emotion-detection-input-and-output-example"></a>Érzelemfelismerő bemeneti és kimeneti példa
### <a name="input-video"></a>A bemeneti videó
[A bemeneti videó](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>A feladat konfigurációs (előre)
A feladat létrehozásakor **Azure Media Face detector használatával**, meg kell adnia egy konfigurációs készletet. A következő konfigurációs beállítás megadja, hogy hozzon létre JSON a érzelemfelismerő alapján.

```json
    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }
```


#### <a name="attribute-descriptions"></a>Attribútum leírása
| Attribútum neve | Leírás |
| --- | --- |
| Mód |Arcok: Csak arcfelismerés.<br/>PerFaceEmotion: Érzelemfelismerési egymástól függetlenül az egyes arcfelismerés visszaadása.<br/>AggregateEmotion: Keret összes arcok átlagos érzelemfelismerési értékeit visszaadása. |
| AggregateEmotionWindowMs |Akkor használja, ha AggregateEmotion üzemmódot választja. A videó minden összesített eredmény, ezredmásodpercben létrehozásához használt hosszát adja meg. |
| AggregateEmotionIntervalMs |Akkor használja, ha AggregateEmotion üzemmódot választja. Itt adhatja meg, milyen gyakorisággal el összesített eredményt. |

#### <a name="aggregate-defaults"></a>Összesített alapértelmezései
Alább ajánlottak az összesített ablakot, és időköz beállítások értékeit. AggregateEmotionWindowMs AggregateEmotionIntervalMs hosszabb lehet.

|| Alapértelmezett érték (s) | Max(s) | Percben |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |0.25|
| AggregateEmotionIntervalMs |0,5 |1 |0.25|

### <a name="json-output"></a>JSON-kimenet
JSON-kimenetet az összesített érzelemfelismerési (csonkolt):

```json
    {
     "version": 1,
     "timescale": 30000,
     "offset": 0,
     "framerate": 29.97,
     "width": 1280,
     "height": 720,
     "fragments": [
       {
         "start": 0,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               }
             }
           ]
         ]
       },
       {
         "start": 60060,
         "duration": 60060,
         "interval": 15015,
         "events": [
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
                 "contempt": 0
               },
               "windowMeanScores": {
                 "neutral": 0.688541,
                 "happiness": 0.0586323,
                 "surprise": 0.227184,
                 "sadness": 0.00945675,
                 "anger": 0.00592107,
                 "disgust": 0.00154993,
                 "fear": 0.00450447,
                 "contempt": 0.0042109
               }
             }
           ],
           [
             {
               "windowFaceDistribution": {
                 "neutral": 1,
                 "happiness": 0,
                 "surprise": 0,
                 "sadness": 0,
                 "anger": 0,
                 "disgust": 0,
                 "fear": 0,
```

## <a name="limitations"></a>Korlátozások
* A támogatott bemeneti videóformátumok MP4 MOV és WMV tartalmazza.
* Az észlelhető oldallal mérete tartománya 24 x 24, 2048 x 2048 képpont. Az engedélyezett tartományon kívül esik az arcok észlelése nem történik meg.
* Minden videó visszaadott arcok legfeljebb 64.
* Néhány arcok technikai problémák miatt nem észlelhető például nagyon nagy méretű face szög (head-testtartás), és nagy hangelnyelés. Elülső és a közel-elülső arcok rendelkezik a legjobb eredmények elérése érdekében.

## <a name="net-sample-code"></a>.NET mintakód

A következő program mutat be, hogyan:

1. Hozzon létre egy objektumot, és a egy médiafájlt feltöltése az objektumba.
2. Hozzon létre egy feladatot a következő json-készletet tartalmazó konfigurációs fájl alapján arcok észlelése feladatokkal: 

    ```json
            {
                "version": "1.0"
            }
    ```
3. Töltse le a kimeneti JSON-fájlokat. 

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

namespace FaceDetection
{
    class Program
    {
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

            // Run the FaceDetection job.
            var asset = RunFaceDetectionJob(@"C:\supportFiles\FaceDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\FaceDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceDetection\Output");
        }

        static IAsset RunFaceDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Face Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Detection Job");

            // Get a reference to Azure Media Face Detector.
            string MediaProcessorName = "Azure Media Face Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Detection Output Asset", AssetCreationOptions.None);

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

[Az Azure Médiaelemzés bemutatók](http://amslabs.azurewebsites.net/demos/Analytics.html)

