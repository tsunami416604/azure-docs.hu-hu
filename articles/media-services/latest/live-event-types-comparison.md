---
title: Azure Media Services LiveEvent-típusok | Microsoft dokumentumok
description: Az Azure Media Services szolgáltatásban az élő esemény beállítható *átadó* vagy *élő kódolásra.* Ez a cikk egy részletes táblázatot mutat be, amely összehasonlítja az élő eseménytípusokat.
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
ms.openlocfilehash: a28d4d96f643c12eeb6aa542db2c6af06f4fd954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78244643"
---
# <a name="live-event-types-comparison"></a>Élő eseménytípusok összehasonlítása

Az Azure Media Servicesben az [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) beállítható egy *áthaladásra* (egy helyszíni élő kódoló több bitráta-adatfolyamot küld) vagy *élő kódolásra* (egy helyszíni élő kódoló egyetlen bitráta-adatfolyamot küld). 

Ez a cikk az élő eseménytípusok funkcióit hasonlítja össze.

## <a name="types-comparison"></a>Típusok összehasonlítása 

Az alábbi táblázat az Élő eseménytípusok funkcióit hasonlítja össze. A típusok a [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)használatával történő létrehozás során vannak beállítva:

* **LiveEventEncodingType.None** – A helyszíni élő kódoló több átviteli sebességű adatfolyamot küld. A bevitt adatfolyamok további feldolgozás nélkül haladnak át az élő eseményen. Más néven átmenő élő esemény.
* **LiveEventEncodingType.Standard** – A helyszíni élő kódoló egyetlen bitráta-adatfolyamot küld az élő eseménynek, és a Media Services több bitráta-adatfolyamot hoz létre. Ha a hozzájárulási hírcsatorna 720p vagy nagyobb felbontású, a **Default720p** készlet 6 felbontású/bitráta párból álló készletet kódol (a részletek a cikk későbbi részében következnek).
* **LiveEventEncodingType.Premium1080p** – A helyszíni élő kódoló egyetlen bitráta-adatfolyamot küld az Élő eseménynek, és a Media Services több bitráta-adatfolyamot hoz létre. A Default1080p készlet a felbontás/bitráta párok kimeneti készletét adja meg (a részletek a cikk későbbi részét követik). 

| Szolgáltatás | Átmenő élő esemény | Standard vagy Premium1080p élő esemény |
| --- | --- | --- |
| Az egyátviteli bemenet több bitsebességbe van kódolva a felhőben |Nem |Igen |
| Maximális videofelbontás a hozzájárulási hírcsatornához |4K (4096x2160 60 képkocka/mp)4K (4096x2160 at 60 frames/sec) |1080p (1920x1088 30 képkocka/mp sebességgel)|
| A hozzájárulási takarmány ajánlott maximális rétegei|Akár 12|Egy hang|
| Maximális rétegek a kimenetben| Megegyezik a bemenettel|Legfeljebb 6 (lásd az alábbi rendszerkészleteket)|
| A hozzájárulási hírcsatorna maximális összesített sávszélessége|60 Mbps|N/A|
| Maximális bitráta egyetlen rétegre a hozzájárulásban |20 Mbps|20 Mbps|
| Több nyelvű hangsáv támogatása|Igen|Nem|
| Támogatott bemeneti videokodekek |H.264/AVC és H.265/HEVC|H.264/AVC|
| Támogatott kimeneti videokodekek|Megegyezik a bemenettel|H.264/AVC|
| Támogatott videobitmélység, -bevitel és -kimenet|Akár 10 bites HDR 10/HLG-vel|8 bites|
| Támogatott bemeneti hangkodekek|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Támogatott kimeneti hangkodekek|Megegyezik a bemenettel|AAC-LC|
| A kimeneti videó maximális felbontása|Megegyezik a bemenettel|Standard - 720p, Premium1080p - 1080p|
| A bemeneti videó maximális képkockasebessége|60 képkocka/másodperc|Standard vagy Premium1080p - 30 képkocka/másodperc|
| Bemeneti protokollok|RTMP, töredezett MP4 (Smooth Streaming)|RTMP, töredezett MP4 (Smooth Streaming)|
| Price|Lásd az [árképzési oldalt,](https://azure.microsoft.com/pricing/details/media-services/) és kattintson a "Live Video" fülre|Lásd az [árképzési oldalt,](https://azure.microsoft.com/pricing/details/media-services/) és kattintson a "Live Video" fülre|
| Maximális futási idő| 24 óra x 365 nap, élő lineáris | 24 óra x 365 nap, élő lineáris (előzetes verzió)|
| A beágyazott CEA 608/708 feliratok adatainak áthaladása|Igen|Igen|
| Az élő átírás bekapcsolásának képessége|Igen|Igen|
| Palabehelyezések támogatása|Nem|Nem|
| A hirdetésjelzés támogatása API-n keresztül| Nem|Nem|
| Hirdetésjelzés támogatása SCTE-35 sávon keresztüli üzeneteken keresztül|Igen|Igen|
| Képesség, hogy felépüljön a rövid standokon hozzájárulás takarmány|Igen|Részleges|
| Nem egységes bemeneti gok-k támogatása|Igen|Nem – a bemenetnek rögzített GOP-időtartammal kell rendelkeznie|
| A változó képkockasebesség bemenetének támogatása|Igen|Nem – a bemenetnek rögzített képkockasebességnek kell lennie. Kisebb variációk tolerálható, például a nagy mozgás jelenetek. De a hozzájárulás idomár a képkockasebesség (például 15 képkocka/másodperc) nem csökkenhet.|
| Élő esemény automatikus kikapcsolása a bemeneti hírcsatorna elvészekén|Nem|12 óra elteltével, ha nincs LiveOutput futás|

## <a name="system-presets"></a>Rendszerkészletek

Az élő kódoló kimenetében található felbontásokat és bitrátákat a [presetName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding)határozza meg. **Ha egy szabványos** élő kódolót (LiveEventEncodingType.Standard) használ, akkor a *Default720p* készlet az alábbiakban ismertetett 6 felbontású/bitráta párkészletet határoz meg. Ellenkező esetben, ha **egy Premium1080p** élő kódoló (LiveEventEncodingType.Premium1080p), majd a *Default1080p* készlet adja meg a kimeneti felbontás / bitráta párok.

> [!NOTE]
> Nem alkalmazhatja a Default1080p készletet élő eseményre, ha a Standard live kódoláshoz van beállítva - hibaüzenetet kap. Akkor is hibaüzenetet kap, ha a Default720p készletet prémium1080p élő kódolóra próbálja alkalmazni.

### <a name="output-video-streams-for-default720p"></a>Kimeneti videoadatfolyamok default720p esetén

Ha a hozzájárulási hírcsatorna 720p vagy nagyobb felbontású, a **Default720p** készlet a következő 6 rétegbe kódolja a hírcsatornát. Az alábbi táblázatban a bitráta kbit/s-ban, a MaxFPS pedig azt jelzi, hogy a maximálisan megengedett képkockasebesség (képkockában/másodpercben), a Profil a használt H.264 profilt jelöli.

| Bitráta | Szélesség | Height (Magasság) | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Magasság |
| 2200 |960 |540 |30 |Magasság |
| 1350 |704 |396 |30 |Magasság |
| 850 |512 |288 |30 |Magasság |
| 550 |384 |216 |30 |Magasság |
| 200 |340 |192 |30 |Magasság |

> [!NOTE]
> Ha testre kell szabnia az élő kódolási készletet, nyisson meg egy támogatási jegyet az Azure Portalon keresztül. Meg kell adnia a felbontás és a bitsebesség kívánt táblázatát. Győződjön meg arról, hogy csak egy réteg 720p sebességű, és hogy legfeljebb 6 réteg van. Azt is adja meg, hogy egy standard élő kódoló készletét kéri.
> A bitráták és felbontások egyedi értékei idővel módosíthatók

### <a name="output-video-streams-for-default1080p"></a>Kimeneti videoadatfolyamok a Default1080p-hez

Ha a hozzájárulási hírcsatorna felbontása 1080p, a **Default1080p** készlet a következő 6 rétegbe kódolja a hírcsatornát.

| Bitráta | Szélesség | Height (Magasság) | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Magasság |
| 3000 |1280 |720 |30 |Magasság |
| 1600 |960 |540 |30 |Magasság |
| 800 |640 |360 |30 |Magasság |
| 400 |480 |270 |30 |Magasság |
| 200 |320 |180 |30 |Magasság |

> [!NOTE]
> Ha testre kell szabnia az élő kódolási készletet, nyisson meg egy támogatási jegyet az Azure Portalon keresztül. Meg kell adnia a felbontás és a bitsebesség kívánt táblázatát. Ellenőrizze, hogy csak egy réteg van 1080p-n, és legtöbb 6 réteg. Azt is adja meg, hogy egy premium1080p élő kódoló készletét kéri.
> A bitráták és felbontások fajlagos értékei idővel módosíthatók.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Kimeneti hangadatfolyam default720p és Default1080p

A *Default720p* és a *Default1080p* készletek esetén a hang 128 kbps-es sztereó AAC-LC kódolású. A mintavételi arány a hozzájárulási hírcsatornában lévő hangsáv é.

## <a name="implicit-properties-of-the-live-encoder"></a>Az élő kódoló implicit tulajdonságai

Az előző szakasz az élő kódoló azon tulajdonságait ismerteti, amelyek explicit módon vezérelhetők az előre beállított módon – például a rétegek, a felbontások és a bitráták száma. Ez a szakasz tisztázza az implicit tulajdonságokat.

### <a name="group-of-pictures-gop-duration"></a>Képcsoport időtartama (GOP)

Az élő kódoló követi a [hozzájárulási](https://en.wikipedia.org/wiki/Group_of_pictures) csatorna GOP struktúráját , ami azt jelenti, hogy a kimeneti rétegek időtartama megegyezik a GOP időtartamával. Ezért ajánlott konfigurálni a helyszíni kódolót, hogy készítsen egy hozzájárulási hírcsatornát, amely rögzített GOP-időtartammal (általában 2 másodperc). Ez biztosítja, hogy a kimenő HLS- és MPEG DASH-adatfolyamok a szolgáltatásból is rögzített GOP-időtartamokkal rendelkezzenek. A gop időtartamának kis eltéréseit a legtöbb eszköz valószínűleg tolerálja.

### <a name="frame-rate"></a>Képkockasebesség

Az élő kódoló a hozzájárulási hírcsatornában lévő egyes videokeretek időtartamát is követi - ami azt jelenti, hogy a kimeneti rétegek ugyanolyan időtartamú képkockákkal rendelkeznek. Ezért ajánlott úgy konfigurálni a helyszíni kódolót, hogy rögzített képkockasebességgel (legkorábban 30 képkocka/másodperc) rögzített képkockasebességgel (legrögzített képkocka/másodperc) tartalmazó forráscsatornát állítson elő. Ez biztosítja, hogy a szolgáltatás kimenő HLS- és MPEG DASH-adatfolyamai rögzített képkockasebesség-időtartammal is rendelkezzenek. A képkockasebesség kis eltéréseit a legtöbb eszköz eltolhatja, de nincs garancia arra, hogy az élő kódoló olyan kimenetet fog létrehozni, amely helyesen fog lejátszani. A helyszíni élő kódoló nem lehet csepegtető keretek (pl. alacsony töltöttségi szint mellett) vagy a képkockasebesség bármilyen módon való átváltozása.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>A hozzájárulási előtolás és a kimeneti rétegek felbontása

Az élő kódoló úgy van beállítva, hogy elkerülje a hozzájárulási hírcsatorna upconvert. Ennek eredményeképpen a kimeneti rétegek maximális felbontása nem haladja meg a hozzájárulási hírcsatornáét.

Ha például 720p sebességgel küld egy hozzájárulási hírcsatornát egy Default1080p élő kódolásra konfigurált élő eseményre, a kimenet nek csak 5 rétege lesz, kezdve a 720p 3Mbps értékkel, és 200 kbps-re csökken 1080p-re. Vagy ha 360p-es hozzájárulási hírcsatornát küld egy Normál élő kódolásra konfigurált élő eseménybe, a kimenet 3 réteget fog tartalmazni (288p, 216p és 192p felbontásban). A degenerált esetben, ha mondjuk 160x90 képpontos hozzájárulási hírcsatornát küld egy szabványos élő kódolónak, a kimenet egy 160x90-es felbontású réteget fog tartalmazni, a hozzájárulási hírcsatorna mértékletességével megegyező átviteli sebességgel.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>A hozzájárulási előtolás és a kimeneti rétegek bitrátája

Az élő kódoló úgy van beállítva, hogy tartsa tiszteletben a bitráta-beállításokat a készletben, függetlenül a hozzájárulási hírcsatorna bitrátájától. Ennek eredményeképpen a kimeneti rétegek bitrátája meghaladhatja a hozzájárulási előtolásét. Ha például 720p felbontásban küld be egy hozzájárulási hírcsatornát 1 Mbps felbontással, a kimeneti rétegek ugyanazok maradnak, mint a fenti [táblázatban.](live-event-types-comparison.md#output-video-streams-for-default720p)

## <a name="next-steps"></a>További lépések

[Élő közvetítés – áttekintés](live-streaming-overview.md)
