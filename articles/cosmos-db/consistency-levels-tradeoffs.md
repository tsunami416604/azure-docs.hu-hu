---
title: Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény |} A Microsoft Docs
description: Az Azure Cosmos DB különböző konzisztenciaszintet rendelkezésre állás és teljesítmény kompromisszumot.
keywords: konzisztencia, teljesítmény, az azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: 061a7c223d0e1c6fa7384d7defe9f84f470236ce
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244072"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels"></a>Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény

Elosztott adatbázisok hagyatkoznia a magas rendelkezésre állás, alacsony késleltetésű, illetve mindkettőt, replikációs győződjön availability1, latency2 és az átviteli sebesség és az olvasás következetes alapvető magával. Az Azure Cosmos DB álló kínálat az erős és végleges konzisztencia a két szélsőséges helyett lehetőségek, az adatkonzisztencia közelíti meg. A cosmos DB segítségével a fejlesztők számára, hogy választhat a konzisztencia (legerősebb leggyengébb felé haladva) – színspektrumból öt jól definiált konzisztenciamodellekkel **erős**, **korlátozott frissesség**, **munkamenet** , **konzisztens előtag**, és **végleges**. Az öt konzisztenciamodell mindegyike egyértelmű rendelkezésre állását és teljesítményét és kompromisszumot kínál a biztosít, és átfogó SLA-k élvezik.

## <a name="consistency-levels-and-latency"></a>Konzisztenciaszintek és késés

- A **olvasási késés** az összes konzisztenciaszintek mindig garantáltan 99 százalékon legfeljebb 10 ezredmásodperc, és az SLA-je támogat. (A az 50. percentilis) az átlagos olvasási késése általában 2 ezredmásodperc vagy kisebb.
- Cosmos-fiókok átfedés számos régióban, kivéve, és konfigurálta az erős konzisztencia, a **írási késés** az összes konzisztenciaszintek mindig garantáltan 99 százalékon legfeljebb 10 ezredmásodperc és alapját a SLA-T. A (a az 50. percentilis), az átlagos írási késést általában 5 ezredmásodperc vagy kisebb.
- Az erős konzisztencia (jelenleg előzetes verzióban érhető el), a konfigurált számos régióban Cosmos-fiókok a **írási késés** garantáltan kevesebb mint < (2 * RTT) + 99 százalékon 10 ezredmásodperc. A RTT a legtávolabbi bármely, a Cosmos-fiókjához társított két régió között. A pontos Körbejárási késés a pontos Azure hálózati topológiáját és sebessége, kis távolság függvénye. Az Azure-hálózatok nem biztosít bármely késés SLA-k számára a két Azure-régiók közötti Körbejárási. Cosmos DB replikáció késése Cosmos-fiókja, hogy felmérhesse a replikáció késése-Cosmos-fiókjához hozzárendelt különböző régiók közötti Azure-portálon jelennek meg.

## <a name="consistency-levels-and-throughput"></a>Konzisztenciaszintek és az átviteli sebesség

- RU akkora munkamenet, konzisztens előtag és végleges konzisztencia adja meg körülbelül 2 X olvasási teljesítménye, erős és a korlátozott frissesség képest.
- Írási művelet (például Insert, Replace, Upsert, törlés stb.) egy adott típusú a lemezírás teljesítménye kérelemegység megegyezik az összes konzisztenciaszintek.

## <a name="consistency-levels-and-durability"></a>Konzisztenciaszintek és tartóssága

Írási művelet arra vonatkozik, az ügyfél számára, mielőtt az adatokat a tartósan véglegesítve lettek a szerint a kvórum replikák írást fogadja a régión belül. Továbbá ha a tároló az egységes indexelési szabályzat van konfigurálva, az index is szinkron módon frissítve, replikálja, és tartósan véglegesítve lettek a kvórum replikák által az írási arra vonatkozik, az ügyfél előtt. 

A következő táblázat összefoglalja a potenciális adatvesztés ablakában regionális katasztrófa számos régióban átfedés Cosmos-fiókok.

| **Konzisztenciaszint** | **Regionális katasztrófa esetleges adatvesztés ablakba.** |
| - | - |
| Erős | Nulla |
| Kötött elavulás | A Cosmos-fiók a fejlesztő által konfigurált "frissesség ablakban" korlátozódik. |
| Munkamenet | Legfeljebb 5 másodperc |
| Konzisztens előtag | Legfeljebb 5 másodperc |
| Végleges | Legfeljebb 5 másodperc |

## <a name="next-steps"></a>További lépések

Ezután meg tudhat meg többet globális terjesztés, és kompromisszumot kínál a konzisztencia általános elosztott rendszerek használatával a következő cikkeket:

* [Konzisztencia kompromisszumot kínál a modern elosztott adatbázis-rendszerek terve](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Magas rendelkezésre állás](high-availability.md)
* [Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
