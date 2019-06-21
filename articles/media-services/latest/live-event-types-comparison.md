---
title: Az Azure Media Services videókhoz típusok |} A Microsoft Docs
description: Ez a cikk, amely összehasonlítja a videókhoz típusok részletes táblázatát jeleníti meg.
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: 93f01513841d1174fea796f1615ab05df0a41af4
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150384"
---
# <a name="live-event-types-comparison"></a>Élő esemény típusok összehasonlítása

Az Azure Media Services egy [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) két típus egyike lehet: valós idejű kódolás és a csatlakoztatott. 

## <a name="types-comparison"></a>Típusok összehasonlítása 

Az alábbi táblázat az élő esemény típusok funkcióit hasonlítja össze. A típusok létrehozása használata során vannak beállítva [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** – egy helyszíni élő kódoló egy több sávszélességű adatfolyamot küld. A feldolgozott adatfolyamok további feldolgozás nélkül áthalad az élő esemény. 
* **LiveEventEncodingType.Standard** – egy helyszíni élő kódoló küld az élő esemény és a Media Services egyféle sávszélességű adatfolyamot hoz létre több átviteli sebességű streamet. Ha a közreműködői hírcsatorna 720p vagy nagyobb felbontású a **Default720p** előbeállítás kódolja (részletek kövesse a cikk későbbi részében) 6 feloldási/sávszélességű párok készletét.
* **LiveEventEncodingType.Premium1080p** – egy helyszíni élő kódoló küld az élő esemény és a Media Services egyféle sávszélességű adatfolyamot hoz létre több átviteli sebességű streamet. A Default1080p előbeállítás megadja kimeneti feloldási/sávszélességű párok (részletek kövesse a cikk későbbi részében). 

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

A felbontásra és bitsebességre való átkódolása az élő kódoló kimenetében található határozza meg a [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Ha használja egy **Standard** live encoder (LiveEventEncodingType.Standard), majd a *Default720p* előbeállítás 6 feloldási/sávszélességű párok az alábbiakban egy halmazát határozza meg. Egyéb esetben, ha a használatával egy **Premium1080p** live encoder (LiveEventEncodingType.Premium1080p), akkor a *Default1080p* előre megadja kimeneti feloldási/sávszélességű párok.

> [!NOTE]
> Az élő esemény készletet, ha használható a standard szintű live encoding telepítő volt – hiba lép fel Default1080p nem lehet alkalmazni. Ha megpróbálja a alkalmazni a Default720p Premium1080p az élő kódolók olyan készletet is megjelenik egy hibaüzenet.

### <a name="output-video-streams-for-default720p"></a>Video-adatfolyamokat kimeneti Default720p

Ha a közreműködői hírcsatorna 720p vagy nagyobb felbontású a **Default720p** előbeállítás kódolja a hírcsatorna be a következő 6 rétegek. Az alábbi táblázat a sávszélességű kbit/s, MaxFPS jelenti. a maximális megengedett képkockasebessége (a képkocka/másodperc), profil jelöli a használni kívánt H.264 profilt.

| Átviteli sebesség | Szélesség | Magasság | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Magas |
| 2200 |960 |540 |30 |Magas |
| 1350 |704 |396 |30 |Magas |
| 850 |512 |288 |30 |Magas |
| 550 |384 |216 |30 |Magas |
| 200 |340 |192 |30 |Magas |

> [!NOTE]
> Ha testre szabhatja a élő kódolási előbeállítás van szüksége, nyisson egy támogatási jegyet az Azure Portalon keresztül. Meg kell adnia a felbontás és a bitsebesség kívánt táblázatát. Győződjön meg arról, hogy csak egy réteg 720p sebességű, és hogy legfeljebb 6 réteg van. Is megadhatja, hogy az élő kódolók olyan szabványos készlet kérő.
> Előfordulhat, hogy módosítja a konkrét értékekre felbontásra és bitsebességre való átkódolása időbeli alakulása

### <a name="output-video-streams-for-default1080p"></a>Video-adatfolyamokat kimeneti Default1080p

A közreműködői hírcsatorna 1080p felbontással, ha a **Default1080p** előbeállítás kódolja a hírcsatorna be a következő 6 rétegek.

| Átviteli sebesség | Szélesség | Magasság | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Magas |
| 3000 |1280 |720 |30 |Magas |
| 1600 |960 |540 |30 |Magas |
| 800 |640 |360 |30 |Magas |
| 400 |480 |270 |30 |Magas |
| 200 |320 |180 |30 |Magas |

> [!NOTE]
> Ha testre szabhatja a élő kódolási előbeállítás van szüksége, nyisson egy támogatási jegyet az Azure Portalon keresztül. Meg kell adnia a felbontás és a bitsebesség kívánt táblázatát. Győződjön meg arról, hogy csak egy réteg 1080 p, és legfeljebb 6 rétegek. Is megadhatja, hogy az élő kódolók olyan Premium1080p egy készletet kér.
> A konkrét értékekre felbontásra és bitsebességre való átkódolása idővel előfordulhat, hogy kell beállítani.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Kimeneti Audio Stream Default720p és Default1080p

Mindkét *Default720p* és *Default1080p* sztereó AAC-LC, 128 kb/s, készletek, hang kódolása. A mintavételi ráta követi, a hangsávot hírcsatorna hozzájárulását.

## <a name="implicit-properties-of-the-live-encoder"></a>Az élő kódoló implicit tulajdonságai

Az előző szakaszban szabályozhatja explicit módon, a készlet keresztül – például a rétegek, felbontásra és bitsebességre való átkódolása élőadás-kódoló tulajdonságait ismerteti. Ez a szakasz az implicit tulajdonságok értelmezi.

### <a name="group-of-pictures-gop-duration"></a>Képek (Képcsoporttal) időtartam csoportja

Az élő kódoló követi a [Képcsoporttal](https://en.wikipedia.org/wiki/Group_of_pictures) hírfolyam – a közreműködői, ami azt jelenti, hogy a kimeneti rétegek fog rendelkezni az adott Képcsoporttal időtartam szerkezete. Ezért ajánlott, hogy konfigurálja a helyszíni kódolót előállításához hírcsatorna hozzájárulást, amely javította Képcsoporttal időtartama (általában 2 másodperc). Ez biztosítja, hogy a kimenő HLS és MPEG-DASH adatfolyamok a szolgáltatásból is javította Képcsoporttal időtartamának összegénél. A legtöbb eszköz által megengedett Képcsoporttal időtartamok kis változásait azokat.

### <a name="frame-rate"></a>Képkockasebesség

Az élő kódoló is követi a hírcsatorna - hozzájárulás, ami azt jelenti, hogy a kimeneti rétegek fog rendelkezni az azonos percentilisénél hosszabb időtartamú keretek az egyes videókban időtartamát. Ezért javasoljuk, hogy konfigurálja a helyszíni kódolót hírcsatorna hozzájárulás létrehozásához, amely javította a képkockasebességet (legfeljebb 30 képkocka/másodperc). Ez biztosítja, hogy a kimenő HLS és MPEG-DASH adatfolyamok a szolgáltatásból is javította keret díjak időtartamának összegénél. Kis változások képkockasebességet a legtöbb eszköz is megengedhető, azonban nem garantált, hogy az élő kódoló eredményez, amely a lejátszás kimenet. A helyszíni élő kódoló kell nem lehet eldobni keretek (például) alacsony töltöttségi szint feltételek) vagy a keret bármilyen módon változtatható.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Hozzájárulás hírcsatorna- és kimeneti rétegek

Az élő kódoló upconverting elkerülése érdekében van konfigurálva. a hírcsatorna hozzájárulását. Ennek eredményeképpen a kimeneti réteg maximális felbontását nem haladhatja meg, amely a hozzájárulás hírcsatorna.

Például ha elküldi a 720 p hírcsatorna hozzájárulás Default1080p konfigurált élő események live encoding, a kimenet csak 5 rétegek, kezdve a 3 MB/s, 720p 1080 p 200 KB/s, le is van. Vagy ha 360 p hírcsatorna hozzájárulás küld be egy Standard konfigurált élő események valós idejű kódolás, a kimenetet fog tartalmazni (288p, 216p és 192p felbontással) 3 rétegek. A elfajult esetben, ha egy standard szintű live encoder közreműködői hírcsatornában, mondjuk 160 x 90 képpont küld a kimenet tartalmazza egy réteget, az azonos átviteli sebesség, a hírcsatorna-hozzájárulás 160 x 90 felbontású.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Hozzájárulás sávszélességű hírcsatorna- és kimeneti rétegek

Az élő kódoló figyelembe veszi a készlet, attól függetlenül, az átviteli sebesség a hírcsatorna-hozzájárulás sávszélességű-beállításokat van konfigurálva. Ennek eredményeképpen az átviteli sebesség, a kimeneti rétegek haladhatja meg a hozzájárulás hírcsatorna. Például, ha 1 Mbps sebességnél 720 p felbontással hírcsatorna hozzájárulás küldi, a kimeneti rétegek marad ugyanazok, mint a a [tábla](live-event-types-comparison.md#output-video-streams-for-default720p) felett.

## <a name="next-steps"></a>További lépések

[Élő adatfolyam – áttekintés](live-streaming-overview.md)
