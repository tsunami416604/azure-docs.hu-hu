---
title: Lambda architektúra Azure Cosmos DB és Apache Spark
description: Ez a cikk bemutatja, hogyan lehet Azure Cosmos DB, a HDInsight és a Spark használatával lambda architektúra megvalósítása
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
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Az Azure Cosmos DB: A lambda architektúra megvalósítása az Azure platformon 

Lambda architektúra nagy adatkészletek hatékony adatfeldolgozó engedélyezése. Lambda architektúra kötegelt feldolgozási, az adatfolyam-feldolgozás és a egy kiszolgálórétegbe használja a big Data típusú adatok lekérdezése részt a késés minimalizálása érdekében. 

Lambda architektúra megvalósítása az Azure-ban, kombinálhatja a következő technológiákat, valós idejű big data-elemzések felgyorsítása:
* [Azure Cosmos db](https://azure.microsoft.com/services/cosmos-db/)az iparág első globálisan elosztott, többmodelles adatbázis-szolgáltatása. 
* [Apache Spark for Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), egy nagy léptékű adatelemzési alkalmazásokat futtató feldolgozási keretrendszer
* Azure Cosmos DB [változási hírcsatorna](change-feed.md), amely az új adatforgalmat a Batch-rétegbe továbbítja a HDInsight feldolgozásához
* A [Spark – Azure Cosmos db összekötő](spark-connector.md)

Ez a cikk az eredeti többrétegű tervezési és a egy "rearchitected" lambda architektúra, amely egyszerűbbé teszi a műveletek előnyeit alapján lambda architektúra alapjait ismerteti.  

## <a name="what-is-a-lambda-architecture"></a>Mit jelent a lambda architektúra?
A lambda architektúra egy általános, méretezhető és hibatűrő adatfeldolgozási architektúra a Batch-és a sebességbeli késési forgatókönyvek a [Nathan Marz](https://twitter.com/nathanmarz)által leírt módon történő kezeléséhez.

![Lambda architektúra ábrája](./media/lambda-architecture/lambda-architecture-intro.png)

Forrás: http://lambda-architecture.net/

A lambda architektúra alapelveit az előző ábrán [http://lambda-architecture.net](http://lambda-architecture.net/)szerint kell ismertetni.

 1. Minden adatküldés a *Batch rétegbe* *és a* *sebesség rétegbe*történik.
 2. A **Batch-réteg** fő adatkészlete (nem módosítható, csak a nyers adatok hozzáfűzése) és előre kiszámítja a Batch-nézeteket.
 3. A **kiszolgáló réteg** batch-nézeteket tartalmaz a gyors lekérdezésekhez. 
 4. A **sebesség réteg** kompenzálja a feldolgozási időt (a kiszolgálási rétegre), és csak a legutóbbi adatmennyiséget tárgyalja.
 5. Az összes lekérdezés batch nézetek és a valós idejű nézetek vagy a pingelés őket egyenként példányösszevonás eredmények alapján választ.

Után további információ, azt fogja tudni megvalósítása ebben az architektúrában csak az alábbi:

* Azure Cosmos-tároló (k)
* (Az Apache Spark 2.1-es) HDInsight-fürt
* Spark-összekötő [1,0](https://search.maven.org/artifact/com.microsoft.azure/azure-cosmosdb-spark_2.1.0_2.11/1.2.6/jar)

## <a name="speed-layer"></a>Gyors réteg

Műveletek szempontból két adatfolyamot fenntartása, hogy Mindeközben az adatok megfelelő állapotban lehet egy bonyolult feladat. A műveletek leegyszerűsítéséhez használja a [Azure Cosmos db módosítási csatornájának támogatását](change-feed.md) , hogy megtartsa a *Batch-réteg* állapotát, miközben a *sebesség rétegének* *change feed API* -n keresztül felfedi az Azure Cosmos db változási naplót.  
![diagram a lambda architektúra új adatait, sebességi rétegét és fő adatkészlet részét kiemelve](./media/lambda-architecture/lambda-architecture-change-feed.png)

Ezek a rétegek fontos:

 1. Az összes **adattal** *csak* Azure Cosmos db küldenek, így elkerülhetők a többszörös kiöntési problémák.
 2. A **Batch-réteg** fő adatkészlete (nem módosítható, csak a nyers adatok hozzáfűzése) és előre kiszámítja a Batch-nézeteket.
 3. A **kiszolgáló réteget** a következő szakaszban tárgyaljuk.
 4. A **sebesség réteg** a HDInsight (Apache Spark) használja a Azure Cosmos db változási csatorna olvasásához. Ez lehetővé teszi, hogy továbbra is fennáll, valamint az adatok lekérdezése és a egy időben feldolgozni azt.
 5. Az összes lekérdezés batch nézetek és a valós idejű nézetek vagy a pingelés őket egyenként példányösszevonás eredmények alapján választ.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Példa: A Spark strukturált streamelés, egy Azure Cosmos DB – csatorna módosítása
Ha az Azure Cosmos DB változási csatornájának gyors prototípusát szeretné futtatni a **sebesség réteg**részeként, akkor a Twitter-adataival tesztelheti az [adatfolyam-feldolgozási módosítások részét képező Azure Cosmos db változási hírcsatorna és Apache Spark példa használatával](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark) . A Twitter-kimenet beindításához tekintse meg a [twitterről Cosmos DBre való stream-hírcsatornában](https://github.com/tknandu/TwitterCosmosDBFeed)található kód mintát. Az előző példában a Twitter-adatok tölt be az Azure Cosmos DB-be, és ezután beállíthat a HDInsight (az Apache Spark-) fürthöz való csatlakozáshoz a változáscsatorna. A konfiguráció beállításával kapcsolatos további információkért lásd: [Apache Spark Azure Cosmos db Connector telepítője](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

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

Ennek a kimenete egy `spark-shell`-konzol, amely folyamatosan futtat egy strukturált streaming-feladatot, amely intervallumok számát hajtja végre a Twitter-adatokból a Azure Cosmos DB változási csatornán. Az alábbi képen látható a stream-feladat kimenetét, és az időközt számát.

![Az intervallum száma ellen az Azure Cosmos DB-módosítási hírcsatorna a Twitter-adatok megjelenítése kimeneti adatfolyam](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

További információ az Azure Cosmos DB módosításcsatornáját, lásd:

* [A módosítási hírcsatorna támogatásának használata Azure Cosmos DB](change-feed.md)
* [Az Azure CosmosDB Change hírcsatorna-feldolgozó függvénytárának bemutatása](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [Adatfolyam-feldolgozási változások: az Azure CosmosDB Change feed + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Batch- és szolgálja ki a Rétegek
Mivel az új adatok betöltődik a Azure Cosmos DBba (ahol a változási csatornát használják a sebesség rétegnél), ez az a hely, ahol a **fő adatkészlet** (amely nem módosítható, csak a nyers adatokhoz tartozik). Ettől a ponttól kezdve a HDInsight (Apache Spark) használatával hajtsa végre az előre számítási függvényeket a **Batch rétegből** a **réteg kiszolgálásához**, ahogy az alábbi képen látható:

![A diagram a kötegelt réteg és a lambda architektúra kiszolgálórétegbe menüpont kiemelve](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Ezek a rétegek fontos:

 1. Az összes **adattal** csak Azure Cosmos db lesz leküldve (a többszörösen felmerülő problémák elkerülése érdekében).
 2. A **Batch-rétegben** található a Azure Cosmos DBban tárolt fő adatkészlet (nem módosítható, csak a nyers adatok hozzáfűzése). HDI Spark használatával, előre számítási és az összesítések a számított kötegelt nézetben kell tárolni.
 3. A **kiszolgáló réteg** egy Azure Cosmos-adatbázis, amely gyűjteményeket biztosít a fő adatkészlethez és a számított batch-nézethez.
 4. A **sebességmérő réteget** a cikk későbbi részében tárgyaljuk.
 5. Az összes lekérdezés eredményeinek valós idejű és a batch-nézeteket az egyesítés vagy külön-külön ping paranccsal választ.

### <a name="code-example-pre-computing-batch-views"></a>Példa: batch nézetek előre számítástechnika
Ahhoz, hogy bemutassa, hogyan hajthat végre előre kiszámított nézeteket a **fő adatkészletből** Apache Sparkról Azure Cosmos DBre, használja a következő kódrészleteket a jegyzetfüzetek [lambda architektúrája által átépített-batch rétegből](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) és a [lambda architektúrára, amely a réteg kiszolgálására szolgál](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). Ebben a forgatókönyvben az Azure Cosmos DB-ben tárolt Twitter-adatok használata.

Először hozzon létre a konfigurációs kapcsolat, a Twitter-adatok az Azure Cosmos DB az alábbi PySpark-kód használatával.

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

Ezután futtassa a következő Spark SQL-utasítást az első 10 hashtageket tweetek készlete meghatározásához. A Spark SQL-lekérdezés azt futtatja ezt a Jupyter notebook nélkül ez a kódrészlet következő kimeneti vonaldiagramon.

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

![Ábra, amely kiszolgálónként hashtaget tartalmazó tweeteket száma](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Most, hogy a lekérdezés, nézzük menthetők vissza egy gyűjteményt a Spark-összekötő használatával a kimeneti adatok mentéséhez egy másik gyűjteménybe.  Ebben a példában a Scala használatával a kapcsolat bemutatására. Az előző példához hasonlóan hozzon létre egy konfigurációs kapcsolódást, amely a Apache Spark DataFrame egy másik Azure Cosmos-tárolóba menti.

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
 
#### <a name="resources"></a>Erőforrások

A kód teljes mintáinak megtekintéséhez tekintse meg az [Azure-cosmosdb-Spark/lambda/Samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) című témakört, beleértve a következőket:
* Lambda architektúra újratervezése – batch réteg [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda architektúra áttervezve – Batch a réteg [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) - | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Gyors réteg
Korábban feljegyzett, a az Azure Cosmos DB módosítási hírcsatorna kódtár lehetővé teszi a batch- és sebesség rétegek között a műveletek egyszerűsítése érdekében. Ebben az architektúrában a Apache Spark (a HDInsight-n keresztül) segítségével hajtsa végre az adatokon végzett *strukturált adatfolyam* -lekérdezéseket. Érdemes azt is, ideiglenesen megőrizhető a structured streaming lekérdezések eredményeit, így más rendszerek hozzáférhetnek az ezeket az adatokat.

![Kiemelés a gyors réteg a lambda architektúra diagramja](./media/lambda-architecture/lambda-architecture-speed.png)

Ehhez hozzon létre egy külön Azure Cosmos-tárolót a strukturált adatfolyam-lekérdezések eredményeinek mentéséhez.  Ez lehetővé teszi, más rendszerek hozzáférés ezt az információt nem csak az Apache Spark. Valamint a Cosmos DB idő-az-élettartam (TTL) szolgáltatással konfigurálhatja a dokumentumok automatikusan beállított idő elteltével törölni.  Az Azure Cosmos DB TTL szolgáltatással kapcsolatos további információkért lásd: az [adatok automatikus elévülése az Azure Cosmos-tárolókban](time-to-live.md)

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

## <a name="lambda-architecture-rearchitected"></a>Lambda architektúra: Rearchitected
Az előző szakaszokban leírtaknak egyszerűsítheti az eredeti lambda architektúra a következő összetevők használatával:
* Azure Cosmos DB
* Az Azure Cosmos DB módosítási hírcsatorna Library elkerülése érdekében a csoportos küldésre kell az adatokat a batch- és sebesség rétegek között
* Az Apache Spark on HDInsight
* A Spark-összekötő az Azure Cosmos DB

![A lambda architektúra az Azure Cosmos DB, a Spark és az Azure Cosmos DB módosítási hírcsatorna API a rearchitecture bemutató ábra.](./media/lambda-architecture/lambda-architecture-re-architected.png)

Ez a kialakítás a két felügyelt szolgáltatások, az Azure Cosmos DB és a HDInsight csak kell. Együtt, oldja meg a batch, kiszolgáló és sebesség rétegeket, a lambda architektúra. Ez egyszerűbbé teszi a nem csak a műveleteket, de az adatok az adatfolyam is. 
 1. Az összes adat leküldéssel kerülnek az Azure Cosmos DB feldolgozáshoz
 2. A kötegelt réteg (nyers adatok nem módosítható, csak set) fő adatkészletet tartalmaz, és előre kiszámítja a batch-nézetek
 3. A kiszolgálórétegbe rendelkezik batch-nézetek az adatok gyors lekérdezések érdekében.
 4. A gyors réteg kompenzálja feldolgozási ideje (a kiszolgálórétegbe), és csak a legutóbbi adatok foglalkozik.
 5. Az összes lekérdezés eredményeinek valós idejű és kötegelt nézeteket egyesítésével választ.

### <a name="resources"></a>Erőforrások

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
