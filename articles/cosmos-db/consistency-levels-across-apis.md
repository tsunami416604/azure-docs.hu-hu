---
title: Konzisztenciaszintek és Azure Cosmos DB API-k
description: A különböző API-k közötti konzisztenciaszintű leképezés ismertetése az Azure Cosmos DB és az Apache Cassandra, MongoDB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: ef7d032d37105549ff7b05f85b953cd420954602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131458"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konzisztenciaszintek és Azure Cosmos DB API-k

Az Azure Cosmos DB natív támogatást nyújt a népszerű adatbázisok vezetékes protokollkompatibilis API-khoz. Ezek közé tartozik a MongoDB, az Apache Cassandra, a Gremlin és az Azure Table storage. Ezek az adatbázisok nem nyújtanak pontosan meghatározott konzisztenciamodelleket vagy SLA-alapú garanciákat a konzisztenciaszintekhez. Általában csak az Azure Cosmos DB által kínált öt konzisztenciamodell egy részét biztosítják. 

SQL API, Gremlin API és Table API használata esetén az Azure Cosmos-fiókban konfigurált alapértelmezett konzisztenciaszint használatos. 

Cassandra API vagy az Azure Cosmos DB MongoDB-hoz készült API-jának használatakor az alkalmazások az Apache Cassandra és a MongoDB által kínált konzisztenciaszintek teljes készletét kapják, még erősebb konzisztencia- és tartóssági garanciákkal. Ez a dokumentum az Apache Cassandra és a MongoDB konzisztenciaszintek megfelelő Azure Cosmos DB konzisztenciaszintjeit mutatja be.

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Az Apache Cassandra és az Azure Cosmos DB konzisztenciaszintjei közötti leképezés

Az Azure Cosmos DB-vel ellentétben az Apache Cassandra natív módon nem nyújt pontosan meghatározott konzisztenciagaranciákat.  Ehelyett az Apache Cassandra írási konzisztenciaszintet és olvasási konzisztenciaszintet biztosít a magas rendelkezésre állás, a konzisztencia és a késés kompromisszumok engedélyezéséhez. Az Azure Cosmos DB Cassandra API-jának használatakor: 

* Az Apache Cassandra írási konzisztenciaszintje az Azure Cosmos-fiókban konfigurált alapértelmezett konzisztenciaszintre van leképezve. Az írási művelet (CL) konzisztenciája kérésenként nem módosítható.

* Az Azure Cosmos DB dinamikusan leképezi a Cassandra ügyfél-illesztőprogram által megadott olvasási konzisztenciaszintet az egyik az Azure Cosmos DB konzisztenciaszintek dinamikusan konfigurálva egy olvasási kérelem. 

Az alábbi táblázat bemutatja, hogy a natív Cassandra konzisztenciaszintek hogyan vannak leképezve az Azure Cosmos DB konzisztenciaszintjeihez a Cassandra API használatakor:  

[![Cassandra konzisztenciamodell leképezése](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>Leképezés a MongoDB és az Azure Cosmos DB konzisztenciaszintjei között

Az Azure Cosmos DB-vel ellentétben a natív MongoDB nem nyújt pontosan meghatározott konzisztenciagaranciákat. Ehelyett a natív MongoDB lehetővé teszi a felhasználók számára, hogy konfigurálja a következő konzisztencia-garanciák: egy írási probléma, egy olvasási probléma, és a master direktíva -, hogy az olvasási műveletek et elsődleges vagy másodlagos replikákra irányítsa a kívánt konzisztenciaszint elérése érdekében. 

Az Azure Cosmos DB MongoDB API-jának használatakor a MongoDB-illesztőprogram az írási régiót elsődleges replikaként kezeli, és az összes többi régió olvasási replikája. Kiválaszthatja, hogy az Azure Cosmos-fiókmelyik régiót elsődleges replikaként társítja. 

Az Azure Cosmos DB MongoDB-hoz való API-jának használata közben:

* Az írási probléma az Azure Cosmos-fiókban konfigurált alapértelmezett konzisztenciaszintre van leképezve.
 
* Az Azure Cosmos DB dinamikusan leképezi a MongoDB ügyfél-illesztőprogram által megadott olvasási problémát az Egyik az Azure Cosmos DB konzisztenciaszintek egyikéhez, amely dinamikusan van konfigurálva egy olvasási kérelemhez.  

* Az Azure Cosmos-fiókhoz társított adott régiót "Master" néven is elláthatja, ha a régiót az első írható régióvá teszi. 

Az alábbi táblázat bemutatja, hogy a natív MongoDB írási/olvasási aggodalmak hogyan vannak leképezve az Azure Cosmos konzisztenciaszintjeihez az Azure Cosmos DB MongoDB API-jának használatakor:

[![MongoDB konzisztenciamodell-leképezés](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>További lépések

További információ a konzisztenciaszintekről és az Azure Cosmos DB API-k és a nyílt forráskódú API-k közötti kompatibilitásról. Lásd az alábbi cikkeket:

* [Rendelkezésre állási és teljesítménykompromisszumok a különböző konzisztenciaszintekhez](consistency-levels-tradeoffs.md)
* [Az Azure Cosmos DB MongoDB-hoz való API-ja által támogatott MongoDB-funkciók](mongodb-feature-support.md)
* [Az Azure Cosmos DB Cassandra API által támogatott Apache Cassandra funkciók](cassandra-support.md)
