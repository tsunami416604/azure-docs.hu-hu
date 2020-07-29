---
title: Szöveg digitalizálása Azure Media Analytics OCR-sel | Microsoft Docs
description: Azure Media Analytics OCR (optikai karakterfelismerés) lehetővé teszi, hogy a videofájlok szövegét szerkeszthető, kereshető digitális szöveggé alakítsa át.  Ez lehetővé teszi, hogy automatizálja az értelmes metaadatok kinyerését az adathordozó Videójának jelének használatával.
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
ms.openlocfilehash: 11889bd6df0bcc9564c17fdaacc333df1d418660
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77918334"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>A videofájlok szöveges tartalmának digitális szöveggé alakításához használja a Azure Media Analytics.  

> [!NOTE]
> Az **Azure Media OCR** adathordozó-processzor ki lesz vonva. A lejárati dátumért tekintse meg az [örökölt összetevőkkel](legacy-components.md) foglalkozó témakört.

## <a name="overview"></a>Áttekintés
Ha szöveges tartalmat kell kibontania a videofájlokből, és szerkeszthető, kereshető digitális szöveget kell létrehoznia, akkor Azure Media Analytics OCR-t (optikai karakterfelismerést) kell használnia. Ez az Azure-beli adathordozó-feldolgozó észleli a videofájlok szöveges tartalmát, és szöveges fájlokat hoz létre a használathoz. Az OCR lehetővé teszi, hogy automatizálja az értelmezhető metaadatok kinyerését az adathordozó Videójának jelének használatával.

A keresőmotorokkal együtt használva egyszerűen indexelheti az adathordozót szöveg szerint, és javíthatja a tartalom felderíthetővé tételét. Ez rendkívül hasznos a nagy szöveges videóban, mint például a videó rögzítése vagy képernyőfelvétel készítése egy diavetítéses bemutatóról. Az Azure OCR-adathordozó processzora digitális szövegre van optimalizálva.

Az **Azure Media OCR** adathordozó-processzor jelenleg előzetes verzióban érhető el.

Ez a cikk részletesen ismerteti az **Azure Media OCR** -t, és bemutatja, hogyan használhatja azt a .net-hez készült Media Services SDK-val. További információért és példákért tekintse meg [ezt a blogot](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>OCR bemeneti fájlok
Videofájlok. Jelenleg a következő formátumok támogatottak: MP4, MOV és WMV.

## <a name="task-configuration"></a>Feladatkonfiguráció
Feladat konfigurációja (előre beállított). Amikor az **Azure Media OCR**használatával hoz létre egy feladatot, meg kell adnia egy konfigurációs beállításkészletet JSON vagy XML segítségével. 

>[!NOTE]
>Az OCR-motor csak olyan képterületet vesz igénybe, amely legalább 40 képpont méretű, a maximális 32000 képpont érték pedig érvényes bemenet mind a magasságban, mind a szélességben.
>

### <a name="attribute-descriptions"></a>Attribútumok leírása
| Attribútum neve | Description |
| --- | --- |
|AdvancedOutput| Ha a AdvancedOutput értéke TRUE (igaz), a JSON-kimenet minden egyes szó esetében tartalmaz pozíciós adatokat (a kifejezéseken és a régiókban is). Ha nem szeretné megtekinteni ezeket a részleteket, állítsa hamis értékre a jelzőt. Az alapértelmezett érték a hamis. További információkért tekintse meg [ezt a blogot](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Nyelv |(nem kötelező) annak a szövegnek a nyelvét írja le, amelynek meg kell keresnie. A következők egyike: automatikus észlelés (alapértelmezett), Arab, ChineseSimplified, ChineseTraditional, Cseh dán, holland, angol, finn, francia, német, görög, magyar, olasz, Japán, Koreai, norvég, lengyel, portugál, román, Orosz, SerbianCyrillic, SerbianLatin, szlovák, spanyol, svéd, Török. |
| TextOrientation |(nem kötelező) annak a szövegnek a tájolását írja le, amelynek meg kell keresnie.  A "bal" érték azt jelenti, hogy az összes betű tetejét a bal oldalon kell kimutatni.  Az alapértelmezett szöveg (például a könyvben található) a "fel" irányú.  A következők egyike: automatikus észlelés (alapértelmezett), fel, jobbra, le, balra. |
| TimeInterval |(nem kötelező) a mintavételezési sebességet ismerteti.  Az alapértelmezett érték minden 1/2 másodperc.<br/>JSON-formátum – óó: PP: mm. ÉER (alapértelmezett 00:00:00.500)<br/>XML Format – W3C XSD-időtartam primitív (alapértelmezett PT 0.5) |
| DetectRegions |választható DetectRegion-objektumok tömbje, amely a képkereten belüli régiókat határozza meg a szöveg észleléséhez.<br/>A következő négy egész értékből álló DetectRegion objektum:<br/>Left – képpont a bal oldali margótól<br/>Top – képpont a felső margótól<br/>Szélesség – a régió szélessége képpontban<br/>Magasság – a régió magassága képpontban |

#### <a name="json-preset-example"></a>JSON-beállításkészlet – példa

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

#### <a name="xml-preset-example"></a>XML-beállításkészlet – példa

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

## <a name="ocr-output-files"></a>OCR kimeneti fájlok
Az OCR-adathordozó processzorának kimenete egy JSON-fájl.

### <a name="elements-of-the-output-json-file"></a>A kimeneti JSON-fájl elemei
A videó OCR kimenete időben szegmentált adatokat biztosít a videóban található karakterekről.  Olyan attribútumokat használhat, mint például a nyelv vagy a tájolás, amely pontosan azokat a szavakat használja, amelyeknek az elemzését érdekli. 

A kimenet a következő attribútumokat tartalmazza:

| Elem | Description |
| --- | --- |
| Időskála |"ketyeg" a videó másodpercenként |
| Eltolás |időbélyeg időeltolódása. A video API-k 1,0-es verziójában ez mindig 0 lesz. |
| Képkockasebesség |Képkockák másodpercenkénti száma |
| szélesség |a videó szélessége képpontban |
| magasság |a videó magassága képpontban |
| Töredékek |azon időalapú adatdarabok tömbje, amelyekben a metaadatok feldarabolva vannak |
| start |töredék kezdő időpontja "ketyeg" |
| duration |töredék hossza a "ticks" kifejezésben |
| interval |az adott töredéken belüli események intervalluma |
| események |régiókat tartalmazó tömb |
| régió |az észlelt szavakat vagy kifejezéseket jelképező objektum |
| language |a régión belül észlelt szöveg nyelve |
| tájolás |a régión belül észlelt szöveg tájolása |
| sorok |egy régión belül észlelt szövegsorok tömbje |
| szöveg |a tényleges szöveg |

### <a name="json-output-example"></a>Példa JSON-kimenetre
A következő kimeneti példa az általános videó-információkat és számos videó-töredéket tartalmaz. Minden videó töredékben minden olyan régiót tartalmaz, amelyet az OCR MP a nyelvvel és a szöveges tájolással észlelt. A régió az ebben a régióban található összes szót is tartalmazza a sor szövegével, a sor pozícióját, valamint minden Word-információt (Word-tartalom, pozíció és megbízhatóság) ebben a sorban. A következőkben egy példa látható, és néhány Megjegyzés bekerült.

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

## <a name="net-sample-code"></a>.NET-mintakód

A következő program a következőket mutatja be:

1. Hozzon létre egy adategységet, és töltsön fel egy médiafájlt az eszközre.
2. Hozzon létre egy feladatot egy OCR-konfigurációval vagy egy előre beállított fájllal.
3. Töltse le a kimeneti JSON-fájlokat. 
   
#### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel a app.config fájlt a következő témakörben leírtak szerint: [Media Services fejlesztés a .net](media-services-dotnet-how-to-use.md)-tel. 

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

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

