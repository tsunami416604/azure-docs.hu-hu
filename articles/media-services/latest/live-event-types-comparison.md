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
ms.date: 11/27/2018
ms.author: juliako
ms.openlocfilehash: 3ed38ce75e5ee7c9f05533d64b28171482bfec51
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725932"
---
# <a name="liveevent-types-comparison"></a>Videókhoz típusok összehasonlítása

Az Azure Media Services egy [videókhoz](https://docs.microsoft.com/rest/api/media/liveevents) két típus egyike lehet: valós idejű kódolás és a csatlakoztatott. 

## <a name="types-comparison"></a>Típusok összehasonlítása 

Az alábbi táblázat a videókhoz kétféle funkcióit hasonlítja össze.

| Szolgáltatás | Az átmenő videókhoz | Standard videókhoz |
| --- | --- | --- |
| Egyféle sávszélességű bemeneti bitsebességekre a felhőben van kódolva. |Nem |Igen |
| Az hozzájárulásra hírcsatorna maximális képfelbontás |4 KB-os (4096 × 2160 60 keretek/s) |1080p (1920 x 1088 30 keretek/s)|
| A csatorna közreműködői ajánlott maximális rétegek|Legfeljebb 12|Egy hang|
| A kimenet maximális rétegek| Ugyanaz, mint a bemenet|Legfeljebb 7|
| Hozzájárulás maximális összesített sávszélesség-hírcsatorna|60 MB/s|–|
| Maximális átviteli sebesség a hozzájárulás az egyetlen réteg |20 MB/s|20 MB/s|
| Több nyelv hangsáv támogatása|Igen|Nem|
| Támogatott bemeneti videókodekek |H.264/AVC és H.265/HEVC|H.264/AVC|
| Támogatott kimeneti videókodekek|Ugyanaz, mint a bemenet|H.264/AVC|
| Támogatott videó bitová hloubka obrázku, bemeneti és kimeneti|Legfeljebb 10 bites többek között HDR 10/HLG|8 bites|
| Támogatott bemeneti hangkodekek|Az AAC-LC, HE-AAC v1, v2 az AAC-HE|Az AAC-LC, HE-AAC v1, v2 az AAC-HE|
| Támogatott kimeneti hangkodekek|Ugyanaz, mint a bemenet|AZ AAC-LC|
| Kimeneti videó videó maximális felbontás|Ugyanaz, mint a bemenet|720p (30 képkocka/másodperc)|
| A bemeneti protokollok|RTMP, töredékes MP4 (Smooth Streaming)|RTMP, töredékes MP4 (Smooth Streaming)|
| Ár|Tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/media-services/) , majd kattintson a "Élő videó" lap|Tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/media-services/) , majd kattintson a "Élő videó" lap|
| Maximálisan engedélyezett futási idő|24 x 365 élő lineáris|A hét minden napján, 24 órában elérhető|
| A feliratok adatokat képes keresztül beágyazott CEA 608/708 átadni|Igen|Igen|
| Befutók beillesztésének támogatását|Nem|Nem|
| API-n keresztül jelzés ad támogatása| Nem|Nem|
| Jelzés keresztül SCTE – 35 sávon üzeneteket ad támogatása|Igen|Igen|
| Lehetővé teszi a csatorna közreműködői rövid leállások helyreállítása|Igen|Nem (videókhoz elkezdi slating másodperc 6 + bemeneti adatok nélkül)|
| Nem egységes bemeneti GOPs támogatása|Igen|Nem – bemeneti kell kijavítása Képcsoporttal időtartama|
| Változó keret arány bevitel támogatása|Igen|Nem – bemeneti képkockasebessége kell rögzíteni. Kisebb módosításokat kívánalmakhoz, például magas mozgásban lévő adatoknak egyaránt jelenetek során. De a hozzájárulás hírcsatorna nem dobható el a képkockasebességet (például, hogy a 15 keretek/másodperc).|
| Automatikus – gyors, amikor a bevitel videókhoz hírcsatorna elvész.|Nem|Ha nem fut LiveOutput 12 óra elteltével|

## <a name="next-steps"></a>További lépések

[Élő adatfolyam – áttekintés](live-streaming-overview.md)
