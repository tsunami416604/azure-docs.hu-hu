---
title: Csatlakozás az Apache Spark az Azure Cosmos DB
description: További információ az Azure Cosmos DB Spark-összekötő, amely lehetővé teszi az Apache Spark az Azure Cosmos DB-hez kapcsolódni. A több-bérlős, globálisan elosztott adatbázisrendszerként a Microsoft elosztott összesítések végezheti el.
keywords: az Apache spark
services: cosmos-db
author: tknandu
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: fd1fa105a880acb0d2b18a3b69ba8d475d8f82a3
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081591"
---
# <a name="accelerate-big-data-analytics-by-using-the-apache-spark-to-azure-cosmos-db-connector"></a>Gyorsítsa fel a big data-elemzés az Azure Cosmos DB-összekötő az Apache Spark használatával
 
Az Apache Spark és az Azure Cosmos DB közötti összekötő lehetővé teszi, hogy az Azure Cosmos DB legyen az Apache Spark-feladatok bemenete vagy kimenete. Csatlakozás [Spark](https://spark.apache.org//) való [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) gyorsítja megítélnie gyors ütemben data science kapcsolatos problémák megoldásához. Azure Cosmos DB segítségével gyorsan továbbra is fennáll, illetve adatokat kérdezhet le. Az összekötő hatékonyan használja az Azure Cosmos DB által felügyelt natív indexeket. Az indexek lehetővé teszik a frissíthető oszlopok használatát, amikor elemzéseket és leküldéses predikátumszűréseket végez gyorsan változó, globálisan elosztott adatokon. Az ilyen típusú adatok között lehet Internet of Things (IoT) adatok adatelemzési és analitikai feladatokhoz.

## <a name="connector-components"></a>Összekötő-összetevők

A Spark az Azure Cosmos DB-összekötő a következő összetevőket tartalmazza:

* [Az Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) kiépítése és rugalmasan méretezhető átviteli sebesség és a storage, tetszőleges számú földrajzi régió között.  

* [Az Apache Spark](https://spark.apache.org/) egy hatékony nyílt forráskódú motor, sebessége, a könnyű használat és a kifinomult elemzési épülnek.  

* [Apache Spark-fürt az Azure databricks szolgáltatásban](https://docs.azuredatabricks.net/getting-started/index.html) lehetővé teszi, hogy a Spark-feladatokat futtathat a Spark-fürtön.

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>Csatlakozás az Apache Spark az Azure Cosmos DB

Csatlakozás az Apache Spark és az Azure Cosmos DB kétféleképpen történhet:

- [Az Azure Cosmos DB SQL Python SDK](https://github.com/Azure/azure-documentdb-python), egy Python-alapú összekötőt, amely is nevezik *pyDocumentDB*.  

- [Az Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java), egy Java-alapú összekötőt.


**Támogatott verziók**

| Összetevő | Verzió |
|---------|-------|
|Apache Spark| 2.1.x, 2.2.x, 2.3.x |
| Scala|2.11|
| Az Azure Databricks futtatókörnyezet-verziója | > 3.4 |
| Azure Cosmos DB SQL Java SDK | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>Csatlakozás Python vagy a pyDocumentDB SDK használatával

Az alábbi ábrán a pyDocumentDB SDK megvalósítása architektúráját mutatja be:

![A Spark a pyDocumentDB DB-n keresztül az Azure Cosmos DB adatfolyam ábrája](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>Az adatfolyam

Az adatfolyam pyDocumentDB megvalósítása a következőképpen történik:

* A fő csomóponttal, a Spark az Azure Cosmos DB átjárócsomópont pyDocumentDB keresztül csatlakozik. Csak a Spark és az Azure Cosmos DB kapcsolatok adhatja meg. A megfelelő csomópontok főkiszolgáló és az átjáró kapcsolatot átláthatóak.  

* Az átjárócsomópont lehetővé teszi az Azure Cosmos DB, ahol a lekérdezést később fut a gyűjtemény partícióihoz az adatcsomópontok a lekérdezést. A válasz ezeket a lekérdezéseket küld vissza az átjárócsomópontnak, és adott eredményhalmaz adja vissza a Spark fő csomóponttal.  

* További (például egy Spark-adatkeretbe) ellen küldi el a Spark munkavégző csomópontokhoz feldolgozásra.  

Spark és az Azure Cosmos DB közötti kommunikáció a Spark-főcsomóponttal és az Azure Cosmos DB az átjárócsomópontok korlátozódik. A szállítási réteg a két csomópont között lehetővé teszi, hogy minél gyorsabban nyissa meg a lekérdezéseket.

Futtassa az alábbi lépéseket a csatlakozás a Spark az Azure Cosmos DB pyDocumentDB SDK használatával:

1. Hozzon létre egy [Azure Databricks-munkaterület](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) és a egy [Spark-fürt](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks). Az Azure Databricks-modul 4.0-s verziója tartalmazza az Apache Spark 2.3.0-át és Scala 2.11-et azon belül.  

2. Ha a fürt létrejött, és fut-e, lépjen a **munkaterület** > **létrehozás** > **könyvtár**.  
3. Az új szalagtár párbeszédpanelen válasszon **Python tojás feltöltése vagy PyPi** forrásaként. Adja meg **pydocumentdb** nevét, és válassza ki, **telepítése kódtár**. pyDocumentdb SDK már közzé van téve a pip-csomagoknak, így megkeresheti és telepítheti. 

   ![Az új könyvtár párbeszédpanel képernyőképe](./media/spark-connector/create-library.png)

4. A kódtár telepítését követően csatlakoztat a fürthöz, a korábban létrehozott.  

5. Lépjen a **munkaterület** > **létrehozása** > **Notebook**.  

6. Az a **jegyzetfüzet létrehozása** párbeszédpanelen adjon meg egy felhasználóbarát nevet, és válassza a **Python** nyelve. A legördülő listából válassza ki a korábban létrehozott fürt, és válassza ki **létrehozás**.  

7. A Futtatás néhány Spark lekérdezések futtatása a repülés mintaadatok üzemeltetett "doctorwho" az Azure Cosmos DB-fiókot. Ez egy nyilvánosan elérhető-e. A [azure-cosmos DB-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-tárház gazdagépek a [Read_Batch_PyDocumentDB](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Read_Batch_PyDocumentDB.ipynb) notebookot. A notebook importálása az Azure Databricks-fiókjába, és futtathatja. Az alábbi szakasz részletesen ismertetjük a kódblokkok funkcióit mutatja be.

A következő kódrészletet bemutatja, hogyan importálhatja a pyDocumentDB SDK-t, és a egy lekérdezés futtatása a Spark környezet. A kódtöredék feljegyzett a pyDocumentDB SDK tartalmazza az Azure Cosmos DB-fiókhoz való csatlakozáshoz szükséges kapcsolati paraméterek. Importálja a szükséges kódtárak, és konfigurálja a főkulcs és a gazdagép, az Azure Cosmos DB-ügyfél (pydocumentdb.document_client) létrehozása.


```python
# Import Necessary Libraries
import pydocumentdb
from pydocumentdb import document_client
from pydocumentdb import documents
import datetime

# Configuring the connection policy (allowing for endpoint discovery)
connectionPolicy = documents.ConnectionPolicy()
connectionPolicy.EnableEndpointDiscovery
connectionPolicy.PreferredLocations = ["Central US", "East US 2", "Southeast Asia", "Western Europe","Canada Central"]

# Set keys to connect to Azure Cosmos DB
masterKey = 'le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA=='
host = 'https://doctorwho.documents.azure.com:443/'
client = document_client.DocumentClient(host, {'masterKey': masterKey}, connectionPolicy)

```

Ezután lekérdezéseket is futtathat. A következő kódrészletet a airports.codes gyűjtemény a doctorwho fiókban csatlakozik, és a egy lekérdezést a Washington állambeli repülőtér városok kibontásához. 

```python
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

```

A lekérdezés futtatása után az eredmény egy "query_iterable. QueryIterable", amely egy Python-lista alakítja át. Ebben a listában, alakítja át egy Spark-adatkeretbe. 

```python
# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(list(query))

# Show data
df.show()
```

### <a name="considerations-when-using-pydocumentdb-sdk"></a>A pyDocumentDB SDK használatának szempontjai
A Spark csatlakoztathatók az Azure Cosmos DB SDK-t a következő esetekben ajánlott pyDocumentDB használatával:

* Python használni kívánt.  

* Állítsa be az Azure Cosmos DB-alapú Spark viszonylag kis eredményt vannak visszaadása. Vegye figyelembe, hogy az Azure Cosmos DB az alapul szolgáló adatkészlet Igen tekintélyes lehet, és szűrők alkalmazása vagy predikátum szűrők futtat az Azure Cosmos DB-forrás.

## <a name="connect-by-using-the-java-sdk"></a>Csatlakozás a Java SDK használatával

Az alábbi ábra az Azure Cosmos DB SQL Java SDK megvalósítása architektúráját mutatja be, és adatokat helyez át, a Spark munkavégző csomópontok között:

![A Spark az Azure Cosmos DB-összekötő adatfolyamot ábrája](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>Az adatfolyam

A Java SDK-t végrehajtása az adatfolyam a következőképpen történik:

* A Spark-főkiszolgáló csomópont csatlakozik a partíciótérképen beszerzése az Azure Cosmos DB átjárócsomópontnak. Csak a Spark és az Azure Cosmos DB-kapcsolatok adhatja meg. A megfelelő csomópontok főkiszolgáló és az átjáró kapcsolatot átláthatóak.  

* Ezt az információt Spark fő csomópont van megadva. Ezen a ponton meg kell tudni a lekérdezés határozza meg a partíciók és az Azure Cosmos DB, hozzá kell férnie a helyüket.  

* Ezek az információk átkerülnek a Spark munkavégző csomópontokhoz.  

* A Spark munkavégző csomópontok az Azure Cosmos DB-partíciók közvetlenül kapcsolódni, csomagolja ki, és térjen vissza az adatokat a Spark-partíciók, a munkavégző csomópontok.  

Spark és az Azure Cosmos DB közötti kommunikáció a jelentősen gyorsabb, mert az adatok áthelyezése az Azure Cosmos DB-adatcsomópontok (partíciók) és a Spark munkavégző csomópontok között. Ebben a példában néhány minta Twitter-adatok küldése az Azure Cosmos DB-fiókhoz, és az adatokat a lekérdezések futtatása Spark. A következő lépések segítségével minta Twitter adatokat írni az Azure Cosmos DB:

1. Hozzon létre egy [Azure Cosmos DB SQL API-fiók](create-sql-api-dotnet.md#create-a-database-account) és [gyűjtemény hozzáadása](create-sql-api-dotnet.md#add-a-collection) ahhoz a fiókhoz.  

2. Töltse le a [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed) Githubon található mintát. Ezzel a hírcsatornával használatával minta Twitter adatokat írni az Azure Cosmos DB-hez.  

3. Nyisson meg egy parancssort, és Tweepy és a pyDocumentdb modulok telepítéséhez a következő parancsok futtatásával:

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. Bontsa ki a Twitter-hírcsatornáján minta tartalmát, és nyissa meg a config.py fájlt. Frissítse a masterKey, a gazdagépet, a databaseId, a collectionId és a preferredlocations listában.  

5. Lépjen a `https://apps.twitter.com/`, és a Twitter-hírcsatornáján az alkalmazás regisztrálásához. Miután kiválasztotta az alkalmazás nevét, hogy megkapják a egy **fogyasztói kulcs, a fogyasztói titkos kulcs, a hozzáférési jogkivonatot és a hozzáférési jogkivonat titkos kulcs**. Másolja ki ezeket az értékeket, és frissítse azokat a config.py fájlt adja meg a Twitter-hírcsatorna alkalmazás programozott hozzáférést a Twitteren.   

6. Mentse a config.py fájlt. Nyisson meg egy parancssort, és a Python-alkalmazás futtatása a következő paranccsal:

   ```bash
   Python driver.py
   ```

7. Nyissa meg az Azure Cosmos DB-gyűjtemények a portálon, és győződjön meg arról, hogy a Twitter-adatok íródik a gyűjteményben.

### <a name="find-and-attach-the-java-sdk-to-the-spark-cluster"></a>Keresse meg és a Java SDK-t csatolja a Spark-fürt

1. Hozzon létre egy [Azure Databricks-munkaterület](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) és a egy [Spark-fürt](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks). Az Azure Databricks-modul 4.0-s verziója tartalmazza az Apache Spark 2.3.0-át és Scala 2.11-et azon belül.  

2. Ha a fürt létrejött, és fut-e, lépjen a **munkaterület** > **létrehozás** > **könyvtár**.  

3. Az a **új könyvtár** párbeszédpanelen válassza ki **Maven-koordináta** forrásaként. Adja meg a koordináta értékét **com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0**, és válassza ki **könyvtár létrehozása**. A Maven-függőségek feloldása, és a csomagot kell hozzáadni a munkaterülethez. Az előző Maven koordináta formátumban 2.3.0-át jelöli a Spark-verziót, 2.11-et a Scala-verziót, és 1.2.0-s vagy annál újabb jelenti az Azure Cosmos DB-összekötő verziójának. 

4. A kódtár telepítését követően csatlakoztat a fürthöz, a korábban létrehozott. 

Ez a cikk bemutatja, hogy a Spark-összekötő Java SDK-t a következő esetekben:

* Olvassa el a Twitter-adatok az Azure Cosmos DB-ből.  

* Olvasási Twitter-adatok az Azure Cosmos DB streamelési van.  

* Twitter-adatok írni az Azure Cosmos DB-hez. 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>Twitter-adatok olvasása az Azure Cosmos DB-ből
 
Ebben a szakaszban a Spark lekérdezéseket futtat a Twitter-adatok kötegelt olvasása az Azure Cosmos DB-ből. A [azure-cosmos DB-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-tárház gazdagépek a [Read_Batch_Twitter_Data](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Read_Batch_Twitter_Data.ipynb) notebookot. A notebook importálása az Azure Databricks-fiókjába, és frissítse a fiók URI-t, a főkulcs, adatbázis és gyűjtemény neve. A jegyzetfüzet futtatásához, vagy hozzon létre a következő:

1. Nyissa meg az Azure Databricks-fiókját, és válassza ki **munkaterület** > **létrehozás** > **Notebook**. 

2. Az a **jegyzetfüzet létrehozása** párbeszédpanelen adjon meg egy felhasználóbarát nevet, és válassza a **Python** nyelve. A legördülő listából válassza ki a korábban létrehozott fürt, és válassza ki **létrehozás**.  

3. Frissítse a végpont, főkulcs, adatbázis és gyűjtemény értékek csatlakoztathatja a fiókhoz. Tweetek beolvasása az spark.read.format() paranccsal.

   ```python
   # Configuration Map
   tweetsConfig = {
   "Endpoint" : "<Your Azure Cosmos DB endpoint>",
   "Masterkey" : "<Primary key of your Azure Cosmos DB account>",
   "Database" : "<Your Azure Cosmos DB database name>",
   "Collection" : "<Your Azure Cosmos DB collection name>", 
   "preferredRegions" : "East US",
   "SamplingRatio" : "1.0",
   "schema_samplesize" : "200000",
   "query_custom" : "SELECT c.id, c.created_at, c.user.screen_name, c.user.lang, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   }
   # Read Tweets
   tweets = spark.read.format("com.microsoft.azure.cosmosdb.spark").options(**tweetsConfig).load()
   tweets.createOrReplaceTempView("tweets")
   #tweets.cache()

   ```

4. Futtassa a lekérdezést, a Twitter-üzenetek száma a gyorsítótárazott adatokat a különböző hashtageket lekéréséhez. 

   ```python
   %sql
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

A Java SDK konfigurációs leképezés a következő értékeket támogatja: 

|Beállítás  |Leírás  |
|---------|---------|
|query_maxdegreeofparallelism  | Csoportok párhuzamos műveletek számát futtassa az ügyfél oldalán párhuzamos lekérdezés-végrehajtás során. Ha 0-nál nagyobb értékre van beállítva, a hozzárendelt értékre párhuzamos műveletek számát korlátozza. Ha 0-nál kisebb érték, a rendszer automatikusan úgy dönt, hogy futtassa a párhuzamos műveletek számát. Az összekötő-végrehajtó az egyes gyűjtemény partíciók térképek, ezt az értéket kell az olvasási műveletet gyakorolt hatását.        |
|query_maxbuffereditemcount     |    Úgy állítja be, amely képes lehet pufferelt elemek maximális száma az ügyfél oldalán, párhuzamos lekérdezés-végrehajtás során. Ha 0-nál nagyobb értékre van beállítva, a hozzárendelt értékre pufferelt elemek számát korlátozza. Ha 0-nál kisebb érték, a rendszer automatikusan úgy dönt, hogy a puffer elemek száma.     |
|query_enablescan    |   Engedélyezheti a lekérdezések, amelyek nem nyitható meg, mert az indexelés vizsgálatok volt csoportok kilépteti a programból a kért útvonalakon.       |
|query_disableruperminuteusage  |  Letiltja a kérelemegység (RU) / perc kiszolgálására képes kapacitást adhat a lekérdezéshez, ha rendszeresen kiosztott kérelemegység/s kimerült.       |
|query_emitverbosetraces   |   Beállítja a lehetőséget, hogy a lekérdezések kibocsátható vizsgálat részletes nyomkövetési ki.      |
|query_pagesize  |   A lekérdezés eredménye lap egyes lekérdezési kérés méretének beállítása. A teljesítmény optimalizálása, a nagy lapméret használatával csökkenthető a fetch eredmények való adatváltások számát.      |
|query_custom  |  Azure Cosmos DB lekérdezése felülírják az alapértelmezett lekérdezéssel, ha az adatok lekérése az Azure Cosmos DB-hez állítja be. Megjegyzés: Ha ezt az értéket ad meg, használhatja azt egy lekérdezés helyett leküldte a predikátumok is.     |

A forgatókönyvtől függően különféle konfigurációs értékeket kell használnia a teljesítmény és a teljesítmény optimalizálásához. Vegye figyelembe, hogy a konfigurációs kulcs jelenleg nem betűérzékeny, és a konfigurációs érték mindig egy karakterláncot.

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Az Azure Cosmos DB a streamelési olvasási Twitter-adatok

Ebben a szakaszban a Spark lekérdezéseket futtat egy módosítási hírcsatorna streamelési Twitter-adatok olvasásához. Bár ebben a szakaszban futtassa a lekérdezéseket, ellenőrizze, hogy a Twitter-hírcsatornáján alkalmazás fut, és adatokat az Azure Cosmos DB szivattyúzó. A [azure-cosmos DB-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-tárház gazdagépek a [Read_Stream_Twitter_Data](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Read_Stream_Twitter_Data.scala) notebookot. A notebook importálása az Azure Databricks-fiókjába, és frissítse a fiók URI-t, a főkulcs, adatbázis és gyűjtemény neve. A jegyzetfüzet futtatásához, vagy hozzon létre a következő:

1. Nyissa meg az Azure Databricks-fiókját, és válassza ki **munkaterület** > **létrehozás** > **Notebook**.  

2. Az a **jegyzetfüzet létrehozása** párbeszédpanelen adjon meg egy felhasználóbarát nevet, és válassza a **Scala** nyelve. A legördülő listából válassza ki a korábban létrehozott fürt, és válassza ki **létrehozás**.  

3. Frissítse a végpont, főkulcs, adatbázis és gyűjtemény értékek csatlakoztathatja a fiókhoz.

   ```scala
   // This script does the following:
   // - creates a structured stream from a Twitter feed CosmosDB collection (on top of change feed)
   // - get the count of tweets
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import org.codehaus.jackson.map.ObjectMapper
   import com.microsoft.azure.cosmosdb.spark.streaming._
   import java.time._

   val sourceConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB collection name>", 
   "ConnectionMode" -> "Gateway",
   "ChangeFeedCheckpointLocation" -> "/tmp",
   "changefeedqueryname" -> "Streaming Query from Cosmos DB Change Feed Internal Count")
   ```
4. Indítsa el a spark.readStream.format() paranccsal adatfolyamként olvasása a módosítási hírcsatorna:

   ```scala
   var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
   ```
5. A lekérdezés a konzol streamelésének megkezdéséhez:

   ```scala
   //**RUN THE ABOVE FIRST AND KEEP BELOW IN SEPARATE CELL
   val query = streamData.withColumn("countcol", streamData.col("id").substr(0,0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
   ```

A Java SDK konfigurációs leképezés a következő értékeket támogatja:

|Beállítás  |Leírás  |
|---------|---------|
|readchangefeed   |  Azt jelzi, hogy a gyűjtemény tartalmát az, cosmos DB-módosítási hírcsatorna letöltődött. Az alapértelmezett értéke FALSE (hamis).       |
|changefeedqueryname |   Egyéni karakterláncot a lekérdezés azonosításához. Az összekötő nyomon követi, hogy a gyűjtemény folytatási jogkivonatainak különböző változáscsatorna lekérdezések külön-külön. Ha readchangefeed értéke igaz, akkor ez egy szükséges konfiguráció, amely nem üres érték.      |
|changefeedcheckpointlocation  |   Egy elérési útja helyi file storage segítségével őrzi a folytatási token csomóponthibák esetén.      |
|changefeedstartfromthebeginning  |  Állítja be, hogy el kell indulnia a módosítási hírcsatorna, az elejétől (igaz) vagy az aktuális pontról (hamis). Alapértelmezés szerint indul az aktuális (hamis).       |
|rollingchangefeed  |   A legutóbbi lekérdezés egy logikai érték, amely azt jelzi, hogy a módosítási hírcsatorna kell lennie. Az alapértelmezett értéke FALSE (hamis), ami azt jelenti, hogy a módosítások a gyűjtemény első olvasási bájtjai számítanak.      |
|changefeedusenexttoken  |   Logikai érték feldolgozása sikertelen helyzetek feltételeinek megteremtésére. Azt jelzi, hogy az aktuális változáscsatorna köteg szabályosan kezelése. A Resiliant elosztott adatkészlet a következő folytatási token használatával kell módosításokat az ezt követő kötegelt beolvasása.      |
| InferStreamSchema | Logikai érték, amely azt jelzi-e a séma a streamelési adatok mintát venni streamelési elején. Alapértelmezés szerint az értéke igaz. Ez a paraméter értéke igaz, és a streamelési adatok sémaváltozások mintavételezés után, ha újonnan hozzáadott tulajdonságait a rendszer eldobja a streamelési adatok keretbe. <br/><br/> Ha azt szeretné, hogy a streamelési adatok keret sémafüggetlen kell, ez a paraméter "false" értékűre. Ebben a módban az Azure Cosmos DB-módosítási hírcsatorna olvasni a dokumentumokat törzsében burkolja be a szervezet tulajdonsággal. Ez a tulajdonság szerepel a eredő streamelt adatokat keret, azokat a rendszer tulajdonságértékek kivéve.
 |

### <a name="connection-settings"></a>Kapcsolati beállítások

Java SDK-t az alábbi kapcsolati beállításokat támogatja:

|Beállítás  |Leírás  |
|---------|---------|
|connectionmode   |  Beállítja a csatlakozási mód, amely a belső DocumentClient kell kommunikálni az Azure Cosmos DB használatával. Engedélyezett értékek a következők **DirectHttps** (alapértelmezett érték) és **átjáró**. A DirectHttps csatlakozási mód közvetlenül irányítja a kérelmeket a CosmosDB-partíciók, és némi késés előnyt biztosít.       |
|connectionmaxpoolsize   |  Belső DocumentClient által használt kapcsolat készletméret értékét állítja be. Az alapértelmezett érték 100.       |
|connectionidletimeout  |  Az időtúllépési érték beállítása (másodpercben), a tétlen kapcsolatok. Az alapértelmezett értéke 60.       |
|query_maxretryattemptsonthrottledrequests    |  Beállítja az újrapróbálkozások maximális számát. Kérelem hiba miatt a sebesség korlátozása az ügyfél esetén ezt az értéket használja. Ha nincs megadva, alapértelmezett értéke 1000 újrapróbálkozási kísérleteket.       |
|query_maxretrywaittimeinseconds   |  Beállítja a maximális újrapróbálkozási idő másodpercben. Alapértelmezés szerint 1000 másodperc.       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>Twitter-adatok írni az Azure Cosmos DB-hez 

Ebben a szakaszban a Spark lekérdezéseket futtat a Twitter-adatok kötegelt írni az új gyűjtemény ugyanabban az adatbázisban. A [azure-cosmos DB-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-tárház gazdagépek a [Write_Batch_Twitter_Data](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Write_Batch_Twitter_Data.ipynb) notebookot. A notebook importálása az Azure Databricks-fiókjába, és frissítse a fiók URI-t, a főkulcs, adatbázis és gyűjtemény neve. A jegyzetfüzet futtatásához, vagy hozzon létre a következő:

1. Nyissa meg az Azure Databricks-fiókját, és válassza ki **munkaterület** > **létrehozás** > **Notebook**.  

2. Az a **jegyzetfüzet létrehozása** párbeszédpanelen adjon meg egy felhasználóbarát nevet, és válassza a **Scala** nyelve. A legördülő listából válassza ki a korábban létrehozott fürt, és válassza ki **létrehozás**.  

3. Frissítse a végpont, főkulcs, adatbázis és gyűjtemény értékeket kapcsolhatja az adatbázis-gyűjtemény, Twitter-adatok írására és olvasására.

   ```scala
   %scala
   // Import Necessary Libraries
   import org.joda.time._
   import org.joda.time.format._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.config.Config

   // Maps
   val readConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000",
   "query_custom" -> "SELECT c.id, c.created_at, c.user.screen_name, c.user.location, c.text, c.retweet_count, c.entities.hashtags, c.entities.user_mentions, c.favorited, c.source FROM c"
   )
   val writeConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "preferredRegions" -> "East US",
   "SamplingRatio" -> "1.0",
   "schema_samplesize" -> "200000"
   ) 

   // Configs
   // get read
   val readConfig = Config(readConfigMap)
   val tweets = spark.read.cosmosDB(readConfig)
   tweets.createOrReplaceTempView("tweets")
   tweets.cache()

   // get write
   val writeConfig = Config(writeConfigMap)
   ```
4. Futtassa a lekérdezést, a Twitter-üzenetek száma a gyorsítótárazott adatokat a különböző hashtageket lekéréséhez. 

   ```scala
   %sql
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

5. Hozzon létre egy új adatkeretbe tweetek címkék, és mentse az új gyűjtemény az adatokat. Miután a következő kódot, lépjen vissza a Portalba, és győződjön meg arról, hogy az adatok írása a gyűjteményhez. 

   ```scala
   %scala
   // Import SaveMode so you can Overwrite, Append, ErrorIfExists, Ignore
   import org.apache.spark.sql.{Row, SaveMode, SparkSession}

   // Create new DataFrame of tweets tags
   val tweets_bytags = spark.sql("select '2018-06-12' as currdt, hashtags.text as hashtags, count(distinct id) as tweets from ( select explode(hashtags) as hashtags, id from tweets ) a group by hashtags.text order by tweets desc limit 10")

   // Save to Cosmos DB (using Append in this case)
   // Ensure the baseConfig contains a Read-Write Key
   // The key provided in our examples is a Read-Only Key

   tweets_bytags.write.mode(SaveMode.Overwrite).cosmosDB(writeConfig)
   ```

A Java SDK konfigurációs leképezés a következő értékeket támogatja:

|Beállítás  |Leírás  |
|---------|---------|
| BulkImport | Logikai érték, amely azt jelzi, hogy adatokat importálni kell a BulkExecutor könyvtár használatával. Alapértelmezés szerint az értéke igaz. |
|WritingBatchSize  |   Azt jelzi, hogy a Köteg mérete, akkor használja, ha adatokat ír az Azure Cosmos DB-gyűjtemények. <br/><br/> Ha BulkImport paraméter értéke true, majd WritingBatchSize paraméter azt jelzi, hogy a paraméter értéke importAll API a BulkExecutor könyvtár bemenetként megadott dokumentumok a Köteg mérete. Alapértelmezés szerint ez az érték 100 ezer értéke. <br/><br/> Ha BulkImport paraméter false értékre van állítva, WritingBatchSize paramétert kell írnia az Azure Cosmos DB-gyűjteményhez használni kívánt batch méretét jelzi. Az összekötő Documentclient és upsertDocument kérelmek aszinkron módon elküldi a Batch szolgáltatásban. Minél nagyobb a Köteg mérete, a további átviteli sebességet érhet el, amíg a fürt erőforrásainak érhetők el. Másrészről adja meg a sebesség és a fogyasztott korlátozása kisebb számú kötegméret. Alapértelmezés szerint a írása a Köteg mérete 500 van beállítva.  |
|Upsert   |  E helyett Documentclient upsertDocument használni, amikor az Azure Cosmos DB-gyűjtemények ír jelző logikai érték karakterlánc.   |
| WriteThroughputBudget |  A gyűjtemény számára egy karakterlánc, amely RU\s, amely a tömeges betöltési Spark-feladat, a teljes átviteli sebesség ki a hozzárendelni kívánt számát jelöli. |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Az Azure Cosmos DB az adatfolyam-, Twitter-adatok írása 

Ebben a szakaszban a Spark lekérdezéseket futtat egy módosítási hírcsatorna streamelési ugyanabban az adatbázisban egy új gyűjteményt a Twitter-adatok írására. A [azure-cosmos DB-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-tárház gazdagépek a [Write_Stream_Twitter_Data](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/Documentation_Samples/Write_Stream_Data.scala) notebookot. A notebook importálása az Azure Databricks-fiókjába, és frissítse a fiók URI-t, a főkulcs, adatbázis és gyűjtemény neve. A jegyzetfüzet futtatásához, vagy hozzon létre a következő:

1. Nyissa meg az Azure Databricks-fiókját, és válassza ki **munkaterület** > **létrehozás** > **Notebook**.  

2. Az a **jegyzetfüzet létrehozása** párbeszédpanelen adjon meg egy felhasználóbarát nevet, és válassza a **Scala** nyelve. A legördülő listából válassza ki a korábban létrehozott fürt, és válassza ki **létrehozás**.  

3. Frissítse a végpont, főkulcs, adatbázis és gyűjtemény értékeket kapcsolhatja az adatbázis-gyűjtemény, Twitter-adatok írására és olvasására.

   ```scala
   import com.microsoft.azure.cosmosdb.spark._
   import com.microsoft.azure.cosmosdb.spark.schema._
   import com.microsoft.azure.cosmosdb.spark.config.Config
   import com.microsoft.azure.cosmosdb.spark.streaming._

   // Configure connection to Azure Cosmos DB Change Feed (Trades)
   val ConfigMap = Map(
   // Account settings
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB source collection name>", 
   // Change feed settings
   "ReadChangeFeed" -> "true",
   "ChangeFeedStartFromTheBeginning" -> "true",
   "ChangeFeedCheckpointLocation" -> "dbfs:/cosmos-feed",
   "ChangeFeedQueryName" -> "Structured Stream Read",
   "InferStreamSchema" -> "true"
   )
   ```
4. Indítsa el a spark.readStream.format() paranccsal adatfolyamként olvasása a módosítási hírcsatorna:
 
   ```scala
   // Start reading change feed of trades as a stream
   var streamdata = spark
     .readStream
     .format(classOf[CosmosDBSourceProvider].getName)
     .options(ConfigMap)
     .load()
   ```

5. A célgyűjtemény konfigurációval, és indítsa el a folyamatos átviteli feladat writeStream.format() módszer használatával:

   ```scala
   val sinkConfigMap = Map(
   "Endpoint" -> "<Your Azure Cosmos DB endpoint>",
   "Masterkey" -> "<Primary key of your Azure Cosmos DB account>",
   "Database" -> "<Your Azure Cosmos DB database name>",
   "Collection" -> "<Your Azure Cosmos DB destination collection name>", 
   "checkpointLocation" -> "streamingcheckpointlocation6",
   "WritingBatchSize" -> "100",
   "Upsert" -> "true")

   // Start the stream writer
   val streamingQueryWriter = streamdata
    .writeStream
    .format(classOf[CosmosDBSinkProvider].getName)
    .outputMode("append")
    .options(sinkConfigMap)
    .start()
    ```

A Java SDK konfigurációs leképezés a következő értékeket támogatja:

|Beállítás  |Leírás  |
|---------|---------|
|Upsert   |  E helyett Documentclient upsertDocument használni, amikor az Azure Cosmos DB-gyűjtemények ír jelző logikai érték karakterlánc.   |
|checkpointlocation  |   Egy elérési útja helyi file storage segítségével őrzi a folytatási token csomóponthibák esetén.   |
|WritingBatchSize  |  Azt jelzi, hogy a Köteg mérete írja az adatokat az Azure Cosmos DB-gyűjtemények használni. Az összekötő Documentclient és upsertDocument kérelmek aszinkron módon elküldi a Batch szolgáltatásban. Minél nagyobb a Köteg mérete, a további átviteli sebességet érhet el, amíg a fürt erőforrásainak érhetők el. Másrészről adja meg a sebesség és a fogyasztott korlátozása kisebb számú kötegméret. Alapértelmezés szerint a írása a Köteg mérete 500 van beállítva.  |


### <a name="considerations-when-using-java-sdk"></a>A Java SDK használatának szempontjai

Csatlakozás a Spark az Azure Cosmos DB Java SDK-val a következő esetekben ajánlott:

* Python- vagy a Scala használni kívánt.  

* Van egy nagy mennyiségű adat az Apache Spark és az Azure Cosmos DB közötti átviteléhez. A Java SDK jobban, mint a pyDocumentDB hajt végre. Lekérdezési teljesítmény különbségekkel kapcsolatos további információkért lásd: a [lekérdezéstesztelésből futtatja a wikin](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="next-steps"></a>További lépések

Ha még nem tette, töltse le a Spark az Azure Cosmos DB-összekötő a [azure-cosmos DB-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-adattárban. Ismerje meg a következő további erőforrások az adattárban:

* [Összesítések példák](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Mintaszkriptek és -jegyzetfüzetek](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Emellett érdemes áttekinteni a [adatkészletek útmutató, Apache Spark SQL és DataFrames](https://spark.apache.org/docs/latest/sql-programming-guide.html), és a [Apache Spark on Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) cikk.
