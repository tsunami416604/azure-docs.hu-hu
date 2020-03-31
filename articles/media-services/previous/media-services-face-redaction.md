---
title: Arcok kivonása az Azure Media Analytics szolgáltatással | Microsoft dokumentumok
description: Az Azure Media Redactor egy Azure Media Analytics-médiaprocesszor, amely skálázható arckivonást kínál a felhőben. Ez a cikk bemutatja, hogyan lehet kipirosítani arcok az Azure media analytics.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6a1b7a76ef1efda51f09ac733b3d434235ff40ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900301"
---
# <a name="redact-faces-with-azure-media-analytics"></a>Arcok kivonása az Azure Media Analytics segítségével 
## <a name="overview"></a>Áttekintés
**Az Azure Media Redactor** egy [Azure Media Analytics-médiaprocesszor](media-services-analytics-overview.md) (MP), amely skálázható arckivonást kínál a felhőben. Az arckivonás lehetővé teszi a videó módosítását, hogy elhomályosíthassa a kiválasztott személyek arcát. Érdemes lehet használni a face redaction szolgáltatás a közbiztonság és a hírmédia forgatókönyvek. Néhány percnyi, több arcot tartalmazó felvétel manuális kivonása órákba telhet, de ezzel a szolgáltatással az arckivonási folyamat csak néhány egyszerű lépést igényel. További információ: [ebbe a](https://azure.microsoft.com/blog/azure-media-redactor/) blogba.

Ez a cikk az **Azure Media Redactor** részleteit tartalmazza, és bemutatja, hogyan használható a Media Services SDK-val a .

## <a name="face-redaction-modes"></a>Arckivonási módok
Az arc kivonása úgy működik, hogy a videó minden képkockában észleli az arcokat, és nyomon követi az arctárgyat előre és hátra az időben, így ugyanaz az egyén más szögekből is elmosódik. Az automatikus kivonási folyamat összetett, és nem mindig termel 100%-a kívánt kimenetet, ezért a Media Analytics néhány módot kínál a végső kimenet módosítására.

A teljesen automatikus mód mellett van egy kétmenetes munkafolyamat is, amely lehetővé teszi a talált lapok kiválasztását/törlését az azonosítók listájával. A keretenkénti tetszőleges korrekciókhoz az mp egy Metaadatfájlt használ JSON formátumban. Ez a munkafolyamat **elemzési** és **kivonási** módokra van felosztva. A két módot egy lépésben egyesítheti, amely mindkét feladatot egy feladatban futtatja; ezt a módot Kombinált módnak **nevezzük.**

### <a name="combined-mode"></a>Kombinált mód
Ez automatikusan, manuális bevitel nélkül eredményez i4-es vezérlést.

| Fázis | Fájlnév | Megjegyzések |
| --- | --- | --- |
| Bemeneti eszköz |foo.bar |Videó WMV, MOV vagy MP4 formátumban |
| Bemeneti konfiguráció |Feladatkonfigurációs készlet |{'version':'1.0', 'options': {'mode':'combined'}} |
| Kimeneti eszköz |foo_redacted.mp4 |Videó elmosódott alkalmazással |

#### <a name="input-example"></a>Példa bevitelre:
[a videó megtekintése](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Kimeneti példa:
[a videó megtekintése](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Elemzési mód
A kétlépéses munkafolyamat **elemzési** fázisa videobemenetet vesz fel, és létrehoz egy JSON-fájlt az archelyekről, és jpg képeket az egyes észlelt arcról.

| Fázis | Fájlnév | Megjegyzések |
| --- | --- | --- |
| Bemeneti eszköz |foo.bar |Videó WMV, MPV vagy MP4 formátumban |
| Bemeneti konfiguráció |Feladatkonfigurációs készlet |{'version':'1.0', 'options': {'mode':'analyze'}} |
| Kimeneti eszköz |foo_annotations.json |A névhelyek jegyzetelési adatai JSON formátumban. Ezt a felhasználó szerkesztheti az elmosódási határolókeret módosításához. Lásd az alábbi mintát. |
| Kimeneti eszköz |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Minden észlelt lap levágott jpgje, ahol a szám az arc címkéjét jelzi |

#### <a name="output-example"></a>Kimeneti példa:

```json
    {
      "version": 1,
      "timescale": 24000,
      "offset": 0,
      "framerate": 23.976,
      "width": 1280,
      "height": 720,
      "fragments": [
        {
          "start": 0,
          "duration": 48048,
          "interval": 1001,
          "events": [
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [],
            [
              {
                "index": 13,
                "id": 1138,
                "x": 0.29537,
                "y": -0.18987,
                "width": 0.36239,
                "height": 0.80335
              },
              {
                "index": 13,
                "id": 2028,
                "x": 0.60427,
                "y": 0.16098,
                "width": 0.26958,
                "height": 0.57943
              }
            ],

    … truncated
```

### <a name="redact-mode"></a>Kivonási mód
A munkafolyamat második lépése nagyobb számú bemenetet vesz igénybe, amelyeket egyetlen eszközbe kell egyesíteni.

Ez magában foglalja az elmosódott azonosítók listáját, az eredeti videót és a JSON-jegyzeteket. Ez a mód a jegyzetek segítségével alkalmazza az elmosódást a bemeneti videón.

Az Elemzés ibérlet kimenete nem tartalmazza az eredeti videót. A videót fel kell tölteni a bemeneti eszköz a Redact mód feladat és az elsődleges fájl ként kiválasztott.

| Fázis | Fájlnév | Megjegyzések |
| --- | --- | --- |
| Bemeneti eszköz |foo.bar |Videó WMV, MPV vagy MP4 formátumban. Ugyanaz a videó, mint az 1. |
| Bemeneti eszköz |foo_annotations.json |a metaadatfájlt az első fázisból, opcionális módosításokkal. |
| Bemeneti eszköz |foo_IDList.txt (nem kötelező) |Választható új sorelválasztott arcazonosítók listája a kivonáshoz. Ha üresen hagyja, ez elmossa az összes arcot. |
| Bemeneti konfiguráció |Feladatkonfigurációs készlet |{'version':'1.0', 'options': {'mode':'redact'}} |
| Kimeneti eszköz |foo_redacted.mp4 |Videó elmosódott szöveggel a jegyzetek alapján |

#### <a name="example-output"></a>Példa kimenetre
Ez egy idlista kimenete, amelyen egy azonosító van kiválasztva.

[a videó megtekintése](https://ampdemo.azureedge.net/?url=https%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Példa foo_IDList.txt fájlra
 
     1
     2
     3

## <a name="blur-types"></a>Életlenítési típusok

A **Kombinált** vagy **a Redact** módban 5 különböző elmosódási mód közül választhat a JSON bemeneti konfiguráción keresztül: **Alacsony**, **Med**, **Magas**, **Doboz**és **Fekete**. Alapértelmezés szerint **med** van használva.

Az életlenítési típusok mintáit alább találja.

### <a name="example-json"></a>Példa JSON:

```json
    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}
```

#### <a name="low"></a>Alacsony

![Alacsony](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>Med

![Med](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Magasság

![Magasság](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Fekete

![Fekete](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>A kimeneti JSON-fájl elemei

A Redaction MP nagy pontosságú archely-felismerést és -követést biztosít, amely akár 64 emberi arcot is képes észlelni egy videoképkockában. Az elülső lapok biztosítják a legjobb eredményt, míg az oldallapok és a kis (legfeljebb 24 x24 képpont) lapok kihívást jelentenek.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET mintakód

A következő program bemutatja, hogyan:

1. Hozzon létre egy eszközt, és töltsön fel egy médiafájlt az eszközbe.
2. Hozzon létre egy feladatot arckivonási feladattal egy konfigurációs fájl alapján, amely a következő jsonkészletet tartalmazza: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
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

namespace FaceRedaction
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

            // Run the FaceRedaction job.
            var asset = RunFaceRedactionJob(@"C:\supportFiles\FaceRedaction\SomeFootage.mp4",
                        @"C:\supportFiles\FaceRedaction\config.json");

            // Download the job output asset.
            DownloadAsset(asset, @"C:\supportFiles\FaceRedaction\Output");
        }

        static IAsset RunFaceRedactionJob(string inputMediaFilePath, string configurationFile)
        {
            // Create an asset and upload the input media file to storage.
            IAsset asset = CreateAssetAndUploadSingleFile(inputMediaFilePath,
            "My Face Redaction Input Asset",
            AssetCreationOptions.None);

            // Declare a new job.
            IJob job = _context.Jobs.Create("My Face Redaction Job");

            // Get a reference to Azure Media Redactor.
            string MediaProcessorName = "Azure Media Redactor";

            var processor = GetLatestMediaProcessorByName(MediaProcessorName);

            // Read configuration from the specified file.
            string configuration = File.ReadAllText(configurationFile);

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("My Face Redaction Task",
            processor,
            configuration,
            TaskOptions.None);

            // Specify the input asset.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("My Face Redaction Output Asset", AssetCreationOptions.None);

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

## <a name="next-steps"></a>További lépések

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Az Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

[Az Azure Media Analytics bemutatói](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

