---
title: Konzisztenciaszintek és Azure Cosmos DB API-k
description: A konzisztenciaszintek ismertetése az Azure Cosmos DB API-k között.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a8617ae2b01fc89a4c957b8610164a2b53a16f5
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59274773"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konzisztenciaszintek és Azure Cosmos DB API-k

Az Azure Cosmos DB natív támogatást nyújt az átviteli protokoll-kompatibilis API-k népszerű adatbázisok számára. Ezek közé tartozik a MongoDB, Apache Cassandra, Gremlin és Azure Table storage. Ezeknek az adatbázisoknak nem pontosan definiált konzisztenciamodellekkel vagy a konzisztenciaszintek vonatkozó SLA-alapú garanciákat kínál. Ezek általában az Azure Cosmos DB által kínált öt konzisztenciamodell csak egy adott részét biztosítják. 

SQL API-t, a Gremlin API és a Table API használatakor az alapértelmezett konzisztenciaszint a következőn: az Azure Cosmos-fiók szolgál. 

Cassandra API-t vagy az Azure Cosmos DB API a mongodb-hez használatakor alkalmazások lekérése az Apache Cassandra- és mongodb-vel, által kínált konzisztenciaszintekről, még erősebb konzisztencia-és tartóssági garancia teljes körű. Ez a dokumentum a konzisztenciaszintek a megfelelő Azure Cosmos DB Apache Cassandra- és MongoDB konzisztenciaszintek mutatja be.


## <a id="cassandra-mapping"></a>Az Apache Cassandra és az Azure Cosmos DB konzisztenciaszintjeinek közötti megfeleltetés

Eltérően AzureCosmos DB Apache Cassandra nem natív módon biztosít pontosan definiált és garantált adatkonzisztenciát biztosítanak.  Ehelyett az Apache Cassandra biztosít a konzisztenciaszint írási és olvasási konzisztenciaszint ahhoz, hogy a magas rendelkezésre állás, a konzisztencia és a késleltetés és kompromisszumot kínál a. Ha az Azure Cosmos DB Cassandra API-val: 

* Az Apache Cassandra írási konzisztencia szintjét az alapértelmezett konzisztenciaszint a következőn: az Azure Cosmos-fiók van hozzárendelve. 

* Az Azure Cosmos DB dinamikusan leképezése a Cassandra ügyfél illesztőprogram egy konfigurált dinamikusan egy olvasási kérést az Azure Cosmos DB konzisztenciaszint által meghatározott olvasás következetes szint. 

Az alábbi táblázat mutatja be, hogyan Cassandra natív konzisztenciaszintekről vannak leképezve konzisztenciaszintek az Azure Cosmos DB Cassandra API használatakor:  

[![Cassandra konzisztencia modell leképezés](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Mongodb-hez és az Azure Cosmos DB konzisztenciaszintjeinek közötti megfeleltetés

A natív MongoDB eltérően az Azure Cosmos dB-ben nem tartalmaz pontosan a meghatározott konzisztenciagaranciákat. Ehelyett natív MongoDB lehetővé teszi a felhasználók számára a következő konzisztenciagaranciákat: jelent problémát egy írási, olvasási szempont és: isMaster direktiva – közvetlen elérése érdekében a kívánt konzisztenciaszint elsődleges vagy másodlagos replikákra az olvasási műveletek. 

Azure Cosmos DB API a mongodb-hez használatakor a MongoDB-illesztőprogram kezeli az írási régiót az elsődleges replika, és minden más régiókban olvasható replika. Kiválaszthatja, hogy melyik elsődleges replikaként az Azure Cosmos-fiókjához társított régiót. 

Mongodb-hez készült Azure Cosmos DB API használatakor:

* Az írási szempont az alapértelmezett konzisztenciaszint a következőn: az Azure Cosmos-fiók van hozzárendelve.
 
* Az Azure Cosmos DB dinamikusan leképezése az olvasási szempont a MongoDB ügyfél illesztőprogramot, amely dinamikusan konfigurálva egy olvasási kérést az Azure Cosmos DB konzisztenciaszintekről egyik által megadott. 

* Egy adott régióban, az Azure Cosmos-fiók "Master" azáltal, hogy a régióban, mint az első írható régióba tartozó is jegyzettel láthatja el. 

Az alábbi táblázat bemutatja, hogyan a natív MongoDB írási/olvasási aggályokat vannak leképezve az Azure Cosmos konzisztenciaszintek mongodb-hez készült Azure Cosmos DB API használatakor:

[![MongoDB konzisztencia modell leképezés](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>További lépések

További információ a konzisztenciaszintek és az Azure Cosmos DB API-k a nyílt forráskódú API-kkal közötti kompatibilitást. Lásd az alábbi cikkeket:

* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)
* [A mongodb-hez az Azure Cosmos DB API által támogatott MongoDB-funkciók](mongodb-feature-support.md)
* [Az Azure Cosmos DB Cassandra API által támogatott Apache Cassandra-funkciók](cassandra-support.md)