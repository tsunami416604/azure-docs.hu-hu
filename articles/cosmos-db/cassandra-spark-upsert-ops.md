---
title: Azure Cosmos DB Cassandra API Spark rendszerből Upsert adattárházba
description: Ez a cikk ismerteti az Azure Cosmos DB Cassandra API táblákba upsert hogyan Spark rendszerből
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 7770e7fbe846defc865b3fcc702fcb00bae1b73c
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54033696"
---
# <a name="upsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Azure Cosmos DB Cassandra API Spark rendszerből Upsert adattárházba

Ez a cikk azt ismerteti, hogyan upsert adatok importálása az Azure Cosmos DB Cassandra API Spark rendszerből.

## <a name="cassandra-api-configuration"></a>Cassandra API konfigurálása

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")
//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="dataframe-api"></a>Adathalmaz API

### <a name="create-a-dataframe"></a>Hozzon létre dataframe- 

```scala
// (1) Update: Changing author name to include prefix of "Sir"
// (2) Insert: adding a new book

val booksUpsertDF = Seq(
    ("b00001", "Sir Arthur Conan Doyle", "A study in scarlet", 1887),
    ("b00023", "Sir Arthur Conan Doyle", "A sign of four", 1890),
    ("b01001", "Sir Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
    ("b00501", "Sir Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
    ("b00300", "Sir Arthur Conan Doyle", "The hounds of Baskerville", 1901),
    ("b09999", "Sir Arthur Conan Doyle", "The return of Sherlock Holmes", 1905)
    ).toDF("book_id", "book_author", "book_name", "book_pub_year")
booksUpsertDF.show()
```

### <a name="upsert-data"></a>Adatok beszúrása és frissítése (upsert)

```scala
// Upsert is no different from create
booksUpsertDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .save()
```

### <a name="update-data"></a>Adatok frissítése

```scala
//This runs on the driver, leverage only for one off updates
cdbConnector.withSessionDo(session => session.execute("update books_ks.books set book_price=99.33 where book_id ='b00300';"))
```

## <a name="rdd-api"></a>RDD-API
> [!NOTE]
> Az RDD-API-ból Upsert megegyezik a létrehozási művelet 

## <a name="next-steps"></a>További lépések

Folytassa a következő cikkek az Azure Cosmos DB Cassandra API-táblákban tárolt adatokkal kapcsolatos egyéb műveletek végrehajtásához:
 
* [Törlési műveletek](cassandra-spark-delete-ops.md)
* [Összesítési műveletek](cassandra-spark-aggregation-ops.md)
* [Tábla másolási műveletek](cassandra-spark-table-copy-ops.md)
