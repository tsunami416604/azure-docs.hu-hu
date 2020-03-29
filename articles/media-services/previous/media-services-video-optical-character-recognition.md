---
title: Szöveg digitalizálása az Azure Media Analytics OCR segítségével | Microsoft dokumentumok
description: Az Azure Media Analytics optikai vezérlőneve (optikai karakterfelismerés) lehetővé teszi a videofájlok szöveges tartalmának szerkeszthető, kereshető digitális szöveggé alakítását.  Ez lehetővé teszi az értelmes metaadatok kinyerését a média videojeléből.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918334"
---
# <a name="use-azure-media-analytics-to-convert-text-content-in-video-files-into-digital-text"></a>Videofájlok szöveges tartalmának digitális szöveggé alakítása az Azure Media Analytics segítségével  

> [!NOTE]
> Az **Azure Media OCR** médiaprocesszor a rendszer megszűnik. A kivonási dátum, lásd a [régi összetevők](legacy-components.md) témakörben.

## <a name="overview"></a>Áttekintés
Ha ki kell bontania a szöveges tartalmat a videofájlokból, és szerkeszthető, kereshető digitális szöveget kell létrehoznia, az Azure Media Analytics OCR-t (optikai karakterfelismerés) kell használnia. Ez az Azure Media Processor észleli a videofájlok szöveges tartalmát, és szöveges fájlokat hoz létre az Ön számára. Az OCR lehetővé teszi az értelmes metaadatok kinyerését a média videojeléből.

Ha egy keresőmotorral együtt használja, egyszerűen indexelheti a médiafájlokat szöveg szerint, és javíthatja a tartalom felderíthetőségét. Ez rendkívül hasznos a rendkívül szöveges videó, mint egy videofelvétel vagy képernyő-capture egy diavetítés bemutató. Az Azure OCR médiaprocesszor digitális szövegre van optimalizálva.

Az **Azure Media OCR** médiaprocesszor jelenleg előzetes verzióban van.

Ez a cikk az **Azure Media OCR-ről** nyújt részleteket, és bemutatja, hogyan használható a Media Services SDK-val a . További információkért és példákért lásd [ezt a blogot](https://azure.microsoft.com/blog/announcing-video-ocr-public-preview-new-config/).

## <a name="ocr-input-files"></a>OCR-bemeneti fájlok
Videofájlok. Jelenleg a következő formátumok támogatottak: MP4, MOV és WMV.

## <a name="task-configuration"></a>Feladatkonfiguráció
Feladat konfigurációja (készlet). Amikor feladatot hoz létre az **Azure Media OCR**használatával, meg kell adnia egy konfigurációs készletet JSON vagy XML használatával. 

>[!NOTE]
>Az OCR-motor csak egy legalább 40 képpontból és legfeljebb 32000 képpontig rendelkező képterületet vesz fel érvényes bemenetként mindkét magasságban/szélességben.
>

### <a name="attribute-descriptions"></a>Attribútumleírások
| Attribútum neve | Leírás |
| --- | --- |
|AdvancedOutput (Speciális kimenet)| Ha az AdvancedOutput értéket igaz értékre állítja, a JSON-kimenet minden egyes szóhoz tartalmaz helymeghatározási adatokat (a kifejezéseken és a régiókon kívül). Ha nem szeretné látni ezeket az adatokat, állítsa a jelzőt hamisra. Az alapértelmezett érték a hamis. További információ: [ez a blog](https://azure.microsoft.com/blog/azure-media-ocr-simplified-output/).|
| Nyelv |(nem kötelező) leírja a szöveg nyelvét, amelyet keresni kell. A következők egyike: AutoDetect (alapértelmezett), arab, kínaiegyszerűsített, hagyományos, cseh dán, holland, angol, finn, francia, német, görög, magyar, olasz, japán, koreai, norvég, lengyel, portugál, román, orosz, Szerbcirill, szerblatin, szlovák, spanyol, svéd, török. |
| Szövegirányolás |(nem kötelező) leírja a szöveg tájolását, amelyet keresni kell.  A "bal" azt jelenti, hogy az összes betű teteje balra mutat.  Az alapértelmezett szöveg (mint ami egy könyvben található) "Up" orientáltnak nevezhető.  A következők egyike: Automatikus észlelés (alapértelmezett), Fel, Jobbra, Le, Balra. |
| Időintervallum |(nem kötelező) leírja a mintavételi arányt.  Az alapértelmezett érték 1/2 másodpercenként.<br/>JSON formátum – ÓÓ:pp:ss. SSS (alapértelmezett 00:00:00.500)<br/>XML formátum – W3C XSD időtartam primitív (alapértelmezett PT0.5) |
| Régiók észlelése |(nem kötelező) DetectRegion objektumok tömbje, amely a videokereten belül azokat a területeket határozza meg, amelyekben szöveget észlel.<br/>A DetectRegion objektum a következő négy egész értékből készül:<br/>Balra – képpontok a bal margótól<br/>Felül – képpontok a felső margóról<br/>A terület szélessége képpontban<br/>Magasság – a terület magassága képpontban |

#### <a name="json-preset-example"></a>JSON előre beállított példa

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

#### <a name="xml-preset-example"></a>Példa XML-készletre

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
Az OCR médiaprocesszor kimenete JSON-fájl.

### <a name="elements-of-the-output-json-file"></a>A kimeneti JSON-fájl elemei
A Video OCR kimenet időszegmentált adatokat szolgáltat a videóban található karakterekről.  Használhatja attribútumok, mint a nyelv vagy orientáció élesíteni-ban pontosan a szavakat, hogy érdekli az elemzés. 

A kimenet a következő attribútumokat tartalmazza:

| Elem | Leírás |
| --- | --- |
| Időskála |"kullancsok" másodpercenként a videó |
| Eltolás |időeltolódás az időbélyegek esetében. A videoAPI-k 1.0-s verziójában ez mindig 0 lesz. |
| Képkockasebesség |Képkockák másodpercenként a videó |
| szélesség |a videó szélessége képpontban |
| magasság |a videó magassága képpontban |
| Töredékek |időalapú videotömbök tömbje, amelyekbe a metaadatok at darabolják |
| start |egy töredék kezdő időpontja a "kullancsokban" |
| duration |a "kullancsok" töredékének hossza |
| interval |az adott töredéken belüli minden esemény időköze |
| események |régiókat tartalmazó tömb |
| régió |észlelt szavakat vagy kifejezéseket ábrázoló objektum |
| language |a régión belül észlelt szöveg nyelve |
| Tájolás |a területen belül észlelt szöveg tájolása |
| Vonalak |egy területen belül észlelt szövegsorok tömbje |
| szöveg |a tényleges szöveg |

### <a name="json-output-example"></a>Példa JSON kimenetre
A következő kimeneti példa az általános videóinformációkat és számos videotöredéket tartalmazza. Minden videótöredékben minden régiót tartalmaz, amelyet az OCR MP a nyelvvel és a szöveg tájolásával észlel. A terület a régió minden szósorát tartalmazza a sor szövegével, a sor pozíciójával és minden szóinformációval (szótartalom, pozíció és megbízhatóság) ebben a sorban. A következő egy példa, és tettem néhány megjegyzést inline.

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

A következő program bemutatja, hogyan:

1. Hozzon létre egy eszközt, és töltsön fel egy médiafájlt az eszközbe.
2. Hozzon létre egy feladatot egy OCR konfigurációs/előre beállított fájllal.
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
[Az Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

