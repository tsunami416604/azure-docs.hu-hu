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
ms.openlocfilehash: 974531cd5907e4f69e7d064125d3e51fa4974949
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50956383"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konzisztenciaszintek és az Azure Cosmos DB API-k

Az Azure Cosmos DB által kínált öt konzisztenciamodell által natívan támogatott a Cosmos DB SQL API-t, amely az alapértelmezett API-t akkor, ha a Cosmos DB használatával. Mellett az SQL API-t, Cosmos DB is natív támogatást biztosít a vonalprotokollt kompatibilis API-k népszerű adatbázisok, például a MongoDB, Apache Cassandra, Gremlin és Azure-beli táblák. Ezeknek az adatbázisoknak sem ajánlat pontosan definiált konzisztenciamodelleket, sem az SLA-alapú garanciát a konzisztenciaszintek. Ezek az adatbázisok általában biztosítanak a Cosmos DB által kínált öt konzisztenciamodell csak egy részét. Az SQL API-t, a Gremlin API-t és a Table API az alapértelmezett konzisztenciaszintet Cosmos-fiók konfigurált használható.

A következő szakaszok bemutatják az Apache Cassandra egy nyílt Forráskódú ügyfél-illesztőprogram által kért adatkonzisztencia közötti leképezést 4.x-es és a MongoDB 3.4 Cassandra API és a MongoDB API-t, illetve használata során, és a Cosmos DB megfelelő konzisztenciaszintek.

## <a id="cassandra-mapping"></a>Az Apache Cassandra és a Cosmos DB konzisztenciaszintjeinek közötti megfeleltetés

Az alábbi táblázat a "következetes Olvasás" hozzárendelés között az Apache Cassandra 4.x-es ügyfél és az alapértelmezett konzisztenciaszintet Cosmos DB-ben mind a többrégiós, mind az egyrégiós telepítésekhez.

| **Az Apache Cassandra 4.x** | **A cosmos DB (többrégiós)** | **A cosmos DB (egyetlen régióban)** |
| - | - | - |
| EGY KÉT HÁ' | Konzisztens előtag | Konzisztens előtag |
| LOCAL_ONE | Konzisztens előtag | Konzisztens előtag |
| KVÓRUM, AZ ÖSSZES, SOROS | Korlátozott frissesség (alapértelmezés) vagy erős (a privát előzetes verzió) | Erős |
| LOCAL_QUORUM | Kötött elavulás | Erős |
| LOCAL_SERIAL | Kötött elavulás | Erős |

## <a id="mongo-mapping"></a>A MongoDB 3.4-es és a Cosmos DB konzisztenciaszintjeinek közötti megfeleltetés

Az alábbi táblázat a "aggályokat olvasása" hozzárendelés között MongoDB 3.4-es és az alapértelmezett konzisztenciaszintet Cosmos DB-ben többrégiós mind az egyrégiós telepítésekhez.

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