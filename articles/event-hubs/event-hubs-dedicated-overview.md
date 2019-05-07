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
ms.openlocfilehash: e8a2d8321a42e8b3d090c1ce1fdb3fd9a7ee3714
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65138662"
---
# <a name="overview-of-event-hubs-dedicated"></a>Dedikált Event Hubs áttekintése

Az Azure Event Hubs-fürtök egybérlős központi telepítések a legnagyobb erőforrás-igényű streamelési igényekkel rendelkező ügyfelek számára kínálnak. Egybérlős ajánlatról garantált 99,99 %-os szolgáltatásiszint-szerződés tartozik. Ez csak a dedikált tarifacsomag érhető el.

Az Event Hubs-fürt a garantált és subsecond késése másodpercenként több millió bejövő forgalom is. A dedikált fürtön belül létrehozott névterek és az event hubs és a Standard ajánlat, de minden bejövő forgalom korlátok nélkül az összes funkciókat tartalmaznak. Ezenkívül tartalmazza a [Event Hubs Capture](event-hubs-capture-overview.md) szolgáltatást további költségek nélkül. Automatikusan batch és a naplófájlok adatstreameket Azure Storage vagy az Azure Data Lake használhatja azt.

Dedikált fürtök kiépítése és számlázás (CUs) egységek alapján történik. Kapacitásegységek egy előzetesen lefoglalt CPU és memória-erőforrások mennyiségét. 1, 2, 4, 8, 12, 16 vagy 20 kapacitásegység használható, ha mindegyik fürthöz is vásárolhat. Mennyi betöltheti az és adatfolyam / Kapacitásegység olyan tényezőktől függ, például az előállítók és fogyasztók, a hasznos adat alakzat és a kilépő üzenetek gyakorisága számát.

További információkért tekintse meg a teljesítményteszt eredményeket tartalmazó tábla.

## <a name="why-use-event-hubs-dedicated"></a>Miért érdemes használni az Event Hubs dedikált?

Event Hubs dedikált három előnyöket kínál a felhasználóknak készült, akik vállalati szintű kapacitásra van szüksége.

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Single-bérlős garantálja a kapacitás, a jobb teljesítmény érdekében

Egy dedikált fürt garantálja a kapacitás teljes méretben. Streamelési adatok befogadásához szolgáltatás a forgalom teljes mértékben tartós tárolási és subsecond késéssel gigabájt legfeljebb bejövő képes kezelni.

#### <a name="inclusive-and-exclusive-access-to-features"></a>Funkciók között lehet és kizárólagos hozzáférést 
A dedikált ajánlat magában foglalja a szolgáltatások, mint az rögzítési további költségek nélkül. Később megjelenő funkciók, például a BYOK exkluzív hozzáférést is kínál. A szolgáltatás kezeli a terheléselosztást, az operációs rendszer frissítések, biztonsági javítások és a particionálás. Ezekkel a lehetőségekkel kevesebb időt beváltható az infrastruktúra-karbantartási és több időt ügyféloldali funkciók.

#### <a name="cost-savings"></a>Költségmegtakarítás
Bejövő nagy mennyiségben egy fürt költségei jelentősen kevesebb, mint ha óránként megvásárlása átviteli egységek (átviteli egységek) hasonló mennyiségű a Standard ajánlat. Van egy nagy mennyiségű > 100 átviteli egységek.


## <a name="event-hubs-standard-vs-dedicated"></a>Event Hubs Standard vs. Dedikált

Az alábbi táblázat hasonlítja össze az elérhető az Event Hubs szolgáltatásszintjét. Az Event Hubs dedikált ajánlat fix havi díja képest használati a legtöbb funkciót a Standard díjszabás szerint történik. A dedikált szintet kínál a standard szintű csomag, de az erőforrás-igényű számítási feladatok rendelkező ügyfelek számára a nagyvállalati szintű kapacitással rendelkező összes funkciót.

| Szolgáltatás | Standard | Dedikált |
| --- |:---:|:---:|
| Belépő események | / Millió esemény kell fizetnie | Tartalmazza |
| Kapacitásegység (1 MB/s belépő, 2 MB/s kilépő) | Fizetés / óra | Tartalmazza |
| Üzenet mérete | 1 MB | 1 MB |
| Partíciók | 40 névterenként | 2000 / Kapacitásegység |
| Felhasználói csoportok | eseményközpont 20 | 1000 event hubs kiszolgálónként |
| Maximális sávszélesség | 20 átviteli egységek (legfeljebb 40 átviteli egységek) | 20 kapacitásegység használható |
| Felügyelt kapcsolatok | 1000 tartalmazza | 100 000 tartalmazza |
| Üzenetmegőrzés | Egy ingyenes nap | Legfeljebb 7 napot tartalmaz |
| Rögzítés | Fizetés / óra | Tartalmazza |

## <a name="what-can-i-achieve-with-a-cluster"></a>Mit érhet és egy fürt?

Az Event Hubs-fürt mennyi fogadni és adatfolyam-attól függ, a gyártó, a felhasználók, a sebesség, amellyel fogadni és feldolgozni és még sok más.

Az alábbi táblázat a teljesítménytesztek eredménye, hogy a tesztelés során elért is.

| Hasznos adat alakzat | Fogadók | Bejövő sávszélesség| Bejövő üzenetek | Kimenő adatforgalmat | Kimenő üzenetek | Teljes átviteli egységek | Átviteli egységek / Kapacitásegység |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB váró | 2 | 400 MB/mp | 400 000 üzenet/mp | 800 MB/mp | 800 000 üzenetek/s | 400 átviteli egységek | 100 átviteli egységek | 
| 10x10KB váró | 2 | 666 MB/mp | 66,600 üzenetek/s | 1,33 GB/mp | 133,000 üzenetek/s | 666 átviteli egységek | 166 átviteli egységek |
| 6x32KB váró | 1 | 1.05 GB/mp | 34 000 üzenetek/s | 1.05 GB/mp | 34 000 üzenetek/s | 1000 átviteli egységek | 250 átviteli egységek |

A vizsgálat során, a következő feltételek használták:

- Az Event Hubs dedikált szintű fürtök négy kapacitásegységek lett megadva.
- Az event hubs streamfeldolgozási használt 200 partíciók rendelkezett.
- Két fogadó alkalmazás lett betöltött adatok kapott. Az összes partíció adatokat kapott azokat.

## <a name="use-event-hubs-dedicated"></a>Az Event Hubs dedikált használata

Event Hubs dedikált használandó [számlázási támogatásától](https://ms.portal.azure.com/#create/Microsoft.Support) vagy a Microsoft helyi képviselőjéhez. Felfelé és lefelé az igényeinek hozzáadásával vagy eltávolításával CUs a hónap során is méretezheti a kapacitást. Az Event Hubs termékért felelős csoport segít a rugalmas üzembe helyezést az Ön számára megfelelő beolvasása.

## <a name="next-steps"></a>További lépések

Forduljon a Microsoft értékesítési képviselőjével vagy a Microsoft Support Event Hubs dedikált kapacitás további információt szeretne kapni. További tarifacsomagok az Event Hubs szolgáltatásról, használja az alábbi hivatkozásokat:

- [Event Hubs dedikált díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/). Az Event Hubs dedikált kapacitás további információt szeretne kapni a Microsoft értékesítési képviselőjével vagy Support is kérhet segítséget.
- A [Event Hubs – gyakori kérdések](event-hubs-faq.md) díjszabási információkat tartalmaz, valamint választ ad néhány gyakori kérdés az Event Hubs szolgáltatásról.
