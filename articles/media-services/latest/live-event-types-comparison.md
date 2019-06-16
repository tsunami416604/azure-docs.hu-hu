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
ms.openlocfilehash: bd4899374c06246ddd4d5fa81d0f6e3a6a1e7017
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075005"
---
# <a name="live-event-types-comparison"></a>Élő esemény típusok összehasonlítása

Az Azure Media Services egy [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) két típus egyike lehet: valós idejű kódolás és a csatlakoztatott. 

## <a name="types-comparison"></a>Típusok összehasonlítása 

Az alábbi táblázat az élő esemény típusok funkcióit hasonlítja össze.

| Funkció | Az átmenő élő esemény | Standard szintű vagy Premium1080p élő esemény |
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
| Kimeneti videó videó maximális felbontás|Ugyanaz, mint a bemenet|Standard – 720p Premium1080p - 1080p|
| A bemeneti videó maximális sebessége|60 képkocka/másodperc|Standard szintű vagy Premium1080p – 30 képkocka/másodperc|
| A bemeneti protokollok|RTMP, töredékes MP4 (Smooth Streaming)|RTMP, töredékes MP4 (Smooth Streaming)|
| Ár|Tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/media-services/) , majd kattintson a "Élő videó" lap|Tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/media-services/) , majd kattintson a "Élő videó" lap|
| Maximálisan engedélyezett futási idő| 24 x 365 nap órája és élő lineáris | Akár 24 óra|
| A feliratok adatokat képes keresztül beágyazott CEA 608/708 átadni|Igen|Igen|
| Befutók beillesztésének támogatását|Nem|Nem|
| API-n keresztül jelzés ad támogatása| Nem|Nem|
| Jelzés keresztül SCTE – 35 sávon üzeneteket ad támogatása|Igen|Igen|
| Lehetővé teszi a csatorna közreműködői rövid leállások helyreállítása|Igen|Részleges|
| Nem egységes bemeneti GOPs támogatása|Igen|Nem – bemeneti kell kijavítása Képcsoporttal időtartama|
| Változó keret arány bevitel támogatása|Igen|Nem – bemeneti képkockasebessége kell rögzíteni. Kisebb módosításokat kívánalmakhoz, például magas mozgásban lévő adatoknak egyaránt jelenetek során. De a hozzájárulás hírcsatorna nem dobható el a képkockasebességet (például 15-re képkocka/másodperc).|
| Automatikus – gyors élő esemény, amikor a bevitel hírcsatorna elvész.|Nem|Ha nem fut LiveOutput 12 óra elteltével|

## <a name="system-presets"></a>Rendszer-készletek

A felbontásra és bitsebességre való átkódolása az élő kódoló kimenetében található határozza meg a [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Ha használja egy **Standard** live encoder (LiveEventEncodingType.Standard), majd a *Default720p* előbeállítás 6 feloldási/átviteli sebesség párok az alábbiakban egy halmazát határozza meg. Egyéb esetben, ha a használatával egy **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), akkor a *Default1080p* előre beállított specifiesthe kimeneti feloldási/átviteli sebesség párok készletét. 

### <a name="output-video-streams-for-default720p"></a>Video-adatfolyamokat kimeneti Default720p

**Default720p** lesz a bemeneti videó kódolandó a következő 6 rétegek.

| Átviteli sebesség | Szélesség | Magasság | MaxFPS | Profil | Kimeneti Stream neve |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Magas |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Magas |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Magas |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Magas |Video_512x288_850kbps |
| 550 |384 |216 |30 |Magas |Video_384x216_550kbps |
| 200 |340 |192 |30 |Magas |Video_340x192_200kbps |

> [!NOTE]
> Ha testre szabhatja a élő kódolási előbeállítás van szüksége, nyisson egy támogatási jegyet az Azure Portalon keresztül. Meg kell adnia a felbontás és a bitsebesség kívánt táblázatát. Győződjön meg arról, hogy csak egy réteg 720p sebességű, és hogy legfeljebb 6 réteg van. Is megadhatja, hogy az élő kódolók olyan szabványos készlet kérő.

### <a name="output-video-streams-for-default1080p"></a>Video-adatfolyamokat kimeneti Default1080p

**Default1080p** lesz a bemeneti videó kódolandó a következő 6 rétegek.

| Átviteli sebesség | Szélesség | Magasság | MaxFPS | Profil | Kimeneti Stream neve |
| --- | --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Magas |Video_1920x1080_5500kbps |
| 3000 |1280 |720 |30 |Magas |Video_1280x720_3000kbps |
| 1600 |960 |540 |30 |Magas |Video_960x540_1600kbps |
| 800 |640 |360 |30 |Magas |Video_640x360_800kbps |
| 400 |480 |270 |30 |Magas |Video_480x270_400kbps |
| 200 |320 |180 |30 |Magas |Video_320x180_200kbps |

> [!NOTE]
> Ha testre szabhatja a élő kódolási előbeállítás van szüksége, nyisson egy támogatási jegyet az Azure Portalon keresztül. Meg kell adnia a felbontás és a bitsebesség kívánt táblázatát. Győződjön meg arról, hogy csak egy réteg 1080 p, és legfeljebb 6 rétegek. Is megadhatja, hogy az élő kódolók olyan Premium1080p egy készletet kér.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Kimeneti Audio Stream Default720p és Default1080p

Mindkét *Default720p* és *Default1080p* sztereó AAC-LC, 128 kb/s, mintavételi ráta 48 kHz a készletek, hang kódolása.

## <a name="next-steps"></a>További lépések

[Élő adatfolyam – áttekintés](live-streaming-overview.md)
