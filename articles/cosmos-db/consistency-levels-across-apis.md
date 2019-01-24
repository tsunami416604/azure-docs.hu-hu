---
title: Konzisztenciaszintek és Azure Cosmos DB API-k
description: A konzisztenciaszintek ismertetése az Azure Cosmos DB API-k között.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: 4d2994ea6ab6d6472ec56f0f2e378062590c8920
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54806997"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konzisztenciaszintek és Azure Cosmos DB API-k

Azure Cosmos DB által kínált öt konzisztenciamodell az Azure Cosmos DB SQL API natív módon támogatottak. Azure Cosmos DB használata esetén az SQL API az alapértelmezett érték. 

Az Azure Cosmos DB natív támogatást is nyújt átviteli protokoll-kompatibilis API-k népszerű adatbázisok számára. Adatbázisok belefoglalása a MongoDB, Apache Cassandra, Gremlin és Azure Table storage. Ezek az adatbázisok pontosan a meghatározott konzisztenciamodellt kínál vagy a konzisztenciaszintek garanciát SLA-alapú nem kínálnak. Ezek általában az Azure Cosmos DB által kínált öt konzisztenciamodell csak egy adott részét biztosítják. Az SQL API-t, a Gremlin API-t és a Table API az Azure Cosmos DB-fiókot konfigurálni az alapértelmezett konzisztenciaszint használható. 

A következő szakaszok bemutatják az adatok konzisztenciáját, az Apache Cassandra egy nyílt Forráskódú ügyfél-illesztőprogram által kért közötti leképezést 4.x-es és a MongoDB 3.4-es. Ez a dokumentum az Azure Cosmos DB megfelelő konzisztenciaszintek is bemutatja az Apache Cassandra- és mongodb-hez.

## <a id="cassandra-mapping"></a>Az Apache Cassandra és az Azure Cosmos DB konzisztenciaszintjeinek közötti megfeleltetés

Ez a táblázat az Apache Cassandra és a konzisztenciaszint közötti konzisztencia leképezése az Azure Cosmos DB. Minden Cassandra olvasási és írási konzisztenciaszintet Cosmos DB konzisztencia szintnek megfelelő biztosít erősebb, azaz a szigorúbb garanciát.

Az alábbi táblázat a **írási konzisztencia leképezés** Azure Cosmos DB és a Cassandra között:

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

Az alábbi táblázat a **konzisztencia leképezés olvasása** Azure Cosmos DB és a Cassandra között:

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