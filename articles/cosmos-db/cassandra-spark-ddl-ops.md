---
title: Az Azure Cosmos DB Cassandra API a Spark DDL-műveletek
description: Ez a cikk részletesen kulcstér és tábla ellen az Azure Cosmos DB Cassandra API a Spark DDL-műveletek.
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: a799d85cc27575badda6892ba7baf68ca1eb1dfb
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47226075"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>Az Azure Cosmos DB Cassandra API a Spark DDL-műveletek

Ez a cikk részletesen kulcstér és tábla ellen az Azure Cosmos DB Cassandra API a Spark DDL-műveletek.

## <a name="cassandra-api-related-configuration"></a>Cassandra API-kapcsolódó konfiguráció 

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

## <a name="keyspace-ddl-operations"></a>Kulcstér DDL-műveletek

### <a name="create-a-keyspace"></a>Hozzon létre egy kulcstér

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Ellenőrizze az cqlsh

Futtassa a következő parancsot a cqlsh, és a korábban létrehozott kulcstér kell megjelennie.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Dobja el a kulcstér

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Ellenőrizze az cqlsh

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>TABLE DDL-műveletek

**Megfontolandó szempontok:**  

- Átviteli sebesség a create table utasítás használatával a tábla szintjén is hozzárendelhető.  
- A partíciókulcs egy 10 GB adatot tárolhat.  
- Egy rekord egy legfeljebb 2 MB adatot képes tárolni.  
- Egy partíciókulcs-tartományok tárolhat több partíciós kulccsal.

### <a name="create-a-table"></a>Tábla létrehozása

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Ellenőrizze az cqlsh

Futtassa a következő parancsot a cqlsh, és megjelenik a táblázat neve "könyvek: 

```bash
USE books_ks;
DESCRIBE books;
```

Kiosztott átviteli sebesség és az alapértelmezett élettartam-értékek nem jelennek meg az előző parancs kimenetét, ezeket az értékeket is beszerzése a portálról.

### <a name="alter-table"></a>Az ALTER table

Az alter table parancs használatával módosítható a következő értékeket:

* kiosztott átviteli sebesség 
* idő-érték
<br>Oszlop módosítása jelenleg nem támogatott.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>DROP table

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Ellenőrizze az cqlsh

Futtassa a következő parancsot a cqlsh, és megjelenik, hogy a "könyvek" tábla már nem érhető el:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>További lépések

Miután létrehozta a kulcstér és a tábla, folytassa a következő cikkek CRUD-műveleteket és más rendszerekhez:
 
* [Hozzon létre/beszúrási műveletek](cassandra-spark-create-ops.md)  
* [olvasási műveletek](cassandra-spark-read-ops.md)  
* [Upsert művelet](cassandra-spark-upsert-ops.md)  
* [Törlési műveletek](cassandra-spark-delete-ops.md)  
* [Összesítési műveletek](cassandra-spark-aggregation-ops.md)  
* [Tábla másolási műveletek](cassandra-spark-table-copy-ops.md)  
