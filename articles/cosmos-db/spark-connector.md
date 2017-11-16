---
title: "Csatlakozás az Apache Spark on Azure Cosmos DB |} Microsoft Docs"
description: "Ez az oktatóanyag segítségével további információkhoz az Azure Cosmos DB Spark-összekötő, amely lehetővé teszi a kapcsolódást az Apache Spark on Azure Cosmos DB elosztott összesítésekkel és adatokkal sciences végre a több-bérlős globálisan elosztott készült, a felhő Microsoft adatbázisrendszer."
keywords: az Apache Spark on
services: cosmos-db
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: 
ms.assetid: c4f46007-2606-4273-ab16-29d0e15c0736
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: denlee
ms.openlocfilehash: ba824ed1bad49c71f8de9f2da8249945d9430222
ms.sourcegitcommit: 3ee36b8a4115fce8b79dd912486adb7610866a7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="accelerate-real-time-big-data-analytics-with-the-spark-to-azure-cosmos-db-connector"></a>A Spark a valós idejű big data elemzések érdekében, és Azure Cosmos DB-összekötő

A Spark on Azure Cosmos DB összekötőre lehetővé teszi, hogy az Azure Cosmos DB egy bemeneti forrás vagy a kimeneti fogadóját Apache Spark-feladatok nevében járhasson el. Csatlakozás [Spark](http://spark.apache.org/) való [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) felgyorsítják arra, hogy a gyorsan változó adatok tudományos problémák megoldásához használható Azure Cosmos DB gyorsan és adatait. A Spark on Azure Cosmos DB összekötőre hatékonyan használja a natív Azure Cosmos DB felügyelt indexeket. Az indexek frissíthető oszlopok lehetővé teszik, amikor elemzés végrehajtása, és leküldéses-le nyilas predikátum szűrése fast-módosítása globális adatokat, amelyek az eszközök internetes hálózatát (IoT) között adatok tudományos és elemzés forgatókönyvekre.

Spark GraphX és a Gremlin graph API-kat az Azure Cosmos DB használata, lásd: [Spark és Apache TinkerPop Gremlin graph időben](spark-connector-graph.md).

## <a name="download"></a>Letöltés

A kezdéshez töltse le a Spark on Azure Cosmos DB-összekötőt a [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark/) GitHub tárházából.

## <a name="connector-components"></a>Összekötő-összetevők

Az összekötő a következő összetevőket használja:

* [Az Azure Cosmos DB](http://documentdb.com) lehetővé teszi az ügyfelek kiépítéséhez és rugalmasan méretezhető átviteli sebesség és a tárolási tetszőleges számú földrajzi régiók között. A szolgáltatás nyújt:
   * Kapcsolja be a kulcs [globális terjesztési](distribute-data-globally.md) és horizontális skálázhatóságot
   * Egyetlen számjegy késések fordulnak elő az 99th aránya a garantált
   * [Több jól meghatározott konzisztencia modellek](consistency-levels.md)
   * Magas rendelkezésre állású többhelyű képességekkel garantált
   * Minden szolgáltatás üzemelnek iparágvezető, átfogó [szolgáltatási szintek](https://azure.microsoft.com/support/legal/sla/cosmos-db) (SLA).

* [Az Apache Spark on](http://spark.apache.org/) egy hatékony nyílt forráskódú feldolgozási motor, amely a sebesség, valamint a kifinomult analytics könnyű épül.

* [Apache Spark on Azure Hdinsighttal](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) , hogy a segítségével telepíthet a felhőbe a kritikus fontosságú központi telepítések elvégzéséhez az Apache Spark on [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/).

Hivatalos támogatott verziók:

| Összetevő | Verzió |
|---------|-------|
|Apache Spark|2.0+|
| Scala| 2.11|
| Az Azure DocumentDB Java SDK | 1.10.0 |

Ez a cikk segít néhány egyszerű példák futtatása Python (a pyDocumentDB) keresztül és a Scala-felületek használatával.

Apache Spark és Azure Cosmos DB kétféleképpen történhet:
- PyDocumentDB keresztül használja a [Azure DocumentDB Python SDK](https://github.com/Azure/azure-documentdb-python).
- Hozzon létre egy Java-alapú Spark on Azure Cosmos DB-összekötőhöz való használatával a [Azure DocumentDB Java SDK](https://github.com/Azure/azure-documentdb-java).

## <a name="pydocumentdb-implementation"></a>pyDocumentDB végrehajtása
Az aktuális [pyDocumentDB SDK](https://github.com/Azure/azure-documentdb-python) lehetővé teszi a Spark on Azure Cosmos DB csatlakozni, az alábbi ábrán látható módon:

![Spark Azure Cosmos DB adatfolyamra pyDocumentDB DB keresztül](./media/spark-connector/spark-pydocumentdb.png)


### <a name="data-flow-of-the-pydocumentdb-implementation"></a>A pyDocumentDB megvalósítási adatfolyama

Az adatfolyam a következőképpen történik:

1. A Spark főcsomópont az Azure Cosmos DB átjárócsomópont pyDocumentDB keresztül kapcsolódik. A felhasználó csak a Spark- és Azure Cosmos DB-kapcsolatok ad meg. A megfelelő fő és átjáró csomópontok való csatlakozás történik a felhasználó számára.
2. Az átjárócsomópont teszi Azure Cosmos DB, ahol a lekérdezést később fut a gyűjtemény a partíciók az adatok csomópontok irányuló lekérdezés. A lekérdezések választ küld vissza az átjárócsomópontnak, és adott eredményhalmaz a Spark főcsomópont küld vissza.
3. A Spark munkavégző csomópontokhoz későbbi lekérdezéseket (például egy Spark DataFrame) kerülnek feldolgozásra.

Spark és Azure Cosmos DB közötti kommunikáció a Spark főcsomópont és az Azure Cosmos DB átjárócsomópontok korlátozódik.  A lekérdezések lépjen a gyors lehetővé teszi, hogy a szállítási réteg a két csomópont között.

### <a name="install-pydocumentdb"></a>Telepítse a pyDocumentDB
Telepíthető pyDocumentDB az illesztőprogram-csomópont használatával **pip**, például:

```
pip install pyDocumentDB
```


### <a name="connect-spark-to-azure-cosmos-db-via-pydocumentdb"></a>Csatlakozás az Azure Cosmos Adatbázishoz Spark pyDocumentDB
A kommunikáció átviteli egyszerűsége hajt végre a lekérdezés végrehajtása a Spark on Azure Cosmos DB a pyDocumentDB viszonylag egyszerű.

A következő kódrészletet a pyDocumentDB használata Spark környezetben jeleníti meg.

```
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

A kódrészletet leírtaknak megfelelően:

* Az Azure Cosmos DB Python SDK (`pyDocumentDB`) tartalmazza az összes szükséges kapcsolati paraméter. Például az elsődleges helyek paraméter úgy dönt, a olvasható replika és a prioritási sorrendben.
*  A szükséges kódtárak importálja és konfigurálja a **főkulcsos** és **állomás** létrehozása az Azure Cosmos DB *ügyfél* (**pydocumentdb.document_client**).


### <a name="execute-spark-queries-via-pydocumentdb"></a>A Spark-lekérdezéseket hajt végre a pyDocumentDB keresztül
A következő példák az előző részlet megadott írásvédett kulcsok használatával létrehozott Azure Cosmos DB példányának használatára. A következő kódrészletet csatlakozik a **airports.codes** gyűjtemény a DoctorWho fiók, mint a korábban meghatározott, és a repülőtéri várost Washington államban kibontásához lekérdezés futtatása.

```
# Configure Database and Collections
databaseId = 'airports'
collectionId = 'codes'

# Configurations the Azure Cosmos DB client will use to connect to the database and collection
dbLink = 'dbs/' + databaseId
collLink = dbLink + '/colls/' + collectionId

# Set query parameter
querystr = "SELECT c.City FROM c WHERE c.State='WA'"

# Query documents
query = client.QueryDocuments(collLink, querystr, options=None, partition_key=None)

# Query for partitioned collections
# query = client.QueryDocuments(collLink, query, options= { 'enableCrossPartitionQuery': True }, partition_key=None)

# Push into list `elements`
elements = list(query)
```

A lekérdezés végrehajtása keresztül után **lekérdezés**, az eredmény egy **query_iterable. QueryIterable** , Python listájára alakítja át. Python listáját könnyen átalakítható a Spark DataFrame az alábbi kód használatával:

```
# Create `df` Spark DataFrame from `elements` Python list
df = spark.createDataFrame(elements)
```

### <a name="why-use-the-pydocumentdb-to-connect-spark-to-azure-cosmos-db"></a>Miért érdemes használni a pyDocumentDB Spark az Azure Cosmos Adatbázishoz csatlakozni?
Spark pyDocumentDB segítségével az Azure Cosmos Adatbázishoz kapcsolódó forgatókönyvek általában van ahol:

* Python használni kívánt.
* A Spark az Azure Cosmos Adatbázisból viszonylag kis eredményhalmazt vannak ad vissza. Vegye figyelembe, hogy az Azure Cosmos Adatbázisba az alapul szolgáló dataset Igen tekintélyes lehet. Szűrők, ez azt jelenti, hogy az Azure Cosmos DB adatforrás predikátum szűrők futtatott alkalmazzák.  

## <a name="spark-to-azure-cosmos-db-connector"></a>Spark és Azure Cosmos DB-összekötő

A Spark on Azure Cosmos DB összekötőre használja a [Azure DocumentDB Java SDK](https://github.com/Azure/azure-documentdb-java) és mozgatja az adatokat a Spark munkavégző csomópontokhoz és Azure Cosmos DB között, az alábbi ábrán látható módon:

![A Spark az Azure Cosmos DB összekötő adatfolyama](./media/spark-connector/spark-connector.png)

Az adatfolyam a következőképpen történik:

1. A Spark főcsomópont az beszerzése a partíciótérképen Azure Cosmos DB átjárócsomópont csatlakozik. A felhasználó csak a Spark- és Azure Cosmos DB-kapcsolatok ad meg. A megfelelő fő és átjáró csomópontok való csatlakozás történik a felhasználó számára.
2. Ezt az információt a külső fő csomópont valósul meg.  Ezen a ponton adható meg, hogy a partíció és az Azure Cosmos Adatbázisba való hozzáférést igénylő helyükre értelmezni tudja.
3. Ezek az információk átkerülnek a Spark munkavégző csomópontokhoz.
4. A Spark munkavégző csomópontokhoz csatlakozni az Azure Cosmos DB partíciókat közvetlenül kinyeri az adatokat, és térjen vissza az adatokat a Spark a partíciók a Spark munkavégző csomópontokhoz.

Spark és Azure Cosmos DB közötti kommunikáció oka jelentősen gyorsabb az adatátvitelt jelölik a Spark munkavégző csomópontokhoz és az Azure Cosmos DB adatok csomópontok (partíciók) között.

### <a name="build-the-spark-to-azure-cosmos-db-connector"></a>A Spark és Azure Cosmos DB-összekötő létrehozása
Az összekötő projekt jelenleg maven használja. Az összekötő függőségek nélkül létrehozásához futtathatja:
```
mvn clean package
```
Emellett letöltheti a legújabb verzióját a JAR a *kiadott* mappát.

### <a name="include-the-azure-cosmos-db-spark-jar"></a>Az Azure Cosmos DB külső JAR tartalmazza
A kód végrehajtása előtt meg kell adnia az Azure Cosmos DB külső JAR.  Használatakor a **spark-rendszerhéj**, majd a használatával megadhatja a JAR a **--JAR-fájlok kivételével** lehetőséget.  

```
spark-shell --master $master --jars /$location/azure-cosmosdb-spark-0.0.3-jar-with-dependencies.jar
```

A JAR függőségek nélkül végrehajtani, használja a következő kódot:

```
spark-shell --master $master --jars /$location/azure-cosmosdb-spark-0.0.3.jar,/$location/azure-documentdb-1.10.0.jar
```

Ha a notebook szolgáltatással, például az Azure HDInsight Jupyter notebook szolgáltatást használ, akkor használhatja a **spark magic** parancsokat:

```
%%configure
{ "jars": ["wasb:///example/jars/azure-documentdb-1.10.0.jar","wasb:///example/jars/azure-cosmosdb-spark-0.0.3.jar"],
  "conf": {
    "spark.jars.excludes": "org.scala-lang:scala-reflect"
   }
}
```

A **JAR-fájlok kivételével** parancs lehetővé teszi a két JAR-fájlok kivételével, a szükséges **azure-cosmosdb-spark** (önmagában, illetve az Azure DocumentDB Java SDK-t), és amelyeket **scala-tükrözze** , hogy ne zavarja a Livy hívások (Jupyter notebook > Livy > Spark).

### <a name="connect-spark-to-azure-cosmos-db-using-the-connector"></a>Spark csatlakozzon az összekötővel Azure Cosmos Adatbázishoz.
Bár a kommunikációs átviteli egy kicsit bonyolultabb, a lekérdezés végrehajtása a Spark on Azure Cosmos DB-összekötő segítségével is jelentősen gyorsabb.

A következő kódrészletet bemutatja, hogyan Spark környezetben az összekötő használatára.

```
// Import Necessary Libraries
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Configure connection to your collection
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

A kódrészletet leírtaknak megfelelően:

- **Azure-cosmosdb-spark** az összes szükséges kapcsolat paramétereit tartalmazza, többek között az elsődleges helyek. Például kiválaszthatja a olvasható replika és a prioritási sorrendben.
- Ebben az esetben a szükséges könyvtárak importálása, és a főkulcsos és az állomás hozhat létre az Azure Cosmos DB ügyfél.

### <a name="execute-spark-queries-via-the-connector"></a>Az összekötőn keresztül Spark-lekérdezéseket hajt végre

Az alábbi példában az Azure Cosmos DB-példány az előző részlet jött létre a megadott írásvédett kulcsok használatával. A következő kódrészletet a DepartureDelays.flights_pcoll gyűjtemény (a korábban megadott DoctorWho fiókban) csatlakozik, és a felhőszolgáltató közötti átviteléhez késleltetés információkat, amelyek a budapesti vannak induló légijáratok lekérdezés futtatása.

```
// Queries
var query = "SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'"
val df = spark.sql(query)

// Run DF query (count)
df.count()

// Run DF query (show)
df.show()
```

### <a name="why-use-the-spark-to-azure-cosmos-db-connector-implementation"></a>A Spark on Azure Cosmos DB összekötő megvalósításához miért érdemes használni?

Forgatókönyvek általában Spark csatlakozik Azure Cosmos DB-összekötővel van ahol:

* Scala használja, és frissítse úgy, hogy tartalmazzák a Python-burkoló leírtaknak megfelelően [probléma 3: hozzáadása Python burkoló és példák](https://github.com/Azure/azure-cosmosdb-spark/issues/3).
* Rendelkezik egy nagy mennyiségű adatot Apache Spark- és Azure Cosmos DB közötti átviteléhez.

Segítenek a lekérdezés teljesítménybeli különbség az, tekintse meg a [lekérdezés teszt futtatása wiki](https://github.com/Azure/azure-cosmosdb-spark/wiki/Query-Test-Runs).

## <a name="distributed-aggregation-example"></a>Elosztott összesítési – példa
Ez a témakör néhány példa arra, hogyan lehet úgy teheti meg, elosztott összesítések és az elemzések Apache Spark- és Azure Cosmos DB együtt. Azure Cosmos-adatbázis már támogatja az összesítéseket, amely ismertet a [bolygónk méretezési összesíti az Azure Cosmos DB blog](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/). Ez hogyan-EK az Apache Spark a következő szintre.

Vegye figyelembe, hogy ezeket az összesítéseket reference, hogy a [Spark az Azure Cosmos DB összekötő notebook](https://github.com/Azure/azure-cosmosdb-spark/blob/master/samples/notebooks/Spark-to-CosmosDB_Connector.ipynb).

### <a name="connect-to-flights-sample-data"></a>Járatok minta adatokhoz
Összesítési példákban elérni bizonyos repülési teljesítményadatokat tárolt a **DoctorWho** Azure Cosmos DB adatbázisban. A csatlakozáshoz, szüksége használatára a következő kódrészletet:

```
// Import Spark to Azure Cosmos DB connector
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark._
import com.microsoft.azure.cosmosdb.spark.config.Config

// Connect to Azure Cosmos DB Database
val readConfig2 = Config(Map("Endpoint" -> "https://doctorwho.documents.azure.com:443/",
"Masterkey" -> "le1n99i1w5l7uvokJs3RT5ZAH8dc3ql7lx2CG0h0kK4lVWPkQnwpRLyAN0nwS1z4Cyd1lJgvGUfMWR3v8vkXKA==",
"Database" -> "DepartureDelays",
"preferredRegions" -> "Central US;East US 2;",
"Collection" -> "flights_pcoll",
"SamplingRatio" -> "1.0"))

// Create collection connection
val coll = spark.sqlContext.read.cosmosDB(readConfig2)
coll.createOrReplaceTempView("c")
```

Ezt a kódrészletet a is fogjuk továbbítani a szűrt adatkészletet az Azure Cosmos Adatbázisból Spark ahol ez utóbbi végezhet elosztott összesítések alap-lekérdezés futtatható. Ebben az esetben arra kérjük, hogy a budapesti (SZE) egymástól légijáratoknál.

```
// Run, get row count, and time query
val originSEA = spark.sql("SELECT c.date, c.delay, c.distance, c.origin, c.destination FROM c WHERE c.origin = 'SEA'")
originSEA.createOrReplaceTempView("originSEA")
```

Az alábbi eredményeket a lekérdezések futtatása Jupyter notebook szolgáltatásból hoztak létre.  Ne feledje, hogy a kódrészleteket általános és nem meghatározott, bármely szolgáltatás.

### <a name="running-limit-and-count-queries"></a>Futó korlát és száma
Fentiekhez hasonló által használt SQL/Spark SQL, kezdjük egy **korlát** lekérdezést:

![Spark korlát lekérdezés](./media/spark-connector/spark-sql-query.png)

A következő lekérdezés az egyszerű és gyors **száma** lekérdezést:

![Spark COUNT lekérdezés](./media/spark-connector/spark-count-query.png)

### <a name="group-by-query"></a>A GROUP BY lekérdezés
A következő be van állítva, hogy könnyen futtatható az **GROUP BY** lekérdezések írásában, az Azure Cosmos DB adatbázishoz:

```
select destination, sum(delay) as TotalDelays
from originSEA
group by destination
order by sum(delay) desc limit 10
```

![Spark GROUP BY lekérdezés diagramhoz](./media/spark-connector/group-by-query-graph.png)

### <a name="distinct-order-by-query"></a>KÜLÖNÁLLÓ, ORDER BY lekérdezés
Itt egy **DISTINCT, ORDER BY** lekérdezést:

![Spark GROUP BY lekérdezés diagramhoz](./media/spark-connector/order-by-query.png)

### <a name="continue-the-flight-data-analysis"></a>Továbbra is az felé továbbított adatok elemzése
A következő példa lekérdezések felé továbbított adatok elemzése a folytatáshoz használhatja:

#### <a name="top-5-delayed-destinations-cities-departing-from-seattle"></a>Leggyakoribb 5 késleltetett célok (városokat) onnan induló Budapest
```
select destination, sum(delay)
from originSEA
where delay < 0
group by destination
order by sum(delay) limit 5
```
![Spark felső késések diagramhoz](./media/spark-connector/top-delays-graph.png)


#### <a name="calculate-median-delays-by-destination-cities-departing-from-seattle"></a>Közepes késések által a budapesti induló cél városokat kiszámítása
```
select destination, percentile_approx(delay, 0.5) as median_delay
from originSEA
where delay < 0
group by destination
order by percentile_approx(delay, 0.5)
```

![Spark közepes késések diagramhoz](./media/spark-connector/median-delays-graph.png)

## <a name="next-steps"></a>Következő lépések

Ha még nem tette meg, töltse le a Spark on Azure Cosmos DB-összekötőt a [azure-cosmosdb-spark](https://github.com/Azure/azure-cosmosdb-spark) GitHub-tárházban és vizsgálja meg a tárházban további források:

* [Elosztott összesítések példák](https://github.com/Azure/azure-cosmosdb-spark/wiki/Aggregations-Examples)
* [Mintaparancsfájlok és notebookok](https://github.com/Azure/azure-cosmosdb-spark/tree/master/samples)

Is érdemes áttekinteni a [Apache Spark SQL, DataFrames és adatkészletek útmutató](http://spark.apache.org/docs/latest/sql-programming-guide.html) és a [Apache Spark on Azure Hdinsighttal](../hdinsight/spark/apache-spark-jupyter-spark-sql.md) cikk.
