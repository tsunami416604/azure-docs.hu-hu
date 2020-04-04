---
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 03/31/2020
ms.author: juliako
ms.openlocfilehash: 99aeb5384b317d1b4d291c769b5402e829247b30
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656198"
---
> [!NOTE]
> A nem rögzített erőforrások esetében nyisson meg egy támogatási jegyet a kvóták növelésének kéréséhez. Ne hozzon létre további Azure Media Services-fiókokat, hogy magasabb korlátokat szerezzen.

### <a name="account-limits"></a>Fiókkorlátok

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Media Services-fiókok egyetlen előfizetésben | 25 (rögzített) |

### <a name="asset-limits"></a>Eszközlimitek

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Eszközök Media Services-fiókonként | 1,000,000|

### <a name="storage-media-limits"></a>Tárolási (adathordozós) korlátok

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Fájlméret| Bizonyos esetekben a Media Services feldolgozásához támogatott maximális fájlméret korlátozva van. <sup>(1)</sup> |
| Tárfiókok | 100<sup>(2)</sup> (rögzített) |

<sup>1</sup> Az egyetlen blob maximális támogatott mérete jelenleg legfeljebb 5 TB az Azure Blob Storage-ban. További korlátozások vonatkoznak a Media Services a szolgáltatás által használt virtuális gép méretealapján. A méretkorlát a feltöltött fájlokra, valamint a Media Services feldolgozása (kódolása vagy elemzése) eredményeként létrehozott fájlokra vonatkozik. Ha a forrásfájl nagyobb, mint 260 GB, a feladat valószínűleg sikertelen lesz. 

Az alábbi táblázat az S1, S2 és S3 adathordozó-lefoglalt egységek rekedéseit mutatja be. Ha a forrásfájl nagyobb, mint a táblázatban meghatározott korlátok, a kódolási feladat sikertelen lesz. Ha hosszú időtartamú 4K felbontású forrásokat kódol, a szükséges teljesítmény eléréséhez S3 média számára fenntartott egységeket kell használnia. Ha az S3-ban fenntartott egységekre vonatkozó 260 GB-os korlátnál nagyobb 4K-s tartalommal rendelkezik, nyisson meg egy támogatási jegyet.

|Adathordozóáltal lefoglalt egység típusa|Maximális bemeneti méret (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> A tárfiókoknak ugyanabból az Azure-előfizetésből kell származniuk.

### <a name="jobs-encoding--analyzing-limits"></a>Feladatok (kódolás & elemzés) határértékek

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Feladatok Media Services-fiókonként | 500 000 <sup>(3)</sup> (rögzített)|
| Feladatonkénti alapanyagok | 50 (rögzített)|
| Feladatonkénti munkakimenetek | 20 (rögzített) |
| Átalakítások Media Services-fiókonként | 100 (fix)|
| Kimenetek átalakítása átalakításban | 20 (rögzített) |
| Fájlok feladatbemenetenként|10 (rögzített)|

<sup>3</sup> Ez a szám tartalmazza a várólistán lévő, befejezett, aktív és megszakított feladatokat. Nem tartalmazza a törölt feladatokat. 

A fiókban lévő, 90 napnál régebbi feladatbejegyzések automatikusan törlődnek, még akkor is, ha a rekordok teljes száma a maximális kvóta alatt van. 

### <a name="live-streaming-limits"></a>Élő közvetítéskorlátai

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Élő események <sup>(4)</sup> Media Services-fiókonként |5|
| Élő kimenetek élő eseményenként |3 <sup>(5)</sup> |
| Élő kimenet maximális időtartama | 25 óra |

<sup>4</sup> Az Élő események korlátozásairól az [Élő eseménytípusok összehasonlítása és korlátozásai](../articles/media-services/latest/live-event-types-comparison.md)című témakörben talál részletes információt.

<sup>5</sup> Az élő kimenetek létrehozásakor kezdődnek, és törléskor leállnak.

### <a name="packaging--delivery-limits"></a>Csomagolási & szállítási korlátok

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Végpontok streamelése (leállítva vagy futtatva) Media Services-fiókonként|2 (rögzített)|
| Dinamikusjegyzék-szűrők|100|
| Streamelési szabályok | 100 <sup>(6) Az</sup> |
| Egy eszközhöz egyszerre társított egyedi streamelési lokátorok | 100<sup>(7)</sup> (rögzített) |

<sup>6</sup> Egyéni [streamelési házirend](https://docs.microsoft.com/rest/api/media/streamingpolicies)használataesetén a Media Service-fiókhoz korlátozott számú ilyen házirendet kell megterveznie, és újra fel kell használnia őket a StreamingLocatorok számára, amikor ugyanazokra a titkosítási beállításokra és protokollokra van szükség. Nem hozhat létre új streamelési szabályzatot az egyes streamelési lokátorokhoz.

<sup>7</sup> A streamelési lokátorok at nem a felhasználónkénti hozzáférés-vezérlés kezelésére tervezték. Ha az egyes felhasználóknak különböző hozzáférési jogosultságokat szeretne biztosítani, válassza a digitális jogkezelési (DRM) megoldásokat.

### <a name="protection-limits"></a>Védelmi korlátok

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Beállítások tartalomkulcs-házirendenként |30 | 
| Licencek havonta a Media Services kulcskézbesítési szolgáltatásában szereplő drm-típusok hoz a fiókonként|1,000,000|

### <a name="support-ticket"></a>Támogatási jegy

A nem rögzített erőforrások esetében kérheti a kvóták emelését egy [támogatási jegy](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)megnyitásával. Részletes információkat a kérelemben a kívánt kvótaváltozásokról, a használati esetekről és a szükséges régiókról. <br/>**Ne** hozzon létre további Azure Media Services-fiókokat csak azért, hogy megpróbálja tágítani a korlátokat.
