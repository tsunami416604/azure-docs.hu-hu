---
title: Lambda architektúra Azure Cosmos DB és Apache Spark
description: Ez a cikk bemutatja, hogyan valósítható meg egy lambda architektúra a Azure Cosmos DB, a HDInsight és a Spark használatával
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 68ce06d8a2904bf99f58a53817444b2992b23501
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719738"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Azure Cosmos DB: lambda architektúra implementálása az Azure platformon 

A lambda architektúrák lehetővé teszik a nagy adatkészletek hatékony adatfeldolgozását. A lambda architektúrák a kötegelt feldolgozást, az adatfolyam-feldolgozást, valamint a kiszolgáló réteget használják a big data lekérdezése során felmerülő késés minimalizálására. 

A lambda architektúra Azure-beli megvalósításához a következő technológiákat kombinálhatja a valós idejű big data Analitika felgyorsításához:
* [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/)az iparág első globálisan elosztott, többmodelles adatbázis-szolgáltatása. 
* [Apache Spark for Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), egy nagy léptékű adatelemzési alkalmazásokat futtató feldolgozási keretrendszer
* Azure Cosmos DB [változási hírcsatorna](change-feed.md), amely az új adatforgalmat a Batch-rétegbe továbbítja a HDInsight feldolgozásához
* A [Spark – Azure Cosmos db összekötő](spark-connector.md)

Ez a cikk a lambda architektúrák alapjait ismerteti az eredeti többrétegű kialakítás és az olyan "átalakított" lambda architektúra előnyei alapján, amely leegyszerűsíti a műveleteket.  

## <a name="what-is-a-lambda-architecture"></a>Mi az a lambda architektúra?
A lambda architektúra egy általános, méretezhető és hibatűrő adatfeldolgozási architektúra a Batch-és a sebességbeli késési forgatókönyvek a [Nathan Marz](https://twitter.com/nathanmarz)által leírt módon történő kezeléséhez.

![Lambda architektúrát ábrázoló diagram](./media/lambda-architecture/lambda-architecture-intro.png)

Forrás: http://lambda-architecture.net/

A lambda architektúra alapelveit az előző ábrán [http://lambda-architecture.net](http://lambda-architecture.net/)szerint kell ismertetni.

 1. Minden adatküldés a *Batch rétegbe* *és a* *sebesség rétegbe*történik.
 2. A **Batch-réteg** fő adatkészlete (nem módosítható, csak a nyers adatok hozzáfűzése) és előre kiszámítja a Batch-nézeteket.
 3. A **kiszolgáló réteg** batch-nézeteket tartalmaz a gyors lekérdezésekhez. 
 4. A **sebesség réteg** kompenzálja a feldolgozási időt (a kiszolgálási rétegre), és csak a legutóbbi adatmennyiséget tárgyalja.
 5. Az összes lekérdezés megválaszolható úgy, hogy az eredményeket a Batch-nézetekből és a valós idejű nézetekkel egyesíti, vagy egyenként pingelést végez.

További olvasnivalók esetén ezt az architektúrát csak a következőkkel tudjuk megvalósítani:

* Azure Cosmos-tároló (k)
* HDInsight (Apache Spark 2,1) fürt
* Spark-összekötő [1,0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.6/jar)

## <a name="speed-layer"></a>Sebesség réteg

A műveleti perspektívából két streamet tart fenn, miközben az adatok megfelelő állapotának biztosítása bonyolult feladat lehet. A műveletek leegyszerűsítéséhez használja a [Azure Cosmos db módosítási csatornájának támogatását](change-feed.md) , hogy megtartsa a *Batch-réteg* állapotát, miközben a *sebesség rétegének* *change feed API* -n keresztül felfedi az Azure Cosmos db változási naplót.  
![diagram a lambda architektúra új adatait, sebességi rétegét és fő adatkészlet részét kiemelve](./media/lambda-architecture/lambda-architecture-change-feed.png)

A következő rétegek fontosak:

 1. Az összes **adattal** *csak* Azure Cosmos db küldenek, így elkerülhetők a többszörös kiöntési problémák.
 2. A **Batch-réteg** fő adatkészlete (nem módosítható, csak a nyers adatok hozzáfűzése) és előre kiszámítja a Batch-nézeteket.
 3. A **kiszolgáló réteget** a következő szakaszban tárgyaljuk.
 4. A **sebesség réteg** a HDInsight (Apache Spark) használja a Azure Cosmos db változási csatorna olvasásához. Ez lehetővé teszi az adatmegőrzést, valamint az egyidejű lekérdezését és feldolgozását.
 5. Az összes lekérdezés megválaszolható úgy, hogy az eredményeket a Batch-nézetekből és a valós idejű nézetekkel egyesíti, vagy egyenként pingelést végez.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Példa programkódra: Spark strukturált streaming Azure Cosmos DB változási csatornához
Ha az Azure Cosmos DB változási csatornájának gyors prototípusát szeretné futtatni a **sebesség réteg**részeként, akkor a Twitter-adataival tesztelheti az [adatfolyam-feldolgozási módosítások részét képező Azure Cosmos db változási hírcsatorna és Apache Spark példa használatával](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) . A Twitter-kimenet beindításához tekintse meg a [twitterről Cosmos DBre való stream-hírcsatornában](https://github.com/tknandu/TwitterCosmosDBFeed)található kód mintát. Az előző példában a Twitter-adatait Azure Cosmos DBba tölti be, és beállíthatja, hogy a HDInsight (Apache Spark) fürt csatlakozhasson a változási csatornához. A konfiguráció beállításával kapcsolatos további információkért lásd: [Apache Spark Azure Cosmos db Connector telepítője](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

A következő kódrészlet azt mutatja be, hogyan konfigurálható `spark-shell` egy strukturált streaming-feladatok futtatásához egy Azure Cosmos DB változási csatornához való kapcsolódáshoz, amely a valós idejű Twitter adatfolyamot tekinti át a futó intervallumok számának elvégzéséhez.

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

A kód teljes mintáinak megtekintéséhez tekintse meg az [Azure-cosmosdb-Spark/lambda/Samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)című témakört, beleértve a következőket:
* [Adatfolyam-lekérdezés Cosmos DB változási hírcsatorna. Scala](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [A Cosmos DB változási csatornán keresztüli adatátviteli címkék lekérdezése](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Ennek a kimenete egy `spark-shell`-konzol, amely folyamatosan futtat egy strukturált streaming-feladatot, amely intervallumok számát hajtja végre a Twitter-adatokból a Azure Cosmos DB változási csatornán. Az alábbi képen az adatfolyam-feladatok kimenete és az intervallumok száma látható.

![Adatfolyam-kimenet, amely az Azure Cosmos DB változási csatornán a Twitter-adatok számának intervallumát mutatja](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

A Azure Cosmos DB változási csatornával kapcsolatos további információkért lásd:

* [A módosítási hírcsatorna támogatásának használata Azure Cosmos DB](change-feed.md)
* [Az Azure CosmosDB Change hírcsatorna-feldolgozó függvénytárának bemutatása](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Adatfolyam-feldolgozási változások: az Azure CosmosDB Change feed + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Köteg és rétegek kiszolgálása
Mivel az új adatok betöltődik a Azure Cosmos DBba (ahol a változási csatornát használják a sebesség rétegnél), ez az a hely, ahol a **fő adatkészlet** (amely nem módosítható, csak a nyers adatokhoz tartozik). Ettől a ponttól kezdve a HDInsight (Apache Spark) használatával hajtsa végre az előre számítási függvényeket a **Batch rétegből** a **réteg kiszolgálásához**, ahogy az alábbi képen látható:

![A lambda architektúra kötegelt rétegét és kiszolgáló rétegét ábrázoló diagram](./media/lambda-architecture/lambda-architecture-batch-serve.png)

A következő rétegek fontosak:

 1. Az összes **adattal** csak Azure Cosmos db lesz leküldve (a többszörösen felmerülő problémák elkerülése érdekében).
 2. A **Batch-rétegben** található a Azure Cosmos DBban tárolt fő adatkészlet (nem módosítható, csak a nyers adatok hozzáfűzése). A HDI Spark használatával előre kiszámíthatja, hogy az összesítések a számított batch-nézetekben legyenek tárolva.
 3. A **kiszolgáló réteg** egy Azure Cosmos-adatbázis, amely gyűjteményeket biztosít a fő adatkészlethez és a számított batch-nézethez.
 4. A **sebességmérő réteget** a cikk későbbi részében tárgyaljuk.
 5. Az összes lekérdezés megválaszolható úgy, hogy összefésüli az eredményeket a Batch-nézetekből és a valós idejű nézetekkel, vagy pingelést végez.

### <a name="code-example-pre-computing-batch-views"></a>Mintakód: előre számítástechnikai batch-nézetek
Ahhoz, hogy bemutassa, hogyan hajthat végre előre kiszámított nézeteket a **fő adatkészletből** Apache Sparkról Azure Cosmos DBre, használja a következő kódrészleteket a jegyzetfüzetek [lambda architektúrája által átépített-batch rétegből](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) és a [lambda architektúrára, amely a réteg kiszolgálására szolgál](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). Ebben az esetben használja a Azure Cosmos DBban tárolt Twitter-adattartalmakat.

Kezdjük azzal, hogy létrehozjuk a konfigurációs kapcsolódást a Twitter-adataihoz Azure Cosmos DBon belül az alábbi PySpark-kód használatával.

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

Ezután futtassa a következő Spark SQL-utasítást a tweetek első 10 hashtagének meghatározásához. Ehhez a Spark SQL-lekérdezéshez ezt a kódrészletet közvetlenül követő kimeneti sávdiagram nélkül futtatjuk Jupyter jegyzetfüzetben.

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

![A tweetek másodpercenkénti számát bemutató diagram](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Most, hogy már rendelkezik a lekérdezéssel, mentse vissza egy gyűjteménybe a Spark-összekötő használatával, és mentse a kimeneti adatokat egy másik gyűjteménybe.  Ebben a példában a Scala használatával bemutatjuk a kapcsolódást. Az előző példához hasonlóan hozzon létre egy konfigurációs kapcsolódást, amely a Apache Spark DataFrame egy másik Azure Cosmos-tárolóba menti.

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

A `SaveMode` megadása után (amely azt jelzi, hogy `Overwrite` vagy `Append` dokumentum), hozzon létre egy `tweets_bytags` DataFrame az előző példában szereplő Spark SQL-lekérdezéshez hasonlóan.  A létrehozott `tweets_bytags` DataFrame a korábban megadott `writeConfig`használatával mentheti a `write` metódussal.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Ez az utolsó utasítás most mentette a Spark-DataFrame egy új Azure Cosmos-tárolóba; a lambda architektúra szempontjából ez a **Batch-nézet** a **kiszolgáló rétegen**belül.
 
#### <a name="resources"></a>Segédanyagok és eszközök

A kód teljes mintáinak megtekintéséhez tekintse meg az [Azure-cosmosdb-Spark/lambda/Samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) című témakört, beleértve a következőket:
* Lambda architektúra újratervezése – batch réteg [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda architektúra áttervezve – Batch a réteg [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) - | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Sebesség réteg
Amint azt korábban említettük, a Azure Cosmos DB módosítás hírcsatorna-függvénytár használatával egyszerűbbé válik a köteg és a sebesség rétegek közötti műveletek egyszerűsítése. Ebben az architektúrában a Apache Spark (a HDInsight-n keresztül) segítségével hajtsa végre az adatokon végzett *strukturált adatfolyam* -lekérdezéseket. Érdemes lehet átmenetileg megőrizni a strukturált adatfolyam-lekérdezések eredményeit is, hogy más rendszerek is hozzáférhessenek ezekhez az adatforrásokhoz.

![A lambda architektúra sebességi rétegét kiemelő diagram](./media/lambda-architecture/lambda-architecture-speed.png)

Ehhez hozzon létre egy külön Azure Cosmos-tárolót a strukturált adatfolyam-lekérdezések eredményeinek mentéséhez.  Ez lehetővé teszi, hogy más rendszerek is hozzáférhessenek ehhez az információhoz, nem csupán Apache Spark. Továbbá a Cosmos DB élettartam (TTL) szolgáltatással beállíthatja, hogy a dokumentumok automatikusan törlődjenek a beállított időtartam után.  Az Azure Cosmos DB TTL szolgáltatással kapcsolatos további információkért lásd: az [adatok automatikus elévülése az Azure Cosmos-tárolókban](time-to-live.md)

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

## <a name="lambda-architecture-rearchitected"></a>Lambda architektúra: újratervezett
Ahogy az előző szakaszban is látható, az eredeti lambda architektúrát a következő összetevők használatával egyszerűsítheti le:
* Azure Cosmos DB
* A Azure Cosmos DB módosítja a hírcsatorna-függvénytárat, hogy ne kelljen több átadni az adatait a köteg és a sebesség rétegek között
* Apache Spark a HDInsight
* A Azure Cosmos DB Spark-összekötője

![A lambda architektúra újraarchitektúráját bemutató ábra a Azure Cosmos DB, a Spark és a Azure Cosmos DB Change feed API használatával](./media/lambda-architecture/lambda-architecture-re-architected.png)

Ebben a kialakításban csak két felügyelt szolgáltatásra, Azure Cosmos DBra és HDInsight van szükség. Együttesen a lambda architektúra batch, serving és Speed rétegeit kezelik. Ez nem csak a műveleteket, hanem az adatfolyamot is leegyszerűsíti. 
 1. A rendszer az összes adatAzure Cosmos DB feldolgozás céljából leküldi
 2. A Batch-réteg fő adatkészlete (nem módosítható, csak a nyers adatok hozzáfűzése) és előre kiszámítja a Batch-nézeteket.
 3. A kiszolgáló réteg a gyors lekérdezések esetében a Batch-nézeteket tartalmaz.
 4. A sebesség réteg kompenzálja a feldolgozási időt (a kiszolgálási rétegre), és csak a legutóbbi adatmennyiséget tárgyalja.
 5. Az összes lekérdezést választhatja a Batch-nézetek és a valós idejű nézetek eredményeinek egyesítésével.

### <a name="resources"></a>Segédanyagok és eszközök

* **Új adatok**: az [adatfolyam a Twitterről a CosmosDB](https://github.com/tknandu/TwitterCosmosDBFeed), amely az új adatok Azure Cosmos DBba való leküldésének mechanizmusa.
* **Batch-réteg:** A Batch-réteg a *fő adatkészletből* (a nyers adatok nem módosítható, csak Hozzáfűzéses készletből) áll, és lehetővé teszi, hogy előre kiszámítsa a Batch-nézeteket a **kiszolgáló rétegbe**küldött adatokból.
   * A **lambda architektúra újraépítésze – batch Layer** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) a Batch-nézetek *fő adatkészlet* -készletét kérdezi le.
* **Kiszolgáló létrehozása:** A **kiszolgáló réteg** előre kiszámított adatokból áll, amelyek a gyors lekérdezések esetében a kötegelt nézeteket (például összesítéseket, specifikus szeletelőket stb.) eredményezik.
  * A **lambda architektúra újratervezése – Batch a Layer** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) kiszolgálásához | a [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) leküldi a Batch-adatait a kiszolgáló rétegbe; Ez a Spark lekérdezi a tweetek batch-gyűjteményét, feldolgozza azt, és egy másik gyűjteménybe (egy számított kötegbe) tárolja őket.
    * **Sebesség réteg:** A **sebesség réteg** a sparkból áll, amely a Azure Cosmos db változási hírcsatornát használja az olvasáshoz, és azonnal intézkedik. Az adatok a *számított RT* -be is menthetők, így más rendszerek is lekérhetik a feldolgozott valós idejű adatok lekérését a valós idejű lekérdezés futtatásával szemben.
  * A [Cosmos db Change feed](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) Scala-parancsfájl adatfolyam-lekérdezése végrehajt egy adatfolyam-lekérdezést a Azure Cosmos db változási csatornából a Spark-shellben lévő intervallumok számításához.
  * A [Cosmos db Change feed Scala-parancsfájlból származó adatfolyam-címkék lekérdezése](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) végrehajt egy adatfolyam-lekérdezést a Azure Cosmos db változási csatornából, hogy kiszámítsa az intervallumok számát a Spark-shellből.
  
## <a name="next-steps"></a>Következő lépések
Ha még nem tette meg, töltse le a Sparkot Azure Cosmos DB összekötőt az [Azure-cosmosdb-Spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub adattárból, és fedezze fel a további forrásokat a tárházban:
* [Lambda architektúra](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Elosztott összesítések – példák](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Példa parancsfájlok és jegyzetfüzetek](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Strukturált adatfolyam-bemutatók](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Hírcsatorna-bemutatók módosítása](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Az adatfolyam-feldolgozás változásai Azure Cosmos DB változási csatornát és Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Érdemes áttekinteni a [Apache Spark SQL, a DataFrames és az adatkészletek útmutatóját](https://spark.apache.org/docs/latest/sql-programming-guide.html) , valamint az [Azure HDInsight-on található Apache Sparkt](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) is.
