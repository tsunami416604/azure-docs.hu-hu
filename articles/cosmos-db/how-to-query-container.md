---
title: Tárolók lekérdezése az Azure Cosmos DB-ben
description: Megismerheti, hogyan kérdezhet le tárolókat az Azure Cosmos DB-ben
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 08d9978134ce214a468691ec367fb1797f6e86fc
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457751"
---
# <a name="query-an-azure-cosmos-container"></a>A lekérdezés egy Azure Cosmos-tárolóhoz

Ez a cikk ismerteti, hogyan kérdezhet le egy (gyűjtemény, gráf vagy táblázat típusú) tárolót az Azure Cosmos DB-ben.

## <a name="in-partition-query"></a>Partíción belüli lekérdezés

Tárolók, adatokat kérdezhet le, ha a lekérdezés a partíció megadott szűrőjének, amikor az Azure Cosmos DB automatikusan átirányítja a lekérdezést a szűrőben meghatározott partíciós kulcsérték-értékeknek megfelelő partíciókhoz. A következő lekérdezés például az "XMS-0001" partíciókulcs-értékkel tartozó összes dokumentumot tartalmazó DeviceId partíció lesz irányítva.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Többpartíciós kiterjedő lekérdezés

A következő lekérdezés nem rendelkezik a partíciókulcsra (DeviceId) vonatkozó szűrővel, és minden partícióra kiterjed, ahol a partíció indexén lesz végrehajtva. A többpartíciós lekérdezés végrehajtásához állítsa az **EnableCrossPartitionQuery** értéket igazra (vagy adja meg az x-ms-documentdb-query-enablecrosspartition értéket a REST API-ban).

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

A Cosmos DB az SQL használatával támogatja a COUNT, MIN, MAX és AVG összesítő függvényeket a tárolókban. A tárolókra vonatkozó összesítő függvények az SDK 1.12.0-s és újabb verzióiban használhatók. A lekérdezéseknek egyetlen összesítő operátort kell tartalmazniuk, valamint egyetlen értéket a vetítésben.

## <a name="parallel-cross-partition-query"></a>Párhuzamos többpartíciós lekérdezés

A Cosmos DB SDK 1.9.0-s és újabb verziói támogatják a párhuzamos lekérdezés-végrehajtási lehetőségeket.  A párhuzamos többpartíciós lekérdezésekkel kis késésű többpartíciós lekérdezések hajthatók végre. A következő lekérdezés például a partíciókon való párhuzamos futtatásra van konfigurálva.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

A lekérdezések párhuzamos végrehajtását az alábbi paraméterek beállításával kezelheti:

- **MaxDegreeOfParallelism**: A tároló partíciók állítja be egyidejű hálózati kapcsolatok maximális számát. Ha ennek a tulajdonságnak az értéke -1, a párhuzamosság szintjét az SDK felügyeli. Ha a MaxDegreeOfParallelism érték nincs megadva, vagy az értéke 0 (ez az alapértelmezett érték), akkor csak egy hálózati kapcsolat jön létre a tároló partícióihoz.

- **MaxBufferedItemCount**: Ügyletek ügyféloldali memóriahasználat és a késés lekérdezése. Ha kihagyja ezt a beállítást, vagy -1 értéket ad meg, akkor a párhuzamos lekérdezés-végrehajtás során pufferelt elemek számát az SDK felügyeli.

Ha a gyűjtemény állapota azonos, a párhuzamos lekérdezés ugyanazon sorrendben adja vissza az értékeket, mint egy soros lekérdezés esetén. A rendezési operátorokat (ORDER BY és/vagy TOP) is tartalmazó, több partícióra kiterjedő lekérdezések végrehajtásakor az Azure Cosmos DB SDK párhuzamosan hajtja végre a lekérdezéseket a partíciókon, majd egyesíti a részlegesen tárolt eredményeket az ügyféloldalon, ezáltal globálisan rendezett eredményeket ad vissza.

## <a name="next-steps"></a>További lépések

A Cosmos DB-ben történő particionálásról a következő cikkekben talál további információt:

- [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
- [Szintetikus partíciókulcsok az Azure Cosmos DB-ben](synthetic-partition-keys.md)
