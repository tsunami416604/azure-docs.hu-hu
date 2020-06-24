---
title: Azure Cosmos DB analitikus áruház (előzetes verzió) lekérdezése az Azure szinapszis Analytics Apache Sparkával
description: Azure Cosmos DB analitikus lekérdezése az Azure szinapszis Analytics Apache Spark használatával
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 05/06/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 399aa00be3d585b1d37bab95d8108ee8a09a8791
ms.sourcegitcommit: 3988965cc52a30fc5fed0794a89db15212ab23d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2020
ms.locfileid: "85195011"
---
# <a name="query-azure-cosmos-db-analytical-store-preview-with-apache-spark-for-azure-synapse-analytics"></a>Azure Cosmos DB analitikus áruház (előzetes verzió) lekérdezése az Azure szinapszis Analytics Apache Sparkával

Ez a cikk példákat mutat be arra, hogyan használhatja az analitikai tárolót a szinapszis-kézmozdulatokkal. A kézmozdulatok akkor láthatók, ha a jobb egérgombbal egy tárolóra kattint. A kézmozdulatokkal gyorsan hozhat létre kódot, és testre szabhatja azt az igényei szerint. A kézmozdulatok az adatok egy kattintással való felderítéséhez is ideálisan használhatók.

## <a name="load-to-dataframe"></a>Betöltés a DataFrame

Ebben a lépésben beolvassa a Azure Cosmos DB analitikus áruházból származó adatait egy Spark-DataFrame. Ekkor 10 sort fog megjeleníteni a ***DF***nevű DataFrame. Ha az adatai bekerültek a dataframe, további elemzést végezhet.

Ez a művelet nem érinti a tranzakciós tárolót.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

df = spark.read.format("cosmos.olap")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .load()

df.show(10)
```

A **Scala** megfelelő kód-kézmozdulata a következő kód lehet:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    load()
```

## <a name="create-spark-table"></a>Spark-tábla létrehozása

Ebben a kézmozdulatban létre fog hozni egy Spark-táblázatot, amely a kiválasztott tárolóra mutat. Ez a művelet semmilyen adatáthelyezést nem von maga után. Ha úgy dönt, hogy törli a táblát, a mögöttes tárolót (és a hozzá tartozó analitikai tárolót) nem érinti a rendszer. 

Ez a forgatókönyv lehetővé teszi a táblák harmadik féltől származó eszközökkel való újrafelhasználását, és az adatelérést a Futtatás ideje alatt elérhetővé teszi.

```sql
%%sql
-- To select a preferred list of regions in a multi-region Azure Cosmos DB account, add spark.cosmos.preferredRegions '<Region1>,<Region2>' in the config options

create table call_center using cosmos.olap options (
    spark.synapse.linkedService 'INFERRED',
    spark.cosmos.container 'INFERRED'
)
```

## <a name="write-dataframe-to-container"></a>DataFrame írása tárolóba

Ebben a kézmozdulatban egy dataframe fog írni egy tárolóba. Ez a művelet hatással lesz a tranzakciós teljesítményre és a kérelmek mennyiségének felhasználására. Az Azure Cosmos DB tranzakciós teljesítmény használata ideális írási tranzakcióhoz. Győződjön meg arról, hogy a **YOURDATAFRAME** lecseréli a dataframe, amelyet vissza szeretne írni.

```python
# Write a Spark DataFrame into an Azure Cosmos DB container
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")


YOURDATAFRAME.write.format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .mode('append')\
    .save()
```

A **Scala** megfelelő kód-kézmozdulata a következő kód lehet:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

import org.apache.spark.sql.SaveMode

df.write.format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl"). 
    option("spark.cosmos.write.upsertEnabled", "true").
    mode(SaveMode.Overwrite).
    save()
```

## <a name="load-streaming-dataframe-from-container"></a>Adatfolyam-DataFrame betöltése a tárolóból
Ebben a kézmozdulatban a Spark streaming funkciót fogja használni az adatok egy tárolóból egy dataframe való betöltéséhez. Az adattárolást a rendszer a munkaterülethez csatlakoztatott elsődleges adatlake-fiókban (és fájlrendszerben) tárolja. 

Ha a */localReadCheckpointFolder* mappa nincs létrehozva, a rendszer automatikusan létrehozza. Ez a művelet hatással lesz Azure Cosmos DB tranzakciós teljesítményére.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

dfStream = spark.readStream\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "INFERRED")\
    .option("spark.cosmos.container", "INFERRED")\
    .option("spark.cosmos.changeFeed.readEnabled", "true")\
    .option("spark.cosmos.changeFeed.startFromTheBeginning", "true")\
    .option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder")\
    .option("spark.cosmos.changeFeed.queryName", "streamQuery")\
    .load()
```

A **Scala** megfelelő kód-kézmozdulata a következő kód lehet:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val dfStream = spark.readStream.
    format("cosmos.oltp").
    option("spark.synapse.linkedService", "pySparkSamplesDb").
    option("spark.cosmos.container", "trafficSourceColl").
    option("spark.cosmos.changeFeed.readEnabled", "true").
    option("spark.cosmos.changeFeed.startFromTheBeginning", "true").
    option("spark.cosmos.changeFeed.checkpointLocation", "/localReadCheckpointFolder").
    option("spark.cosmos.changeFeed.queryName", "streamTestRevin2").
    load()
```

## <a name="write-streaming-dataframe-to-container"></a>Adatfolyam-DataFrame írása tárolóba
Ebben a kézmozdulatban egy streaming dataframe fog írni a kiválasztott Azure Cosmos DB-tárolóba. Ha a */localReadCheckpointFolder* mappa nincs létrehozva, a rendszer automatikusan létrehozza. Ez a művelet hatással lesz Azure Cosmos DB tranzakciós teljesítményére.

```python
# To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

streamQuery = dfStream\
        .writeStream\
        .format("cosmos.oltp")\
        .outputMode("append")\
        .option("checkpointLocation", "/localWriteCheckpointFolder")\
        .option("spark.synapse.linkedService", "INFERRED")\
        .option("spark.cosmos.container", "trafficSourceColl_sink")\
        .option("spark.cosmos.connection.mode", "gateway")\
        .start()

streamQuery.awaitTermination()
```

A **Scala** megfelelő kód-kézmozdulata a következő kód lehet:
```java
// To select a preferred list of regions in a multi-region Azure Cosmos DB account, add .option("spark.cosmos.preferredRegions", "<Region1>,<Region2>")

val query = dfStream.
            writeStream.
            format("cosmos.oltp").
            outputMode("append").
            option("checkpointLocation", "/localWriteCheckpointFolder").
            option("spark.synapse.linkedService", "pySparkSamplesDb").
            option("spark.cosmos.container", "test2").
            option("spark.cosmos.connection.mode", "gateway").
            start()

query.awaitTermination()
```
## <a name="next-steps"></a>További lépések

* [Ismerkedjen meg a szinapszis és a Azure Cosmos DB között támogatottak között](./concept-synapse-link-cosmos-db-support.md)
* [Csatlakozás a szinapszis hivatkozásához Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md)
