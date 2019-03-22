---
title: Tevékenység-készletek a Media Encoder Standard (MES) |} A Microsoft Docs
description: A témakör nyújt, és a szolgáltatás által definiált minta áttekintése előzetes beállítások, a Media Encoder Standard (MES) állnak.
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
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58260026"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>A Media Encoder Standard (MES) minta-készletek

**Media Encoder Standard** határozza meg azon előre definiált rendszer kódolás készletek is használhatja a kódolási feladat létrehozásakor. Javasoljuk, hogy a "adaptív Streamelés" előbeállítást, ha azt szeretné, a Media Services használatával folyamatos átviteli videó kódolásához használja. Media Encoder Standard előre beállított, ezzel megadása [skála automatikus létrehozását](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>Egyéni beállításkészletek létrehozása minták
A Media Services teljes körűen támogatja az adott kódolási igények és követelmények készletek található értékek némelyike testreszabása. Ha szeretne egy kódolási előbeállítás testreszabása, először érdemes az egyik a rendszer készletek biztosított ebben a szakaszban egy sablont a egyéni konfigurációra vonatkozó alábbi. Az egyes milyen egyes elemei, és ezen készletek azt jelenti, hogy az érvényes értékek az egyes elemekhez, tekintse meg a [Media Encoder Standard-séma](media-services-mes-schema.md) témakör.  
  
> [!NOTE]
>  Használatakor egy készletet a 4k kódol, meg kell kapnia a `S3` szolgáltatás számára fenntartott egység típussal. További információkért lásd: [How to Scale kódolás](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  

#### <a name="video-rotation-default-setting-in-presets"></a>Videó Elforgatás alapértelmezett beállítás a készletek:
A Media Encoder Standard használatakor videó Elforgatás alapértelmezés szerint engedélyezve van. Ha a videó rögzítettük álló tájolású módban egy mobileszközön, majd ezen készletek fog elforgatása őket fekvő tájolásra a kódolás előtt.
 
## <a name="available-presets"></a>Rendelkezésre álló készletek: 

 [H264 Multiple Bitrate 1080p hang 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) 8 Képcsoporttal igazított MP4-fájlokat, és a 6000 KB/s 400 kb/s és AAC 5.1 hang eredményez.  
  
 [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) 8 Képcsoporttal igazított MP4-fájlokat, és a 6000 KB/s 400 kb/s és sztereó AAC hang eredményez.  
  
 [H264 Multiple Bitrate 16 x 9 iOS-es](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) 8 Képcsoporttal igazított MP4-fájlokat, és a 8500 KB/s 200 KB/s és sztereó AAC hang eredményez.  
  
 [H264 Multiple Bitrate 16 x 9 SD hang 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) 5 Képcsoporttal igazított MP4-fájlokat, és a 1900 kbps 400 kb/s és AAC 5.1 hang eredményez.  
  
 [H264 Multiple Bitrate 16 x 9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) 5 Képcsoporttal igazított MP4-fájlokat, és a 1900 kbps 400 kb/s és sztereó AAC hang eredményez.  
  
 [5.1 H264 Multiple Bitrate 4K hang](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) 12 Képcsoporttal igazított MP4-fájlokat, és a 20000 KB/s 1000 KB/s és AAC 5.1 hang eredményez.  
  
 [H264 Multiple Bitrate 4K](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) 12 Képcsoporttal igazított MP4-fájlokat, és a 20000 KB/s 1000 KB/s és sztereó AAC hang eredményez.  
  
 [H264 Multiple Bitrate 4 x 3 iOS-es](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) 8 Képcsoporttal igazított MP4-fájlokat, és a 8500 KB/s 200 KB/s és sztereó AAC hang eredményez.  
  
 [H264 Multiple Bitrate 4 x 3 SD hang 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) 5 Képcsoporttal igazított MP4-fájlokat, és a 1600 KB/s 400 kb/s és AAC 5.1 hang eredményez.  
  
 [H264 Multiple Bitrate 4 x 3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) 5 Képcsoporttal igazított MP4-fájlokat, és a 1600 KB/s 400 kb/s és sztereó AAC hang eredményez.  
  
 [H264 Multiple Bitrate 720p hang 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) 6 Képcsoporttal igazított MP4-fájlokat, és a 3400 kbps 400 kb/s és AAC 5.1 hang eredményez.  
  
 [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) 6 Képcsoporttal igazított MP4-fájlokat, és a 3400 kbps 400 kb/s és sztereó AAC hang eredményez.  
  
 [H264 egyetlen Bitrate 1080p hang 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) egyetlen MP4-fájl egy sávszélességű 6750 KB/s és AAC 5.1 hang eredményez.  
  
 [H264 egyetlen Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) egyetlen MP4-fájl egy sávszélességű 6750 KB/s és sztereó AAC hang eredményez.  
  
 [5.1 H264 egyféle sávszélességű 4K hang](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) egyetlen MP4-fájl egy sávszélességű 18000 KB/s és AAC 5.1 hang eredményez.  
  
 [H264 egyféle sávszélességű 4K](media-services-mes-preset-H264-Single-Bitrate-4K.md) egyetlen MP4-fájl egy sávszélességű 18000 KB/s és sztereó AAC hang eredményez.  
  
 [H264 egyféle sávszélességű 4 x 3 SD hang 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) egyetlen MP4-fájl egy 1800 KB/s és AAC 5.1 hang sávszélességű eredményez.  
  
 [H264 egyféle sávszélességű 4 x 3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) egyetlen MP4-fájl egy 1800 KB/s és sztereó AAC hang sávszélességű eredményez.  
  
 [H264 egyféle sávszélességű 16 x 9 SD hang 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) egyetlen MP4-fájl egy 2200 KB/s és AAC 5.1 hang sávszélességű eredményez.  
  
 [H264 egyféle sávszélességű 16 x 9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) egyetlen MP4-fájl egy 2200 KB/s és sztereó AAC hang sávszélességű eredményez.  
  
 [H264 egyféle sávszélességű 720p hang 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) egyetlen MP4-fájl egy sávszélességű 4500 KB/s és AAC 5.1 hang eredményez.  
  
 [H264 egyféle sávszélességű Android 720p](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) előbeállítás egyetlen MP4-fájl egy sztereó AAC és 2000 KB/s sávszélességű hoz létre.  
  
 [H264 egyféle sávszélességű 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) egyetlen MP4-fájl egy sávszélességű 4500 KB/s és sztereó AAC hang eredményez.  
  
 [H264 egyetlen sávszélességű, magas minőségű SD Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) hoz létre egyetlen MP4-fájl egy 500 kbps és sztereó AAC hang sávszélességű...  
  
 [H264 egyetlen Mp4 alacsony minőségi SD Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) egyetlen MP4-fájl egy sávszélességű 56 kb/s és sztereó AAC hang eredményez.  
  
 A Media Services kódolók kapcsolatos további információkért lásd: [kódolás igény szerinti az Azure Media Services](https://azure.microsoft.com/documentation/articles/media-services-encode-asset/).
