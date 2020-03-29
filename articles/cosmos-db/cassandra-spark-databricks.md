---
title: Az Azure Cosmos DB Cassandra API elérése az Azure Databricks-ből
description: Ez a cikk ismerteti, hogyan működik együtt az Azure Cosmos DB Cassandra API-t az Azure Databricks.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 37a06b19285c1196b5d87830ea176d4bd0d4eade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60894011"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Az Azure Cosmos DB Cassandra API-adatok elérése az Azure Databricks-ből

Ez a cikk ismerteti, hogyan dolgozhat az Azure Cosmos DB Cassandra API-val a Sparkból az [Azure Databricks-en.](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks)

## <a name="prerequisites"></a>Előfeltételek

* [Azure Cosmos DB Cassandra API-fiók kiépítése](create-cassandra-dotnet.md#create-a-database-account)

* [Tekintse át az Azure Cosmos DB Cassandra API-hoz való csatlakozás alapjait](cassandra-spark-generic.md)

* [Azure Databricks-fürt kiépítése](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Tekintse át a Cassandra API-val való munka kódmintáit](cassandra-spark-generic.md#next-steps)

* [Használja a cqlsh-t az érvényesítéshez, ha úgy kívánja](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Cassandra API-példány konfigurációja a Cassandra-összekötőhöz:**

  A Cassandra API összekötője megköveteli, hogy a Cassandra-kapcsolat részleteit inicializálják a spark-környezet részeként. Databricks-jegyzetfüzet indításakor a spark-környezet már inicializálva van, és nem tanácsos leállítani és újrainicializálni. Az egyik megoldás a Cassandra API-példány konfigurációjának hozzáadása fürtszinten, a fürt szikrakonfigurációjában. Ez fürtenként egyszeri tevékenység. Adja hozzá a következő kódot a Spark konfigurációjához szóközt választott kulcsérték-párként:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

* **Cassandra Spark-összekötő:** - az Azure Cosmos DB Cassandra API és a Spark integrálásához a Cassandra-összekötőt csatolni kell az Azure Databricks-fürthöz. A fürt csatolása:

  * Tekintse át a Databricks futásidejű verzió, a Spark-verzió. Ezután keresse meg a [Maven koordinátáit,](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) amelyek kompatibilisek a Cassandra Spark csatlakozóval, és csatolja a fürthöz. [Lásd: "Töltsön fel egy Maven-csomagot vagy a Spark-csomagot"](https://docs.databricks.com/user-guide/libraries.html) című cikket az összekötő könyvtár fürthöz való csatolásához. Például a "Databricks Runtime 4.3", "Spark 2.3.1" és "Scala 2.11" (Databricks Runtime 4.3-as verzió", "Spark 2.3.1" és "Scala 2.11" koordinátája`spark-cassandra-connector_2.11-2.3.1`

* **Azure Cosmos DB Cassandra API-specifikus könyvtár:** – Egyéni kapcsolatgyár szükséges a Cassandra Spark-összekötőről az Azure Cosmos DB Cassandra API-ra vonatkozó újrapróbálkozási szabályzat konfigurálásához. Adja `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0`hozzá a [maven koordinátákat,](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) hogy csatolja a könyvtárat a fürthöz.

## <a name="sample-notebooks"></a>Mintanotebookok

Az Azure Databricks [mintajegyzetfüzetek](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) listája letölthető a GitHub-tárházban. Ezek a minták tartalmazzák, hogyan csatlakozhat az Azure Cosmos DB Cassandra API-hoz a Sparktól, és különböző CRUD-műveleteket hajthat végre az adatokon. Az [összes jegyzetfüzetet importálhatja a Databricks](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) fürt munkaterületére, és futtathatja azt. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Az Azure Cosmos DB Cassandra API elérése a Spark Scala-programokból

Az Azure Databricks automatizált folyamataiként futtatandó [Spark-programok a spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html)használatával kerülnek elküldésre a fürthöz, és az Azure Databricks-feladatokon való futtatásra vannak ütemezve.

Az alábbi hivatkozások segítségével könnyebben elindíthatja a Spark Scala-programok készítését az Azure Cosmos DB Cassandra API-val való együttműködéshez.
* [Csatlakozás az Azure Cosmos DB Cassandra API-hoz egy Spark Scala-programból](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [A Spark Scala-programok automatikus feladatként való futtatása az Azure Databricks-en](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [A Cassandra API-val végzett munka kódmintáinak teljes listája](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>További lépések

Cassandra [API-fiók, adatbázis és tábla javaalkalmazás](create-cassandra-api-account-java.md) használatával történő létrehozásával ismerkedés.
