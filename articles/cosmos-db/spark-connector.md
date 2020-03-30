---
title: Az Apache Spark csatlakoztatása az Azure Cosmos DB-hoz
description: Ismerje meg az Azure Cosmos DB Spark összekötőt, amely lehetővé teszi az Apache Spark csatlakoztatását az Azure Cosmos DB-hoz.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: bbc97489f0c7045040dd0189b97946c2bd8fb3c0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481636"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Gyorsítsa fel a big data-elemzéseket az Apache Spark–Azure Cosmos DB összekötő használatával

[Spark-feladatok](https://spark.apache.org/) at futtathat az Azure Cosmos DB-ben tárolt adatokkal a Cosmos DB Spark-összekötő használatával. A Cosmos kötegelt és streamelési feldolgozásra, valamint az alacsony késésű hozzáférés kiszolgáló rétegeként használható.

Használhatja az összekötőt az [Azure Databricks](https://azure.microsoft.com/services/databricks) vagy [az Azure HDInsight,](https://azure.microsoft.com/services/hdinsight/)amely felügyelt Spark-fürtök az Azure-ban. Az alábbi táblázat a támogatott Spark-verziókat mutatja be.

| Összetevő | Verzió |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x és 2.1.x |
| Scala | 2.11 |
| Az Azure Databricks futásidejű verziója | > 3,4 |

> [!WARNING]
> Ez az összekötő támogatja az Azure Cosmos DB core (SQL) API-ját.
> A Cosmos DB a MongoDB API-hoz használja a [MongoDB Spark-összekötőt.](https://docs.mongodb.com/spark-connector/master/)
> A Cosmos DB Cassandra API-hoz használja a [Cassandra Spark összekötőt.](https://github.com/datastax/spark-cassandra-connector)
>

## <a name="quickstart"></a>Első lépések

* Kövesse a [Java SDK első](sql-api-async-java-get-started.md) lépései című lépéseket a Cosmos DB-fiók beállításához és bizonyos adatok feltöltéséhez.
* Kövesse az [Azure Databricks első lépések](/azure/azure-databricks/quickstart-create-databricks-workspace-portal) ben leírt lépéseket egy Azure Databricks-munkaterület és fürt beállításához.
* Most már létrehozhat új jegyzetfüzeteket, és importálhatja a Cosmos DB összekötő könyvtárat. Ugrás [a Munka a Cosmos DB összekötővel](#bk_working_with_connector) a munkaterület beállításával kapcsolatos részletekért.
* A következő szakasz ban kódrészletek, hogyan kell olvasni és olvasni az összekötő használatával.

### <a name="batch-reads-from-cosmos-db"></a>Batch olvasás a Cosmos DB-ből

A következő kódrészlet bemutatja, hogyan hozhat létre egy Spark DataFrame olvasni a Cosmos DB a PySpark.The following tniptt bemutatja, hogyan hozhat létre egy Spark DataFrame olvasni a Cosmos DB a PySpark.The following tniptt bemutatja, hogyan hozhat létre egy Spark DataFrame olvasni a Cosmos DB a Py

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "query_custom": "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format(
    "com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

És ugyanaz a kódrészlet a Scala-ban:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "query_custom" -> "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
))

// Connect via azure-cosmosdb-spark to create Spark DataFrame
val flights = spark.read.cosmosDB(readConfig)
flights.count()
```

### <a name="batch-writes-to-cosmos-db"></a>Batch írja a Cosmos DB

A következő kódrészlet bemutatja, hogyan írhat egy adatkeretet a Cosmos DB-be a PySparkban.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(
    **writeConfig).save()
```

És ugyanaz a kódrészlet a Scala-ban:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" : "true"
))

// Write to Cosmos DB from the flights DataFrame
import org.apache.spark.sql.SaveMode
flights.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

### <a name="streaming-reads-from-cosmos-db"></a>Olvasás streamelése a Cosmos DB-ből

Az alábbi kódrészlet bemutatja, hogyan csatlakozhat az Azure Cosmos DB-módosítási hírcsatornájához, és hogyan olvashat onnan.

```python
# Read Configuration
readConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_pcoll",
    "ReadChangeFeed": "true",
    "ChangeFeedQueryName": "Departure-Delays",
    "ChangeFeedStartFromTheBeginning": "false",
    "InferStreamSchema": "true",
    "ChangeFeedCheckpointLocation": "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
           .readStream
           .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
           .options(**readConfig)
           .load())
```
És ugyanaz a kódrészlet a Scala-ban:

```scala
// Import Necessary Libraries
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Read Configuration
val readConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_pcoll",
  "ReadChangeFeed" -> "true",
  "ChangeFeedQueryName" -> "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" -> "false",
  "InferStreamSchema" -> "true",
  "ChangeFeedCheckpointLocation" -> "dbfs:/Departure-Delays"
))

// Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
val df = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(readConfig).load()
```

### <a name="streaming-writes-to-cosmos-db"></a>A streamelés a Cosmos DB-nek ír

A következő kódrészlet bemutatja, hogyan írhat egy adatkeretet a Cosmos DB-be a PySparkban.

```python
# Write configuration
writeConfig = {
    "Endpoint": "https://doctorwho.documents.azure.com:443/",
    "Masterkey": "YOUR-KEY-HERE",
    "Database": "DepartureDelays",
    "Collection": "flights_fromsea",
    "Upsert": "true",
    "WritingBatchSize": "500",
    "CheckpointLocation": "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
              .writeStream
              .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
              .outputMode("append")
              .options(**writeconfig)
              .start())
```

És ugyanaz a kódrészlet a Scala-ban:

```scala
// Write configuration

val writeConfig = Config(Map(
  "Endpoint" -> "https://doctorwho.documents.azure.com:443/",
  "Masterkey" -> "YOUR-KEY-HERE",
  "Database" -> "DepartureDelays",
  "Collection" -> "flights_fromsea",
  "Upsert" -> "true",
  "WritingBatchSize" -> "500",
  "CheckpointLocation" -> "/checkpointlocation_write1"
))

// Write to Cosmos DB from the flights DataFrame
df
.writeStream
.format(classOf[CosmosDBSinkProvider].getName)
.options(writeConfig)
.start()
```
További kódrészletek és a végpontok között minták, [lásd: Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="working-with-the-connector"></a><a name="bk_working_with_connector"></a>A csatlakozó val való együttműködés

Az összekötőt a GitHub forrásából hozhatja létre, vagy letöltheti az uber-üvegeket a Mavenből az alábbi linkeken.

| Spark | Scala | Legújabb verzió |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Databricks notebookok használata

Hozzon létre egy könyvtárat a Databricks-munkaterület használatával az Azure Databricks-útmutató > [az Azure Cosmos DB Spark-összekötő használatával.](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Megjegyzés: Az **Azure Cosmos DB Spark Connector** használata lap jelenleg nem naprakész. Ahelyett, hogy letölti a hat különálló üvegek hat különböző könyvtárak, letöltheti az uber jar a maven at https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar) és telepítse ezt egy jar / könyvtár.
> 

### <a name="using-spark-cli"></a>Használata szikra-cli

Ha az összekötővel a szikra-cli (azaz `spark-shell`, , `pyspark`, ) használatával szeretne dolgozni, `spark-submit`használhatja a `--packages` paramétert az összekötő [maven koordinátáival.](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11)

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Jupyter notebookok használata

Ha Jupyter-jegyzetfüzeteket használ a HDInsight-on belül, `%%configure` a spark-magic cella segítségével megadhatja az összekötő maven koordinátáit.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Vegye figyelembe, hogy `spark.jars.excludes` a felvétele a specifikus az összekötő, az Apache Spark és a Livy közötti lehetséges ütközések eltávolítása.

### <a name="build-the-connector"></a>Az összekötő megépítése

Jelenleg ez az `maven` összekötő projekt függőségek nélküli létrehozásához használja a következőket:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>Együttműködés a mintákkal

A [Cosmos DB Spark GitHub-tárház](https://github.com/Azure/azure-cosmosdb-spark) a következő mintanotebookok és parancsfájlok, amelyek kipróbálhatja.

* **Időben történő repülési teljesítmény a Spark és a Cosmos DB (Seattle)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html:](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html)Csatlakoztassa a Sparkot a Cosmos DB-hez a HDInsight Jupyter notebook szolgáltatás használatával a Spark SQL, graphframes és a járatkésések előrejelzése ml-folyamatok használatával.
* **Twitter forrás Apache Spark és az Azure Cosmos DB Change Feed**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Az Apache Spark használata cosmos DB grafikonok lekérdezéséhez:** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Az Azure Databricks csatlakoztatása az Azure Cosmos DB-hez](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)** a használatával. `azure-cosmosdb-spark`  Az itt hivatkozott is egy Azure Databricks változata [az on-time flight performance notebook.](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks)
* **[Lambda Architektúra az Azure Cosmos DB és a HDInsight (Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** használatával: Csökkentheti a big data-folyamatok karbantartásának működési terhelését a Cosmos DB és a Spark használatával.

## <a name="more-information"></a>További információ

Több információ nk `azure-cosmosdb-spark` van a [wikiben,](https://github.com/Azure/azure-cosmosdb-spark/wiki) beleértve a következőket:

* [Az Azure Cosmos DB Spark-összekötő felhasználói útmutatója](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Példák összesítések](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Konfigurálás és beállítás

* [Spark-összekötő konfigurációja](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Spark a Cosmos DB összekötő beállítása](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (folyamatban)
* [A Power BI Direct-lekérdezés konfigurálása az Azure Cosmos DB-re az Apache Sparkon (HDI) keresztül](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Hibaelhárítás

* [Cosmos DB összesítések használata](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Ismert problémák](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Teljesítmény

* [Teljesítménytippek](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Lekérdezési tesztfuttatások](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Tesztfuttatások írása](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Változáscsatorna

* [Streamelési módosítások az Azure Cosmos DB change feed és az Apache Spark használatával](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Hírcsatorna-bemutatók módosítása](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Strukturált streambemutatók](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Figyelés

* [Spark-feladatok figyelése alkalmazáselemzésekkel](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>További lépések

Ha még nem tette meg, töltse le a Spark az Azure Cosmos DB-összekötőt az [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-tárházból. Fedezze fel a következő további forrásokat a tárházban:

* [Példák összesítések](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Példaparancsfájlok és jegyzetfüzetek](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Érdemes lehet az [Apache Spark SQL, DataFrames és adatkészletek útmutatóját](https://spark.apache.org/docs/latest/sql-programming-guide.html)és az Apache Spark az [Azure HDInsight-cikkét](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) is áttekinteni.
