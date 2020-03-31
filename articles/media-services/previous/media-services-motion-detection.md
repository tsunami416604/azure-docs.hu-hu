---
title: Mozgásészlelése az Azure Media Analytics szolgáltatással | Microsoft dokumentumok
description: Az Azure Media Motion Detector médiaprocesszor (MP) lehetővé teszi, hogy hatékonyan azonosítsa az érdeklődési szakaszokat egy egyébként hosszú és eseménytelen videón belül.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: milanga
ms.openlocfilehash: f4c021531a4d04bf16e5dbee4172952433f675d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913004"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Mozgások észlelése az Azure Media Analytics segítségével

> [!NOTE]
> Az **Azure Media Motion Detector médiaprocesszor** a rendszer megszűnik. A kivonási dátum, lásd a [régi összetevők](legacy-components.md) témakörben.
 
## <a name="overview"></a>Áttekintés

Az **Azure Media Motion Detector médiaprocesszor** (MP) lehetővé teszi, hogy hatékonyan azonosítsa az érdeklődési szakaszokat egy egyébként hosszú és eseménytelen videón belül. A mozgásérzékelés statikus kamerafelvételeken használható a videó azon részeinek azonosítására, ahol mozgás történik. Létrehoz egy JSON-fájlt, amely metaadatokat tartalmaz időbélyegekkel és az esemény előfordulási régiójával.

A biztonsági videocsatornákra irányuló technológia képes kategorizálni a mozgást a releváns eseményekbe és hamis pozitívumokba, például az árnyékokba és a megvilágítási változásokba. Ez lehetővé teszi, hogy biztonsági riasztásokat hozzon létre a kamera hírcsatornáiból anélkül, hogy végtelen irreleváns eseményekkel spamelne, miközben képes kivonni a hosszú felügyeleti videók érdekes pillanatait.

Az **Azure Media motion detector** MP jelenleg előzetes verzióban van.

Ez a cikk az **Azure Media Motion Detector** szolgáltatással kapcsolatos részleteket ismerteti, és bemutatja, hogyan használható a Media Services SDK for .

## <a name="motion-detector-input-files"></a>Mozgásérzékelő bemeneti fájljai
Videofájlok. Jelenleg a következő formátumok támogatottak: MP4, MOV és WMV.

## <a name="task-configuration-preset"></a>Feladat konfigurációja (készlet)
Amikor feladatot hoz létre az **Azure Media Motion Detector**használatával, meg kell adnia egy konfigurációs készletet. 

### <a name="parameters"></a>Paraméterek
A következő paramétereket használhatja:

| Név | Beállítások | Leírás | Alapértelmezett |
| --- | --- | --- | --- |
| sensitivityLevel |String:'low', 'medium', 'high' |Beállítja azt az érzékenységi szintet, amelyen a mozgásokat jelentik. Ezt állítsa be a hamis pozitívak számának beállításához. |"közepes" |
| frameSamplingValue |Pozitív egész szám |Beállítja az algoritmus futásának gyakoriságát. 1 egyenlő minden képkocka, 2 azt jelenti, minden második képkocka, és így tovább. |1 |
| detectLightChange (Fényváltozás észlelése) |Logikai: "igaz", "hamis" |Beállítja, hogy az eredmények ben jelentsék-e a fényváltozásokat |"Hamis" |
| mergeTimeThreshold |Xs-idő: Ó:mm:ss<br/>Például: 00:00:03 |Megadja a mozgási események közötti időablakot, ahol a 2 esemény összeslesz, és 1-ként lesz jelentve. |00:00:00 |
| detectionZones |Észlelési zónák tömbje:<br/>- Az észlelési zóna 3 vagy több pontból áll<br/>- A pont 0 és y koordináta 0-tól 1-ig. |A használandó sokszögelőedési zónák listáját ismerteti.<br/>Az eredményeket a zónák azonosítóként jelentik, az első pedig "id":0 |Egyetlen zóna, amely lefedi a teljes keretet. |

### <a name="json-example"></a>JSON példa

```json
    {
      "version": "1.0",
      "options": {
        "sensitivityLevel": "medium",
        "frameSamplingValue": 1,
        "detectLightChange": "False",
        "mergeTimeThreshold":
        "00:00:02",
        "detectionZones": [
          [
            {"x": 0, "y": 0},
            {"x": 0.5, "y": 0},
            {"x": 0, "y": 1}
           ],
          [
            {"x": 0.3, "y": 0.3},
            {"x": 0.55, "y": 0.3},
            {"x": 0.8, "y": 0.3},
            {"x": 0.8, "y": 0.55},
            {"x": 0.8, "y": 0.8},
            {"x": 0.55, "y": 0.8},
            {"x": 0.3, "y": 0.8},
            {"x": 0.3, "y": 0.55}
          ]
        ]
      }
    }
```

## <a name="motion-detector-output-files"></a>Mozgásérzékelő kimeneti fájlok
A mozgásészlelési feladat egy JSON-fájlt ad vissza a kimeneti eszközben, amely leírja a videón belüli mozgásriasztásokat és azok kategóriáit. A fájl információkat tartalmaz a videóban észlelt mozgás idejéről és időtartamáról.

A Mozgásérzékelő API jelzőket biztosít, ha egy rögzített háttérvideóban (például egy felügyeleti videóban) mozgásban lévő objektumok vannak. A mozgásérzékelő be van tanítva a téves riasztások, például a megvilágítás és az árnyékváltozások csökkentésére. Az algoritmusok jelenlegi korlátai közé tartoznak az éjjellátó videók, a félig átlátszó tárgyak és a kis tárgyak.

### <a name="elements-of-the-output-json-file"></a><a id="output_elements"></a>A kimeneti JSON-fájl elemei
> [!NOTE]
> A legújabb kiadásban a Kimeneti JSON formátum megváltozott, és néhány ügyfél számára törésváltozást jelenthet.
> 
> 

Az alábbi táblázat a kimeneti JSON-fájl elemeit ismerteti.

| Elem | Leírás |
| --- | --- |
| version |Ez a videó API verziójára vonatkozik. A jelenlegi verzió 2. |
| Időskála |"Kullancsok" másodpercenként a videó. |
| offset |Az időbélyegek időeltolódása a "ticks"-ben. A videoAPI-k 1.0-s verziójában ez mindig 0 lesz. Az általunk támogatott jövőbeli forgatókönyvekben ez az érték változhat. |
| Frameráta |Képkockák másodpercenkénti száma a videóban. |
| szélesség, magasság |A videó szélességére és magasságára hivatkozik képpontban. |
| start |A kezdő időbélyeg a "ticks".The start timestamp in "ticks". |
| duration |Az esemény hossza a "kullancsokban". |
| interval |Az esemény minden egyes bejegyzésének intervalluma a "kullancsok" mezőben. |
| események |Minden eseményrészlet tartalmazza az adott időtartamon belül észlelt mozgást. |
| type |A jelenlegi verzió, ez mindig "2" az általános mozgás. Ez a címke rugalmasságot biztosít a videoAPI-k számára a mozgás kategorizálásához a későbbi verziókban. |
| regionId |Mint már említettük, ez mindig 0 ebben a verzióban. Ez a címke rugalmasságot biztosít a Video API számára, hogy a jövőbeli verziókban mozgást találjon a különböző régiókban. |
| Régiók |Arra a területre utal a videódban, ahol érdekel a mozgás. <br/><br/>-"id" képviseli a régió területén - ebben a verzióban csak egy, ID 0. <br/>-"típus" az Ön számára fontos terület alakját jelöli. Jelenleg a "téglalap" és a "sokszög" támogatott.<br/> Ha a "téglalap" szót adta meg, a terület méretei X, Y, Szélesség és Magasság. Az X és Y koordináták a régió bal felső XY koordinátáit jelölik egy 0,0 és 1,0 között normalizált léptékben. A szélesség és a magasság a régió méretét jelöli egy 0,0 és 1,0 között normalizált léptékben. Az aktuális verzióban az X, Y, szélesség és magasság mindig 0, 0 és 1, 1. <br/>Ha a "sokszög" szót adta meg, a régió méretei pontokban vannak. <br/> |
| Töredékek |A metaadatok különböző szegmensekbe, úgynevezett töredékekbe vannak felosztva. Minden töredék tartalmaz kezdési időpontot, időtartamot, intervallumszámot és esemény(eke)t. Az események nélküli töredék azt jelenti, hogy a kezdési idő és időtartam alatt nem észlelt mozgás. |
| zárójelek [] |Minden zárójel egy intervallumot jelöl az eseményben. Az adott intervallumüres zárójelei azt jelentik, hogy a rendszer nem észlelt mozgást. |
| Helyek |Ez az új bejegyzés az események alatt felsorolja azt a helyet, ahol a mozgás történt. Ez specifikusabb, mint az észlelési zónák. |

A következő JSON-példa a kimenetet mutatja be:

```json
    {
      "version": 2,
      "timescale": 23976,
      "offset": 0,
      "framerate": 24,
      "width": 1280,
      "height": 720,
      "regions": [
        {
          "id": 0,
          "type": "polygon",
          "points": [{'x': 0, 'y': 0},
            {'x': 0.5, 'y': 0},
            {'x': 0, 'y': 1}]
        }
      ],
      "fragments": [
        {
          "start": 0,
          "duration": 226765
        },
        {
          "start": 226765,
          "duration": 47952,
          "interval": 999,
          "events": [
            [
              {
                "type": 2,
                "typeName": "motion",
                "locations": [
                  {
                    "x": 0.004184,
                    "y": 0.007463,
                    "width": 0.991667,
                    "height": 0.985185
                  }
                ],
                "regionId": 0
              }
            ],
```

## <a name="limitations"></a>Korlátozások
* A támogatott bemeneti videoformátumok közé tartozik az MP4, MOV és WMV.
* A mozgásérzékelés álló háttérvideókhoz van optimalizálva. Az algoritmus a téves riasztások, például a megvilágítási változások és az árnyékok csökkentésére összpontosít.
* Technikai kihívások miatt előfordulhat, hogy bizonyos mozgások nem észlelhetők; például éjjellátó videók, félig átlátszó tárgyak és kis tárgyak.

## <a name="net-sample-code"></a>.NET mintakód

A következő program bemutatja, hogyan:

1. Hozzon létre egy eszközt, és töltsön fel egy médiafájlt az eszközbe.
2. Hozzon létre egy feladatot egy videó mozgásészlelési feladattal egy konfigurációs fájl alapján, amely a következő json-készletet tartalmazza: 
   
    ```json
            {
            "Version": "1.0",
            "Options": {
                "SensitivityLevel": "medium",
                "FrameSamplingValue": 1,
                "DetectLightChange": "False",
                "MergeTimeThreshold":
                "00:00:02",
                "DetectionZones": [
                [
                    {"x": 0, "y": 0},
                    {"x": 0.5, "y": 0},
                    {"x": 0, "y": 1}
                ],
                [
                    {"x": 0.3, "y": 0.3},
                    {"x": 0.55, "y": 0.3},
                    {"x": 0.8, "y": 0.3},
                    {"x": 0.8, "y": 0.55},
                    {"x": 0.8, "y": 0.8},
                    {"x": 0.55, "y": 0.8},
                    {"x": 0.3, "y": 0.8},
                    {"x": 0.3, "y": 0.55}
                ]
                ]
            }
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

namespace VideoMotionDetection
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

            // Run the VideoMotionDetection job.
            var asset = RunVideoMotionDetectionJob(@"C:\supportFiles\VideoMotionDetection\BigBuckBunny.mp4",
                                        @"C:\supportFiles\VideoMotionDetection\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\VideoMotionDetection\Output");
        }

        static IAsset RunVideoMotionDetectionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My Video Motion Detection Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Video Motion Detection Job");

            // Get a reference to Azure Media Motion Detector.
            string MediaProcessorName = "Azure Media Motion Detector";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Video Motion Detection Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Video Motion Detection Output Asset", AssetCreationOptions.None);

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
[Az Azure Media Services mozgásérzékelőjében blog](https://azure.microsoft.com/blog/motion-detector-update/)

[Az Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

[Az Azure Media Analytics bemutatói](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

