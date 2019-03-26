---
title: Az alkalmazás, amely az Azure Cosmos DB a megfelelő konzisztenciaszint kiválasztása
description: Az alkalmazás a megfelelő konzisztenciaszint kiválasztása az Azure Cosmos DB-ben.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2018
ms.reviewer: sngun
ms.openlocfilehash: f32434e5ac0cd35cf620c1589aeb441476622442
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407453"
---
# <a name="choose-the-right-consistency-level"></a>A megfelelő konzisztenciaszint kiválasztása 

Elosztott adatbázisok hagyatkoznia a magas rendelkezésre állás, alacsony késleltetésű, illetve mindkettőt, replikációs győződjön és a rendelkezésre állási, teljesítmény és késés az olvasás következetes alapvető magával. A legtöbb kereskedelmi forgalomban kapható elosztott adatbázisok kérje meg a fejlesztők számára, hogy a két szélsőséges konzisztencia modell közül választhat: *erős* konzisztencia és *végleges* konzisztencia. Az Azure Cosmos DB lehetővé teszi a fejlesztők számára, hogy az öt jól definiált konzisztenciamodellekkel közül választhat: *erős*, *korlátozott frissesség*, *munkamenet*, *konzisztens előtag* és *végleges*. Egyes ezek konzisztenciamodellt kínál jól definiált és könnyen kezelhető és bizonyos valós forgatókönyvek esetén is használható. Az öt konzisztenciamodell mindegyike biztosít pontos [rendelkezésre állás és teljesítmény kompromisszumot kínál a](consistency-levels-tradeoffs.md) és átfogó SLA-k élvezik. Az alábbi egyszerű szempontok segítségével számos gyakori szituációhoz kínál végezze el a megfelelő választás.

## <a name="sql-api-and-table-api"></a>Az SQL API és a tábla API

Ha az alkalmazás az SQL API-t vagy a Table API használatával lett összeállítva, vegye figyelembe a következőket:

- A való életből vett helyzetekben munkamenet-konzisztencia optimális, és a javasolt megoldás. További információkért tekintse meg, [útmutató az alkalmazás munkamenet-token kezelése](how-to-manage-consistency.md#utilize-session-tokens).

- Ha az alkalmazás az erős konzisztencia igényel, korlátozott frissesség konzisztenciaszint használata ajánlott.

- Ha ennél szigorúbb van szüksége a konzisztencia, mint a megadott munkamenet-konzisztencia, és egyetlen-számjegy-ezredmásodperces késés az írások, ajánlott használt korlátozott frissesség konzisztenciaszint garantálja.  

- Ha az alkalmazás a végleges konzisztencia igényel, konzisztens előtag konzisztenciaszint használata ajánlott.

- Ha kevésbé szigorú konzisztenciagaranciákat munkamenet-konzisztencia által biztosított rétegében van szüksége, konzisztens előtag konzisztenciaszint használata ajánlott.

- Ha a legmagasabb szintű rendelkezésre állásra és a legkisebb késés van szüksége, használja a végleges konzisztencia szintjét.

- Ha még magasabb adatok tartóssága teljesítmény feláldozása nélkül, létrehozhat egy egyéni konzisztenciaszint az alkalmazásréteg szintjén. További információ: [útmutató egyéni-szinkronizálás megvalósítása az alkalmazásokban](how-to-custom-synchronization.md).

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Cassandra, MongoDB és Gremlin API-k

- Részletek közötti leképezést az "Olvasás Konzisztenciaszint" és a Cosmos DB Apache Cassandra konzisztenciaszintek érhető el: [konzisztenciaszintek és a Cosmos DB API-k](consistency-levels-across-apis.md#cassandra-mapping).

- További információ a mongodb-hez és az Azure Cosmos DB konzisztenciaszintjeinek "olvasási aggodalomra" közötti leképezést: [konzisztenciaszintek és a Cosmos DB API-k](consistency-levels-across-apis.md#mongo-mapping).

## <a name="consistency-guarantees-in-practice"></a>A gyakorlatban és garantált adatkonzisztenciát biztosítanak

A gyakorlatban erősebb konzisztenciagaranciákat előfordulhat, hogy milyen gyakran kap. Olvasási művelet és garantált adatkonzisztenciát biztosítanak felel meg a frissesség és a megrendelés, az adatbázis állapotát kérhetnek. Olvasási-konzisztencia a rendezés és propagálás írási/frissítés művelet van kötve.  

* Ha a konzisztenciaszint beállítása **korlátozott frissesség**, Cosmos DB garantálja, hogy az ügyfelek mindig elolvashassák a korábbi írási értékét a frissesség ablak, amelyet késéssel.

* Ha a konzisztenciaszint beállítása **erős**, a frissesség ablak egyenértékű nulla, és az ügyfelek garantáltan olvassa el a legújabb véglegesített értéke az írási művelet.

* A fennmaradó három konzisztenciaszintek a frissesség ablak nagymértékben függ a számítási feladat. Ha nincs írási művelet az adatbázison, az olvasási művelete például **végleges**, **munkamenet**, vagy **konzisztens előtag** konzisztenciaszintek valószínű, hogy eddig is számtalan előnyét ugyanazokat az eredményeket, erős konzisztenciaszintre olvasási művelet.

Az Azure Cosmos-fiók egy eltérő az erős konzisztencia konzisztenciaszint van beállítva, ha annak a valószínűsége annak, hogy az ügyfelek erős kaphat, és a számítási feladatokhoz konzisztens olvasás megnézzük a *Probabilistically Korlátozott frissesség* (PBS) metrikát. Ez a metrika az Azure Portalon érhető el, további tudnivalókért lásd: [figyelő Probabilistically korlátozott frissesség (PBS) metrika](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Valószínűségi korlátozott frissesség látható, hogyan végleges a végleges konzisztenciát. Ez a metrika abba, hogy milyen gyakran kap egy erősebb konzisztencia a konzisztencia szintjét, amely a jelenleg konfigurált az Azure Cosmos-fiók, mint biztosít. Más szóval tekintse meg az első erősen konzisztens olvasási, írási kombinációja (ezredmásodpercekben mért) valószínűségét, és olvasási régió.

## <a name="next-steps"></a>További lépések

További információ az az alábbi cikkeket:

* [Cosmos DB API-k közötti megfeleltetés konzisztencia](consistency-levels-across-apis.md)
* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)
* [A munkamenet-azonosító, az alkalmazás kezelése](how-to-manage-consistency.md#utilize-session-tokens)
* [A figyelő Probabilistically korlátozott frissesség (PBS) metrika](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
