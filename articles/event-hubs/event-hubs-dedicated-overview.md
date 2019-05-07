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
ms.openlocfilehash: a5184b9980dd9f83764950445c10e8bdfea6d71a
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65203947"
---
# <a name="overview-of-event-hubs-dedicated"></a>Dedikált Event Hubs áttekintése

*Event Hubs-fürtök* egybérlős központi telepítések a legnagyobb erőforrás-igényű streamelési igényekkel rendelkező ügyfelek számára kínálnak. Az egybérlős ajánlattal rendelkezik garantált 99,99 %-os SLA-t, és csak a dedikált tarifacsomagban elérhető. Az Event Hubs-fürt is garantált kapacitás és a másodperc törtrésze késése másodpercenként több millió bejövő forgalom. A dedikált fürtön belül létrehozott névterek és az event hubs többek között az összes, a Standard ajánlat és más, de minden bejövő forgalom korlátok nélkül. Ezenkívül tartalmazza a népszerű [Event Hubs Capture](event-hubs-capture-overview.md) további költségek nélkül, így automatikusan batch és a naplófájlok adatfolyamok kezelése az Azure Storage vagy az Azure Data Lake szolgáltatást. 

Fürtök kiépítése, és a számlázás alapját **kapacitásegységek (CUs)**, egy előre kiosztott Processzor és memória-erőforrások mennyiségét. Minden egyes fürt az 1, 2, 4, 8, 12, 16 vagy 20 kapacitásegységek vásárolhatók. Mennyit képes fogadni, és adatfolyam-/ Kapacitásegység függ számos tényezőtől, például az előállítók és fogyasztók, hasznos adat alakzat, kimenő forgalom száma (lásd a részleteket alább a teljesítménytesztek eredménye) értékelésére. 

> [!NOTE]
> Minden Event Hubs-fürtök alapértelmezés szerint a Kafka-engedélyezve, és támogatja a Kafka-végpontok, amelyek segítségével a meglévő által a Kafka-alapú alkalmazások. Kafka engedélyezve a kellene a fürt nem befolyásolja a a Kafka használati esetek; nincs lehetőség vagy tiltsa le a Kafka-fürt szükséges.

## <a name="why-dedicated"></a>Miért dedikált?

Dedikált Event hubs szolgáltatás három meggyőző előnyöket kínál az ügyfelek esetében, akik vállalati szintű kapacitásra van szüksége:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Single-bérlős garantálja a kapacitás, a jobb teljesítmény érdekében

Egy dedikált fürt garantálja a kapacitás teljes méretű, és legfeljebb streamelési adatok befogadásához bármely teljes tartós tárolási és a másodperc törtrésze késéssel gigabájt bejövő burst is a forgalom. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Funkciók között lehet és kizárólagos hozzáférést 
A dedikált szolgáltatás, jelenleg nincs további költség, valamint a később megjelenő funkciók, például a BYOK kizárólagos hozzáférést rögzítése szolgáltatást tartalmaz. A szolgáltatás is kezeli a terheléselosztást, az operációs rendszer frissítések, biztonsági javítások és a vásárlók particionálási úgy, hogy kevesebb időt beváltható az infrastruktúra-karbantartási és több időt ügyféloldali funkciók.  

#### <a name="cost-savings"></a>Költségmegtakarítás
Bejövő nagy mennyiségben (> 100 átviteli egységek), fürt költségei jelentősen kisebb óránként a Standard ajánlat az átviteli egységek hasonló mennyiségű megvásárlásával.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Az Event Hubs dedikált kvótái és korlátai

Az Event Hubs dedikált ajánlat fix havi díja, legalább 4 óra számoljuk fel. A dedikált szintet kínál összes funkciót, a standard szintű csomag, de a vállalati méretezés és korlátait az erőforrás-igényű számítási feladatok rendelkező ügyfelek számára. 

| Szolgáltatás | Standard | Dedikált |
| --- |:---:|:---:|
| Bandwidth | 20 átviteli egységek (legfeljebb 40 átviteli egységek) | 20 kapacitásegység használható |
| Névterek |  1 | 50 / Kapacitásegység |
| Event Hubs |  10 | Korlátlan |
| Belépő események | / Millió esemény kell fizetnie | Tartalmazza |
| Üzenet mérete | 1 millió bájt | 1 millió bájt |
| Partíciók | 40 névterenként | event hubs / 1024 / Kapacitásegység, 2000 |
| Felhasználói csoportok | Eseményközpont 20 | Nincs korlátozva az event hubs 1000 / Kapacitásegység, |
| Felügyelt kapcsolatok | 1000 tartalmazza | 100 K csomagban foglalt |
| Üzenetmegőrzés | 7 nap, 84 GB-os átviteli egység per tartalmazza | 90 nap, a 10 TB-ot foglalt / Kapacitásegység |
| Rögzítés | Fizetés / óra | Tartalmazza |

## <a name="how-to-onboard"></a>Hogyan vezethető

Dedikált megoldás előkészítése az önkiszolgáló felhasználói élményt előzetes verzióban érhető el, amelyekkel létrehozhat 1 CU-fürtök a következő régióban:
  - Közép-Kanada
  - Nyugat-Európa
  - USA középső régiója
  - USA keleti régiója
  - USA 2. keleti régiója
  - USA északi középső régiója
  - USA nyugati régiója

A Microsoft aktívan ad hozzá új régióban, de addig is, ha az elsődleges régió nem szerepel a listán, küldjön támogatási kérelmet a [Event Hubs-csapattal](https://ms.portal.azure.com/#create/Microsoft.Support) alatt *műszaki > az Event Hubs > kvóta > kérhetnek Dedikált Termékváltozat*. A dedikált terv sajátossága, hogy egy több gyakorlati Bevezetés az Event Hubs termékért felelős csoport a rugalmas üzembe helyezést az Ön számára megfelelő beolvasni a fog tapasztalni. 

## <a name="faqs"></a>Gyakori kérdések

#### <a name="what-can-i-achieve-with-a-cluster"></a>Mit érhet és egy fürt?

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

#### <a name="how-do-i-create-a-cluster-larger-than-1-cu"></a>Hogyan hozhatok létre egy fürtöt 1-nél nagyobb CU?

Az önkiszolgáló élményt előzetes kiadását kérheti a fürt vertikális felskálázása a fürt létrehozása után. Egy 1 CU fürt létrehozását követően vegye fel a kapcsolatot az Event Hubs-támogatás a bejelentés egy [támogatási kérelem](https://ms.portal.azure.com/#create/Microsoft.Support) alatt *műszaki > kvóta > méretezési felfelé vagy lefelé dedikált fürt méretezése irányuló kérelem*. A GA kiadás fogja tudni közvetlenül a portálon keresztül a fürt vertikális. 

#### <a name="can-i-scale-down-my-cluster"></a>Skálázhatom-e le a fürtöt?

A létrehozás után fürtök használatáért legalább 4 óra. Az önkiszolgáló élményt előzetes verzióként küldhet egy [támogatási kérést](https://ms.portal.azure.com/#create/Microsoft.Support) mellett az Event Hubs csapatának *műszaki > kvóta > méretezési felfelé vagy lefelé dedikált fürt méretezése irányuló kérelem*. Használatával csökkentheti a fürt a kérelem végrehajtásához legfeljebb 7 napig is eltarthat. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Hogyan működik a Geo-DR-fürt?

Geo-pár másik névtér alatt egy dedikált szintű fürtöt tartalmazó névtér alatt egy dedikált szintű fürtöt is. Nem javasoljuk egy dedikált szintű névteret, a standard szintű kínál, és mivel az átviteli sebességhatár nem kompatibilis, és emiatt a hibák a névtér párosítási. 


#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Áttelepíthetek egy dedikált szintű fürthöz tartoznak, a standard szintű névterek?
Hogy jelenleg nem támogatják az automatikus migrálási folyamat az event hubs-adatok áttelepíthető egy dedikált egy standard szintű névtérben. Fürtbe történő áttelepítéséhez egy dedikált szintű, javasoljuk a kiürítés minden olyan üzeneteket balra, a Standard szintű event hubs és és cserélje le a kapcsolati végpontok, amelyek a dedikált névtér.

## <a name="next-steps"></a>További lépések

Forduljon a Microsoft értékesítési képviselőjével vagy a Microsoft Support további részletes információkat Event Hubs dedikált kapacitást. Akkor is tudhat meg többet az Event Hubs tarifacsomagok kövesse az alábbi hivatkozásokat:

- [Event Hubs dedikált díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/). A Microsoft értékesítési képviselőjével vagy a Microsoft Support további részletes információkat Event Hubs dedikált kapacitást is kérhet segítséget.
- A [Event Hubs – gyakori kérdések](event-hubs-faq.md) díjszabási információkat tartalmaz, valamint választ ad néhány gyakori kérdés az Event Hubs szolgáltatásról.
