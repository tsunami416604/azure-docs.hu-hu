---
title: Feladatkészletek a Media Encoder Standardhoz (MES) | Microsoft dokumentumok
description: A témakör a Media Encoder Standard (MES) szolgáltatás által definiált mintakészletekről ad áttekintést.
author: Juliako
manager: femila
editor: johndeu
services: media-services
documentationcenter: ''
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 50c52369a5a957a4dd6279cac5079e2dea023106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463403"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>Mintakészletek a Media Encoder Standardhoz (MES)

**A Media Encoder Standard** egy előre definiált rendszerkódolási készletet határoz meg, amelyet kódolási feladatok létrehozásakor használhat. Javasoljuk, hogy használja az "Adaptive Streaming" készletet, ha a Media Services szolgáltatással való streameléshez videót szeretne kódolni. Ha megadja ezt a készletet, a Media Encoder Standard [automatikusan létrehoz egy bitráta létrát.](media-services-autogen-bitrate-ladder-with-mes.md) 

### <a name="creating-custom-presets-from-samples"></a>Egyéni készletek létrehozása mintákból
A Media Services teljes mértékben támogatja az összes érték beállításban való testreszabását, hogy megfeleljen az ön egyedi kódolási igényeinek és követelményeinek. Ha egy kódolási készletet kell testreszabnia, kezdje az alábbi rendszerkészletek egyikével, amelyek ebben a szakaszban az egyéni konfiguráció sablonjaként találhatók. A készletek egyes elemeinek és az egyes elemek érvényes értékeinek magyarázatát a [Media Encoder Standard séma](media-services-mes-schema.md) témakörében talál.  
  
> [!NOTE]
>  Ha 4k kódoláshoz használt készletet, akkor `S3` a lefoglalt egység típusát kell beszereznie. További információt a [Kódolás méretezése](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units)című témakörben talál.  

#### <a name="video-rotation-default-setting-in-presets"></a>A videó elforgatásának alapértelmezett beállítása a készletekben:
A Media Encoder Standard szolgáltatással végzett munka során a videó elforgatása alapértelmezés szerint engedélyezve van. Ha a videót álló módban rögzítette egy mobileszközön, akkor ezek a készletek a kódolás előtt fekvő módba forgatják őket.
 
## <a name="available-presets"></a>Elérhető készletek: 

 [A H264 Multiple Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) 8 GOP-hangalapú MP4-fájlt eredményez, 6000 kbit/s-tól 400 kbps-ig terjedő sebességgel és AAC 5.1-es hanggal.  
  
 [A H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) 8 GOP-hangalapú MP4-fájlt eredményez, 6000 kbps-től 400 kbps-ig, valamint sztereó AAC hangot.  
  
 [Az iOS-hez készült H264 Multiple Bitrate 16x9](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) 8 GOP-hangalapú MP4-fájlt eredményez, 8500 kbps-től 200 kbps-ig, valamint sztereó AAC hangot.  
  
 [A H264 Multiple Bitrate 16x9 SD Audio 5.1 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) 5 GOP-hangalapú MP4-fájlt eredményez, 1900 kbit/s-tól 400 kbps-ig, valamint AAC 5.1-es hangátvitelt.  
  
 [A H264 Multiple Bitrate 16x9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) 5 GOP-hangalapú MP4-fájlt eredményez, 1900 kbps-től 400 kbps-ig, valamint sztereó AAC hangot.  
  
 [A H264 Multiple Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) 12 GOP-hangalapú MP4-fájlt eredményez, 20000 kbps-től 1000 kbps-ig és AAC 5.1-es hangfájlokig.  
  
 [A H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) 12 GOP-hangalapú MP4-fájlt eredményez, 20000 kbps-től 1000 kbps-ig, valamint sztereó AAC hangot.  
  
 [Az iOS-hez készült Többbitráta 4x3](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) 8 GOP-hangalapú MP4-fájlt eredményez, 8500 kbps-től 200 kbps-ig, valamint sztereó AAC hangot.  
  
 [A H264 Multiple Bitrate 4x3 SD Audio 5.1 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) 5 GOP-hangalapú MP4-fájlt eredményez, 1600 kbit/s-tól 400 kbps-ig terjedő sebességgel és AAC 5.1-es hanggal.  
  
 [A H264 Multiple Bitrate 4x3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) 5 GOP-hangalapú MP4-fájlt eredményez, 1600 kbps-től 400 kbps-ig, valamint sztereó AAC hangot.  
  
 [A H264 Multiple Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) 6 GOP-hanggal rendelkező MP4-fájlt eredményez, 3400 kbps-től 400 kbps-ig és AAC 5.1-es hangfájlokig.  
  
 [A H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) 6 GOP-hangalapú MP4-fájlt eredményez, 3400 kbps-től 400 kbps-ig, valamint sztereó AAC hangot.  
  
 [A H264 Single Bitrate 1080p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) egyetlen MP4-fájlt készít 6750 kbps bitrátával és AAC 5.1-es hanggal.  
  
 [A H264 Single Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) egyetlen MP4-fájlt eredményez 6750 kbps bitrátával és sztereó AAC hanggal.  
  
 [A H264 Single Bitrate 4K Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) egyetlen MP4-fájlt készít 18000 kbps bitrátával és AAC 5.1-es hanggal.  
  
 [A H264 Single Bitrate 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) egyetlen MP4-fájlt eredményez 18000 kbps bitrátával és sztereó AAC hanggal.  
  
 [A H264 Single Bitrate 4x3 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) egyetlen MP4-fájlt készít 1800 kbps bitrátával és AAC 5.1-es hanggal.  
  
 [A H264 Single Bitrate 4x3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) egyetlen MP4-fájlt készít 1800 kbps bitrátával és sztereó AAC hanggal.  
  
 [A H264 Single Bitrate 16x9 SD Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) egyetlen MP4-fájlt készít 2200 kbps bitrátával és AAC 5.1-es hanggal.  
  
 [A H264 Single Bitrate 16x9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) egyetlen MP4-fájlt készít 2200 kbps bitrátával és sztereó AAC hanggal.  
  
 [A H264 Single Bitrate 720p Audio 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) egyetlen MP4-fájlt eredményez 4500 kbps bitrátával és AAC 5.1-es hanggal.  
  
 [A H264 Single Bitrate 720p for Android](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) preset egyetlen MP4-fájlt hoz létre 2000 kbps bitrátával és sztereó AAC-val.  
  
 [A H264 Single Bitrate 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) egyetlen MP4-fájlt eredményez 4500 kbps bitrátával és sztereó AAC hanggal.  
  
 [H264 Single Bitrate High Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) termel egyetlen MP4 fájl egy bitráta 500 kbps, és sztereó AAC audio..  
  
 [A H264 Single Bitrate Low Quality SD for Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) egyetlen MP4-fájlt állít elő 56 kbps bitrátával és sztereó AAC hanggal.  
  
 A Media Services kódolóival kapcsolatos további információkért lásd: [Igény szerinti kódolás az Azure Media Services szolgáltatással.](https://azure.microsoft.com/documentation/articles/media-services-encode-asset/)
