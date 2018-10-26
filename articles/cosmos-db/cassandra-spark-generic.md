---
title: A Spark az Azure Cosmos DB Cassandra API használata
description: Ez a cikk a fő lapján, a Spark a Cosmos DB Cassandra API-integráció.
services: cosmos-db
author: anagha-microsoft
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: 165919fa3d456786e926f754dba378be38c12588
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094244"
---
# <a name="connect-to-azure-cosmos-db-cassandra-api-from-spark"></a>Csatlakozás a Spark az Azure Cosmos DB Cassandra API-hoz

Ez a cikk az egyik, többek között Spark az Azure Cosmos DB Cassandra API-integrációval kapcsolatos cikkeket. A cikkek terjed ki a kapcsolat, adatok definíció Language(DDL) operations, alapvető adatokat adatkezelési Language(DML) műveleteket és speciális Azure Cosmos DB Cassandra API-integráció Spark rendszerből. 

## <a name="prerequisites"></a>Előfeltételek
* [Üzembe helyezhető egy Azure Cosmos DB Cassandra API-fiókot.](create-cassandra-dotnet.md#create-a-database-account)

* A választott, a Spark környezet kiépítése [[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) | [Azure HDInsight-Spark](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-jupyter-spark-sql) |} Mások].

## <a name="dependencies-for-connectivity"></a>A hálózati kapcsolatot függőségek
* **Spark-összekötő a Cassandra:** Spark-összekötő segítségével Azure Cosmos DB Cassandra API-hoz csatlakozhat.  Azonosítsa és verzióját használja, az összekötő található [Maven központi]( https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) Ez az a Spark környezet és a Scala Spark verzióival kompatibilis.

* **Az Azure Cosmos DB Cassandra API segédkódtárba helyezni:** a Spark-összekötő mellett egy másik szalagtárhoz nevű kell [azure-cosmos-cassandra-spark-segítő]( https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) Azure Cosmos DB-ből. Ez a kódtár egyéni kapcsolat gyári, és ismételje meg a házirend osztályokat tartalmazza.

  Az Azure Cosmos DB újrapróbálkozási szabályzat HTTP állapot kód 429 ("kérés sebessége nagy") kivételek van konfigurálva. Az Azure Cosmos DB Cassandra API a rendszer lefordítja a Cassandra natív protokoll túlterhelt hibákká ezeket a kivételeket, és újra a biztonsági kompromisszumot. Mivel az Azure Cosmos DB a kiosztott átviteli sebesség modellt használ, kérelem korlátozó kivételeket fordulhat elő, ha a bejövő/kimenő forgalom sebesség növekedését. Az újrapróbálkozási szabályzat védelmet biztosít a spark-feladatok ellen, amelyek rövid ideig túllépik a gyűjteményhez kiosztott átviteli adatok adatforgalmi csúcsokhoz.

  > [!NOTE] 
  > Az újrapróbálkozási szabályzat a spark-feladatok csak pillanatnyi adatforgalmi csúcsokhoz ellen védheti. Ha nem konfigurálta elegendő csökkenti a számítási feladatok futtatásához szükséges, az újrapróbálkozási szabályzat nem alkalmazható, és az újrapróbálkozási szabályzat osztály rethrows a kivétel.

* **Az Azure Cosmos DB-fiók kapcsolat részletei:** Your Azure Cassandra API fióknév, fiók végpontját és kulcsát.
    
## <a name="spark-connector-throughput-configuration-parameters"></a>A Spark összekötő átviteli konfigurációs paraméterei

A következő táblázat felsorolja az Azure Cosmos DB Cassandra API-specifikus átviteli konfigurációs paramétereket az összekötő által biztosított. Az összes konfigurációs paramétereket részletes listáját lásd: [hivatkozás](https://github.com/datastax/spark-cassandra-connector/blob/master/doc/reference.md) a Spark Cassandra összekötő GitHub-adattár oldalát.

| **Tulajdonság neve** | **Alapértelmezett érték** | **Leírás** |
|---------|---------|---------|
| Spark.cassandra.Output.Batch.size.Rows |  1 |Egyetlen kötegenkénti sorok száma. Ez a paraméter értéke 1. Ezt a paramétert a nehéz számítási feladatokhoz magasabb átviteli sebesség eléréséhez használja. |
| Spark.cassandra.connection.connections_per_executor_max  | None | Csomópontonként engedélyezett végrehajtó kapcsolatok maximális számát. 10 * n megegyezik a csomópontonként Cassandra-n csomópontos fürtben 10 kapcsolatot. Tehát 5 kapcsolatot végrehajtó csomópontonként egy Cassandra-fürt 5 csomópontos van szükség, ha majd kell beállítania ezt a konfigurációt a 25. Módosítsa ezt az értéket a párhuzamosság foka vagy a spark-feladatok konfigurált végrehajtóval száma alapján.   |
| Spark.cassandra.Output.concurrent.writes  |  100 | A párhuzamos írások száma végrehajtó előforduló számát határozza meg. Mivel "batch.size.rows" értéke 1, ügyeljen arra, hogy ennek megfelelően vertikális felskálázás ezt az értéket. Módosítsa ezt az értéket a párhuzamosság vagy az átviteli sebességet, amelyeket szeretne eléréséhez a számítási feladatok alapján. |
| Spark.cassandra.concurrent.reads |  512 | A párhuzamos olvasások száma végrehajtó előforduló számát határozza meg. Ez az érték alapján a párhuzamosság vagy az átviteli sebességet a számítási feladatok elérni kívánt módosítása  |
| Spark.cassandra.Output.throughput_mb_per_sec  | None | A teljes olvasási sebességet végrehajtó határozza meg. Ez a paraméter használható, valamint a felső korlátja. a spark-feladat átviteli alapja, az átviteli sebesség a Cosmos DB-gyűjtemények.   |
| Spark.cassandra.input.reads_per_sec| None   | A teljes olvasási sebességet végrehajtó határozza meg. Ez a paraméter használható, valamint a felső korlátja. a spark-feladat átviteli alapja, az átviteli sebesség a Cosmos DB-gyűjtemények.  |
| Spark.cassandra.Output.Batch.Grouping.Buffer.size |  1000  | Cassandra API küldése előtt a memóriában tárolt egyetlen spark feladatonként kötegeknek a száma határozza meg |
| Spark.cassandra.connection.keep_alive_ms | 60000 | Határozza meg az az időtartam, ameddig a fel nem használt kapcsolatok érhetők el. | 

Állítsa be az átviteli sebesség és a ezeket a paramétereket a számítási feladatok várt a spark-feladatok és az átviteli sebesség a Cosmos DB-fiók kiépítése a párhuzamosság szintjét.

## <a name="connecting-to-azure-cosmos-db-cassandra-api-from-spark"></a>A Spark az Azure Cosmos DB Cassandra API-t kapcsolódás

### <a name="cqlsh"></a>cqlsh
A következő parancsok bemutatják, hogyan csatlakozhat az Azure cosmos DB Cassandra API cqlsh.  Ez akkor hasznos, haladjon végig a minták a Spark ellenőrzés céljából.<br>
**A Linux/Unix/Mac:**

```bash
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false
cqlsh.py YOUR-COSMOSDB-ACCOUNT-NAME.cassandra.cosmosdb.azure.com 10350 -u YOUR-COSMOSDB-ACCOUNT-NAME -p YOUR-COSMOSDB-ACCOUNT-KEY --ssl
```

### <a name="1--azure-databricks"></a>1.  Azure Databricks
Az alábbi cikk ismerteti az Azure Databricks-fürt kiépítése, fürtkonfiguráció csatlakozhat az Azure Cosmos DB Cassandra API-hoz, és számos mintafüzetek, mind a DDL-műveletek, DML-műveletek és egyéb.<BR>
[Azure Cosmos DB Cassandra API együttműködik az Azure databricks](cassandra-spark-databricks.md)<BR>
  
### <a name="2--azure-hdinsight-spark"></a>2.  Az Azure HDInsight-Spark
Az alábbi cikk ismerteti a Spark HDinsight szolgáltatás, a fürtkonfiguráció csatlakozhat az Azure Cosmos DB Cassandra API-hoz, és számos mintafüzetek, mind a DDL-műveletek, DML-műveletek és egyéb.<BR>
[Az Azure Cosmos DB Cassandra API-hoz az Azure HDInsight-Spark használata](cassandra-spark-hdinsight.md)
 
### <a name="3--spark-environment-in-general"></a>3.  Az általános Spark-környezetben
A fenti szakaszban meghatározott, Azure Spark-alapú PaaS-szolgáltatások is, amíg ez a szakasz ismertet bármely általános Spark-környezetben.  Összekötő függőségeket, importálja és munkamenet-konfiguráció Spark lásd lent. A "Következő lépések" szakaszban Kódminták a DDL-műveletek, DML-műveletek és egyéb ismerteti.  

#### <a name="connector-dependencies"></a>Összekötő függőségei:

1. Adja hozzá a maven-koordináták beolvasni a [Cassandra Spark-összekötő](cassandra-spark-generic.md#dependencies-for-connectivity)
2. Adja hozzá a maven-koordinátái a [segédkódtárba helyezni az Azure Cosmos DB](cassandra-spark-generic.md#dependencies-for-connectivity) Cassandra API-hoz

#### <a name="imports"></a>Importálásokat:

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector

//CosmosDB library for multiple retry
import com.microsoft.azure.cosmosdb.cassandra
```

#### <a name="spark-session-configuration"></a>A Spark munkamenet-konfiguráció:

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

A következő cikkek bemutatják a Spark-integráció az Azure Cosmos DB Cassandra API. 
 
* [DDL-műveletek](cassandra-spark-ddl-ops.md)
* [Hozzon létre/beszúrási műveletek](cassandra-spark-create-ops.md)
* [olvasási műveletek](cassandra-spark-read-ops.md)
* [Upsert művelet](cassandra-spark-upsert-ops.md)
* [Törlési műveletek](cassandra-spark-delete-ops.md)
* [Összesítési műveletek](cassandra-spark-aggregation-ops.md)
* [Tábla másolási műveletek](cassandra-spark-table-copy-ops.md)
