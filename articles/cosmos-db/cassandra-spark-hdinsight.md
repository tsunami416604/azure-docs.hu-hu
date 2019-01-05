---
title: Hozzáférés az Azure Cosmos DB Cassandra API a Spark a YARN-HDInsight
description: Ez a cikk bemutatja, hogyan működik az Azure Cosmos DB Cassandra API a Spark on HDInsight a YARN
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: f728baedf9e325f224ce52e64325064f553d2671
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54032899"
---
# <a name="access-azure-cosmos-db-cassandra-api-from-spark-on-yarn-with-hdinsight"></a>Hozzáférés az Azure Cosmos DB Cassandra API a Spark a YARN-HDInsight

Ez a cikk bemutatja, hogyan a Spark on HDInsight-Spark a YARN a spark-shell az Azure Cosmos DB Cassandra API elérését. HDInsight a Microsoft Hortonworks Hadoop PaaS Azure objektumtár használja a HDFS, és számos változata használható, beleértve a érhető el a rendszer [Spark](../hdinsight/spark/apache-spark-overview.md).  Amíg ez a dokumentum a tartalmának a Spark HDInsight hivatkozik, esetén minden Hadoop disztribúciók alkalmazható.  

## <a name="prerequisites"></a>Előfeltételek

* [Az Azure Cosmos DB Cassandra API üzembe helyezése](create-cassandra-dotnet.md#create-a-database-account)

* [Az Azure Cosmos DB Cassandra API-t kapcsolódás alapjai](cassandra-spark-generic.md)

* [A Spark HDInsight-fürt üzembe helyezése](../hdinsight/spark/apache-spark-jupyter-spark-sql.md)

* [Tekintse át a mintakódokat, a Cassandra API használata](cassandra-spark-generic.md#next-steps)

* [Cqlsh ellenőrzés céljából használja, így igény szerint](cassandra-spark-generic.md##connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Cassandra API-konfigurációt a Spark2** -Cassandra a Spark-összekötő szükséges, hogy a Cassandra-kapcsolatot a részletek a Spark környezet részeként inicializálni. Ha elindítja egy Jupyter notebookot, a spark-munkamenetet, és a környezet már inicializálva vannak, és nem tanácsos leállítása és a Spark környezet újrainicializálása, kivéve, ha ez kész, de minden beállítani, a HDInsight alapértelmezett Jupyter notebook indítási konfiguráció. Egy lehetséges megoldást, hogy a Cassandra példány részleteit hozzá Ambari, közvetlenül a Spark2 szolgáltatás konfigurációja. Ez a Spark2 szolgáltatás újraindítását igénylő fürtönként egy egyszeri tevékenységet.
 
  1. Nyissa meg az Ambari, Spark2 szolgáltatás és a select configs

  2. Ezután nyissa meg az egyéni spark2-alapértelmezett, és adja hozzá az alábbi új tulajdonság, és Spark2 szolgáltatás újraindítása:

  ```scala
  spark.cassandra.connection.host=YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com<br>
  spark.cassandra.connection.port=10350<br>
  spark.cassandra.connection.ssl.enabled=true<br>
  spark.cassandra.auth.username=YOUR_COSMOSDB_ACCOUNT_NAME<br>
  spark.cassandra.auth.password=YOUR_COSMOSDB_KEY<br>
  ```

## <a name="access-azure-cosmos-db-cassandra-api-from-spark-shell"></a>Hozzáférés az Azure Cosmos DB Cassandra API a Spark-shell

Spark-shell tesztelés/feltárás célokat szolgál.

* Indítsa el a spark-shell a szükséges maven függőségekkel a fürtön a Spark-verzióval kompatibilis.

  ```scala
  spark-shell --packages "com.datastax.spark:spark-cassandra-connector_2.11:2.3.0,com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0"
  ```

* Néhány DDL és DML-művelet végrehajtása

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

## <a name="access-azure-cosmos-db-cassandra-api-from-jupyter-notebooks"></a>Hozzáférés az Azure Cosmos DB Cassandra API a Jupyter notebookok

A Spark HDInsight Zeppelin és a Jupyter notebook szolgáltatásokat tartalmaz. Mindkét, amelyek támogatják a Scala- és Python webes notebook környezetben. Notebookok kitűnően alkalmasak interaktív és feltáró jellegű elemzési és együttműködést, de nem jelentette az operatív/productionized folyamatok.

A következő Jupyter notebookok tölthető fel, a HDInsight Spark-fürthöz, és adja meg készen áll a minták használata az Azure Cosmos DB Cassandra API. Ne feledje el áttekinteni az első Jegyzetfüzet `1.0-ReadMe.ipynb` , tekintse át a Spark szolgáltatás konfigurációját az Azure Cosmos DB Cassandra API-hoz való kapcsolódáshoz.

Töltse le ezeket a notebookokat alatt [azure-cosmos-db-cassandra-api-spark-notebooks-jupyter](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-jupyter/blob/master/scala/) a gépre.
  
### <a name="how-to-upload"></a>Hogyan tölthetők fel:
Ha elindítja a Jupyter, navigáljon a Scala. Először hozzon létre egy könyvtárat, és ezután a notebookok feltöltése a könyvtárba. A feltöltés gombot a felső van jobb-oldalon.  

### <a name="how-to-run"></a>Hogyan futtathat:
Egymás után futnak a jegyzetfüzetek és a notebook cellákat.  A Futtatás gombra minden egyes notebook számára, hogy hajtsa végre az összes cellát, vagy a shift + enter billentyűkombinációt az egyes cellák tetején.

## <a name="access-with-azure-cosmos-db-cassandra-api-from-your-spark-scala-program"></a>Hozzáférés az Azure Cosmos DB Cassandra API a Spark Scala-program

Automatizált folyamatok éles környezetben, a Spark-programok elküldi a fürtön keresztül [spark-submit szkripttel](https://spark.apache.org/docs/latest/submitting-applications.html).

## <a name="next-steps"></a>További lépések

* [Hogyan hozhat létre egy Spark Scala program IDE-ben, és küldheti el a HDInsight Spark-fürt livy-n keresztül végrehajtás](../hdinsight/spark/apache-spark-create-standalone-application.md)

* [Hogyan csatlakozhat az Azure Cosmos DB Cassandra API a Spark Scala-program](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)

* [Kódminták a Cassandra API használatának teljes listája](cassandra-spark-generic.md)
