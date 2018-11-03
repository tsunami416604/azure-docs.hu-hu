---
title: Rendelkezésre állás és teljesítmény kompromisszumot kínál a különböző konzisztenciaszintet az Azure Cosmos DB |} A Microsoft Docs
description: Az Azure Cosmos DB különböző konzisztenciaszintet rendelkezésre állás és teljesítmény kompromisszumot.
keywords: konzisztencia, teljesítmény, az azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: 8f36026c7e5802994b8cf22d60c6ecea052e6382
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963047"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels-in-azure-cosmos-db"></a>Kompromisszumot kínál a különböző konzisztenciaszintet az Azure Cosmos DB rendelkezésre állás és teljesítmény

Elosztott adatbázisok hagyatkoznia a magas rendelkezésre állás, alacsony késleltetésű, illetve mindkettőt, replikációs győződjön és a rendelkezésre állási, teljesítmény és a késés az olvasás következetes alapvető magával. Az Azure Cosmos DB álló kínálat az erős és végleges konzisztencia a két szélsőséges helyett lehetőségek, az adatkonzisztencia közelíti meg. A cosmos DB segítségével a fejlesztők számára, hogy az öt jól definiált konzisztenciamodellekkel közül választhat a konzisztencia (legerősebb leggyengébb felé haladva) – színspektrumból **erős**, **korlátozott frissesség**, **munkamenet** , **konzisztens előtag**, és **végleges**. Az öt konzisztenciamodell mindegyike a rendelkezésre állás és teljesítmény kompromisszumot kínál, és átfogó SLA-k élvezik.

## <a name="consistency-levels-and-latency"></a>Konzisztenciaszintek és késés

- A **olvasási késés** az összes konzisztenciaszintek mindig garantáltan 99 százalékon legfeljebb 10 ezredmásodperc, és az SLA-je támogat. (A az 50. percentilis) az átlagos olvasási késése általában 2 ezredmásodperc vagy kisebb.

- A Cosmos-fiókok, span számos régióban, és vannak konfigurálva az erős konzisztencia, kivéve a **írási késés** a fennmaradó konzisztencia szintek mindig garantáltan a 99th, legfeljebb 10 ezredmásodperc PERCENTILIS. Az SLA biztonsági Ez írási késése. A (a az 50. percentilis), az átlagos írási késést általában 5 ezredmásodperc vagy kisebb.

- A Cosmos-fiókok, amelyek számos régióban konfigurálva az erős konzisztencia (jelenleg előzetes verzióban érhető el) a **írási késés** garantáltan kisebb, mint < (2 * üzenetváltási idő/RTT) + 99 százalékon 10 ezredmásodperc. Az a Cosmos-fiókjához társított két legtávolabbi régiók közötti Körbejárási. A pontos Körbejárási késés a pontos Azure hálózati topológiáját és sebessége, kis távolság függvénye. Az Azure-hálózatok nem biztosít bármely késés SLA-k számára a két Azure-régiók közötti Körbejárási. Cosmos DB replikáció késése Cosmos-fiókja, hogy felmérhesse a replikáció késése-Cosmos-fiókjához hozzárendelt különböző régiók közötti Azure-portálon jelennek meg.

## <a name="consistency-levels-and-throughput"></a>Konzisztenciaszintek és az átviteli sebesség

- Kérelemegység azonos számú a munkamenet, konzisztens előtag és végleges konzisztencia szintek adja meg körülbelül 2 X olvasási teljesítménye az erős és a korlátozott frissesség képest.

- Írási művelet, például az insert, replace, upsert, törlés, stb. egy adott típusú a lemezírás teljesítménye, a kérelemegységek megegyezik az összes konzisztenciaszintek.

## <a name="consistency-levels-and-durability"></a>Konzisztenciaszintek és tartóssága

Írási művelet arra vonatkozik, az ügyfél számára, mielőtt az adatok tartósan véglegesítve lettek szerint a kvórum replikával, amely az írási műveletek a régión belül. Továbbá ha a tároló az egységes indexelési szabályzat van konfigurálva, az index is szinkron módon frissíti, replikálja, és tartósan véglegesítve lettek a kvórum replikák által az ügyfélnek az írási művelet nyugtázása elküldése előtt.

A következő táblázat összefoglalja a potenciális adatvesztési időszak regionális katasztrófa a Cosmos-fiókok, amelyek számos régióban.

| **Konzisztenciaszint** | **A lehetséges regionális katasztrófa adatvesztési időszak** |
| - | - |
| Erős | Nulla |
| Kötött elavulás | A Cosmos-fiók konfigurálása a "frissesség ablakban" korlátozódik. |
| Munkamenet | Legfeljebb 5 másodperc |
| Konzisztens előtag | Legfeljebb 5 másodperc |
| Végleges | Legfeljebb 5 másodperc |

## <a name="next-steps"></a>További lépések

Ezután meg tudhat meg többet globális terjesztés, és kompromisszumot kínál a konzisztencia általános elosztott rendszerek használatával a következő cikkeket:

* [Konzisztencia kompromisszumot kínál a modern elosztott adatbázis-rendszerek terve](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Magas rendelkezésre állás](high-availability.md)
* [Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
