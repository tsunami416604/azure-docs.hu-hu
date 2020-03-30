---
title: DDL-műveletek az Azure Cosmos DB Cassandra API-ban a Sparktól
description: Ez a cikk részletezi a keyspace és a table DDL-műveletek az Azure Cosmos DB Cassandra API-t a Sparktól.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c0df05eff5dc84ef24e1ed5afcaf705d99f447ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622580"
---
# <a name="ddl-operations-in-azure-cosmos-db-cassandra-api-from-spark"></a>DDL-műveletek az Azure Cosmos DB Cassandra API-ban a Sparktól

Ez a cikk részletezi a keyspace és a table DDL-műveletek az Azure Cosmos DB Cassandra API-t a Sparktól.

## <a name="cassandra-api-related-configuration"></a>Cassandra API-val kapcsolatos konfiguráció 

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

## <a name="keyspace-ddl-operations"></a>Kulcstér DDL-műveletei

### <a name="create-a-keyspace"></a>Kulcstér létrehozása

```scala
//Cassandra connector instance
val cdbConnector = CassandraConnector(sc)

// Create keyspace
cdbConnector.withSessionDo(session => session.execute("CREATE KEYSPACE IF NOT EXISTS books_ks WITH REPLICATION = {'class': 'SimpleStrategy', 'replication_factor': 1 } "))
```

#### <a name="validate-in-cqlsh"></a>Érvényesítés cqlsh-ban

Futtassa a következő parancsot a cqlsh-ban, és látnia kell a korábban létrehozott kulcsteret.

```bash
DESCRIBE keyspaces;
```

### <a name="drop-a-keyspace"></a>Kulcstér eldobása

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP KEYSPACE books_ks"))
```

#### <a name="validate-in-cqlsh"></a>Érvényesítés cqlsh-ban

```bash
DESCRIBE keyspaces;
```
## <a name="table-ddl-operations"></a>DDL-műveletek tábla

**Szempontok:**  

- Az átviteli átaló a tábla szintjén rendelhető hozzá a create table utasítás használatával.  
- Egy partíciókulcs 20 GB adatot képes tárolni.  
- Egy rekord legfeljebb 2 MB adatot tárolhat.  
- Egy partíciókulcs-tartomány több partíciókulcsot is képes tárolni.

### <a name="create-a-table"></a>Tábla létrehozása

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000 , WITH default_time_to_live=630720000;"))
```

#### <a name="validate-in-cqlsh"></a>Érvényesítés cqlsh-ban

Futtassa a következő parancsot a cqlsh-ban, és látnia kell a "könyvek" nevű táblát: 

```bash
USE books_ks;
DESCRIBE books;
```

A kiépített átviteli és az alapértelmezett TTL-értékek nem jelennek meg az előző parancs kimenetében, ezeket az értékeket a portálról szerezheti be.

### <a name="alter-table"></a>Tábla módosítása

A tábla módosítása paranccsal a következő értékek módosíthatók:

* kiépített átbocsátás 
* az élő életben töltött idő értéke
<br>Az oszlopmódosítások jelenleg nem támogatottak.

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("ALTER TABLE books_ks.books WITH cosmosdb_provisioned_throughput=8000, WITH default_time_to_live=0;"))
```

### <a name="drop-table"></a>Táblázat eldobása

```scala
val cdbConnector = CassandraConnector(sc)
cdbConnector.withSessionDo(session => session.execute("DROP TABLE IF EXISTS books_ks.books;"))
```

#### <a name="validate-in-cqlsh"></a>Érvényesítés cqlsh-ban

Futtassa a következő parancsot a cqlsh-ban, és látnia kell, hogy a "könyvek" tábla már nem érhető el:

```bash
USE books_ks;
DESCRIBE tables;
```

## <a name="next-steps"></a>További lépések

A kulcstér és a tábla létrehozása után folytassa a CRUD-műveletekre vonatkozó alábbi cikkekkel és egyebekkel:
 
* [Műveletek létrehozása/beszúrása](cassandra-spark-create-ops.md)  
* [Olvasási műveletek](cassandra-spark-read-ops.md)  
* [Upsert műveletek](cassandra-spark-upsert-ops.md)  
* [Műveletek törlése](cassandra-spark-delete-ops.md)  
* [Összesítési műveletek](cassandra-spark-aggregation-ops.md)  
* [Táblamásolási műveletek](cassandra-spark-table-copy-ops.md)  
