---
title: "Az Azure Media Analytics mozdulatok észlelése |} Microsoft Docs"
description: "Az Azure Media mozgásérzékelő media processzor (MP) lehetővé teszi egy egyébként hosszú és Eseménytelen video házirendsablonokkal szakaszok hatékonyan azonosításához."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: milanga;juliako;
ms.openlocfilehash: dd422308ed728ed4e8bc35daee3bd50f0f02aaac
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="detect-motions-with-azure-media-analytics"></a>Az Azure Media Analytics mozdulatok észlelése
## <a name="overview"></a>Áttekintés
A **Azure Media mozgásérzékelő** media processzor (MP) lehetővé teszi a szakaszok egy egyébként hosszú és Eseménytelen video házirendsablonokkal hatékonyan azonosításához. Mozgásérzékelés statikus kamerák felvételei szakaszok a videó hol mozgásérzékelési jelentkezik azonosításához használhatja. Az időbélyegzőket és a határoló régió, ahol az esemény történt a metaadatokat tartalmazó JSON-fájlt hoz létre.

Cél biztonsági videó hírcsatornák, ez a technológia el tudja mozgásérzékelési kategorizálása kapcsolódó eseményeket és vakriasztások például árnyékok és megvilágítási módosításokat. Ez lehetővé teszi, hogy a biztonsági riasztások generálása kamera hírcsatornák közben igényt érdeklő pillanat kinyerése rendkívül hosszú felügyeleti videók végtelen irreleváns események, éppen címünkre nélkül.

A **Azure Media mozgásérzékelő** felügyeleti csomag jelenleg előzetes verzió.

Ez a cikk részletezi kapcsolatos **Azure Media mozgásérzékelő** és a .NET-keretrendszerhez készült Media Services SDK-val való használatát ismerteti

## <a name="motion-detector-input-files"></a>Mozgásérzékelő – érzékelő bemeneti fájlok
Videofájlok lejátszását. Jelenleg a következő formátumok használhatók: MP4 MOV és WMV.

## <a name="task-configuration-preset"></a>A feladat konfigurációja (beállítás)
A feladat létrehozásakor **Azure Media mozgásérzékelő**, meg kell adnia egy konfigurációs készletet. 

### <a name="parameters"></a>Paraméterek
A következő paramétereket használhatja:

| Név | Beállítások | Leírás | Alapértelmezett |
| --- | --- | --- | --- |
| sensitivityLevel |Karakterlánc: "alacsony", "közepes", "magas" |Beállítja az érzékenységi szint mely mozdulatok jelenti. Ez úgy, hogy a vakriasztások számának módosítása |"közepes" |
| frameSamplingValue |Pozitív egész szám |A készletek algoritmus gyakorisága futtatja. 1 egyenlő minden keret, 2 azt jelenti, hogy minden 2. keret, és így tovább. |1 |
| detectLightChange |Logikai: "true", "false" |Megadja, hogy e könnyű módosítások jelentik az eredmények között |"False" |
| mergeTimeThreshold |Idő xs: Óó: pp:<br/>. Példa: 00:00:03 |Adja meg az időszak, ahol 2 esemény lesz kombinált és 1 jelentett mozgásérzékelési események között. |00:00:00 |
| detectionZones |A tömb észlelési zónák:<br/>-Észlelési zóna: 3 vagy több pontok bájttömb<br/>-Pont egy x és y koordináta 0, 1. |Ismerteti a sokszög észlelési zónák használandó listáját.<br/>Eredmények a zónákat az azonosítója, először egy folyamatban "id" a jelentett: 0 |Egy zóna, amely magában foglalja a teljes keret. |

### <a name="json-example"></a>JSON-példa
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


## <a name="motion-detector-output-files"></a>Mozgásérzékelő – érzékelő kimeneti fájlok
A mozgás észlelése a JSON-fájl adja vissza a kimeneti adategységen, amely a mozgásérzékelési riasztások, és azok kategóriák belül a videó ismerteti. A fájl ideje és időtartama észlelte a videóból mozgásérzékelési fogja tartalmazni.

A Mozgásérzékelési érzékelő API mutatók biztosít, miután egy videó rögzített háttérben mozgó objektumok (például a kamera). A mozgásérzékelő be van tanítva, téves riasztásokat, például a megvilágítási és árnyékmásolat módosítások csökkentése érdekében. Aktuális korlátozások algoritmusok éjszakai stratégiai videók, félig átlátható és kis objektumok tartalmazza.

### <a id="output_elements"></a>A kimeneti JSON-fájl elemeinek
> [!NOTE]
> Legújabb kiadása a kimeneti JSON formátumban megváltozott, és az egyes ügyfelek használhatatlanná tévő változást jelöl.
> 
> 

A következő táblázat ismerteti a kimeneti JSON-fájl elemeinek.

| Elem | Leírás |
| --- | --- |
| Verzió |Ez a videó API verziója vonatkozik. A jelenlegi verzió: 2. |
| időskálára |A videó másodpercenként "ticks". |
| Eltolás |A "ticks" időbélyegeket időeltolódás. Videó API-k 1.0-s verziójában az mindig 0 lesz. A jövőben támogatott forgatókönyveket, ezt az értéket módosíthatja. |
| képkockasebességhez |A videó képkockasebessége. |
| Szélesség, Hosszúság |A szélességét és magasságát képpontban hivatkozik. |
| Indítás |A start időbélyegzőjéhez viszonyítva a "ticks". |
| Időtartam |Az esemény, a "ticks" hosszát. |
| Időköz |Az időköz az egyes bejegyzések az eseményben a "ticks". |
| Események |Minden esemény-töredéket adott időtartamig belül észlelhető a mozgásérzékelési tartalmazza. |
| Típus |A jelenlegi verzióban ez a "2" általános mozgásérzékelési. A címke által biztosított videó API-k a rugalmasságot kategorizálásának mozgási későbbi verzióit. |
| RegionID |Ahogy fent, az mindig 0 lesz ebben a verzióban. Ezt a címkét mozgásérzékelési későbbi verzióiban különböző régiókban található rugalmasságot biztosít a videó API. |
| Régiók |A terület a videó, ahol az Ön számára legfontosabb mozgásérzékelési hivatkozik. <br/><br/>-az "id" jelöli a régió terület – ebben a verzióban van csak egy, azonosító: 0. <br/>-"type" jelenti. az Ön számára legfontosabb mozgásérzékelő – az a régió alakját. Jelenleg a "téglalap", "sokszög" támogatottak.<br/> Ha a megadott "téglalap", a régiót dimenziója van X, Y, szélességét és magasságát. Az X és Y koordinátáit a 0,0 és 1,0 normalizált skálája régió bal felső Oxykiszolgáló koordinátáit jelölik. A szélesség és magasság határoz meg a 0,0 és 1,0 normalizált skálája terület méretét. Az aktuális verzió X, Y, szélességének és magasságának mindig rögzített 0, 0 és 1, 1. <br/>Ha meg van adva "sokszög", az a régió pontokon dimenziója van. <br/> |
| töredék |A metaadatok töredék nevű különböző részekre van darabolásos fel. Minden töredéke a start, a időtartama, a időszakának száma és a esemény (eke) tartalmaz. Nincsenek események a töredéket azt jelenti, hogy semmilyen adott kezdő időpontja és időtartama során észlelt. |
| Szögletes zárójelbe] |Minden egyes zárójel az esemény egy időköz jelöli. Üres szögletes az adott időköz azt jelenti, hogy semmilyen volt észlelhető. |
| Helyek |Az új belépési események alapján sorolja fel a helyre, ahol a mozgásérzékelési történt. Ez a pontosabb, mint a észlelési zónákat. |

A JSON kimeneti például

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

    …
## <a name="limitations"></a>Korlátozások
* A támogatott bemeneti videó formátumnak tartalmaznia kell MP4 MOV és WMV.
* Mozgásérzékelés álló háttér videók van optimalizálva. Az algoritmus összpontosít téves riasztásokat, például a megvilágítási érintő változtatások, valamint az árnyékok csökkentése.
* Néhány mozgásérzékelő – műszaki akadályok; miatt nem észlelhető például éjszaka stratégiai videók, félig átlátható és kis objektumok.

## <a name="net-sample-code"></a>.NET mintakód

A következő program bemutatja hogyan:

1. Hozzon létre egy eszközt, és adathordozó-fájl feltöltése az objektumba.
2. Hozzon létre egy feladatot a következő json-készletet tartalmazó konfigurációs fájl alapján Videós mozgásérzékelési észlelési feladatokkal: 
   
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
            task.OutputAssets.AddNew("My Video Motion Detectoion Output Asset", AssetCreationOptions.None);

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
[Az Azure Media Services mozgásérzékelő blog](https://azure.microsoft.com/blog/motion-detector-update/)

[Azure Media Services elemző áttekintése](media-services-analytics-overview.md)

[Az Azure Médiaelemzés bemutatók](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

