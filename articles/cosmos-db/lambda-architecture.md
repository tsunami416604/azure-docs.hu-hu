---
title: Lambda architektúra az Azure Cosmos DB és a HDInsight (az Apache Spark)
description: Ez a cikk bemutatja, hogyan lehet Azure Cosmos DB, a HDInsight és a Spark használatával lambda architektúra megvalósítása
ms.service: cosmos-db
author: tknandu
ms.author: ramkris
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 3c59b96146928a066c70113cb3fb1cd1915d9c8b
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034012"
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Az Azure Cosmos DB: Lambda architektúra megvalósítása az Azure platformon 

Lambda architektúra nagy adatkészletek hatékony adatfeldolgozó engedélyezése. Lambda architektúra kötegelt feldolgozási, az adatfolyam-feldolgozás és a egy kiszolgálórétegbe használja a big Data típusú adatok lekérdezése részt a késés minimalizálása érdekében. 

Lambda architektúra megvalósítása az Azure-ban, kombinálhatja a következő technológiákat, valós idejű big data-elemzések felgyorsítása:
* [Az Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), az iparág első globálisan elosztott, többmodelles adatbázis-szolgáltatás. 
* [Az Apache Spark for Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), egy feldolgozást végző keretrendszer, amely nagyméretű adatelemzési alkalmazások
* Az Azure Cosmos DB [módosításcsatornáját](change-feed.md), amely új adatok feldolgozása a HDInsight a kötegelt rétegének adatfolyamok
* A [Spark az Azure Cosmos DB-összekötő](spark-connector.md)

Ez a cikk az eredeti többrétegű tervezési és a egy "rearchitected" lambda architektúra, amely egyszerűbbé teszi a műveletek előnyeit alapján lambda architektúra alapjait ismerteti.  

## <a name="what-is-a-lambda-architecture"></a>Mit jelent a lambda architektúra?
Lambda architektúra általános, méretezhető, és hibatűrő adatfeldolgozási címre architektúra batch- és gyorsítsa fel a késési igényű helyzetekben leírtak szerint [Nathan Marz](https://twitter.com/nathanmarz).

![Lambda architektúra ábrája](./media/lambda-architecture/lambda-architecture-intro.png)

Forrás: http://lambda-architecture.net/

Lambda architektúra alapelveinek ismertetett megfelelően a fenti ábrán [ http://lambda-architecture.net ](http://lambda-architecture.net/).

 1. Az összes **adatok** leküldéssel kerülnek *mindkét* a *kötegelt réteg* és *gyors réteg*.
 2. A **kötegelt réteg** fő adatkészletet (nyers adatok nem módosítható, csak set) rendelkezik, és előre kiszámítja a batch-nézeteket.
 3. A **kiszolgálórétegbe** gyors lekérdezések batch nézeteket tartalmazza. 
 4. A **gyors réteg** kompenzálja feldolgozási ideje (a kiszolgálórétegbe), és csak a legutóbbi adatok foglalkozik.
 5. Az összes lekérdezés batch nézetek és a valós idejű nézetek vagy a pingelés őket egyenként példányösszevonás eredmények alapján választ.

Után további információ, azt fogja tudni megvalósítása ebben az architektúrában csak az alábbi:

* Az Azure Cosmos DB próbaidőszakában
* (Az Apache Spark 2.1-es) HDInsight-fürt
* Spark-összekötő [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>Gyors réteg

Műveletek szempontból két adatfolyamot fenntartása, hogy Mindeközben az adatok megfelelő állapotban lehet egy bonyolult feladat. Megkönnyítik a műveleteket, hogy kihasználhassák a [Azure Cosmos DB-módosítási hírcsatorna támogatása](change-feed.md) tartani az állapotot a *kötegelt réteg* közben illesztőprogramban keresztül az Azure Cosmos DB – módosítási napló a *módosítása hírcsatorna API* számára a *gyors réteg*.  
![Kiemelés, az új adatokat, a gyors réteg és a fő adatkészletet része a lambda architektúra diagramja](./media/lambda-architecture/lambda-architecture-change-feed.png)

Ezek a rétegek fontos:

 1. Az összes **adatok** leküldéssel *csak* az Azure Cosmos DB, így elkerülheti a több döntő problémákat.
 2. A **kötegelt réteg** fő adatkészletet (nyers adatok nem módosítható, csak set) rendelkezik, és előre kiszámítja a batch-nézeteket.
 3. A **kiszolgálórétegbe** a következő szakaszban tárgyalt.
 4. A **gyors réteg** HDInsight (az Apache Spark), olvassa el az Azure Cosmos DB-módosítási hírcsatorna használja. Ez lehetővé teszi, hogy továbbra is fennáll, valamint az adatok lekérdezése és a egy időben feldolgozni azt.
 5. Az összes lekérdezés batch nézetek és a valós idejű nézetek vagy a pingelés őket egyenként példányösszevonás eredmények alapján választ.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Példa: A Spark strukturált streamelés, egy Azure Cosmos DB – csatorna módosítása
Futtatásához az Azure Cosmos DB változáscsatorna részeként egy gyors prototípust a **gyors réteg**, Twitter-adatok használatával részeként ki tesztelheti a [Stream feldolgozása módosításait az Azure Cosmos DB módosítása hírcsatorna és Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)példa. Indítsa be a Twitter-kimenetben, lásd a Kódmintát [Cosmos DB-hez a Twitter hírcsatorna Stream](https://github.com/tknandu/TwitterCosmosDBFeed). Az előző példában a Twitter-adatok tölt be az Azure Cosmos DB-be, és ezután beállíthat a HDInsight (az Apache Spark-) fürthöz való csatlakozáshoz a változáscsatorna. Ez a konfiguráció beállítása a további információkért lásd: [Apache Spark az Azure Cosmos DB-összekötő telepítése](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

A következő kódrészletet bemutatja, hogyan konfigurálhatja `spark-shell` strukturált folyamatos átviteli feladatok futtatásához szeretne csatlakozni egy Azure Cosmos DB változáscsatorna, amely áttekinti a valós idejű Twitter data stream futtatásának időköz száma végrehajtásához.

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

Teljes körű Kódminták, lásd: [azure-cosmos DB-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), többek között:
* [A Cosmos DB módosítás Feed.scala streamelési lekérdezés](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Cosmos DB módosítás Feed.scala streamelési címkék lekérdezése](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Ennek kimenete egy `spark-shell` konzol, amely folyamatosan fut, amely végrehajtja az időköz száma, szemben a Twitter-adatok az Azure Cosmos DB – csatorna módosítása a strukturált a folyamatos átviteli feladatok. Az alábbi képen látható a stream-feladat kimenetét, és az időközt számát.

![Az intervallum száma ellen az Azure Cosmos DB-módosítási hírcsatorna a Twitter-adatok megjelenítése kimeneti adatfolyam](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

További információ az Azure Cosmos DB módosításcsatornáját, lásd:

* [A módosítási hírcsatorna támogatása az Azure Cosmos DB használata](change-feed.md)
* [Bemutatkozik az Azure cosmos DB-módosítási hírcsatorna feldolgozói kódtára](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [A Stream feldolgozni a módosításokat: Az Azure cosmos DB-módosítási hírcsatorna + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Batch- és szolgálja ki a Rétegek
Mivel az új adatok betöltése az Azure Cosmos DB (ahol a módosítási hírcsatorna használatos a gyors réteg), ez hol van a **fő adatkészletet** (egy nem módosítható, csak hozzáfűzése nyers adatok készletét) található. Innentől kezdve használni (az Apache Spark) HDInsight előtti számítási feladatait a **kötegelt réteg** való **kiszolgálórétegbe**, ahogy az alábbi képen látható:

![A diagram a kötegelt réteg és a lambda architektúra kiszolgálórétegbe menüpont kiemelve](./media/lambda-architecture/lambda-architecture-batch-serve.png)

Ezek a rétegek fontos:

 1. Az összes **adatok** a rendszer előkészítésre csak az Azure Cosmos DB (csoportos küldési problémák elkerülése érdekében).
 2. A **kötegelt réteg** rendelkezik egy Azure Cosmos DB-ben tárolt fő adatkészletet (nyers adatok nem módosítható, csak set). HDI Spark használatával, előre számítási és az összesítések a számított kötegelt nézetben kell tárolni.
 3. A **kiszolgálórétegbe** rendelkező gyűjtemények esetében a fő adatkészletet egy Azure Cosmos DB-adatbázis és a kötegelt nézet számított.
 4. A **gyors réteg** Ez a cikk későbbi részében olvashat.
 5. Az összes lekérdezés eredményeinek valós idejű és a batch-nézeteket az egyesítés vagy külön-külön ping paranccsal választ.

### <a name="code-example-pre-computing-batch-views"></a>Példa: Előre számítási batch-nézetek
Hogyan hajtható végre előre számított nézeteinek bemutatására a **fő adatkészletet** Apache Spark az Azure Cosmos DB, használja az alábbi kódrészletek az a notebookok [Lambda architektúra Rearchitected - kötegelt réteg ](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) és [Lambda architektúra Rearchitected - köteg réteg szolgáltató](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). Ebben a forgatókönyvben az Azure Cosmos DB-ben tárolt Twitter-adatok használata.

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

Most, hogy a lekérdezés, nézzük menthetők vissza egy gyűjteményt a Spark-összekötő használatával a kimeneti adatok mentéséhez egy másik gyűjteménybe.  Ebben a példában a Scala használatával a kapcsolat bemutatására. Hasonló az előző példában a mentse az Apache Spark DataFrame egy másik Azure Cosmos DB-gyűjtemények a konfigurációs kapcsolat hozható létre.

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

Adjon meg a `SaveMode` (jelző e `Overwrite` vagy `Append` dokumentumok), hozzon létre egy `tweets_bytags` DataFrame hasonló az előző példában a Spark SQL-lekérdezést.  Az a `tweets_bytags` DataFrame létrehozni, mentheti a fájlt használja a `write` módszer használatával a korábban megadott `writeConfig`.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Az utolsó utasítás most már mentette a Spark DataFrame egy új Azure Cosmos DB gyűjteménybe; Ez az a lambda architektúra szempontjából, a **kötegelt nézetben** belül a **kiszolgálórétegbe**.
 
#### <a name="resources"></a>További források

Teljes körű Kódminták, lásd: [azure-cosmos DB-spark/lambda/samples](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) többek között:
* Lambda architektúra Rearchitected - kötegelt réteg [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda architektúra Rearchitected - kötegelt réteg szolgáltató [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Gyors réteg
Korábban feljegyzett, a az Azure Cosmos DB módosítási hírcsatorna kódtár lehetővé teszi a batch- és sebesség rétegek között a műveletek egyszerűsítése érdekében. Ebben az architektúrában az Apache Spark (keresztül a HDInsight) segítségével hajtsa végre a *strukturált streamelés* lekérdezéseket az adatokat. Érdemes azt is, ideiglenesen megőrizhető a structured streaming lekérdezések eredményeit, így más rendszerek hozzáférhetnek az ezeket az adatokat.

![Kiemelés a gyors réteg a lambda architektúra diagramja](./media/lambda-architecture/lambda-architecture-speed.png)

Ehhez hozzon létre egy külön Azure Cosmos DB-gyűjtemények menteni a structured streaming lekérdezések eredményeit.  Ez lehetővé teszi, más rendszerek hozzáférés ezt az információt nem csak az Apache Spark. Valamint a Cosmos DB idő-az-élettartam (TTL) szolgáltatással konfigurálhatja a dokumentumok automatikusan beállított idő elteltével törölni.  Az Azure Cosmos DB TTL szolgáltatás további információkért lásd: [adatok az Azure Cosmos DB-gyűjtemények automatikusan az élettartam elévülése](time-to-live.md)

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

### <a name="resources"></a>További források

 * **Az új adatok**: A [a cosmosdb-be a Twitter hírcsatorna stream](https://github.com/tknandu/TwitterCosmosDBFeed), amely az új adatok leküldése az Azure Cosmos DB-be való mechanizmusa.
 * **Kötegelt réteg:** A kötegelt réteg áll, amelyek a *fő adatkészletet* (egy nem módosítható, csak hozzáfűzése nyers adatok készletét), és előre a batch nézeteket, amelyek leküldéssel kerülnek az adatok számítási lehetővé teszi a **kiszolgálórétegbe**.
    * A **Lambda architektúra Rearchitected - kötegelt réteg** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) lekérdezések a *fő adatkészletet* batch-nézetek készlete.
 * **Kiszolgálórétegbe:** A **kiszolgálórétegbe** áll előre kiszámított adatok kötegelt nézeteket (például összesítések, adott szeletelők, stb.) gyors lekérdezéseket eredményez.
    * A **Lambda architektúra Rearchitected - kötegelt réteg szolgáltató** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) a batch-adatokat leküldi az kiszolgálórétegbe a; azt jelenti, Spark- lekérdezi a tweeteket batch gyűjteménye, dolgozza fel, és tárolja azokat egy másik gyűjteménybe (számított batch).
* **Gyors réteg:** A **gyors réteg** szintből áll, a Spark használatával az Azure Cosmos DB változáscsatorna, olvassa el, és azonnal reagálhat rájuk. Az adatok is menthetők *RT számított* úgy, hogy más rendszerek lekérdezheti a feldolgozott valós idejű adatokat figyelésekor fut egy valós idejű lekérdezési magukat.
    * A [adatfolyam-lekérdezést az, Cosmos DB módosítása hírcsatorna](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) scala parancsfájl végrehajtja a streamelési lekérdezést a Azure Cosmos DB-Váltás az intervallum száma a spark-shell a compute-hírcsatorna.
    * A [címkék lekérdezése Streamelési Cosmos DB módosítása hírcsatorna](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) scala parancsfájl végrehajtja a streamelési lekérdezést az Azure Cosmos DB változáscsatorna számítási-címkéket a spark-shell az intervallum száma.
  
## <a name="next-steps"></a>További lépések
Ha még nem tette, töltse le a Spark az Azure Cosmos DB-összekötő a [azure-cosmos DB-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-adattár és a tárházban további források:
* [Lambda architektúra](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Elosztott összesítések példák](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Mintaszkriptek és -jegyzetfüzetek](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Strukturált streamelés bemutatók](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [A változáscsatorna bemutatók](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [A Stream feldolgozni a módosításokat az Azure Cosmos DB módosítási hírcsatorna és az Apache Spark használatával](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Emellett érdemes áttekinteni a [adatkészletek útmutató, Apache Spark SQL és DataFrames](https://spark.apache.org/docs/latest/sql-programming-guide.html) és a [Apache Spark on Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) cikk.
