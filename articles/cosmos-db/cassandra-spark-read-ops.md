---
title: Olvassa el az Azure cosmos DB Cassandra API-val kapcsolatos művelet-végrehajtási Spark rendszerből
description: Ez a cikk az, Cosmos DB Cassandra API táblájából olvasása
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: ef5f1a3479080aaab665a449bbf946a29344f26c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224980"
---
# <a name="read-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Olvassa el az Azure Cosmos DB Cassandra API táblák Spark rendszerből

 Ez a cikk ismerteti, hogyan olvashatja be a Spark az Azure Cosmos DB Cassandra API-ban tárolt adatokkal.

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

### <a name="read-table-using-sessionreadformat-command"></a>Olvasási tábla session.read.format paranccsal

```scala
val readBooksDF = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load

readBooksDF.explain
readBooksDF.show
```
### <a name="read-table-using-sparkreadcassandraformat"></a>Olvasási tábla spark.read.cassandraFormat segítségével 

```scala
val readBooksDF = spark.read.cassandraFormat("books", "books_ks", "").load()
```

### <a name="read-specific-columns-in-table"></a>Olvassa el az adott tábla oszlopai

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

### <a name="apply-filters"></a>Szűrők alkalmazása

Jelenleg nem támogatott a predikátum legördülő lista, az alábbi minták az ügyféloldali szűrésen tükrözik. 

```scala
val readBooksDF = spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_author", "book_pub_year")
  .filter("book_pub_year > 1891")
//.filter("book_name IN ('A sign of four','A study in scarlet')")
//.filter("book_name='A sign of four' OR book_name='A study in scarlet'")
//.filter("book_author='Arthur Conan Doyle' AND book_pub_year=1890")
//.filter("book_pub_year=1903")  

readBooksDF.printSchema
readBooksDF.explain
readBooksDF.show
```

## <a name="rdd-api"></a>RDD-API

### <a name="read-table"></a>Olvasási tábla
```scala
val bookRDD = sc.cassandraTable("books_ks", "books")
bookRDD.take(5).foreach(println)
```

### <a name="read-specific-columns-in-table"></a>Olvassa el az adott tábla oszlopai

```scala
val booksRDD = sc.cassandraTable("books_ks", "books").select("book_id","book_name").cache
booksRDD.take(5).foreach(println)
```

## <a name="sql-views"></a>Az SQL-nézetek 

### <a name="create-a-temporary-view-from-a-dataframe"></a>A dataframe egy ideiglenes nézet létrehozása

```scala
spark
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load.createOrReplaceTempView("books_vw")
```

### <a name="run-queries-against-the-view"></a>Futtasson lekérdezéseket a nézet

```sql
select * from books_vw where book_pub_year > 1891
```

## <a name="next-steps"></a>További lépések

További cikkek az Azure Cosmos DB Cassandra API a Spark a következők:
 
 * [Upsert művelet](cassandra-spark-upsert-ops.md)
 * [Törlési műveletek](cassandra-spark-delete-ops.md)
 * [Összesítési műveletek](cassandra-spark-aggregation-ops.md)
 * [Tábla másolási műveletek](cassandra-spark-table-copy-ops.md)

