---
title: Azure Cosmos DB Apache Spark-összekötő az SQL API kibocsátási megjegyzései és erőforrásai számára
description: Ismerkedjen meg az SQL API Azure Cosmos DB Apache Spark-összekötővel, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat, valamint a Azure Cosmos DB SQL aszinkron Java SDK egyes verzióiban végrehajtott módosításokat.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 46ddbd18051ffa44232468704ce189d4171b50e7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590009"
---
# <a name="azure-cosmos-db-apache-spark-connector-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Apache Spark-összekötő a Core (SQL) API-hoz: kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [Spring-adatv2](sql-api-sdk-java-spring-v2.md)
> * [Spring-adatforrások v3](sql-api-sdk-java-spring-v3.md)
> * [Spark-összekötő](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Tömeges végrehajtó – .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Tömeges végrehajtó – Java](sql-api-sdk-bulk-executor-java.md)

A Core (SQL) Azure Cosmos DB Apache Spark Connector használatával felgyorsíthatja big data elemzését. A Spark-összekötő lehetővé teszi [Spark ](https://spark.apache.org/) -feladatok futtatását Azure Cosmos DBban tárolt adatokon. A Batch-és adatfolyam-feldolgozás támogatott.

Az összekötőt használhatja [Azure Databricks](https://azure.microsoft.com/services/databricks) vagy [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), amelyek felügyelt Spark-fürtöket biztosítanak az Azure-ban. Az alábbi táblázatban a Spark-verziók láthatók.

| Összetevő | Verzió |
|---------|-------|
| Apache Spark | 2.4. x, 2.3. x, 2.2. x és 2.1. x |
| Scala | 2,11 |
| Azure Databricks futtatókörnyezet verziója | > 3,4 |

> [!WARNING]
> Ez az összekötő támogatja a Azure Cosmos DB Core (SQL) API-ját.
> A MongoDB API-hoz Cosmos DB használja a [MongoDB Spark-összekötőt](https://docs.mongodb.com/spark-connector/master/).
> Cosmos DB Cassandra API esetén használja a [Cassandra Spark-összekötőt](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="helpful-content"></a>Hasznos tartalom

| Tartalom | Hivatkozás |
|---|---|
| **SDK letöltése** | [Letöltés innen: Apache Spark](https://aka.ms/CosmosDB_OLTP_Spark_2.4_LKG) |
|**API-dokumentáció** | [Spark-összekötő referenciája]() |
|**Közreműködés az SDK-val** | [Azure Cosmos DB-összekötő Apache Spark a GitHubon](https://github.com/Azure/azure-cosmosdb-spark) | 
|**Első lépések** | [Gyorsítsa fel big data elemzést az Azure Cosmos DB Connector Apache Spark használatával](https://docs.microsoft.com/azure/cosmos-db/spark-connector#bk_working_with_connector) <br> [Apache Spark strukturált adatfolyam használata Apache Kafka és Azure Cosmos DB](https://docs.microsoft.com/azure/hdinsight/apache-kafka-spark-structured-streaming-cosmosdb?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json) | 

## <a name="release-history"></a>Kiadási előzmények

### <a name="311"></a>3.1.1
#### <a name="new-features"></a>Új funkciók
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Kijavítja a folyamatos átviteli ellenőrzőpontok Edge-esetét, ha a "id" tartalmazza a "ChangeFeedMaxPagesPerBatch" konfigurációs konfigurációt tartalmazó "|" karaktert.

### <a name="310"></a>3.1.0
#### <a name="new-features"></a>Új funkciók
* Támogatja a tömeges frissítéseket beágyazott partíciós kulcsok használatakor
* A a decimális és az úszó adattípusok támogatását adja a Cosmos DBba írás során.
* Az időbélyeg-típusok támogatását adja meg, ha hosszú (UNIX EPOCH) értéket használ
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai

### <a name="308"></a>3.0.8
#### <a name="new-features"></a>Új funkciók
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* A (z) "WriteThroughputBudget" konfiguráció használatakor a javítások típusa leadott kivétel.

### <a name="307"></a>3.0.7
#### <a name="new-features"></a>Új funkciók
* Hibaüzeneteket ad a tömeges hibákról a kivétel és a napló számára.
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai

### <a name="306"></a>3.0.6
#### <a name="new-features"></a>Új funkciók
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Javítja a streaming ellenőrzőponttal kapcsolatos problémákat.

### <a name="305"></a>3.0.5
#### <a name="new-features"></a>Új funkciók
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Az üzenetek naplózási szintjének hibajavítása akaratlanul, a zaj csökkentése érdekében

### <a name="304"></a>3.0.4
#### <a name="new-features"></a>Új funkciók
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Kijavítja a strukturált adatfolyamban felmerülő hibát a partíciók felosztása során – lehetséges, hogy hiányzik néhány módosítási hírcsatorna, vagy a rendszer null kivételeket lát az ellenőrzőpont-írás

### <a name="303"></a>3.0.3
#### <a name="new-features"></a>Új funkciók
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Kijavít egy hibát, ha a rendszer figyelmen kívül hagyja a readStream számára megadott egyéni sémát

### <a name="302"></a>3.0.2
#### <a name="new-features"></a>Új funkciók
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Javítások regressziója (a nem árnyékolt JAR tartalmazza az összes árnyékolt függőséget), amely megnövelte a kiépítési időt 50%-kal

### <a name="301"></a>3.0.1
#### <a name="new-features"></a>Új funkciók
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai
* Kijavít egy függőségi problémát, amely közvetlen átvitelt okoz a TCP protokollon keresztül a RequestTimeoutException

### <a name="300"></a>3.0.0
#### <a name="new-features"></a>Új funkciók
* Javítja a kapcsolatok kezelését és a kapcsolatok készletezését a metaadat-hívások számának csökkentése érdekében
#### <a name="key-bug-fixes"></a>Kulcs hibajavításai

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>További lépések

További információ a Cosmos DBről: [Microsoft Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapja.

Ha többet szeretne megtudni a Apache Sparkről, tekintse meg [a kezdőlapot](https://spark.apache.org/).