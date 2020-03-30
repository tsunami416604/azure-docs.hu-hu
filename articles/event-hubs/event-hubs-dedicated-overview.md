---
title: Dedikált eseményközpontok áttekintése – Azure Event Hubs | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt a dedikált Azure Event Hubs, amely egybérlős telepítések eseményközpontok kínál.
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
ms.openlocfilehash: f67be1d31125b21048deca4d9cafcc76f4ffc3b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72516746"
---
# <a name="overview-of-event-hubs-dedicated"></a>Dedikált eseményközpontok áttekintése

*Az Event Hubs-fürtök egybérlős* telepítéseket kínálnak a legigényesebb streamelési igényekkel rendelkező ügyfelek számára. Ez az egybérlős ajánlat garantált99,99%-os SLA-val rendelkezik, és csak a dedikált tarifacsomagonként érhető el. Az Event Hubs-fürt másodpercenként több millió eseményt képes befeketésni garantált kapacitással és másodperc alatti késéssel. A dedikált fürtön belül létrehozott névterek és eseményközpontok tartalmazzák a standard ajánlat összes szolgáltatását és egyebeket, de a be- és információtér-korlátok nélkül. Emellett a népszerű [Event Hubs Capture](event-hubs-capture-overview.md) funkciót is tartalmazza további költségek nélkül, lehetővé téve az adatfolyamok automatikus kötegelését és naplózását az Azure Storage-ba vagy az Azure Data Lake-be. 

A fürtök kiépítésre és számlázásra **kapacitásegységek (CUs)** állnak, amely előre lefoglalt mennyiségű PROCESSZOR- és memóriaerőforrás. Fürthöz 1, 2, 4, 8, 12, 16 vagy 20 e-t vásárolhat. Az, hogy mennyit tud betölteni és streamelni CU-nként, számos tényezőtől függ, például a gyártók és a fogyasztók számától, a hasznos teher alakjából, a kiáramlási aránytól (további részletekért lásd az alábbi teljesítményeredményeket). 

> [!NOTE]
> Az összes Event Hubs-fürt alapértelmezés szerint Kafka-kompatibilis, és támogatja a Kafka-végpontokat, amelyeket a meglévő Kafka alapú alkalmazások használhatnak. A Kafka engedélyezése a fürtön nincs hatással a nem Kafka használati esetekre; nincs lehetőség, vagy le kell tiltani a Kafka-t egy fürtön.

## <a name="why-dedicated"></a>Miért dedikált?

A dedikált Event Hubs három lenyűgöző előnyt kínál a nagyvállalati szintű kapacitásra szoruló ügyfelek számára:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Az egybérlős bérleti díj garantálja a jobb teljesítményt

A dedikált fürt garantálja a teljes kapacitást, és akár gigabájtnyi streamelési adatokat is betud fogadni teljesen tartós tárolással és másodperc alatti késéssel a forgalom bármely sorozatának befogadása érdekében. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>Inkluzív és exkluzív hozzáférés a funkciókhoz 
A dedikált ajánlat olyan funkciókat tartalmaz, mint a Capture további költségek nélkül, valamint exkluzív hozzáférést biztosít a közelgő funkciókhoz, mint például a Bring Your Own Key (BYOK). A szolgáltatás is kezeli a terheléselosztás, operációs rendszer frissítései, biztonsági javítások és particionálás az ügyfél számára, így kevesebb időt töltenek az infrastruktúra karbantartása és több időt az ügyféloldali szolgáltatások kiépítése.  

#### <a name="cost-savings"></a>Költségmegtakarítás
Nagy bejövő forgalom (>100 átviteli egység) a fürt óránként lényegesen kevesebbe kerül, mint a standard ajánlatban összehasonlítható mennyiségű átviteli egység vásárlása.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Eseményközpontok dedikált kvóták és korlátok

Az Event Hubs dedikált ajánlat számlázása rögzített havi díj, legalább 4 óra használat. A dedikált szint a Standard csomag összes funkcióját kínálja, de vállalati méretezési kapacitással és korlátokkal az igényes számítási feladatokat igénylő ügyfelek számára. 

| Szolgáltatás | Standard | Dedikált |
| --- |:---:|:---:|
| Sávszélesség | 20 inus (legfeljebb 40 egészen más int) | 20 cus |
| Névterek |  1 | 50 CU-nként |
| Event Hubs |  10 névtérenként | 1000 névtérenként |
| Be- és be- és visszakozóesemények | Millió eseményenkénti fizetés | Tartalmazza |
| Üzenet mérete | 1 millió bájt | 1 millió bájt |
| Partíciók | 32 eseményközpontonként | 1024 eseményközpontonként |
| Felhasználói csoportok | 20 eseményközpontonként | Nincs korlátozás CU-nként, 1000 eseményközpontonként |
| Közvetített kapcsolatok | 1000-et beleértve, max. | 100 K-val és max. |
| Üzenetek megőrzése | 7 nap, 84 GB tu-nként | 90 nap, 10 TB cu-nként |
| Rögzítés | Fizetés óránként | Tartalmazza |

## <a name="how-to-onboard"></a>Hogyan kell a fedélzeten

Az Azure Portalon keresztül idítendő önkiszolgáló élmény, amely az [Azure Portalon](https://aka.ms/eventhubsclusterquickstart) keresztül [hoz létre egy Event Hubs-fürtöt,](event-hubs-dedicated-cluster-create-portal.md) előzetes verzióban érhető el. Ha bármilyen kérdése van, vagy segítségre van szüksége a dedikált Eseményközpontokba való bevezetéshez, kérjük, lépjen kapcsolatba az [Event Hubs csapatával.](mailto:askeventhubs@microsoft.com)

## <a name="faqs"></a>Gyakori kérdések

#### <a name="what-can-i-achieve-with-a-cluster"></a>Mit érhetek el egy fürttel?

Az Event Hubs-fürtök esetében a betöltés és a streamelés mértéke különböző tényezőktől függ, például a gyártóktól, a fogyasztóktól, a betöltés és feldolgozás sebességétől és még sok mástól. 

Az alábbi táblázat a tesztelés során elért teljesítményeredményeket mutatja be:

| Hasznos teher alakja | Vevők | Be- és be- és visszasiklósávszélesség| Be- és be- és visszaküldött üzenetek | Kimenő sávszélesség | Kimenő üzenetek | Teljes Tosz | Tous per CU |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1 KB-os kötegek | 2 | 400 MB/mp | 400 ezer üzenet/mp | 800 MB/mp | 800 ezer üzenet/mp | 400 tosz | 100 tosz | 
| 10x10 KB-os kötegek | 2 | 666 MB/mp | 66,6 ezer üzenet/mp | 1,33 GB/mp | 133 ezer üzenet/mp | 666 Tosz | 166 Tosz |
| 6x32 KB-os kötegek | 1 | 1,05 GB/mp | 34 ezer üzenet / mp | 1,05 GB/mp | 34 ezer üzenet/mp | 1000 tosz | 250 Tosz |

A vizsgálat során a következő kritériumokat alkalmazták:

- Egy dedikált szintű Event Hubs-fürt öt kapacitásegységgel (CUs) használatos. 
- A betöltéshez használt eseményközpont 200 partícióval volt. 
- A betöltött adatokat két fogadóalkalmazás fogadta az összes partícióról.

#### <a name="can-i-scale-updown-my-cluster"></a>Skálázható fel/le a fürt?

A létrehozás után a fürtöknek legalább 4 órányi használatért kell fizetniük. Az önkiszolgálás előzetes kiadásában [támogatási kérelmet](https://ms.portal.azure.com/#create/Microsoft.Support) küldhet az Event Hubs csapatnak a *Technikai > kvóta > a dedikált fürt skálázására vagy leskálázásra vonatkozó kérése* a fürt fel- vagy leskálázása keretében a fürt fel- vagy leskálázása érdekében. A fürt leskálázási kérésének teljesítése akár 7 napot is igénybe vehet. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Hogyan működik a Geo-DR a fürtömmel?

A névtér földrajzi párosítása egy dedikált rétegű fürt alatt egy másik névtérrel egy dedikált szintű fürt alatt. Nem javasoljuk a dedikált szintű névtér és a névtér párosítását a standard ajánlatban, mivel az átviteli korlát nem lesz kompatibilis, ami hibákat eredményez. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Áttelepíthetem a standard névtereimet, hogy egy dedikált rétegű fürthöz tartozzanak?
Jelenleg nem támogatjuk az automatikus áttelepítési folyamat ot az eseményközpontok adatainak standard névtérből egy dedikáltba történő áttelepítéséhez. 

## <a name="next-steps"></a>További lépések

Lépjen kapcsolatba a Microsoft értékesítési képviselőjével vagy a Microsoft támogatási szolgálatával, hogy további részleteket kapjon a Dedikált Eseményközponttal kapcsolatban. Fürtöt is létrehozhat, vagy többet is megtudhat az Event Hubs tarifacsomagjairól az alábbi hivatkozásokon:

- [Eseményközpontok fürtjének létrehozása az Azure Portalon keresztül](https://aka.ms/eventhubsclusterquickstart) 
- [Eseményközpontok dedikált árképzés .](https://azure.microsoft.com/pricing/details/event-hubs/) A Microsoft értékesítési képviselőjével vagy a Microsoft támogatási szolgálatával kapcsolatos további részletekért kaphat további részleteket az Event Hubs dedikált kapacitásáról.
- Az [Event Hubs GYIK](event-hubs-faq.md) díjszabási információkat tartalmaz, és választ ad az Event Hubokkal kapcsolatos gyakori kérdésekre.
