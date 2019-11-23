---
title: Tárolók lekérdezése az Azure Cosmos DB-ben
description: Megismerheti, hogyan kérdezhet le tárolókat az Azure Cosmos DB-ben
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: mjbrown
ms.openlocfilehash: 799fa43ad6ff12e5fa84326cbb41842e76daff12
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70092969"
---
# <a name="query-an-azure-cosmos-container"></a>Azure Cosmos-tároló lekérdezése

Ez a cikk azt ismerteti, hogyan lehet lekérdezni egy tárolót (gyűjtemény, gráf vagy tábla) a Azure Cosmos DBban.

## <a name="in-partition-query"></a>Partíción belüli lekérdezés

Ha tárolóból kérdez le adatait, ha a lekérdezéshez meg van adva egy partíciós kulcs szűrő, Azure Cosmos DB automatikusan kezeli a lekérdezést. A lekérdezés a szűrőben megadott partíciós kulcs értékeinek megfelelő partíciókhoz irányítja a lekérdezést. Például a következő lekérdezés a `DeviceId` partícióra van irányítva, amely a partíciós kulcs értékének megfelelő összes dokumentumot tartalmazza `XMS-0001`.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Többpartíciós kiterjedő lekérdezés

A következő lekérdezés nem rendelkezik szűrővel a partíciós kulcson (`DeviceId`), és az összes partícióra partíciókulcsra, ahol a partíció indexén fut. A partíciók közötti lekérdezés futtatásához állítsa a `EnableCrossPartitionQuery` igaz értékre (vagy `x-ms-documentdb-query-enablecrosspartition` a REST API).

A EnableCrossPartitionQuery tulajdonság egy logikai értéket fogad el. Ha igaz értékre van állítva, és ha a lekérdezés nem rendelkezik partíciós kulccsal, Azure Cosmos DB a partíciók közötti lekérdezést. A ventilátort úgy teheti meg, hogy egyéni lekérdezéseket bocsát ki az összes partícióra. A lekérdezés eredményeinek olvasásához az ügyfélalkalmazások a FeedResponse származó eredményeket használják, és a Continuationtoken argumentumot használja tulajdonságot kell megkeresni. Az összes eredmény beolvasásához tartsa ismétlődően az értékeket, amíg a Continuationtoken argumentumot használja null nem lesz. 

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Azure Cosmos DB támogatja az összesítő függvények DARABSZÁMát, a MIN, a MAXIMUMot és az ÁTLAGot a tárolók számára az SQL használatával. A tárolók összesített függvényei az SDK-verzió 1.12.0 és újabb verziójától kezdve. A lekérdezéseknek tartalmazniuk kell egyetlen aggregált operátort, és tartalmaznia kell egy értéket a vetítésben.

## <a name="parallel-cross-partition-query"></a>Párhuzamos többpartíciós lekérdezés

Az Azure Cosmos DB SDK-k 1.9.0, és később támogatják a párhuzamos lekérdezés-végrehajtási lehetőségeket. A párhuzamos többpartíciós lekérdezésekkel kis késésű többpartíciós lekérdezések hajthatók végre. A következő lekérdezés például a partíciókon való párhuzamos futtatásra van konfigurálva.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

A lekérdezések párhuzamos végrehajtását az alábbi paraméterek beállításával kezelheti:

- A **MaxDegreeOfParallelism**: beállítja a tároló partícióihoz egyszerre létrehozott hálózati kapcsolatok maximális számát. Ha a tulajdonságot-1 értékre állítja, az SDK kezeli a párhuzamosság mértékét. Ha a `MaxDegreeOfParallelism` nincs megadva, vagy 0 értékre van állítva, amely az alapértelmezett érték, akkor a tároló partícióinak egyetlen hálózati kapcsolatai vannak.

- **MaxBufferedItemCount**: kompromisszumot alakít ki a lekérdezések késése és az ügyféloldali memóriahasználat között. Ha ez a beállítás nincs megadva, vagy az-1 értékre van állítva, az SDK kezeli a párhuzamos lekérdezés végrehajtása során pufferelt elemek számát.

A gyűjtemény azonos állapotában a párhuzamos lekérdezés a soros végrehajtással megegyező sorrendben adja vissza az eredményeket. Ha több partíciós lekérdezést is végrehajt, amely tartalmazza a rendezési operátorokat (ORDER BY, TOP), akkor a Azure Cosmos DB SDK párhuzamosan kiadja a lekérdezést a partíciók között. A részben rendezett eredményeket egyesíti az ügyfél oldalán, hogy globálisan rendezett eredményeket hozzon létre.

## <a name="next-steps"></a>Következő lépések

A particionálással kapcsolatos tudnivalókat az alábbi cikkekben találja Azure Cosmos DB:

- [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
- [Szintetikus partíciókulcsok az Azure Cosmos DB-ben](synthetic-partition-keys.md)
