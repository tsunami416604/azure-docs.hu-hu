---
title: Miniatűrök létrehozása a .NET-es Media Encoder Standard használatával
description: Ez a témakör bemutatja, hogyan használható a .NET kódolása és miniatűrök létrehozása a Media Encoder Standard használatával egy időben.
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
ms.date: 02/09/2019
ms.author: juliako
ms.openlocfilehash: e557f7f615a53bc7ed27849e9ea1721328c1afee
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57834584"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Miniatűrök létrehozása a .NET-es Media Encoder Standard használatával 

Használhatja a Media Encoder Standard használatával hozzon létre egy vagy több miniatűrök a bemeneti videóhoz a [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics), vagy [BMP](https://en.wikipedia.org/wiki/BMP_file_format) fájlformátumok kép. Elküldheti a feladatokat, amelyek csak képeket, vagy kombinálhatja miniatűrkészítés kódolással. Ez a cikk néhány minta XML és JSON miniatűr készletek ilyen esetekben. A cikk végén, van egy [mintakód](#code_sample) , amely bemutatja, hogyan használja a Media Services .NET SDK a kódolási feladatok.

A minta készletek használt elemek a további részletekért tekintse át [Media Encoder Standard-séma](media-services-mes-schema.md).

Mindenképpen tekintse át a [szempontok](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) szakaszban.
    
## <a name="example-of-a-single-png-file-preset"></a>Példa egy "egyetlen PNG-fájl" beállításkészletet.

A következő JSON- és XML-készlet létrehozásához egy egyetlen kimeneti PNG-fájl a bemeneti videóhoz, az első néhány másodpercet, ahol a kódoló lehetővé teszi egy kísérlet a legjobb keresése egy "érdekes" keret használható. Vegye figyelembe, hogy a kimeneti kép mérete 100 %-ra, ami azt jelenti, ezek a bemeneti videó méretéhez van állítva. Is vegye figyelembe, hogyan "Formátum" beállítás a "Kimenetek" szükséges "PngLayers" a "Kodekek" szakaszban használatának megfelelően. 

### <a name="json-preset"></a>JSON-előbeállítás

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
    
### <a name="xml-preset"></a>XML-készletet

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

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Példa egy "JPEG-képek series" beállításkészletet.

A következő JSON- és XML-készlet képkészlet 10, 5 időbélyegeket létrehozásához használható % 15 %,..., 95 %-a bemeneti ütemtervét, ahol a képméret egyik van megadva, a bemeneti videó negyedév.

### <a name="json-preset"></a>JSON-előbeállítás

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

### <a name="xml-preset"></a>XML-készletet
    
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

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Példa egy "egy lemezkép egy adott időbélyegző," beállításkészletet.

A következő JSON- és XML-készletet a bemeneti videó 30 másodperces befejeződnie egyetlen JPEG-képek létrehozására használható. Ezt a beállítást a bemeneti videó időtartama a több mint 30 másodpercre vár (ellenkező esetben a feladat sikertelen).

### <a name="json-preset"></a>JSON-előbeállítás

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

### <a name="xml-preset"></a>XML-készletet
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

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>A "miniatűrök, a különböző" készlet – példa

Az alábbi előre definiált, a különböző miniatűrök létrehozása a feladatban egy használható. A példában pozíciók 5 %-os 15 %,..., 95 %-át a bemeneti ütemterv a kódoló létrehoz két lemezképet – egyenként 100 %-a bemeneti videó felbontása és a másik 50 %-ában.

Figyeljük meg a fájlnév; {feloldási} makra azt jelzi, a szélességét és magasságát, hogy a készlet létrehozásakor a fájlnevet, a kimeneti rendszerképek kódolás szakaszában megadott használata a kódoló. Ez segít is egyszerűen megkülönböztetni a különböző rendszerképek

### <a name="json-preset"></a>JSON-előbeállítás

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

### <a name="xml-preset"></a>XML-készletet
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

## <a name="example-of-generating-a-thumbnail-while-encoding"></a>A miniatűr létrehozása közben kódolás – példa

Amíg a fenti példák mindegyike rendelkezik tárgyalt, hogyan küldhet egy kódolási feladatot, amely csak képeket, kombinálhatja is a videó vagy hang kódolás miniatűrkészítés. A következő JSON- és XML-készlet mondja el **Media Encoder Standard** , miniatűrkép generálása során kódolást.

### <a id="json"></a>JSON-előbeállítás
Sémával kapcsolatos információkért lásd: [ez](https://msdn.microsoft.com/library/mt269962.aspx) cikk.

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

### <a id="xml"></a>XML-készletet
Sémával kapcsolatos információkért lásd: [ez](https://msdn.microsoft.com/library/mt269962.aspx) cikk.

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

## <a id="code_sample"></a>A videó kódolása és .NET-tel miniatűr létrehozása

Az alábbi példakód a Media Services .NET SDK-t használja a következő feladatokat:

* Hozzon létre egy kódolási feladatot.
* A Media Encoder Standard kódolóval mutató hivatkozás beolvasása.
* A készlet betöltéséhez [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) vagy [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) tartalmaznak, a kódolási előbeállítást miniatűrök létrehozása a szükséges információkat és. Ez mentheti [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) vagy [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) a fájl- és használja a következő kód betölteni a fájlt.
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* Egyetlen kódolási feladat hozzáadása a feladathoz. 
* Adja meg a kódolni kívánt bemeneti objektuma.
* A kódolt objektumhoz tartalmazó kimeneti adategység létrehozása.
* Adjon hozzá egy eseménykezelőt a feladat állapotának ellenőrzése.
* A feladat elküldéséhez.

Tekintse meg a [.NET-keretrendszerrel történő Media Services-fejlesztés](media-services-dotnet-how-to-use.md) cikk tartalmazza a fejlesztési környezet beállítása a.

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

* Kezdő/lépés/tartomány explicit időbélyegeket használatát feltételezi, hogy a bemeneti forrás legalább 1 percnek hosszú.
* JPG vagy Png/BmpImage elemek elindítani, a lépésben és karakterlánc-attribútumok tartomány – ezeket úgy:
  
  * Keret száma, ha azok nem negatív egész szám, például "a Start": "120",
  * Ha forrásidőtartamhoz %-a forrás időtartama, például "Start" relatív: "15 %", VAGY
  * Időbélyeg, ha óó kifejezett... formátum. Például "kezdő": "00:01:00"
    
    Ön szabadon kombinálhatók, jelölések meg.
    
    Ezenkívül kezdő is támogatja a speciális makró: {ajánlott}, amely kísérli meg meghatározni a tartalom Megjegyzés: az első "érdekes" kép: (A lépés, és a tartomány nem veszi figyelembe kezdő {legjobb} értékre van állítva)
  * Alapértelmezettek: Indítsa el: {ajánlott}
* Minden egyes képformátum explicit módon megadott kimeneti formátum van szüksége: Jpg/Png/BmpFormat. Jelenléte esetén MES felel meg JpgVideo JpgFormat, és így tovább. OutputFormat vezet be az új lemezkép-kodek adott makró: {Index}, mely kell lennie a jelen (egyszer és csak egyszer) rendszerkép kimeneti formátum.

## <a name="next-steps"></a>További lépések

Ellenőrizheti a [feladat-végrehajtási](media-services-check-job-progress.md) közben a kódolási feladat függőben.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[A Media Services kódolási áttekintése](media-services-encode-asset.md)

