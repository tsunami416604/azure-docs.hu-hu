---
title: Kvóták és korlátozások az Azure Media Services v3-ban | Microsoft dokumentumok
description: Ez a témakör a Microsoft Azure Media Services v3-as verzióban szereplő kvótákat és korlátozásokat ismerteti.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/17/2019
ms.author: juliako
ms.openlocfilehash: 5a4f7e31cb17f47e8796ab99c1f8a089339903df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888427"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvóták és korlátozások az Azure Media Services v3-as részén

Ez a cikk az Azure Media Services v3-as szolgáltatásának kvótáit és korlátait ismerteti.

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Egy Azure Media Services-fiókra jutó adategység | 1,000,000|
| Dinamikusjegyzék-szűrők|100|
| JobInputs projektenként | 50 (rögzített)|
| JobOutputs feladatonként | 20 (rögzített) |
| Átalakító kimenetek átalakítása | 20 (rögzített) |
| Fájlok feladatbemenetenként|10 (rögzített)|
| Fájlméret| Bizonyos esetekben a Media Services feldolgozásához támogatott maximális fájlméret korlátozva van. <sup>(1)</sup> |
| Feladatok Media Services-fiókonként | 500 000 <sup>(2)</sup> (rögzített)|
| Élő események Media Services-fiókonként |5|
| Media Services-fiókok egyetlen előfizetésben | 25 (rögzített) |
| Élő kimenetek élő eseményenként |3 <sup>(3) Bekezdés</sup> |
| Élő kimenet maximális időtartama | 25 óra |
| Tárfiókok | 100<sup>(4)</sup> (rögzített) |
| Végpontok streamelése (leállítva vagy futtatva) Media Services-fiókonként|2 (rögzített)|
| Streamelési szabályok | 100 <sup>(5) Az</sup> |
| Átalakítások Media Services-fiókonként | 100 (fix)|
| Egy eszközhöz egyszerre társított egyedi streamelési lokátorok | 100<sup>(6)</sup> (rögzített) |
| Beállítások tartalomkulcs-házirendenként |30 | 
| Licencek havonta a Media Services kulcskézbesítési szolgáltatásában szereplő drm-típusok hoz a fiókonként|1,000,000|

<sup>1</sup> Az egyetlen blob maximális támogatott mérete jelenleg legfeljebb 5 TB az Azure Blob Storage-ban. További korlátozások vonatkoznak a Media Services a szolgáltatás által használt virtuális gép méretealapján. A méretkorlát a feltöltött fájlokra, valamint a Media Services feldolgozása (kódolása vagy elemzése) eredményeként létrehozott fájlokra vonatkozik. Ha a forrásfájl nagyobb, mint 260 GB, a feladat valószínűleg sikertelen lesz. 

Az alábbi táblázat az S1, S2 és S3 adathordozó-lefoglalt egységek rekedéseit mutatja be. Ha a forrásfájl nagyobb, mint a táblázatban meghatározott korlátok, a kódolási feladat sikertelen lesz. Ha hosszú időtartamú 4K felbontású forrásokat kódol, a szükséges teljesítmény eléréséhez S3 média számára fenntartott egységeket kell használnia. Ha az S3-ban fenntartott egységekre vonatkozó 260 GB-os korlátnál nagyobb 4K-s tartalommal rendelkezik, nyisson meg egy támogatási jegyet.

|Adathordozóáltal lefoglalt egység típusa   |Maximális bemeneti méret (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> Ez a szám tartalmazza a várólistán lévő, befejezett, aktív és megszakított feladatokat. Nem tartalmazza a törölt feladatokat. 

A fiókban lévő, 90 napnál régebbi feladatbejegyzések automatikusan törlődnek, még akkor is, ha a rekordok teljes száma a maximális kvóta alatt van. 

<sup>3</sup> Az élő kimenetek létrehozásakor kezdődnek, és törléskor leállnak.

<sup>4</sup> A tárfiókoknak ugyanabból az Azure-előfizetésből kell származniuk.

<sup>5</sup> Egyéni [streamelési házirend](https://docs.microsoft.com/rest/api/media/streamingpolicies)használataesetén a Media Service-fiókhoz korlátozott számú ilyen házirendet kell megterveznie, és újra fel kell használnia őket a StreamingLocatorok számára, amikor ugyanazokra a titkosítási beállításokra és protokollokra van szükség. Nem hozhat létre új streamelési szabályzatot az egyes streamelési lokátorokhoz.

<sup>6</sup> A streamelési lokátorok at nem a felhasználónkénti hozzáférés-vezérlés kezelésére tervezték. Ha az egyes felhasználóknak különböző hozzáférési jogosultságokat szeretne biztosítani, válassza a digitális jogkezelési (DRM) megoldásokat.

## <a name="support-ticket"></a>Támogatási jegy

A nem rögzített erőforrások esetében kérheti a kvóták emelését egy [támogatási jegy](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)megnyitásával. Részletes információkat a kérelemben a kívánt kvótaváltozásokról, a használati esetekről és a szükséges régiókról. <br/>**Ne** hozzon létre további Azure Media Services-fiókokat csak azért, hogy megpróbálja tágítani a korlátokat.

## <a name="next-steps"></a>További lépések

[Áttekintés](media-services-overview.md)
