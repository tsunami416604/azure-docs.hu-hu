---
title: Konzisztenciaszintek és az Azure Cosmos DB API-k |} A Microsoft Docs
description: A konzisztenciaszintek ismertetése az Azure Cosmos DB API-k között.
keywords: konzisztencia, az azure cosmos db, azure, modellek, mongodb, cassandra, gráf, tábla, a Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: ed08b90b9e216ee8713bfe445e98144bf2ba02d4
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50244191"
---
# <a name="consistency-levels-and-cosmos-db-apis"></a>Konzisztenciaszintek és a Cosmos DB API-k

Az öt konzisztenciamodell által natívan támogatott az SQL API-t, amely az alapértelmezett API-t akkor, ha a Cosmos DB használatával. Mellett az SQL API-t, Cosmos DB is natív támogatást biztosít a vonalprotokollt kompatibilis API-k népszerű adatbázisok, például a MongoDB, Apache Cassandra, Gremlin és Azure-beli táblák. Ezek az adatbázisok ajánlat sem pontosan definiált konzisztenciamodelleket, sem a a konzisztenciaszint garantálja az SLA-alapú, és általában a Cosmos DB által öt konzisztenciamodell csak egy adott részét biztosítják. Az SQL API-t, a Gremlin API-t és a Table API az alapértelmezett konzisztenciaszintet Cosmos-fiók konfigurálva lesz használatban.

Az alábbi táblázatban láthatók az Apache Cassandra egy nyílt Forráskódú ügyfél-illesztőprogram által kért adatkonzisztencia közötti leképezést 4.x-es és a MongoDB 3.4 Cassandra API és a MongoDB API-t, illetve használata során, és a Cosmos DB megfelelő konzisztenciaszintek.

## <a id="cassandra-mapping"></a>Az Apache Cassandra és a Cosmos DB konzisztenciaszintjeinek leképezése

Az alábbi táblázat az Apache Cassandra 4.x-es ügyfél és a Cosmos DB "Alapértelmezett" konzisztenciaszint egy többrégiós és a egyetlen régióban üzembe között olvasás következetes hozzárendelését.

| **Az Apache Cassandra 4.x** | **A cosmos DB (többrégiós)** | **A cosmos DB (egyetlen régióban)** |
| - | - | - |
| EGY KÉT HÁ' | Konzisztens előtag | Konzisztens előtag |
| LOCAL_ONE | Konzisztens előtag | Konzisztens előtag |
| KVÓRUM, AZ ÖSSZES, SOROS | Korlátozott frissesség (alapértelmezés) vagy erős (a privát előzetes verzió) | Erős |
| LOCAL_QUORUM | Kötött elavulás | Erős |
| LOCAL_SERIAL | Kötött elavulás | Erős |

## <a id="mongo-mapping"></a>A MongoDB 3.4-es és a Cosmos DB konzisztenciaszintjeinek közötti megfeleltetés

Az alábbi táblázat a leképezés "olvasási aggályokat" MongoDB 3.4-es és a Cosmos DB "Alapértelmezett" konzisztenciaszint egy többrégiós és a egyetlen régióban üzembe.

| **MongoDB 3.4** | **A cosmos DB (többrégiós)** | **A cosmos DB (egyetlen régióban)** |
| - | - | - |
| Linearizable | Erős | Erős |
| Csomóponttöbbséget használó | Kötött elavulás | Erős |
| Helyi | Konzisztens előtag | Konzisztens előtag |

## <a name="next-steps"></a>További lépések

Olvassa el a további információk a konzisztenciaszintekről és kompatibilitás a nyílt forráskódú API-k az alábbi cikkeket a Cosmos DB API-k között:

* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)
* [Cosmos DB MongoDB API által támogatott MongoDB-funkciók](mongodb-feature-support.md)
* [Cosmos DB Cassandra API által támogatott Apache Cassandra-funkciók](cassandra-support.md)