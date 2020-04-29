---
title: Az arc és az érzelem észlelése Azure Media Analyticssal | Microsoft Docs
description: Ez a témakör bemutatja, hogyan észlelhetők az arcok és az érzelmek a Azure Media Analytics.
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
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: 2d746167f993438e5fce467365844df2078c08a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77919311"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Az arc és az érzelem észlelése Azure Media Analytics

> [!NOTE]
> A **Azure Media Face Detector** adathordozó-processzor ki lesz vonva. A lejárati dátumért tekintse meg az [örökölt összetevőkkel](legacy-components.md) foglalkozó témakört.

## <a name="overview"></a>Áttekintés

A **Azure Media Face Detector** Media Processor (mp) lehetővé teszi a mozgások megszámlálását, nyomon követését, valamint az arc-kifejezéseken keresztüli közönség részvételének és reagálásának mérését. Ez a szolgáltatás két funkciót tartalmaz: 

* **Arcfelismerés**
  
    A Arcfelismerés megkeresi és nyomon követi az emberi arcokat a videón belül. Több arc is észlelhető, és ezt követően nyomon követhető, ahogy mozognak, a JSON-fájlban visszaadott idő és hely metaadataival. A nyomon követés során megkísérli egy egységes azonosítót adni ugyanahhoz az oldalhoz, amíg a személy a képernyőn mozog, még akkor is, ha azok el vannak akadályozva, vagy röviden elhagyják a keretet.
  
  > [!NOTE]
  > Ez a szolgáltatás nem végez Arcfelismerés-felismerést. Az a személy, aki elhagyja a keretet, vagy túl sokáig akadályozni fog, új azonosítót kap, amikor visszatérnek.
  > 
  > 
* **Érzelem észlelése**
  
    Az érzelem észlelése a Arcfelismerés adathordozó-feldolgozó egyik választható összetevője, amely az észlelt arcok több érzelmi attribútumának elemzését adja vissza, beleértve a boldogságot, a szomorúságot, a félelmet és a düh, valamint egyebeket. 

A **Azure Media Face Detector** mp jelenleg előzetes verzióban érhető el.

Ez a cikk részletesen ismerteti a **Azure Media Face Detector** , és bemutatja, hogyan használható a Media Services SDK for .net szolgáltatással.

## <a name="face-detector-input-files"></a>Szembenéző detektor bemeneti fájljai
Videofájlok. Jelenleg a következő formátumok támogatottak: MP4, MOV és WMV.

## <a name="face-detector-output-files"></a>Szembenéző detektor kimeneti fájljai
A arcfelismerés és követési API nagy pontosságú arcfelismerés és-követést biztosít, amely akár 64 emberi arcot is képes észlelni egy videóban. Az elülső arcok biztosítják a legjobb eredményeket, míg az arcok és a kisméretű arcok (24x24 képpontnál kisebb vagy egyenlő) nem feltétlenül pontosak.

Az észlelt és követett arcok a koordinátákkal (bal, felső, szélesség és magasság) jelzik a képpontokban lévő arcok helyét, valamint az adott személy nyomon követését jelző Arcfelismerés-számot. A Face ID-számok olyan körülmények között állnak alaphelyzetbe, amikor az elülső arc elveszett vagy átfedésben van a keretben, így egyes személyek több azonosítót kapnak.

## <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>A kimeneti JSON-fájl elemei

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

A Face detektor a töredezettségi technikákat használja (ahol a metaadatok az időalapú adattömbökben bonthatók fel, és csak a szükséges adatok tölthetők le) és szegmentálás (ahol az események túl nagy méretűek lesznek). Néhány egyszerű számítás segítségére lehet az adatok átalakításában. Ha például egy esemény a 6300-as (kullancsok) időpontban kezdődött, és a 2997 (óra/s) és a (z) 29,97 frameráta (Frames/s), akkor:

* kezdési időpont/időskála = 2,1 másodperc
* Másodperc x frameráta = 63 keret

## <a name="face-detection-input-and-output-example"></a>Arcfelismerés bemeneti és kimeneti példája
### <a name="input-video"></a>Bemeneti videó
[Bemeneti videó](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Feladat konfigurációja (előre beállított)
Ha **Azure Media Face Detectorkal**rendelkező feladatot hoz létre, meg kell adnia egy konfigurációs beállításkészletet. A következő konfigurációs beállításkészlet csak a Arcfelismerés esetében használható.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Attribútumok leírása
| Attribútum neve | Leírás |
| --- | --- |
| Mód |Gyors gyors feldolgozási sebesség, de kevésbé pontos (alapértelmezett).|

### <a name="json-output"></a>JSON-kimenet
A JSON-kimenet következő példája csonkolt.

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


## <a name="emotion-detection-input-and-output-example"></a>Érzelem-észlelés bemeneti és kimeneti példája
### <a name="input-video"></a>Bemeneti videó
[Bemeneti videó](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Feladat konfigurációja (előre beállított)
Ha **Azure Media Face Detectorkal**rendelkező feladatot hoz létre, meg kell adnia egy konfigurációs beállításkészletet. A következő konfigurációs beállításkészlet azt adja meg, hogy az érzelem észlelése alapján hozzon létre JSON-t.

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


#### <a name="attribute-descriptions"></a>Attribútumok leírása
| Attribútum neve | Leírás |
| --- | --- |
| Mód |Arcok: csak Arcfelismerés.<br/>PerFaceEmotion: az érzelmeket egymástól függetlenül visszaküldi az egyes Arcfelismerés.<br/>AggregateEmotion: a keretben lévő összes arc átlagos érzelem-értékének visszaadása. |
| AggregateEmotionWindowMs |Akkor használja, ha a AggregateEmotion mód van kiválasztva. Megadja az egyes összesített eredmények előállításához használt videó hosszát ezredmásodpercben. |
| AggregateEmotionIntervalMs |Akkor használja, ha a AggregateEmotion mód van kiválasztva. Meghatározza, hogy milyen gyakorisággal hozhatók létre összesített eredmények. |

#### <a name="aggregate-defaults"></a>Összesített alapértékek
Az alábbi értékek az összesítő ablak és az intervallum beállításainak ajánlott értékei. A AggregateEmotionWindowMs hosszabbnak kell lennie, mint AggregateEmotionIntervalMs.

|| Alapértelmezett érték (ek) | Maximum (ok) | Minimum (ok) |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0,5 |2 |0,25|
| AggregateEmotionIntervalMs |0,5 |1 |0,25|

### <a name="json-output"></a>JSON-kimenet
Az összesített érzelem JSON-kimenete (csonkolt):

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
* A támogatott bemeneti videoformátum például az MP4, a MOV és a WMV.
* A észlelhető arc méretének 24x24 a 2048x2048 képpont értékre kell esnie. A tartományon kívüli arcok nem észlelhetők.
* Minden videó esetében a visszaadott arcok maximális száma 64.
* Előfordulhat, hogy a technikai kihívások miatt egyes arcok nem észlelhetők. ilyenek például a nagyon nagy látószögek (Head-póz) és a nagy elzáródás. Az elülső és a közel-elülső arcok a legjobb eredmények.

## <a name="net-sample-code"></a>.NET-mintakód

A következő program a következőket mutatja be:

1. Hozzon létre egy adategységet, és töltsön fel egy médiafájlt az eszközre.
2. Hozzon létre egy Arcfelismerés feladattal rendelkező feladatot egy olyan konfigurációs fájl alapján, amely a következő JSON-beállításkészletet tartalmazza: 

    ```json
            {
                "version": "1.0"
            }
    ```
3. Töltse le a kimeneti JSON-fájlokat. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel az app. config fájlt a következő témakörben ismertetett módon: [Media Services fejlesztés a .net](media-services-dotnet-how-to-use.md)-tel. 

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

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

[Azure Media Analytics bemutatók](https://amslabs.azurewebsites.net/demos/Analytics.html)

