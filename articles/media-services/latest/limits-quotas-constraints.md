---
title: Kvóták és korlátozások az Azure Media Services v3 |} A Microsoft Docs
description: Ez a témakör ismerteti a kvóták és korlátozások az Azure Media Services v3
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/16/2019
ms.author: juliako
ms.openlocfilehash: 1aa15a42893d867ae18c267e163e8df94af50723
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65824459"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvóták és korlátozások az Azure Media Services v3

Ez a cikk azt ismerteti, kvóták és korlátozások az Azure Media Services v3.

| Resource | Alapértelmezett korlát | 
| --- | --- | 
| Egy Azure Media Services-fiókra jutó adategység | 1,000,000|
| Dinamikusjegyzék-szűrők|100|
| JobInputs feladatonként | 50 (fix)|
| JobOutputs feladatonként | 20 (fixed) |
| Az átalakító TransformOutputs | 20 (fixed) |
| Fájlok JobInput száma|10 (fix)|
| Fájlméret| Bizonyos esetekben nincs korlátozva a Media Services támogatja a maximális fájlméretet. <sup>(1)</sup> |
| Feladatok száma a Media Services-fiók | 500 000 <sup>(2)</sup> (fix)|
| Átalakítások listázása|A válasz oldalakra bontása oldalanként 1000 átalakítással|
| Feladatok listázása|A válasz oldalakra bontása oldalanként 500 feladattal|
| Élő események Media Services-fiókonként |5|
| Egyetlen előfizetéshez Media Services-fiókok | 25 (rögzített) |
| Élő esemény élő kimenetek |3 <sup>(3)</sup> |
| Élő kimeneti maximális időtartama | 25 óra |
| Tárfiókok | 100<sup>(4)</sup> (fix) |
| Streamvégpontok (Leállítva vagy fut) a Media Services-fiókonként|2 (fix)|
| Streamelési szabályok | 100 <sup>(5)</sup> |
| A Media Services-fiókonként átalakítások | 100 (fix)|
| Egy eszköz egyszerre társított egyedi Streamelési Lokátorok | 100<sup>(6)</sup> (fix) |
| Tartalmi kulcs szabályzatonként beállításai |30 | 

<sup>1</sup> egy blob jelenleg legfeljebb 5 TB-os Azure Blob Storage-ban támogatott maximális méretét. További korlátozások érvényesek a Media Services a szolgáltatás által használt Virtuálisgép-méretek alapján. A méretkorlát a feltöltött fájlokat, és a fájlokat, a Media Services – feldolgozás (kódolás és elemzése) eredményeként létrehozott első vonatkozik. Ha a forrásfájl 260-GB nál nagyobb, a feladat valószínűleg sikertelen lesz. 

Az alábbi táblázatban látható a korlátok a media szolgáltatás számára fenntartott egységek S1, S2 és S3. Ha a forrásfájl mérete nagyobb, mint a tábla meghatározott keretek, a kódolási feladat sikertelen lesz. Ha hosszú időtartam 4K felbontása forrásai kódol, meg kell S3 szintű media szolgáltatás számára fenntartott egységek használata szükséges a teljesítmény elérése érdekében. Ha 4K tartalom, amely nagyobb, mint az S3 szintű media szolgáltatás számára fenntartott egységek 260 GB-os korlátot, írjon nekünk az amshelp@microsoft.com a támogatásához a lehetséges kezelésükre.

|Media szolgáltatás számára fenntartott egység típusa   |Maximális bemeneti mérete (GB)|
|---|---|
|S1 |   26|
|S2 | 60|
|S3 |260|

<sup>2</sup> ezt az értéket tartalmazza a sorba állított, befejezett, aktív és a visszavont feladatokat. Törölt feladatokat nem tartalmazza. 

A fiókjában 90 napnál régebbi feladat rekordot automatikusan törölve lesznek, akkor is, ha a rekordok száma nem éri a maximális kvótát. 

<sup>3</sup> élő kimenetek létrehozás indítása és leállítása, ha törli.

<sup>4</sup> a tárfiókok az Azure-előfizetéshez kell lennie.

<sup>5</sup> egyéni használatakor [Streamelési házirend](https://docs.microsoft.com/rest/api/media/streamingpolicies), korlátozott számú házirendeket tervezzen a Media Services-fiók, és újra alkalmazza őket a StreamingLocators, amikor ugyanazt a titkosítási beállítások és protokollok van szükség. Meg kell nem lehet új szabályzatot hoz létre Streamelési az egyes Streamelési lokátor.

<sup>6</sup> streamelési Lokátorok nem felhasználónkénti hozzáférés-vezérlés kezelésére tervezték. Ha az egyes felhasználóknak különböző hozzáférési jogosultságokat szeretne biztosítani, válassza a digitális jogkezelési (DRM) megoldásokat.

## <a name="support-ticket"></a>Támogatási jegy

Nem rögzített erőforrások, kérheti a kvóták megnyitásával generál egy [támogatási jegyet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Részletes információkat tartalmazza a kérés a kívánt kvótát változik, a használatieset-forgatókönyvek, és a szükséges régiókról. <br/>**Ne** hozzon létre további Azure Media Services-fiókokat csak azért, hogy megpróbálja tágítani a korlátokat.

## <a name="next-steps"></a>További lépések

[Áttekintés](media-services-overview.md)
