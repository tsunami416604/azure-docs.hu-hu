---
title: "Egy eszköz kódolása a Media Encoder Standard .NET használatával |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan .NET használatával kódolása a Media Encoder Strandard keresztül."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: 03431b64-5518-478a-a1c2-1de345999274
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 6f9108beba69e52398f16e2b9055c7a2f2d04f48
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="encode-an-asset-with-media-encoder-standard-using-net"></a>Egy eszköz kódolása a Media Encoder Standard .NET használatával
A kódolási feladat a Media Services egyik leggyakrabban használt művelete. A kódolási feladat a médiafájlokat alakítja át egy meghatározott kódolásból egy másikra. Amikor a kódolására, a Media Services beépített Media Encoder is használhatja. Egy Media Services partner; által biztosított kódoló is használható külső kódolókkal az Azure piactéren keresztül érhetők el. 

Ez a cikk bemutatja, hogyan lehet .NET segítségével a kódolása a Media Encoder Standard (MES). Media Encoder Standard segítségével konfigurálható: az egyik a kódolók készletek leírt [Itt](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

Javasoljuk, hogy mindig kódolása egy adaptív sávszélességű MP4 állítsa be a forrásfájlokat, és a set átalakítása a kívánt formátum használatával az [dinamikus becsomagolás](media-services-dynamic-packaging-overview.md). 

Ha az kimeneti adategységen tárolótitkosítást alkalmaz, konfigurálnia kell az adategység továbbítási házirendjét. További információkért lásd: [objektumtovábbítási szabályzat konfigurálása](media-services-dotnet-configure-asset-delivery-policy.md).

> [!NOTE]
> MES hoz létre a kimeneti fájl a bemeneti fájl nevét az első 32 karakternél néven. A név alapján Mi az előre definiált fájlban megadott. Például a "fájlnevet": "{Index} {bővítmény} {Basename} _". {Basename} helyébe az első 32 karakter megegyezik a bemeneti fájl.
> 
> 

### <a name="mes-formats"></a>MES formátumok
[Formátumok és kodekek](media-services-media-encoder-standard-formats.md)

### <a name="mes-presets"></a>MES-beállításkészletek
Media Encoder Standard segítségével konfigurálható: az egyik a kódolók készletek leírt [Itt](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Bemeneti és kimeneti metaadatok
Amikor kódol MES használatával egy bemeneti eszköz (vagy eszközök), a kimeneti eszköz, a sikeres beolvasása, amely megvalósításának feladat kódolása. A kimeneti adategységen videó, hang, miniatűröket, jegyzék, stb. alapján használja a kódolási beállításkészlet tartalmazza.

A kimeneti adategységen is tartalmaz a bemeneti eszköz metaadatait tartalmazó fájl. A metaadatok XML-fájl nevének formátuma a következő: < asset_id > _metadata.xml (például 41114ad3-eb5e - 4c 57-8d 92-5354e2b7d4a4_metadata.xml), ahol a < asset_id > a bemeneti eszköz AssetId értékét. A bemeneti XML metaadatok sémája leírt [Itt](media-services-input-metadata-schema.md).

A kimeneti adategységen a kimeneti adategységen vonatkozó metaadatok fájlt is tartalmaz. A metaadatok XML-fájl nevének formátuma a következő: < source_file_name > _manifest.xml (például BigBuckBunny_manifest.xml). A kimeneti metaadatok XML leírt sémája [Itt](media-services-output-metadata-schema.md).

Ha meg szeretné vizsgálni a két Metaadatfájlok valamelyikét, hozzon létre egy SAS-kereső, és töltse le a helyi számítógépen. Egy példa található SAS-kereső létrehozásával, és töltse le a fájlt, a Media Services .NET SDK-bővítmények használatával.

## <a name="download-sample"></a>Minta letöltése
Lekérni, és futtasson egy mintát, amely bemutatja, hogyan rendelkező a MES kódolni [Itt](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="net-sample-code"></a>.NET mintakód

Az alábbi példakód Media Services .NET SDK-t használja a következő feladatok végezhetők el:

* Hozzon létre egy kódolási feladat.
* A Media Encoder Standard encoder mutató hivatkozás beszerzése.
* Adja meg, hogy használja a [adaptív Streameléshez](media-services-autogen-bitrate-ladder-with-mes.md) előre. 
* Egy kódolási feladat hozzáadása a projekthez. 
* Adja meg a bemeneti eszköz kódolni kell.
* Hozzon létre egy kimeneti eszközt, amely tartalmazza a kódolt objektumhoz.
* Adjon hozzá egy eseménykezelő, ellenőrizze a feladat előrehaladását.
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

## <a name="advanced-encoding-features-to-explore"></a>Speciális kódolás szolgáltatások felfedezése
* [A miniatűrök létrehozásának módját](media-services-dotnet-generate-thumbnail-with-mes.md)
* [Miniatűrök létrehozása során kódolás](media-services-dotnet-generate-thumbnail-with-mes.md#example-of-generating-a-thumbnail-while-encoding)
* [Kódolás során körülvágása videók](media-services-crop-video.md)
* [Kódolási készletek testreszabása](media-services-custom-mes-presets-with-dotnet.md)
* [Átfedő, vagy olyan képpel videó vízjel](media-services-advanced-encoding-with-mes.md#overlay)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések
[.NET Media Encoder Standard használatával miniatűr létrehozásával](media-services-dotnet-generate-thumbnail-with-mes.md)
[Media Services kódolási áttekintése](media-services-encode-asset.md)

