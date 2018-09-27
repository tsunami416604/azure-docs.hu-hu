---
title: Hozzáférés az Azure Cosmos DB Cassandra API-t az Azure Databricks
description: Ez a cikk bemutatja, hogyan működik az Azure Cosmos DB Cassandra API Azure databricksből.
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: e1d8f41c55ffd453507804b005d10620665b512c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222028"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Az Azure Databricks érheti el az Azure Cosmos DB Cassandra API adatait

Ez a cikk részletesen workwith az Azure Cosmos DB Cassandra API a Spark a hogyan [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks).

## <a name="prerequisites"></a>Előfeltételek

* [Egy Azure Cosmos DB Cassandra API-fiók](create-cassandra-dotnet.md#create-a-database-account)

* [Az Azure Cosmos DB Cassandra API-t kapcsolódás alapjai](cassandra-spark-generic.md)

* [Az Azure Databricks-fürt üzembe helyezése](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Tekintse át a mintakódokat, a Cassandra API használata](cassandra-spark-generic.md#next-steps)

* [Cqlsh ellenőrzés céljából használja, így igény szerint](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Cassandra API-példány konfigurációja Cassandra-összekötőhöz:**

  A Cassandra API-összekötő szükséges a Cassandra-kapcsolat adatai részeként a spark környezet inicializálása. Ha elindítja a Databricks-jegyzetfüzet, a spark környezet inicializálása már megtörtént, és nem tanácsos leállítása és, inicializálja újra. Egy megoldás, ha a fürt szintjén, a spark-fürt konfigurációját a Cassandra API példány konfigurációjának hozzáadásához. Ez a fürtönként egy egyszeri tevékenységet. Adja hozzá a következő kódot a Spark konfigurációhoz, szóközzel elválasztott kulcs-érték párt:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Adja hozzá a szükséges függőségek

* **Cassandra Spark-összekötő:** – Azure Cosmos DB Cassandra API integrálása a Spark, a Cassandra-összekötő az Azure Databricks-fürt akarjuk csatolni. Csatlakoztassa a fürt:

  * Tekintse át a Databricks futtatókörnyezet-verziója, a Spark-verziót. Majd keresse meg a [maven-koordináták](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) , amelyek kompatibilisek a Cassandra Spark-összekötő, és mellékelje a fürthöz. Lásd: ["Feltöltése egy Maven-csomag vagy a Spark-csomag"](https://docs.databricks.com/user-guide/libraries.html) a cikk az összekötő könyvtár csatlakoztatni a fürthöz. Például maven-koordináta "Databricks modul 4.3-as verzió", "Spark 2.3.1" és "Scala 2.11" van `spark-cassandra-connector_2.11-2.3.1`

* **Az Azure Cosmos DB Cassandra API-specifikus részletes ismertetése:** – egyéni kapcsolat gyár az újrapróbálkozási szabályzat a Cassandra Spark-összekötő az Azure Cosmos DB Cassandra API konfigurálásához szükséges. Adja hozzá a `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0` [maven-koordináták](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) a kódtár csatlakoztatása a fürtöt.

## <a name="sample-notebooks"></a>Mintafüzetek

Az Azure Databricks listáját [notebookok minta](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) letölthető a Github-adattárban érhetők el. Ezek a minták kitérnek, hogyan Spark az Azure Cosmos DB Cassandra API-hoz csatlakozhat, és végezze el az adatokat különböző CRUD-műveletek. Emellett [importálja az összes jegyzetfüzet](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) be a Databricks-munkaterület fürt, és futtathatja az alkalmazást. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Azure Cosmos DB Cassandra API a Spark Scala-programok elérése

Spark, automatizált folyamatokat az Azure databricks szolgáltatásban elküldi a fürt használatával futtatható programokat [spark-submit szkripttel](https://spark.apache.org/docs/latest/submitting-applications.html)) és ütemezett keresztül az Azure Databricks-feladatok futtatásához.

Az alábbiakban a hivatkozások segítségével kommunikál az Azure Cosmos DB Cassandra API Spark Scala-programok létrehozásának első lépései.
* [Hogyan csatlakozhat az Azure Cosmos DB Cassandra API a Spark Scala-program](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Spark Scala-program futtatása egy automatizált feladat az Azure Databricksben](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [Kódminták a Cassandra API használatának teljes listája](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>További lépések

Ismerkedés a [Cassandra API-fiók, adatbázis és tábla létrehozása](create-cassandra-api-account-java.md) Java-alkalmazás használatával.
