---
title: Készletek feladathoz tartozó Media Encoder Standard (MES) |} Microsoft Docs
description: A témakör által biztosított és a szolgáltatás által definiált minta készletek a Media Encoder Standard (MES) áttekintése.
author: Juliako
manager: cfow
editor: johndeu
services: media-services
documentationcenter: ''
ms.assetid: f243ed1c-ac9c-4300-a5f7-f092cf9853b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2017
ms.author: juliako
ms.openlocfilehash: 4c518771d95dcb571da294f8a752e7b5740ef19f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788591"
---
# <a name="sample-presets-for-media-encoder-standard-mes"></a>A minta készletek Media Encoder standard (MES)

**Media Encoder Standard** kódolás készletek kódolási feladatok létrehozásakor is használhat előre definiált rendszer álló készletet határoz meg. Javasoljuk, hogy a "adaptív Streameléshez" készletet, ha azt szeretné, hogy a Media Services streaming videó kódolásához használja. Ha előre definiált, Media Encoder Standard ezzel a [automatikus létrehozása egy sávszélességű létra](media-services-autogen-bitrate-ladder-with-mes.md). 

### <a name="creating-custom-presets-from-samples"></a>A minták egyéni készletek létrehozása
A Media Services teljes mértékben támogatja az adott kódolási igények és követelményei beállításkészletekkel szereplő összes érték testreszabása. Ha szeretne egy kódolási beállításkészlet testreszabása, kell kezdődnie, egy a rendszer készletek biztosított ebben a szakaszban egy sablont az egyéni konfiguráció alatt. Az egyes milyen egyes elemei a készletek azt jelenti, és az érvényes értékek az egyes elemekhez, tekintse meg a [Media Encoder Standard séma](media-services-mes-schema.md) témakör.  
  
> [!NOTE]
>  Használatakor a készletet a 4 KB-os kódolja szerezheti be a `S3` szolgáltatás számára fenntartott egység típusát. További információkért lásd: [hogyan méretezési kódolás](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units).  

#### <a name="video-rotation-default-setting-in-presets"></a>A készletek videó elforgatása alapértelmezett beállítása:
A Media Encoder Standard használatakor videó elforgatása alapértelmezés szerint engedélyezve van. Ha a videó rögzítettük álló módban egy mobileszközön, majd a készleteket fog elforgatása őket a fekvő tájolás kódolás előtt.
 
## <a name="available-presets"></a>Rendelkezésre álló készletek: 

 [H264 Multiple Bitrate 1080p hang 5.1](media-services-mes-preset-H264-Multiple-Bitrate-1080p-Audio-5.1.md) létrejön egy 8 GOP igazított MP4-fájlok 400 kbit/s, és AAC 5.1 hang 6000 kbps kezdve.  
  
 [H264 Multiple Bitrate 1080p](media-services-mes-preset-H264-Multiple-Bitrate-1080p.md) létrejön egy 8 GOP igazított MP4-fájlok 400 kbit/s és sztereó AAC hang 6000 kbps kezdve.  
  
 [H264 Multiple Bitrate 16 x 9 IOS](media-services-mes-preset-H264-Multiple-Bitrate-16x9-for-iOS.md) létrejön egy 8 GOP igazított MP4-fájlok 200 KB/s és sztereó AAC hang 8500 kbps kezdve.  
  
 [H264 Multiple Bitrate 16 x 9 SD hang 5.1](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD-Audio-5.1.md) létrejön egy 5 GOP igazított MP4-fájlok 400 kbit/s, és AAC 5.1 hang 1900 kbps kezdve.  
  
 [H264 Multiple Bitrate 16 x 9 SD](media-services-mes-preset-H264-Multiple-Bitrate-16x9-SD.md) létrejön egy 5 GOP igazított MP4-fájlok 400 kbit/s és sztereó AAC hang 1900 kbps kezdve.  
  
 [5.1 H264 Multiple Bitrate 4 KB-os hang](media-services-mes-preset-H264-Multiple-Bitrate-4K-Audio-5.1.md) létrejön egy 12 GOP igazított MP4-fájlok 1000 KB/s és AAC 5.1 hang 20000 kbps kezdve.  
  
 [H264 Multiple Bitrate 4 KB-os](media-services-mes-preset-H264-Multiple-Bitrate-4K.md) létrejön egy 12 GOP igazított MP4-fájlok 1000 KB/s és sztereó AAC hang 20000 kbps kezdve.  
  
 [H264 Multiple Bitrate 4 x 3 IOS](media-services-mes-preset-H264-Multiple-Bitrate-4x3-for-iOS.md) létrejön egy 8 GOP igazított MP4-fájlok 200 KB/s és sztereó AAC hang 8500 kbps kezdve.  
  
 [H264 Multiple Bitrate 4 x 3 SD hang 5.1](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD-Audio-5.1.md) létrejön egy 5 GOP igazított MP4-fájlok 400 kbit/s, és AAC 5.1 hang 1600 kbps kezdve.  
  
 [H264 Multiple Bitrate 4 x 3 SD](media-services-mes-preset-H264-Multiple-Bitrate-4x3-SD.md) létrejön egy 5 GOP igazított MP4-fájlok 400 kbit/s és sztereó AAC hang 1600 kbps kezdve.  
  
 [H264 Multiple Bitrate 720p hang 5.1](media-services-mes-preset-H264-Multiple-Bitrate-720p-Audio-5.1.md) létrejön egy 6 GOP igazított MP4-fájlok 400 kbit/s, és AAC 5.1 hang 3400 kbps kezdve.  
  
 [H264 Multiple Bitrate 720p](media-services-mes-preset-H264-Multiple-Bitrate-720p.md) létrejön egy 6 GOP igazított MP4-fájlok 400 kbit/s és sztereó AAC hang 3400 kbps kezdve.  
  
 [H264 egyetlen Bitrate 1080p hang 5.1](media-services-mes-preset-H264-Single-Bitrate-1080p-Audio-5.1.md) egyetlen MP4-fájl egy sávszélességű 6750 kbit/s és AAC 5.1 hang eredményez.  
  
 [H264 egyetlen Bitrate 1080p](media-services-mes-preset-H264-Single-Bitrate-1080p.md) egyetlen MP4-fájl egy sávszélességű 6750 kbit/s és sztereó AAC hang eredményez.  
  
 [5.1 H264 egyféle sávszélességű 4 KB-os hang](media-services-mes-preset-H264-Single-Bitrate-4K-Audio-5.1.md) egyetlen MP4-fájl egy sávszélességű 18000 kbit/s és AAC 5.1 hang eredményez.  
  
 [H264 egyféle sávszélességű 4 KB-os](media-services-mes-preset-H264-Single-Bitrate-4K.md) egyetlen MP4-fájl egy sávszélességű 18000 kbit/s és sztereó AAC hang eredményez.  
  
 [H264 egyféle sávszélességű 4 x 3 SD hang 5.1](media-services-mes-preset-H264-Single-Bitrate-4x3-SD-Audio-5.1.md) egyetlen MP4-fájl egy 1800 kbit/s és AAC 5.1 hang sávszélességű eredményez.  
  
 [H264 egyféle sávszélességű 4 x 3 SD](media-services-mes-preset-H264-Single-Bitrate-4x3-SD.md) egyetlen MP4-fájl egy 1800 kbit/s és sztereó AAC hang sávszélességű eredményez.  
  
 [H264 egyféle sávszélességű 16 x 9 SD hang 5.1](media-services-mes-preset-H264-Single-Bitrate-16x9-SD-Audio-5.1.md) egyetlen MP4-fájl egy sávszélességű 2200 kbit/s és AAC 5.1 hang eredményez.  
  
 [H264 egyféle sávszélességű 16 x 9 SD](media-services-mes-preset-H264-Single-Bitrate-16x9-SD.md) egyetlen MP4-fájl egy sávszélességű 2200 kbit/s és sztereó AAC hang eredményez.  
  
 [H264 egyféle sávszélességű 720p hang 5.1](media-services-mes-preset-H264-Single-Bitrate-720p-Audio-5.1.md) egyetlen MP4-fájl egy sávszélességű 4500 kbit/s és AAC 5.1 hang eredményez.  
  
 [H264 egyféle sávszélességű Android 720p](media-services-mes-preset-H264-Single-Bitrate-720p-for-Android.md) készletet hoz létre egyetlen MP4-fájl egy sávszélességű 2000 kbit/s és sztereó AAC.  
  
 [H264 egyféle sávszélességű 720p](media-services-mes-preset-H264-Single-Bitrate-720p.md) egyetlen MP4-fájl egy sávszélességű 4500 kbit/s és sztereó AAC hang eredményez.  
  
 [H264 egyetlen sávszélességű kiváló minőségű SD Android](media-services-mes-preset-H264-Single-Bitrate-High-Quality-SD-for-Android.md) hozza létre egyetlen MP4-fájl egy sávszélességű 500 KB/s és sztereó AAC hang...  
  
 [H264 egyetlen sávszélességű alacsony minőségi SD Android](media-services-mes-preset-H264-Single-Bitrate-Low-Quality-SD-for-Android.md) egyetlen MP4-fájl egy sávszélességű 56 kb/s és sztereó AAC hang eredményez.  
  
 A Media Services kódolók kapcsolatos további információkért lásd: [kódolás igény az Azure Media Services](https://azure.microsoft.com/documentation/articles/media-services-encode-asset/).
