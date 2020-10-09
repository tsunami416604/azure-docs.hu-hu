---
title: Hozzáférés Azure Cosmos DB Cassandra API a Azure Databricks
description: Ez a cikk azt ismerteti, hogyan használható a Azure Cosmos DB Cassandra API a Azure Databricks.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 30bd3187973de204f27a3be3862351550d6a56f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85260474"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Azure Cosmos DB Cassandra API adatok elérése a Azure Databricks

Ez a cikk részletesen ismerteti, hogyan workwith Azure Cosmos DB Cassandra API a Sparkból a [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks).

## <a name="prerequisites"></a>Előfeltételek

* [Azure Cosmos DB Cassandra API fiók kiépítése](create-cassandra-dotnet.md#create-a-database-account)

* [Tekintse át Azure Cosmos DB Cassandra APIhoz való csatlakozás alapjait](cassandra-spark-generic.md)

* [Azure Databricks-fürt kiépítése](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Tekintse át a Cassandra API használatáról szóló kód mintáit](cassandra-spark-generic.md#next-steps)

* [Cqlsh használata az érvényesítéshez, ha szeretné](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Cassandra API példány konfigurációja a Cassandra connectorhoz:**

  A Cassandra API-összekötőhöz szükséges a Cassandra kapcsolat részleteinek inicializálása a Spark-környezet részeként. Databricks-jegyzetfüzet indításakor a Spark-környezet már inicializálva van, és nem tanácsos leállítani és újrainicializálni. Az egyik megoldás az Cassandra API-példány konfigurációjának hozzáadása fürt szintjén, a fürt Spark-konfigurációjában. Ez egy fürtön belüli egyszeri tevékenység. Adja hozzá a következő kódot a Spark-konfigurációhoz szóközzel tagolt kulcs-érték párokként:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>A szükséges függőségek hozzáadása

* **Cassandra Spark-összekötő:** – Azure Cosmos db Cassandra API a Spark-nal való integrálásához csatolni kell a Cassandra-összekötőt a Azure Databricks-fürthöz. A fürt csatolása:

  * Tekintse át a Databricks futásidejű verzióját, a Spark verzióját. Ezután keresse meg a Cassandra Spark-összekötővel kompatibilis [Maven-koordinátákat](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) , és csatolja azt a fürthöz. Az összekötő függvénytár fürthöz csatolásához tekintse meg a ["Maven-csomag vagy Spark-csomag feltöltése"](https://docs.databricks.com/user-guide/libraries.html) című cikket. Például a Maven koordinálása a "Databricks Runtime Version 4,3", a "Spark 2.3.1" és a "Scala 2,11" `spark-cassandra-connector_2.11-2.3.1`

* **Cassandra API-specifikus függvénytár Azure Cosmos db:** – az újrapróbálkozási szabályzatnak a Cassandra Spark-összekötőről a Azure Cosmos db Cassandra API való konfigurálásához egyéni csatlakozási gyár szükséges. Adja hozzá a `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0` [Maven koordinátáit](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) , hogy csatolja a tárat a fürthöz.

## <a name="sample-notebooks"></a>Mintanotebookok

Az Azure Databricks [minta jegyzetfüzetek](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) listája elérhető a GitHub-tárházban a letöltéshez. Ezek a minták bemutatják, hogyan csatlakozhat a Spark Azure Cosmos DB Cassandra APIhoz, és különböző szifilisz-műveleteket hajthat végre az adatokon. [Az összes jegyzetfüzetet is importálhatja](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) a Databricks-fürt munkaterületére, és futtathatja azt. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Azure Cosmos DB Cassandra API elérése a Spark Scala-programokból

A Spark-programok, amelyeken a Azure Databricks automatikus folyamatai futnak, a rendszer a [Spark-Submit](https://spark.apache.org/docs/latest/submitting-applications.html)használatával küldi el a fürtöt, és ütemezi a Azure Databricks feladatok futtatására.

Az alábbi hivatkozások segítséget nyújtanak a Spark Scala-programok a Azure Cosmos DB Cassandra API való interakcióhoz való megkezdésében.
* [Kapcsolódás Azure Cosmos DB Cassandra API Spark Scala programból](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Spark Scala-program futtatása automatizált feladatokként Azure Databricks](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [A Cassandra API használatához használható mintakód-minták teljes listája](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>További lépések

Ismerkedjen meg [Cassandra API fiók, adatbázis és egy Java-alkalmazás használatával létrehozott táblázat létrehozásával](create-cassandra-api-account-java.md) .
