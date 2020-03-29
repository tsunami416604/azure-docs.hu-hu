---
title: Lambda architektúra az Azure Cosmos DB-vel és az Apache Sparkkal
description: Ez a cikk bemutatja, hogyan valósítható meg a lambda architektúra az Azure Cosmos DB, a HDInsight és a Spark használatával
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 68ce06d8a2904bf99f58a53817444b2992b23501
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76719738"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: Lambda-architektúra megvalósítása az Azure platformon 

A Lambda architektúrák lehetővé teszik a tömeges adatkészletek hatékony adatfeldolgozását. A Lambda architektúrák kötegelt feldolgozást, streamfeldolgozást és egy kiszolgálóréteget használnak a big data lekérdezésével kapcsolatos késés minimalizálásához. 

Lambda architektúra azure-beli megvalósításához a következő technológiákat kombinálhatja a valós idejű big data-elemzés felgyorsításához:
* [Az Azure Cosmos DB,](https://azure.microsoft.com/services/cosmos-db/)az iparág első globálisan elosztott, többmodelles adatbázis-szolgáltatása. 
* [Apache Spark for Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), egy nagyméretű adatelemzési alkalmazásokat futtató feldolgozási keretrendszer
* Az Azure Cosmos DB [módosítási hírcsatornája](change-feed.md), amely új adatokat továbbít a kötegelt rétegbe a HDInsight számára a feldolgozáshoz
* A [Spark az Azure Cosmos DB-összekötőhöz](spark-connector.md)

Ez a cikk ismerteti az alapjait a lambda architektúra alapján az eredeti többrétegű design és az előnyeit egy "újraépítész" lambda architektúra, amely egyszerűsíti a műveleteket.  

## <a name="what-is-a-lambda-architecture"></a>Mi az a lambda építészet?
A lambda architektúra egy általános, skálázható és hibatűrő adatfeldolgozási architektúra a Kötegelt és sebességű késleltetési forgatókönyvek kezelésére [Nathan Marz](https://twitter.com/nathanmarz)szerint.

![Lambda architektúrát bemutató diagram](./media/lambda-architecture/lambda-architecture-intro.png)

Forrás: http://lambda-architecture.net/

A lambda architektúra alapelveit az [http://lambda-architecture.net](http://lambda-architecture.net/)előző ábra a szerint írja le.

 1. Minden **adat** a *kötegrétegbe* és a *sebességrétegbe* *is* bekerül.
 2. A **kötegelt réteg** rendelkezik egy fő adatkészlet (nem módosítható, csak hozzáfűzhető nyers adatok) és előre kiszámítja a kötegnézeteket.
 3. A **kiszolgálóréteg** kötegelt nézetekkel rendelkezik a gyors lekérdezésekhez. 
 4. A **sebességréteg** kompenzálja a feldolgozási időt (a kiszolgálóréteghez), és csak a legutóbbi adatokkal foglalkozik.
 5. Minden lekérdezés megválaszolható a kötegnézetek és a valós idejű nézetek eredményeinek egyesítésével, illetve külön-külön pingeléssel.

További olvasmányok után ezt az architektúrát csak a következők használatával tudjuk megvalósítani:

* Azure Cosmos-tároló(k)
* HDInsight (Apache Spark 2.1) fürt
* Spark-összekötő [1.0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.6/jar)

## <a name="speed-layer"></a>Sebesség réteg

A műveletek szempontjából két adatfolyam karbantartása, miközben biztosítja az adatok megfelelő állapotát, bonyolult törekvés lehet. A műveletek egyszerűsítése érdekében használja az [Azure Cosmos DB változáscsatorna-támogatást,](change-feed.md) hogy megtarthassa a *kötegréteg* állapotát, miközben felfedi az Azure Cosmos DB változási naplóját a *change feed API-n* keresztül a *sebességréteghez.*  
![A lambda architektúra új adat-, sebességréteg- és fő adatkészletrészét kiemelő diagram](./media/lambda-architecture/lambda-architecture-change-feed.png)

Mi a fontos ezekben a rétegekben:

 1. Az összes **adat** *csak* az Azure Cosmos DB-be kerül, így elkerülheti a többcastingos problémákat.
 2. A **kötegelt réteg** rendelkezik egy fő adatkészlet (nem módosítható, csak hozzáfűzhető nyers adatok) és előre kiszámítja a kötegnézeteket.
 3. A **kiszolgálóréteget** a következő szakasz tárgyalja.
 4. A **sebességréteg** a HDInsight (Apache Spark) segítségével olvassa el az Azure Cosmos DB módosítási hírcsatornáját. Ez lehetővé teszi az adatok megőrzését, valamint egyidejű lekérdezését és feldolgozását.
 5. Minden lekérdezés megválaszolható a kötegnézetek és a valós idejű nézetek eredményeinek egyesítésével, illetve külön-külön pingeléssel.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Példa kód: Spark strukturált streamelés egy Azure Cosmos DB változási hírcsatornába
Az Azure Cosmos DB módosítási hírcsatornájának gyors prototípusának futtatásához a **sebességréteg**részeként tesztelheti azt a Twitter-adatok használatával az [Azure Cosmos DB change feed és](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) az Apache Spark-példa használatával. A Twitter kimenetének ugrásszerű elindításához tekintse meg a kódmintát a [Twittertől a Cosmos DB-ig.](https://github.com/tknandu/TwitterCosmosDBFeed) Az előző példában a Twitter-adatok betöltése az Azure Cosmos DB-be, és ezután beállíthatja a HDInsight (Apache Spark) fürtjét a módosítási hírcsatornához való csatlakozáshoz. A konfiguráció beállításáról az [Apache Spark az Azure Cosmos DB Connector setup című témakörben](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup)talál további információt.  

A következő kódrészlet bemutatja, `spark-shell` hogyan konfigurálható egy strukturált streamelési feladat, hogy csatlakozzon egy Azure Cosmos DB módosítási hírcsatornához, amely áttekinti a valós idejű Twitter-adatfolyamot, a futási időköz-szám végrehajtásához.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceConfigMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[MASTER KEY]",
"Database" -> "[DATABASE]",
"Collection" -> "[COLLECTION]",
"ConnectionMode" -> "Gateway",
"ChangeFeedCheckpointLocation" -> "checkpointlocation",
"changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Interval Count")

// Start reading change feed as a stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()

// Start streaming query to console sink
val query = streamData.withColumn("countcol", streamData.col("id").substr(0, 0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
```

A teljes kódmintákat lásd: [azure-cosmosdb-spark/lambda/samples,](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)beleértve:
* [Lekérdezés streamelése a Cosmos DB Change Feed.scala fájlból](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Címkék streamelése lekérdezés a Cosmos DB Change Feed.scala fájlból](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

A kimenet egy `spark-shell` konzol, amely folyamatosan futtat egy strukturált streamelési feladat, amely időközeg-számlálót hajt végre az Azure Cosmos DB módosítási hírcsatornából származó Twitter-adatokkal. Az alábbi képen az adatfolyam-feladat kimenete és az időköz számít.

![Az Azure Cosmos DB módosítási hírcsatornájából származó Twitter-adatok időköze közötti időköz-számlálót megjelenítő streamelési kimenet](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

Az Azure Cosmos DB módosítási hírcsatornájával kapcsolatos további információkért lásd:

* [A módosítási hírcsatorna támogatásának támogatása az Azure Cosmos DB-ben](change-feed.md)
* [Az Azure CosmosDB change feed processzorkönyvtárának bemutatása](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Streamfeldolgozási változások: Az Azure CosmosDB módosítási hírcsatornája + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Kötegelt és kiszolgáló rétegek
Mivel az új adatok betöltődnek az Azure Cosmos DB-be (ahol a változáscsatorna a sebességréteghez használatos), ez az a hely, ahol a **fő adatkészlet** (egy nem módosítható, csak hozzáfűző nyers adatok készlete) található. Ettől a ponttól kezdve a HDInsight (Apache Spark) segítségével hajtsa végre az előre számítási funkciókat a **kötegrétegtől** a **kiszolgáló rétegig**, ahogy az az alábbi képen látható:

![A lambda architektúra kötegrétegét és kiszolgálórétegét kiemelő diagram](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Mi a fontos ezekben a rétegekben:

 1. Az összes **adat** csak az Azure Cosmos DB-be kerül (a többszereplős problémák elkerülése érdekében).
 2. A **kötegelt réteg** rendelkezik egy fő adatkészlet (nem módosítható, csak hozzáfűzésű nyers adatok) az Azure Cosmos DB tárolja. A HDI Spark használatával előre kiszámíthatja az összesítéseket a számított kötegnézetekben való tároláshoz.
 3. A **kiszolgáló réteg** egy Azure Cosmos-adatbázis, amely a fő adatkészlet és a kiszámított kötegnézet gyűjteményeit.
 4. A **sebességréteget** a cikk későbbi részében tárgyaljuk.
 5. Minden lekérdezés válaszolható a kötegnézetek és a valós idejű nézetek eredményeinek egyesítésével, vagy egyenként pingelésével.

### <a name="code-example-pre-computing-batch-views"></a>Példa kód: Kötegnézetek előzetes számítása
Az előre kiszámított nézetek apache sparktól az Azure Cosmos DB-ig történő **fő adatkészlettel** való végrehajtásához használja a következő kódrészleteket a [Lambda Architecture Rearchitected - Batch Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) és [Lambda Architecture Rearchitected - Batch to Serving Layer](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)notebookokból. Ebben a forgatókönyvben használja az Azure Cosmos DB-ben tárolt Twitter-adatokat.

Kezdjük azzal, hogy létrehozza a konfigurációs kapcsolatot a Twitter-adatok az Azure Cosmos DB-n belül az alábbi PySpark-kód használatával.

```
# Configuration to connect to Azure Cosmos DB
tweetsConfig = {
  "Endpoint" : "[Endpoint URL]",
  "Masterkey" : "[Master Key]",
  "Database" : "[Database]",
  "Collection" : "[Collection]", 
  "preferredRegions" : "[Preferred Regions]",
  "SamplingRatio" : "1.0",
  "schema_samplesize" : "200000",
  "query_custom" : "[Cosmos DB SQL Query]"
}

# Create DataFrame
tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()

# Create Temp View (to run Spark SQL statements)
tweets.createOrReplaceTempView("tweets")
```

Ezután futtassa a következő Spark SQL utasítást a tweetek készletének első 10 hashtagjének meghatározásához. Ehhez a Spark SQL-lekérdezéshez ezt egy Jupyter-jegyzetfüzetben futtatjuk anélkül, hogy a kimeneti sávdiagram közvetlenül ezt a kódrészletet követne.

```
%%sql
select hashtags.text, count(distinct id) as tweets
from (
  select 
    explode(hashtags) as hashtags,
    id
  from tweets
) a
group by hashtags.text
order by tweets desc
limit 10
```

![Hashtagenkénti tweetek számát megjelenítő diagram](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Most, hogy már rendelkezik a lekérdezéssel, mentse vissza egy gyűjteménybe a Spark Connector használatával mentse a kimeneti adatokat egy másik gyűjteménybe.  Ebben a példában a Scala használatával mutassa be a kapcsolatot. Az előző példához hasonlóan hozza létre a konfigurációs kapcsolatot az Apache Spark DataFrame egy másik Azure Cosmos-tárolóba mentéséhez.

```
val writeConfigMap = Map(
    "Endpoint" -> "[Endpoint URL]",
    "Masterkey" -> "[Master Key]",
    "Database" -> "[Database]",
    "Collection" -> "[New Collection]", 
    "preferredRegions" -> "[Preferred Regions]",
    "SamplingRatio" -> "1.0",
    "schema_samplesize" -> "200000"
)

// Configuration to write
val writeConfig = Config(writeConfigMap)

```

Megadása után `SaveMode` a (jelezve, `Append` hogy a `tweets_bytags` `Overwrite` vagy a dokumentumok), hozzon létre egy DataFrame hasonló a Spark SQL-lekérdezés az előző példában.  A `tweets_bytags` DataFrame létrehozása után a `write` korábban megadott `writeConfig`módon mentheti azt.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Ez az utolsó utasítás most mentette a Spark DataFrame egy új Azure Cosmos-tárolóba; a lambda architektúra szempontjából ez a **batch nézet** a **kiszolgáló rétegen**belül.
 
#### <a name="resources"></a>Források

A teljes kódmintákat lásd: [azure-cosmosdb-spark/lambda/samples including:](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* Lambda Építészet Rearchitected - Batch Layer [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda Építészet Rearchitected - Batch tálalás layer [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Sebesség réteg
Mint korábban említettük, az Azure Cosmos DB change feed library használatával lehetővé teszi, hogy egyszerűsítse a köteg és a sebesség rétegek közötti műveleteket. Ebben az architektúrában használja az Apache Spark (HDInsight-on keresztül) a *strukturált streamelési* lekérdezések az adatok on-mail végrehajtásához. Előfordulhat, hogy ideiglenesen meg szeretné tartani a strukturált streamelési lekérdezések eredményeit, hogy más rendszerek is hozzáférhessenek ezekhez az adatokhoz.

![A lambda architektúra sebességrétegét kiemelő diagram](./media/lambda-architecture/lambda-architecture-speed.png)

Ehhez hozzon létre egy külön Azure Cosmos-tárolót a strukturált streamelési lekérdezések eredményeinek mentéséhez.  Ez lehetővé teszi, hogy más rendszerek ne csak az Apache Sparkhoz férjenek hozzá ezekhez az információkhoz. A Cosmos DB Time-to-Live (TTL) funkcióval együtt beállíthatja, hogy a dokumentumok automatikusan törlődjenek egy meghatározott időtartam után.  Az Azure Cosmos DB TTL szolgáltatásról az [Adatok automatikus lejárata az Azure Cosmos-tárolókban az élő idő vel című](time-to-live.md) témakörben talál további információt.

```
// Import Libraries
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.codehaus.jackson.map.ObjectMapper
import com.microsoft.azure.cosmosdb.spark.streaming._
import java.time._


// Configure connection to Azure Cosmos DB Change Feed
val sourceCollectionName = "[SOURCE COLLECTION NAME]"
val sinkCollectionName = "[SINK COLLECTION NAME]"

val configMap = Map(
"Endpoint" -> "[COSMOSDB ENDPOINT]",
"Masterkey" -> "[COSMOSDB MASTER KEY]",
"Database" -> "[DATABASE NAME]",
"Collection" -> sourceCollectionName,
"ChangeFeedCheckpointLocation" -> "changefeedcheckpointlocation")

val sourceConfigMap = configMap.+(("changefeedqueryname", "Structured Stream replication streaming test"))

// Start to read the stream
var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
val sinkConfigMap = configMap.-("collection").+(("collection", sinkCollectionName))

// Start the stream writer to new collection
val streamingQueryWriter = streamData.writeStream.format(classOf[CosmosDBSinkProvider].getName).outputMode("append").options(sinkConfigMap).option("checkpointLocation", "streamingcheckpointlocation")
var streamingQuery = streamingQueryWriter.start()

```

## <a name="lambda-architecture-rearchitected"></a>Lambda építészet: Újratervezve
Az előző szakaszokban láthatómódon egyszerűsítheti az eredeti lambda architektúrát a következő összetevők használatával:
* Azure Cosmos DB
* Az Azure Cosmos DB Change Feed Library, hogy ne kelljen több adatküldést a köteg- és sebességrétegek között
* Apache Spark a HDInsighton
* A Spark Connector az Azure Cosmos DB-hez

![A lambda architektúra átarchitását bemutató diagram az Azure Cosmos DB, a Spark és az Azure Cosmos DB Change Feed API használatával](./media/lambda-architecture/lambda-architecture-re-architected.png)

Ezzel a kialakítással csak két felügyelt szolgáltatásra van szükség, az Azure Cosmos DB-re és a HDInsightra. Együtt foglalkoznak a lambda architektúra kötegével, kiszolgálásával és sebességrétegeivel. Ez nemcsak a műveleteket, hanem az adatfolyamot is leegyszerűsíti. 
 1. Minden adat leküldéses az Azure Cosmos DB feldolgozásra
 2. A kötegréteg fő adatkészletet (nem módosítható, csak végpontot hozzáfűző nyers adathalmazt) rendelkezik, és előre kiszámítja a kötegnézeteket
 3. A kiszolgálóréteg kötegelt nézetekkel rendelkezik a gyors lekérdezésekhez.
 4. A sebességréteg kompenzálja a feldolgozási időt (a kiszolgálóréteghez), és csak a legutóbbi adatokkal foglalkozik.
 5. Minden lekérdezés megválaszolható a kötegnézetek és a valós idejű nézetek eredményeinek egyesítésével.

### <a name="resources"></a>Források

* **Új adatok:** A stream feed a [Twitter a CosmosDB,](https://github.com/tknandu/TwitterCosmosDBFeed)amely a mechanizmus az új adatok leküldése súgás az Azure Cosmos DB.
* **Kötegréteg:** A kötegréteg a *fő adatkészletből* (egy nem módosítható, csak hozzáfűzésű nyers adathalmazból) és a **kiszolgálórétegbe**leadott adatok kötegelő nézeteinek előre számítási képességéből áll.
   * A **Lambda Architektúra újratervezett – Batch Layer** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) lekérdezések a *kötegnézetek fő adatkészletét.*
* **Tálaló réteg:** A **kiszolgálóréteg** előre kiszámított adatokból áll, amelyek kötegnézeteket (például összesítéseket, konkrét szeletelőket stb.) eredményeznek a gyors lekérdezésekhez.
  * A **Lambda Architecture Rearchitected - Batch to Serving Layer** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) lenyomja a kötegadatokat a kiszolgáló rétegbe; ez azt, hogy a Spark lekérdezi a tweetek kötegelt gyűjteményét, feldolgozza azt, és egy másik gyűjteménybe (egy számított kötegbe) tárolja.
    * **Sebesség réteg:** A **sebességréteg** az Azure Cosmos DB változási hírcsatornáját használó Sparkból áll, amely azonnal elolvassa és eljár. Az adatok kiszámítható RT-be is *menthetők,* így más rendszerek is lekérdezhetik a feldolgozott valós idejű adatokat, nem pedig maguk a valós idejű lekérdezések futtatását.
  * A [Streamelési lekérdezés a Cosmos DB change feed scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) parancsfájl egy streamelési lekérdezést hajt végre az Azure Cosmos DB módosítási csatornából a spark-shell időköze közötti szám kiszámításához.
  * A [Streamelési címkék lekérdezés cosmos DB változási csatorna scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) parancsfájl egy streamelési lekérdezést hajt végre az Azure Cosmos DB módosítási csatornából a címkék időköze közötti szám ának kiszámításához a spark-shell.
  
## <a name="next-steps"></a>További lépések
Ha még nem tette meg, töltse le a Spark az Azure Cosmos DB-összekötőt az [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-tárházból, és fedezze fel a további erőforrásokat a tárházban:
* [Lambda architektúra](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Példák elosztott összesítések](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Példaparancsfájlok és jegyzetfüzetek](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Strukturált streamelési bemutatók](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Hírcsatorna-bemutatók módosítása](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Streamelési módosítások az Azure Cosmos DB change feed és az Apache Spark használatával](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Érdemes lehet az [Apache Spark SQL, DataFrames és adatkészletek útmutatóés](https://spark.apache.org/docs/latest/sql-programming-guide.html) az Apache Spark az [Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) cikket is érdemes áttekintenie.
