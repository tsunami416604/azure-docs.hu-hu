---
title: Dedikált event hubs – Azure Event Hubs áttekintése |} A Microsoft Docs
description: Ez a cikk az Azure Event Hubs, így az event hubs szolgáltatás példányainak egybérlős dedciated áttekintést nyújt.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: d418715ab651721d03b67bd411eb90607391bf10
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2019
ms.locfileid: "54402336"
---
# <a name="overview-of-event-hubs-dedicated"></a>Dedikált Event Hubs áttekintése

*Event Hubs dedikált* kapacitást kínál egybérlős központi telepítések a legszigorúbb követelményeknek rendelkező ügyfelek számára. Teljes méretű, az Azure Event Hubs bejövő képes második vagy legfeljebb 2 GB-nyi a telemetriai adatok teljesen tartós tárolási és a másodperc törtrésze késése másodpercenként több mint 2 millió eseményt. Ez is lehetővé teszi, hogy integrált megoldások feldolgozása valós idejű és kötegelt ugyanazon a rendszeren. A [Event Hubs Capture](event-hubs-capture-overview.md) ajánlatunkban benne foglalt, csökkentheti a megoldás bonyolultságát támogatja mind a valós idejű, mind a batch-alapú folyamatok egyetlen streammel kellene.

Az alábbi táblázat hasonlítja össze az elérhető az Event Hubs szolgáltatásszintjét. Az Event Hubs dedikált ajánlat fix havi díja, a legtöbb funkciót a Standard díjszabás használati képest. A dedikált szintet biztosít minden funkció a standard szintű csomag, de az erőforrás-igényű számítási feladatok rendelkező ügyfelek számára a vállalati méretezési kapacitással. 

| Szolgáltatás | Standard | Dedikált |
| --- |:---:|:---:|:---:|
| Belépő események | / Millió esemény kell fizetnie | Tartalmazza |
| Kapacitásegység (1 MB/s belépő, 2 MB/s kilépő) | Fizetés / óra | Tartalmazza |
| Üzenet mérete | 1 MB | 1 MB |
| Kiadói irányelvek | Igen | Igen |   
| Felhasználói csoportok | 20 | 20 |
| Üzenetek visszajátszása | Igen | Igen |
| Kapacitásegységek maximális száma | 20 (rugalmas 100)   | 1 kapacitásegység (CU) ≈ 50 |
| Felügyelt kapcsolatok | 1000 tartalmazza | 100 K csomagban foglalt |
| További felügyelt kapcsolatok | Igen | Igen |
| Üzenetmegőrzés | 1 ingyenes nap | Legfeljebb 7 napot tartalmaz |
| Rögzítés | Fizetés / óra | Tartalmazza |

## <a name="benefits-of-event-hubs-dedicated-capacity"></a>Event Hubs dedikált kapacitás előnyei

A következő előnyöket Event Hubs dedikált használatakor érhetők el:

* Egyetlen bérlő nincs más bérlőktől származó zajok üzemeltet.
* Minden alkalommal ismételhető teljesítménye.
* Garantált kapacitás adatlöketek igényeinek.
* Magában foglalja a [rögzítése](event-hubs-capture-overview.md) funkciója az Event Hubs irányuló mikrokötegelt és hosszú távú megőrzésre integrációt biztosít.
* Zero maintenance: A szolgáltatás kezeli a terheléselosztást, az operációs rendszer frissítések, biztonsági javítások és a particionálás.
* Rögzített óradíj.
* Üzenetmegőrzés fel és 7 nap a külön díjfizetés nélkül.

Event Hubs dedikált is eltávolítja a Standard ajánlat átviteli vonatkozó korlátozások némelyike. Átviteli egységek a Standard szinten 1000 esemény második vagy 1 MB / s belépő / átviteli egység és a dupla a kimenő forgalom mennyisége jogosít. A dedikált méretezési ajánlat nem korlátozza a bejövő forgalom rendelkezik, és a kilépő események számát. Ezek a korlátok vonatkoznak rájuk csak a megvásárolt az event hubs feldolgozási kapacitását.

A fenntartott, dedikált környezetben egyéb lehetőségeket kínál, egyedi ezt a szintre, például:

* Azt szabályozza, hogy a fürt névterek száma.
* Teljesítmény korlátok megadja az egyes a névterek.
* Konfigurálja az egyes névterek alatt az event hubs száma.
* Meghatározza, hogy a partíciók számának korlátját.

Ez a szolgáltatás célja a legnagyobb telemetriai felhasználókat, és nagyvállalati szerződéssel rendelkező ügyfelek számára érhető el.

## <a name="how-to-onboard"></a>Hogyan vezethető

Felfelé és lefelé az igényeinek hozzáadásával vagy eltávolításával CUs a hónap során is méretezheti a kapacitást. A dedikált terv sajátossága, hogy egy több gyakorlati Bevezetés az Event Hubs termékért felelős csoport a rugalmas üzembe helyezést az Ön számára megfelelő beolvasni a fog tapasztalni. Ez a Termékváltozat előkészítése a [számlázási támogatásától](https://ms.portal.azure.com/#create/Microsoft.Support) vagy a Microsoft helyi képviselőjéhez.

## <a name="next-steps"></a>További lépések

Forduljon a Microsoft értékesítési képviselőjével vagy a Microsoft Support további részletes információkat Event Hubs dedikált kapacitást. Akkor is tudhat meg többet az Event Hubs tarifacsomagok kövesse az alábbi hivatkozásokat:

- [Event Hubs dedikált díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/). A Microsoft értékesítési képviselőjével vagy a Microsoft Support további részletes információkat Event Hubs dedikált kapacitást is kérhet segítséget.
- A [Event Hubs – gyakori kérdések](event-hubs-faq.md) díjszabási információkat tartalmaz, valamint választ ad néhány gyakori kérdés az Event Hubs szolgáltatásról. 
