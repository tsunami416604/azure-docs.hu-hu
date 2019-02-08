---
title: Tárolók lekérdezése az Azure Cosmos DB-ben
description: Megismerheti, hogyan kérdezhet le tárolókat az Azure Cosmos DB-ben
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: f7536b5d0815351d2e6cb67705060d2e1046c970
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55857872"
---
# <a name="query-an-azure-cosmos-container"></a>A lekérdezés egy Azure Cosmos-tárolóhoz

Ez a cikk bemutatja, hogyan kérdezhet le egy tárolót az Azure Cosmos DB (gyűjtemény, graph vagy tábla).

## <a name="in-partition-query"></a>Partíción belüli lekérdezés

Tárolók, adatokat kérdezhet le, ha a lekérdezés a partíció megadott szűrőjének, amikor az Azure Cosmos DB automatikusan kezeli a lekérdezést. A lekérdezés a partíciós kulcs szűrőben megadott értékeknek megfelelő partíciókhoz irányítja. Ha például a következő lekérdezés irányítja a rendszer a `DeviceId` partíció található, a megfelelő partíciókulcs-értékkel dokumentumokat tartalmazó `XMS-0001`.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Többpartíciós kiterjedő lekérdezés

A következő lekérdezés nem tartalmaz egy szűrőt a partíciókulcs (`DeviceId`), és az összes partíciót, amelyen fut a partíció indexe alapján van rendezve. Több partícióra kiterjedő lekérdezések futtatása, állítsa be `EnableCrossPartitionQuery` igaz (vagy `x-ms-documentdb-query-enablecrosspartition`  a REST API-ban).

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Az Azure Cosmos DB támogatja az aggregátumfüggvények COUNT, MIN, MAX és átlagos keresztül tárolók SQL használatával. Az aggregátumfüggvények keresztül tárolók kezdődően az SDK-verzió 1.12.0 és újabb verziók. Lekérdezések tartalmaznia kell egy egyetlen összesítő operátort, és egyetlen értéket kell adni a leképezésben.

## <a name="parallel-cross-partition-query"></a>Párhuzamos többpartíciós lekérdezés

Azure Cosmos DB SDK 1.9.0-s és újabb verziói támogatják a párhuzamos lekérdezés-végrehajtási lehetőségeket. A párhuzamos többpartíciós lekérdezésekkel kis késésű többpartíciós lekérdezések hajthatók végre. A következő lekérdezés például a partíciókon való párhuzamos futtatásra van konfigurálva.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

A lekérdezések párhuzamos végrehajtását az alábbi paraméterek beállításával kezelheti:

- **MaxDegreeOfParallelism**: A tároló partíciók állítja be egyidejű hálózati kapcsolatok maximális számát. Ha ez a tulajdonság a -1, az SDK kezeli a párhuzamosság foka. Ha a `MaxDegreeOfParallelism` nem megadott, vagy az értéke 0, amely az alapértelmezett érték, egy egyetlen hálózati kapcsolat a tároló partíciókra.

- **MaxBufferedItemCount**: Ügyletek ügyféloldali memóriahasználat és a késés lekérdezése. Ha kihagyja ezt a beállítást, vagy a -1 értékre állítva, az SDK kezeli a párhuzamos lekérdezés-végrehajtás során pufferelt elemek számát.

A gyűjtemény állapota azonos, a a párhuzamos lekérdezés ugyanazon sorrendben, mint a soros lekérdezés eredményeket ad vissza. A rendezést (ORDER BY, TOP) operátorok tartalmazó partícióra kiterjedő lekérdezések végrehajtásakor az Azure Cosmos DB SDK bocsát ki a párhuzamos lekérdezés partíciók között. Egyesíti a részlegesen tárolt eredményeket az ügyféloldalon, globálisan rendezett eredményeket.

## <a name="next-steps"></a>További lépések

További információ az Azure Cosmos DB particionálási a következő cikkekben talál:

- [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
- [Szintetikus partíciókulcsok az Azure Cosmos DB-ben](synthetic-partition-keys.md)
