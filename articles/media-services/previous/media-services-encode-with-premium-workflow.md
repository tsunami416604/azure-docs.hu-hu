---
title: Speciális kódolás a Media Encoder Premium workflow segítségével | Microsoft dokumentumok
description: Ismerje meg, hogyan kódolhat a Media Encoder Premium workflow segítségével. A kódminták C# nyelven íródnak, és a Media Services SDK-t használják a .NET-hez.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: ca5de657ad45f53cff0cb01d5fe9cc412baf4533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792305"
---
# <a name="advanced-encoding-with-media-encoder-premium-workflow"></a>Speciális kódolás a Media Encoder Premium-munkafolyamattal
> [!NOTE]
> A cikkben tárgyalt Media Encoder Premium Workflow médiaprocesszor kínában nem érhető el.
>
>

## <a name="overview"></a>Áttekintés
A Microsoft Azure Media Services bemutatja a **Media Encoder Premium Workflow** médiaprocesszort. Ez a processzor előzetes kódolási lehetőségeket kínál a prémium igény szerinti munkafolyamatokhoz.

A következő témakörök a **Media Encoder Premium Workflow-val**kapcsolatos részleteket ismertetik:

* [A Media Encoder Premium Workflow által támogatott formátumok](media-services-premium-workflow-encoder-formats.md) – A Media **Encoder Premium Workflow**által támogatott fájlformátumok és kodekek.
* [Az Igény szerinti Azure-médiakódolók áttekintése és összehasonlítása](media-services-encode-asset.md) összehasonlítja a **Media Encoder Premium Workflow** és a Media **Encoder Standard**kódolási lehetőségeit.

Ez a cikk bemutatja, hogyan kódolható a **Media Encoder Premium munkafolyamat** használatával .

A **Media Encoder Premium munkafolyamat** kódolási feladataikülön konfigurációs fájlt, úgynevezett munkafolyamat-fájlt igényelnek. Ezek a fájlok .workflow kiterjesztéssel rendelkeznek, és a [Workflow Designer](media-services-workflow-designer.md) eszközzel jönnek létre.

Itt is beszerezheti [here](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows)az alapértelmezett munkafolyamat-fájlokat. A mappa tartalmazza a fájlok leírását is.

A munkafolyamat-fájlokat eszközként kell feltölteni a Media Services-fiókba, és ezt az eszközt át kell adni a kódolási feladatnak.

## <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztői környezetet, és népesítse be az app.config fájlt a kapcsolatadataival, ahogy azt a Media Services fejlesztése a [.NET fájlban leírta.](media-services-dotnet-how-to-use.md) 

## <a name="encoding-example"></a>Példa kódolásra

A következő példa bemutatja, hogyan kódolható a **Media Encoder Premium Workflow**.

A következő lépéseket hajtjuk végre:

1. Hozzon létre egy eszközt, és töltsön fel egy munkafolyamat-fájlt.
2. Hozzon létre egy eszközt, és töltsön fel egy forrásmédia-fájlt.
3. A "Media Encoder Premium Workflow" médiaprocesszor beszerezni.
4. Hozzon létre egy feladatot és egy feladatot.

    A legtöbb esetben a feladat konfigurációs karakterlánca üres (például a következő példában). Vannak speciális forgatókönyvek (amelyek megkövetelik, hogy állítsa le a futásidejű tulajdonságok dinamikusan) ebben az esetben adna egy XML-karakterláncot a kódolási feladat. Ilyen esetek például a következők: átfedés létrehozása, párhuzamos vagy szekvenciális adathordozó-varrás, feliratozás.
5. Adjon hozzá két bemeneti elemet a feladathoz.

   1. 1. – a munkafolyamat-eszköz.
   2. 2. - a videó eszköz.

      >[!NOTE]
      >A munkafolyamat-eszközt hozzá kell adni a feladathoz a médiaeszköz előtt.
      A feladat konfigurációs karakterláncának üresnek kell lennie.
   
6. Küldje el a kódolási feladatot.

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

## <a name="need-help"></a>Segítségre van szüksége?

Támogatási jegyet úgy nyithat meg, hogy az [Új támogatási kérelemre](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) navigál.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
