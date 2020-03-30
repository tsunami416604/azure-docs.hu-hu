---
title: Eszköz kódolása a Media Encoder Standard használatával . Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan kódolható a .NET alkalmazással egy eszköz a Media Encoder Standard használatával.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 259e32d55f25c4a146b7ff358eb503763dd5fab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016583"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Eszköz kódolása a Media Encoder Standard használatával .  

A kódolási feladatok a Media Services egyik leggyakoribb feldolgozási műveletei. Kódolási feladatokat hozhat létre a médiafájlok egyik kódolásból a másikba történő konvertálásához. Kódoláskor használhatja a Media Services beépített médiakódolót. A Media Services-partnerek által biztosított kódolót is használhatja; harmadik féltől származó kódolók érhetők el az Azure Marketplace-en keresztül. 

Ez a cikk bemutatja, hogyan kódolhatja eszközeit a .NET segítségével a Media Encoder Standard (MES) segítségével. A Media Encoder Standard az [itt](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)leírt kódolókészletek egyikével van konfigurálva.

Javasoljuk, hogy a forrásfájlokat mindig adaptív mp4-es bitráta-készletbe kódolja, majd a [dinamikus csomagolás](media-services-dynamic-packaging-overview.md)segítségével konvertálja a készletet a kívánt formátumra. 

Ha a kimeneti eszköz titkosítva van, konfigurálnia kell az eszközkézbesítési szabályzatot. További információ: [Asset delivery policy configur.](media-services-dotnet-configure-asset-delivery-policy.md)

> [!NOTE]
> A MES olyan kimeneti fájlt hoz létre, amelynek neve a bemeneti fájl nevének első 32 karakterét tartalmazza. A név az előre beállított fájlban megadott adatokon alapul. Például "Fájlnév": "{Basename}_{Index}{Extension}". A(z) {Basename} karakterhelyébe a bemeneti fájlnév első 32 karaktere kerül.
> 
> 

### <a name="mes-formats"></a>MES-formátumok
[Formátumok és kodekek](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>MES-beállításkészletek
A Media Encoder Standard az [itt](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)leírt kódolókészletek egyikével van konfigurálva.

### <a name="input-and-output-metadata"></a>Bemeneti és kimeneti metaadatok
Amikor mes használatával kódol egy bemeneti eszközt (vagy eszközöket), akkor egy kimeneti eszközt kap a kódolási feladat sikeres befejezésekor. A kimeneti eszköz video-, hang-, miniatűrök, jegyzékfájl, stb a használt kódolási készlet alapján.

A kimeneti eszköz is tartalmaz egy fájlt a bemeneti eszköz metaadatait. A metaadat-XML-fájl neve a következő formátumú: <asset_id>_metadata.xml (például 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), ahol <asset_id> a bemeneti eszköz AssetId értéke. A bemeneti metaadat-XML sémáját [itt](media-services-input-metadata-schema.md)ismerteti.

A kimeneti eszköz is tartalmaz egy fájlt a kimeneti eszköz metaadatait. A metaadat-XML-fájl neve formátuma a következő: <source_file_name>_manifest.xml (például BigBuckBunny_manifest.xml). A kimeneti metaadat-XML sémáját [itt](media-services-output-metadata-schema.md)ismerteti.

Ha meg szeretné vizsgálni a két metaadatfájl egyikét, létrehozhat egy SAS-lokátort, és letöltheti a fájlt a helyi számítógépre. A SAS-lokátor létrehozásához és a Fájl letöltéséhez használható a Media Services .NET SDK Extensions használatával.

## <a name="download-sample"></a>Minta letöltése
Tudod, és fuss egy mintát, amely bemutatja, hogyan kell kódolni a MES [innen](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>.NET mintakód

A következő kódpélda a Media Services .NET SDK segítségével hajtja végre a következő feladatokat:

* Hozzon létre egy kódolási feladatot.
* Hivatkozás a Media Encoder Standard kódolóra.
* Adja meg az [Adaptív streamelési](media-services-autogen-bitrate-ladder-with-mes.md) készlet használatát. 
* Egyetlen kódolási feladat hozzáadása a feladathoz. 
* Adja meg a kódolandó bemeneti eszközt.
* Hozzon létre egy kimeneti eszközt, amely tartalmazza a kódolt eszközt.
* Adjon hozzá egy eseménykezelőt a feladat előrehaladásának ellenőrzéséhez.
* Küldje el a feladatot.

#### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztői környezetet, és népesítse be az app.config fájlt a kapcsolatadataival, ahogy azt a Media Services fejlesztése a [.NET fájlban leírta.](media-services-dotnet-how-to-use.md) 

#### <a name="example"></a>Példa 

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace MediaEncoderStandardSample
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

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);


            // Get an uploaded asset.
            var asset = _context.Assets.FirstOrDefault();

            // Encode and generate the output using the "Adaptive Streaming" preset.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the encoding details, using a string preset.
            // In this case "Adaptive Streaming" preset is used.
            ITask task = job.Tasks.AddNew("My encoding task",
                processor,
                "Adaptive Streaming",
                TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);
            // Add an output asset to contain the results of the job. 
            // This output is specified as AssetCreationOptions.None, which 
            // means the output asset is not encrypted. 
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
            job.Submit();
            job.GetExecutionProgressTask(CancellationToken.None).Wait();

            return job.OutputMediaAssets[0];
        }

        private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
        {
            Console.WriteLine("Job state changed event:");
            Console.WriteLine("  Previous state: " + e.PreviousState);
            Console.WriteLine("  Current state: " + e.CurrentState);
            switch (e.CurrentState)
            {
                case JobState.Finished:
                    Console.WriteLine();
                    Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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

## <a name="advanced-encoding-features-to-explore"></a>Speciális kódolási funkciók a felfedezéshez
* [Miniatűrök létrehozása](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Bélyegképek létrehozása kódolás közben](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Videók körülvágása kódolás közben](media-services-crop-video.md)
* [Kódolási készletek testreszabása](media-services-custom-mes-presets-with-dotnet.md)
* [Videó fedvényezése vagy vízjellel képpel](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések
[Miniatűr létrehozása a Media Encoder Standard használatával a .NET](media-services-dotnet-generate-thumbnail-with-mes.md)
[Media Services kódolással – áttekintés](media-services-encode-asset.md)

