---
title: Az Azure Media Analytics OCR szöveg digitize |} Microsoft Docs
description: Az Azure Media Analytics OCR (optikai karakter használata) videofájlok a szöveges tartalom átalakítása szerkeszthető, kereshető digitális szöveg teszi lehetővé.  Ez lehetővé teszi, hogy automatizálja a médiafájlt a videó jel jelentéssel bíró metaadatok kibontásával.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 307c196e-3a50-4f4b-b982-51585448ffc6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 03b9de7374880cdb2741821edae246bffaf3f921
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
ms.locfileid: "29738041"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Azure Médiaelemzés használatával videofájlok a szöveges tartalom átalakítása digitális szöveg
## <a name="overview"></a>Áttekintés
Szöveges tartalom kibontása a videofájlok és létrehozni egy szerkeszthető, kereshető digitális szöveget kell, ha az Azure Media Analytics OCR (optikai karakter felismerés) kell használnia. Az Azure Media processzor szöveges tartalom észleli a videofájlok lejátszását, és állít elő, a szöveg fájljait. OCR lehetővé teszi a médiafájlt a videó jel jelentéssel bíró metaadatok kibontásával automatizálását.

Ha együtt használják a keresőprogramok találatait, egyszerűen médiatartalmak index a szöveg, és találni az Önt érdeklő a tartalom javítása érdekében. Ez különösen fontos például videó-felvételt vagy diavetítési bemutató képernyőfelvétel a magas szöveges videó. Az Azure OCR Media processzor digitális szöveg van optimalizálva.

A **Azure Media OCR** media processzor jelenleg előzetes verzió.

Ez a cikk részletezi kapcsolatos **Azure Media OCR** és a .NET-keretrendszerhez készült Media Services SDK-val való használatát ismerteti. További információk és példák: [ebben a blogban](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>A bemeneti fájlok OCR
Videofájlok lejátszását. Jelenleg a következő formátumok használhatók: MP4 MOV és WMV.

## <a name="task-configuration"></a>A feladat konfigurációja
A feladat konfigurációja (beállítás). A feladat létrehozásakor **Azure Media OCR**, meg kell adnia egy készlet használatával JSON vagy XML-konfiguráció. 

>[!NOTE]
>Csak a OCR motor fogadja egy lemezkép a régióban, minimális 40 képpont maximális 32000 képpont a mindkét magasság/szélesség egy érvényes bemeneti adatként.
>

### <a name="attribute-descriptions"></a>Az attribútumok leírása
| Attribútum neve | Leírás |
| --- | --- |
|AdvancedOutput| Ha AdvancedOutput igaz értékre van beállítva, a JSON-kimenetét fogja tartalmazni pozicionális adatainak minden egyszavas (mellett kifejezések és régiókban). Ha nem szeretné, hogy ezek az adatok megjelenítéséhez, jelzőt FALSE értékre kell beállítani. Az alapértelmezett értéke hamis. További információkért lásd: [ebben a blogban](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Nyelv |(választható) ismerteti a keresendő szöveg nyelvét. A következők egyikét: automatikus felismerés (alapértelmezett), arab, ChineseSimplified, ChineseTraditional, dán Cseh, holland, angol, finn, francia, német, görög, magyar, olasz, japán, koreai, lengyel, lengyel, portugál, román, orosz, SerbianCyrillic, SerbianLatin, szlovák, spanyol, svéd, török. |
| TextOrientation |(választható) a keresendő szöveg irányának ismerteti.  "Left" azt jelenti, hogy az összes felső felé, a bal vannak változó.  Alapértelmezett szöveg (például, amely itt található: egy könyv) hívható "Mentés" objektumorientált.  A következők egyikét: AutoDetect (alapértelmezett), akár, jobbra, lefelé, balra. |
| TimeInterval |a mintavételi ráta (nem kötelező) ismertetése  Alapértelmezett érték 1/2 másodpercenként.<br/>JSON formátumban – óó: pp:. Biztonságos tár szolgáltatás (alapértelmezett 00:00:00.500)<br/>XML-formátum – a W3C XSD időtartama egyszerű (alapértelmezett PT0.5) |
| DetectRegions |(választható) Adja meg, amelyben a szöveg észlelése a videó kerethez régiók DetectRegion objektumokból álló tömb.<br/>Egy DetectRegion objektum a következő négy egész érték történik:<br/>Left – a bal margó a képpont<br/>Felső – az a felső margó képpontban megadva<br/>Szélesség – szélessége képpontban régió<br/>Magasság – magassága képpontban régió |

#### <a name="json-preset-example"></a>Előre definiált JSON-példa

```json
    {
        "Version":1.0, 
        "Options": 
        {
            "AdvancedOutput":"true",
            "Language":"English", 
            "TimeInterval":"00:00:01.5",
            "TextOrientation":"Up",
            "DetectRegions": [
                    {
                       "Left": 10,
                       "Top": 10,
                       "Width": 100,
                       "Height": 50
                    }
             ]
        }
    }
```

#### <a name="xml-preset-example"></a>Előre definiált XML-példa

```xml
    <?xml version=""1.0"" encoding=""utf-16""?>
    <VideoOcrPreset xmlns:xsi=""http://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""http://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""http://www.windowsazure.com/media/encoding/Preset/2014/03"">
      <Options>
         <AdvancedOutput>true</AdvancedOutput>
         <Language>English</Language>
         <TimeInterval>PT1.5S</TimeInterval>
         <DetectRegions>
             <DetectRegion>
                   <Left>10</Left>
                   <Top>10</Top>
                   <Width>100</Width>
                   <Height>50</Height>
            </DetectRegion>
       </DetectRegions>
       <TextOrientation>Up</TextOrientation>
      </Options>
    </VideoOcrPreset>
```

## <a name="ocr-output-files"></a>OCR kimeneti fájlok
A OCR media processzor eredménye egy JSON-fájlt.

### <a name="elements-of-the-output-json-file"></a>A kimeneti JSON-fájl elemeinek
A videó OCR kimeneti idő szegmentált adatokat biztosít a videó megtalálható karaktere.  Segítségével például nyelvi vagy tájolás attribútumok hone-található meg pontosan a szavakat, hogy érdekli elemzése. 

A kimenet tartalmazza a következő attribútumokat:

| Elem | Leírás |
| --- | --- |
| időskálára |"ticks" videó másodpercenként |
| Eltolás |az időbélyegekhez időeltolódás. Videó API-k 1.0-s verziójában az mindig 0 lesz. |
| képkockasebességhez |A videó képkockasebessége |
| width |szélesség képpontban videó |
| Magassága |magasságát képpontban |
| töredék |időalapú adattömböket, amelybe a metaadatok darabolásos van videó tömbje |
| start |a "ticks" töredéket kezdete |
| Időtartam |a "ticks" töredéket hossza |
| interval |a megadott töredéke belül mindegyik esemény időköz |
| események |régiók tartalmazó tömb |
| régió |szavakat vagy kifejezéseket objektumot képviselő észlelt |
| nyelv |a szöveg észlelt régión belül |
| Tájolás |a szöveg régión belül észlelt irányának |
| sorok |a tömb sornyi szöveget észlelt régión belül |
| Szöveg |a tényleges szöveg |

### <a name="json-output-example"></a>JSON kimeneti példa
A következő kimeneti példa az általános videó információkat és több videó töredék tartalmazza. Minden videó töredékben szereplő minden egyes régió, a nyelvet és a szöveg irányú OCR mp észlelt tartalmaz. A régió minden word sor ebben a régióban a sor szövege, a sor helye és a sor minden word információkkal (word tartalmat, pozíció és abban, hogy) is tartalmaz. A következő egy példa, és szeretnék bizonyos megjegyzéseket beágyazott.

```json
    {
        "version": 1, 
        "timescale": 90000, 
        "offset": 0, 
        "framerate": 30, 
        "width": 640, 
        "height": 480,  // general video information
        "fragments": [
            {
                "start": 0, 
                "duration": 180000, 
                "interval": 90000,  // the time information about this fragment
                "events": [
                    [
                       { 
                            "region": { // the detected region array in this fragment 
                                "language": "English",  // region language
                                "orientation": "Up",  // text orientation
                                "lines": [  // line information array in this region, including the text and the position
                                    {
                                        "text": "One Two", 
                                        "left": 10, 
                                        "top": 10, 
                                        "right": 210, 
                                        "bottom": 110, 
                                        "word": [  // word information array in this line
                                            {
                                                "text": "One", 
                                                "left": 10, 
                                                "top": 10, 
                                                "right": 110, 
                                                "bottom": 110, 
                                                "confidence": 900
                                            }, 
                                            {
                                                "text": "Two", 
                                                "left": 110, 
                                                "top": 10, 
                                                "right": 210, 
                                                "bottom": 110, 
                                                "confidence": 910
                                            }
                                        ]
                                    }
                                ]
                            }
                        }
                    ]
                ]
            }
        ]
    }
```

## <a name="net-sample-code"></a>.NET mintakód

A következő program bemutatja hogyan:

1. Hozzon létre egy eszközt, és adathordozó-fájl feltöltése az objektumba.
2. Hozzon létre egy feladatot OCR konfigurációs/előre definiált fájllal.
3. A kimeneti JSON-fájlok letöltésére. 
   
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

namespace OCR
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

            // Run the OCR job.
            var asset = RunOCRJob(@"C:\supportFiles\OCR\presentation.mp4",
                                        @"C:\supportFiles\OCR\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\OCR\Output");
        }

        static IAsset RunOCRJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
                "My OCR Input Asset",
                AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My OCR Job");

            // Get a reference to Azure Media OCR.
            string MediaProcessorName = "Azure Media OCR";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My OCR Task",
                processor,
                configuration,
                TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My OCR Output Asset", AssetCreationOptions.None);

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

