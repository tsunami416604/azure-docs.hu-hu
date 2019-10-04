---
title: Dedikált event hubs – Azure Event Hubs áttekintése |} A Microsoft Docs
description: Ez a cikk áttekintést nyújt a dedikált Azure Event Hubsről, amely az Event hub egybérlős üzembe helyezését kínálja.
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
ms.openlocfilehash: ebc6dd672fd180e22cc1edf5c9978e0985427e50
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991857"
---
# <a name="overview-of-event-hubs-dedicated"></a>Dedikált Event Hubs áttekintése

*Event Hubs-fürtök* egybérlős üzemelő példányokat biztosítanak a legigényesebb folyamatos átviteli igényű ügyfelek számára. Ez az egybérlős ajánlat 99,99%-os szolgáltatásiszint-szerződéssel jár, és csak dedikált tarifacsomagunkban érhető el. Egy Event Hubs fürt másodpercenként több millió eseményt tud befogadni a garantált kapacitással és a másodperces késleltetéssel. A dedikált fürtön belül létrehozott névterek és az Event hubok tartalmazzák a standard ajánlat összes funkcióját, többek között a bejövő korlátok nélkül. Emellett a népszerű [Event Hubs Capture](event-hubs-capture-overview.md) szolgáltatást díjmentesen ingyenesen használhatja, így automatikusan kötegelt és naplózhatja az adatstreameket az Azure Storage-ba vagy Azure Data Lakeba. 

A fürtök kiépítése és kiszámlázása **Kapacitási egységek (ke)** alapján történik, a processzor-és memória-erőforrások előre lefoglalt mennyisége. Minden fürthöz 1, 2, 4, 8, 12, 16 vagy 20 ke lehet vásárolni. A betöltés és az adatfolyam-továbbítás mennyisége számos tényezőtől függ, például a termelők és a felhasználók számától, a hasznos adatok méretétől, a kimenő forgalom szintjétől (további részletekért lásd az alábbi teljesítményteszt-eredményeket). 

> [!NOTE]
> Az összes Event Hubs-fürt alapértelmezetten a Kafka-kompatibilis, és támogatja a Kafka-végpontokat, amelyeket a meglévő Kafka-alapú alkalmazások használhatnak. Ha a Kafka engedélyezve van a fürtön, nem érinti a nem Kafka használati eseteit; nincs lehetőség, vagy le kell tiltania a Kafka-t a fürtön.

## <a name="why-dedicated"></a>Miért dedikált?

A dedikált Event Hubs három vonzó előnyt kínál a nagyvállalati szintű kapacitást igénylő ügyfelek számára:

#### <a name="single-tenancy-guarantees-capacity-for-better-performance"></a>Az egybérlős kapacitás a jobb teljesítmény érdekében

A dedikált fürt teljes skálán garantálja a kapacitást, és akár GB-ig terjedő adatfolyam-továbbítási adatokat tud biztosítani teljes mértékben tartós tárolással és almásodperces késéssel, hogy bármilyen adatforgalomban bejárható legyen. 

#### <a name="inclusive-and-exclusive-access-to-features"></a>A funkciókhoz tartozó és kizárólagos hozzáférés 
A dedikált ajánlat olyan funkciókat tartalmaz, mint például a capture díjmentesen, valamint a következő funkciókhoz való kizárólagos hozzáférés (például Bring Your Own Key (BYOK)). A szolgáltatás a terheléselosztást, az operációs rendszer frissítéseit, a biztonsági javításokat és a particionálást is kezeli az ügyfél számára, így kevesebb időt kell fordítani az infrastruktúra karbantartására és az ügyféloldali funkciók kiépítésére.  

#### <a name="cost-savings"></a>Költségmegtakarítás
A nagy forgalmú köteteken (> 100 TUs) a fürt óradíja jóval kevesebb, mint a standard ajánlatban található hasonló mennyiségű átviteli egység megvásárlása.


## <a name="event-hubs-dedicated-quotas-and-limits"></a>Kvóták és korlátozások dedikált Event Hubs

Az dedikált Event Hubs ajánlat számlázása rögzített havi díjszabással történik, amely legalább 4 órányi használatot biztosít. A dedikált szint a standard csomag összes funkcióját felkínálja, de nagyvállalati kapacitást és korlátokat biztosít az ügyfelek számára igényes számítási feladatokkal. 

| Funkció | Standard | Dedikált |
| --- |:---:|:---:|
| A sávszélesség | 20 TUs (legfeljebb 40 TUs) | 20 ke |
| Névterek |  1 | 50/CU |
| Event Hubs |  10/névtér | 1000/névtér |
| Belépő események | / Millió esemény kell fizetnie | Tartalmazza |
| Üzenet mérete | 1 000 000 bájt | 1 000 000 bájt |
| Partíciók | 40/névtér | 2000/CU |
| Felhasználói csoportok | 20/Event hub | Az Event hub-ban nincs korlát/CU, 1000 |
| Felügyelt kapcsolatok | 1 000 tartalmazott, 5 000 Max | 100 kb és max. |
| Üzenetmegőrzés | 7 nap, 84 GB/TU | 90 nap, 10 TB tartalmaz/CU |
| Rögzítés | Fizetés / óra | Tartalmazza |

## <a name="how-to-onboard"></a>Hogyan vezethető

A [Event Hubs-fürt](event-hubs-dedicated-cluster-create-portal.md) [Azure Portalon](https://aka.ms/eventhubsclusterquickstart) keresztüli létrehozásának önkiszolgáló felülete mostantól előzetes verzióban érhető el. Ha bármilyen kérdése van, vagy segítségre van szüksége a dedikált Event Hubs bevezetéséhez, forduljon a [Event Hubs csapatához](mailto:askeventhubs@microsoft.com).

## <a name="faqs"></a>Gyakori kérdések

#### <a name="what-can-i-achieve-with-a-cluster"></a>Mit lehet elérni egy fürttel?

Egy Event Hubs-fürt esetében a betöltés és az adatfolyam mennyisége a különböző tényezőktől, például a termelőktől, a fogyasztóktól, a betöltés és a feldolgozástól, valamint sok más tényezőtől függ. 

Következő táblázatban a teljesítményteszt eredménye, hogy mi érhető el a tesztelés során:

| Hasznos adat alakzat | Fogadók | Bejövő sávszélesség| Bejövő üzenetek | Kimenő adatforgalmat | Kimenő üzenetek | Teljes átviteli egységek | Átviteli egységek / Kapacitásegység |
| ------------- | --------- | ---------------- | ------------------ | ----------------- | ------------------- | --------- | ---------- |
| 100x1KB váró | 2 | 400 MB/mp | 400k üzenetek/mp | 800 MB/mp | 800k üzenetek/mp | 400 átviteli egységek | 100 átviteli egységek | 
| 10x10KB váró | 2 | 666 MB/mp | 66.6 üzenet/mp | 1,33 GB/mp | 133k üzenetek/mp | 666 átviteli egységek | 166 átviteli egységek |
| 6x32KB váró | 1 | 1.05 GB/mp | 34k üzenetek/mp | 1.05 GB/mp | 34k üzenetek/mp | 1000 átviteli egységek | 250 átviteli egységek |

A vizsgálat során, a következő feltételek lett megadva:

- A dedikált rétegbeli Event Hubs-fürt négy kapacitású egységgel (ke) lett használva. 
- Az event hubs streamfeldolgozási használt 200 partíciók rendelkezett. 
- Volt betöltött adatokat fogad az összes partíció két fogadó alkalmazás kapott.

#### <a name="can-i-scale-updown-my-cluster"></a>Felskálázás a fürtön?

A létrehozást követően a fürtök számlázása legalább 4 órányi használat után történik. Az önkiszolgáló élmény előzetes kiadásában egy [támogatási kérést](https://ms.portal.azure.com/#create/Microsoft.Support) küldhet a Event Hubs csapatnak a *technikai > kvóta > kérelem a dedikált fürt* vertikális felskálázásához vagy méretezéséhez a fürt fel-vagy leskálázásához. Akár 7 napig is eltarthat a fürt skálázására irányuló kérelem teljesítése. 

#### <a name="how-will-geo-dr-work-with-my-cluster"></a>Hogyan működik majd a Geo-DR a fürttel?

Egy dedikált rétegbeli fürthöz tartozó névteret egy dedikált rétegbeli fürt egy másik névterével is megadhat. Nem javasoljuk, hogy egy dedikált szintű névteret a standard ajánlatban található névtérrel párosítson, mivel az átviteli sebesség korlátja inkompatibilis lesz, ami hibákat eredményezhet. 

#### <a name="can-i-migrate-my-standard-namespaces-to-belong-to-a-dedicated-tier-cluster"></a>Áttelepíthetem a standard névtereket, hogy egy dedikált rétegbeli fürthöz tartozzanak?
Jelenleg nem támogatunk egy automatizált áttelepítési folyamatot, amely az Event hub-adatok standard névtérből egy Dedikáltra történő áttelepítését végzi. 

## <a name="next-steps"></a>További lépések

A dedikált Event Hubs további részleteiért forduljon a Microsoft értékesítési képviselőjéhez vagy a Microsoft ügyfélszolgálatahoz. Létrehozhat egy fürtöt is, vagy további információkat kaphat Event Hubs díjszabási szintjeiről az alábbi hivatkozásokra kattintva:

- [Event Hubs-fürt létrehozása az Azure Portalon](https://aka.ms/eventhubsclusterquickstart) 
- [Event Hubs dedikált díjszabás](https://azure.microsoft.com/pricing/details/event-hubs/). A Microsoft értékesítési képviselőjével vagy a Microsoft Support további részletes információkat Event Hubs dedikált kapacitást is kérhet segítséget.
- A [Event Hubs – gyakori kérdések](event-hubs-faq.md) díjszabási információkat tartalmaz, valamint választ ad néhány gyakori kérdés az Event Hubs szolgáltatásról.
