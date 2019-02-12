---
title: Konzisztenciaszintek és Azure Cosmos DB API-k
description: A konzisztenciaszintek ismertetése az Azure Cosmos DB API-k között.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: b620ca76cfea296e504afffd91852308a01575db
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56001983"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konzisztenciaszintek és Azure Cosmos DB API-k

Azure Cosmos DB által kínált öt konzisztenciamodell az SQL API natív módon támogatottak. Azure Cosmos DB használata esetén az SQL API az alapértelmezett érték. 

Az Azure Cosmos DB natív támogatást is nyújt átviteli protokoll-kompatibilis API-k népszerű adatbázisok számára. Adatbázisok belefoglalása a MongoDB, Apache Cassandra, Gremlin és Azure Table storage. Ezek az adatbázisok pontosan a meghatározott konzisztenciamodellt kínál vagy a konzisztenciaszintek garanciát SLA-alapú nem kínálnak. Ezek általában az Azure Cosmos DB által kínált öt konzisztenciamodell csak egy adott részét biztosítják. Az SQL API-t, a Gremlin API-t és a Table API az alapértelmezett konzisztenciaszint a következőn: az Azure Cosmos-fiók használható. 

A következő szakaszok bemutatják a leképezés között az Apache Cassandra, mongodb-hez és a megfelelő konzisztenciaszintek az Azure Cosmos DB egy nyílt Forráskódú ügyfél-illesztőprogram által kért adatok konzisztenciáját.

## <a id="cassandra-mapping"></a>Az Apache Cassandra és az Azure Cosmos DB konzisztenciaszintjeinek közötti megfeleltetés

Alábbi táblázat ismerteti az egy Cassandra API-hoz és az azzal egyenértékű natív konzisztencia megfeleltetés Cosmos DB segítségével különböző konzisztencia együttes használata. Az Apache Cassandra írási és olvasási mód minden kombinációja Cosmos DB natív módon támogatottak. Minden Apache Cassandra írási és olvasási konzisztenciájú modellt kombinációját, a Cosmos DB fog biztosítani, egyenlő vagy nagyobb konzisztenciára vonatkozó garanciákat, mint az Apache Cassandra. Emellett a Cosmos DB biztosítja a nagyobb tartósságot garantál, mint az Apache Cassandra írási leggyengébb módban is.

Az alábbi táblázat a **írási konzisztencia leképezési** Azure Cosmos DB és a Cassandra között:

| Cassandra | Azure Cosmos DB | Garancia |
| - | - | - |
|Összes|Erős  | Linearizálhatóság |
| EACH_QUORUM   | Erős    | Linearizálhatóság | 
| SOROS KVÓRUM |  Erős |    Linearizálhatóság |
| LOCAL_QUORUM, HÁROM, KÉT, EGY, LOCAL_ONE, BÁRMELY | Konzisztens előtag |Globális konzisztens előtag |
| EACH_QUORUM   | Erős    | Linearizálhatóság |
| SOROS KVÓRUM |  Erős |    Linearizálhatóság |
| LOCAL_QUORUM, HÁROM, KÉT, EGY, LOCAL_ONE, BÁRMELY | Konzisztens előtag | Globális konzisztens előtag |
| SOROS KVÓRUM | Erős   | Linearizálhatóság |
| LOCAL_QUORUM, HÁROM, KÉT, EGY, LOCAL_ONE, BÁRMELY | Konzisztens előtag | Globális konzisztens előtag |
| LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE    | Kötött elavulás | <ul><li>Korlátozott frissesség.</li><li>A legtöbb K verziók vagy t idő mögött.</li><li>Olvassa el a régióban található legutóbbi véglegesített érték.</li></ul> |
| EGY LOCAL_ONE, BÁRMELY   | Konzisztens előtag | Régiók szerinti konzisztens előtag |

Az alábbi táblázat a **olvasás konzisztenciájának leképezési** Azure Cosmos DB és a Cassandra között:

| Cassandra | Azure Cosmos DB | Garancia |
| - | - | - |
| MINDEN, A KVÓRUM, SOROS, LOCAL_QUORUM, LOCAL_SERIAL, HÁROM, KÉT, EGY, LOCAL_ONE | Erős  | Linearizálhatóság|
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO   |Erős |   Linearizálhatóság |
|LOCAL_ONE, ÉS AZ EGYIK | Konzisztens előtag | Globális konzisztens előtag |
| AZ ÖSSZES, KVÓRUM SOROZAT   | Erős    | Linearizálhatóság |
| LOCAL_ONE, EGY, LOCAL_QUORUM, LOCAL_SERIAL, KÉT, HÁROM |  Konzisztens előtag   | Globális konzisztens előtag |
| LOCAL_ONE, EGY, KETTŐ, HÁROM, LOCAL_QUORUM, KVÓRUM |    Konzisztens előtag   | Globális konzisztens előtag |
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO   |Erős |   Linearizálhatóság |
| LOCAL_ONE, ÉS AZ EGYIK    | Konzisztens előtag | Globális konzisztens előtag|
| MINDEN, a KVÓRUM, soros erős Linearizálhatósági
LOCAL_ONE, EGY, LOCAL_QUORUM, LOCAL_SERIAL, KÉT, HÁROM  |Konzisztens előtag  | Globális konzisztens előtag |
|Összes    |Erős |Linearizálhatóság |
| LOCAL_ONE, EGY, KETTŐ, HÁROM, LOCAL_QUORUM, KVÓRUM  |Konzisztens előtag  |Globális konzisztens előtag|
|MINDEN, a KVÓRUM, soros erős Linearizálhatósági
LOCAL_ONE, EGY, LOCAL_QUORUM, LOCAL_SERIAL, KÉT, HÁROM  |Konzisztens előtag  |Globális konzisztens előtag |
|Összes    |Erős | Linearizálhatóság |
| LOCAL_ONE, EGY, KETTŐ, HÁROM, LOCAL_QUORUM, KVÓRUM  | Konzisztens előtag | Globális konzisztens előtag |
| QUORUM, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE |  Kötött elavulás   | <ul><li>Korlátozott frissesség.</li><li>A legtöbb K verziók vagy t idő mögött. </li><li>Olvassa el a régióban található legutóbbi véglegesített érték.</li></ul>
| LOCAL_ONE, ÉS AZ EGYIK |Konzisztens előtag | Régiók szerinti konzisztens előtag |
| LOCAL_ONE, EGY, KETTŐ, HÁROM, LOCAL_QUORUM, KVÓRUM  | Konzisztens előtag | Régiók szerinti konzisztens előtag |


## <a id="mongo-mapping"></a>A MongoDB 3.4-es és az Azure Cosmos DB konzisztenciaszintjeinek közötti megfeleltetés

Az alábbi táblázat az Azure Cosmos DB MongoDB 3.4-es és az alapértelmezett konzisztenciaszint között "aggályokat olvasása" hozzárendelést mutatja be. A táblázat több régióban, és egyetlen régióban üzemelő példányok.

| **MongoDB 3.4** | **Az Azure Cosmos DB (többrégiós)** | **Az Azure Cosmos DB (egyetlen régióban)** |
| - | - | - |
| Linearizable | Erős | Erős |
| Csomóponttöbbséget használó | Korlátozott frissesség | Erős |
| Helyi | Konzisztens előtag | Konzisztens előtag |

## <a name="next-steps"></a>További lépések

További információ a konzisztenciaszintek és az Azure Cosmos DB API-k a nyílt forráskódú API-kkal közötti kompatibilitást. Lásd az alábbi cikkeket:

* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)
* [A mongodb-hez az Azure Cosmos DB API által támogatott MongoDB-funkciók](mongodb-feature-support.md)
* [Az Azure Cosmos DB Cassandra API által támogatott Apache Cassandra-funkciók](cassandra-support.md)