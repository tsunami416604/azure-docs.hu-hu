---
title: Válassza ki a megfelelő konzisztenciaszintet az Azure Cosmos DB alkalmazásához
description: Válassza ki a megfelelő konzisztenciaszintet az alkalmazás hoz az Azure Cosmos DB.Choosing the right consistency level for your application in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: cea157e272a2bf464141e592b8e742f314a83233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441922"
---
# <a name="choose-the-right-consistency-level"></a>A megfelelő konzisztenciaszint kiválasztása 

A magas rendelkezésre állás, alacsony késés vagy mindkettő replikációra támaszkodó elosztott adatbázisok alapvető kompromisszumot kötnek az olvasási konzisztencia és a rendelkezésre állás, a késés és az átviteli feszültség között. A legtöbb kereskedelmi forgalomban kapható elosztott adatbázis arra kéri a fejlesztőket, hogy válasszanak a két szélsőséges konzisztenciamodell közül: *erős* konzisztencia és *végleges* konzisztencia. Az Azure Cosmos DB lehetővé teszi a fejlesztők számára, hogy az öt jól meghatározott konzisztenciamodell közül válasszanak: *erős*, *határolt frissesség*, *munkamenet*, *konzisztens előtag* és *végleges*. Ezek a konzisztenciamodellek mindegyike jól meghatározott, intuitív, és használható adott valós forgatókönyvek. Az öt konzisztenciamodell mindegyike pontos [rendelkezésre állást és teljesítménykompromisszumot](consistency-levels-tradeoffs.md) biztosít, és átfogó SLA-k támasztják alá őket. A következő egyszerű szempontok segítenek abban, hogy a helyes választás számos gyakori forgatókönyv.

## <a name="sql-api-and-table-api"></a>SQL API és Table API

Vegye figyelembe a következő pontokat, ha az alkalmazás SQL API vagy Table API használatával készült:

- Számos valós forgatókönyv esetén a munkamenet konzisztenciája optimális, és ez az ajánlott lehetőség. További információt az [alkalmazás munkamenet-jogkivonatának kezelése](how-to-manage-consistency.md#utilize-session-tokens)című témakörben talál.

- Ha az alkalmazás erős konzisztenciát igényel, ajánlott a kötött frissesség konzisztenciaszintjét használni.

- Ha szigorúbb konzisztenciagaranciákra van szüksége, mint a munkamenet-konzisztencia és az írási késés egyszámjegyű késése, ajánlott a kötött frissesség konzisztenciaszintjét használni.  

- Ha az alkalmazás végleges konzisztenciát igényel, ajánlott konzisztens előtag-konzisztenciaszintet használni.

- Ha a munkamenet-konzisztencia által biztosítottaknál kevésbé szigorú konzisztenciagaranciákra van szüksége, ajánlott az előtag konzisztenciaszintjének használata.

- Ha a legmagasabb rendelkezésre állásra és a legalacsonyabb késésre van szüksége, használja a végleges konzisztenciaszintet.

- Ha még nagyobb adattartósságra van szüksége a teljesítmény feláldozása nélkül, egyéni konzisztenciaszintet hozhat létre az alkalmazásrétegen. További információ: [Útmutató az egyéni szinkronizálás megvalósításához az alkalmazásokban.](how-to-custom-synchronization.md)

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Cassandra, MongoDB és Gremlin API-k

- Az Apache Cassandra és a Cosmos DB konzisztenciaszintekben kínált "Olvasási konzisztenciaszint" közötti leképezésről a [Konzisztenciaszintek és a Cosmos DB API-k című témakörben olvashat](consistency-levels-across-apis.md#cassandra-mapping)részletesen.

- A MongoDB és az Azure Cosmos DB konzisztenciaszintek "Olvasási probléma" közötti leképezésről a [Konzisztenciaszintek és a Cosmos DB API-k című témakörben](consistency-levels-across-apis.md#mongo-mapping)olvashat részletesen.

## <a name="consistency-guarantees-in-practice"></a>Konzisztencia-garanciák a gyakorlatban

A gyakorlatban gyakran erősebb konzisztencia garanciákat kaphat. Az olvasási művelet konzisztenciagaranciái megfelelnek a kért adatbázisállapot frissességének és sorrendjének. Az olvasási-konzisztencia az írási/frissítési műveletek rendeléséhez és terjesztéséhez kötődik.  

* Ha a konzisztenciaszint **van beállítva, a kötött frissesség,** Cosmos DB garantálja, hogy az ügyfelek mindig olvassa el az előző írási értéket, a késés által határolt frissesség ablakban.

* Ha a konzisztenciaszint **értéke erős,** a frissességi ablak nullának felel meg, és az ügyfelek garantáltan beolvassák az írási művelet legutóbbi véglegesített értékét.

* A fennmaradó három konzisztenciaszint esetén a frissesség ablak nagymértékben függ a számítási feladatoktól. Ha például nincsenek írási műveletek az adatbázisban, akkor az **olvasási**művelet végleges , **munkamenettel**vagy **konzisztens** előtag-konzisztenciaszinttel valószínűleg ugyanazt az eredményt adja, mint az erős konzisztenciaszintű olvasási művelet.

Ha az Azure Cosmos-fiók az erős konzisztenciaszinttől eltérő konzisztenciaszinttel van konfigurálva, megtudhatja annak valószínűségét, hogy az ügyfelek erős és konzisztens olvasásokat kaphatnak a számítási feladatokhoz a *Probabilistically Bounded Staleness* (PBS) metrika vizsgálatával. Ez a metrika elérhető az Azure Portalon, további információkért [lásd: Probabilistically Bounded Staleness (PBS) metrika.](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)

Probabilistic határos állottság azt mutatja, hogy az esetleges az ön végleges konzisztencia. Ez a metrika betekintést nyújt abba, hogy milyen gyakran kaphat erősebb konzisztenciát, mint az Azure Cosmos-fiókban jelenleg konfigurált konzisztenciaszint. Más szóval láthatja annak valószínűségét (ezredmásodpercben mérve), hogy az írási és olvasási területek kombinációjához erősen konzisztens olvasások legyenek.

## <a name="next-steps"></a>További lépések

A konzisztenciaszintekről az alábbi cikkekben olvashat bővebben:

* [Konzisztenciaszint-hozzárendelés a Cosmos DB API-k között](consistency-levels-across-apis.md)
* [Rendelkezésre állási és teljesítménykompromisszumok a különböző konzisztenciaszintekhez](consistency-levels-tradeoffs.md)
* [Az alkalmazás munkamenet-jogkivonatának kezelése](how-to-manage-consistency.md#utilize-session-tokens)
* [A valószínűség alapján korlátozott frissesség (PBS) metrika monitorozása](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
