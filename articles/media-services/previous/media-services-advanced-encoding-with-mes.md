---
title: Kódolásra speciális MES készletek testreszabásával |} Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan kódolásra speciális Media Encoder Standard feladat készletek testreszabásával.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 2a4ade25-e600-4bce-a66e-e29cf4a38369
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: juliako
ms.openlocfilehash: 9480e6f3f651611e5281968d6d1651bd39dda44f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788920"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Kódolásra speciális MES készletek testreszabásával. 

## <a name="overview"></a>Áttekintés

Ez a témakör bemutatja, hogyan szabhatja testre a Media Encoder Standard készletek. A [kódolása a Media Encoder Standard használatával egyéni készletek](media-services-custom-mes-presets-with-dotnet.md) a témakör bemutatja, hogyan lehet .NET segítségével hozzon létre egy kódolási feladat és egy feladatot, amely végrehajtja ezt a feladatot. Egy előre definiált és testreszabott, adja meg az egyéni készletek a kódolási feladat. 

>[!NOTE]
>Ha egy XML-készletet használ, ügyeljen arra, hogy elemek sorrendjének megőrzése ahogy az alábbi XML-minták (például KeyFrameInterval előtt kell állnia SceneChangeDetection).
>

Ebben a témakörben az egyéni készletek, amelyek a következő kódolási feladatokat egy.

## <a name="support-for-relative-sizes"></a>Relatív méretek támogatása

Miniatűrök létrehozásakor nem kell mindig adja meg kimeneti szélessége és magassága képpontban megadva. Százalékos értékként, a tartomány [1 %,..., 100 %-os] megadhatja azokat.

### <a name="json-preset"></a>JSON-készlet
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>XML-készlet
    <Width>100%</Width>
    <Height>100%</Height>

## <a id="thumbnails"></a>Indexképének létrehozására

Ez a szakasz bemutatja, hogyan szabhatja testre a készletet, amely hoz létre a miniatűrökön. Az alábbiakban meghatározott előre definiált ismerteti, hogyan szeretné kódolni, a fájl, valamint a fordítás során indexképének létrehozására használnak. Is igénybe vehet a dokumentált MES készletek bármelyikét [ez](media-services-mes-presets-overview.md) szakaszt, és adja hozzá a miniatűrök generáló kódot.  

> [!NOTE]
> A **SceneChangeDetection** a következő készlet-beállítás csak akkor állítható igaz értékre, ha a kódolni kívánt videó egyszeres sávszélességű. Ha egy többszörös sávszélességű videó és a készlet kódolást **SceneChangeDetection** az értéke igaz, a kódoló hibát ad vissza.  
>
>

Séma kapcsolatos információkért lásd: [ez](media-services-mes-schema.md) témakör.

Mindenképpen tekintse át a [szempontok](#considerations) szakasz.

### <a id="json"></a>JSON-készlet
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


### <a id="xml"></a>XML-készlet
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

A következők érvényesek:

* Start/lépés/címtartomány explicit időbélyegeket használatát feltételezi, hogy a bemeneti forrás legalább 1 percnek hosszú.
* JPG vagy Png/BmpImage elemek elindítani, lépés, és karakterlánc-attribútumok között – ezek úgy:

  * Keret számát, ha azok nem negatív egész számokat, például "Start": "120"
  * Relatív forrás időtartama, ha kifejezett % utótaggal szerint, például "Start": "15 %", vagy
  * Ha óó: pp: kifejezett időbélyeg... formázása, például "Start": "00: 01:00"

    Ön szabadon kombinálhatók jelölések, ha Ön adja.

    Emellett Start is támogatja a speciális makró: {ajánlott}, amely kísérli meg meghatározni a tartalom "érdekes" első keretében: (lépés, és a tartomány figyelmen kívül lesznek hagyva Ha kezdő {legjobb} értékre van állítva)
  * Alapértelmezett: Start: {legjobb}
* Kimeneti formátumot kell explicit módon meg kell adni az egyes képformátum: Jpg vagy Png/BmpFormat. Ha létezik, MES felel meg a JpgFormat JpgVideo és így tovább. OutputFormat bevezet egy új lemezkép-kodek adott makró: {Index}, mely rendelkeznie kell megjeleníteni az (egyszer és csak egyszer) kimeneti képformátum.

## <a id="trim_video"></a>Trim (vágás) videó
Ez a szakasz beszél a kódoló készletek levágása, vagy ha a bemeneti érték egy úgynevezett mezzazine-fájlt vagy a tárolt fájl a bemeneti videó trim módosítása. A kódoló is használható levágása, vagy egy eszköz, ez rögzített vagy egy élő adatfolyam archivált trim – Ez a részletes adatok találhatók [ebben a blogban](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

Lehet levágni a videók, elvégezhető a dokumentált MES készletek bármelyikét [ez](media-services-mes-presets-overview.md) szakaszt, és módosítsa a **források** elem (mivel lásd alább). A StartTime értékének a bemeneti videó abszolút időbélyegeket egyezniük kell. Például, ha a bemeneti videó első keret 12:00:10.000 időbélyeggel rendelkezik, majd StartTime kell lennie, mint 12:00:10.000 és nagyobb. Az alábbi példában feltételezzük, hogy a bemeneti videó nulla kezdési időbélyeggel rendelkezik-e. **Adatforrások** kell helyezni az előre definiált elején.

### <a id="json"></a>JSON-készlet
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

### <a name="xml-preset"></a>XML-készlet
Lehet levágni a videók, elvégezhető a dokumentált MES készletek bármelyikét [Itt](media-services-mes-presets-overview.md) , és módosítsa a **források** elem (mivel lásd alább).

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

A Media Encoder Standard segítségével átfedő egy meglévő video egy képet. Jelenleg a következő formátumok használhatók: png, jpg, gif, bmp és. Az alábbiakban meghatározott előre definiált egy egyszerű példa egy videófelirat.

Mellett egy előre definiált fájl határozza meg, akkor is, hogy a Media Services tudja, az eszköz milyen fájl az átmeneti területre lemezképét és melyik videó adatforrás, amelyre a kép átfedő szeretne. A videó fájl rendelkezik kell lennie a **elsődleges** fájlt.

Ha .NET használ, adja hozzá a következő két funkciók a meghatározott .NET típusú példát [ez](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) témakör. A **UploadMediaFilesFromFolder** függvény fel a fájlokat (például BigBuckBunny.mp4 és Image001.png) egy mappából, és beállítja az elsődleges fájlnak az adategységben mp4-fájl. A **EncodeWithOverlay** függvény azt (például a készletet, amely a következő) számára átadott egyéni előre definiált fájlt használja a kódolási feladat létrehozásához.


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
> Aktuális korlátozások vonatkoznak:
>
> Az átmeneti területre átlátszatlanság beállítás nem támogatott.
>
> A videó forrásfájlt és az átmeneti területre képfájl biztosítani kell, az azonos eszköz, és a videofájl kell állítani, ez az eszköz elsődleges fájlként.
>
>

### <a name="json-preset"></a>JSON-készlet
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


### <a name="xml-preset"></a>XML-készlet
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


## <a id="silent_audio"></a>Csendes hang nyomon beszúrni, ha bemeneti nincsenek hang
Alapértelmezés szerint ha küldi el a kódoló csak videó, és nincsenek hang tartalmazó bemenete majd a kimeneti adategységen tartalmaz csak videó adatokat tartalmazó fájlt. Előfordulhat, hogy néhány lejátszó nem kezeli az ilyen kimeneti adatfolyamokba. Ez a beállítás segítségével csendes hang nyomon hozzáadása a kimenetet a forgatókönyv a kódoló kényszerítése.

Ha bemeneti hang nélkül csendes hang nyomon tartalmazó objektumot eredményezett a kódoló kényszerítéséhez "InsertSilenceIfNoAudio" értéket adja meg.

A részletes ismertetését lásd: MES készletek bármelyikét eltarthat [ez](media-services-mes-presets-overview.md) szakaszt, és ellenőrizze a következő módosítást:

### <a name="json-preset"></a>JSON-készlet
    {
      "Channels": 2,
      "SamplingRate": 44100,
      "Bitrate": 96,
      "Type": "AACAudio",
      "Condition": "InsertSilenceIfNoAudio"
    }

### <a name="xml-preset"></a>XML-készlet
    <AACAudio Condition="InsertSilenceIfNoAudio">
      <Channels>2</Channels>
      <SamplingRate>44100</SamplingRate>
      <Bitrate>96</Bitrate>
    </AACAudio>

## <a id="deinterlacing"></a>Automatikus deszerializálni rövidebbnek letiltása
Az ügyfelek bármilyen teendő, ha azokat, például a váltott soros csoportban tartalmukat automatikusan deszerializálni váltakozó nem szükséges. Ha az automatikus deszerializálni rövidebbnek (alapértelmezett) az a MES váltakozó keretek automatikus észlelése nem, és csak deszerializálni interlaces keretek váltakozó megjelölve.

Bármikor kikapcsolhatják az automatikus deszerializálni rövidebbnek. Ez a lehetőség nem ajánlott.

### <a name="json-preset"></a>JSON-készlet
    "Sources": [
    {
     "Filters": {
        "Deinterlace": {
          "Mode": "Off"
        }
      },
    }
    ]

### <a name="xml-preset"></a>XML-készlet
    <Sources>
    <Source>
      <Filters>
        <Deinterlace>
          <Mode>Off</Mode>
        </Deinterlace>
      </Filters>
    </Source>
    </Sources>


## <a id="audio_only"></a>Csak hang-készletek
Ez a szakasz azt mutatja be két csak MES készletek: AAC hang- és AAC jó minőségű hang.

### <a name="aac-audio"></a>AAC hang
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

### <a name="aac-good-quality-audio"></a>AAC jó minőségű hang
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

## <a id="concatenate"></a>ÖSSZEFŰZ két vagy több videofájlok

A következő példa bemutatja, hogyan hozhat létre egy készletet két vagy több videofájlok összefűzésére. A leggyakoribb eset az, amikor a fejléc vagy egy hozzáadása a fő videó. A tervezett használattól akkor, ha a szerkesztett együtt videofájlok megosztás tulajdonságai (képernyőfelbontást képkockasebessége, zenei száma, stb.). Meg kell ügyeljen arra nem, videókhoz, amelyeket a különböző keret díjszabás, illetve zeneszámok különböző számú kombinálhatók.

>[!NOTE]
>Az aktuális terv a kapott funkció vár, hogy a bemeneti videó videóklipeket megegyeznek a megoldás, tekintetében képkockasebessége stb. 

### <a name="requirements-and-considerations"></a>Követelmények és szempontok

* A bemeneti videó csak kell rendelkeznie egy hang követése.
* Az összes rendelkeznie kell a azonos képkockasebessége bemeneti videók.
* Kell tölteni a videók a különböző eszközök, és a videók állítja be az elsődleges fájl az egyes eszközökre.
* A videók időtartama tudnia kell.
* Az előre definiált az alábbi példák azt feltételezi, hogy, hogy a bemeneti videók indítsa el az időbélyegzőnek nulla. Módosítania kell a StartTime érték Ha a videók különböző kezdési Timestamp típusú, mint általában élő archívummal rendelkező esetében.
* A JSON-készlet lehetővé teszi, hogy a bemeneti eszközök AssetID értékének mutató explicit hivatkozásokat.
* A mintakód azt feltételezi, hogy mentette-e a JSON-készlet egy helyi fájlba, például a "C:\supportFiles\preset.json". Azt is feltételezi, hogy létrejöttek-e a két eszközök két videofájlok feltöltésével, és arról, hogy az eredményül kapott AssetID értékeket.
* A kódrészletet, és JSON készletet két videofájlok hozzáfűzésével példáját mutatja be. A több mint két videók által bővíthető:

  1. Hívása a feladatot. InputAssets.Add() ismételten hozzáadandó további videók sorrendben.
  2. Tétele a megfelelő módosítása a "Források" elem a JSON-ban ugyanabban a sorrendben több bejegyzés hozzáadásával.

### <a name="net-code"></a>.NET-kódot

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

### <a name="json-preset"></a>JSON-készlet

Frissítse az egyéni összefűzésére kívánt eszközök azonosítókat, és a megfelelő időpontban szegmens minden egyes videót.

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

## <a id="crop"></a>A Media Encoder Standard körülvágása videók
Tekintse meg a [a Media Encoder Standard videók körülvágása](media-services-crop-video.md) témakör.

## <a id="no_video"></a>Videó nyomon beszúrni, ha bemeneti nincs kép

Alapértelmezés szerint ha küldi el a kódoló csak hang, és a nem kép tartalmazó bemenete majd a kimeneti adategységen tartalmaz csak hang adatokat tartalmazó fájlt. Néhány szereplő, köztük az Azure Media Player (lásd: [ez](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)) nem lehet kezelni ilyen adatfolyamokat. Ez a beállítás segítségével kényszerítheti a fekete-fehér videó nyomon hozzáadása a kimenetet a forgatókönyv a kódoló.

> [!NOTE]
> A kódoló beszúrása egy kimeneti videó követése kényszerített növeli a kimeneti méretét eszköz, és ezáltal a költségeket a kódolási feladat. Futtassa a teszt futtatásával győződjön meg arról, hogy ezzel a növekedéssel eredő csak mérsékelt hatást gyakorol a havi költségeket.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Videó: csak a legalacsonyabb sávszélességű beszúrása

Tegyük fel, amelyek több sávszélességű kódolás használatával, mint a beállított ["H264 Multiple Bitrate 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) a teljes bemeneti katalógus az adatfolyamként történő, videofájlok lejátszását, és csak hang-fájlokat tartalmazó kódolására. Ebben a forgatókönyvben Ha a bemeneti adatok nem videó előfordulhat, hogy kényszeríteni kívánja a kódoló beszúrása egy fekete-fehér videó nyomon követése, csak a legalacsonyabb sávszélességű videó beszúrása az összes kimeneti sávszélességű szemben. Ennek érdekében kell használnia a **InsertBlackIfNoVideoBottomLayerOnly** jelzőt.

A részletes ismertetését lásd: MES készletek bármelyikét eltarthat [ez](media-services-mes-presets-overview.md) szakaszt, és ellenőrizze a következő módosítást:

#### <a name="json-preset"></a>JSON-készlet
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML-készlet

XML használatakor használjon feltétel = a attribútumként "InsertBlackIfNoVideoBottomLayerOnly" a **H264Video** elem és a feltétel a attribútumként "InsertSilenceIfNoAudio" = **AACAudio**.

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

### <a name="inserting-video-at-all-output-bitrates"></a>A kimeneti bitrates videó minden beszúrása
Tegyük fel, amelyek több sávszélességű kódolás használatával, mint a beállított ["H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) a teljes bemeneti katalógus az adatfolyamként történő, videofájlok lejátszását, és csak hang-fájlokat tartalmazó kódolására. Ebben a forgatókönyvben Ha a bemeneti adatok nem videó előfordulhat, hogy kényszeríteni kívánja a kódoló minden beszúrása egy fekete-fehér videó nyomon követése a kimeneti bitrates. Ez biztosítja, hogy a kimeneti eszközök olyan összes homogén videó sávok száma és zeneszámok tekintetében. Ennek érdekében meg kell adnia a "InsertBlackIfNoVideo" jelzőt.

A részletes ismertetését lásd: MES készletek bármelyikét eltarthat [ez](media-services-mes-presets-overview.md) szakaszt, és ellenőrizze a következő módosítást:

#### <a name="json-preset"></a>JSON-készlet
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML-készlet

XML használatakor használjon feltétel = a attribútumként "InsertBlackIfNoVideo" a **H264Video** elem és a feltétel a attribútumként "InsertSilenceIfNoAudio" = **AACAudio**.

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
A [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) elforgatási szög által 0/90/180 vagy 270 támogatja. Az alapértelmezett viselkedés az "Auto", ha megkísérli a bejövő videofájl Elforgatás metaadatok észlelését, és ellensúlyozza a azt. Adja meg a következőket **források** elemben, amely a meghatározott készletek egyikét [ez](media-services-mes-presets-overview.md) szakasz:

### <a name="json-preset"></a>JSON-készlet
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
### <a name="xml-preset"></a>XML-készlet
    <Sources>
           <Source>
          <Streams />
          <Filters>
            <Rotation>90</Rotation>
          </Filters>
        </Source>
    </Sources>

Lásd még [ez](media-services-mes-schema.md#PreserveResolutionAfterRotation) talál további információkat a kódoló értelmezése a készletet, szélességének és magasságának szolgáltatásbeállításai Elforgatás kompenzációs kiváltásakor a.

Az érték "0" segítségével jelzi az elforgatás metaadat figyelmen kívül hagyásához, ha jelen van, a bemeneti videóhoz a kódoló.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Media Services kódolási áttekintése](media-services-encode-asset.md)
