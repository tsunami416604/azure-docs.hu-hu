---
title: Az Azure Media Analytics mozdulatok észleléséhez |} A Microsoft Docs
description: Az Azure Media Motion detector használatával médiafeldolgozót. (pont) lehetővé teszi, hogy hatékonyan az egyes szakaszokhoz egy egyébként hosszú és Eseménytelen videó házirendsablonokkal.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: milanga;juliako;
ms.openlocfilehash: 12af87ab0a8b15528acbd9ce8a1bc92f478aba28
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54820971"
---
# <a name="detect-motions-with-azure-media-analytics"></a>Az Azure Media Analytics mozdulatok észlelése
## <a name="overview"></a>Áttekintés
A **Azure Media Motion detector használatával** médiafeldolgozót. (pont) lehetővé teszi, hogy hatékonyan az egyes szakaszokhoz egy egyébként hosszú és Eseménytelen videó házirendsablonokkal. Mozgásérzékelés a statikus kamerák felvételei, azonosíthatja a videó szakaszok hol történik a mozgásban lévő adatoknak egyaránt használható. Az időbélyegeket és a határoló régió, ahol az esemény történt egy metaadatokat tartalmazó JSON-fájlt állít elő.

Biztonsági videóközvetítési céloz, ezt a technológiát is képes mozgásban lévő adatoknak egyaránt kategorizálásához kapcsolódó eseményeket, és a téves, például az árnyékok és megvilágítási módosításokat. Ez lehetővé teszi, hogy a biztonsági riasztások létrehozása a kamera hírcsatornák nélkül végtelen irreleváns eseményekkel rendelkező során képes arra, hogy a lényeges pillanat kinyerése hosszú felügyeleti videók címünkre folyamatban van.

A **Azure Media Motion detector használatával** felügyeleti csomag jelenleg előzetes verzióban érhető el.

Ez a cikk kapcsolatos részleteket nyújt **Azure Media Motion detector használatával** , és bemutatja, hogyan használja a Media Services SDK for .NET

## <a name="motion-detector-input-files"></a>Motion detector használatával bemeneti fájlok
Videofájlok. Jelenleg a következő formátumok támogatottak: MP4 MOV és WMV.

## <a name="task-configuration-preset"></a>A feladat konfigurációs (előre)
A feladat létrehozásakor **Azure Media Motion detector használatával**, meg kell adnia egy konfigurációs készletet. 

### <a name="parameters"></a>Paraméterek
A következő paraméterek használhatók:

| Name (Név) | Beállítások | Leírás | Alapértelmezett |
| --- | --- | --- | --- |
| sensitivityLevel |Karakterlánc: "alacsony", "közepes", "nagy" |Beállítja az érzékenységi szint melyik mozdulatok jelenti. Módosítsa ezt a módosíthatja a vakriasztások számát. |"közepes" |
| frameSamplingValue |pozitív egész szám lehet |Beállítja a gyakoriságát, hogy mely algoritmus futtatja. 1 egyenlő minden keret, 2 jelenti, hogy minden második keret, és így tovább. |1 |
| detectLightChange |Boolean:'true', 'false' |Megadja, hogy az összes kisebb változtatást kell jelenteni az eredményeket,-e |"False" |
| mergeTimeThreshold |Xs-time: Óó<br/>Példa: 00:00:03 |Az időtartomány mozgáseseményeket hol tárolja a rendszer 2 esemény között lehet kombinált és jelentett 1, adja meg. |00:00:00 |
| detectionZones |Egy tömb észlelési zónák:<br/>-Észlelési zóna egy 3 vagy több pontok tömbje<br/>– Pont, az x és y koordináta 0, 1-re. |A lista a sokszög alakú észlelési zónák használandó ismerteti.<br/>Eredmények a zónák-azonosító, először egy folyamatban "id" jelentett: 0 |Egy zóna, amely magában foglalja a teljes keret. |

### <a name="json-example"></a>Példa JSON

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

## <a name="motion-detector-output-files"></a>Motion detector használatával kimeneti fájlok
A mozgás észlelése egy JSON-fájlt a kimeneti objektum, amely a mozgásban lévő adatoknak egyaránt riasztásokat és a kategóriák, a videó adja vissza. A fájl tartalmazza a időpontja és időtartama észlelhető a videó a mozgásban lévő információkat.

A Motion detector használatával API mutatókat biztosít, ha egy videó rögzített háttérben mozgásban lévő objektumok (például egy felügyeleti videó). A Motion detector használatával csökkenti a vakriasztások, például a világítás és árnyékmásolat-módosítások, be van tanítva. Aktuális korlátozások algoritmusok éjszakai vision videókat, félig átlátszó objektumokat és kis méretű objektumok tartalmazza.

### <a id="output_elements"></a>A kimenet JSON-fájl elemeinek
> [!NOTE]
> A kimenet JSON formátumban a legújabb kiadásban megváltozott, és használhatatlanná tévő változást tüntetheti egyes ügyfelek számára.
> 
> 

A következő táblázat ismerteti a kimeneti JSON-fájl elemeinek.

| Elem | Leírás |
| --- | --- |
| Verzió |Ez vonatkozik a Video API verziója. A jelenlegi verzió: 2. |
| Időskála |"Órajel során végbemenő" a videó másodpercenként. |
| Eltolás |Időbélyegeket "órajelben." az idő eltolása Videó API-k 1.0-s verziójában ez mindig 0 lesz. A jövőben támogatott forgatókönyveket, ez az érték változhatnak. |
| Képkockasebesség |Képkockák másodpercenkénti száma a videóban. |
| Szélesség, Hosszúság |A szélességét és magasságát (képpontban) hivatkozik. |
| Indítás |A "órajel során végbemenő" a start időbélyegző. |
| Időtartam |Az esemény, a "órajel során végbemenő" hossza. |
| Intervallum |Az időköz az egyes bejegyzések az eseményben szereplő "órajel során végbemenő". |
| Események |Minden egyes esemény töredék idő töltött belül észlelt a mozgásban lévő adatoknak egyaránt tartalmazza. |
| Típus |A jelenlegi verzióban ez a "2" általános mozgásban lévő adatoknak egyaránt. A címke ad Video API-k a rugalmasság kategorizálásának mozgási későbbi verziók. |
| RegionID |Amint azt fent kifejtettük, ez mindig 0 lesz ebben a verzióban. Ez a címke rugalmasságot biztosít Video API a mozgásban lévő keresése a későbbi verziókban különböző régiókban. |
| Régiók |A videó a terület, ahol konkrét mozgásban lévő adatoknak egyaránt vonatkozik. <br/><br/>– az "id" jelöli a régió terület – ebben a verzióban van csak egy, azonosító: 0. <br/>– "type" az Ön számára a mozgásban lévő adatoknak egyaránt régió az alakzat jelöli. Jelenleg a "téglalap" és "sokszög" támogatottak.<br/> "Téglalap" adott meg, ha a régió dimenziók rendelkezik X, Y szélessége és magassága. Az X és Y koordinátái a 0,0 és 1,0 normalizált méretezési régió felső bal XY koordináták képviseli. A szélesség és magasság képviselik a 0,0 és 1,0 a normalizált méretezési terület méretét. A jelenlegi verzió X, Y, szélesség és magasság mindig rögzíteni, 0, 0 és 1, 1. <br/>"Sokszög" adott meg, ha a régió dimenziók pontokat tartalmaz. <br/> |
| Töredékek |A metaadatok nevű töredék különböző részekre van darabolásos fel. Minden töredék tartalmaz kezdési időpontot, időtartamot, intervallumszámot és esemény(eke)t. Egy kódrészletet az események nem azt jelenti, hogy semmilyen adott kezdő időpontja és időtartama során észlelt. |
| Szögletes zárójeleket] |Minden egyes zárójel egy időköz az esemény jelöli. Üres szögletes az adott időköz azt jelenti, hogy semmilyen volt észlelhető. |
| helyek |Ezen események alapján új bejegyzést a helyet, ahol a mozgásban lévő adatoknak egyaránt történt sorolja fel. Ez a pontosabb, mint az észlelési zónák. |

Az alábbi JSON-példa a kimenet látható:

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
* A támogatott bemeneti videóformátumok MP4 MOV és WMV tartalmazza.
* Mozgás észlelése álló háttér-videók van optimalizálva. Az algoritmus a csökkenti a vakriasztások, például a világítás módosításokat, és az árnyékok összpontosít.
* Néhány mozgásban lévő adatoknak egyaránt technikai problémák miatt nem észlelhető például éjszakai vision videók, félig átlátszó objektumokat és kis méretű objektumok.

## <a name="net-sample-code"></a>.NET mintakód

A következő program mutat be, hogyan:

1. Hozzon létre egy objektumot, és a egy médiafájlt feltöltése az objektumba.
2. Hozzon létre egy feladatot a következő json-készletet tartalmazó konfigurációs fájl alapján Videós mozgásérzékelési észlelési feladatokkal: 
   
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
[Az Azure Media Services Motion detector használatával blogja](https://azure.microsoft.com/blog/motion-detector-update/)

[Az Azure Media Services analitikai funkcióinak áttekintése](media-services-analytics-overview.md)

[Az Azure Médiaelemzés bemutatók](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

