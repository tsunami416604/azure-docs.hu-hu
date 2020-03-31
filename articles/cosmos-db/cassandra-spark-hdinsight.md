---
title: Az Azure Cosmos DB Cassandra API elérése a Sparkból a YARN-on a HDInsight segítségével
description: Ez a cikk ismerteti, hogyan működik az Azure Cosmos DB Cassandra API-t a Spark yarn HDInsight
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: bef4ee14cb4a7d64d80dc5776d8ecea0f831881a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887632"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Az Azure Cosmos DB Cassandra API elérése a Sparkból a YARN-on a HDInsight segítségével

Ez a cikk ismerteti, hogyan érheti el az Azure Cosmos DB Cassandra API-t a Spark a YARN és a HDInsight-Spark a spark-shell. A HDInsight a Microsoft Hortonworks Hadoop PaaS-ja az Azure-ban, amely kihasználja a HDFS objektumtárolását, és számos ízben kapható, beleértve a [Sparkot](../hdinsight/spark/apache-spark-overview.md)is.  Míg a dokumentum tartalma hdinsight-spark hivatkozik, minden Hadoop-disztribúcióra alkalmazható.  

## <a name="prerequisites"></a>Előfeltételek

* [Azure Cosmos DB Cassandra API kiépítése](create-cassandra-dotnet.md#create-a-database-account)

* [Tekintse át az Azure Cosmos DB Cassandra API-hoz való csatlakozás alapjait](cassandra-spark-generic.md)

* [HDInsight-Spark-fürt kiépítése](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Tekintse át a Cassandra API-val való munka kódmintáit](cassandra-spark-generic.md#next-steps)

* [Használja a cqlsh-t az érvényesítéshez, ha úgy kívánja](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Cassandra API-konfiguráció a Spark2** - A Spark-összekötő Cassandra megköveteli, hogy a Cassandra-kapcsolat részleteit a Spark-környezet részeként inicializálva kell lennie. Amikor elindítja a Jupyter-jegyzetfüzetet, a spark-munkamenet és a környezet már inicializálva van, és nem tanácsos leállítani és újrainicializálni a Spark-környezetet, kivéve, ha a HDInsight alapértelmezett Jupyter-jegyzetfüzet-indítási részeként minden konfigurációkészlettel befejeződik. Az egyik megoldás az, hogy a Cassandra-példány részleteinek hozzáadása az Ambari, Spark2 szolgáltatás konfigurációja közvetlenül. Ez egy egyszeri tevékenység fürtenként, amely a Spark2 szolgáltatás újraindítását igényli.
 
  1. Nyissa meg az Ambari, Spark2 szolgáltatást, és válassza ki a konfigurációkat

  2. Ezután lépjen az egyéni spark2-defaults elemre, és adjon hozzá egy új tulajdonságot a következőkkel, majd indítsa újra a Spark2 szolgáltatást:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Az Azure Cosmos DB Cassandra API elérése a Spark rendszerhéjból

A Spark shell tesztelési/feltárási célokra szolgál.

* Indítsa el a spark-shell a szükséges maven-függőségek kompatibilis a fürt Spark-verzió.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Néhány DDL- és DML-művelet végrehajtása

  ```scala
  import org.apache.spark.rdd.RDD
  import org.apache.spark.{SparkConf, SparkContext}

  import spark.implicits._
  import org.apache.spark.sql.functions._
  import org.apache.spark.sql.Column
  import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType,LongType,FloatType,DoubleType, TimestampType}
  import org.apache.spark.sql.cassandra._

  //Spark connector
  import com.datastax.spark.connector._
  import com.datastax.spark.connector.cql.CassandraConnector

  //CosmosDB library for multiple retry
  import com.microsoft.azure.cosmosdb.cassandra

  // Specify connection factory for Cassandra
  spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

  // Parallelism and throughput configs
  spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
  spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10")
  spark.conf.set("spark.cassandra.output.concurrent.writes", "100")
  spark.conf.set("spark.cassandra.concurrent.reads", "512")
  spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
  spark.conf.set("spark.cassandra.connection.keep_alive_ms", "60000000") //Increase this number as needed
  ```

* CRUD-műveletek futtatása

  ```scala
  //1) Create table if it does not exist
  val cdbConnector = CassandraConnector(sc)
  cdbConnector.withSessionDo(session => session.execute("CREATE TABLE IF NOT EXISTS books_ks.books(book_id TEXT PRIMARY KEY,book_author TEXT, book_name TEXT,book_pub_year INT,book_price FLOAT) WITH cosmosdb_provisioned_throughput=4000;"))

  //2) Delete data from potential prior runs
  cdbConnector.withSessionDo(session => session.execute("DELETE FROM books_ks.books WHERE book_id IN ('b00300','b00001','b00023','b00501','b09999','b01001','b00999','b03999','b02999','b000009');"))

  //3) Generate a few rows
  val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
  ).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

  //4) Persist
  booksDF.write.mode("append").format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000")).save()

  //5) Read the data in the table
  spark.read.format("org.apache.spark.sql.cassandra").options(Map( "table" -> "books", "keyspace" -> "books_ks")).load.show
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Access Azure Cosmos DB Cassandra API a Jupyter-jegyzetfüzetekből

A HDInsight-Spark zeppelin- és Jupyter-jegyzetfüzet-szolgáltatásokat kínál. Mindkettő webalapú jegyzetfüzet-környezet, amely támogatja a Scala és a Python. A jegyzetfüzetek kiválóan használhatók interaktív feltáró elemzésekhez és együttműködéshez, de nem operatív/gyártási folyamatokhoz való kavatódáshoz.

A következő Jupyter-jegyzetfüzetek feltölthetők a HDInsight Spark-fürtbe, és kész mintákat biztosítanak az Azure Cosmos DB Cassandra API-val való munkához. Győződjön meg arról, hogy tekintse át az első notebook, `1.0-ReadMe.ipynb` hogy tekintse át a Spark szolgáltatás konfigurációját az Azure Cosmos DB Cassandra API-hoz való csatlakozáshoz.

Töltse le ezeket a jegyzetfüzeteket [az azure-cosmos-db-cassandra-api-spark-notebook-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) segítségével a gépére.
  
### <a name="how-to-upload"></a>Hogyan lehet feltölteni:
Amikor elindítja a Jupytert, keresse meg a Scala-t. Először hozzon létre egy könyvtárat, majd töltse fel a jegyzetfüzeteket a könyvtárba. A feltöltés gomb a jobb felső oldalon található.  

### <a name="how-to-run"></a>Hogyan kell futtatni:
Futtassa végig a jegyzetfüzeteket és az egyes jegyzetfüzet-cellákat egymás után.  Az összes cella végrehajtásához kattintson az egyes jegyzetfüzetek tetején található futtatás gombra, vagy az egyes cellákhoz a shift+enter gombra.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Hozzáférés az Azure Cosmos DB Cassandra API-val a Spark Scala-programból

Az automatizált folyamatok éles környezetben, Spark-programok küldése a fürthöz keresztül [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>További lépések

* [Spark Scala-program létrehozása ide-ben, és beküldése a HDInsight Spark-fürtbe a Livy-n keresztül végrehajtásra](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Csatlakozás az Azure Cosmos DB Cassandra API-hoz egy Spark Scala-programból](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [A Cassandra API-val végzett munka kódmintáinak teljes listája](cassandra-spark-generic.md)
