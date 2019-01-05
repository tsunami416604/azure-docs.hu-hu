---
title: Hozzon létre/beszúrási művelet adatait az Azure Cosmos DB Cassandra API Spark rendszerből
description: Ez a cikk részletesen bemutatja az Azure Cosmos DB Cassandra API táblák Mintaadat beszúrásához
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: aea646e7a390d5b53f0d4b388cfecd0c80fb19da
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54036613"
---
# <a name="createinsert-data-into-azure-cosmos-db-cassandra-api-from-spark"></a>Hozzon létre/beszúrási művelet adatait az Azure Cosmos DB Cassandra API Spark rendszerből
 
Ez a cikk ismerteti, hogyan lehet egy Azure Cosmos DB Cassandra API a Spark-táblára Mintaadat beszúrásához.

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

### <a name="create-a-dataframe-with-sample-data"></a>Hozzon létre Dataframe-mintaadatok

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
> "Létrehozása if not exists" funkciót, egy sor szintjén még nem támogatott.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Az Azure Cosmos DB Cassandra API megőrzése

Adatok mentésekor is beállíthat, time-to-live és a konzisztencia-beállítások a következő példában látható módon:

```scala
//Persist
booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

> [!NOTE]
> Oszlopszintű Élettartama még nem támogatott.

#### <a name="validate-in-cqlsh"></a>Ellenőrizze az cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="resilient-distributed-database-rdd-api"></a>Rugalmas elosztott adatbázis (RDD) API

### <a name="create-a-rdd-with-sample-data"></a>Egy RDD létrehozása mintaadatok használatával
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
> Hozzon létre, ha nem létezik a funkció még nem támogatott.

### <a name="persist-to-azure-cosmos-db-cassandra-api"></a>Az Azure Cosmos DB Cassandra API megőrzése

Cassandra API-hoz az adatok mentésekor is beállíthat, time-to-live és a konzisztencia-beállítások a következő példában látható módon:

```scala
import com.datastax.spark.connector.writer._

//Persist
booksRDD.saveToCassandra("books_ks", "books", SomeColumns("book_id", "book_author", "book_name", "book_pub_year"),writeConf = WriteConf(ttl = TTLOption.constant(900000),consistencyLevel = ConsistencyLevel.ALL))
```

#### <a name="validate-in-cqlsh"></a>Ellenőrizze az cqlsh

```sql
use books_ks;
select * from books;
```

## <a name="next-steps"></a>További lépések

Adatok beszúrása az Azure Cosmos DB Cassandra API-tábla, után folytassa a Cosmos DB Cassandra API-ban tárolt adatokkal kapcsolatos egyéb műveletek végrehajtásához a következő cikkeket:
 
* [olvasási műveletek](cassandra-spark-read-ops.md)
* [Upsert művelet](cassandra-spark-upsert-ops.md)
* [Törlési műveletek](cassandra-spark-delete-ops.md)
* [Összesítési műveletek](cassandra-spark-aggregation-ops.md)
* [Tábla másolási műveletek](cassandra-spark-table-copy-ops.md)

