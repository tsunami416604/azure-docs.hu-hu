---
title: Rendelkezésre állási és teljesítménybeli kompromisszumok a Azure Cosmos DB különböző konzisztenciáji szintjeihez
description: Rendelkezésre állási és teljesítménybeli kompromisszumok a Azure Cosmos DB különböző konzisztenciáji szintjeihez.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 2d80e291b3c054fec92b169c8a216a7189e24b79
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384196"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Kompromisszumok a konzisztencia, a rendelkezésre állás és a teljesítmény között 

A magas rendelkezésre állást, az alacsony késést vagy mindkettőt a replikálásra támaszkodó elosztott adatbázisoknak kompromisszumokat kell tenniük. A kompromisszumok az olvasási konzisztencia és a rendelkezésre állás, a késés és az átviteli sebesség között vannak.

A Azure Cosmos DB az adatkonzisztencia a választási lehetőségek spektrumát közelíti meg. Ez a megközelítés több lehetőséget is kínál, mint az erős és a végleges konzisztencia két véglete. Öt jól definiált modell közül választhat a konzisztencia-spektrumon. A modellek a legerősebbtól a leggyengébbig a következők:

- *Erős*
- *Korlátozott frissesség*
- *Munkamenet*
- *Konzisztens előtag*
- *Végleges*

Az egyes modellek rendelkezésre állási és teljesítménybeli kompromisszumokat biztosítanak, és átfogó SLA-kat támogatnak.

## <a name="consistency-levels-and-latency"></a>Konzisztencia-szintek és késés

Az összes konzisztencia-érték olvasási késése mindig garantáltan 10 ezredmásodpercnél kisebb a esetek 99% percentilis esetében. Ezt az olvasási késést az SLA támogatja. Az átlagos olvasási késés az 50%-os százalékos értéknél általában 2 ezredmásodperc vagy kevesebb. A számos régióra kiterjedő Azure Cosmos-fiókok, amelyek erős konzisztencia-konfigurációval rendelkeznek, kivételt jelentenek ez a garancia.

Az összes konzisztencia-érték írási késése mindig garantáltan 10 ezredmásodpercnél kisebb a esetek 99% percentilis esetében. Ezt az írási késést az SLA támogatja. Az átlagos írási késleltetés 50%-os százalékban általában 5 ezredmásodperc vagy kevesebb.

Az olyan Azure Cosmos-fiókok esetében, amelyeknek több régióval való erős konzisztencia van beállítva, az írási késés garantált, hogy a két legtávolabbi régió bármelyike, illetve 10 ezredmásodperc alatt a esetek 99% percentilis esetében kevesebb, mint kétszerese (RTT).

A pontos RTT késés a fénysebességi távolság és az Azure hálózati topológia függvénye. Az Azure Networking nem biztosít késési SLA-kat a két Azure-régió közötti RTT. Az Azure Cosmos-fiók esetében a replikációs késések megjelennek a Azure Portal. A Azure Portal (ugrás a metrikák panelre) segítségével figyelheti az Azure Cosmos-fiókjához társított különböző régiók közötti replikációs késéseket.

## <a name="consistency-levels-and-throughput"></a>Konzisztencia-szintek és átviteli sebesség

- Azonos számú kérelem esetén a munkamenet, a konzisztens előtag és a végleges konzisztencia-szintek az olvasási sebességnek az erős és a határos elavulás összevetésével való összevetéséhez képest két alkalommal is elérhetők.

- Egy adott írási művelet (például INSERT, replace, upsert és DELETE) esetében az írási sebesség a kérelmek egységeinek esetében azonos minden konzisztencia-szinten.

## <a id="rto"></a>A konzisztencia szintjei és az adattartósság

Egy globálisan elosztott adatbázis-környezeten belül közvetlen kapcsolat áll fenn a konzisztencia szintje és az adattartósság között egy adott régióra kiterjedő leállás esetén. Az üzletmenet-folytonossági terv kidolgozása során meg kell ismernie a maximális elfogadható időtartamot, mielőtt az alkalmazás teljesen helyreállít egy zavaró esemény után. Az alkalmazás teljes helyreállításához szükséges idő a **helyreállítási időre vonatkozó célkitűzés** (**RTO**). Azt is meg kell ismernie, hogy a legutóbbi adatfrissítések maximális időtartama alatt az alkalmazás elveszítheti a zavaró események utáni helyreállítást. A frissítések elvesztésének időpontját a **helyreállítási pont célkitűzésének** (**RPO**) nevezzük.

Az alábbi táblázat a konzisztencia-modell és az adattartósság közötti kapcsolatot határozza meg a régiók széles kimaradásának jelenlétében. Fontos megjegyezni, hogy egy elosztott rendszeren, még erős konzisztencia esetén is előfordulhat, hogy a CAP-tétel miatt nem lehet RPO és nulla RTO rendelkező elosztott adatbázis. Ha többet szeretne megtudni arról, hogy miért, tekintse meg [a Azure Cosmos db egységességi szintjei](consistency-levels.md)című témakört.

|**Régió (k)**|**Replikálási mód**|**Konzisztenciaszint**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Egy vagy több főkiszolgáló|Bármely konzisztencia-szint|< 240 perc|< 1 hét|
|>1|Egyetlen főkiszolgáló|Munkamenet, konzisztens előtag, végleges|< 15 perc|< 15 perc|
|>1|Egyetlen főkiszolgáló|Kötött elavulás|*K* & *T*|< 15 perc|
|>1|Egyetlen főkiszolgáló|Erős|0|< 15 perc|
|>1|Multi-Master|Munkamenet, konzisztens előtag, végleges|< 15 perc|0|
|>1|Multi-Master|Kötött elavulás|*K* & *T*|0|

*K* = egy elem *"k"* verziója (azaz frissítései) száma.

*T* = a legutóbbi frissítés óta eltelt idő *"t"* .

## <a name="next-steps"></a>További lépések

Tudjon meg többet az elosztott rendszerek globális eloszlásáról és az általános konzisztencia-kompromisszumokról. Lásd az alábbi cikkeket:

- [Konzisztencia-kompromisszumok a modern elosztott adatbázis-rendszerek kialakításában](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Magas rendelkezésre állás](high-availability.md)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
