---
title: Konzisztenciaszintek és Azure Cosmos DB API-k
description: A konzisztenciaszintek ismertetése az Azure Cosmos DB API-k között.
keywords: konzisztencia, az azure cosmos db, azure, modellek, mongodb, cassandra, gráf, tábla, a Microsoft azure
services: cosmos-db
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: 2fde29111ae1a95aec0cfef93762d3ae4f0e0dd0
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2018
ms.locfileid: "53809386"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konzisztenciaszintek és Azure Cosmos DB API-k

Azure Cosmos DB által kínált öt konzisztenciamodell az Azure Cosmos DB SQL API natív módon támogatottak. Azure Cosmos DB használata esetén az SQL API az alapértelmezett érték. 

Az Azure Cosmos DB natív támogatást is nyújt átviteli protokoll-kompatibilis API-k népszerű adatbázisok számára. Adatbázisok belefoglalása a MongoDB, Apache Cassandra, Gremlin és Azure Table storage. Ezek az adatbázisok pontosan a meghatározott konzisztenciamodellt kínál vagy a konzisztenciaszintek garanciát SLA-alapú nem kínálnak. Ezek általában az Azure Cosmos DB által kínált öt konzisztenciamodell csak egy adott részét biztosítják. Az SQL API-t, a Gremlin API-t és a Table API az Azure Cosmos DB-fiókot konfigurálni az alapértelmezett konzisztenciaszint használható. 

A következő szakaszok bemutatják az adatok konzisztenciáját, az Apache Cassandra egy nyílt Forráskódú ügyfél-illesztőprogram által kért közötti leképezést 4.x-es és a MongoDB 3.4-es. Ez a dokumentum az Azure Cosmos DB megfelelő konzisztenciaszintek is bemutatja az Apache Cassandra- és mongodb-hez.

## <a id="cassandra-mapping"></a>Az Apache Cassandra és az Azure Cosmos DB konzisztenciaszintjeinek közötti megfeleltetés

Ez a táblázat az Azure Cosmos DB az Apache Cassandra 4.x-es ügyfél és az alapértelmezett konzisztenciaszint között "következetes Olvasás" hozzárendelést mutatja be. A táblázat több régióban, és egyetlen régióban üzemelő példányok.

| **Az Apache Cassandra 4.x** | **Az Azure Cosmos DB (többrégiós)** | **Az Azure Cosmos DB (egyetlen régióban)** |
| - | - | - |
| EGY KÉT HÁ' | Konzisztens előtag | Konzisztens előtag |
| LOCAL_ONE | Konzisztens előtag | Konzisztens előtag |
| KVÓRUM, AZ ÖSSZES, SOROS | Korlátozott frissesség az alapértelmezett érték. Erős private preview verzióban van. | Erős |
| LOCAL_QUORUM | Korlátozott frissesség | Erős |
| LOCAL_SERIAL | Korlátozott frissesség | Erős |

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