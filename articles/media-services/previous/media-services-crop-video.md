---
title: Videók vágása a Media Encoder Standard - Azure | Microsoft dokumentumok
description: A körülvágás az a folyamat, amelynek során négyszögletes ablakot választ ki a videokereten belül, és csak az ablakon belüli képpontokat kódolja. Ez a cikk bemutatja, hogyan vághatja le a videókat a Media Encoder Standard segítségével.
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
ms.date: 03/18/2019
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: 059816284e39c65bb772bd02f066d73da624722f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887764"
---
# <a name="crop-videos-with-media-encoder-standard"></a>Videók körülvágása a Media Encoder Standarddel  

A Media Encoder Standard (MES) segítségével levághatja a bemeneti videót. A körülvágás az a folyamat, amelynek során négyszögletes ablakot választ ki a videokereten belül, és csak az ablakon belüli képpontokat kódolja. Az alábbi ábra szemlélteti a folyamatot.

![Videó körülvágása](./media/media-services-crop-video/media-services-crop-video01.png)

Tegyük fel, hogy 1920x1080 képpont (16:9 képarány) felbontású, de bal és jobb oldalán fekete sávok (oszlopdobozok) rendelkezik, így csak egy 4:3 vagy 1440x1080 képpont tartalmaz aktív videót. A MES segítségével levághatja vagy szerkesztheti a fekete sávokat, és kódolhatja a 1440x1080 régiót.

A MES-ben történő körülvágás egy előfeldolgozási szakasz, így a kódolási készlet ben szereplő vágási paraméterek az eredeti bemeneti videóra vonatkoznak. A kódolás egy későbbi szakasz, és a szélesség/magasság beállítások az *előre feldolgozott* videóra vonatkoznak, nem pedig az eredeti videóra. A készlet tervezésekor a következőket kell tennie: (a) válassza ki a vágási paramétereket az eredeti bemeneti videó alapján, és (b) válassza ki a kódolási beállításokat a levágott videó alapján. Ha nem egyezteti a kódolási beállításokat a levágott videóval, a kimenet nem a várt lesz.

A [következő](media-services-custom-mes-presets-with-dotnet.md#encoding_with_dotnet) témakör bemutatja, hogyan hozhat létre kódolási feladatot a MES-sel, és hogyan adhat meg egyéni készletet a kódolási feladathoz. 

## <a name="creating-a-custom-preset"></a>Egyéni készlet létrehozása
Az ábrán látható példában:

1. Az eredeti bemenet: 1920x1080
2. 1440x1080-as kimenetre kell vágni, amely a bemeneti keret közepén van
3. Ez azt jelenti, hogy az X eltolás (1920 – 1440)/2 = 240, az Y eltolása pedig nulla
4. A Vágás téglalap szélessége és magassága 1440, illetve 1080
5. A kódolási szakaszban, a kérni, hogy készítsen három réteg, a felbontás1440x1080, 960x720 és 480x360, illetve

### <a name="json-preset"></a>JSON készlet
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


## <a name="restrictions-on-cropping"></a>A növénytermesztésre vonatkozó korlátozások
A vágási funkció célja, hogy manuális legyen. Meg kell tölteni a bemeneti videó egy megfelelő szerkesztő eszköz, amely lehetővé teszi, hogy válassza ki a képkockák érdekes, helyezze a kurzort, hogy meghatározza eltolások a vágás téglalap, hogy meghatározzák a kódolási készlet, amely be van hangolva, hogy az adott videó, stb Ez a funkció nem célja, hogy a dolgok, mint például: automatikus észlelése és eltávolítása fekete postafiók / pillarbox határok a bemeneti videó.

A következő megkötések vonatkoznak a vágási szolgáltatásra. Ha ezek nem teljesülnek, a kódolási feladat sikertelen lehet, vagy váratlan kimenetet hozhat létre.

1. A Crop téglalap koordinátáinak és méretének el kell illeszkedniük a bemeneti videóba
2. Mint már említettük, a szélesség & magasság a kódolási beállításokat meg kell felelnie a levágott videó
3. A körülvágás a fekvő módban rögzített videókra vonatkozik (azaz nem vonatkozik a függőlegesen vagy álló módban tartott okostelefonnal rögzített videókra)
4. A négyzetes képpontokkal rögzített progresszív videókkal működik a legjobban

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-step"></a>Következő lépés
Tekintse meg az Azure Media Services tanulási útvonalait, amelyek segítségével megismerheti az AMS által kínált nagyszerű funkciókat.  

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]
