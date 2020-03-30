---
title: Adatok létrehozása vagy beszúrása az Azure Cosmos DB Cassandra API-ba a Sparkból
description: Ez a cikk bemutatja, hogyan szúrhat be mintaadatokat az Azure Cosmos DB Cassandra API-táblákba
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 3eb23a3d8b1098110bd8b75faa22cc483637d183
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442123"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Adatok létrehozása/beszúrása az Azure Cosmos DB Cassandra API-ba a Sparkból
 
Ez a cikk ismerteti, hogyan szúrhat be mintaadatokat egy táblába az Azure Cosmos DB Cassandra API-t a Sparkból.

## <a name="cassandra-api-configuration"></a>Cassandra API-konfiguráció

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
## <a name="dataframe-api"></a>Dataframe API

### <a name="create-a-dataframe-with-sample-data"></a>Adatkeret létrehozása mintaadatokkal

```scala
// Generate a dataframe containing five records
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
).toDF("book_id", "book_author", "book_name", "book_pub_year")

//Review schema
booksDF.printSchema

//Print
booksDF.show
```

> [!NOTE]
> A "Létrehozás, ha nem létezik" funkció sorszinten még nem támogatott.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Az Azure Cosmos DB Cassandra API megmaradása

Adatok mentésekor az élő és konzisztencia-házirend-beállításokat is beállíthatja az alábbi példában látható módon:

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> Az oszlopszintű TTL még nem támogatott.

#### <a name="validate-in-cqlsh"></a>Érvényesítés cqlsh-ban

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>Rugalmas elosztott adatbázis (RDD) API

### <a name="create-a-rdd-with-sample-data"></a>RdD létrehozása mintaadatokkal
```scala
//Delete records created in the previous section 
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("delete from books_ks.books where book_id in ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999');"))

//Create RDD
val booksRDD = sc.parallelize(Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901)
))

//Review
booksRDD.take(2).foreach(println)
```

> [!NOTE]
> A create if not exists funkció még nem támogatott.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Az Azure Cosmos DB Cassandra API megmaradása

Amikor adatokat ment a Cassandra API-ba, az élő és konzisztencia-házirend-beállításokat is beállíthatja az alábbi példában látható módon:

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>Érvényesítés cqlsh-ban

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>További lépések

Miután beilleszti az adatokat az Azure Cosmos DB Cassandra API-táblába, folytassa a következő cikkekkel a Cosmos DB Cassandra API-ban tárolt adatokon tárolt egyéb műveletek végrehajtásához:
 
* [Olvasási műveletek](cassandra-spark-read-ops.md)
* [Upsert műveletek](cassandra-spark-upsert-ops.md)
* [Műveletek törlése](cassandra-spark-delete-ops.md)
* [Összesítési műveletek](cassandra-spark-aggregation-ops.md)
* [Táblamásolási műveletek](cassandra-spark-table-copy-ops.md)

