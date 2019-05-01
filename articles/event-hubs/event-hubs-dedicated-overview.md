---
title: Dedikált event hubs – Azure Event Hubs áttekintése |} A Microsoft Docs
description: Ez a cikk a dedikált Azure Event Hubs, az event hubs egybérlős központi telepítései kínál, amelyek áttekintést nyújt.
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
ms.openlocfilehash: 52d46009464c7417d5b525154fdac09c030aabe7
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64708008"
---
# <a name="overview-of-event-hubs-dedicated"></a>Dedikált Event Hubs áttekintése

*Event Hubs-fürtök* egybérlős központi telepítések a legnagyobb erőforrás-igényű streamelési igényekkel rendelkező ügyfelek számára kínálnak. Az egybérlős ajánlattal rendelkezik garantált 99,99 %-os SLA-t, és csak a dedikált tarifacsomagban elérhető. Az Event Hubs-fürt is garantált kapacitás és a másodperc törtrésze késése másodpercenként több millió bejövő forgalom. A dedikált fürtön belül létrehozott névterek és az event hubs többek között az összes, a Standard ajánlat és más, de minden bejövő forgalom korlátok nélkül. Ezenkívül tartalmazza a népszerű [Event Hubs Capture](event-hubs-capture-overview.md) további költségek nélkül, így automatikusan batch és a naplófájlok adatfolyamok kezelése az Azure Storage vagy az Azure Data Lake szolgáltatást. 

Dedikált fürtök kiépítése, és a számlázás alapját **kapacitásegységek (CUs)**, egy előre kiosztott Processzor és memória-erőforrások mennyiségét. Minden egyes fürt az 1, 2, 4, 8, 12, 16 vagy 20 kapacitásegységek vásárolhatók. Mennyit képes fogadni, és adatfolyam-/ Kapacitásegység függ számos tényezőtől, például az előállítók és fogyasztók, hasznos adat alakzat, kimenő forgalom száma (lásd a részleteket alább a teljesítménytesztek eredménye) értékelésére. 

## <a name="why-dedicated"></a>Miért dedikált?

Dedikált Event hubs szolgáltatás három meggyőző előnyöket kínál az ügyfelek esetében, akik vállalati szintű kapacitásra van szüksége:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Single-bérlős garantálja a kapacitás, a jobb teljesítmény érdekében

Egy dedikált fürt garantálja a kapacitás teljes méretű, és legfeljebb streamelési adatok befogadásához bármely teljes tartós tárolási és a másodperc törtrésze késéssel gigabájt bejövő burst is a forgalom. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Funkciók között lehet és kizárólagos hozzáférést 
A dedikált szolgáltatás, jelenleg nincs további költség, valamint a később megjelenő funkciók, például a BYOK kizárólagos hozzáférést rögzítése szolgáltatást tartalmaz. A szolgáltatás is kezeli a terheléselosztást, az operációs rendszer frissítések, biztonsági javítások és a vásárlók particionálási úgy, hogy kevesebb időt beváltható az infrastruktúra-karbantartási és több időt ügyféloldali funkciók.  

#### <a name="cost-savings"></a>Költségmegtakarítás
Bejövő nagy mennyiségben (> 100 átviteli egységek), fürt költségei jelentősen kisebb óránként a Standard ajánlat az átviteli egységek hasonló mennyiségű megvásárlásával.


## <a name="event-hubs-standard-vs-dedicated"></a>Event Hubs Standard vs. Dedikált

Az alábbi táblázat hasonlítja össze az elérhető az Event Hubs szolgáltatásszintjét. Az Event Hubs dedikált ajánlat fix havi díja, képest használati a legtöbb funkciót a Standard díjszabása alapján történik. A dedikált szintet biztosít minden funkció a standard szintű csomag, de az erőforrás-igényű számítási feladatok rendelkező ügyfelek számára a vállalati méretezési kapacitással. 

| Szolgáltatás | Standard | Dedikált |
| --- |:---:|:---:|
| Belépő események | / Millió esemény kell fizetnie | Tartalmazza |
| Kapacitásegység (1 MB/s belépő, 2 MB/s kilépő) | Fizetés / óra | Tartalmazza |
| Üzenet mérete | 1 MB | 1 MB |
| Partíciók | 40 névterenként | 2000 / Kapacitásegység |
| Felhasználói csoportok | Eseményközpont 20 | 1000 Event Hubs |
| Legfeljebb Bandwidth | 20 átviteli egységek (legfeljebb 40 átviteli egységek)    | 20 kapacitásegység használható |
| Felügyelt kapcsolatok | 1000 tartalmazza | 100 K csomagban foglalt |
| Üzenetmegőrzés | 1 ingyenes nap | Legfeljebb 7 napot tartalmaz |
| Rögzítés | Fizetés / óra | Tartalmazza |

## <a name="what-can-i-achieve-with-a-cluster"></a>Mit érhet és egy fürt?

Az Event Hubs-fürt mennyi betöltési és streamelése függ, számos tényező befolyásolja, például a gyártó, a fogyasztók, a sebesség, amellyel feldolgozására és feldolgozása és még sok más. 

Következő táblázatban a teljesítményteszt eredménye, hogy mi érhető el a tesztelés során:

| Hasznos adat alakzat | Fogadók | Bejövő sávszélesség| Bejövő üzenetek | Kimenő adatforgalmat | Kimenő üzenetek | Teljes átviteli egységek | Átviteli egységek / Kapacitásegység |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB váró | 2 | 400 MB/mp | 400 k üzenetek/mp | 800 MB/mp | 800 k üzenetek/mp | 400 átviteli egységek | 100 átviteli egységek | 
| 10x10KB váró | 2 | 666 MB/mp | 66.6 k üzenetek/mp | 1,33 GB/mp | 133 k üzenetek/mp | 666 átviteli egységek | 166 átviteli egységek |
| 6x32KB váró | 1 | 1.05 GB/mp | 34 k üzenetek / másodperc | 1.05 GB/mp | 34 k üzenetek/mp | 1000 átviteli egységek | 250 átviteli egységek |

A vizsgálat során, a következő feltételek lett megadva:

- Az Event Hubs dedikált szintjét fürtök négy kapacitásegységek (CUs) lett megadva. 
- Az event hubs streamfeldolgozási használt 200 partíciók rendelkezett. 
- Volt betöltött adatokat fogad az összes partíció két fogadó alkalmazás kapott.

## <a name="how-to-onboard"></a>Hogyan vezethető

Ez a Termékváltozat előkészítése a [számlázási támogatásától](https://ms.portal.azure.com/#create/Microsoft.Support) vagy a Microsoft helyi képviselőjéhez. Felfelé és lefelé az igényeinek hozzáadásával vagy eltávolításával CUs a hónap során is méretezheti a kapacitást. A dedikált terv sajátossága, hogy egy több gyakorlati Bevezetés az Event Hubs termékért felelős csoport a rugalmas üzembe helyezést az Ön számára megfelelő beolvasni a fog tapasztalni. 

## <a name="next-steps"></a>További lépések

Forduljon a Microsoft értékesítési képviselőjével vagy a Microsoft Support további részletes információkat Event Hubs dedikált kapacitást. Akkor is tudhat meg többet az Event Hubs tarifacsomagok kövesse az alábbi hivatkozásokat:

- [Event Hubs dedikált díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/). A Microsoft értékesítési képviselőjével vagy a Microsoft Support további részletes információkat Event Hubs dedikált kapacitást is kérhet segítséget.
- A [Event Hubs – gyakori kérdések](event-hubs-faq.md) díjszabási információkat tartalmaz, valamint választ ad néhány gyakori kérdés az Event Hubs szolgáltatásról.
