---
title: A Media Encoder Standard-beállításkészletek testreszabása |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan hajthat végre a speciális kódolási feladat a Media Encoder Standard készletek testre szabásával. A témakör bemutatja, hogyan hozhat létre egy kódolási feladat és a feladat a Media Services .NET SDK használatával. Azt is bemutatja, hogyan lehet egyéni előbeállításokat a kódolási feladat ellátására.
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
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58516484"
---
# <a name="customizing-media-encoder-standard-presets"></a>Testreszabás Media Encoder Standard készletek  

## <a name="overview"></a>Áttekintés

Ez a cikk bemutatja, hogyan végrehajtásához speciális kódolás a Media Encoder Standard (MES) használatával egyéni előbeállítás. A cikkben .NET kódolási tevékenység és a egy feladatot, amely végrehajtja a feladat létrehozásához.  

Ez a cikk bemutatja, hogyan szabhatja testre a készlet végrehajtásával a [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) készletet, és a rétegek számának csökkentését. A [Media Encoder Standard testreszabása készletek](media-services-advanced-encoding-with-mes.md) a cikk bemutatja az egyéni előbeállításokat speciális kódolási feladatok végrehajtásához használható.

> [!NOTE]
> Az egyéni készletek ebben a cikkben leírt nem használható [Media Services V3](https://docs.microsoft.com/azure/media-services/latest/) átalakítások vagy a CLI-parancsok. Tekintse meg a [áttelepítési útmutató a v2 v3](../latest/migrate-from-v2-to-v3.md) további részletekért.

## <a id="customizing_presets"></a> A MES előre testreszabása

### <a name="original-preset"></a>Eredeti készlet

A JSON-megadott mentése a [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) a cikk az egyes .json kiterjesztésű fájlt. Ha például **CustomPreset_JSON.json**.

### <a name="customized-preset"></a>Egyéni előbeállítás

Nyissa meg a **CustomPreset_JSON.json** fájlt, és távolítsa el az első három réteg **H264Layers** így nézhet ki a fájlt.

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

## <a id="encoding_with_dotnet"></a>Kódolás a Media Services .NET SDK használatával

Az alábbi példakód a Media Services .NET SDK-t használja a következő feladatokat:

- Hozzon létre egy kódolási feladatot.
- A Media Encoder Standard kódolóval mutató hivatkozás beolvasása.
- Töltse be az egyéni JSON-készletet, amely az előző szakaszban létrehozott. 
  
        // Load the JSON from the local file.
        string configuration = File.ReadAllText(fileName);  

- Adja hozzá a kódolási tevékenység a feladathoz. 
- Adja meg a kódolni kívánt bemeneti objektuma.
- A kódolt objektumhoz tartalmazó kimeneti adategység létrehozása.
- Adjon hozzá egy eseménykezelőt a feladat állapotának ellenőrzése.
- A feladat elküldéséhez.
   
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

- [Az egyéni átalakítási kódolása a parancssori felület használatával](../latest/custom-preset-cli-howto.md)
- [Kódolás a Media Services v3](../latest/encoding-concept.md)

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
