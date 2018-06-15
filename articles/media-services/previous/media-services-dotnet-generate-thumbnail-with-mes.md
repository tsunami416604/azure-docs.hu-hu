---
title: Miniatűrök létrehozása a .NET-es Media Encoder Standard használatával
description: Ez a témakör bemutatja, hogyan .NET használatával egy eszköz kódolni és indexképének létrehozására használnak Media Encoder Standard használatával egyszerre.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: b8dab73a-1d91-4b6d-9741-a92ad39fc3f7
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2017
ms.author: juliako
ms.openlocfilehash: 08332865a60baa0dd87b16809994065ddfed3055
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33789438"
---
# <a name="how-to-generate-thumbnails-using-media-encoder-standard-with-net"></a>Miniatűrök létrehozása a .NET-es Media Encoder Standard használatával

Használhatja Media Encoder Standard használatával hozzon létre egy vagy több miniatűrök a bemeneti videóhoz a [JPEG](https://en.wikipedia.org/wiki/JPEG), [PNG](https://en.wikipedia.org/wiki/Portable_Network_Graphics), vagy [BMP](https://en.wikipedia.org/wiki/BMP_file_format) fájlformátumok lemezképet. Elküldheti a feladatokat, amelyek csak a képeket, vagy kombinálhatja a miniatűr generációs kódolással. A cikkben néhány minta XML és JSON miniatűr készletek ilyen forgatókönyvek esetén. A cikk végén van egy [példakód](#code_sample) , amely bemutatja, hogyan kódolási a feladatnak a Media Services .NET SDK használatával.

A minta készletek használt elemein további részletekért tekintse át [Media Encoder Standard séma](media-services-mes-schema.md).

Mindenképpen tekintse át a [szempontok](media-services-dotnet-generate-thumbnail-with-mes.md#considerations) szakasz.
    
## <a name="example-of-a-single-png-file-preset"></a>Egy "egyetlen PNG file" készletet – példa

A következő JSON és az XML-készlet egy egyetlen kimeneti PNG fájlt az első néhány másodpercet a bemeneti videóhoz, ahol a kódolás lehetővé teszi a legjobb kísérlet egy "érdekes" keret keresése létrehozására használható. Vegye figyelembe, hogy a kimeneti kép mérete 100 %, ami azt jelenti, ezek a bemeneti videó méretéhez van állítva. Azt is fontos megjegyezni, hogyan "Kimenetek" a "Format" beállítást kell felel meg a "Kodekek" szakasz "PngLayers" használatát. 

### <a name="json-preset"></a>JSON-készlet

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
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
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

## <a name="example-of-a-series-of-jpeg-images-preset"></a>Egy "sorozata, JPEG-képek" készletet – példa

A következő JSON és az XML-készlet segítségével létrehoznak egy 10 kép időbélyegeket 5, %, 15 %,..., 95 %-a bemeneti ütemterv, ahol a képméret ilyen van megadva a negyedév, amely a bemeneti videó.

### <a name="json-preset"></a>JSON-készlet

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
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
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

## <a name="example-of-a-one-image-at-a-specific-timestamp-preset"></a>Egy "egy adott időbélyeg képnek" készletet – példa

A következő JSON és az XML-készletet: a bemeneti videóhoz 30 másodperces védjegye egyetlen JPEG-képek létrehozására használható. Az előre definiált vár a bemeneti videó lehet több mint 30 másodperc az időtartam (ellenkező esetben a feladat sikertelen lesz).

### <a name="json-preset"></a>JSON-készlet

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
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
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

## <a name="example-of-a-thumbnails-at-different-resolutions-preset"></a>Egy "a miniatűrök eltérő a megoldásuk" készletet – példa

A következő beállítások segítségével különböző felbontásban indexképének létrehozására használnak egy feladat. A példában pozíciók 5 %-os 15 %,..., a bemeneti ütemterv 95 %-át a kódoló hoz létre két lemezképet – egyenként 100 %-a bemeneti videó felbontása, a másik 50 %.

Megjegyzés: {feloldási} makró a; fájlnév használata azt jelzi, szélességét és magasságát, a készlet nevét a kimeneti képek létrehozása közben kódolás szakaszában megadott használandó kódolóhoz. Azt is lehetővé teszi tudja egyszerűen megkülönböztetni a különböző képek

### <a name="json-preset"></a>JSON-készlet

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
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
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

A fenti példák mindegyikét kérdéseket hogyan küldhet egy kódolási feladat, amely csak létrehozza a lemezképeket, amíg kombinálhatja is videó/hang miniatűr generációs kódolást. A következő JSON és az XML-készlet közölje **Media Encoder Standard** során kódolás miniatűr létrehozásához.

### <a id="json"></a>JSON-készlet
Séma kapcsolatos információkért lásd: [ez](https://msdn.microsoft.com/library/mt269962.aspx) cikk.

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

### <a id="xml"></a>XML-készlet
Séma kapcsolatos információkért lásd: [ez](https://msdn.microsoft.com/library/mt269962.aspx) cikk.

```csharp
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
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

## <a id="code_sample"></a>Videó kódolása és a .NET miniatűr készítése

Az alábbi példakód Media Services .NET SDK-t használja a következő feladatok végezhetők el:

* Hozzon létre egy kódolási feladat.
* A Media Encoder Standard encoder mutató hivatkozás beszerzése.
* A készlet betöltéséhez [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) vagy [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) , amelyek tartalmazzák a kódolást és a miniatűrök létrehozásához szükséges információkat az adott néven beállítás. Ez mentheti [XML](media-services-dotnet-generate-thumbnail-with-mes.md#xml) vagy [JSON](media-services-dotnet-generate-thumbnail-with-mes.md#json) a fájl- és használja a következő kódot betölteni a fájlt.
  
        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(fileName);  
* Egy kódolási feladat hozzáadása a projekthez. 
* Adja meg a bemeneti eszköz kódolni kell.
* Hozzon létre egy kimeneti eszközt, amely tartalmazza a kódolt objektumhoz.
* Adjon hozzá egy eseménykezelő, ellenőrizze a feladat előrehaladását.
* A feladat elküldéséhez.

Tekintse meg a [Media Services-fejlesztés a .NET](media-services-dotnet-how-to-use.md) számú cikk ismerteti a fejlesztői környezet beállítása.

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
A következők érvényesek:

* Start/lépés/címtartomány explicit időbélyegeket használatát feltételezi, hogy a bemeneti forrás legalább 1 percnek hosszú.
* JPG vagy Png/BmpImage elemek elindítani, lépés, és karakterlánc-attribútumok között – ezek úgy:
  
  * Keret számát, ha azok nem negatív egész számokat, például "Start": "120"
  * Relatív forrás időtartama, ha kifejezett % utótaggal szerint, például "Start": "15 %", vagy
  * Ha óó: pp: kifejezett időbélyeg... formátumban. Például "Start": "00: 01:00"
    
    Ön szabadon kombinálhatók jelölések, ha Ön adja.
    
    Emellett Start is támogatja a speciális makró: {ajánlott}, amely kísérli meg meghatározni a tartalom "érdekes" első keretében: (lépés, és a tartomány figyelmen kívül lesznek hagyva Ha kezdő {legjobb} értékre van állítva)
  * Alapértelmezett: Start: {legjobb}
* Kimeneti formátumot kell explicit módon meg kell adni az egyes képformátum: Jpg vagy Png/BmpFormat. Ha létezik, MES felel meg a JpgFormat JpgVideo és így tovább. OutputFormat bevezet egy új lemezkép-kodek adott makró: {Index}, mely rendelkeznie kell megjeleníteni az (egyszer és csak egyszer) kimeneti képformátum.

## <a name="next-steps"></a>További lépések

Ellenőrizheti a [folyamatban lévő feladat](media-services-check-job-progress.md) közben a kódolási feladat folyamatban.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Media Services kódolási áttekintése](media-services-encode-asset.md)

