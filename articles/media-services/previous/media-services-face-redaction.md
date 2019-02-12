---
title: Az Azure Media Analytics arcok szereplők |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan szereplők az Azure media analytics arcokat.
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
ms.date: 02/09/2019
ms.author: juliako;
ms.openlocfilehash: 7fa1bec1b6f83b18684f6139bf6ac8db1ae9daec
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004893"
---
# <a name="redact-faces-with-azure-media-analytics-legacy"></a>Kivonás arcokat, az Azure Media Analytics (örökölt)
## <a name="overview"></a>Áttekintés
**Az Azure Media Redactor** van egy [Azure Media Analytics](media-services-analytics-overview.md) médiafeldolgozót. (pont) által kínált méretezhető arcszerkesztés a felhőben. Arcszerkesztés lehetővé teszi, hogy a videó módosíthatja annak érdekében, hogy arcának elmossa. Előfordulhat, hogy szeretné használni a face kivonási szolgáltatás közbiztonsági megfontolásokból és sajtóbeli híradásoknál. Néhány perc alatt több arcokat tartalmazó képanyag manuálisan szereplők órát is igénybe vehet, de ezzel a szolgáltatással a face kivonási folyamat néhány egyszerű lépéssel van szükség. További információkért lásd: [ez](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Ez a cikk kapcsolatos részleteket nyújt **Azure Media Redactor** , és bemutatja, hogyan használja a Media Services SDK a .NET-hez.

## <a name="face-redaction-modes"></a>Face kivonási módok
Arcfelismerési kivonási működik, arcokat található videó és előre és visszafelé időben, a face objektum mindkét nyomon követésére, hogy az azonos egyes is homályosan, a többi szögek is. Az automatikus kivonási folyamat túl összetett, és nem nem mindig a termék 100 %-a kívánt kimenetet, ezért a Media Analytics biztosítja a végső kimenet módosítása többféleképpen.

Egy teljesen automatikus mód kívül a rendszer két – pass munkafolyamat, amely lehetővé teszi, hogy a kijelölés/kivonási-selection található téglalapot keresztül azonosítók listáját. Is, hogy a keret módosítását a felügyeleti csomag egy tetszőleges JSON formátumban metaadatok fájlt használ. Ez a munkafolyamat van felosztva, amelyek **elemzés** és **Redact** módokat. Kombinálhatja a két mód, amely mindkét feladat fut egy feladat; egyetlen menetben Ez az üzemmód nevezik **kombinált**.

### <a name="combined-mode"></a>Kombinált mód
Ez hoz létre egy kivonatosan mp4 automatikusan szükséges bemeneti manuális nélkül.

| Fázis | Fájlnév | Megjegyzések |
| --- | --- | --- |
| Bemeneti objektuma |foo.bar |A videó WMV, MOV vagy MP4 formátumban |
| A bemeneti config |Feladat konfigurációs készlet |{'version':'1.0 ', "beállítások": {"mód": "kombinált"}} |
| Kimeneti adategység |foo_redacted.mp4 |Videókat a alkalmazni felismerhetetlenné |

#### <a name="input-example"></a>A bemeneti. példa:
[Ez a videó megtekintése](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fed99001d-72ee-4f91-9fc0-cd530d0adbbc%2FDancing.mp4)

#### <a name="output-example"></a>Példa a kimenetre:
[Ez a videó megtekintése](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fc6608001-e5da-429b-9ec8-d69d8f3bfc79%2Fdance_redacted.mp4)

### <a name="analyze-mode"></a>Mód elemzése
A **elemzése** pass a két-pass munkafolyamat vesz igénybe egy videó bemeneti hoz létre egy JSON-fájlt, a face helyek és a jpg-lemezképek az egyes észlelhető oldallal.

| Fázis | Fájlnév | Megjegyzések |
| --- | --- | --- |
| Bemeneti objektuma |foo.bar |A videó WMV, MPV vagy MP4 formátumban |
| A bemeneti config |Feladat konfigurációs készlet |{"verziójú": "1.0',"beállítások": {'mód':"elemzése"}} |
| Kimeneti adategység |foo_annotations.json |Jegyzet adatok face helyek JSON formátumban. Ez a felhasználó módosíthatja a mezők határoló felismerhetetlenné szerkeszthetik. Tekintse meg az alábbi mintát. |
| Kimeneti adategység |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Az egyes körülvágva jpg észlelhető oldallal, ahol a szám azt jelzi a címkeazonosító, a face |

#### <a name="output-example"></a>Példa a kimenetre:

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

### <a name="redact-mode"></a>Kivonás mód
A második fázis, a munkafolyamat-bemenet, amely egyetlen eszköz kombinálni kell nagyobb számú vesz igénybe.

Ez magában foglalja a elmossa azonosítók, az eredeti videó és a jegyzetek JSON listáját. Ebben a módban a jegyzetek a alkalmazni a a bemeneti videó felismerhetetlenné használ.

Az elemzési fázis kimenete nem tartalmazza az eredeti videó. A videó a Redact mód tevékenység bemeneti objektuma feltölti és az elsődleges fájllal kiválasztott van szükség.

| Fázis | Fájlnév | Megjegyzések |
| --- | --- | --- |
| Bemeneti objektuma |foo.bar |A videó WMV, MPV vagy MP4 formátumban. Ugyanaz, mint 1. lépés videó. |
| Bemeneti objektuma |foo_annotations.json |jegyzetek választható módosításokkal, fázis származó metaadatfájl. |
| Bemeneti objektuma |foo_IDList.txt (nem kötelező) |Választható új sor elválasztott face szereplők azonosítóinak listája. Ha üresen hagyja, ez az összes arcok életleníti. |
| A bemeneti config |Feladat konfigurációs készlet |{"verziójú": "1.0',"beállítások": {'mód':"szereplők"}} |
| Kimeneti adategység |foo_redacted.mp4 |Videókat a alkalmazni felismerhetetlenné magyarázó jelek alapján |

#### <a name="example-output"></a>Példa a kimenetre
Ez az egy kiválasztott egy azonosítójú IDList kimenete.

[Ez a videó megtekintése](http://ampdemo.azureedge.net/?url=http%3A%2F%2Freferencestream-samplestream.streaming.mediaservices.windows.net%2Fad6e24a2-4f9c-46ee-9fa7-bf05e20d19ac%2Fdance_redacted1.mp4)

Példa foo_IDList.txt
 
     1
     2
     3

## <a name="blur-types"></a>Elmossa típusok

Az a **kombinált** vagy **Redact** üzemmódban, 5 különböző életlenítés módban közül választhat a bemeneti JSON-konfigurációs keresztül: **Alacsony**, **Med**, **magas**, **Box**, és **fekete**. Alapértelmezés szerint **Med** szolgál.

Megtalálhatja a életlenítés típusú mintákat.

### <a name="example-json"></a>JSON. példa:

```json
    {'version':'1.0', 'options': {'Mode': 'Combined', 'BlurType': 'High'}}
```

#### <a name="low"></a>Alacsony

![Alacsony](./media/media-services-face-redaction/blur1.png)
 
#### <a name="med"></a>Med

![Med](./media/media-services-face-redaction/blur2.png)

#### <a name="high"></a>Magas

![Magas](./media/media-services-face-redaction/blur3.png)

#### <a name="box"></a>Box

![Box](./media/media-services-face-redaction/blur4.png)

#### <a name="black"></a>Fekete

![Fekete](./media/media-services-face-redaction/blur5.png)

## <a name="elements-of-the-output-json-file"></a>A kimenet JSON-fájl elemeinek

A kivonás felügyeleti csomag biztosít nagy pontosságú arcfelismeréssel és nyomon követése, mely képes akár 64 emberi arcok észlelése egy videó keret. Elülső arcok a legjobb eredmények elérése érdekében adja meg, a kiszolgálóoldali arcok közben és kis arcok (kisebb vagy egyenlő 24 x 24 képpont) kihívást.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET mintakód

A következő program mutat be, hogyan:

1. Hozzon létre egy objektumot, és a egy médiafájlt feltöltése az objektumba.
2. Hozzon létre egy feladatot a következő json-készletet tartalmazó konfigurációs fájl alapján face kivonási feladatokkal: 

    ```json
            {
                'version':'1.0',
                'options': {
                    'mode':'combined'
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
[Az Azure Media Services analitikai funkcióinak áttekintése](media-services-analytics-overview.md)

[Az Azure Médiaelemzés bemutatók](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

