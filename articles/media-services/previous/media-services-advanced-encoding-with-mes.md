---
title: Speciális kódolás végrehajtása mes-készletek testreszabásával | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan hajtható végre a speciális kódolás a Media Encoder Standard feladatkészletek testreszabásával.
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
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 5f7611fd9df207df51fa0e51218d8a234583b1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529783"
---
# <a name="perform-advanced-encoding-by-customizing-mes-presets"></a>Speciális kódolás végrehajtása a MES-készletek testreszabásával 

## <a name="overview"></a>Áttekintés

Ez a témakör bemutatja, hogyan szabhatja testre a Media Encoder Standard készleteket. A [Media Encoder Standard kódolásegyéni készleteket használó](media-services-custom-mes-presets-with-dotnet.md) témakör bemutatja, hogyan lehet a .NET használatával kódolási feladatot és a feladatot végrehajtó feladatot létrehozni. Miután testreszabott egy készletet, adja meg az egyéni készleteket a kódolási feladathoz. 

Ha XML-készletet használ, ügyeljen arra, hogy megőrizze az elemek sorrendjét, ahogy az az alábbi XML-mintákban látható (például a KeyFrameInterval-nak meg kell előznie a SceneChangeDetection-t).

> [!NOTE] 
> A Media Encoder Standard számos speciális Media Services v2-es szolgáltatása jelenleg nem érhető el a v3-ban. További információt a szolgáltatáshiányosságai című [témakörben talál.](https://docs.microsoft.com/azure/media-services/latest/media-services-v2-vs-v3#feature-gaps-with-respect-to-v2-apis)

## <a name="support-for-relative-sizes"></a>A relatív méretek támogatása

Bélyegképek létrehozásakor nem kell mindig képpontban megadni a kimeneti szélességet és magasságot. Megadhatja őket százalékban, a tartományban [1%, ..., 100%].

### <a name="json-preset"></a>JSON készlet
    "Width": "100%",
    "Height": "100%"

### <a name="xml-preset"></a>XML-készlet
    <Width>100%</Width>
    <Height>100%</Height>

## <a name="generate-thumbnails"></a><a id="thumbnails"></a>Miniatűrök létrehozása

Ez a szakasz bemutatja, hogyan szabható testre egy miniatűröket létrehozó készlet. Az alábbiakban meghatározott készlet információkat tartalmaz afájl kódolásáról, valamint a miniatűrök létrehozásához szükséges információkat. Bármelyik MES-készletet elkészítheti, amely dokumentálja [ezt a](media-services-mes-presets-overview.md) szakaszt, és hozzáadhat olyan kódot, amely miniatűröket generál.  

> [!NOTE]
> A következő készletBen a **SceneChangeDetection** beállítás csak akkor állítható igaz értékre, ha egyetlen bitrátavideóra kódol. Ha többbitráta-sebességű videóra kódol, és a **SceneChangeDetection** értékét igaz értékre állítja, a kódoló hibát ad vissza.  
>
>

A sémáról a [következő](media-services-mes-schema.md) témakörben olvashat bővebben.

Győződjön meg arról, hogy áttekinti a [Szempontok szakaszt.](#considerations)

### <a name="json-preset"></a><a id="json"></a>JSON készlet
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


### <a name="xml-preset"></a><a id="xml"></a>XML-készlet
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

* Explicit időbélyegek használata a Start/Step/Range feltételezi, hogy a bemeneti forrás legalább 1 perc hosszú.
* Jpg /Png/BmpImage elemek Start, Step és Range karakterlánc attribútumokkal rendelkeznek – ezek a következőképpen értelmezhetők:

  * Keretszám, ha nem negatív egész számok, például "Start": "120",
  * A forrás időtartamához viszonyítva, ha %utótagban van kifejezve, például "Start": "15%", VAGY
  * Időbélyeg, ha hh:PP:SS- ben van kifejezve... formátum, például "Start" : "00:01:00"

    Keverheti és párosíthatja a jelöléseket, ahogy tetszik.

    Ezenkívül a Start egy speciális makrót is támogat:{Best}, amely megpróbálja meghatározni a tartalom első "érdekes" keretét MEGJEGYZÉS: (A lépés és a tartomány figyelmen kívül lesz hagyva, ha a Start értéke {Best})
  * Alapértelmezések: Start:{Best}
* A kimeneti formátumot minden képformátumhoz kifejezetten meg kell adni: Jpg/Png/BmpFormat. Ha jelen van, a MES a JpgVideo-t jpgformat-ra és így tovább egyezik. A OutputFormat egy új képkokod-specifikus makrót vezet be: {Index}, amelynek jelen kell lennie (egyszer és csak egyszer) a képkimeneti formátumokhoz.

## <a name="trim-a-video-clipping"></a><a id="trim_video"></a>Videó vágása (vágás)
Ez a szakasz a kódoló készletek módosításáról szól, hogy kivágja vagy vágja le a bemeneti videót, ahol a bemenet irt. A kódoló is használható klip vagy berendezés egy eszköz, amely rögzített vagy archivált egy élő közvetítés - a részleteket e állnak rendelkezésre ebben a [blogban](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).

A videók vágásához az [ebben](media-services-mes-presets-overview.md) a szakaszban dokumentált MES-készletek bármelyikét elviheti, és módosíthatja a **Források** elemet (lásd alább). A StartTime értékének meg kell egyeznie a bemeneti videó abszolút időbélyegeivel. Ha például a bemeneti videó első képkockájának időbélyegzője 12:00:10.000, akkor a StartTime-nak legalább 12:00:10.000-nek kell lennie. Az alábbi példában azt feltételezzük, hogy a bemeneti videó kezdő időbélyege nulla. **A forrásokat** az előre beállított készlet elejére kell helyezni.

### <a name="json-preset"></a><a id="json"></a>JSON készlet
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
A videók vágásához az [itt](media-services-mes-presets-overview.md) dokumentált MES-készletek bármelyikét elviheti, és módosíthatja a **Források** elemet (lásd alább).

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
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

## <a name="create-an-overlay"></a><a id="overlay"></a>Átfedés létrehozása

A Media Encoder Standard lehetővé teszi, hogy egy képet egy meglévő videóra fedjen. Jelenleg a következő formátumok támogatottak: png, jpg, gif és bmp. Az alábbiakban definiált készlet egy egyszerű példa a videó fedvény.

Az előre beállított fájl definiálása mellett tudatnia kell a Media Services szolgáltatással, hogy melyik fájl van az eszközben az átfedési kép, és melyik fájl az a forrásvideó, amelyre a képet át szeretné fedni. A videofájlnak kell lennie az **elsődleges** fájlnak.

A .NET használata esetén adja hozzá a következő két függvényt a [témakörben](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) definiált .NET példához. Az **UploadMediaFilesFromFolder** függvény fájlokat tölt fel egy mappából (például BigBuckBunny.mp4 és Image001.png), és az mp4 fájlt az eszköz elsődleges fájljának állítja be. Az **EncodeWithOverlay** függvény a neki átadott egyéni előre beállított fájlt (például az azt követő készletet) használja a kódolási feladat létrehozásához.


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
> Jelenlegi korlátozások:
>
> Az átfedés opacitási beállítása nem támogatott.
>
> A forrásvideó-fájlnak és az átfedési képfájlnak ugyanabban az eszközben kell lennie, és a videofájlt kell elsődleges fájlként beállítani ebben az eszközben.
>
>

### <a name="json-preset"></a>JSON készlet
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
                  "OverlayLoopCount": 1
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
    <Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
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
                </MediaParam>
                <MediaParam>
                  <IsOverlay>true</IsOverlay>
                  <OverlayLoopCount>1</OverlayLoopCount>
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


## <a name="insert-a-silent-audio-track-when-input-has-no-audio"></a><a id="silent_audio"></a>Néma hangsáv beszúrása, ha a bemenet nem rendelkezik hanggal
Alapértelmezés szerint, ha olyan bemenetet küld a kódolónak, amely csak videót tartalmaz, és nem tartalmaz hangot, akkor a kimeneti eszköz olyan fájlokat tartalmaz, amelyek csak videoadatokat tartalmaznak. Előfordulhat, hogy egyes játékosok nem tudják kezelni az ilyen kimeneti adatfolyamokat. Ezzel a beállítással kényszerítheti a kódolót, hogy csendes hangsávot adjon a kimenethez ebben a forgatókönyvben.

Ha azt szeretné, hogy a kódoló olyan eszközt állítson létre, amely néma hangsávot tartalmaz, ha a bemenet nem rendelkezik hanggal, adja meg az "InsertSilenceIfNoAudio" értéket.

Az [ebben](media-services-mes-presets-overview.md) a szakaszban dokumentált MES-készletek bármelyikét elláthatja, és elvégezheti a következő módosításokat:

### <a name="json-preset"></a>JSON készlet
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

## <a name="disable-auto-de-interlacing"></a><a id="deinterlacing"></a>Automatikus áthatolás letiltása
Az ügyfeleknek nem kell semmit sem tenniük, ha szeretnék, ha a váltottsoros tartalmak automatikusan kilesznek eresztve. Ha az automatikus de-interlacing be van kapcsolva (alapértelmezett), a MES nem az automatikus kimutatására váltottsoros keretek és csak de-interlaces keretek jelölt váltottsoros.

Kikapcsolhatja az automatikus de-interlacing kikapcsolását. Ez a beállítás nem ajánlott.

### <a name="json-preset"></a>JSON készlet
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


## <a name="audio-only-presets"></a><a id="audio_only"></a>Csak hangbeállítások
Ez a rész két csak hanggal kapcsolatos MES-készletet mutat be: AAC Audio és AAC Good Quality Audio.

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

## <a name="concatenate-two-or-more-video-files"></a><a id="concatenate"></a>Két vagy több videofájl összefűzése

A következő példa bemutatja, hogyan hozhat létre egy készletet két vagy több videofájl összefűzésére. A leggyakoribb forgatókönyv az, amikor fejlécet vagy előzetest szeretne hozzáadni a fő videóhoz. A tervezett felhasználás az, amikor a szerkesztett videofájlok megosztják a tulajdonságokat (videofelbontás, képkockasebesség, hangsávszám stb.). Ügyeljen arra, hogy ne keverje össze a különböző képkockasebességgel rendelkező videókat, vagy a különböző számú hangsávot.

>[!NOTE]
>Az összefűzés funkció jelenlegi kialakítása arra számít, hogy a bemeneti videoklipek konzisztensek a felbontás, a képkockasebesség stb. 

### <a name="requirements-and-considerations"></a>Követelmények és megfontolások

* A bemeneti videóknak csak egy hangsávval kell rendelkezniük.
* A bemeneti videók nak azonos képkockasebességgel kell rendelkezniük.
* A videókat külön eszközökbe kell feltölteni, és a videókat az egyes eszközök elsődleges fájljaként kell beállítani.
* Tudnod kell a videók időtartamát.
* Az alábbi előre beállított példák feltételezik, hogy az összes bemeneti videó nullával kezdődik. Módosítania kell a StartTime-értékeket, ha a videók eltérő indítási időbélyeggel rendelkeznek, mint általában az élő archívumok esetében.
* A JSON-készlet explicit hivatkozásokat tesz az input eszközök Eszközazonosító értékére.
* A mintakód feltételezi, hogy a JSON-készlet egy helyi fájlba lett mentve, például "C:\supportFiles\preset.json". Azt is feltételezi, hogy két eszköz két videofájl feltöltésével jött létre, és ismeri a kapott AssetID értékeket.
* A kódrészlet és a JSON-készlet két videofájl összefűzésének példáját mutatja. A következő konkretitissza ki kettőnél több videóra:

  1. Hívás feladat. InputAssets.Add() többször is hozzá több videót, sorrendben.
  2. A JSON "Források" elemének megfelelő szerkesztése további bejegyzések hozzáadásával, ugyanabban a sorrendben.

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

### <a name="json-preset"></a>JSON készlet

Frissítse az egyéni készletet az összefonni kívánt eszközök azonosítóival és az egyes videók megfelelő időszegmensével.

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

## <a name="crop-videos-with-media-encoder-standard"></a><a id="crop"></a>Videók körülvágása a Media Encoder Standarddel
Tekintse meg a [Crop videók Media Encoder Standard témakörben.](media-services-crop-video.md)

## <a name="insert-a-video-track-when-input-has-no-video"></a><a id="no_video"></a>Videosáv beszúrása, ha a bemenet nem rendelkezik videóval

Alapértelmezés szerint, ha olyan bemenetet küld a kódolónak, amely csak hangot tartalmaz, és nem tartalmaz videót, akkor a kimeneti eszköz olyan fájlokat tartalmaz, amelyek csak hangadatokat tartalmaznak. Előfordulhat, hogy egyes lejátszók, köztük az Azure Media Player [(lásd ezt)](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/8082468-audio-only-scenarios)nem tudják kezelni az ilyen adatfolyamokat. Ezzel a beállítással kényszerítheti a kódolót, hogy monokróm videosávot adjon a kimenethez ebben a forgatókönyvben.

> [!NOTE]
> A kódoló kényszerítése egy kimeneti videosáv beszúrására növeli a kimeneti eszköz méretét, és ezáltal a kódolási feladat költségeit. Teszteket kell futtatnia annak ellenőrzésére, hogy ez az eredő növekedés csak szerény hatással van-e a havi díjakra.
>

### <a name="inserting-video-at-only-the-lowest-bitrate"></a>Videó beszúrása csak a legalacsonyabb bitrátával

Tegyük fel, hogy több bitráta-kódolási készletet használ, például [a "H264 Multiple Bitrate 720p"](media-services-mes-preset-h264-multiple-bitrate-720p.md) a teljes bemeneti katalógus kódolásához a streameléshez, amely videofájlok és csak hangfájlok keverékét tartalmazza. Ebben az esetben, ha a bemenetnem rendelkezik videóval, érdemes lehet kényszeríteni a kódolót, hogy egy monokróm videosávot helyezzen be a legalacsonyabb bitrátával, szemben a videó beillesztésével minden kimeneti bitráta esetén. Ennek eléréséhez az **InsertBlackIfNoVideoBottomLayerOnly** jelzőt kell használnia.

Az [ebben](media-services-mes-presets-overview.md) a szakaszban dokumentált MES-készletek bármelyikét elláthatja, és elvégezheti a következő módosításokat:

#### <a name="json-preset"></a>JSON készlet
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideoBottomLayerOnly",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML-készlet

XML használatakor használja a Condition="InsertBlackIfNoVideoBottomLayerOnly" tulajdonságot a **H264Video** elem attribútumaként, és a Condition="InsertSilenceIfNoAudio" attribútumot **az AACAudio**attribútumként.

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

### <a name="inserting-video-at-all-output-bitrates"></a>Videó beszúrása az összes kimeneti bitrátára
Tegyük fel, hogy több bitráta kódolási készletet használ, például [a "H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) a teljes bemeneti katalógus kódolásához a streameléshez, amely videofájlok és csak hangfájlok keverékét tartalmazza. Ebben a forgatókönyvben, ha a bemenet nem rendelkezik videóval, érdemes lehet kényszeríteni a kódolót, hogy helyezzen be egy monokróm videosávot az összes kimeneti bitrátába. Ez biztosítja, hogy a kimeneti eszközök homogének tekintetében számos video-és hangsávot. Ennek eléréséhez meg kell adnia az "InsertBlackIfNoVideo" jelzőt.

Az [ebben](media-services-mes-presets-overview.md) a szakaszban dokumentált MES-készletek bármelyikét elláthatja, és elvégezheti a következő módosításokat:

#### <a name="json-preset"></a>JSON készlet
    {
          "KeyFrameInterval": "00:00:02",
          "StretchMode": "AutoSize",
          "Condition": "InsertBlackIfNoVideo",
          "H264Layers": [
          …
          ]
    }

#### <a name="xml-preset"></a>XML-készlet

XML használatakor használja a Condition="InsertBlackIfNoVideo" tulajdonságot a **H264Video** elem attribútumaként, és a Condition="InsertSilenceIfNoAudio" attribútumot az **AACAudio**attribútumként.

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

## <a name="rotate-a-video"></a><a id="rotate_video"></a>Videó elforgatása
A [Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) 0/90/180/270 szögben támogatja a forgatást. Az alapértelmezett viselkedés az "Automatikus", ahol megpróbálja észlelni a bejövő videofájl elforgatási metaadatait, és kompenzálni azt. Az [ebben](media-services-mes-presets-overview.md) a szakaszban definiált készletek egyikének a következő **Források** elemet kell feltüntetnie:

### <a name="json-preset"></a>JSON készlet
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

A [jelen](media-services-mes-schema.md#PreserveResolutionAfterRotation) témakörben további információt talál arról, hogy a kódoló hogyan értelmezi a szélesség és a magasság beállításokat a készletben, amikor az elforgatáskompenzáció aktiválódik.

A "0" értékkel jelezheti a kódolónak, hogy figyelmen kívül hagyja a bemeneti videóban lévő elforgatási metaadatokat, ha vannak ilyenek.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Media Services kódolás – áttekintés](media-services-encode-asset.md)
