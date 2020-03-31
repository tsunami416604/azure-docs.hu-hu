---
title: Arc és érzelem észlelése az Azure Media Analytics szolgáltatással | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan észlelheti az arcokat és az érzelmeket az Azure Media Analytics szolgáltatással.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919311"
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Arc és érzelem észlelése az Azure Media Analytics segítségével

> [!NOTE]
> Az **Azure Media Face Detector médiaprocesszor** a rendszer kilesz vonva. A kivonási dátum, lásd a [régi összetevők](legacy-components.md) témakörben.

## <a name="overview"></a>Áttekintés

Az **Azure Media Face Detector médiafeldolgozó** (MP) lehetővé teszi a számlálást, a mozgások nyomon követését, és akár a közönség részvételét és reakcióját is felmérheti arckifejezéseken keresztül. Ez a szolgáltatás két funkciót tartalmaz: 

* **Arcfelismerés**
  
    Az arcfelismerés emberi arcokat talál és követ nyomon egy videón belül. Több arc észlelhető, majd nyomon követhető, ahogy mozognak, és az idő- és helymetaadatok at egy JSON-fájlban adják vissza. A követés során megpróbálja egységes azonosítót adni ugyanannak az arcnak, miközben a személy a képernyőn mozog, még akkor is, ha akadályoztatva van, vagy rövid időre elhagyja a keretet.
  
  > [!NOTE]
  > Ez a szolgáltatás nem végez arcfelismerést. Az egyén, aki elhagyja a keretet, vagy lesz akadályozta túl sokáig kap egy új azonosítót, amikor visszatérnek.
  > 
  > 
* **Érzelemfelismerés**
  
    Emotion Detection egy opcionális eleme a Face Detection Media Processor, hogy visszatér elemzés több érzelmi attribútumok az arcok észlelt, beleértve a boldogság, szomorúság, félelem, harag, és így tovább. 

Az **Azure Media Face Detector** MP jelenleg előzetes verzióban van.

Ez a cikk az **Azure Media Face Detector** részleteit tartalmazza, és bemutatja, hogyan használható a Media Services SDK-val a .

## <a name="face-detector-input-files"></a>Arcdetektor beviteli fájljai
Videofájlok. Jelenleg a következő formátumok támogatottak: MP4, MOV és WMV.

## <a name="face-detector-output-files"></a>Arcdetektor kimeneti fájljai
Az arcfelismerési és -követési API nagy pontosságú archely-felismerést és -követést biztosít, amely akár 64 emberi arcot is képes észlelni egy videóban. Az elülső lapok biztosítják a legjobb eredményt, míg az oldallapok és a kis (legfeljebb 24x24 képpontméretű) lapok nem lehetnek olyan pontosak.

Az észlelt és nyomon követett lapok at koordinátákkal (balra, felül, szélesség és magasság) adják vissza, jelezve az arcok helyét a képen képpontban, valamint egy arcazonosító számot, amely jelzi az adott személy nyomon követését. Az arcazonosító-számok olyan körülmények között állíthatók alaphelyzetbe, amikor az elülső felület elvész vagy átfedi egymást a keretben, ami azt eredményezi, hogy egyes személyek több azonosítót kapnak.

## <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>A kimeneti JSON-fájl elemei

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

Az Arcdetektor a töredezettség technikáit használja (ahol a metaadatok időalapú adattömbökben bonthatók fel, és csak azt töltheti le, amire szüksége van), valamint a szegmentálást (ahol az események megszakadnak, ha túl nagyok lennének). Néhány egyszerű számítás segítségére lehet az adatok átalakításában. Ha például egy esemény 6300-nál (kullancsoknál) kezdődött, 2997 (ticks/sec) és 29,97-es (képkocka/mp) képkockasebességgel, akkor:

* kezdési időpont/időskála = 2,1 másodperc
* Másodperc x Framerate = 63 képkocka

## <a name="face-detection-input-and-output-example"></a>Példa arcfelismerő bemenetre és kimenetre
### <a name="input-video"></a>Videó bevitele
[Videó bevitele](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Feladat konfigurációja (készlet)
Amikor feladatot hoz létre az **Azure Media Face Detector**használatával, meg kell adnia egy konfigurációs készletet. A következő konfigurációs készlet csak az arcfelismeréshez.

```json
    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }
```

#### <a name="attribute-descriptions"></a>Attribútumleírások
| Attribútum neve | Leírás |
| --- | --- |
| Mód |Gyors - gyors feldolgozási sebesség, de kevésbé pontos (alapértelmezett).|

### <a name="json-output"></a>JSON kimenet
A következő példa a JSON kimenet csonkolva lett.

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


## <a name="emotion-detection-input-and-output-example"></a>Példa érzelemészlelési bemenetre és kimenetre
### <a name="input-video"></a>Videó bevitele
[Videó bevitele](https://ampdemo.azureedge.net/azuremediaplayer.html?url=httpss%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>Feladat konfigurációja (készlet)
Amikor feladatot hoz létre az **Azure Media Face Detector**használatával, meg kell adnia egy konfigurációs készletet. A következő konfigurációs készlet határozza meg a JSON létrehozását az érzelemészlelés alapján.

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


#### <a name="attribute-descriptions"></a>Attribútumleírások
| Attribútum neve | Leírás |
| --- | --- |
| Mód |Arcok: Csak arcfelismerés.<br/>PerFaceEmotion: Az egyes arcfelismerések független ülekét egymástól függetlenül adja vissza.<br/>AggregateEmotion: A képkocka összes arcának átlagos érzelemértékeit adja vissza. |
| Összesített emotionwindowms |Akkor használja, ha az AggregateEmotion mód van kiválasztva. Az egyes összesítő eredmény előállításához használt videó hosszát adja meg ezredmásodpercben. |
| Összesített emotionintervalms |Akkor használja, ha az AggregateEmotion mód van kiválasztva. Megadja, hogy milyen gyakorisággal hozza létre az összesített eredményeket. |

#### <a name="aggregate-defaults"></a>Összesített alapértelmezett értékek
Az alábbiakban az összesített ablak- és intervallumbeállítások ajánlott értékeit találjuk. Az AggregateEmotionWindowMs-nek hosszabbnak kell lennie, mint a AggregateEmotionIntervalMs.

|| Alapértelmezett (s) | Max(ok) | Min(ek) |
|--- | --- | --- | --- |
| Összesített emotionwindowms |0,5 |2 |0,25|
| Összesített emotionintervalms |0,5 |1 |0,25|

### <a name="json-output"></a>JSON kimenet
JSON kimenet összesített érzelemhez (csonkolva):

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
* A támogatott bemeneti videoformátumok közé tartozik az MP4, MOV és WMV.
* A kimutatható arcméret tartomány a 24x24 és 2048x2048 képpont között van. Az ebből a tartományból álló arcok nem lesznek észlelve.
* Az egyes videók esetében a visszaadott arcok maximális száma 64.
* Előfordulhat, hogy technikai kihívások miatt egyes arcok nem észlelhetők; például nagyon nagy arcszögek (fej-póz), és a nagy elzáródás. A frontális és a közel-frontális arcok a legjobb eredményt.

## <a name="net-sample-code"></a>.NET mintakód

A következő program bemutatja, hogyan:

1. Hozzon létre egy eszközt, és töltsön fel egy médiafájlt az eszközbe.
2. Hozzon létre egy arcfelismerő feladatot tartalmazó feladatot egy konfigurációs fájl alapján, amely a következő jsonkészletet tartalmazza: 

    ```json
            {
                "version": "1.0"
            }
    ```
3. Töltse le a kimeneti JSON fájlokat. 

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
[Az Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

[Az Azure Media Analytics bemutatói](https://amslabs.azurewebsites.net/demos/Analytics.html)

