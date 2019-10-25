---
title: Mozgások észlelése Azure Media Analyticssal | Microsoft Docs
description: A Azure Media Motion Detector Media Processor (MP) lehetővé teszi, hogy hatékonyan azonosítsa a érdekeltségi részeket egy egyébként hosszú és eseménytelen videón belül.
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
ms.openlocfilehash: c319b3e53f550e56fbf4f655cb9cfa43326f9c72
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882424"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Mozgások észlelése Azure Media Analytics

> [!IMPORTANT]
> Tekintse át az egyes adathordozó-processzorok [nyugdíjazási terveit](media-services-analytics-overview.md#retirement-plans) .

## <a name="overview"></a>Áttekintés
A **Azure Media Motion Detector** Media Processor (mp) lehetővé teszi, hogy hatékonyan azonosítsa a érdekeltségi részeket egy egyébként hosszú és eseménytelen videón belül. A mozgásérzékelő statikus kamerás felvételeken használható a videó azon részeinek azonosítására, ahol a mozgás történik. Létrehoz egy JSON-fájlt, amely tartalmazza a metaadatokat az időbélyegekkel, valamint az eseményt tartalmazó határ régiót.

A biztonsági videós hírcsatornák megkeresése révén ez a technológia a releváns eseményekre és a téves változásokra, például árnyékokra és a megvilágításra irányuló mozgásokat képes kategorizálni. Ez lehetővé teszi, hogy biztonsági riasztásokat készítsen a kamerás hírcsatornáról anélkül, hogy a végtelen lényegtelen események nélkül levélszemétbe kerül, miközben a hosszú megfigyelési videókból kinyerheti a fontos pillanatokat.

A **Azure Media Motion Detector** mp jelenleg előzetes verzióban érhető el.

Ez a cikk részletesen ismerteti a **Azure Media Motion Detector** , és bemutatja, hogyan használható a Media Services SDK for .net

## <a name="motion-detector-input-files"></a>Mozgásérzékelő bemeneti fájljai
Videofájlok. Jelenleg a következő formátumok támogatottak: MP4, MOV és WMV.

## <a name="task-configuration-preset"></a>Feladat konfigurációja (előre beállított)
Ha **Azure Media Motion Detectorkal**rendelkező feladatot hoz létre, meg kell adnia egy konfigurációs beállításkészletet. 

### <a name="parameters"></a>Paraméterek
A következő paramétereket használhatja:

| Név | Beállítások | Leírás | Alapértelmezett |
| --- | --- | --- | --- |
| sensitivityLevel |Karakterlánc: "Low", "medium", "High" |Meghatározza a mozgások jelentésének érzékenységi szintjét. Módosítsa a hamis pozitív számok számának módosítását. |közepes |
| frameSamplingValue |Pozitív egész szám |Meghatározza az algoritmus futtatásának gyakoriságát. 1 egyenlő minden kerettel, 2 azt jelenti, hogy minden második keret és így tovább. |1 |
| detectLightChange |Boolean: "true", "false" |Beállítja, hogy az eredményekben megjelenjenek-e a világos változások |Hamis |
| mergeTimeThreshold |XS-idő: óó: PP: mm<br/>Példa: 00:00:03 |Meghatározza az időablakot a mozgási események között, ahol 2 eseményt egyesíteni és 1-ként kell jelenteni. |00:00:00 |
| detectionZones |Észlelési zónák tömbje:<br/>Az észlelési zóna 3 vagy több pontból álló tömb.<br/>A-Point egy 0 és 1 közötti x és y koordináta. |A használandó sokszög-észlelési zónák listáját ismerteti.<br/>Az eredményeket a rendszer AZONOSÍTÓként jelenti a zónákban, az első pedig az "id": 0 |Egyetlen zóna, amely a teljes keretet fedi le. |

### <a name="json-example"></a>JSON-példa

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

## <a name="motion-detector-output-files"></a>Mozgásérzékelő kimeneti fájljai
A mozgásészlelési feladatok egy JSON-fájlt adnak vissza a kimeneti objektumban, amely leírja a mozgási riasztásokat és azok kategóriáit a videón belül. A fájl a videóban észlelt mozgási idő és időtartam információit tartalmazza.

A mozgásérzékelő API-k jelzik, hogy vannak-e mozgásban lévő objektumok egy rögzített háttérbeli videóban (például egy megfigyelési videóban). A mozgásérzékelő a téves riasztások, például a világítás és az árnyék változásainak csökkentése érdekében van betanítva. Az algoritmusok jelenlegi korlátai közé tartoznak az éjjellátó videók, a félig átlátszó objektumok és a kis objektumok.

### <a id="output_elements"></a>A kimeneti JSON-fájl elemei
> [!NOTE]
> A legújabb kiadásban a kimenet JSON-formátuma megváltozott, és egyes ügyfeleknél feltörési változást jelenthet.
> 
> 

A következő táblázat a kimeneti JSON-fájl elemeit ismerteti.

| Elem | Leírás |
| --- | --- |
| version |Ez a videó API verziójára vonatkozik. A jelenlegi verzió: 2. |
| időskála |A videó másodpercenkénti száma. |
| eltolás |Az időbélyegek időeltolódása a "kullancsok" alatt. A video API-k 1,0-es verziójában ez mindig 0 lesz. Az általunk támogatott jövőbeli forgatókönyvek esetében ez az érték változhat. |
| frameráta |Képkockák másodpercenkénti száma a videóban. |
| szélesség, magasság |A videó szélességét és magasságát képpontban jelöli. |
| start |A kezdő időbélyeg "ketyeg". |
| Időtartama |Az esemény hosszúsága "ketyeg". |
| interval |Az esemény egyes bejegyzéseinek intervalluma ("ticks"). |
| események |Az egyes események töredékei az adott időtartamon belül észlelt mozgást tartalmazzák. |
| type |A jelenlegi verzióban ez mindig "2" az általános mozgáshoz. Ez a címke a videó API-k számára biztosít rugalmasságot a jövőbeli verziókban való mozgás kategorizálásához. |
| regionId |A fentiekben leírtaknak megfelelően ez a verzió mindig 0 lesz. Ez a címke a videó API rugalmasságát kínálja a különböző régiókban a jövőbeli verziókban való mozgás megkereséséhez. |
| régió |A videó azon részére utal, amelyben érdekli a mozgás. <br/><br/>-az "id" a régió területét jelenti – ebben a verzióban csak egy azonosító 0. <br/>a "type" kifejezés a mozgáshoz szükséges régió alakját jelöli. Jelenleg a "téglalap" és a "sokszög" támogatott.<br/> Ha a "téglalap" értéket adta meg, a régió mérete X, Y, width és height. Az X és Y koordináták a régió bal oldali XY koordinátáit jelölik a 0,0 és 1,0 közötti normalizált méretekben. A szélesség és a magasság a 0,0 és 1,0 közötti normalizált skálán lévő régió méretét jelöli. Az aktuális verzióban az X, Y, width és height mindig a 0, 0 és 1, 1. <br/>Ha a "sokszög" értéket adta meg, a régió méretei vannak a pontokban. <br/> |
| töredékek |A metaadatok a töredékek nevű különböző szegmensekben vannak kiosztva. Minden töredék tartalmaz kezdési időpontot, időtartamot, intervallumszámot és esemény(eke)t. Az események nélküli töredékek azt jelzik, hogy a kezdési idő és az időtartam során nem észlelhető mozgás. |
| szögletes zárójelek [] |Az egyes zárójelek az esemény egy intervallumát jelölik. Az adott intervallumhoz tartozó üres zárójelek azt jelzik, hogy nem észlelhető mozgás. |
| Helyek |Ez az új bejegyzés az események területen listázza azt a helyet, ahol a mozgás történt. Ez konkrétabb, mint az észlelési zónák. |

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
* A támogatott bemeneti videoformátum például az MP4, a MOV és a WMV.
* A mozgásérzékelő a helyhez kötött háttérbeli videókra van optimalizálva. Az algoritmus a téves riasztások, például a világítási változások és az árnyékok csökkentésére koncentrál.
* A technikai kihívások miatt előfordulhat, hogy egyes mozgások nem észlelhetők. például: éjjellátó videók, félig átlátszó objektumok és kis objektumok.

## <a name="net-sample-code"></a>.NET-mintakód

A következő program a következőket mutatja be:

1. Hozzon létre egy adategységet, és töltsön fel egy médiafájlt az eszközre.
2. Hozzon létre egy feladatot egy Video Motion észlelési feladattal egy olyan konfigurációs fájl alapján, amely a következő JSON-beállításkészletet tartalmazza: 
   
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

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Azure Media Services Motion detektor blogja](https://azure.microsoft.com/blog/motion-detector-update/)

[Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

[Azure Media Analytics bemutatók](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

