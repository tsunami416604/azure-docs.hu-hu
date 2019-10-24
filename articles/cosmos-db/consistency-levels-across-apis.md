---
title: Konzisztenciaszintek és Azure Cosmos DB API-k
description: A Azure Cosmos DB API-k közötti konzisztencia-szintek megismerése.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 6779e434339935b64440d7cf617c8946338e36e3
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756982"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konzisztenciaszintek és Azure Cosmos DB API-k

A Azure Cosmos DB natív támogatást biztosít a huzal protokollal kompatibilis API-k számára a népszerű adatbázisokhoz. Ilyenek például a MongoDB, az Apache Cassandra, a Gremlin és az Azure Table Storage. Ezek az adatbázisok nem biztosítanak pontosan meghatározott konzisztencia-modelleket vagy SLA-alapú garanciákat a konzisztencia szintjéhez. Általában csak a Azure Cosmos DB által kínált öt konzisztencia-modell részhalmazát biztosítják. 

Az SQL API, a Gremlin API és a Table API használatakor a rendszer az Azure Cosmos-fiókban konfigurált alapértelmezett konzisztencia-szintet használja. 

Ha Cassandra API vagy Azure Cosmos DB API-ját használja a MongoDB, az alkalmazások az Apache Cassandra és a MongoDB által kínált összes konzisztencia-készletet megkapják, ami még erősebb következetességet és tartósságot garantál. Ez a dokumentum az Apache Cassandra és a MongoDB konzisztencia-szintjeinek megfelelő Azure Cosmos DB következetességi szinteket mutatja.


## <a id="cassandra-mapping"></a>Az Apache Cassandra és a Azure Cosmos DB konzisztencia-szintjei közötti leképezés

A AzureCosmos DB-vel ellentétben az Apache Cassandra nem biztosít natív módon pontosan meghatározott konzisztencia-garanciákat.  Az Apache Cassandra Ehelyett írási konzisztencia-szintet és olvasási konzisztencia-szintet biztosít a magas rendelkezésre állás, a konzisztencia és a késési kompromisszumok engedélyezéséhez. Azure Cosmos DB Cassandra API használatakor: 

* Az Apache Cassandra írási konzisztencia-szintje az Azure Cosmos-fiókban konfigurált alapértelmezett konzisztencia-szintre van leképezve. 

* Azure Cosmos DB dinamikusan leképezi a Cassandra-ügyfél illesztőprogramjában megadott olvasási konzisztencia-szintet az olvasási kérelemben dinamikusan konfigurált Azure Cosmos DB konzisztencia-szintek egyikére. 

Az alábbi táblázat azt mutatja be, hogy a natív Cassandra konzisztencia-szintjei hogyan vannak leképezve a Azure Cosmos DB konzisztencia-szintjére Cassandra API használatakor:  

[![Cassandra konzisztencia-modell leképezése](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>A MongoDB és a Azure Cosmos DB konzisztencia-szintjei közötti megfeleltetés

A Azure Cosmos DBtól eltérően a natív MongoDB nem biztosít pontosan meghatározott konzisztencia-garanciát. Ehelyett a natív MongoDB lehetővé teszi a felhasználók számára a következő konzisztencia-garanciák konfigurálását: egy írási aggály, egy olvasási probléma és a isMaster direktíva, hogy az olvasási műveleteket elsődleges vagy másodlagos replikára irányítsa a kívánt konzisztencia-szint elérése érdekében. 

Ha Azure Cosmos DB API-ját használja a MongoDB, a MongoDB-illesztőprogram az írási régiót elsődleges replikaként kezeli, és minden más régió olvasási replika. Kiválaszthatja, hogy melyik régiót rendeli hozzá az Azure Cosmos-fiókjához elsődleges replikaként. 

A MongoDB Azure Cosmos DB API-ját használja:

* Az írási aggály az Azure Cosmos-fiókban konfigurált alapértelmezett konzisztencia-szintre van leképezve.
 
* Azure Cosmos DB dinamikusan leképezi a MongoDB-ügyfél illesztőprogramja által megadott olvasási aggályt az olvasási kérelemben dinamikusan konfigurált Azure Cosmos DB konzisztencia-szintek egyikére. 

* Az Azure Cosmos-fiókjához tartozó adott régiót a "Master" kifejezéssel jegyzetekkel láthatja el, ha az első írható régióként teszi a régiót. 

Az alábbi táblázat bemutatja, hogyan vannak leképezve a natív MongoDB írási/olvasási problémái az Azure Cosmos konzisztencia-szintjére, ha Azure Cosmos DB API-ját használja a MongoDB számára:

[![MongoDB konzisztencia-modell leképezése](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Következő lépések

További információ a konzisztencia szintjeiről és a Azure Cosmos DB API-k és a nyílt forráskódú API-k közötti kompatibilitásról. Lásd az alábbi cikkeket:

* [Rendelkezésre állási és teljesítménybeli kompromisszumok különböző konzisztencia-szintekhez](consistency-levels-tradeoffs.md)
* [A Azure Cosmos DB API-MongoDB által támogatott MongoDB-funkciók](mongodb-feature-support.md)
* [Az Azure Cosmos DB által támogatott Apache Cassandra-funkciók Cassandra API](cassandra-support.md)