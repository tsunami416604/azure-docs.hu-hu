---
title: Speciális kódolás a Media Encoder Premium munkafolyamat |} A Microsoft Docs
description: Ismerje meg, hogyan kódolás a Media Encoder Premium munkafolyamat. Kódminták nyelven írták C# , és használja a Media Services SDK a .NET-hez.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 7b91f63057fa8b781fdfab17e0837a9f5547c1eb
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55988034"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Speciális kódolás a Media Encoder Premium munkafolyamat
> [!NOTE]
> Kínában nem érhető el ebben a cikkben tárgyalt Media Encoder Premium munkafolyamat médiafeldolgozót.
>
>

Ha kérdése van a prémium szintű kódoló, e-mailben mepd@microsoft.com.

## <a name="overview"></a>Áttekintés
A Microsoft Azure Media Services-ban bevezetjük az **Media Encoder Premium munkafolyamat** médiafeldolgozót. A processzor ajánlatok speciális kódolási funkciókat a prémium szintű igény szerinti munkafolyamatok.

A következő témakörök szerkezeti kapcsolatos részleteket **Media Encoder Premium munkafolyamat**:

* [A Media Encoder Premium munkafolyamat által támogatott formátumok](media-services-premium-workflow-encoder-formats.md) – foglalkozik a fájl formázza és kodekei által támogatott **Media Encoder Premium munkafolyamat**.
* [Áttekintése és összehasonlítása az Azure igény szerinti médiakódolók](media-services-encode-asset.md) hasonlítja össze a kódolási képességeit **Media Encoder Premium munkafolyamat** és **Media Encoder Standard**.

Ez a cikk bemutatja, hogyan kódolás a **Media Encoder Premium munkafolyamat** .NET használatával.

Kódolási feladatok a **Media Encoder Premium munkafolyamat** egy külön konfigurációs fájlt, egy munkafolyamatfájlt nevű igényelnek. Ezek a fájlok .workflow kiterjesztést és használatával jön létre a [munkafolyamat-tervezővel](media-services-workflow-designer.md) eszközt.

Is használhatja az alapértelmezett munkafolyamat-fájlok [Itt](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows). A mappa leírását, ezeket a fájlokat is tartalmaz.

A munkafolyamat-fájlokat kell feltölteni a Media Services-fiók eszközként, és az adategység kell átadni a kódolási feladat.

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint. 

## <a name="encoding-example"></a>Kódolási példa

Az alábbi példa bemutatja, hogyan kódolás a **Media Encoder Premium munkafolyamat**.

Az alábbi lépéseket kell végrehajtani:

1. Hozzon létre egy objektumot, és a egy munkafolyamat-fájl feltöltése.
2. Hozzon létre egy objektumot, és töltse fel a forrás médiafájl.
3. A "Media Encoder Premium munkafolyamat" médiafeldolgozót beolvasása.
4. Hozzon létre egy feladatot és a egy feladatot.

    A legtöbb esetben a konfigurációs karakterláncból, a tevékenység az üres (például a következő példában). Néhány speciális esetben (igénylő futásidejű tulajdonságok dinamikusan állítható be), amely esetben biztosítja, a kódolási feladat XML-karakterlánc. Példák ilyen forgatókönyvek például: egy átmeneti területre, párhuzamos és soros vágása, a feliratozás adathordozó létrehozása.
5. Két bemeneti eszközeinek adja hozzá a feladatot.

    1. 1. – a munkafolyamat-objektumot.
    2. 2. – a videó eszköz.

    >[!NOTE]
    >A feladat a médiaobjektum előtt hozzá kell adni a munkafolyamat-objektumot.
   Lehet, hogy erre a célra a konfigurációs karakterlánc üres.
   
6. A kódolási feladat elküldéséhez.

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderPremiumWorkflowSample
{
    class Program
    {
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

        private static readonly string _supportFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _workflowFilePath =
            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");

        private static readonly string _singleMP4InputFilePath =
            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");

        static void Main(string[] args)
        {

            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
            var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
            IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset);

        }

        static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
        {
            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
            var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);

            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading {0}", assetFile.Name);

            return asset;
        }

        static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Premium Workflow encoding job");
            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

            // Create a task with the encoding details, using a string preset.
            ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                processor,
                "",
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(workflow);
            task.InputAssets.Add(video); // we add one asset
                                         // Add an output asset to contain the results of the job.
                                         // This output is specified as AssetCreationOptions.None, which
                                         // means the output asset is not encrypted.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Use the following event handler to check job progress.  
            job.StateChanged += new
                    EventHandler<JobStateChangedEventArgs>(StateChanged);

            // Launch the job.
            job.Submit();

            // Check job execution and wait for job to finish.
            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
            progressJobTask.Wait();

            // If job state is Error the event handling
            // method for job progress should log errors.  Here we check
            // for error state and exit if needed.
            if (job.State == JobState.Error)
            {
                throw new Exception("\nExiting method due to job error.");
            }

            return job.OutputMediaAssets[0];
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
        private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
        {
            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
            ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

            if (processor == null)
                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

            return processor;
        }
    }
}
```

Ha kérdése van a prémium szintű kódoló, e-mailben mepd@microsoft.com.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
