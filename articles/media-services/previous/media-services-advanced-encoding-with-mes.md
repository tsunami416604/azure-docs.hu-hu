---
title: Fejlett kódolás MES-beállításkészletek testreszabásával végre |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan hajthat végre a speciális kódolási feladat a Media Encoder Standard készletek testre szabásával.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: juliako
ms.openlocfilehash: f2be733fc0f127288a86372adf54a547983285db
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55998705"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Fejlett kódolás MES-beállításkészletek testreszabásával végrehajtása 

## <a name="overview"></a>Áttekintés

Ez a témakör bemutatja a Media Encoder Standard-beállításkészletek testreszabása. A [kódolás a Media Encoder Standard használatával egyéni előbeállításokat](media-services-custom-mes-presets-with-dotnet.md) a témakör bemutatja, hogyan hozhat létre a kódolási tevékenység és a egy feladatot, amely végrehajtja ezt a feladatot a .NET használatával. Miután testre szabta a készletet, adja meg az egyéni készletek a kódolási feladat. 

>[!NOTE]
>Ha egy XML-készletet használ, ügyeljen arra, hogy elemek sorrendjének megőrzése, ahogyan az alábbi XML-mintát (például KeyFrameInterval előzze meg a SceneChangeDetection).
>

Ebben a témakörben találja meg az egyéni készletek, amelyek a következő kódolási feladatokat.

## <a name="support-for-relative-sizes"></a>Relatív méretek támogatása

Miniatűrök létrehozása, amikor nem kell mindig adja meg az kimeneti szélességét és magasságát (képpontban). Százalékos értékként, tartománya: [1 %..., 100 %-os] megadhatja azokat.

### <a name="json-preset"></a>JSON-előbeállítás
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>XML-készletet
    <Width>100%</Width>
    <Height>100%</Height>

## <a id="thumbnails"></a>Miniatűrök létrehozása

Ez a szakasz bemutatja, hogyan szabhatja testre a készletet, amely miniatűrképet generál. Az alább definiált készletet ismerteti, hogyan szeretné kódolni, a fájl, valamint a miniatűrök létrehozása a szükséges információkat. A MES-beállításkészletek dokumentált bármelyikét teheti [ez](media-services-mes-presets-overview.md) szakaszt, és adja hozzá a kódot, amely bélyegképeket hoz létre.  

> [!NOTE]
> A **SceneChangeDetection** az alábbi előre definiált beállítás csak akkor állítható igaz értékre, ha a videó egy egyféle sávszélességű kódolja. Ha kódolja többszörös sávszélességű MP4 videó-és set **SceneChangeDetection** az értéke igaz, a kódoló hibát ad vissza.  
>
>

Sémával kapcsolatos információkért lásd: [ez](media-services-mes-schema.md) témakör.

Mindenképpen tekintse át a [szempontok](#considerations) szakaszban.

### <a id="json"></a>JSON-előbeállítás
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
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "{Best}",
          "Type": "JpgImage"
        },
        {
          "PngLayers": [
            {
              "Type": "PngLayer",
              "Width": 640,
              "Height": 360,
            }
          ],
          "Start": "00:00:01",
          "Step": "00:00:10",
          "Range": "00:00:58",
          "Type": "PngImage"
        },
        {
          "BmpLayers": [
            {
              "Type": "BmpLayer",
              "Width": 640,
              "Height": 360
            }
          ],
          "Start": "10%",
          "Step": "10%",
          "Range": "90%",
          "Type": "BmpImage"
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
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "PngFormat"
          }
        },
        {
          "FileName": "{Basename}_{Index}{Extension}",
          "Format": {
            "Type": "BmpFormat"
          }
        },
        {
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a id="xml"></a>XML-készletet
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
              <Width>640</Width>
              <Height>360</Height>
              <Quality>90</Quality>
            </JpgLayer>
          </JpgLayers>
        </JpgImage>
        <BmpImage Start="10%" Step="10%" Range="90%">
          <BmpLayers>
            <BmpLayer>
              <Width>640</Width>
              <Height>360</Height>
            </BmpLayer>
          </BmpLayers>
        </BmpImage>
        <PngImage Start="00:00:01" Step="00:00:10" Range="00:00:58">
          <PngLayers>
            <PngLayer>
              <Width>640</Width>
              <Height>360</Height>
            </PngLayer>
          </PngLayers>
        </PngImage>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <JpgFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <BmpFormat />
        </Output>
        <Output FileName="{Basename}_{Index}{Extension}">
          <PngFormat />
        </Output>
      </Outputs>
    </Preset>

### <a name="considerations"></a>Megfontolandó szempontok

A következő szempontokat kell figyelembe venni:

* Kezdő/lépés/tartomány explicit időbélyegeket használatát feltételezi, hogy a bemeneti forrás legalább 1 percnek hosszú.
* JPG vagy Png/BmpImage elemek elindítani, a lépésben és karakterlánc-attribútumok tartomány – ezeket úgy:

  * Keret száma, ha azok nem negatív egész szám, például "a Start": "120",
  * Ha forrásidőtartamhoz %-a forrás időtartama, például "Start" relatív: "15 %", VAGY
  * Időbélyeg, ha óó kifejezett... formátum, "a Start" például: "00:01:00"

    Ön szabadon kombinálhatók, jelölések meg.

    Ezenkívül kezdő is támogatja a speciális makró: {ajánlott}, amely kísérli meg meghatározni a tartalom Megjegyzés: az első "érdekes" kép: (A lépés, és a tartomány nem veszi figyelembe kezdő {legjobb} értékre van állítva)
  * Alapértelmezettek: Indítsa el: {ajánlott}
* Minden egyes képformátum explicit módon megadott kimeneti formátum van szüksége: Jpg/Png/BmpFormat. Jelenléte esetén MES felel meg JpgVideo JpgFormat, és így tovább. OutputFormat vezet be az új lemezkép-kodek adott makró: {Index}, mely kell lennie a jelen (egyszer és csak egyszer) rendszerkép kimeneti formátum.

## <a id="trim_video"></a>(A vágás) videó vágása
Ez a szakasz ismerteti a kódoló készletek grafikus, vagy csökkentse a bemeneti videó, ahol a bemeneti egy úgynevezett mezzanine-fájlt vagy igény szerinti fájl módosítása. A kódoló is használható grafikus vagy egy eszköz, amely a rögzített vagy az élő stream archivált trim – a részleteket a [ebben a blogbejegyzésben](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Amelyből törölni kell a videók, elvégezhető a MES-beállításkészletek dokumentált bármelyikét [ez](media-services-mes-presets-overview.md) szakaszt, és módosítsa a **források** elem (ahogy az alább látható). StartTime értékét meg kell felelnie a bemeneti videó abszolút időbélyegei. Például, ha a bemeneti videó első keret 12:00:10.000 az időbélyeg, majd StartTime kell lennie, mint 12:00:10.000 és nagyobb. Az alábbi példában feltételezzük, hogy a bemeneti videó rendelkezik-e a nulla kiindulási időbélyeg. **Források** kell elhelyezni a készlet elején.

### <a id="json"></a>JSON-előbeállítás
    {
      "Version": 1.0,
      "Sources": [
        {
          "StartTime": "00:00:04",
          "Duration": "00:00:16"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1280,
              "Height": 720,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 2250,
              "MaxBitrate": 2250,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1500,
              "MaxBitrate": 1500,
              "BufferWindow": "00:00:05",
              "Width": 960,
              "Height": 540,
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "FrameRate": "0/1"
            },
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

### <a name="xml-preset"></a>XML-készletet
Amelyből törölni kell a videók, elvégezhető a MES-beállításkészletek dokumentált bármelyikét [Itt](media-services-mes-presets-overview.md) és módosítása a **források** elem (ahogy az alább látható).

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source StartTime="PT4S" Duration="PT14S"/>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>3400</Bitrate>
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
              <MaxBitrate>3400</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>2250</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>2250</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1500</Bitrate>
              <Width>960</Width>
              <Height>540</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1500</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>1000</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1000</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>650</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>650</MaxBitrate>
            </H264Layer>
            <H264Layer>
              <Bitrate>400</Bitrate>
              <Width>320</Width>
              <Height>180</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>3</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cabac</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>400</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <AACAudio>
          <Profile>AACLC</Profile>
          <Channels>2</Channels>
          <SamplingRate>48000</SamplingRate>
          <Bitrate>128</Bitrate>
        </AACAudio>
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>

## <a id="overlay"></a>Hozzon létre egy átmeneti területre

A Media Encoder Standard lehetővé teszi egy meglévő videóhoz képet átfedő. Jelenleg a következő formátumok támogatottak: png, jpg, gif és bmp. Az alább definiált készletet egy videófelirat példáit.

Mellett egy előre beállított fájl határozza meg, akkor is, hogy a Media Services tudja, melyik fájl az eszközben az átmeneti területre kép mely fájlok pedig a videó forrás amelyre átfedi a képet szeretne. A videó fájlt azt kell a **elsődleges** fájlt.

Ha .NET használ, adja hozzá az alábbi két függvényt a megadott .NET típusú példát [ez](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) témakör. A **UploadMediaFilesFromFolder** függvény feltölti a fájlokat (például BigBuckBunny.mp4 és Image001.png) egy mappából, és beállítja a mp4-fájlt az elsődleges fájl az eszközben. A **EncodeWithOverlay** függvény, amely azt (például a készletet a következő) átadása történt egyéni előre beállított fájlt használja a kódolási feladat létrehozásához.


    static public IAsset UploadMediaFilesFromFolder(string folderPath)
    {
        IAsset asset = _context.Assets.CreateFromFolder(folderPath, AssetCreationOptions.None);
    
        foreach (var af in asset.AssetFiles)
        {
            // The following code assumes 
            // you have an input folder with one MP4 and one overlay image file.
            if (af.Name.Contains(".mp4"))
                af.IsPrimary = true;
            else
                af.IsPrimary = false;
    
            af.Update();
        }
    
        return asset;
    }

    static public IAsset EncodeWithOverlay(IAsset assetSource, string customPresetFileName)
    {
        // Declare a new job.
        IJob job = _context.Jobs.Create("Media Encoder Standard Job");
        // Get a media processor reference, and pass to it the name of the 
        // processor to use for the specific task.
        IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

        // Load the XML (or JSON) from the local file.
        string configuration = File.ReadAllText(customPresetFileName);

        // Create a task
        ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
            processor,
            configuration,
            TaskOptions.None);

        // Specify the input assets to be encoded.
        // This asset contains a source file and an overlay file.
        task.InputAssets.Add(assetSource);

        // Add an output asset to contain the results of the job. 
        task.OutputAssets.AddNew("Output asset",
            AssetCreationOptions.None);

        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
        job.Submit();
        job.GetExecutionProgressTask(CancellationToken.None).Wait();

        return job.OutputMediaAssets[0];
    }


> [!NOTE]
> Aktuális korlátozások:
>
> Az átmeneti területre átlátszatlanság beállítás nem támogatott.
>
> A videó forrásfájlt, és az átmeneti területre képfájl kell lennie az adott objektum, és a videó fájlt kell megadni az elsődleges az adategység-fájlként.
>
>

### <a name="json-preset"></a>JSON-előbeállítás
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "VideoOverlay": {
              "Position": {
                "X": 100,
                "Y": 100,
                "Width": 100,
                "Height": 50
              },
              "AudioGainLevel": 0.0,
              "MediaParams": [
                {
                  "OverlayLoopCount": 1
                },
                {
                  "IsOverlay": true,
                  "OverlayLoopCount": 1,
                  "InputLoop": true
                }
              ],
              "Source": "Image001.png",
              "Clip": {
                "Duration": "00:00:05"
              },
              "FadeInDuration": {
                "Duration": "00:00:01"
              },
              "FadeOutDuration": {
                "StartTime": "00:00:03",
                "Duration": "00:00:04"
              }
            }
          },
          "Pad": true
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "H264Layers": [
            {
              "Profile": "Auto",
              "Level": "auto",
              "Bitrate": 1045,
              "MaxBitrate": 1045,
              "BufferWindow": "00:00:05",
              "ReferenceFrames": 3,
              "EntropyMode": "Cavlc",
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
        },
        {
          "Type": "CopyAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}{Extension}",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }


### <a name="xml-preset"></a>XML-készletet
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
      <Sources>
        <Source>
          <Streams />
          <Filters>
            <VideoOverlay>
              <Source>Image001.png</Source>
              <Clip Duration="PT5S" />
              <FadeInDuration Duration="PT1S" />
              <FadeOutDuration StartTime="PT3S" Duration="PT4S" />
              <Position X="100" Y="100" Width="100" Height="50" />
              <Opacity>0</Opacity>
              <AudioGainLevel>0</AudioGainLevel>
              <MediaParams>
                <MediaParam>
                  <IsOverlay>false</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>false</InputLoop>
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
                  <InputLoop>true</InputLoop>
                </MediaParam>
              </MediaParams>
            </VideoOverlay>
          </Filters>
          <Pad>true</Pad>
        </Source>
      </Sources>
      <Encoding>
        <H264Video>
          <KeyFrameInterval>00:00:02</KeyFrameInterval>
          <H264Layers>
            <H264Layer>
              <Bitrate>1045</Bitrate>
              <Width>640</Width>
              <Height>360</Height>
              <FrameRate>0/1</FrameRate>
              <Profile>Auto</Profile>
              <Level>auto</Level>
              <BFrames>0</BFrames>
              <ReferenceFrames>3</ReferenceFrames>
              <Slices>0</Slices>
              <AdaptiveBFrame>true</AdaptiveBFrame>
              <EntropyMode>Cavlc</EntropyMode>
              <BufferWindow>00:00:05</BufferWindow>
              <MaxBitrate>1045</MaxBitrate>
            </H264Layer>
          </H264Layers>
        </H264Video>
        <CopyAudio />
      </Encoding>
      <Outputs>
        <Output FileName="{Basename}{Extension}">
          <MP4Format />
        </Output>
      </Outputs>
    </Preset>


## <a id="silent_audio"></a>Szúrjon be egy csendes hangsávra, ha a bemeneti még nincs hang
Alapértelmezés szerint ha elküldi egy bemenetet a kódolót, amely tartalmazza a csak a videó és hang nélkül, majd a kimeneti objektum tartalmaz csak videó adatokat tartalmazó fájlok. Egyes lejátszók előfordulhat, hogy nem tudja kezelni az ilyen kimeneti adatfolyamokba. Ez a beállítás használatával kényszerítheti a kódoló csendes hangsávra hozzá a kimenetet a forgatókönyvhöz.

A kódoló egy eszköz, amely tartalmazza a beavatkozás nélküli hangsávra, ha bemenet nincs hang előállításához kényszerítéséhez adja meg a "InsertSilenceIfNoAudio" értéket.

Is igénybe vehet a MES-beállításkészletek részletes ismertetését lásd: valamelyik [ez](media-services-mes-presets-overview.md) szakaszt, és győződjön meg arról, a következő módosítást:

### <a name="json-preset"></a>JSON-előbeállítás
    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

### <a name="xml-preset"></a>XML-készletet
    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

## <a id="deinterlacing"></a>Automatikus megszüntetéséhez váltakozó letiltása
Ügyfeleknek nem kell semmit, ha azok automatikusan megszünteti váltakozó kell váltott soros csoportban tartalmát. Ha az automatikus megszüntetéséhez váltakozó (alapértelmezett) a a MES használatával az automatikus észlelés a váltakozó keretek nem, és csak megszüntetéséhez interlaces keretek váltakozó megjelölve.

Kikapcsolhatja az automatikus megszüntetéséhez váltakozó. Ezt a beállítást nem ajánlott.

### <a name="json-preset"></a>JSON-előbeállítás
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

### <a name="xml-preset"></a>XML-készletet
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


## <a id="audio_only"></a>Csak hangfájlt tartalmazó készletek
Ez a szakasz két csak MES-beállításkészletek mutat be: Az AAC hang- és AAC jó minőségű hang.

### <a name="aac-audio"></a>Az AAC-hang
    {
      "Version": 1.0,
      "Codecs": [
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
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

### <a name="aac-good-quality-audio"></a>Az AAC jó minőségű hang
    {
      "Version": 1.0,
      "Codecs": [
        {
          "Profile": "AACLC",
          "Channels": 2,
          "SamplingRate": 48000,
          "Bitrate": 192,
          "Type": "AACAudio"
        }
      ],
      "Outputs": [
        {
          "FileName": "{Basename}_AAC_{AudioBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a id="concatenate"></a>Legalább két videó fájlok összefűzése

A következő példa bemutatja, hogyan hozhat létre egy készletet két vagy több videó fájlok összefűzésére. A leggyakoribb eset akkor, ha egy fejléc vagy egy bemutató hozzáadása a fő videó. A tervezett használata esetén a együtt szerkesztett videofájlok megosztás tulajdonságai (képernyőfelbontást, képkockasebessége, hangsávra száma stb.). Nem kombinálhatók a videók különböző képkockasebességet, vagy különböző számú hangsáv való beavatkozást.

>[!NOTE]
>Az összefűzés funkció a jelenlegi kialakítás vár, hogy tekintetében a felbontás összhangban-e a bemeneti videoklipeket képkockasebessége stb. 

### <a name="requirements-and-considerations"></a>Követelmények és szempontok

* A bemeneti videó kell rendelkeznie egy hangsávra.
* A bemeneti videó minden kell keret költsége.
* Kell külön eszközök a videók feltöltése és a videók állítja be az elsődleges fájlnak a minden egyes eszközbe.
* Érdemes tudni a videók időtartamára.
* Az alábbi előre definiált példák azt feltételezi, hogy a bemeneti videók nulla időbélyegzővel ellátott indítsa el. A StartTime érték esetén módosítsa a videók különböző kiindulási időbélyeget, rendelkezik hasonlóan általában az élő adásokból kell.
* A JSON-készlet lehetővé teszi, hogy a bemeneti eszközök AssetID értékének mutató explicit hivatkozásokat.
* A mintakód feltételezi, hogy a JSON-készlet mentése egy helyi fájlba, például a "C:\supportFiles\preset.json". Azt is feltételezi, hogy létrejöttek-e a két eszköz két videó fájlok feltöltésével, és arról, hogy a eredő AssetID értékeket.
* A kódtöredék és a JSON készletet két videó fájlok összetűzésének példán látható. Kibővítheti a több mint két videókra:

  1. Hívása a feladatot. InputAssets.Add() ismételten hozzáadandó további videók sorrendben.
  2. További bejegyzések hozzáadásával ugyanabban a sorrendben tétele a megfelelő a JSON-t, a "Források" elemének szerkeszti.

### <a name="net-code"></a>.NET-kód

    IAsset asset1 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:606db602-efd7-4436-97b4-c0b867ba195b").FirstOrDefault();
    IAsset asset2 = _context.Assets.Where(asset => asset.Id == "nb:cid:UUID:a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e").FirstOrDefault();

    // Declare a new job.
    IJob job = _context.Jobs.Create("Media Encoder Standard Job for Concatenating Videos");
    // Get a media processor reference, and pass to it the name of the
    // processor to use for the specific task.
    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

    // Load the XML (or JSON) from the local file.
    string configuration = File.ReadAllText(@"c:\supportFiles\preset.json");

    // Create a task
    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
        processor,
        configuration,
        TaskOptions.None);

    // Specify the input videos to be concatenated (in order).
    task.InputAssets.Add(asset1);
    task.InputAssets.Add(asset2);
    // Add an output asset to contain the results of the job.
    // This output is specified as AssetCreationOptions.None, which
    // means the output asset is not encrypted.
    task.OutputAssets.AddNew("Output asset",
        AssetCreationOptions.None);

    job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
    job.Submit();
    job.GetExecutionProgressTask(CancellationToken.None).Wait();

### <a name="json-preset"></a>JSON-előbeállítás

Frissítse az egyéni azonosítókkal összefűzése kívánt eszközök, és minden egyes szegmens megfelelő időpontot.

    {
      "Version": 1.0,
      "Sources": [
        {
          "AssetID": "606db602-efd7-4436-97b4-c0b867ba195b",
          "StartTime": "00:00:01",
          "Duration": "00:00:15"
        },
        {
          "AssetID": "a7e2b90f-0565-4a94-87fe-0a9fa07b9c7e",
          "StartTime": "00:00:02",
          "Duration": "00:00:05"
        }
      ],
      "Codecs": [
        {
          "KeyFrameInterval": "00:00:02",
          "SceneChangeDetection": true,
          "H264Layers": [
            {
              "Level": "auto",
              "Bitrate": 1800,
              "MaxBitrate": 1800,
              "BufferWindow": "00:00:05",
              "BFrames": 3,
              "ReferenceFrames": 3,
              "AdaptiveBFrame": true,
              "Type": "H264Layer",
              "Width": "640",
              "Height": "360",
              "FrameRate": "0/1"
            }
          ],
          "Type": "H264Video"
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
          "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",
          "Format": {
            "Type": "MP4Format"
          }
        }
      ]
    }

## <a id="crop"></a>Videók körülvágása a Media Encoder standarddel
Tekintse meg a [körülvágása a Media Encoder Standard videók](media-services-crop-video.md) témakör.

## <a id="no_video"></a>Szúrjon be egy videó nyomon követése, ha a bemeneti még nincs videó

Alapértelmezés szerint ha elküldi a kódolót, amely tartalmazza a csak hangot, és nincs videó bemenete majd a kimeneti objektum tartalmaz csak hang adatokat tartalmazó fájlok. Néhány lejátszó, köztük az Azure Media Player is (lásd: [ez](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) nem feltétlenül tudja kezelni az ilyen adatfolyamokat. Ez a beállítás használatával kényszerítheti ad hozzá a kimenetet a forgatókönyvhöz egy fekete-fehér videó nyomon követése a kódolót.

> [!NOTE]
> A kódoló beszúrása egy kimeneti videót követése kényszerítése növeli a méretét, a kimenet eszköz, és ezáltal a költségek merülnek fel, a kódolási feladat. Futtassa a tesztet, ellenőrizze, hogy rendelkezik-e a eredő növekvő csak egy mérsékelt hatás az elszámolási időszakban felszámított díjakat az.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Videó: a legalacsonyabb sávszélességű beszúrása

Tegyük fel, hogy egy több sávszélességű kódolási előbeállítást például használ ["H264 Multiple Bitrate 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) a teljes bemeneti katalógus streameléshez, videók és a csak hangfájlt tartalmazó fájlokat tartalmazó kódolása. Ebben a forgatókönyvben Ha a bemenet nem videó, érdemes beszúrása egy fekete-fehér videó nyomon követése, csak a legalacsonyabb sávszélességű szúr be a videó minden kimeneti sávszélességű, ellentétben a kódoló kényszerítése. Ennek érdekében kell használnia a **InsertBlackIfNoVideoBottomLayerOnly** jelzőt.

Is igénybe vehet a MES-beállításkészletek részletes ismertetését lásd: valamelyik [ez](media-services-mes-presets-overview.md) szakaszt, és győződjön meg arról, a következő módosítást:

#### <a name="json-preset"></a>JSON-előbeállítás
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML-készletet

XML használjon feltétel a attribútumaként "InsertBlackIfNoVideoBottomLayerOnly" = az **H264Video** elem és az állapot = való attribútumaként "InsertSilenceIfNoAudio" **AACAudio**.

```
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideoBottomLayerOnly">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .
```

### <a name="inserting-video-at-all-output-bitrates"></a>Minden videó beszúrása kimeneti bitsebességre való átkódolása
Tegyük fel, hogy egy több sávszélességű kódolási előbeállítást például használ ["H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) a teljes bemeneti katalógus streameléshez, videók és a csak hangfájlt tartalmazó fájlokat tartalmazó kódolása. Ebben a forgatókönyvben Ha a bemenet nem videó, érdemes kényszerítése minden beszúrása egy fekete-fehér videó nyomon követése a kimeneti bitsebességre való átkódolása a kódoló. Ez biztosítja, hogy a kimenet olyan eszközök összes homogén megállapodást videó nyomon követi és hangsáv száma. Ennek érdekében adja meg a "InsertBlackIfNoVideo" jelző kell.

Is igénybe vehet a MES-beállításkészletek részletes ismertetését lásd: valamelyik [ez](media-services-mes-presets-overview.md) szakaszt, és győződjön meg arról, a következő módosítást:

#### <a name="json-preset"></a>JSON-előbeállítás
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML-készletet

XML használjon feltétel a attribútumaként "InsertBlackIfNoVideo" = az **H264Video** elem és az állapot = való attribútumaként "InsertSilenceIfNoAudio" **AACAudio**.

```
. . .
<Encoding>
  <H264Video Condition="InsertBlackIfNoVideo">
    <KeyFrameInterval>00:00:02</KeyFrameInterval>
    <SceneChangeDetection>true</SceneChangeDetection>
    <StretchMode>AutoSize</StretchMode>
    <H264Layers>
      <H264Layer>
        . . .
      </H264Layer>
    </H264Layers>
    <Chapters />
  </H264Video>
  <AACAudio Condition="InsertSilenceIfNoAudio">
    <Profile>AACLC</Profile>
    <Channels>2</Channels>
    <SamplingRate>48000</SamplingRate>
    <Bitrate>128</Bitrate>
  </AACAudio>
</Encoding>
. . .  
```

## <a id="rotate_video"></a>Videó elforgatása
A [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) elforgatási szög szerint 0 és 90/180 vagy 270 támogatja. Alapértelmezés szerint az "Auto", amikor megpróbálja Elforgatás metaadatait a bejövő videó fájlban, és azt a meghiúsult lépések kompenzációjához. A következők **források** elem egy megadott készlet [ez](media-services-mes-presets-overview.md) szakaszban:

### <a name="json-preset"></a>JSON-előbeállítás
    "Sources": [
    {
      "Streams": [],
      "Filters": {
        "Rotation": "90"
      }
    }
    ],
    "Codecs": [

    ...
### <a name="xml-preset"></a>XML-készletet
    <Sources>
           <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

Lásd még [ez](media-services-mes-schema.md#PreserveResolutionAfterRotation) témakör további információ a kódoló értelmezése az a készlet beállításait szélességének és magasságának Elforgatás kompenzációs aktiválásakor.

Az érték "0" használatával figyelmen kívül Elforgatás metaadatok, ha a bemeneti videó szerepel a kódoló jelzi.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[A Media Services kódolási áttekintése](media-services-encode-asset.md)
