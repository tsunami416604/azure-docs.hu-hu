---
title: "A Media Encoder Standard - Azure videók levágása |} Microsoft Docs"
description: "Ez a cikk bemutatja a Media Encoder Standard videók levágása."
services: media-services
documentationcenter: 
author: anilmur
manager: cfowler
editor: 
ms.assetid: 7628f674-2005-4531-8b61-d7a4f53e46ba
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/09/2017
ms.author: anilmur;juliako;
ms.openlocfilehash: 60d0ce14a271fcbe698559da95ca011cb888b221
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="crop-videos-with-media-encoder-standard"></a>Videók körülvágása a Media Encoder Standarddel
Media Encoder Standard (MES) segítségével a bemeneti videó levágása. A folyamat kiválasztása a videó kerethez téglalap alakú ablak, és csak az adott időszakban képpont kódolás vágás. A következő diagram segít ábrázolni a folyamatot.

![Körülvágása videó](./media/media-services-crop-video/media-services-crop-video01.png)

Tegyük fel, bemeneti videó felbontása 1920 x 1080 képpont (16:9 oldalarány), de rendelkezik fekete sávok (pillar mezők) bal és jobb, így csak a 4:3 ablak vagy 1440 x 1080 képpont aktív videót tartalmaz. MES segítségével körülvágása, vagy szerkesztheti a fekete sávok ki, és a 1440 x 1080 régió kódolása.

Előre feldolgozásra szakasza alatt a MES vágás, így a kódolási beállításkészlet vágási paraméterek érvényesek az eredeti bemeneti videó. Kódolás későbbi, és a szélességének és magasságának beállítások érvényesek a *előre feldolgozott* video-, és nem az eredeti videó. Az előre definiált tervezésekor végre kell hajtani a következő: (a) a körülvágása paraméterek alapján az eredeti bemeneti videó (b) válassza ki és a beállítások alapján a levágott videó kódolása. Ha nem egyezik meg a beállításokat a levágott videó kódolására, a kimenet nem lesz a várt módon.

A [következő](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) a témakör bemutatja, hogyan hozzon létre egy kódolási feladat MES és egy egyéni a kódolási feladat az adott néven beállítás megadása. 

## <a name="creating-a-custom-preset"></a>Egy készlet létrehozása
A példában az ábrán is látható:

1. Eredeti bemeneti érték 1920 x 1080
2. A bemeneti keretében középre 1440 x 1080 kimenete az részei le lesznek vágva kell
3. Ez azt jelenti, hogy egy X eltolását (1920 – 1440) / 2 = 240, és egy Y eltolás: 0
4. A szélességét és magasságát a körülvágása téglalap: 1440 és 1080, illetve
5. Encode szakaszában a kérjen három réteg előállítása, megoldások 1440 x 1080, 960 x 720 és 480 x 360, illetve

### <a name="json-preset"></a>JSON-készlet
    {
      "Version": 1.0,
      "Sources": [
        {
          "Streams": [],
          "Filters": {
            "Crop": {
                "X": 240,
                "Y": 0,
                "Width": 1440,
                "Height": 1080
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
              "Bitrate": 3400,
              "MaxBitrate": 3400,
              "BufferWindow": "00:00:05",
              "Width": 1440,
              "Height": 1080,
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
              "Bitrate": 1250,
              "MaxBitrate": 1250,
              "BufferWindow": "00:00:05",
              "Width": 480,
              "Height": 360,
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


## <a name="restrictions-on-cropping"></a>Vágás korlátozásai
A vágási szolgáltatást arra szolgál, hogy manuálisan kell. A bemeneti videó betölthető egy megfelelő szerkesztési eszköz, amely lehetővé teszi, hogy válassza ki az egyik fontos keretek, a kurzor meghatározásához a kódolási készlet, amely az adott adott video-stb van beállítva a vágási téglalap tartozó eltolások meghatározásához kellene. Ez a funkció nem jelenti azt, hogy többek között: automatikus észlelése és eltávolítása a bemeneti videóhoz fekete postaláda/pillarbox szegélyek.

Következő korlátozások vonatkoznak a vágási szolgáltatásra vonatkoznak. Ha ezek nem teljesülnek, a encode feladatütemezés is sikertelen, vagy egy váratlan eredménye.

1. A fordítani és a vágás téglalap mérete rendelkezik méretéhez igazítja a bemeneti videó
2. Fent említett szélessége és magassága encode beállítások kell megegyeznie a levágott videó
3. A fekvő tájolás rögzített videók vágás vonatkozik (azaz nem alkalmazható okostelefont rögzített videók tárolt függőleges vagy álló módban)
4. Működik a legjobban a négyzetes képpont rögzített fokozatos videó

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Következő lépés
Azure Media Services tanulási útvonalai, az AMS kiváló szolgáltatásaira témákhoz talál.  

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]
