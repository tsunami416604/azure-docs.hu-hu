---
title: Az adathordozó-kódoló standard készleteinek testreszabása | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan hajtható végre a speciális kódolás a Media Encoder Standard feladatkészletek testreszabásával. A témakör bemutatja, hogyan hozhat létre kódolási feladatot és feladatot a Media Services .NET SDK használatával. Azt is bemutatja, hogyan kell egyéni készleteket a kódolási feladathoz.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec95392f-d34a-4c22-a6df-5274eaac445b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: juliako
ms.openlocfilehash: 39a1dd5c3d26eeb6545a96aa35f9457bd9859c21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251244"
---
# <a name="customizing-media-encoder-standard-presets"></a>Az adathordozó-kódoló standard készleteinek testreszabása  

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan végezhet speciális kódolást a Media Encoder Standard (MES) segítségével egyéni készlet használatával. A cikk a .NET segítségével hoz létre egy kódolási feladatot és egy feladatot végrehajtó feladatot.  

Ez a cikk bemutatja, hogyan szabhatja testre a beállításokat a [H264 Többszörös bitráta 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) készlet használatával és a rétegek számának csökkentésével. A [Media Encoder Standard készletkészlettestreszabása](media-services-advanced-encoding-with-mes.md) cikk bemutatja a speciális kódolási feladatok végrehajtására használható egyéni készleteket.

> [!NOTE]
> A cikkben ismertetett egyéni készletek nem használhatók a Media Services V3-transzformációkban vagy a CLI-parancsokban. [Media Services V3](https://docs.microsoft.com/azure/media-services/latest/) További részletekért tekintse meg a [v2 és a v3 között történő áttelepítési útmutatást.](../latest/migrate-from-v2-to-v3.md)

## <a name="customizing-a-mes-preset"></a><a id="customizing_presets"></a>MES-készlet testreszabása

### <a name="original-preset"></a>Eredeti készlet

Mentse a [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) cikkben definiált JSON-t egy .json kiterjesztésű fájlban. Például **CustomPreset_JSON.json**.

### <a name="customized-preset"></a>Testre szabott készlet

Nyissa meg a **CustomPreset_JSON.json** fájlt, és távolítsa el az első három réteget a **H264Layers-ből,** hogy a fájl így néz ki.

```json 
    {  
      "Version": 1.0,  
      "Codecs": [  
        {  
          "KeyFrameInterval": "00:00:02",  
          "H264Layers": [  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 1000,  
              "MaxBitrate": 1000,  
              "BufferWindow": "00:00:05",  
              "Width": 640,  
              "Height": 360,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            },  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 650,  
              "MaxBitrate": 650,  
              "BufferWindow": "00:00:05",  
              "Width": 640,  
              "Height": 360,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            },  
            {  
              "Profile": "Auto",  
              "Level": "auto",  
              "Bitrate": 400,  
              "MaxBitrate": 400,  
              "BufferWindow": "00:00:05",  
              "Width": 320,  
              "Height": 180,  
              "BFrames": 3,  
              "ReferenceFrames": 3,  
              "AdaptiveBFrame": true,  
              "Type": "H264Layer",  
              "FrameRate": "0/1"  
            }  
          ],  
          "Type": "H264Video"  
        },  
        {  
          "Profile": "AACLC",  
          "Channels": 2,  
          "SamplingRate": 48000,  
          "Bitrate": 128,  
          "Type": "AACAudio"  
        }  
      ],  
      "Outputs": [  
        {  
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
          "Format": {  
            "Type": "MP4Format"  
          }  
        }  
      ]  
    }  
```

## <a name="encoding-with-media-services-net-sdk"></a><a id="encoding_with_dotnet"></a>Kódolás a Media Services .NET SDK szolgáltatással

A következő kódpélda a Media Services .NET SDK segítségével hajtja végre a következő feladatokat:

- Hozzon létre egy kódolási feladatot.
- Hivatkozás a Media Encoder Standard kódolóra.
- Töltse be az előző szakaszban létrehozott egyéni JSON-készletet. 
  
        // Load the JSON from the local file.
        string configuration = File.ReadAllText(fileName);  

- Kódolási feladat hozzáadása a feladathoz. 
- Adja meg a kódolandó bemeneti eszközt.
- Hozzon létre egy kimeneti eszközt, amely tartalmazza a kódolt eszközt.
- Adjon hozzá egy eseménykezelőt a feladat előrehaladásának ellenőrzéséhez.
- Küldje el a feladatot.
   
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

namespace CustomizeMESPresests
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

        private static readonly string _mediaFiles =
            Path.GetFullPath(@"../..\Media");

        private static readonly string _singleMP4File =
            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");

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

            // Encode and generate the output using custom presets.
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

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("CustomPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
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

## <a name="see-also"></a>Lásd még

- [Hogyan kódolhatja az egyéni átalakítás segítségével CLI](../latest/custom-preset-cli-howto.md)
- [Kódolás a Media Services v3 használatával](../latest/encoding-concept.md)

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
