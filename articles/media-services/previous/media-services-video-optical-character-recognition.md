---
title: Az Azure Media Analytics OCR-rel szöveg digitalizálhatók |} A Microsoft Docs
description: Az Azure Media Analytics OCR (optikai karakterfelismerés) lehetővé teszi a szöveges tartalom lévő szerkeszthetők, kereshető digitális szöveggé alakíthatja.  Ez lehetővé teszi, hogy az értelmezhető metaadatokat kivonása kinyert videó az adathordozó automatizálja.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 307c196e-3a50-4f4b-b982-51585448ffc6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: 91fad34073d7505c596bedfb6c93946ee7393dd7
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315130"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>A videofájlok szöveges tartalom digitális szöveggé alakíthatja az Azure Médiaelemzés használatával  
## <a name="overview"></a>Áttekintés
Ha a szöveges tartalom kinyerése a videofájlok és a egy szerkeszthetők, kereshető digitális szöveg generálása van szüksége, az Azure Media Analytics OCR (optikai karakterfelismerés) kell használnia. Az Azure Media processzor szöveges tartalom észleli a videó fájlokban, és a szöveges fájlokat generál. Optikai Karakterfelismerés lehetővé teszi, hogy az értelmezhető metaadatokat kivonása kinyert videó az adathordozó automatizálja.

Használt keresőmotor, egyszerűen a multimédiás index a szöveg, és a médiaindexelés, a tartalom. Ez különösen hasznos a magas értéket képviselő szöveges videó, például egy videó-felvételt vagy a képernyő-rögzítési diavetítés a bemutató. Az Azure OCR Médiafeldolgozót digitális szöveg van optimalizálva.

A **Azure Media OCR** médiafeldolgozót jelenleg előzetes verzióban érhető el.

Ez a cikk kapcsolatos részleteket nyújt **Azure Media OCR** , és bemutatja, hogyan használja a Media Services SDK a .NET-hez. További információért és példákért lásd: [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>Optikai Karakterfelismerés bemeneti fájlok
Videofájlok. Jelenleg a következő formátumok támogatottak: MP4 MOV és WMV.

## <a name="task-configuration"></a>Feladatkonfiguráció
A feladat konfigurációs (előre). A feladat létrehozásakor **Azure Media OCR**, meg kell adnia egy készlet használatával JSON vagy XML-konfiguráció. 

>[!NOTE]
>A OCR-motor mindössze egy lemezkép-régióhoz minimális 40 képpont maximális 32000 képpont mindkét magasságát/szélességét a egy érvényes bemeneti adatként.
>

### <a name="attribute-descriptions"></a>Attribútum leírása
| Attribútum neve | Leírás |
| --- | --- |
|AdvancedOutput| Ha AdvancedOutput true értékre állítja, a JSON-kimenet minden egyszavas (kifejezések és régiók) mellett a helyzeti adatok fogja tartalmazni. Ha nem szeretné, hogy ezek a részletek megtekintéséhez, a jelzőt "false" értékűre. Az alapértelmezett értéke FALSE (hamis). További információt [ebben a blogban](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/) talál.|
| Nyelv |(nem kötelező) ismerteti a keresendő szöveg nyelvét. A következők egyikét: Automatikus észlelés (alapértelmezett), arab, ChineseSimplified, ChineseTraditional, dán Cseh, holland, angol nyelven, finn, francia, német, görög, magyar, olasz, japán, koreai, norvég, lengyel, portugál, román, orosz, SerbianCyrillic, SerbianLatin , Szlovák, spanyol, svéd, török. |
| TextOrientation |(nem kötelező) ismerteti a tájolásának keresendő szöveg.  "Left" azt jelenti, hogy a felső részén csupa felé, a bal vannak mutatott.  Alapértelmezett szöveg (például, hogy egy könyv található) nem hívható meg "Be" objektumorientált.  A következők egyikét: AutoDetect (default), Up, Right, Down, Left. |
| TimeInterval |(nem kötelező) a mintavételi ráta ismerteti.  Az alapértelmezett érték 1/2 másodpercenként.<br/>JSON-formátumban – óó. Biztonságos tár szolgáltatás (alapértelmezett 00:00:00.500)<br/>XML-formátum – a W3C XSD időtartama primitív (alapértelmezett PT0.5) |
| DetectRegions |(nem kötelező) Adja meg a képkocka használandó szöveget észlelése belül más régiókba DetectRegion objektumok egy tömbjét.<br/>Egy DetectRegion objektum a következő négy egész érték jön létre:<br/>Left – a bal margó a képpont<br/>Felső – az a felső margó képpont<br/>Szélesség – szélességét (képpontban) régió<br/>Magasság – magasságát (képpontban) régió |

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
    <VideoOcrPreset xmlns:xsi=""https://www.w3.org/2001/XMLSchema-instance"" xmlns:xsd=""https://www.w3.org/2001/XMLSchema"" Version=""1.0"" xmlns=""https://www.windowsazure.com/media/encoding/Preset/2014/03"">
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

## <a name="ocr-output-files"></a>Optikai Karakterfelismerés kimeneti fájlok
Az optikai Karakterfelismerés médiafeldolgozót kimenete egy JSON-fájlt.

### <a name="elements-of-the-output-json-file"></a>A kimenet JSON-fájl elemeinek
A videós optikai Karakterfelismerés kimeneti idő szegmentált adatok nyújt a karaktereket a videóban található.  Használhatja például a nyelvi és a tájolás hone be a pontosan az, hogy Önt érdeklő elemzése szavakat. 

A kimenet tartalmazza a következő attribútumokat:

| Elem | Leírás |
| --- | --- |
| Időskála |"órajel során végbemenő" a videó másodpercenként |
| Eltolás |az időbélyegekhez időeltolódás. Videó API-k 1.0-s verziójában ez mindig 0 lesz. |
| Képkockasebesség |A képkockák másodpercenkénti a videó |
| Szélesség |szélességét (képpontban) a videó |
| Magasság |magasságát (képpontban) |
| Töredékek |időalapú adattömböket, amelybe a metaadatok adattömbökre osztotta videó tömbje |
| start |a "órajel során végbemenő" töredéket kezdete |
| időtartam |a "órajel során végbemenő" töredéket hossza |
| interval |a megadott töredék belül az egyes eseményeket időköz |
| események |Pole obsahující régiók |
| régió |szavak vagy kifejezések objektumot képviselő észlelt |
| language |egy adott régión belül észlelt szöveg nyelvét |
| Tájolás |egy adott régión belül észlelt a szöveg tájolása |
| sorok |sor szöveget észlelt egy adott régión belül tömbje |
| szöveg |a tényleges szövege |

### <a name="json-output-example"></a>JSON kimeneti példa
Az alábbi kimeneti példa tartalmazza a videó általános adatait, és több videó töredék. Minden videó töredék az tartalmaz minden régióhoz, a nyelv és a szöveg tájolása OCR felügyeleti csomag által észlelt. A régió is tartalmaz, minden szó sor ebben a régióban a sor szövege, a sor pozícióját és minden szót információkat (a word tartalmat, beosztás és magabiztosan) az ebben a sorban. Az alábbiakban látható egy példa, és helyezhetem el bizonyos megjegyzéseket beágyazott.

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

A következő program mutat be, hogyan:

1. Hozzon létre egy objektumot, és a egy médiafájlt feltöltése az objektumba.
2. Hozzon létre egy feladatot egy OCR-konfiguráció/készlet fájl.
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
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Az Azure Media Services analitikai funkcióinak áttekintése](media-services-analytics-overview.md)

