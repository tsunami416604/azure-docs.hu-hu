---
title: Az Azure Cosmos DB Cassandra API-val való együttműködés a Sparkból
description: Ez a cikk a Cosmos DB Cassandra API-integráció a Spark főoldala.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/01/2019
ms.openlocfilehash: cb34ea44c069f067d13a6480531a94a1a515f380
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70241247"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Csatlakozás az Azure Cosmos DB Cassandra API-hoz a Sparkból

Ez a cikk az Azure Cosmos DB Cassandra API-integráció a Spark egyik cikksorozata. A cikkek a kapcsolat, adatdefiníciós nyelv(DDL) műveletek, alapvető adatkezelési nyelv (DML) műveletek, és a spark fejlett Azure Cosmos DB Cassandra API-integráció. 

## <a name="prerequisites"></a>Előfeltételek
* [Azure Cosmos DB Cassandra API-fiók kiépítése.](create-cassandra-dotnet.md#create-a-database-account)

* A Spark-környezet kiválasztása [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [Azure HDInsight-Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) | Egyéb].

## <a name="dependencies-for-connectivity"></a>Függőségek a kapcsolathoz
* **Spark csatlakozó Cassandra számára:** A Spark-összekötő az Azure Cosmos DB Cassandra API-hoz való csatlakozáshoz használható.  Azonosítsa és használja az összekötő [Maven-központban]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) található verzióját, amely kompatibilis a Spark és a Scala spark-i verzióival.

* **Az Azure Cosmos DB segítőkönyvtára a Cassandra API-hoz:** A Spark-összekötő mellett egy másik [azure-cosmos-cassandra-spark-helper nevű azure-cosmos-spark-helper]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) az Azure Cosmos DB-től. Ez a tár egyéni kapcsolat-előállító és újraházirendosztályokat tartalmaz.

  Az Újrapróbálkozási szabályzat az Azure Cosmos DB-ben úgy van konfigurálva, hogy kezelje a 429-es HTTP-állapotkódot("Nagy kérelemarány") kivételeket. Az Azure Cosmos DB Cassandra API ezeket a kivételeket túlterhelt hibákká fordítja a Cassandra natív protokollon, és újra próbálkozhat a back-offokkal. Mivel az Azure Cosmos DB kiépített átviteli sebességmodellt használ, a kérelmek sebességkorlátozási kivételei akkor fordulnak elő, amikor a be- és kilépési arányok nőnek. Az újrapróbálkozási szabályzat megvédi a spark-feladatok adatkiugrások, amelyek egy pillanatra meghaladják a tároló számára lefoglalt átviteli.

  > [!NOTE] 
  > Az újrapróbálkozási szabályzat csak a pillanatnyi csúcsok ellen védi a szikrafeladatokat. Ha még nem konfigurált a számítási feladatok futtatásához szükséges elegendő rendszercsoport, majd az újrapróbálkozási szabályzat nem alkalmazható, és az újrapróbálkozási házirend osztály újradobja a kivételt.

* **Az Azure Cosmos DB-fiók kapcsolatának részletei:** Az Azure Cassandra API-fiók neve, a fiók végpontja és a kulcs.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>Spark-összekötő átviteli konfigurációs paraméterei

Az alábbi táblázat az Azure Cosmos DB Cassandra API-specifikus átviteli sebességű konfigurációs paramétereket sorolja fel az összekötő által. Az összes konfigurációs paraméter részletes listáját a Spark Cassandra Connector GitHub-tárház [konfigurációs referencialapján](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) találja.

| **Tulajdonság neve** | **Alapértelmezett érték** | **Leírás** |
|---------|---------|---------|
| spark.cassandra.output.batch.size.rows |  1 |Sorok száma egy kötegenként. Állítsa ezt a paramétert 1-re. Ez a paraméter nagyobb átviteli terhelés eléréséhez használható a nagy számítási feladatokhoz. |
| szikra.cassandra.connection.connections_per_executor_max  | None | A kapcsolatok maximális száma csomópontonként végrehajtónként. A 10*n az n-csomópont Cassandra-fürt csomópontonkénti 10 kapcsolatának felel meg. Így ha egy 5 csomópontcassandra-fürtcsomópontonként 5 kapcsolatra van szüksége csomópontonként, akkor ezt a konfigurációt 25-re kell állítania. Módosítsa ezt az értéket a párhuzamosság mértéke vagy a végrehajtók száma alapján, amelyekhez a spark-feladatok konfigurálva vannak.   |
| spark.cassandra.output.concurrent.writes  |  100 | A végrehajtónként előforduló párhuzamos írások számát határozza meg. Mivel a "batch.size.rows" értékét 1-re állította, győződjön meg arról, hogy ennek megfelelően felskálázhatja ezt az értéket. Módosítsa ezt az értéket a párhuzamosság foka vagy a számítási feladatokhoz elérni kívánt átviteli érték alapján. |
| spark.cassandra.concurrent.reads |  512 | A végrehajtónként előforduló párhuzamos olvasások számát határozza meg. Módosítsa ezt az értéket a párhuzamosság foka vagy a munkaterheléshez elérni kívánt átviteli érték alapján  |
| szikra.cassandra.output.throughput_mb_per_sec  | None | A végrehajtónkénti teljes írási átviteli áteresztőparancsot határozza meg. Ez a paraméter a spark-feladat átviteli érték felső határaként használható, és a Cosmos-tároló kiosztott átviteli keresztüli.   |
| spark.cassandra.input.reads_per_sec| None   | A végrehajtónkénti teljes olvasási átviteli áteresztőkészséget határozza meg. Ez a paraméter a spark-feladat átviteli érték felső határaként használható, és a Cosmos-tároló kiosztott átviteli keresztüli.  |
| spark.cassandra.output.batch.grouping.buffer.size |  1000  | A Cassandra API-nak való küldés előtt a memóriában tárolható egyetlen szikrafeladatonkénti kötegek számát határozza meg. |
| spark.cassandra.connection.keep_alive_ms | 60000 | Azt az időtartamot határozza meg, ameddig a fel nem használt kapcsolatok elérhetők. | 

Állítsa be ezeknek a paramétereknek az átviteli és a párhuzamossági fokát a spark-feladatok várható munkaterhelése és a Cosmos DB-fiókhoz kiépített átviteli igény alapján.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>Csatlakozás az Azure Cosmos DB Cassandra API-hoz a Sparkból

### <a name="cqlsh"></a>cqlsh között
Az alábbi parancsok részletesen ismertetik, hogyan csatlakozhat az Azure CosmosDB Cassandra API-hoz cqlsh-ból.  Ez akkor hasznos, ha a Spark-ban a minták on-t futtatja az ellenőrzés hez.<br>
**Linux/Unix/Mac-ről:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1. Azure Databricks
Az alábbi cikk ismerteti az Azure Databricks fürtkiépítés, fürtkonfiguráció az Azure Cosmos DB Cassandra API-hoz való csatlakozáshoz, és számos mintajegyzetfüzetet, amelyek DDL-műveleteket, DML-műveleteket és egyebeket fednek le.<BR>
[Az Azure Cosmos DB Cassandra API-val való munka az Azure databricks-ből](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2. Azure HDInsight-Spark
Az alábbi cikk ismerteti a HDinsight-Spark szolgáltatás, kiépítése, fürtkonfiguráció az Azure Cosmos DB Cassandra API-hoz való csatlakozáshoz, és számos mintanotebook, amely a DDL-műveletek, DML-műveletek és több.<BR>
[Az Azure Cosmos DB Cassandra API munkája az Azure HDInsight-Sparkból](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3. Spark környezet általában
Míg a fenti szakaszok az Azure Spark-alapú PaaS-szolgáltatásokra vonatkoztak, ez a szakasz minden általános Spark-környezetet lefed.  Az összekötőfüggőségeket, az importálásokat és a Spark-munkamenet-konfigurációt az alábbiakban részletezzük. A "Következő lépések" szakasz a DDL-műveletek, A DML-műveletek és egyebek kódmintáit ismerteti.  

#### <a name="connector-dependencies"></a>Összekötő-függőségek:

1. Adja hozzá a maven koordinátákat a [Spark Cassandra összekötőjének leérkezéséhez](cassandra-spark-generic.md#dependencies-for-connectivity)
2. Adja hozzá a Maven koordinátáit az [Azure Cosmos DB segítő könyvtárcassandra](cassandra-spark-generic.md#dependencies-for-connectivity) API

#### <a name="imports"></a>Behozatal:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>Spark-munkamenet konfigurációja:

```scala
//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related. You can adjust the values as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

## <a name="next-steps"></a>További lépések

Az alábbi cikkek bemutatják a Spark-integrációt az Azure Cosmos DB Cassandra API-val. 
 
* [DDL-műveletek](cassandra-spark-ddl-ops.md)
* [Műveletek létrehozása/beszúrása](cassandra-spark-create-ops.md)
* [Olvasási műveletek](cassandra-spark-read-ops.md)
* [Upsert műveletek](cassandra-spark-upsert-ops.md)
* [Műveletek törlése](cassandra-spark-delete-ops.md)
* [Összesítési műveletek](cassandra-spark-aggregation-ops.md)
* [Táblamásolási műveletek](cassandra-spark-table-copy-ops.md)
