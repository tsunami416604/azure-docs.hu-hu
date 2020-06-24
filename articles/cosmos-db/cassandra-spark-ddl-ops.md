---
title: DDL-műveletek a Azure Cosmos DB Cassandra API a Sparkból
description: Ebből a cikkből megtudhatja, hogyan helyezheti el a Azure Cosmos DB Cassandra API a Sparkból a webtárhely és a táblázat DDL
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 30cac5894998ca2bb9c37217820e1000ed97ba5d
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85260560"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>DDL-műveletek a Azure Cosmos DB Cassandra API a Sparkból

Ebből a cikkből megtudhatja, hogyan helyezheti el a Azure Cosmos DB Cassandra API a Sparkból a webtárhely és a táblázat DDL

## <a name="cassandra-api-related-configuration"></a>Cassandra API kapcsolatos konfiguráció 

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

## <a name="keyspace-ddl-operations"></a>Címterület DDL-műveletei

### <a name="create-a-keyspace"></a>Szóköz létrehozása

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Érvényesítés a cqlsh

Futtassa a következő parancsot a cqlsh-ben, és látnia kell a korábban létrehozott lemezterületet.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Szóköz eldobása

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Érvényesítés a cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>Táblázatos DDL-műveletek

**Szempontok**  

- Az átviteli sebességet a CREATE TABLE utasítással lehet hozzárendelni a tábla szintjén.  
- Egy partíciós kulcs 20 GB-nyi adat tárolására képes.  
- Egy rekord legfeljebb 2 MB adat tárolására képes.  
- Egy partíciós kulcs tartománya több partíciós kulcsot is tárolhat.

### <a name="create-a-table"></a>Tábla létrehozása

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Érvényesítés a cqlsh

Futtassa a következő parancsot a cqlsh-ben, és látnia kell a "könyvek: 

```bash
USE books_ks;
DESCRIBE books;
```

A kiépített átviteli sebesség és az alapértelmezett TTL-értékek nem jelennek meg az előző parancs kimenetében, ezeket az értékeket a portálon érheti el.

### <a name="alter-table"></a>Módosítási táblázat

A következő értékeket módosíthatja az ALTER TABLE parancs használatával:

* kiosztott átviteli sebesség 
* élettartam értéke
<br>Az oszlopok módosításai jelenleg nem támogatottak.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>Táblázat eldobása

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Érvényesítés a cqlsh

Futtassa a következő parancsot a cqlsh-ben, és látnia kell, hogy a "könyvek" tábla már nem érhető el:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>További lépések

A térköz és a tábla létrehozása után folytassa a következő cikkekkel a SZIFILISZi műveletekhez és egyebekhez:
 
* [Műveletek létrehozása/beszúrása](cassandra-spark-create-ops.md)  
* [Olvasási műveletek](cassandra-spark-read-ops.md)  
* [Upsert-műveletek](cassandra-spark-upsert-ops.md)  
* [Műveletek törlése](cassandra-spark-delete-ops.md)  
* [Összesítési műveletek](cassandra-spark-aggregation-ops.md)  
* [Táblázatos másolási műveletek](cassandra-spark-table-copy-ops.md)  
