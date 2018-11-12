---
title: Levágása videókat a Media Encoder Standard – Azure |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan körülvágása a Media Encoder Standard videókat.
services: media-services
documentationcenter: ''
author: anilmur
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/29/2018
ms.author: anilmur;juliako;
ms.openlocfilehash: 3a8888148e1f58cdf2b1893c624b74ec6d6c93da
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282543"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Videók körülvágása a Media Encoder Standarddel
A Media Encoder Standard (MES) használatával a bemeneti videó vágása. Vágás az a folyamat egy téglalap alakú ablakot a képkocka kiválasztásával, és csak az adott időszakon belül képpont kódolás. Az alábbi ábra segítségével ábrázolni a folyamatot.

![Videók körülvágása](./media/media-services-crop-video/media-services-crop-video01.png)

Tegyük fel, bemeneti videó felbontása 1920 x 1080 képpont (16:9 oldalarányának megőrzésével), de a bal és jobb, fekete sávok (pillar mezők) rendelkezik, hogy csak egy 4:3 ablakban vagy 1440 x 1080 képpont aktív videót tartalmaz. MES használatával vágja körül, vagy szerkesztheti a fekete sávok ki, és a 1440 x 1080 régió kódolása.

Egy előfeldolgozási fázis, a MES vágása, így a körbevágási paramétereket a kódolási előbeállítás a alkalmazni a bemeneti videó. Kódolás a későbbi szakaszában, és a szélességének és magasságának beállítások vonatkoznak a *előre feldolgozott* videó, nem pedig az eredeti videó. Amikor megtervezi a készletet kell tegye a következőket: (a) a bemeneti videó alapján körülvágása paraméterek (b) válassza ki és a beállítások alapján a körülvágva videó kódolása. Ha nem egyeznek a beállításokat a körülvágva videó kódolásához, a kimenet nem lesz a várt módon.

A [következő](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) a témakör bemutatja, hogyan kódolási feladat létrehozása a MES használatával és a egy egyedi, a kódolási feladat készletek megadása. 

## <a name="creating-a-custom-preset"></a>Egyéni előbeállítás létrehozása
A példában az ábrán látható:

1. Eredeti bemeneti adat 1920 × 1080 képpont
2. A bemeneti keretben közepén 1440 x 1080 kimenetre csonkolva kell
3. Ez azt jelenti, hogy egy X eltolását (1920 – 1440) / 2 = 240, és a egy Y eltolás a nulla
4. A szélességét és magasságát körülvágása téglalap: 1440 és 1080 képpont, illetve
5. A encode fázisban a három réteg előállítása, megoldások 1440 x 1080, 960 × 720 és 480 x 360, illetve

### <a name="json-preset"></a>JSON-előbeállítás
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


## <a name="restrictions-on-cropping"></a>Vágása korlátozásai
A vágási szolgáltatást helyezni manuális. A bemeneti videó tölthet be egy megfelelő szerkesztési eszköz, amely lehetővé teszi az jelölje ki a lényeges, vigye a kurzort a körbevágási téglalap alakú, a kódolási előbeállítás, amely van hangolva, hogy adott videó, stb. meghatározására tartozó eltolások meghatározásához kell. Ez a funkció nem jelenti azt, hogy többek között: automatikus észlelése és eltávolítása a bemeneti videóhoz a fekete postaláda/pillarbox szegélyek.

A következő korlátozások vonatkoznak a vágási szolgáltatást. Ha ezek nem teljesülnek, a encode feladat is sikertelen, vagy nem várt kimenetet.

1. A fordítani és körülvágása téglalap mérete kell férnie a bemeneti videó
2. Amint már említettük, a szélessége és magassága encode beállításaiban kell megegyeznie a körülvágva videó
3. Vágása fekvő tájolásban rögzített videókra vonatkozik (azaz nem vonatkozik a videók okostelefont rögzített tárolt, függőlegesen vagy álló tájolású módban)
4. Működik a legjobban, négyzet alakú rögzített fokozatos videó

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Következő lépés
Tekintse meg az Azure Media Services képzési tervek segítenek megismerni az AMS által kínált nagyszerű funkciókat.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
