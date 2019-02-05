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
ms.date: 02/03/2019
ms.author: juliako
ms.openlocfilehash: 1bada751ffac45b36faad6e978567233901adc7d
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2019
ms.locfileid: "55700315"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Kvóták és korlátozások az Azure Media Services v3

Ez a cikk azt ismerteti, kvóták és korlátozások az Azure Media Services v3.

| Erőforrás | Alapértelmezett korlát | 
| --- | --- | 
| Eszközök az Azure Media Services-fiókonként | 1,000,000|
| Dinamikusjegyzék-szűrők|100|
| JobInputs feladatonként | 50 (fix)|
| / Feladat/TransformOutputs az átalakító JobOutputs | 20 (fixed) |
| Fájlok JobInput száma|10 (fix)|
| Fájlméret| Bizonyos esetekben nincs korlátozva a Media Services támogatja a maximális fájlméretet. <sup>(1)</sup> |
| Feladatok száma a Media Services-fiók | 500 000 <sup>(2)</sup> (fix)|
| Átalakítások listázása|A választ, az 1000 átalakítások laponként böngész|
| Feladatok listázása|A válasz az 500 feladatok laponként böngész|
| Élő események Media Services-fiókonként |5|
| Egyetlen előfizetéshez Media Services-fiókok | 25 (rögzített) |
| Futó állapotú videókhoz élő kimenetek |3|
| Tárfiókok | 100<sup>(4)</sup> (fix) |
| Streamvégpontok futó állapotban a Media Services-fiókonként|2|
| Streamelési szabályok | 100 <sup>(3)</sup> |
| A Media Services-fiókonként átalakítások | 100 (fix)|
| Egy eszköz egyszerre társított egyedi Streamelési Lokátorok | 100<sup>(5)</sup> (fix) |

<sup>1</sup> egy blob jelenleg legfeljebb 5 TB-os Azure Blob Storage-ban támogatott maximális méretét. Azonban további korlátozások érvényesek az Azure Media Services a szolgáltatás által használt Virtuálisgép-méretek alapján. Ha a forrásfájl 260-GB nál nagyobb, a feladat valószínűleg sikertelen lesz. Ha 4K tartalom, amely nagyobb, mint 260 GB-os korlátot, írjon nekünk az amshelp@microsoft.com a támogatásához a lehetséges kezelésükre.

<sup>2</sup> ezt az értéket tartalmazza a sorba állított, befejezett, aktív és a visszavont feladatokat. Törölt feladatokat nem tartalmazza. 

A fiókjában 90 napnál régebbi feladat rekordot automatikusan törölve lesznek, akkor is, ha a rekordok száma nem éri a maximális kvótát. 

<sup>3</sup> egyéni használatakor [Streamelési házirend](https://docs.microsoft.com/rest/api/media/streamingpolicies), korlátozott számú házirendeket tervezzen a Media Services-fiók, és újra alkalmazza őket a StreamingLocators, amikor ugyanazt a titkosítási beállítások és protokollok van szükség. Meg kell nem lehet új szabályzatot hoz létre Streamelési az egyes Streamelési lokátor.

<sup>4</sup> a tárfiókok az Azure-előfizetéshez kell lennie.

<sup>5</sup> streamelési Lokátorok nem felhasználónkénti hozzáférés-vezérlés kezelésére tervezték. Ha az egyes felhasználóknak különböző hozzáférési jogosultságokat szeretne biztosítani, válassza a digitális jogkezelési (DRM) megoldásokat.

## <a name="support-ticket"></a>Támogatási jegy

Nem rögzített erőforrások, kérheti a kvóták megnyitásával generál egy [támogatási jegyet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Részletes információkat tartalmazza a kérés a kívánt kvótát változik, a használatieset-forgatókönyvek, és a szükséges régiókról. <br/>**Ne** hozzon létre további Azure Media Services-fiókokat csak azért, hogy megpróbálja tágítani a korlátokat.

## <a name="next-steps"></a>További lépések

[Áttekintés](media-services-overview.md)
