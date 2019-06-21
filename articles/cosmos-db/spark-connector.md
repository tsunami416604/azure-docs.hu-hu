---
title: Csatlakozás az Apache Spark az Azure Cosmos DB
description: További információ az Azure Cosmos DB Spark-összekötő, amely lehetővé teszi az Apache Spark az Azure Cosmos DB-hez kapcsolódni.
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: ramkris
ms.openlocfilehash: c39614a05db6553102e74ddbc3838d8c8f812640
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190490"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Gyorsítsa fel a big data-elemzés az Azure Cosmos DB-összekötő az Apache Spark használatával

Futtathat [Spark](https://spark.apache.org/) feladatok a Cosmos DB Spark-összekötő segítségével Azure Cosmos DB-ben tárolt adatokat. Cosmos használható a batch- és adatfolyam-feldolgozó és a egy kiszolgálórétegbe, a közel valós idejű hozzáféréshez.

Használhatja az összekötőnél a [Azure Databricks](https://azure.microsoft.com/services/databricks) vagy [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/), biztosító felügyelt Spark-fürtök az Azure-ban. Az alábbi táblázat a Spark támogatott verzió.

| Összetevő | Verzió |
|---------|-------|
| Apache Spark | 2.4.x, 2.3.x, 2.2.x és 2.1.x |
| Scala | 2.11 |
| Az Azure Databricks futtatókörnyezet-verziója | > 3.4 |

> [!WARNING]
> Ez az összekötő támogatja az Azure Cosmos DB core (SQL) API-t.
> A Cosmos DB MongoDB API-hoz, használja a [MongoDB Spark-összekötő](https://docs.mongodb.com/spark-connector/master/).
> A Cosmos DB Cassandra API-t használja a [Cassandra Spark-összekötő](https://github.com/datastax/spark-cassandra-connector).
>

## <a name="quickstart"></a>Első lépések

* Kövesse a lépéseket [a Java SDK használatának első lépései](sql-api-async-java-get-started.md) egy Cosmos DB-fiók beállítását, és töltse ki az egyes adatokat.
* Kövesse a lépéseket [első lépései az Azure Databricks](https://docs.azuredatabricks.net/getting-started/index.html) egy Azure Databricks-munkaterület és egy fürt beállításához.
* Most hozzon létre új jegyzetfüzet, és importálása a Cosmos DB-összekötő könyvtár. Ugrás a [a Cosmos DB-összekötő használata](#bk_working_with_connector) megtudhatja, hogyan állíthatja be a munkaterület számára.
* Az alábbi szakasz kódrészletek való olvasása és írása az összekötővel rendelkezik.

### <a name="batch-reads-from-cosmos-db"></a>A Batch beolvassa a Cosmos DB-ből

Az alábbi kódrészlet bemutatja, hogyan hozhat létre a Spark DataFrame olvasni a Cosmos DB a PySpark.

```python
# Read Configuration
readConfig = {
  "Endpoint" : "https://doctorwho.documents.azure.com:443/",
  "Masterkey" : "YOUR-KEY-HERE",
  "Database" : "DepartureDelays",
  "Collection" : "flights_pcoll",
  "query_custom" : "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'" // Optional
}

# Connect via azure-cosmosdb-spark to create Spark DataFrame
flights = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**readConfig).load()
flights.count()
```

És a Scala azonos kódrészlet:

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

### <a name="batch-writes-to-cosmos-db"></a>A Batch írja a Cosmos DB-hez

Az alábbi kódrészlet bemutatja, hogyan írhat adatkeretek PySpark a Cosmos DB-hez.

```python
# Write configuration
writeConfig = {
 "Endpoint" : "https://doctorwho.documents.azure.com:443/",
 "Masterkey" : "YOUR-KEY-HERE",
 "Database" : "DepartureDelays",
 "Collection" : "flights_fromsea",
 "Upsert" : "true"
}

# Write to Cosmos DB from the flights DataFrame
flights.write.format("com.microsoft.azure.cosmosdb.spark").options(**writeConfig).save()
```

És a Scala azonos kódrészlet:

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

### <a name="streaming-reads-from-cosmos-db"></a>Adatfolyam-olvasások Cosmos DB-ből

Az alábbi kódrészlet bemutatja, hogyan csatlakozhat, és az Azure Cosmos DB módosítási hírcsatorna olvasni.

```python
# Read Configuration
readConfig = {
  "Endpoint" : "https://doctorwho.documents.azure.com:443/",
  "Masterkey" : "YOUR-KEY-HERE",
  "Database" : "DepartureDelays",
  "Collection" : "flights_pcoll",
  "ReadChangeFeed" : "true",
  "ChangeFeedQueryName" : "Departure-Delays",
  "ChangeFeedStartFromTheBeginning" : "false",
  "InferStreamSchema" : "true",
  "ChangeFeedCheckpointLocation" : "dbfs:/Departure-Delays"
}


# Open a read stream to the Cosmos DB Change Feed via azure-cosmosdb-spark to create Spark DataFrame
changes = (spark
.readStream
.format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSourceProvider")
.options(**readConfig)
.load())
```
És a Scala azonos kódrészlet:

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

### <a name="streaming-writes-to-cosmos-db"></a>Streamelési írási, Cosmos DB-hez

Az alábbi kódrészlet bemutatja, hogyan írhat adatkeretek PySpark a Cosmos DB-hez.

```python
# Write configuration
writeConfig = {
 "Endpoint" : "https://doctorwho.documents.azure.com:443/",
 "Masterkey" : "YOUR-KEY-HERE",
 "Database" : "DepartureDelays",
 "Collection" : "flights_fromsea",
 "Upsert" : "true",
 "WritingBatchSize" : "500",
 "CheckpointLocation" : "/checkpointlocation_write1"
}

# Write to Cosmos DB from the flights DataFrame
changeFeed = (changes
 .writeStream
 .format("com.microsoft.azure.cosmosdb.spark.streaming.CosmosDBSinkProvider")
 .outputMode("append")
 .options(**writeconfig)
 .start())
```

És a Scala azonos kódrészlet:

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
Kódrészletek és a teljes körű kódmintákat, több további: [Jupyter](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/notebooks).

## <a name="bk_working_with_connector"></a> Az összekötő használata

Az összekötők a GitHub forrás, vagy töltse le az uber JAR-fájlok kivételével a Mavenből a lenti hivatkozásokra kattintva.

| Spark | Scala | Legújabb verziója |
|---|---|---|
| 2.4.0 | 2.11 | [azure-cosmosdb-spark_2.4.0_2.11_1.4.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar)
| 2.3.0 | 2.11 | [azure-cosmosdb-spark_2.3.0_2.11_1.3.3](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.3.0_2.11/1.3.3/jar)
| 2.2.0 | 2.11 | [azure-cosmosdb-spark_2.2.0_2.11_1.1.1](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-cosmosdb-spark_2.2.0_2.11%7C1.1.1%7Cjar)
| 2.1.0 | 2.11 | [azure-cosmosdb-spark_2.1.0_2.11_1.2.2](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.2/jar)

### <a name="using-databricks-notebooks"></a>Databricks-jegyzetfüzetek használata

Hozzon létre egy könyvtárat a Databricks-munkaterület használata az Azure Databricks útmutató található útmutatást követve > [az Azure Cosmos DB Spark-összekötő használatára](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

> [!NOTE]
> Vegye figyelembe, hogy a **használata az Azure Cosmos DB Spark-összekötő** lap jelenleg nem naprakész. Hanem a hat különálló JAR-fájlok kivételével, hat különböző szalagtárat, letöltheti az uber jar a mavenben https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11/1.4.0/jar) és a egy jar kódtár telepítését.
> 

### <a name="using-spark-cli"></a>Spark – parancssori felület használatával

A parancssori felületről spark-összekötő működéséhez (azt jelenti, `spark-shell`, `pyspark`, `spark-submit`), használhatja a `--packages` paraméter az összekötővel [maven-koordináták](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.4.0_2.11).

```sh
spark-shell --master yarn --packages "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0"

```

### <a name="using-jupyter-notebooks"></a>Jupyter notebook használatával

HDInsight belül Jupyter notebookok használata spark-Magic Quadrant használhatja `%%configure` cella az összekötő maven-koordináták megadása.

```python
{ "name":"Spark-to-Cosmos_DB_Connector",
  "conf": {
    "spark.jars.packages": "com.microsoft.azure:azure-cosmosdb-spark_2.4.0_2.11:1.4.0",
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
   ...
}
```

> Vegye figyelembe, felvételét a `spark.jars.excludes` távolítsa el az összekötőt, az Apache Spark és Livy között ellentmondások jellemző.

### <a name="build-the-connector"></a>Az összekötő létrehozása

Jelenleg a összekötő projekt által használt `maven` létrehozását úgy, függőségeket, futtatásához:

```sh
mvn clean package
```

## <a name="working-with-our-samples"></a>A minták használata

A [Cosmos DB Spark GitHub-adattár](https://github.com/Azure/azure-cosmosdb-spark) rendelkezik a következő mintafüzetek és parancsfájlok, próbálja meg.

* **Kereskedelmi repülési teljesítmény Spark és a Cosmos DB (Budapesten)** [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/On-Time%20Flight%20Performance%20with%20Spark%20and%20Cosmos%20DB%20-%20Seattle.html): Csatlakozzon a Spark HDInsight Jupyter notebook szolgáltatás használatával a Spark SQL, GraphFrames és előrejelzésére járatok késésének használatával a gépi Tanulási folyamatok bemutatására Cosmos DB-hez.
* **Twitter-forrás az Apache Spark és az Azure Cosmos DB módosítási hírcsatorna**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Twitter%20with%20Spark%20and%20Azure%20Cosmos%20DB%20Change%20Feed.html)
* **Az Apache Spark használatával lekérdezés Cosmos DB Gráfok**: [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Using%20Apache%20Spark%20to%20query%20Cosmos%20DB%20Graphs.html)
* **[Csatlakozás az Azure Databricks az Azure Cosmos DB](https://docs.databricks.com/spark/latest/data-sources/azure/cosmosdb-connector.html)**  használatával `azure-cosmosdb-spark`.  Csatolva ide van is egy Azure Databricks verzióját a [időben repülési teljesítmény notebook](https://github.com/dennyglee/databricks/tree/master/notebooks/Users/denny%40databricks.com/azure-databricks).
* **[Lambda architektúra az Azure Cosmos DB és a HDInsight (az Apache Spark)](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/readme.md)** : Csökkentheti a Cosmos DB- és Spark használata big data-adatcsatornákat fenntartásának üzemeltetési terheit.

## <a name="more-information"></a>További információ

További információt van a `azure-cosmosdb-spark` [wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki) többek között:

* [Az Azure Cosmos DB Spark-összekötő felhasználói útmutató](https://github.com/Azure/azure-documentdb-spark/wiki/Azure-Cosmos-DB-Spark-Connector-User-Guide)
* [Összesítések példák](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)

### <a name="configuration-and-setup"></a>Konfigurálás és beállítás

* [Spark-összekötő konfigurálása](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuration-references)
* [Apache Spark a Cosmos DB-összekötő telepítése](https://github.com/Azure/azure-documentdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup) (folyamatban)
* [Az Azure Cosmos DB Apache Spark (HDI) a Power BI közvetlen lekérdezés konfigurálása](https://github.com/Azure/azure-cosmosdb-spark/wiki/Configuring-Power-BI-Direct-Query-to-Azure-Cosmos-DB-via-Apache-Spark-(HDI))

### <a name="troubleshooting"></a>Hibaelhárítás

* [Összesíti a Cosmos DB használatával](https://github.com/Azure/azure-documentdb-spark/wiki/Troubleshooting:-Using-Cosmos-DB-Aggregates)
* [Ismert problémák](https://github.com/Azure/azure-cosmosdb-spark/wiki/Known-Issues)

### <a name="performance"></a>Teljesítmény

* [Teljesítménnyel kapcsolatos tippek](https://github.com/Azure/azure-cosmosdb-spark/wiki/Performance-tips)
* [Lekérdezés tesztelések](https://github.com/Azure/azure-documentdb-spark/wiki/Query-Test-Runs)
* [Írási teszt futtatása](https://github.com/Azure/azure-cosmosdb-spark/wiki/Writing-Test-Runs)

### <a name="change-feed"></a>Változáscsatorna

* [Az Azure Cosmos DB módosítási hírcsatorna és az Apache Spark Stream feldolgozása módosítások](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)
* [Módosítási hírcsatorna bemutatók](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Strukturált Stream bemutatók](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)

### <a name="monitoring"></a>Figyelés

* [Az application insights segítségével a Spark-feladatok figyelése](https://github.com/Azure/azure-cosmosdb-spark/tree/2.3/samples/monitoring)

## <a name="next-steps"></a>További lépések

Ha még nem tette, töltse le a Spark az Azure Cosmos DB-összekötő a [azure-cosmos DB-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-adattárban. Ismerje meg a következő további erőforrások az adattárban:

* [Összesítések példák](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Mintaszkriptek és -jegyzetfüzetek](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Emellett érdemes áttekinteni a [adatkészletek útmutató, Apache Spark SQL és DataFrames](https://spark.apache.org/docs/latest/sql-programming-guide.html), és a [Apache Spark on Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) cikk.
