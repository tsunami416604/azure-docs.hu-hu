---
title: Az Azure Cosmos DB és a HDInsight (Apache Spark) lambda architektúra |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan megvalósításához egy Azure Cosmos DB, HDInsight és Spark lambda architektúra
keywords: lambda-architektúra
services: cosmos-db
documentationcenter: ''
author: dennyglee
manager: kfile
editor: ''
ms.assetid: 273aeae9-e31c-4a43-b216-5751c46f212e
ms.service: cosmos-db
ms.workload: data-services
ms.topic: article
ms.date: 01/19/2018
ms.author: denlee
ms.openlocfilehash: 9c1a8c212fbacde9fbf69fdf9bf72ed2393786db
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="azure-cosmos-db-implement-a-lambda-architecture-on-the-azure-platform"></a>Az Azure Cosmos DB: Egy lambda architektúra valósítja meg az Azure platformon 

Lambda architektúrák nagy adatkészletek hatékony feldolgozásának engedélyezése. Lambda architektúrák részt big Data típusú adatok lekérdezése a késés csökkentése érdekében érdemes kötegfeldolgozási adatfolyam-feldolgozási és szolgáló réteg használja. 

A lambda architektúra végrehajtásához Azure kombinálhatja a következő technológiákat annak érdekében, valós idejű big data-elemzések:
* [Az Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), az iparág első globálisan elosztott, több modellre adatbázis-szolgáltatás. 
* [Az Apache Spark on Azure hdinsight](https://azure.microsoft.com/services/hdinsight/apache-spark/), a feldolgozást végző keretrendszer, amely a nagyméretű adatok analytics alkalmazások futnak
* Azure Cosmos DB [adatcsatorna módosítása](change-feed.md), amely az adatfolyamokat a kötegelt réteghez HDInsight feldolgozni az új adatok
* A [Spark on Azure Cosmos DB összekötővé](spark-connector.md)

Ez a cikk ismerteti a alapjai egy lambda architektúra alapján az eredeti többrétegű egy "rearchitected" lambda architektúra, amely egyszerűbbé teszi a műveletek előnyeit.  

A lambda architektúra és a rendelkezésre álló lambda architektúra minta erőforrások áttekintéséhez tekintse meg a következő videót:

> [!VIDEO https:///channel9.msdn.com/Events/Connect/2017/T135/player]
>

## <a name="what-is-a-lambda-architecture"></a>Mi az a lambda architektúra?
A lambda architektúra általános, méretezhető, és hibatűrő adatfeldolgozási címre architektúra kötegelt és késési igényű helyzetekben sebessége szerint [Nathan Marz](https://twitter.com/nathanmarz).

![A lambda architektúra ábrája](./media/lambda-architecture/lambda-architecture-intro.png)

Forrás: http://lambda-architecture.net/

A fenti ábrán megfelelően ismerteti a lambda-architektúra alapelvei [ https://lambda-architecture.net ](http://lambda-architecture.net/).

 1. Minden **adatok** a fejlesztőre *mindkét* a *kötegelt réteg* és *sebesség réteg*.
 2. A **kötegelt réteg** a fő adatkészletet (nem módosítható, csak nyers adatok készletét) rendelkezik, és előre kiszámítja a kötegelt nézeteket.
 3. A **szolgáló réteg** gyors lekérdezéseket kötegelt nézeteket tartalmaz. 
 4. A **sebesség réteg** kiegyenlíti feldolgozási idő (a szolgáló réteg), és csak a legfrissebb adatok foglalkozik.
 5. Az összes lekérdezés kötegelt nézetek és a valós idejű nézetek vagy a pingelés őket egyenként is válaszolhatók egyesítésének eredmények.

Követően további olvasása, azt lesz beállításától ebbe az architektúrába csak a következők:

* Az Azure Cosmos DB következő gyűjtemény(ek) készleteit szinkronizálja
* (Apache Spark 2.1) HDInsight-fürt
* Összekötő Spark [1.0](https://github.com/Azure/azure-cosmosdb-spark/tree/master/releases/azure-cosmosdb-spark_2.1.0_2.11-1.0.0)

## <a name="speed-layer"></a>Sebesség réteg

Műveletek szempontból két adatstreamek karbantartása során az adatok megfelelő állapotban lehet egy összetett feladat. Megkönnyítik a műveleteket, hogy kihasználhassák a [Azure Cosmos DB módosítás hírcsatorna támogatási](change-feed.md) a állapotban tartani a *kötegelt réteg* közben, hogy nem jeleníti meg az Azure Cosmos DB Változásnapló keresztül a *módosítása hírcsatorna API* a a *sebesség réteg*.  
![Kiemelve az új adatokat, a sebesség réteg és a fő dataset része a lambda architektúra diagramja](./media/lambda-architecture/lambda-architecture-change-feed.png)

A fontos ezek a rétegek:

 1. Minden **adatok** fejlesztőre *csak* be Azure Cosmos DB, így elkerülheti a többszörös adattípusokról problémákat.
 2. A **kötegelt réteg** a fő adatkészletet (nem módosítható, csak nyers adatok készletét) rendelkezik, és előre kiszámítja a kötegelt nézeteket.
 3. A **szolgáló réteg** a következő szakasz ismertet.
 4. A **sebesség réteg** HDInsight (Apache Spark) olvasni az Azure Cosmos DB módosítás adatcsatorna használja. Ez lehetővé teszi, hogy továbbra is fennáll, valamint az adatok lekérdezése és párhuzamosan dolgozza fel.
 5. Az összes lekérdezés kötegelt nézetek és a valós idejű nézetek vagy a pingelés őket egyenként is válaszolhatók egyesítésének eredmények.
 
### <a name="code-example-spark-structured-streaming-to-an-azure-cosmos-db-change-feed"></a>Példa: Spark strukturált streamelés egy hírcsatorna Azure Cosmos DB módosítása
A hírcsatorna részeként Azure Cosmos DB változás gyors prototípus futtatásához a **sebesség réteg**, tesztelheti soron Twitter-adatok részeként a [adatfolyam feldolgozása módosítások Azure Cosmos DB módosítása adatcsatorna és az Apache Spark](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)példa. A Twitter kimeneti jump-start, tekintse meg a kódmintában a [Cosmos DB a Twitter hírcsatorna adatfolyam](https://github.com/tknandu/TwitterCosmosDBFeed). Az előző példában a Twitter-adatokat tölt be az Azure Cosmos DB, és ezután beállíthat (Apache Spark) HDInsight-fürtjéhez adatcsatornához való kapcsolódáshoz. Ez a konfiguráció beállítása további információkért lásd: [az Apache Spark on Azure Cosmos DB összekötő telepítési](https://github.com/Azure/azure-cosmosdb-spark/wiki/Spark-to-Cosmos-DB-Connector-Setup).  

A következő kódrészletet ismerteti, hogyan konfigurálható `spark-shell` végezzen strukturált adatfolyam csatlakozni egy Azure Cosmos DB módosítása hírcsatornát, amely ellenőrzi, hogy a valós idejű Twitter adatfolyam esetében futtatásának időköz száma végrehajtásához.

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

Teljes mintakódok, lásd: [azure-cosmosdb-spark/lambda/minták](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda), többek között a következőket:
* [A Cosmos DB módosítása Feed.scala adatfolyam lekérdezés](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala)
* [Adatfolyam-továbbítási Cosmos DB módosítása Feed.scala címkék lekérdezése](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala)

Ennek eredménye egy `spark-shell` konzol, amely folyamatosan fut a strukturált adatfolyam-feladatot, amely elvégzi az intervallum száma a Twitter-adatok alapján a hírcsatorna Azure Cosmos DB váltás. A következő kép bemutatja az adatfolyam-feladat eredményének, és a időköz számát.

![Az intervallum száma a Twitter-adatok az Azure Cosmos DB módosítás csatornáról alapján megjelenítő kimeneti adatfolyam](./media/lambda-architecture/lambda-architecture-speed-layer-twitter-count.png) 

További információ az Azure-Cosmos adatbázis adatcsatorna módosításához lásd:

* [A módosítás adatcsatorna-támogatás az Azure Cosmos Adatbázisba használata](change-feed.md)
* [Introducing Azure CosmosDB módosítása hírcsatorna processzor könyvtár](https://azure.microsoft.com/blog/introducing-the-azure-cosmosdb-change-feed-processor-library/)
* [: Az adatfolyam feldolgozása Azure CosmosDB megváltoztatása adatcsatorna + Apache Spark](https://azure.microsoft.com/blog/stream-processing-changes-azure-cosmosdb-change-feed-apache-spark/)

## <a name="batch-and-serving-layers"></a>Kötegelt és a kiszolgáló rétegek
Mivel az új adatok betöltése az Azure Cosmos DB (ahol a módosítás hírcsatorna használatos a sebesség réteg), ez akkor, ha a **fő adatkészletet** (egy nem módosítható, csak a nyers adatok) tartozik. Ettől kezdve, használja a HDInsight (Apache Spark) előtti számítási feladatait from a **kötegelt réteg** való **szolgáló réteg**, a következő ábrán látható módon:

![A kötegelt réteg és a lambda architektúra szolgáló réteg kiemelés diagramja](./media/lambda-architecture/lambda-architecture-batch-serve.png)

A fontos ezek a rétegek:

 1. Minden **adatok** kerül csak az Azure Cosmos DB (csoportos küldési problémák elkerülése érdekében).
 2. A **kötegelt réteg** Azure Cosmos DB tárolt fő adatkészletet (nem módosítható, csak nyers adatok készletét) rendelkezik. HDI Spark használatával, előre számítási és a rendszer ne tárolja őket a számított kötegelt nézetek összesítések.
 3. A **szolgáló réteg** egy Azure Cosmos DB adatbázis fő adatkészlet gyűjteményéhez és számított kötegelt nézet.
 4. A **sebesség réteg** Ez a cikk későbbi részében olvashat.
 5. Az összes lekérdezés eredményeit a valós idejű és a kötegelt nézeteket az egyesítés, vagy külön-külön ping paranccsal is válaszolni.

### <a name="code-example-pre-computing-batch-views"></a>Példa: előre számítástechnikai kötegelt nézetek
Hogyan hajthat végre előre számított nézeteinek megjelenítve a **fő adatkészletet** az Apache Spark on Azure Cosmos DB, használja az alábbi kódrészleteket a notebookok a [Lambda architektúra Rearchitected - kötegelt réteg ](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) és [Lambda architektúra Rearchitected - réteg szolgál a kötegelt](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb). Ebben a forgatókönyvben a Azure Cosmos DB tárolt Twitter adatokat használják.

Először hozzon létre a Twitter-adatok Azure Cosmos DB használatával az alábbi kódot a PySpark belül a konfigurációs kapcsolatot.

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

Most futtassa a következő Spark SQL-utasítás határozza meg a Twitter-üzeneteket közül az első 10 hashtageket Tovább gombra. A Spark SQL-lekérdezés jelenleg futtatja a Jupyter notebook a kimeneti sávdiagram, közvetlenül a következő kódrészletet következő nélkül.

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

![A diagram Twitter-üzeneteket hashtaggel történő másodpercenkénti száma](./media/lambda-architecture/lambda-architecture-batch-hashtags-bar-chart.png)

Most, hogy a lekérdezést, most menti azt egy gyűjteményt a Spark-összekötővel a kimeneti adatok mentése egy másik gyűjteménybe.  Ebben a példában a Scala használatával kihasználhatják a kapcsolat. Hasonló az előző példában a hozzon létre egy másik Azure Cosmos DB gyűjteményt az Apache Spark DataFrame menteni a konfigurációs kapcsolata.

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

Adja meg a a `SaveMode` (, amely jelzi, hogy `Overwrite` vagy `Append` dokumentumok), hozzon létre egy `tweets_bytags` DataFrame hasonló az előző példában a Spark SQL-lekérdezésben.  Az a `tweets_bytags` DataFrame létrehozott, használatával mentheti a `write` módszer használatával a korábban megadott `writeConfig`.

```
// Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
import org.apache.spark.sql.{Row, SaveMode, SparkSession}

// Create new DataFrame of tweets tags
val tweets_bytags = spark.sql("select hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc")

// Save to Cosmos DB (using Append in this case)
tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
```

Az utolsó utasításnak most mentette a Spark DataFrame egy új Azure Cosmos DB gyűjteménybe; lambda architektúra szempontjából, ami a **nézet kötegelt** belül a **szolgáló réteg**.
 
#### <a name="resources"></a>További források

Teljes mintakódok, lásd: [azure-cosmosdb-spark/lambda/minták](vhttps://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda) többek között:
* Lambda architektúra Rearchitected - kötegelt réteg [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb)
* Lambda architektúra Rearchitected - szolgáltató réteghez kötegelt [HTML](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) | [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb)

## <a name="speed-layer"></a>Sebesség réteg
Az Azure Cosmos DB változás-hírcsatorna könyvtár használatával, mint korábban feljegyzett egyszerűsítheti a műveletek a kötegelt és sebességét, a rétegek közötti segítségével. Ebben az architektúrában az Apache Spark on (keresztül HDInsight) segítségével hajthatja végre a *strukturált streaming* lekérdezések írásában, az adatokat. Érdemes azt is, ideiglenesen megőrizhető a strukturált adatfolyam-lekérdezések eredményének, ezért a más rendszerekkel hozzáférnének ehhez ezen adatokhoz.

![Kiemelés a sebesség réteg a lambda architektúra diagramja](./media/lambda-architecture/lambda-architecture-speed.png)

Ehhez hozzon létre egy külön Azure Cosmos DB gyűjteményt a strukturált adatfolyam lekérdezések eredményeit.  Ez lehetővé teszi más rendszerek elérhessék ezt az információt nem csak az Apache Spark. A Cosmos DB idő élettartamát (TTL) szolgáltatás is beállíthatja beállított idő elteltével automatikusan törli a dokumentumok.  Az Azure Cosmos DB TTL szolgáltatásról további információkért lásd: [jár le automatikusan az élettartam Azure Cosmos DB gyűjteményekben lévő adatok](time-to-live.md)

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
Amint azt a korábbi szakaszokban, a következő összetevők egyszerűbbé teheti az eredeti lambda architektúra:
* Azure Cosmos DB
* Az Azure Cosmos DB módosítás hírcsatorna könyvtár elkerülése érdekében a csoportos küldésre kell az adatokat a kötegelt és sebességét, a rétegek között
* Az Apache Spark on hdinsight
* A Spark on Azure Cosmos DB-összekötő

![A lambda architektúra Azure Cosmos DB, Spark és az Azure Cosmos DB változás-hírcsatorna API használatával rearchitecture bemutató ábra](./media/lambda-architecture/lambda-architecture-re-architected.png)

Ezzel a kialakítással két felügyelt szolgáltatások, Azure Cosmos DB és HDInsight csak kell. Megoldást együtt, a kötegelt szolgál és sebesség rétegek a lambda architektúra. Ez leegyszerűsíti a nemcsak a műveletek, de az adatfolyam is. 
 1. Minden adatot az Azure Cosmos DB leküldött feldolgozás
 2. A kötegelt réteg a fő adatkészletet (nem módosítható, csak nyers adatok készletét) rendelkezik, és előre kiszámítja a kötegelt nézetek
 3. A szolgáló réteg adatok gyors lekérdezések kötegelt nézeteinek rendelkezik.
 4. A sebesség réteg kiegyenlíti feldolgozási idő (a szolgáló réteg), és csak a legfrissebb adatok foglalkozik.
 5. Az összes lekérdezés eredményeit a valós idejű és kötegelt nézeteket egyesítésével is válaszolni.

### <a name="resources"></a>További források

 * **Új adatok**: A [való CosmosDB a Twitter hírcsatorna adatfolyam](https://github.com/tknandu/TwitterCosmosDBFeed), amely, akkor az eszköz új adatokat küldeni az Azure Cosmos DB.
 * **Kötegelt réteg:** a kötegelt rétegből áll a *fő adatkészletet* (egy nem módosítható, csak nyers adatok készletét), és előre számítási kötegelt nézetek az adatok, amelyek be vannak leküldött képes a **szolgáló réteg** .
    * A **Lambda architektúra Rearchitected - kötegelt réteg** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20Layer.html) lekérdezések a *fő adatkészletet* kötegelt nézetek készlete.
 * **Szolgáló réteg:** a **szolgáló réteg** előre számított adatok kötegelt nézetek (például összesítéseket, adott szeletelők, stb.) a gyors lekérdezések eredményezve tevődik össze.
    * A **Lambda architektúra Rearchitected - szolgáltató réteghez kötegelt** notebook [ipynb](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.ipynb) | [html](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Lambda%20Architecture%20Re-architected%20-%20Batch%20to%20Serving%20Layer.html) leküldi a szolgáló réteg a kötegelt adatok; Ez azt jelenti, hogy Spark lekérdezi a Twitter-üzeneteket kötegelt gyűjteménye, folyamatokat engedélyez, és azt egy másik gyűjteménybe (számított kötegelt) tárolja.
* **Sebesség réteg:** a **sebesség réteg** hírcsatornát, hogy olvassa el és rájuk azonnal Azure Cosmos DB módosítása használata Spark tevődik össze. Az adatok is menthető *RT számított* , hogy a más rendszerekkel tudja lekérdezni felhasználókétól fut egy valós idejű feldolgozott valós idejű adatok lekérdezése magukat.
    * A [Streaming lekérdezést Cosmos DB módosítása hírcsatorna](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Query%20from%20Cosmos%20DB%20Change%20Feed.scala) scala parancsfájl egy adatfolyam-továbbítási lekérdezést végrehajtja a hírcsatornát, hogy az intervallum száma a spark-rendszerhéjból számítási Azure Cosmos DB váltás.
    * A [címkék lekérdezése Streaming Cosmos DB módosítása hírcsatorna](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/lambda/Streaming%20Tags%20Query%20from%20Cosmos%20DB%20Change%20Feed%20.scala) scala parancsfájl egy adatfolyam-továbbítási lekérdezést végrehajtja a hírcsatornát, hogy egy címkét a spark-rendszerhéjból időköz száma számítási Azure Cosmos DB váltás.
  
## <a name="next-steps"></a>További lépések
Ha még nem tette meg, töltse le a Spark on Azure Cosmos DB-összekötőt a [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-tárházban és vizsgálja meg a tárházban további források:
* [Lambda architektúrája](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples/lambda)
* [Elosztott összesítések példák](https://github.com/Azure/azure-documentdb-spark/wiki/Aggregations-Examples)
* [Mintaparancsfájlok és notebookok](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)
* [Adatfolyam-továbbítási strukturált bemutatók](https://github.com/Azure/azure-cosmosdb-spark/wiki/Structured-Stream-demos)
* [Adatcsatorna bemutatók módosítása](https://github.com/Azure/azure-cosmosdb-spark/wiki/Change-Feed-demos)
* [Az adatfolyam használata Azure Cosmos DB módosítás adatcsatorna és az Apache Spark változások feldolgozása](https://github.com/Azure/azure-cosmosdb-spark/wiki/Stream-Processing-Changes-using-Azure-Cosmos-DB-Change-Feed-and-Apache-Spark)

Is érdemes áttekinteni a [Apache Spark SQL, DataFrames és adatkészletek útmutató](http://spark.apache.org/docs/latest/sql-programming-guide.html) és a [Apache Spark on Azure Hdinsighttal](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) cikk.
