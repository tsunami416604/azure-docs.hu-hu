---
title: Az Azure Media Services videókhoz típusok |} A Microsoft Docs
description: Ez a cikk bemutatja a videókhoz típusok összehasonlítása részletes tábla.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/01/2019
ms.author: juliako
ms.openlocfilehash: 9952a7bbac1eb79de0d3425f839e3bd30196844e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322284"
---
# <a name="live-event-types-comparison"></a>Élő esemény típusok összehasonlítása

Az Azure Media Services egy [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) két típus egyike lehet: valós idejű kódolás és a csatlakoztatott. 

## <a name="types-comparison"></a>Típusok összehasonlítása 

Az alábbi táblázat az élő esemény kétféle funkcióit hasonlítja össze.

| Szolgáltatás | Az átmenő élő esemény | Szabványos élő esemény |
| --- | --- | --- |
| Egyféle sávszélességű bemeneti bitsebességekre a felhőben van kódolva. |Nem |Igen |
| Az hozzájárulásra hírcsatorna maximális képfelbontás |4 KB-os (4096 × 2160 60 keretek/s) |1080p (1920 x 1088 30 keretek/s)|
| A csatorna közreműködői ajánlott maximális rétegek|Legfeljebb 12|Egy hang|
| A kimenet maximális rétegek| Ugyanaz, mint a bemenet|Akár 6 (lásd az alábbi rendszer készletek)|
| Hozzájárulás maximális összesített sávszélesség-hírcsatorna|60 MB/s|–|
| Maximális átviteli sebesség a hozzájárulás az egyetlen réteg |20 Mbps|20 Mbps|
| Több nyelv hangsáv támogatása|Igen|Nem|
| Támogatott bemeneti videókodekek |H.264/AVC és H.265/HEVC|H.264/AVC|
| Támogatott kimeneti videókodekek|Ugyanaz, mint a bemenet|H.264/AVC|
| Támogatott videó bitová hloubka obrázku, bemeneti és kimeneti|Legfeljebb 10 bites többek között HDR 10/HLG|8 bites|
| Támogatott bemeneti hangkodekek|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Támogatott kimeneti hangkodekek|Ugyanaz, mint a bemenet|AAC-LC|
| Kimeneti videó videó maximális felbontás|Ugyanaz, mint a bemenet|720p (30 képkocka/másodperc)|
| A bemeneti protokollok|RTMP, töredékes MP4 (Smooth Streaming)|RTMP, töredékes MP4 (Smooth Streaming)|
| Ár|Tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/media-services/) , majd kattintson a "Élő videó" lap|Tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/media-services/) , majd kattintson a "Élő videó" lap|
| Maximálisan engedélyezett futási idő| 24 x 365 nap órája és élő lineáris | Akár 24 óra|
| A feliratok adatokat képes keresztül beágyazott CEA 608/708 átadni|Igen|Igen|
| Befutók beillesztésének támogatását|Nem|Nem|
| API-n keresztül jelzés ad támogatása| Nem|Nem|
| Jelzés keresztül SCTE – 35 sávon üzeneteket ad támogatása|Igen|Igen|
| Lehetővé teszi a csatorna közreműködői rövid leállások helyreállítása|Igen|Nem (élő esemény elkezdi slating másodperc 6 + bemeneti adatok nélkül)|
| Nem egységes bemeneti GOPs támogatása|Igen|Nem – bemeneti kell kijavítása Képcsoporttal időtartama|
| Változó keret arány bevitel támogatása|Igen|Nem – bemeneti képkockasebessége kell rögzíteni. Kisebb módosításokat kívánalmakhoz, például magas mozgásban lévő adatoknak egyaránt jelenetek során. De a hozzájárulás hírcsatorna nem dobható el a képkockasebességet (például, hogy a 15 keretek/másodperc).|
| Automatikus – gyors élő esemény, amikor a bevitel hírcsatorna elvész.|Nem|Ha nem fut LiveOutput 12 óra elteltével|

## <a name="system-presets"></a>Rendszer-készletek

Élő kódolás használatakor (amikor az élő esemény beállítása **Standard**), a kódolási előbeállítás határozza meg, hogyan van többféle bitsebességbe vagy rétegbe kódolva a bejövő stream. Jelenleg az egyetlen megengedett érték a készlet *Default720p* (alapértelmezett).

**Default720p** lesz a videó kódolandó a következő 6 rétegek.

### <a name="output-video-stream"></a>Kimeneti videót Stream

| Átviteli sebesség | Szélesség | Magasság | MaxFPS | Profil | Kimeneti Stream neve |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Magas |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Magas |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Magas |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Magas |Video_512x288_850kbps |
| 550 |384 |216 |30 |Magas |Video_384x216_550kbps |
| 200 |340 |192 |30 |Magas |Video_340x192_200kbps |

> [!NOTE]
> Ha egyéni élő kódolási előbeállítást kell használnia, írjon az amshelp@microsoft.com címre. Meg kell adnia a felbontás és a bitsebesség kívánt táblázatát. Győződjön meg arról, hogy csak egy réteg 720p sebességű, és hogy legfeljebb 6 réteg van.

### <a name="output-audio-stream"></a>Kimeneti Audio Stream

Hang, sztereó AAC-LC, 128 kb/s, mintavételi ráta 48 kHz van kódolva.

## <a name="next-steps"></a>További lépések

[Élő adatfolyam – áttekintés](live-streaming-overview.md)
