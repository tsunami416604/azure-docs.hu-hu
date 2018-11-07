---
title: Adategység kódolása a Media Encoder Standard .NET használatával |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan adategység kódolása a Media Encoder Strandard használatával a .NET használatával.
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
ms.date: 10/30/2018
ms.author: juliako;anilmur
ms.openlocfilehash: 426ebb90a0d7716b14664f874db259a0cca2aa7e
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241089"
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Adategység kódolása a Media Encoder Standard .NET használatával 
A kódolási feladat a Media Services egyik leggyakrabban használt művelete. A kódolási feladat a médiafájlokat alakítja át egy meghatározott kódolásból egy másikra. Kódolás, ha a Media Services beépített Media Encoder is használhatja. Egy Media Services; partner által biztosított kódoló is használhatja külső kódolókkal az Azure Marketplace-en keresztül érhetők el. 

Ez a cikk bemutatja, hogyan a kódolásához, a Media Encoder Standard (MES) állnak a .NET használatával. Media Encoder Standard használatával lett konfigurálva a kódolók készletek egyikét leírt [Itt](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Javasoljuk, hogy a forrásfájlok mindig kódolandó egy adaptív sávszélességű MP4-készletet, és alakítsa át a készlet a kívánt formátumban történő a [dinamikus csomagolási](media-services-dynamic-packaging-overview.md). 

Ha a kimeneti objektum tárolása titkosítva, konfigurálnia kell a állít be objektumtovábbítási szabályzatot. További információkért lásd: [objektumtovábbítási szabályzat konfigurálása](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> MES használatával hoz létre egy kimeneti fájl, amelynek neve tartalmazza az első 32 karakter a bemeneti fájl neve. A név alapján az előre beállított fájlban megadott. Ha például a "fájlnevet": "{Basename} _ {Index} {Extension}". {Basename} helyébe a először 32 karakter hosszú lehet a bemeneti fájl neve.
> 
> 

### <a name="mes-formats"></a>MES-formátumok
[Formátumai és kodekei](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>MES-beállításkészletek
Media Encoder Standard használatával lett konfigurálva a kódolók készletek egyikét leírt [Itt](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Bemeneti és kimeneti metaadatok
Amikor, kódolás MES használatával egy bemeneti eszköz (vagy eszközök), a kimeneti adategység kap, a sikeres befejezését, amely kódolási feladat. A kimeneti objektum tartalmazza a videó, hang, a miniatűrök, jegyzékfájl, stb. alapján a kódolási előbeállítás használja.

A kimeneti objektum bemeneti objektuma metaadatait tartalmazó fájl is tartalmazza. A metaadatok XML-fájl neve formátuma a következő: < asset_id > _metadata.xml (például 41114ad3 eb5e – 4c - 57-8d 92-5354e2b7d4a4_metadata.xml), ahol a < asset_id > bemeneti objektuma AssetId értéke. A bemeneti XML metaadatok sémája leírt [Itt](media-services-input-metadata-schema.md).

A kimeneti objektum a kimeneti adategység metaadatait tartalmazó fájl is tartalmazza. A metaadatok XML-fájl neve formátuma a következő: < source_file_name > _manifest.xml (például BigBuckBunny_manifest.xml). A séma XML leírt kimeneti metaadatok [Itt](media-services-output-metadata-schema.md).

Ha szeretne vizsgálni, vagy a két metaadat-fájlt, hozzon létre egy SAS-kereső, és töltse le a helyi számítógépen. Hozzon létre egy SAS-kereső, és töltse le a fájlt a Media Services .NET SDK-bővítményeket találhat egy példát.

## <a name="download-sample"></a>Minta letöltése
Get, és futtassa a minta azt mutatja be kódolás a MES használatával [Itt](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>.NET mintakód

Az alábbi példakód a Media Services .NET SDK-t használja a következő feladatokat:

* Hozzon létre egy kódolási feladatot.
* A Media Encoder Standard kódolóval mutató hivatkozás beolvasása.
* Adja meg, hogy használja a [adaptív Streamelés](media-services-autogen-bitrate-ladder-with-mes.md) beállításkészletet. 
* Egyetlen kódolási feladat hozzáadása a feladathoz. 
* Adja meg a kódolni kívánt bemeneti objektuma.
* A kódolt objektumhoz tartalmazó kimeneti adategység létrehozása.
* Adjon hozzá egy eseménykezelőt a feladat állapotának ellenőrzése.
* A feladat elküldéséhez.

#### <a name="create-and-configure-a-visual-studio-project"></a>Egy Visual Studio-projekt létrehozása és konfigurálása

Állítsa be a fejlesztési környezetet, és töltse fel az app.config fájlt a kapcsolatadatokkal a [.NET-keretrendszerrel történő Media Services-fejlesztést](media-services-dotnet-how-to-use.md) ismertető dokumentumban leírtak szerint. 

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

## <a name="advanced-encoding-features-to-explore"></a>Speciális kódolási funkciókat megismerése
* [Miniatűrök létrehozása](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Miniatűrök létrehozása a kódolás során](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Videók körülvágása a kódolás során](media-services-crop-video.md)
* [Kódolási beállításkészletek testreszabása](media-services-custom-mes-presets-with-dotnet.md)
* [Átfedő vagy a kép a videó küszöbérték](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések
[Hogyan hozhat létre a .NET-es Media Encoder Standard használatával miniatűr](media-services-dotnet-generate-thumbnail-with-mes.md)
[Media Services Encoding áttekintése](media-services-encode-asset.md)

