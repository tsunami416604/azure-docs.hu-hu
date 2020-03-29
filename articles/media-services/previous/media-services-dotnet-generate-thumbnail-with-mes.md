---
title: Miniatűrök létrehozása a .NET-es Media Encoder Standard használatával
description: Ez a témakör bemutatja, hogyan lehet a .NET használatával kódolni egy eszközt, és hogyan hozhat létre miniatűröket egyidejűleg a Media Encoder Standard használatával.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 6bc29c098bcf7ef1d1a2e2532a00c95f0ec7e927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61244229"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Miniatűrök létrehozása a .NET-es Media Encoder Standard használatával 

A Media Encoder Standard segítségével egy vagy több miniatűrt hozhat létre a bemeneti videóból [JPEG,](https://en.wikipedia.org/wiki/JPEG) [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics)vagy [BMP](https://en.wikipedia.org/wiki/BMP_file_format) képfájlformátumban. Elküldheti azokat a feladatokat, amelyek csak képeket hoznak létre, vagy kombinálhatja a miniatűrök létrehozását a kódolással. Ez a cikk néhány minta XML- és JSON-miniatűrkészletet tartalmaz az ilyen forgatókönyvekhez. A cikk végén található egy [mintakód,](#code_sample) amely bemutatja, hogyan használható a Media Services .NET SDK a kódolási feladat végrehajtásához.

A mintakészletekben használt elemekkel kapcsolatos további részletekért tekintse át [a Media Encoder Standard sémáját.](media-services-mes-schema.md)

Győződjön meg arról, hogy áttekinti a [Szempontok szakaszt.](media-services-dotnet-generate-thumbnail-with-mes.md#considerations)
    
## <a name="example-of-a-single-png-file-preset"></a>Példa "egyetlen PNG-fájl" készletre

A következő JSON és XML-készlet segítségével egyetlen kimeneti PNG-fájlt hozhat létre a bemeneti videó első néhány másodpercéből, ahol a kódoló a legjobb kísérletet teszi egy "érdekes" képkocka megtalálására. Vegye figyelembe, hogy a kimeneti kép méretei 100%-ra vannak állítva, ami azt jelenti, hogy ezek megegyeznek a bemeneti videó méretével. Vegye figyelembe azt is, hogy a "Kimenetek" "Formátum" beállítása hogyan szükséges ahhoz, hogy megfeleljen a "PngLayers" használatának a "Kodekek" részben. 

### <a name="json-preset"></a>JSON készlet

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "PngImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        }
      ]
    }
```
    
### <a name="xml-preset"></a>XML-készlet

```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <PngImage Start="{Best}">
          <PngLayers>
            <PngLayer>
              <Width>100%</Width>
              <Height>100%</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Példa "JPEG-képek sorozata" készletre

A következő JSON- és XML-készlet segítségével 10 képkészletet hozhat létre a bemeneti idővonal 5%-os, 15%-os, ..., 95%-os időbélyegein, ahol a képméret a bemeneti videó egynegyedének egynegyede.

### <a name="json-preset"></a>JSON készlet

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-készlet
    
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="5%" Step="10%" Range="96%">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Példa "egy kép egy adott időbélyegen" készletre

A következő JSON- és XML-készlet segítségével egyetlen JPEG-kép készíthető a bemeneti videó 30 másodperces jelén. Ez az előre beállított érték arra számít, hogy a bemeneti videó időtartama meghaladja a 30 másodpercet (különben a feladat sikertelen).

### <a name="json-preset"></a>JSON készlet

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "25%",
              "Height": "25%"
            }
          ],
          "Start": "00:00:30",
          "Step": "1",
          "Range": "1",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-készlet
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <JpgImage Start="00:00:30" Step="00:00:01" Range="00:00:01">
          <JpgLayers>
            <JpgLayer>
              <Width>25%</Width>
              <Height>25%</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>
```

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Példa a "miniatűrök különböző felbontásoknál" készletre

A következő készlet segítségével egy feladatban különböző felbontásban hozhat létre miniatűröket. A példában a bemeneti idővonal 5%-os, 15%-os, ...-i 95%-ában a kódoló két képet hoz létre – az egyik a bemeneti videó felbontásának 100%-át, a másik pedig 50%-os.

Figyelje meg a(z) {Resolution} makró használatát a Fájlnév ben; azt jelzi a kódolónak, hogy a készlet Kódolás szakaszában megadott szélességet és magasságot használja a kimeneti lemezképek fájlnevének létrehozása közben. Ez abban is segít, hogy könnyen megkülönböztesse a különböző

### <a name="json-preset"></a>JSON készlet

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "JpgLayers": [
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "100%",
          "Height": "100%"
        },
        {
          "Quality": 90,
          "Type": "JpgLayer",
          "Width": "50%",
          "Height": "50%"
        }

          ],
          "Start": "5%",
          "Step": "10%",
          "Range": "96%",
          "Type": "JpgImage"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Resolution}_{Index}{Extension}",
          "Format": {
        "Type": "JpgFormat"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a>XML-készlet
```xml
    <?xml version="1.0" encoding="utf-8"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
    <Encoding>
    <JpgImage Start="5%" Step="10%" Range="96%"><JpgImage Start="00:00:01" Step="00:00:15">
      <JpgLayers>
       <JpgLayer>
        <Width>100%</Width>
        <Height>100%</Height>
        <Quality>90</Quality>
       </JpgLayer>
       <JpgLayer>
        <Width>50%</Width>
        <Height>50%</Height>
        <Quality>90</Quality>
       </JpgLayer>
      </JpgLayers>
    </JpgImage>
    </Encoding>
    <Outputs>
      <Output FileName="{Basename}_{Resolution}_{Index}{Extension}">
        <JpgFormat/>
      </Output>
    </Outputs>
    </Preset>
```

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>Példa bélyegkép generálására kódolás közben

Bár a fenti példák mindegyike megvitatta, hogyan küldhet be olyan kódolási feladatot, amely csak képeket állít elő, a video- és hangkódolást a miniatűrök generálásával is kombinálhatja. A következő JSON- és XML-készlet a **Media Encoder Standard** számára jellel jelöli meg, hogy a kódolás során miniatűrt hozzon létre.

### <a name="json-preset"></a><a id="json"></a>JSON készlet
A sémáról ebben [a](https://msdn.microsoft.com/library/mt269962.aspx) cikkben olvashat.

```json
    {
      "Version": 1.0,
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": "true",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 4500,
              "MaxBitrate": 4500,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "ReferenceFrames": 3,
              "EntropyMode": "Cabac",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
    
            }
          ],
          "Type": "H264Video"
        },
        {
          "JpgLayers": [
            {
              "Quality": 90,
              "Type": "JpgLayer",
              "Width": "100%",
              "Height": "100%"
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 128,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "JpgFormat"
          }
        },
        {
          "FileName": "{Basename}_{Resolution}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }
```

### <a name="xml-preset"></a><a id="xml"></a>XML-készlet
A sémáról ebben [a](https://msdn.microsoft.com/library/mt269962.aspx) cikkben olvashat.

```csharp
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <SceneChangeDetection>true</SceneChangeDetection>
          <H264Layers>
            <H264Layer>
              <Bitrate>4500</Bitrate>
              <Width>1280</Width>
              <Height>720</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>4500</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
        <JpgImage Start="{Best}">
          <JpgLayers>
            <JpgLayer>
              <Width>100%</Width>
              <Height>100%/Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Resolution}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
      </Outputs>
    </Preset>   
```

## <a name="encode-video-and-generate-thumbnail-with-net"></a><a id="code_sample"></a>Videó kódolása és miniatűr létrehozása a .NET segítségével

A következő kódpélda a Media Services .NET SDK segítségével hajtja végre a következő feladatokat:

* Hozzon létre egy kódolási feladatot.
* Hivatkozás a Media Encoder Standard kódolóra.
* Töltse be a kódolási készletet tartalmazó előre beállított [XML-t](media-services-dotnet-generate-thumbnail-with-mes.md#xml) vagy [JSON-t,](media-services-dotnet-generate-thumbnail-with-mes.md#json) valamint a miniatűrök létrehozásához szükséges információkat. Ezt az [XML-t](media-services-dotnet-generate-thumbnail-with-mes.md#xml) vagy [JSON-t](media-services-dotnet-generate-thumbnail-with-mes.md#json) fájlba mentheti, és a következő kóddal töltheti be a fájlt.
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* Egyetlen kódolási feladat hozzáadása a feladathoz. 
* Adja meg a kódolandó bemeneti eszközt.
* Hozzon létre egy kimeneti eszközt, amely tartalmazza a kódolt eszközt.
* Adjon hozzá egy eseménykezelőt a feladat előrehaladásának ellenőrzéséhez.
* Küldje el a feladatot.

A fejlesztői környezet beállításával kapcsolatos útmutatást a [Media Services-fejlesztés és](media-services-dotnet-how-to-use.md) a .NET cikk ismerteti.

```csharp
using System;
using System.Configuration;
using System.IO;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Threading;

namespace EncodeAndGenerateThumbnails
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

            // Encode and generate the thumbnails.
            EncodeToAdaptiveBitrateMP4Set(asset);

            Console.ReadLine();
        }

        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("Media Encoder Standard Thumbnail Job");
            // Get a media processor reference, and pass to it the name of the 
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Load the XML (or JSON) from the local file.
            string configuration = File.ReadAllText("ThumbnailPreset_JSON.json");

            // Create a task
            ITask task = job.Tasks.AddNew("Media Encoder Standard Thumbnail task",
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

## <a name="considerations"></a>Megfontolandó szempontok
A következő szempontokat kell figyelembe venni:

* Explicit időbélyegek használata a Start/Step/Range feltételezi, hogy a bemeneti forrás legalább 1 perc hosszú.
* Jpg /Png/BmpImage elemek Start, Step és Range karakterlánc attribútumokkal rendelkeznek – ezek a következőképpen értelmezhetők:
  
  * Keretszám, ha nem negatív egész számok, például "Start": "120",
  * A forrás időtartamához viszonyítva, ha %utótagban van kifejezve, például "Start": "15%", VAGY
  * Időbélyeg, ha hh:PP:SS- ben van kifejezve... Formátum. Például "Start" : "00:01:00"
    
    Keverheti és párosíthatja a jelöléseket, ahogy tetszik.
    
    Ezenkívül a Start egy speciális makrót is támogat:{Best}, amely megpróbálja meghatározni a tartalom első "érdekes" keretét MEGJEGYZÉS: (A lépés és a tartomány figyelmen kívül lesz hagyva, ha a Start értéke {Best})
  * Alapértelmezések: Start:{Best}
* A kimeneti formátumot minden képformátumhoz kifejezetten meg kell adni: Jpg/Png/BmpFormat. Ha jelen van, a MES a JpgVideo-t jpgformat-ra és így tovább egyezik. A OutputFormat egy új képkokod-specifikus makrót vezet be: {Index}, amelynek jelen kell lennie (egyszer és csak egyszer) a képkimeneti formátumokhoz.

## <a name="next-steps"></a>További lépések

Ellenőrizheti a [feladat előrehaladását,](media-services-check-job-progress.md) amíg a kódolási feladat függőben van.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Media Services kódolás – áttekintés](media-services-encode-asset.md)

