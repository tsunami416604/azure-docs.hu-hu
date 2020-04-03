---
title: Kvóták és korlátozások az Azure Media Servicesben
description: Ez a témakör a Microsoft Azure Media Services kvótáit és korlátait ismerteti.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/01/2020
ms.author: juliako
ms.openlocfilehash: 2d4f5f83335f6f115362bcf66cf69d7f9de7eaa5
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582325"
---
# <a name="azure-media-services-quotas-and-limits"></a>Az Azure Media Services kvótái és korlátai

Ez a cikk a Leggyakoribb Microsoft Azure Media Services-korlátokat sorolja fel, amelyeket kvótáknak is neveznek.

> [!NOTE]
> A nem rögzített erőforrások esetében nyisson meg egy támogatási jegyet a kvóták növelésének kéréséhez. Ne hozzon létre további Azure Media Services-fiókokat, hogy magasabb korlátokat szerezzen.

## <a name="account-limits"></a>Fiókkorlátok

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| [Media Services-fiókok](media-services-account-concept.md) egyetlen előfizetésben | 25 (rögzített) |

## <a name="asset-limits"></a>Eszközlimitek

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| [Eszközök](assets-concept.md) Media Services-fiókonként | 1,000,000|

## <a name="storage-limits"></a>Tárolási korlátok

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Fájlméret| Bizonyos esetekben a Media Services feldolgozásához támogatott maximális fájlméret korlátozva van. <sup>(1)</sup> |
| [Tárfiókok](storage-account-concept.md) | 100<sup>(2)</sup> (rögzített) |

<sup>1</sup> Az egyetlen blob maximális támogatott mérete jelenleg legfeljebb 5 TB az Azure Blob Storage-ban. További korlátozások vonatkoznak a Media Services a szolgáltatás által használt virtuális gép méretealapján. A méretkorlát a feltöltött fájlokra, valamint a Media Services feldolgozása (kódolása vagy elemzése) eredményeként létrehozott fájlokra vonatkozik. Ha a forrásfájl nagyobb, mint 260 GB, a feladat valószínűleg sikertelen lesz. 

Az alábbi táblázat az S1, S2 és S3 adathordozó-lefoglalt egységek rekedéseit mutatja be. Ha a forrásfájl nagyobb, mint a táblázatban meghatározott korlátok, a kódolási feladat sikertelen lesz. Ha hosszú időtartamú 4K felbontású forrásokat kódol, a szükséges teljesítmény eléréséhez S3 média számára fenntartott egységeket kell használnia. Ha az S3-ban fenntartott egységekre vonatkozó 260 GB-os korlátnál nagyobb 4K-s tartalommal rendelkezik, nyisson meg egy támogatási jegyet.

|Adathordozóáltal lefoglalt egység típusa|Maximális bemeneti méret (GB)|
|---|---|
|S1 |    26|
|S2    | 60|
|S3    |260|

<sup>2</sup> A tárfiókoknak ugyanabból az Azure-előfizetésből kell származniuk.

## <a name="jobs-encoding--analyzing-limits"></a>Feladatok (kódolás & elemzés) határértékek

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| [Feladatok](transforms-jobs-concept.md) Media Services-fiókonként | 500 000 <sup>(3)</sup> (rögzített)|
| Feladatonkénti alapanyagok | 50 (rögzített)|
| Feladatonkénti munkakimenetek | 20 (rögzített) |
| [Átalakítások](transforms-jobs-concept.md) Media Services-fiókonként | 100 (fix)|
| Kimenetek átalakítása átalakításban | 20 (rögzített) |
| Fájlok feladatbemenetenként|10 (rögzített)|

<sup>3</sup> Ez a szám tartalmazza a várólistán lévő, befejezett, aktív és megszakított feladatokat. Nem tartalmazza a törölt feladatokat. 

A fiókban lévő, 90 napnál régebbi feladatbejegyzések automatikusan törlődnek, még akkor is, ha a rekordok teljes száma a maximális kvóta alatt van. 

## <a name="live-streaming-limits"></a>Élő közvetítéskorlátai

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| [Élő események](live-events-outputs-concept.md) <sup>(4)</sup> Media Services-fiókonként |5|
| Élő kimenetek élő eseményenként |3 <sup>(5)</sup> |
| Élő kimenet maximális időtartama | 25 óra |

<sup>4</sup> Az élő eseménykorlátokkal kapcsolatos részletes információkért lásd: [Élő eseménytípusok összehasonlítása és korlátai.](live-event-types-comparison.md)

<sup>5</sup> Az élő kimenetek létrehozásakor kezdődnek, és törléskor leállnak.

## <a name="packaging--delivery-limits"></a>Csomagolási & szállítási korlátok

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| [Végpontok streamelése](streaming-endpoint-concept.md) (leállítva vagy futtatva) Media Services-fiókonként|2 |
| [Dinamikusjegyzék-szűrők](filters-dynamic-manifest-overview.md)|100|
| [Streamelési szabályzatok](streaming-policy-concept.md) | 100 <sup>(6) Az</sup> |
| Egy eszközhöz egyszerre társított egyedi [streamelési lokátorok](streaming-locators-concept.md) | 100<sup>(7)</sup> (rögzített) |

<sup>6</sup> Egyéni [streamelési házirend](https://docs.microsoft.com/rest/api/media/streamingpolicies)használataesetén a Media Service-fiókhoz korlátozott számú ilyen házirendet kell megterveznie, és újra fel kell használnia őket a StreamingLocatorok számára, amikor ugyanazokra a titkosítási beállításokra és protokollokra van szükség. Nem hozhat létre új streamelési szabályzatot az egyes streamelési lokátorokhoz.

<sup>7</sup> A streamelési lokátorok at nem a felhasználónkénti hozzáférés-vezérlés kezelésére tervezték. Ha az egyes felhasználóknak különböző hozzáférési jogosultságokat szeretne biztosítani, válassza a digitális jogkezelési (DRM) megoldásokat.

## <a name="protection-limits"></a>Védelmi korlátok

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Beállítások [tartalomkulcs-házirendenként](content-key-policy-concept.md) |30 | 
| Licencek havonta a Media Services kulcskézbesítési szolgáltatásában szereplő drm-típusok hoz a fiókonként|1,000,000|

## <a name="support-ticket"></a>Támogatási jegy

A nem rögzített erőforrások esetében kérheti a kvóták emelését egy [támogatási jegy](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)megnyitásával. Részletes információkat a kérelemben a kívánt kvótaváltozásokról, a használati esetekről és a szükséges régiókról. <br/>**Ne** hozzon létre további Azure Media Services-fiókokat csak azért, hogy megpróbálja tágítani a korlátokat.

## <a name="next-steps"></a>További lépések

[Áttekintés](media-services-overview.md)
