---
title: Kompromisszumot kínál a különböző konzisztenciaszintet az Azure Cosmos DB rendelkezésre állás és teljesítmény
description: Az Azure Cosmos DB különböző konzisztenciaszintet rendelkezésre állás és teljesítmény kompromisszumot.
keywords: konzisztencia, teljesítmény, az azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: af17815d2dcf36909ba9b2109f0f9939c79508c0
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52848697"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels-in-azure-cosmos-db"></a>Kompromisszumot kínál a különböző konzisztenciaszintet az Azure Cosmos DB rendelkezésre állás és teljesítmény

Elosztott adatbázisok magas rendelkezésre állás, alacsony késleltetésű, illetve mindkettőt replikációs támaszkodó kell kompromisszumokat kötni. A kompromisszumot kínál a olvasás következetes és rendelkezésre állás, a késés és az átviteli sebesség között kell lennie. 

Az Azure Cosmos DB választási lehetőségek egy átfogó, az adatkonzisztencia közelíti meg. Ez a megközelítés magában foglalja a több lehetőség a két szélsőséges erős és végleges konzisztencia. A konzisztencia spektrum öt jól definiált modellek közül választhat. A leggyengébb, felé a modellek a következők:

- Erős 
- Korlátozott frissesség 
- Munkamenet 
- Konzisztens előtag 
- Végleges 

Minden modell rendelkezésre állás és teljesítmény kompromisszumot kínál, és átfogó SLA-je támogat.

## <a name="consistency-levels-and-latency"></a>Konzisztenciaszintek és késés

- Az olvasási késés az összes konzisztenciaszintek mindig garantáltan 99 százalékon legfeljebb 10 ezredmásodperc. Az olvasási késés alapját a szolgáltatói szerződés. Az átlagos olvasási késés, az 50. percentilis, a rendszer általában 2 ezredmásodperc vagy kisebb. Az Azure Cosmos-fiókok, amelyek span számos régióban, és megtörténik az erős konzisztencia a garancia kivételt jelentenek.

-  A fennmaradó konzisztenciaszintekről írási késése mindig garantáltan 99 százalékon legfeljebb 10 ezredmásodperc. Az SLA biztonsági Ez írási késése. Az átlagos írási késése, az 50. percentilis, akkor általában 5 ezredmásodperc vagy kisebb.

Előfordulhat, hogy az egyes Azure Cosmos-fiókok konfigurálása, erős konzisztencia számos régióban. Ebben az esetben az írási késése garantáltan legalább két alkalommal üzenetváltási időt (RTT) és 99 százalékon 10 ezredmásodperc. Az összes legtávolabbi két régiót közötti Körbejárási társítva az Azure Cosmos-fiók. Az a két, az Azure Cosmos-fiókjához társított legtávolabbi régiók közötti körbejárási megegyezik. Ez a beállítás jelenleg előzetes verzióban érhető el. 

A pontos Körbejárási késés a jelzőfény sebesség távolság függvénye és az Azure hálózati topológia. Az Azure-hálózatok nem biztosít bármely késés SLA-k számára a két Azure-régiók közötti Körbejárási. Az Azure Cosmos-fiókja replikáció késése jelennek meg az Azure Portalon. Az Azure portal segítségével figyelheti a replikáció késése, amely a fiókhoz társított különböző régiók között.

## <a name="consistency-levels-and-throughput"></a>Konzisztenciaszintek és az átviteli sebesség

- Kérelemegység azonos számú a munkamenet, konzisztens előtag és végleges konzisztencia szintek adja meg körülbelül kétszer az olvasás átviteli sebességét az erős és a korlátozott frissesség képest.

- Egy adott típusú írási művelet, például az insert, replace, upsert és törlése a lemezírás teljesítménye, a kérelemegységek megegyezik az összes konzisztenciaszintek.

## <a name="next-steps"></a>További lépések

További információ a globális terjesztés, és kompromisszumot kínál a konzisztencia általános elosztott rendszerek. Lásd az alábbi cikkeket:

* [Konzisztencia kompromisszumot kínál a modern elosztott adatbázis-rendszerek terve](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Magas rendelkezésre állás](high-availability.md)
* [Az Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
