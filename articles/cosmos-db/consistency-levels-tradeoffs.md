---
title: Kompromisszumot kínál a különböző konzisztenciaszintet az Azure Cosmos DB rendelkezésre állás és teljesítmény
description: Az Azure Cosmos DB különböző konzisztenciaszintet rendelkezésre állás és teljesítmény kompromisszumot.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 2/13/2019
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 99b981e6b5c9bc56c10b0491474c0c8773291b7e
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56309199"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Kompromisszumok a konzisztencia, a rendelkezésre állás és a teljesítmény között 

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

- A fennmaradó konzisztenciaszintekről írási késése mindig garantáltan 99 százalékon legfeljebb 10 ezredmásodperc. Az SLA biztonsági Ez írási késése. Az átlagos írási késése, az 50. percentilis, akkor általában 5 ezredmásodperc vagy kisebb.

Előfordulhat, hogy az egyes Azure Cosmos-fiókok konfigurálása, erős konzisztencia számos régióban. Ebben az esetben az írási késése garantáltan legalább két alkalommal üzenetváltási időt (RTT) és 99 százalékon 10 ezredmásodperc. Az összes legtávolabbi két régiót közötti Körbejárási társítva az Azure Cosmos-fiók. Az a két, az Azure Cosmos-fiókjához társított legtávolabbi régiók közötti körbejárási megegyezik. Ez a beállítás jelenleg előzetes verzióban érhető el.

A pontos Körbejárási késés a jelzőfény sebesség távolság függvénye és az Azure hálózati topológia. Az Azure-hálózatok nem biztosít bármely késés SLA-k számára a két Azure-régiók közötti Körbejárási. Az Azure Cosmos-fiókja replikáció késése jelennek meg az Azure Portalon. Az Azure portal segítségével figyelheti a replikáció késése, amely a fiókhoz társított különböző régiók között.

## <a name="consistency-levels-and-throughput"></a>Konzisztenciaszintek és az átviteli sebesség

- Kérelemegység azonos számú a munkamenet, konzisztens előtag és végleges konzisztencia szintek adja meg körülbelül kétszer az olvasás átviteli sebességét az erős és a korlátozott frissesség képest.

- Egy adott típusú írási művelet, például az insert, replace, upsert és törlése a lemezírás teljesítménye, a kérelemegységek megegyezik az összes konzisztenciaszintek.

## <a id="rto"></a>Konzisztencia-szintek és az adatok tartóssága

Egy globálisan elosztott adatbázis-környezeten belül nincs közvetlen kapcsolat folytonosságát egy régióra kiterjedő szolgáltatáskimaradás konzisztencia szint és az adatok tartósságának között. Az üzletmenet folytonosságát biztosító terve kidolgozásakor kell tudni, mielőtt az alkalmazás a zavaró eseményeket követő teljes helyreállításának maximális elfogadható idő. Az alkalmazás teljes helyreállításához szükséges időt a helyreállítási időre vonatkozó célkitűzés (RTO) néven ismert. Emellett ismernie kell a leghosszabb az alkalmazás működését, Adatfrissítés elvesztése zavaró eseményeket követő helyreállítása során. Az adott időszakban, előfordulhat, hogy elfogadható frissítések helyreállításipont-célkitűzés (RPO) néven ismert.

A tábla közötti kapcsolat konzisztencia modellek és az adatok tartóssága régió széles körű leállás zajok mellett között kapcsolat határozza meg. Fontos megjegyezni, hogy az elosztott rendszerekben, még akkor is, erős konzisztencia, nem lehet nulla miatt a CAP-tétel RTO és RPO elosztott adatbázist. Ezért a további tudnivalókért lásd: [Azure Cosmos DB-ben konzisztenciaszintek](consistency-levels.md).

|**Régió(k)**|**Replikációs mód**|**Konzisztenciaszint**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Egy vagy több főkiszolgálós|Bármely Konzisztenciaszint|< 240 perc|< 1 hét|
|>1|Single Master|Session, Consistent Prefix, Eventual|< 15 perc|< 15 perc|
|>1|Single Master|Kötött elavulás|K &AMP; T|< 15 perc|
|>1|Multi-Master|Session, Consistent Prefix, Eventual|< 15 perc|0|
|>1|Multi-Master|Kötött elavulás|K &AMP; T|0|
|>1|Egy vagy több főkiszolgálós|Erős|0|< 15 perc|

K = "K"-verziók (frissítések) egy elem száma.
T "T" idő időtartam = a legutóbbi frissítés óta.

## <a name="next-steps"></a>További lépések

További információ a globális terjesztés, és kompromisszumot kínál a konzisztencia általános elosztott rendszerek. Lásd az alábbi cikkeket:

- [Konzisztencia kompromisszumot kínál a modern elosztott adatbázis-rendszerek terve](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
- [Magas rendelkezésre állás](high-availability.md)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
