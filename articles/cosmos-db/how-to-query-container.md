---
title: Tárolók lekérdezése az Azure Cosmos DB-ben
description: Megismerheti, hogyan kérdezhet le tárolókat az Azure Cosmos DB-ben
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 5d64aa8b50cdde23d1bb8980510cfac202204f9a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51262454"
---
# <a name="query-containers-in-azure-cosmos-db"></a>Tárolók lekérdezése az Azure Cosmos DB-ben

Ez a cikk ismerteti, hogyan kérdezhet le egy (gyűjtemény, gráf vagy táblázat típusú) tárolót az Azure Cosmos DB-ben.

## <a name="in-partition-query"></a>Partíción belüli lekérdezés

A tárolók adatainak lekérdezésekor a Cosmos DB automatikusan átirányítja a lekérdezést a szűrőben meghatározott partíciókulcs-értékeknek megfelelő partíciókhoz (ha vannak ilyenek). Ezt a lekérdezést például az „XMS-0001” partíciókulcsot tartalmazó partícióhoz irányítja.

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

- A **MaxDegreeOfParallelism**: beállítja a tároló partícióihoz egyszerre létrehozott hálózati kapcsolatok maximális számát. Ha ennek a tulajdonságnak az értéke -1, a párhuzamosság szintjét az SDK felügyeli. Ha a MaxDegreeOfParallelism érték nincs megadva, vagy az értéke 0 (ez az alapértelmezett érték), akkor csak egy hálózati kapcsolat jön létre a tároló partícióihoz.

- **MaxBufferedItemCount**: kompromisszumot alakít ki a lekérdezések késése és az ügyféloldali memóriahasználat között. Ha kihagyja ezt a beállítást, vagy -1 értéket ad meg, akkor a párhuzamos lekérdezés-végrehajtás során pufferelt elemek számát az SDK felügyeli.

Ha a gyűjtemény állapota azonos, a párhuzamos lekérdezés ugyanazon sorrendben adja vissza az értékeket, mint egy soros lekérdezés esetén. A rendezési operátorokat (ORDER BY és/vagy TOP) is tartalmazó, több partícióra kiterjedő lekérdezések végrehajtásakor az Azure Cosmos DB SDK párhuzamosan hajtja végre a lekérdezéseket a partíciókon, majd egyesíti a részlegesen tárolt eredményeket az ügyféloldalon, ezáltal globálisan rendezett eredményeket ad vissza.

## <a name="next-steps"></a>További lépések

A Cosmos DB-ben történő particionálásról a következő cikkekben talál további információt:

- [Particionálás az Azure Cosmos DB-ben](partitioning-overview.md)
- [Szintetikus partíciókulcsok az Azure Cosmos DB-ben](synthetic-partition-keys.md)
