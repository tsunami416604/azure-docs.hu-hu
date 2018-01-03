---
title: "Azure Event Hubs dedikált kapacitás áttekintése |} Microsoft Docs"
description: "A Microsoft Azure Event Hubs dedikált kapacitás áttekintése."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: sethm;babanisa
ms.openlocfilehash: 563152a019464f3d0342383ff13e6ee1c87a22fe
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="overview-of-event-hubs-dedicated"></a>Dedikált Event Hubs áttekintése

*Event Hubs dedikált* kapacitás biztosít egy bérlői központi telepítések a legnagyobb igénybevételt jelentő követelményekkel rendelkező ügyfelek esetén. Teljes léptékű Azure Event Hubs érkező is több mint 2 millió események második vagy legfeljebb 2 GB / másodperc teljes tartós tárolási és alárendelt második késés telemetriai adatok száma. Ez egyben lehetővé teszi integrált megoldások feldolgozása valós idejű és kötegelt ugyanarra a rendszerre. A [Event Hubs rögzítése](event-hubs-capture-overview.md) az ajánlatban szereplő, csökkentheti a megoldás összetettsége azzal, hogy valós időben és kötegelt alapú folyamatok támogatja egyetlen adatfolyam.

Az alábbi táblázat összehasonlítja a rendelkezésre álló szolgáltatásszintek az Event hubs. Az Event Hubs dedikált elérhető rögzített havi ár, használati legtöbb szolgáltatások standard díjszabás képest. A dedikált kínál, a Standard csomag, de nagy munkaterhelések rendelkező ügyfelek esetén a vállalati méretezési kapacitással rendelkező összes funkcióját. 

| Szolgáltatás | Standard | Dedikált |
| --- |:---:|:---:|:---:|
| Belépő események | Fizessen millió esemény | Tartalmazza |
| Átviteli egység (1 MB/s érkező, kimenő 2 MB/s) | Fizessen óránként | Tartalmazza |
| Üzenet mérete | 256 KB | 1 MB |
| Kiadói irányelvek | Igen | Igen |   
| Felhasználói csoportok | 20 | 20 |
| Üzenetek visszajátszása | Igen | Igen |
| Kapacitásegységek maximális száma | 20 (rugalmas 100)   | 1 kapacitásegység (CU) ≈ 50 |
| Felügyelt kapcsolatok | 1000 tartalmazza | 100 K tartalmazza |
| További felügyelt kapcsolatok | Igen | Igen |
| Üzenetmegőrzés | 1 ingyenes nap | Legfeljebb 7 napot tartalmaz |
| Rögzítés | Fizessen óránként | Tartalmazza |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Event Hubs dedikált kapacitás előnyei

A következő előnyöket Event Hubs dedikált használatakor érhetők el:

* A többi bérlő nem zaj üzemeltető egybérlős.
* Üzenet mérete 256 KB, a standard képest 1 MB-ra növeli.
* Minden alkalommal ismételhető teljesítménye.
* Garantált kapacitásnövelés igényeinek kielégítése érdekében.
* Magában foglalja a [rögzítése](event-hubs-capture-overview.md) az Event Hubs arra, hogy micro-kötegelt és a hosszú távú adatmegőrzési integrációja szolgáltatása.
* Karbantartási nulla: A szolgáltatás terheléselosztást, az operációs rendszer frissítések, biztonsági javítások és particionálás kezeli.
* Rögzített, óránként árak.
* Megőrzési üzenetküldés be a 7 napig nem külön kell fizetni.

Event Hubs dedikált is eltávolítja a szabványos ajánlat átviteli vonatkozó korlátozások némelyike. A normál rétegben, az átviteli egységek jogosíthatók 1000 események második vagy 1 MB / s érkező / SO és a dupla adott kimenő forgalom mennyisége. A dedikált méretezési ajánlat korlátozások nélkül legyen érkező és a kimenő forgalom esemény száma. Ezek a korlátozások csak a feldolgozási kapacitás a megvásárolt event hubs vonatkozik.

Ez a fenntartott, dedikált környezet más ebben a rétegben egyedi lehetőségeket, mint biztosítja:

* A névterek, a fürt számát szabályozza.
* Adja meg az átviteli sebességének korlátai egyes a névterek.
* Konfigurálja az egyes névterek alatt az event hubs száma.
* Meghatározza, hogy a partíciók számára vonatkozó korlátozást.

Ez a szolgáltatás a legnagyobb telemetriai felhasználók irányul, és nagyvállalati szerződéssel rendelkező ügyfelek számára elérhető.

## <a name="how-to-onboard"></a>Hogyan bevezetni

Felfelé vagy lefelé társították az igényeknek megfelelő hozzáadásával vagy eltávolításával a logikai csoport Egyé is méretezheti a kapacitást. A dedikált terv egyedi abban, hogy egy több gyakorlati Bevezetés az Event Hubs termékért felelős csoport a rugalmas telepítési, amely az Ön számára legmegfelelőbb lekérni a fog tapasztalni. Érheti el a Termékváltozat [kérje a számlázási támogatás segítségét](https://ms.portal.azure.com/#create/Microsoft.Support) vagy a Microsoft-képviselőjére.

## <a name="next-steps"></a>További lépések

Forduljon a Microsoft értékesítési képviselőink vagy a Microsoft Support Event Hubs dedikált kapacitásával kapcsolatos további részletek. Akkor is többet is megtudhat az Event Hubs tarifacsomagok érhetők el az alábbi hivatkozások:

- [Event Hubs dedikált árképzési](https://azure.microsoft.com/pricing/details/event-hubs/). Forduljon a Microsoft értékesítési képviselőink vagy a Microsoft Support Event Hubs dedikált kapacitásával kapcsolatos további részletek.
- A [Event Hubs gyakran ismételt kérdések](event-hubs-faq.md) díjszabási információkat tartalmaz, és az Event Hubs néhány gyakran feltett kérdésekre adott válaszok. 
