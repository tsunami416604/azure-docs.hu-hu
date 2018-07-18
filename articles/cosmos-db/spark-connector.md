---
title: Csatlakozás az Apache Spark az Azure Cosmos DB |} A Microsoft Docs
description: Ez az oktatóanyag segítségével további tudnivalók az Azure Cosmos DB Spark-összekötő, amely lehetővé teszi, hogy csatlakozzon az Apache Spark az Azure Cosmos DB globálisan elosztott összesítésekkel és adatokkal sciences végre a több-bérlős elosztott adatbázisrendszerként a Microsoft meg a felhő számára készült.
keywords: az Apache spark
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: ramkris
ms.openlocfilehash: cae66a40882231f7762af29cdeaaf658dd2aa968
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113947"
---
# <a name="accelerate-real-time-big-data-analytics-by-using-the-spark-to-azure-cosmos-db-connector"></a>Gyorsítsa fel a valós idejű big-data elemzések a Spark az Azure Cosmos DB-összekötő használatával
 
A Spark az Azure Cosmos DB-összekötő lehetővé teszi, hogy az Azure Cosmos DB bemeneti-kiszolgálóként vagy az Apache Spark-feladatok kimeneti. Csatlakozás [Spark](http://spark.apache.org/) való [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) gyorsítja megítélnie gyors ütemben data science kapcsolatos problémák megoldásához használható Azure Cosmos DB gyorsan továbbra is fennáll, illetve adatokat kérdezhet le. A Spark az Azure Cosmos DB-összekötő hatékonyan az Azure Cosmos DB által kezelt natív indexeket használja. Az indexek engedélyezi a frissíthető oszlopok analytics hajt végre, és az adatokat, amelyek az eszközök internetes hálózata (IoT) leküldéses legördülő predikátum szűrése gyorsan változó globálisan elosztott adatok adatelemzési és analitikai feladatokhoz.

További információ a Spark az Azure Cosmos DB-összekötő ezt a videót:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T135/player] 

## <a name="connector-components"></a>Összekötő-összetevők

A Spark az Azure Cosmos DB-összekötő a következő összetevőket használja:

* [Az Azure Cosmos DB](http://documentdb.com) lehetővé teszi ügyfeleink számára, és rugalmasan méretezhető átviteli sebesség és a storage egyaránt tetszőleges számú földrajzi régió között.  

* [Az Apache Spark](http://spark.apache.org/) egy hatékony nyílt forráskódú motor, amely sebessége, a könnyű használat és a kifinomult elemzési épül.  

* [Apache Spark-fürt az Azure databricks szolgáltatásban](https://docs.azuredatabricks.net/getting-started/index.html) spark-feladatok futtatásához a spark-fürtön.

## <a name="connect-apache-spark-to-azure-cosmos-db"></a>Csatlakozás az Apache Spark az Azure Cosmos DB

Csatlakozás az Apache Spark és az Azure Cosmos DB kétféleképpen történhet:

1. Használatával [Azure Cosmos DB SQL Python SDK](https://github.com/Azure/azure-documentdb-python), egy Python-alapú Apache spark a Cosmos DB-összekötő "pyDocumentDB" is nevezik.  

2. Használatával a [Azure Cosmos DB SQL Java SDK](https://github.com/Azure/azure-documentdb-java) egy Java-alapú Apache spark a Cosmos DB-összekötő.


**Támogatott verziók**

| Összetevő | Verzió |
|---------|-------|
|Apache Spark| 2.1.x, 2.2.x, 2.3.x |
| Scala|2.11|
| Databricks futtatókörnyezet-verziója | > 3.4 |
| Azure Cosmos DB SQL Java SDK | 1.16.2 |

## <a name="connect-by-using-python-or-pydocumentdb-sdk"></a>Csatlakozás Python vagy a pyDocumentDB SDK használatával

Az alábbi képen a pyDocumentDB SDK megvalósítása architektúráját mutatja be:

![A Spark az Azure Cosmos DB az adatfolyam pyDocumentDB DB-n keresztül](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow"></a>Az adatfolyam

Az adatfolyam pyDocumentDB megvalósítása a következőképpen történik:

* A fő csomóponttal, a spark az Azure Cosmos DB átjárócsomópont pyDocumentDB keresztül csatlakozik. A felhasználó megadja a spark és a csak az Azure Cosmos DB-kapcsolatok. A megfelelő és az átjáró fő csomópontok való csatlakozás történik a felhasználó számára.  

* Az átjárócsomópont lehetővé teszi az Azure Cosmos DB, ahol a lekérdezést később fut a gyűjtemény partícióihoz az adatcsomópontok a lekérdezést. A válasz ezeket a lekérdezéseket küld vissza az átjárócsomópontnak, és adott eredményhalmaz adja vissza a spark fő csomóponttal.  

* További (például egy spark-adatkeretbe) ellen küldi el a Spark munkavégző csomópontokhoz feldolgozásra.  

Spark és az Azure Cosmos DB közötti kommunikáció a spark-főcsomóponttal és az Azure Cosmos DB az átjárócsomópontok korlátozódik. A szállítási réteg a két csomópont között lehetővé teszi, hogy minél gyorsabban nyissa meg a lekérdezéseket.

Futtassa az alábbi lépéseket a csatlakozás a spark az Azure Cosmos DB pyDocumentDB SDK használatával:

1. Hozzon létre egy [Azure Databricks-munkaterület](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) és a egy [spark-fürt](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks) (Databricks futtatókörnyezet 4.0-s verzióját (tartalmazza az Apache Spark 2.3.0-át és Scala 2.11-et) azon belül.  

2. Miután a fürt létrejött, és fut-e, lépjen **munkaterület** > **létrehozás** > **könyvtár**.  
3. Az új szalagtár párbeszédpanelen válasszon **Python tojás feltöltése vagy PyPi** forrásaként, adja meg **pydocumentdb** nevet, válassza ki **telepítése kódtár**. PyDocumentdb SDK már közzé van téve a pip-csomagoknak, így megtalálhatják azt, és telepítse. 

   ![Hozzon létre és társítsa a könyvtár](./media/spark-connector/create-library.png)

4. A kódtár telepítését követően csatlakoztat a fürthöz, a korábban létrehozott.  

5. Ezután keresse meg a **munkaterület** > **létrehozás** > **Notebook**.  

6. Az a **jegyzetfüzet létrehozása** párbeszédpanel mezőben adjon meg egy felhasználóbarát nevet, válassza a **Python** nyelve. A legördülő listából válassza ki a korábban létrehozott fürt, és válassza ki **létrehozás**.  

7. A kommunikációs szállítás megírható a spark az Azure Cosmos DB-lekérdezések végrehajtásához a pyDocumentDB viszonylag egyszerű. Ezután fogja futtatni néhány spark lekérdezések a járatok mintaadatok használatával üzemeltetett "doctorwho" a Cosmos DB-fiókot, amely nyilvánosan elérhető-e. A notebook HTML-verziójában található a [azure-cosmos DB-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-adattárban. Le kell töltenie a tárház fájljainak és navigáljon a `\samples\Documentation_Samples\Read_Batch_PyDocumentDB.html` az Azure Databricks-fiókhoz a notebookot importálhatja, és futtassa. A következő szakasz a kódblokkok, a részletes funkcióit mutatja be.

A következő kódrészletet bemutatja, hogyan importálhatja a pyDocumentDB SDK-t, és a lekérdezések futtatásához a spark környezet. A kódtöredék feljegyzett a pyDocumentDB SDK tartalmazza az Azure Cosmos DB-fiókhoz való csatlakozáshoz szükséges kapcsolati paraméterek. Importálja a szükséges kódtárak, a főkulcs és a gazdagépcsoport létrehozása az Azure Cosmos DB-ügyfél (pydocumentdb.document_client) konfigurálja.


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

Ezután lekérdezéseket is futtathat, és a következő kódrészletet a airports.codes gyűjtemény a DoctorWho fiókban csatlakozik, és bontsa ki a Washington állambeli repülőtér városok lekérdezés futtatja. 

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

Után hajtja végre, a lekérdezés eredménye "query_iterable. QueryIterable", amely egy Python-listát, amely egy spark-adatkeretbe átalakítja alakítja át. 

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

## <a name="considerations-when-using-pydocumentdb-sdk"></a>A pyDocumentDB SDK használatának szempontjai
A spark csatlakoztathatók az Azure Cosmos DB SDK-t a következő esetekben ajánlott pyDocumentDB használatával:

* Python használni kívánt.  

* A spark az Azure Cosmos DB viszonylag kis eredményhalmazt vannak visszaadása. Vegye figyelembe, hogy az Azure Cosmos DB az alapul szolgáló adatkészlet Igen tekintélyes lehet és szűrők alkalmazása vagy predikátum szűrők futtat az Azure Cosmos DB-forrás.

## <a name="connect-by-using-the-java-sdk"></a>Csatlakozás a Java SDK használatával

Az alábbi képen az Azure Cosmos DB SQL Java SDK megvalósítása architektúráját mutatja be, és adatokat helyez át, a spark munkavégző csomópontok között:

![Az adatfolyam a Spark az Azure Cosmos DB-összekötő](./media/spark-connector/spark-connector.png)

### <a name="data-flow"></a>Az adatfolyam

A Java SDK-t végrehajtása az adatfolyam a következőképpen történik:

* A spark-főkiszolgáló csomópont csatlakozik a partíciótérképen beszerzése az Azure Cosmos DB átjárócsomópontnak. A felhasználó ad meg, csak a spark és az Azure Cosmos DB-kapcsolatokat. A megfelelő és az átjáró fő csomópontok való csatlakozás történik a felhasználó számára.  

* Ezt az információt spark fő csomópont van megadva. Ezen a ponton meg kell tudni a lekérdezés határozza meg a partíciók és az Azure Cosmos DB, hozzá kell férnie a helyüket.  

* Ezek az információk átkerülnek a spark munkavégző csomópontokhoz.  

* A spark-feldolgozó csomópontok csatlakozhat közvetlenül az adatok kinyerése, és térjen vissza az adatokat a spark-partíciók, a munkavégző csomópontok az Azure Cosmos DB partíciókat.  

Spark és az Azure Cosmos DB közötti kommunikáció az jelentősen gyorsabb miatt az adatok áthelyezése az Azure Cosmos DB-adatcsomópontok (partíciók) és a spark munkavégző csomópontok között van. Ebben a dokumentumban néhány twitter mintaadatok elküldése az Azure Cosmos DB-fiókot, és a spark-lekérdezések futtatása az adatok használatával. A következő lépések segítségével minta Twitter adatokat írni az Azure Cosmos DB:

1. Hozzon létre egy [Azure Cosmos DB SQL API-fiók](create-sql-api-dotnet.md#create-a-database-account) és [gyűjtemény hozzáadása](create-sql-api-dotnet.md#add-a-collection) ahhoz a fiókhoz.  

2. Töltse le a [TwitterCosmosDBFeed](https://github.com/tknandu/TwitterCosmosDBFeed) GitHub-minta Twitter-adatok írni az Azure Cosmos DB-hez használt található mintát.  

3. Nyisson meg egy parancssort, és Tweepy és a pyDocumentdb modulok telepítéséhez a következő parancsok futtatásával:

   ```bash
   pip install tweepy==3.3.0
   pip install pyDocumentDB
   ```

4. A Twitter-hírcsatorna minta a tartalma kinyeréséhez, és nyissa meg a config.py fájlt. Frissítse a masterKey, a gazdagépet, a databaseId, a collectionId és a preferredlocations listában.  

5. Navigáljon a `http://apps.twitter.com/` , és regisztrálja a Twitter-hírcsatorna új alkalmazás-parancsprogramot. Miután kiválasztotta, egy nevet és egy alkalmazás az alkalmazás számára, Ön biztosítjuk az egy **fogyasztói kulcs, a fogyasztói titkos kulcs, a hozzáférési jogkivonatot és a hozzáférési jogkivonat titkos kulcs**. Másolja ki ezeket az értékeket, és frissítse azokat a config.py fájlt az alkalmazás programozás alapú hozzáférést biztosít a Twitteren.   

6. Mentse a config.py fájlt. Nyissa meg a parancssort, és futtassa a python-alkalmazás a következő paranccsal:

   ```bash
   Python driver.py
   ```

7. Keresse meg az Azure Cosmos DB-gyűjtemények a portálon, és győződjön meg arról, hogy a gyűjteményt a twitter-adatok írt.

### <a name="find-and-attach-java-sdk-to-the-spark-cluster"></a>Keresse meg és a Java SDK-t csatolja a spark-fürt

1. Hozzon létre egy [Azure Databricks-munkaterület](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-an-azure-databricks-workspace) és a egy [spark-fürt](../azure-databricks/quickstart-create-databricks-workspace-portal.md#create-a-spark-cluster-in-databricks) (Databricks futtatókörnyezet 4.0-s verzióját (tartalmazza az Apache Spark 2.3.0-át és Scala 2.11-et) azon belül.  

2. Miután a fürt létrejött, és fut-e, lépjen **munkaterület** > **létrehozás** > **könyvtár**.  

3. Új kódtár párbeszédpanelen válasszon **Maven-koordináta** forrásaként, adja meg a koordináta értékét **com.microsoft.azure:azure-cosmosdb-spark_2.3.0_2.11:1.2.0**, és válassza ki  **Könyvtár létrehozása**. A Maven-függőségek feloldása, és a csomagot kell hozzáadni a munkaterülethez. A fenti maven koordináta formátumban 2.3.0-át jelöli a spark-verziót, 2.11-et a scala-verziót, és 1.2.0-s vagy annál újabb jelenti az Azure Cosmos DB-összekötő verziójának. 

4. A kódtár telepítését követően csatlakoztat a fürthöz, a korábban létrehozott. 

Ez a cikk bemutatja, hogy a spark-összekötő Java SDK-t a következő esetekben:

* Twitter-adatok olvasása az Azure Cosmos DB-ből  

* Az Azure Cosmos DB az adatfolyam-, twitter-adatok olvasása  

* Twitter-adatok írni az Azure Cosmos DB-hez 

### <a name="read-twitter-data-from-azure-cosmos-db"></a>Twitter-adatok olvasása az Azure Cosmos DB-ből
 
Ebben a szakaszban a Twitter-adatok kötegelt olvasása az Azure Cosmos DB-lekérdezések spark futtatása. A notebook HTML-verziójában található a [azure-cosmos DB-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-adattárban. Le kell töltenie a tárház fájljainak és navigáljon a `\samples\Documentation_Samples\Read_Batch_Twitter_Data.html` az Azure Databricks-fiókhoz a notebookot importálása, frissítheti a fiók URI-t, a főkulcs, adatbázis, gyűjtemény nevének és futtathatja, vagy a jegyzetfüzet módon hozhat létre:

1. Lépjen az Azure Databricks-fiókra, és válassza ki a **munkaterület** > **létrehozás** > **Notebook**. 

2. Az a **jegyzetfüzet létrehozása** párbeszédpanel mezőben adjon meg egy felhasználóbarát nevet, válassza a **Python** a nyelvet, a legördülő listából válassza ki a korábban létrehozott fürt, és válassza ki **létrehozása**.  

3. Frissítse a végpont, a master key, az adatbázis és gyűjtemény értékek csatlakozzon a fiókjához, és olvassa el a tweetek spark.read.format() parancs használatával.

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
|query_enablescan    |   Engedélyezheti a lekérdezéseket, amelyek nem nyitható meg, mert az indexelés vizsgálatok volt csoportok kilépteti a programból a kért útvonalakon.       |
|query_disableruperminuteusage  |  Letiltja a kérelemegység (RU-k) a lekérdezés kiszolgálására, ha a rendszer normál kérelemegység/s kiosztott kapacitás percenként.       |
|query_emitverbosetraces   |   Beállítja a lehetőséget, hogy a lekérdezések kibocsátható vizsgálat részletes nyomkövetési ki.      |
|query_pagesize  |   A lekérdezés eredménye lap egyes lekérdezési kérés méretének beállítása. A teljesítmény optimalizálása érdekében a nagy lapméret használatával beolvassa a lekérdezések eredményei való adatváltások száma csökkenthető.      |
|query_custom  |  Azure Cosmos DB lekérdezése felülírják az alapértelmezett lekérdezéssel, ha az adatok lekérése az Azure Cosmos DB-hez állítja be. Vegye figyelembe, hogy akkor ezt az értéket, a rendszer azt használja a lekérdezés helyett leküldte a predikátumok is.     |

A forgatókönyvtől függően különféle konfigurációs értékeket kell használni a teljesítmény és a teljesítmény optimalizálása érdekében. Vegye figyelembe, hogy a konfigurációs kulcs jelenleg nem betűérzékeny, és a konfigurációs érték mindig egy karakterláncot.

### <a name="read-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Az Azure Cosmos DB az adatfolyam-, twitter-adatok olvasása

Ebben a szakaszban egy módosítási hírcsatorna streamelési twitter-adatok olvasása-lekérdezések spark fogja futtatni. Bár ebben a szakaszban futtassa a lekérdezéseket, ellenőrizze, hogy a Twitter-hírcsatornáján alkalmazás fut, és adatokat az Azure Cosmos DB szivattyúzó. A notebook HTML-verziójában található a [azure-cosmos DB-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-adattárban. Le kell töltenie a tárház fájljainak és navigáljon a `\samples\Documentation_Samples\Read_Stream_Twitter_Data.html` az Azure Databricks-fiókhoz a notebookot importálása, frissítheti a fiók URI-t, a főkulcs, adatbázis, gyűjtemény nevének és futtathatja, vagy a jegyzetfüzet módon hozhat létre:

1. Lépjen az Azure Databricks-fiókra, és válassza ki a **munkaterület** > **létrehozás** > **Notebook**.  

2. Az a **jegyzetfüzet létrehozása** párbeszédpanel mezőben adjon meg egy felhasználóbarát nevet, válassza a **Scala** a nyelvet, a legördülő listából válassza ki a korábban létrehozott fürt, és válassza ki **létrehozása**.  

3. Frissítse a végpont, a master key, az adatbázis és gyűjtemény értékek csatlakoztathatja a fiókhoz.

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
4. Indítsa el az olvasó változáscsatorna adatfolyamként a spark.readStream.format() parancs használatával:

   ```scala
   var streamData = spark.readStream.format(classOf[CosmosDBSourceProvider].getName).options(sourceConfigMap).load()
   ```
5. Lekérdezés-konzolra streamelésének megkezdéséhez:

   ```scala
   //**RUN THE ABOVE FIRST AND KEEP BELOW IN SEPARATE CELL
   val query = streamData.withColumn("countcol", streamData.col("id").substr(0,0)).groupBy("countcol").count().writeStream.outputMode("complete").format("console").start()
   ```

A Java SDK konfigurációs leképezés a következő értékeket támogatja:

|Beállítás  |Leírás  |
|---------|---------|
|readchangefeed   |  Azt jelzi, hogy a gyűjtemény tartalmát az, cosmos DB-módosítási hírcsatorna letöltődött. Az alapértelmezett értéke FALSE (hamis).       |
|changefeedqueryname |   Egyéni karakterláncot a lekérdezés azonosításához. Az összekötő nyomon követi, hogy a gyűjtemény folytatási jogkivonatainak különböző változáscsatorna lekérdezések külön-külön. Ha readchangefeedis igaz, akkor ez egy szükséges konfiguráció, amely nem üres érték.      |
|changefeedcheckpointlocation  |   Egy elérési útja helyi file storage segítségével őrzi a folytatási token csomóponthibák esetén.      |
|changefeedstartfromthebeginning  |  Állítja be, hogy el kell indulnia a módosítási hírcsatorna, az elejétől (igaz) vagy az aktuális pontról (hamis). Alapértelmezés szerint indul az aktuális (hamis).       |
|rollingchangefeed  |   A legutóbbi lekérdezés egy logikai érték, amely azt jelzi, hogy a módosítási hírcsatorna kell lennie. Az alapértelmezett értéke FALSE (hamis), ami azt jelenti, hogy a módosítások a gyűjtemény első olvasási kell számolni.      |
|changefeedusenexttoken  |   Logikai érték feldolgozása sikertelen helyzetek feltételeinek megteremtésére. Azt jelzi, hogy az aktuális változáscsatorna batch kezelt szabályosan és az RDD kell használnia a következő folytatási token beszerzése a változások későbbi köteg szolgál.      |
| InferStreamSchema | Logikai érték, amely jelzi-e a séma a streamelési adatok mintát venni streamelési elején. Alapértelmezés szerint az értéke igaz. Ez a paraméter értéke igaz, és a streamelési adatok sémaváltozások mintavételezés után, ha újonnan hozzáadott tulajdonságait a rendszer eldobja a streamelési adatok keretbe. <br/><br/> Ha azt szeretné, hogy a streamelési adatok keret sémafüggetlen kell, ez a paraméter "false" értékűre. Ebben a módban az Azure Cosmos DB-módosítási hírcsatorna olvasni dokumentumokat törzsében burkolja be azokat a rendszer tulajdonságértékek kivéve eredő streamelt adatokat keret "törzs" tulajdonság.
 |

### <a name="connection-settings"></a>Kapcsolati beállítások

Java SDK-t az alábbi kapcsolati beállításokat támogatja:

|Beállítás  |Leírás  |
|---------|---------|
|connectionmode   |  Beállítja a csatlakozási mód, amely a belső DocumentClient kell kommunikálni az Azure Cosmos DB használatával. Engedélyezett értékek a következők **DirectHttps** (alapértelmezett érték) és **átjáró**. A DirectHttps csatlakozási mód közvetlenül irányítja a kérelmeket a CosmosDB-partíciók, és némi késés előnyt biztosít.       |
|connectionmaxpoolsize   |  Belső DocumentClient által használt kapcsolat készletméret értékét állítja be. Az alapértelmezett érték 100.       |
|connectionidletimeout  |  Beállítja a tétlen kapcsolatok időkorlátja (másodpercben). Az alapértelmezett értéke 60.       |
|query_maxretryattemptsonthrottledrequests    |  Beállítja az újrapróbálkozások maximális számát. Ez az érték miatt a sebesség korlátozása az ügyfél kérést hiba esetén használható. Ha nincs megadva, alapértelmezett értéke 1000 újrapróbálkozási kísérleteket.       |
|query_maxretrywaittimeinseconds   |  Beállítja a maximális újrapróbálkozási idő másodpercben. Alapértelmezés szerint 1000 másodperc.       |

### <a name="write-twitter-data-to-azure-cosmos-db"></a>Twitter-adatok írni az Azure Cosmos DB-hez 

Ebben a szakaszban twitter-adatok kötegelt írni az új gyűjtemény ugyanazt az adatbázist a spark-lekérdezések futtatásához. A notebook HTML-verziójában található a [azure-cosmos DB-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-adattárban. Le kell töltenie a tárház fájljainak és navigáljon a `\samples\Documentation_Samples\Write_Batch_Twitter_Data.html` az Azure Databricks-fiókhoz a notebookot importálása, frissítheti a fiók URI-t, a főkulcs, adatbázis, gyűjtemény nevének és futtathatja, vagy a jegyzetfüzet módon hozhat létre:

1. Lépjen az Azure Databricks-fiókra, és válassza ki a **munkaterület** > **létrehozás** > **Notebook**.  

2. Az a **jegyzetfüzet létrehozása** párbeszédpanel mezőben adjon meg egy felhasználóbarát nevet, válassza a **Scala** a nyelvet, a legördülő listából válassza ki a korábban létrehozott fürt, és válassza ki **létrehozása**.  

3. Frissítse a végpont, főkulcs, adatbázis és gyűjtemény értékeket kapcsolhatja az adatbázis-gyűjtemény, twitter-adatok írására és olvasására.

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

5. Hozzon létre új adatkeretbe tweetek címkék, és mentse az új gyűjtemény az adatokat. Miután a következő kódot, lépjen vissza a Portalba, és győződjön meg arról, hogy az adatok írása a gyűjteményhez. 

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
|WritingBatchSize  |   Azt jelzi, hogy a Köteg mérete, akkor használja, ha az Azure Cosmos DB-gyűjtemények írja az adatokat. <br/><br/> Ha BulkImport paraméter értéke true, majd WritingBatchSize paraméter azt jelzi, hogy a paraméter értéke importAll API a BulkExecutor könyvtár bemenetként megadott dokumentumok a Köteg mérete. Alapértelmezés szerint ez az érték 100 ezer értéke. <br/><br/> Ha BulkImport paraméter false értékre van állítva, WritingBatchSize paraméter használata az Azure Cosmos DB-gyűjtemények való írás esetén batch méretét jelzi. Az összekötő Documentclient/upsertDocument kérelmek aszinkron módon elküldi a Batch szolgáltatásban. Minél nagyobb a köteg méretét a kapacitás növelése érdekében azt érheti el, amíg a fürt erőforrásainak érhetők el. Másrészről adja meg a sebesség és a fogyasztott korlátozása kisebb számú kötegméret. Alapértelmezés szerint a írása a Köteg mérete 500 van beállítva.  |
|Upsert   |  E helyett Documentclient upsertDocument kell használni, amikor az CosmosDB collection írása jelző logikai érték karakterlánc.   |
| WriteThroughputBudget |  A gyűjtemény számára egy karakterlánc, amely RU\s a tömeges betöltési spark-feladat kívül a teljes átviteli sebesség lefoglalni kívánt számát jelöli. |


### <a name="write-twitter-data-that-is-streaming-to-azure-cosmos-db"></a>Az Azure Cosmos DB az adatfolyam-, twitter-adatok írása 

Ebben a szakaszban futtatása spark-lekérdezések írása a módosítási hírcsatorna streamelési ugyanabban az adatbázisban egy új gyűjteményt a twitter-adatok. A notebook HTML-verziójában található a [azure-cosmos DB-spark](https://github.com/Azure/azure-cosmosdb-spark/tree/master) GitHub-adattárban. Le kell töltenie a tárház fájljainak és navigáljon a `\samples\Documentation_Samples\Write_Stream_Twitter_Data.html` az Azure Databricks-fiókhoz a notebookot importálása, frissítheti a fiók URI-t, a főkulcs, adatbázis, gyűjtemény nevének és futtathatja, vagy a jegyzetfüzet módon hozhat létre:

1. Lépjen az Azure Databricks-fiókra, és válassza ki a **munkaterület** > **létrehozás** > **Notebook**.  

2. Az a **jegyzetfüzet létrehozása** párbeszédpanel mezőben adjon meg egy felhasználóbarát nevet, válassza a **Scala** a nyelvet, a legördülő listából válassza ki a korábban létrehozott fürt, és válassza ki **létrehozása**.  

3. Frissítse a végpont, főkulcs, adatbázis és gyűjtemény értékeket kapcsolhatja az adatbázis-gyűjtemény, twitter-adatok írására és olvasására.

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
4. Indítsa el az olvasó változáscsatorna adatfolyamként a spark.readStream.format() parancs használatával:
 
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
|Upsert   |  E helyett Documentclient upsertDocument kell használni, amikor az CosmosDB collection írása jelző logikai érték karakterlánc.   |
|checkpointlocation  |   Egy elérési útja helyi file storage segítségével őrzi a folytatási token csomóponthibák esetén.   |
|WritingBatchSize  |  Azt jelzi, hogy a Köteg mérete, akkor használja, ha az Azure Cosmos DB-gyűjtemények írja az adatokat. Az összekötő Documentclient/upsertDocument kérelmek aszinkron módon elküldi a Batch szolgáltatásban. Minél nagyobb a köteg méretét a kapacitás növelése érdekében azt érheti el, amíg a fürt erőforrásainak érhetők el. Másrészről adja meg a sebesség és a fogyasztott korlátozása kisebb számú kötegméret. Alapértelmezés szerint a írása a Köteg mérete 500 van beállítva.  |


## <a name="considerations-when-using-java-sdk"></a>A Java SDK használatának szempontjai

Összekötő spark az Azure Cosmos DB Java SDK-val a következő esetekben javasoljuk:

* Python-és/vagy a Scala használni kívánt.  

* Nagy mennyiségű adatok között az Apache Spark és az Azure Cosmos DB van, a Java SDK-val rendelkezik a pyDocumentDB képest nagyobb teljesítményt. Támpontot a lekérdezési teljesítmény különbség, tekintse meg a [lekérdezés tesztelések wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="next-steps"></a>További lépések

Ha még nem tette, töltse le a Spark az Azure Cosmos DB-összekötő a [azure-cosmos DB-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-adattár és a tárházban további források:

* [Elosztott összesítések példák](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Mintaszkriptek és -jegyzetfüzetek](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Emellett érdemes áttekinteni a [adatkészletek útmutató, Apache Spark SQL és DataFrames](http://spark.apache.org/docs/latest/sql-programming-guide.html) és a [Apache Spark on Azure HDInsight](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) cikk.
