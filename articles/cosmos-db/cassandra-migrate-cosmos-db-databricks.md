---
title: Adatok migrálása az Apache Cassandra-ból Azure Cosmos DB Cassandra API a Databricks (Spark) használatával
description: Megtudhatja, hogyan telepítheti át az Apache Cassandra Database-ből származó adatok Azure Cosmos DB Cassandra API a Azure Databricks és a Spark használatával.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/16/2020
ms.author: thvankra
ms.reviewer: thvankra
ms.openlocfilehash: 74088d749279ab72851e714a50b558dc2adbc0d7
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516542"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-azure-databricks"></a>Adatok migrálása a Cassandra-ből Azure Cosmos DB Cassandra API-fiókba a Azure Databricks használatával
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

A Azure Cosmos DB Cassandra API az Apache Cassandra-on futó nagyvállalati számítási feladatok széles választékát váltják ki, többek között: 

* **A felügyelet és a figyelés nem jelent terhelést:** Kiküszöböli a több operációs rendszer-, JVM-és YAML-fájlon, valamint azok interakcióján keresztüli különböző beállítások felügyeletének és figyelésének a terhelését.

* **Jelentős költségmegtakarítás:** A költségeket megtakaríthatja Azure Cosmos DBával, amely magában foglalja a virtuális gépek, a sávszélesség és a kapcsolódó licencek költségeit is. Emellett nem szükséges az adatközpontok, a kiszolgálók, az SSD-tárolók, a Hálózatkezelés és a villamosenergia-költségek kezelése. 

* **Meglévő kód és eszközök használatának lehetősége:** A Azure Cosmos DB a meglévő Cassandra SDK-k és eszközök segítségével biztosítja a vezetékes protokollok szintjének kompatibilitását. A kompatibilitásnak köszönhetően meglévő kódbázisát minimális változtatásokkal az Azure Cosmos DB Cassandra API-val is használhatja.

Az adatbázis-számítási feladatok egyik platformról a másikra való áttelepíthetők többféleképpen. A [Azure Databricks](https://azure.microsoft.com/services/databricks/) egy olyan platform, amely [Apache Spark](https://spark.apache.org/) szolgáltatásként szolgál, amely lehetővé teszik az offline Migrálás nagy léptékű elvégzését. Ez a cikk azokat a lépéseket ismerteti, amelyek szükségesek az adatok natív Apache Cassandra-beli alapterületekről/táblázatokból való átírásához, hogy a Azure Databricks használatával Azure Cosmos DB Cassandra API.

## <a name="prerequisites"></a>Előfeltételek

* [Azure Cosmos DB Cassandra API fiók kiépítése](create-cassandra-dotnet.md#create-a-database-account)

* [Tekintse át Azure Cosmos DB Cassandra APIhoz való csatlakozás alapjait](cassandra-spark-generic.md)

* A kompatibilitás érdekében tekintse át a [Azure Cosmos DB Cassandra API támogatott funkcióit](cassandra-support.md) .

* Győződjön meg arról, hogy már létrehozott üres tárhelyet és táblákat a cél Azure Cosmos DB Cassandra API fiókban

* [Cqlsh vagy üzemeltetett rendszerhéj használata az ellenőrzéshez](cassandra-support.md#hosted-cql-shell-preview)

## <a name="provision-an-azure-databricks-cluster"></a>Azure Databricks-fürt kiépítése

[Azure Databricks-fürt](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)kiépítésére vonatkozó utasításokat követve hozhat létre. Vegye figyelembe azonban, hogy a 3. x Apache Spark jelenleg nem támogatott az Apache Cassandra-összekötőn. Létre kell hoznia egy Databricks-futtatókörnyezetet a Apache Spark támogatott v2. x verziójával. Javasoljuk, hogy válassza ki a Databricks Runtime azon verzióját, amely támogatja a Spark 2. x legújabb verzióját, amely a Scala 2,11-as verziójánál későbbi:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-runtime.png" alt-text="Databricks futtatókörnyezet":::


## <a name="add-dependencies"></a>Függőségek hozzáadása

A natív és a Cosmos DB Cassandra-végpontokhoz való csatlakozáshoz hozzá kell adnia a Apache Spark Cassandra Connector-függvénytárat a fürthöz. A fürtben válassza a könyvtárak lehetőséget > telepítse a New-> Maven-> Search csomagokat:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages.png" alt-text="Databricks keresési csomagok":::

Írja be `Cassandra` a keresőmezőbe, majd válassza ki a legújabb `spark-cassandra-connector` Maven-tárházat, majd válassza a telepítés lehetőséget:

:::image type="content" source="./media/cassandra-migrate-cosmos-db-databricks/databricks-search-packages-2.png" alt-text="Databricks kiválasztása":::

> [!NOTE]
> Győződjön meg arról, hogy a Cassandra Connector függvénytárának telepítése után újra kell indítania a Databricks-fürtöt.

## <a name="create-scala-notebook-for-migration"></a>Scala-jegyzetfüzet létrehozása áttelepítéshez

Hozzon létre egy Scala notebookot a Databricks-ben a következő kóddal. Cserélje le a forrás-és cél Cassandra-konfigurációkat a megfelelő hitelesítő adatokkal, valamint a forrás-és cél-alapterületekre és táblákra, majd futtassa a parancsot:

```scala
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql._
import org.apache.spark.SparkContext

// source cassandra configs
val nativeCassandra = Map( 
    "spark.cassandra.connection.host" -> "<Source Cassandra Host>",
    "spark.cassandra.connection.port" -> "9042",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "false",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>"
)

//target cassandra configs
val cosmosCassandra = Map( 
    "spark.cassandra.connection.host" -> "<USERNAME>.cassandra.cosmos.azure.com",
    "spark.cassandra.connection.port" -> "10350",
    "spark.cassandra.auth.username" -> "<USERNAME>",
    "spark.cassandra.auth.password" -> "<PASSWORD>",
    "spark.cassandra.connection.ssl.enabled" -> "true",
    "keyspace" -> "<KEYSPACE>",
    "table" -> "<TABLE>",
    //throughput related settings below - tweak these depending on data volumes. 
    "spark.cassandra.output.batch.size.rows"-> "1",
    "spark.cassandra.connection.connections_per_executor_max" -> "10",
    "spark.cassandra.output.concurrent.writes" -> "1000",
    "spark.cassandra.concurrent.reads" -> "512",
    "spark.cassandra.output.batch.grouping.buffer.size" -> "1000",
    "spark.cassandra.connection.keep_alive_ms" -> "600000000"
)

//Read from native Cassandra
val DFfromNativeCassandra = sqlContext
  .read
  .format("org.apache.spark.sql.cassandra")
  .options(nativeCassandra)
  .load
  
//Write to CosmosCassandra
DFfromNativeCassandra
  .write
  .format("org.apache.spark.sql.cassandra")
  .options(cosmosCassandra)
  .save
```

> [!NOTE]
> A `spark.cassandra.output.concurrent.writes` és a `connections_per_executor_max` konfigurációk fontosak a [ráta korlátozásának](/samples/azure-samples/azure-cosmos-cassandra-java-retry-sample/azure-cosmos-db-cassandra-java-retry-sample/)elkerüléséhez, ami akkor fordul elő, amikor a kérések Cosmos db meghaladják a kiosztott átviteli sebességet ([kérelmek egységei](./request-units.md)). Előfordulhat, hogy módosítania kell ezeket a beállításokat a Spark-fürtben lévő végrehajtók számától függően, és az egyes rekordok méretének (és így RU-nek) a megcélzott táblákba való beírása is lehetséges.

## <a name="next-steps"></a>További lépések

* [Átviteli sebesség kiosztása tárolókra és adatbázisokra](set-throughput.md) 
* [A partíciós kulcs ajánlott eljárásai](partitioning-overview.md#choose-partitionkey)
* [Ru/s becslése a Azure Cosmos db Capacity Planner cikkei alapján](estimate-ru-with-capacity-planner.md)
* [Rugalmas skálázás Azure Cosmos DB Cassandra API](manage-scale-cassandra.md)