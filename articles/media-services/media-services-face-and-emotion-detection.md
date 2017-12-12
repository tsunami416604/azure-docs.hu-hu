---
title: "Arcfelismerés és az Azure Media Analytics Érzelemfelismerési észlelése |} Microsoft Docs"
description: "Ez a témakör bemutatja, hogyan lapokat és az Azure Media Analytics érzelmek észleléséhez."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 5ca4692c-23f1-451d-9d82-cbc8bf0fd707
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: milanga;juliako;
ms.openlocfilehash: 5741a484dcda05e3143b5f896ddee2e8591dabee
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="detect-face-and-emotion-with-azure-media-analytics"></a>Arcfelismerés és az Azure Media Analytics Érzelemfelismerési észlelése
## <a name="overview"></a>Áttekintés
A **Azure Media Arcfelismerési érzékelő** media processzor (MP) lehetővé teszi a száma, nyomon követésére típusú áthelyezések, és akkor is fel tudja mérni a célközönség részvételét és reakciót arcfelismerést kifejezések keresztül. Ez a szolgáltatás két funkciókat tartalmazza: 

* **Arcfelismerési észlelése**
  
    Arcfelismerési észlelési talál, és nyomon követi a videó emberi lapjaira. Több lapokat észlelhető, és ezt követően nyomon követheti az adott vissza egy JSON-fájlban ideje és helye metaadatokkal körül, mozgás. Során nyomon követése, megkísérli adjon egységes azonosító ugyanazon felületére, amíg a személy van Navigálás a képernyőn, még akkor is, ha kényszerítő vagy röviden hagyja a keret.
  
  > [!NOTE]
  > Ez a szolgáltatás nem végez arcfelismerést. Személy elhagyja a keret vagy a válik fedhetik túl hosszú kap egy új Azonosítót amikor azok tér vissza.
  > 
  > 
* **Érzelemfelismerés**
  
    Érzelemfelismerés Arcfelismerési észlelési adathordozó-processzor elemzés több érzelmi attribútum vissza a lapok észlel, például Boldogsága, sadness, félelem, utasítás és egyéb választható összetevőként. 

A **Azure Media Arcfelismerési érzékelő** felügyeleti csomag jelenleg előzetes verzió.

Ez a cikk részletezi kapcsolatos **Azure Media Arcfelismerési érzékelő** és a .NET-keretrendszerhez készült Media Services SDK-val való használatát ismerteti.

## <a name="face-detector-input-files"></a>A bemeneti fájlok érzékelő szembesülhetnek
Videofájlok lejátszását. Jelenleg a következő formátumok használhatók: MP4 MOV és WMV.

## <a name="face-detector-output-files"></a>Szembesülhetnek érzékelő kimeneti fájlok
A tapasztalt felderítését és a nyomon követési API magas pontosság arcfelismerési hely észlelési és követési, amely észlelni tudja a videó legfeljebb 64 emberi lapok biztosít. Elülső lapok nyújtanak a legjobb eredmények elérése érdekében, közben ügyféloldali lapok és kis (legfeljebb 24 x 24 képpont) lapok nem feltétlenül legpontosabb.

Az észlelt és a nyomon követett lapok koordináták (bal, felső, szélességét és magasságát) küld vissza a rendszer jelzi a lapok képpont, valamint egy oldallal azonosítószámát, jelezve, hogy egyes követését a lemezkép helyét. Arcfelismerési azonosítószámát nagyon eséllyel fordulnak elő a elülső arcfelismerési ellopása vagy átfedésben vannak a keretében körülmények alaphelyzetbe néhány első hozzárendelt több azonosítók egyének eredményez.

## <a id="output_elements"></a>A kimeneti JSON-fájl elemeinek

[!INCLUDE [media-services-analytics-output-json](../../includes/media-services-analytics-output-json.md)]

Arcfelismerési érzékelő (ahol az események törik fel, ha túl nagy elérték) töredezettsége (ahol a metaadatokat az időalapú adattömbök is osztható fel és letöltheti a csak találja), és a szegmentálási technikák használja. Néhány egyszerű számítások segítségével átalakíthatja az adatokat. Például, ha egy esemény használatába 6300 (ticks), egy időskálára 2997 (ticks/másodperc), és a 29,97 (keret/mp), majd képkockasebességhez:

* Start/időskálára = 2.1 másodperc
* X Framerate másodperc 63 keretek =

## <a name="face-detection-input-and-output-example"></a>Szembesülhetnek észlelési bemeneti és kimeneti példa
### <a name="input-video"></a>A bemeneti videó
[A bemeneti videó](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>A feladat konfigurációja (beállítás)
A feladat létrehozásakor **Azure Media Arcfelismerési érzékelő**, meg kell adnia egy konfigurációs készletet. A következő konfigurációs készlet folyamat csak arcfelismerési észlelése.

    {
      "version":"1.0",
      "options":{
          "TrackingMode": "Fast"
      }
    }

#### <a name="attribute-descriptions"></a>Az attribútumok leírása
| Attribútum neve | Leírás |
| --- | --- |
| Mód |Gyors - feldolgozása gyors sebességét, de kevésbé pontos (alapértelmezett).|

### <a name="json-output"></a>JSON kimeneti
A következő példa a JSON-kimenetét csonkolódott.

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

        . . . 

## <a name="emotion-detection-input-and-output-example"></a>Érzelemfelismerés bemeneti és kimeneti példa
### <a name="input-video"></a>A bemeneti videó
[A bemeneti videó](http://ampdemo.azureedge.net/azuremediaplayer.html?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc8834d9f-0b49-4b38-bcaf-ece2746f1972%2FMicrosoft%20Convergence%202015%20%20Keynote%20Highlights.ism%2Fmanifest&amp;autoplay=false)

### <a name="task-configuration-preset"></a>A feladat konfigurációja (beállítás)
A feladat létrehozásakor **Azure Media Arcfelismerési érzékelő**, meg kell adnia egy konfigurációs készletet. A következő konfigurációs beállítás határozza meg a érzelemfelismerés alapján JSON létrehozásához.

    {
      "version": "1.0",
      "options": {
        "aggregateEmotionWindowMs": "987",
        "mode": "aggregateEmotion",
        "aggregateEmotionIntervalMs": "342"
      }
    }


#### <a name="attribute-descriptions"></a>Az attribútumok leírása
| Attribútum neve | Leírás |
| --- | --- |
| Mód |Lapok: Csak szembesülhetnek észlelése.<br/>PerFaceEmotion: Visszatérési érzelemfelismerési egymástól függetlenül az egyes arcfelismerési észlelése.<br/>AggregateEmotion: Minden lap keretében átlagos érzelemfelismerési visszatérési értékei. |
| AggregateEmotionWindowMs |Ha a kiválasztott AggregateEmotion módot használja. Megadja azt az időtartamot, ezredmásodpercben minden összesített eredmény létrehozásához használt videó. |
| AggregateEmotionIntervalMs |Ha a kiválasztott AggregateEmotion módot használja. Itt adhatja meg, milyen gyakorisággal összesített eredmények eredményezett. |

#### <a name="aggregate-defaults"></a>Összesített alapértelmezései
Alább összesített ablakot, és időköz beállítások értékei használata ajánlott. AggregateEmotionWindowMs hosszabb, mint AggregateEmotionIntervalMs kell lennie.

|| Alapértelmezett (s) | Max(s) | Min(s) |
|--- | --- | --- | --- |
| AggregateEmotionWindowMs |0.5 |2 |0.25|
| AggregateEmotionIntervalMs |0.5 |1 |0.25|

### <a name="json-output"></a>JSON kimeneti
Összesített érzelemfelismerési (csonkolt) a kimeneti JSON:

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

## <a name="limitations"></a>Korlátozások
* A támogatott bemeneti videó formátumnak tartalmaznia kell MP4 MOV és WMV.
* A észlelhető arcfelismerési mérete tartománya 24 x 24 a 2048 x 2048 képpontban megadva. A tartományon kívül esik a lapok nem fogja észlelni.
* Minden egyes videó visszaadott oldalak maximális számának 64 esetén.
* Bizonyos lapok műszaki akadályok; miatt nem észlelhető például nagyon nagy arcfelismerési szögek (head-jelentő), és nagy hangelnyelés. Elülső és közelében elülső lapok van a legjobb eredmények elérése érdekében.

## <a name="net-sample-code"></a>.NET mintakód

A következő program bemutatja hogyan:

1. Hozzon létre egy eszközt, és adathordozó-fájl feltöltése az objektumba.
2. Hozzon létre egy feladatot a következő json-készletet tartalmazó konfigurációs fájl alapján arcfelismerési észlelési feladatokkal: 
   
        {
            "version": "1.0"
        }
3. A kimeneti JSON-fájlok letöltésére. 

#### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint. 

#### <a name="example"></a>Példa

```
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
            task.OutputAssets.AddNew("My Face Detectoion Output Asset", AssetCreationOptions.None);

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
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Azure Media Services elemző áttekintése](media-services-analytics-overview.md)

[Az Azure Médiaelemzés bemutatók](http://amslabs.azurewebsites.net/demos/Analytics.html)

