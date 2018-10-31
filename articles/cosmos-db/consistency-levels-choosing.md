---
title: Az alkalmazás a megfelelő konzisztenciaszint kiválasztása |} A Microsoft Docs
description: Az alkalmazás a megfelelő konzisztenciaszint kiválasztása az Azure Cosmos DB-ben.
keywords: konzisztencia, teljesítmény, az azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 4b438320ac75918d10880a4bcdccadca4eebec32
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244184"
---
# <a name="choose-the-right-consistency-level-for-your-application"></a>Az alkalmazás a megfelelő konzisztenciaszint kiválasztása

Elosztott adatbázisok hagyatkoznia a magas rendelkezésre állás, alacsony késleltetésű, illetve mindkettőt, replikációs győződjön és a rendelkezésre állási, teljesítmény és késés az olvasás következetes alapvető magával. A legtöbb kereskedelmi forgalomban kapható elosztott adatbázisok kérje meg a fejlesztők számára, hogy a két szélsőséges konzisztencia modell közül választhat: erős konzisztencia és a végleges konzisztencia. Az Azure Cosmos DB lehetővé teszi a fejlesztők számára, hogy öt jól definiált konzisztenciamodellekkel közül választhat: erős, kötött elavulás, munkamenet, konzisztens előtag és végleges. Egyes ezek konzisztenciamodellt kínál jól definiált és könnyen kezelhető és bizonyos valós forgatókönyvek esetén is használható. Az öt konzisztenciamodell mindegyike egyértelmű rendelkezésre állását és teljesítményét és kompromisszumot kínál a biztosít, és átfogó SLA-k élvezik. Az alábbi egyszerű szempontok segítségével számos gyakori szituációhoz kínál végezze el a megfelelő választás.

## <a name="sql-api-or-table-api"></a>Az SQL API-t vagy a Table API

- A való életből vett helyzetekben munkamenet-konzisztencia optimális, és a javasolt megoldás. További információ: [útmutató az alkalmazás munkamenet-token kezelése](how-to-manage-consistency.md#utilize-session-tokens).
- Ha az alkalmazás az erős konzisztencia igényel, korlátozott frissesség konzisztenciaszint használata ajánlott.
- Ha szigorúbb konzisztenciagaranciákat való írásra munkamenet-konzisztencia és számjegy-ezredmásodperces késés által biztosított rétegében, korlátozott frissesség konzisztenciaszint használata ajánlott.  
- Ha az alkalmazás a végleges konzisztencia igényel, konzisztens előtag konzisztenciaszint használata ajánlott.
- Ha kevesebb mint mi munkamenet-konzisztencia által biztosított szigorú konzisztenciagaranciákat van szüksége, konzisztens előtag konzisztenciaszint használata ajánlott.
- Ha a legmagasabb szintű rendelkezésre állásra és a legkisebb késés van szüksége, használja a végleges konzisztencia szintjét.

## <a name="cassandra-mongodb-or-gremlin-api"></a>Cassandra, mongodb-hez vagy a Gremlin API

- További információ a "Olvasási Konzisztenciaszint" és a Cosmos DB Apache Cassandra konzisztenciaszint-közötti leképezést: [konzisztenciaszintek és a Cosmos DB API-k](consistency-levels-across-apis.md#cassandra-mapping).
- További információ a mongodb-hez és az Azure Cosmos DB konzisztenciaszintjeinek "olvasási aggodalomra" közötti leképezést: [konzisztenciaszintek és a Cosmos DB API-k](consistency-levels-across-apis.md#mongo-mapping).

## <a name="you-may-get-stronger-consistency-guarantees-in-practice"></a>A gyakorlatban erősebb konzisztenciagaranciákat kaphat

És garantált adatkonzisztenciát biztosítanak az egy olvasási művelet megfelelnek a frissesség és rendezése a kért adatbázis állapotát. Olvasási-konzisztencia a rendezés és propagálás (frissítés) írási műveletek vannak kötve.  

Ha a konzisztenciaszint beállítása **korlátozott frissesség**, Cosmos DB garantálja, hogy az ügyfelek mindig elolvashassák a korábbi írási értékét a frissesség ablak, amelyet késéssel.

Ha a konzisztenciaszint beállítása **erős**, a frissesség ablak egyenértékű nulla, és az ügyfelek garantáltan olvassa el az írási legutóbbi véglegesített értékét.

A fennmaradó három konzisztenciaszintek a frissesség ablak nagymértékben függ a számítási feladat. Például, ha nincs írási történik az adatbázison, az olvasási művelet **végleges**, **munkamenet**, vagy **konzisztens előtag** konzisztenciaszintek valószínű, hogy eddig is számtalan előnyét ugyanazokat az eredményeket, erős konzisztenciaszintre olvasási művelet.

A Cosmos DB-fiókot az erős konzisztencia kívül bármely konzisztenciaszint van beállítva, ha talál a valószínűsége annak, az ügyfelek számára a workload(s) erősen konzisztens (linearizable) olvasási első megnézzük a valószínűségi korlátozott Az Azure Portalon elérhető frissesség (PBS) metrika [PBS metrika használatáról itt találhat](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric). Valószínűségi korlátozott frissesség látható, hogyan végleges a végleges konzisztenciát. Ez a metrika nyújt abba, hogy milyen gyakran kap, mint a konzisztencia szintjét, a Cosmos DB-fiókjában konfigurált erősebb konzisztencia. Más szóval megjelenik a valószínűsége annak, (a leírt ezredmásodperc) első erősen konzisztens olvasási, írási és olvasási régió kombinációja.

## <a name="next-steps"></a>További lépések

További információ az az alábbi cikkeket:

* [Cosmos DB API-k közötti megfeleltetés konzisztencia](consistency-levels-across-apis.md)
* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)
* [A munkamenet-azonosító, az alkalmazás kezelése](how-to-manage-consistency.md#utilize-session-tokens)
* [A Probabilistically korlátozott frissesség (PBS) metrika figyelése](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)