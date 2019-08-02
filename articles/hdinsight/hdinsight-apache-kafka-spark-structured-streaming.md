---
title: 'Oktatóanyag: Strukturált streaming Apache Spark Apache Kafka-Azure HDInsight'
description: Megtudhatja, hogyan használhatja az Apache Spark streamelést az adatok az Apache Kafkába való betöltéséhez, illetve az onnan való exportálásához. Ebben az oktatóanyagban egy Jupyter notebookkal streamelünk adatokat a Spark on HDInsightból.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seodec18
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: hrasheed
ms.openlocfilehash: da31b6a880344de918a3b3e0f89f60d985db2ce7
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68406022"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Oktatóanyag: Apache Spark strukturált streaming használata a HDInsight Apache Kafka használatával

Ez az oktatóanyag azt mutatja be, hogyan használhatók az [Apache Spark strukturált streamek](https://spark.apache.org/docs/latest/structured-streaming-programming-guide) az Azure HDInsight lévő [Apache Kafkaekkel](https://kafka.apache.org/) való adatolvasásra és-írásra.

A Spark strukturált streaming egy Spark SQL-re épülő stream-feldolgozó motor. Lehetővé teszi, hogy ugyanúgy fejezze ki a streamszámításokat, mint a kötegelt számításokat a statikus adatok esetében.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Fürtök létrehozása Azure Resource Manager sablon használatával
> * Spark strukturált streaming használata a Kafka használatával

Amikor végzett a dokumentum lépéseivel, ne felejtse el törölni a fürtöket a további díjak elkerülése érdekében.

## <a name="prerequisites"></a>Előfeltételek

* jQ, parancssori JSON-processzor.  Lásd [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/):.

* A [Jupyter notebookok](https://jupyter.org/) és a Spark on HDInsight használatával való ismerete. További információ: [adatok betöltése és lekérdezések futtatása Apache Spark HDInsight-](spark/apache-spark-load-data-run-query.md) dokumentumon.

* A [Scala](https://www.scala-lang.org/) programozási nyelv ismerete. Az oktatóanyagban használt kód Scala nyelven van megírva.

* A Kafka-témakörök létrehozásának ismerete. További információ: [Apache Kafka on HDInsight](kafka/apache-kafka-get-started.md) rövid útmutató dokumentum.

> [!IMPORTANT]  
> A dokumentum lépéseihez egy olyan Azure-erőforráscsoport szükséges, amely Spark on HDInsight- és Kafka on HDInsight-fürtöt is tartalmaz. Mindkét fürt Azure virtuális hálózatban található, így a Spark-fürt közvetlenül kommunikálhat a Kafka-fürttel.
> 
> A kényelmes használat érdekében ez a dokumentum tartalmaz egy hivatkozást egy olyan sablonra, amellyel az összes szükséges Azure-erőforrás létrehozható. 
>
> A virtuális hálózat HDInsight használatával kapcsolatos további információkért lásd a [virtuális hálózat megtervezése HDInsight](hdinsight-plan-virtual-network-deployment.md) dokumentumhoz című témakört.

## <a name="structured-streaming-with-apache-kafka"></a>Strukturált streaming Apache Kafka

A Spark strukturált stream egy, a Spark SQL-motorra épülő streamfeldolgozó rendszer. A strukturált stream használatával ugyanúgy írhat streamelési lekérdezéseket, mint a kötegelt lekérdezések esetében.

Az alábbi kódrészletek az adatok Kafkából való beolvasását és fájlban való tárolását mutatják be. Az első egy köteg-, míg a második egy streamelési művelet:

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

Mindkét kódrészlet a Kafkából olvassa be az adatokat, majd fájlba írja azokat. A két példa közötti különbségek:

| Batch | Streamelés |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

Az adatfolyam-továbbítási `awaitTermination(30000)`művelet a szolgáltatást is használja, amely leállítja az adatfolyamot a 30 000 MS után. 

A strukturált streamelés a Kafkával való használatához a projektnek függőségi viszonyban kell lennie az `org.apache.spark : spark-sql-kafka-0-10_2.11` csomaggal. A csomag verziójának egyeznie kell a Spark on HDInsight verziójával. A Spark 2.2.0 (ez a HDInsight 3.6 verzióján érhető el) esetében a különböző projekttípusokra vonatkozó függőségek adatait a következő helyen találja: [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar).

Az oktatóanyaghoz használt Jupyter Notebook a következő cella tölti be a csomag függőségét:

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>A fürtök létrehozása

Az Apache Kafka on HDInsight nem nyújt hozzáférést a Kafka-közvetítőkhöz a nyilvános interneten keresztül. A Kafkát használó minden eszköznek ugyanabban az Azure virtuális hálózatban kell lennie. Ebben az oktatóanyagban a Kafka- és a Spark-fürtök is ugyanabban az Azure virtuális hálózatban szerepelnek. 

Az alábbi ábra a Spark és a Kafka közötti kommunikáció áramlását mutatja be.

![Azure virtuális hálózatban lévő Spark- és Kafka-fürtök ábrája](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> A Kafka szolgáltatás a virtuális hálózaton belüli kommunikációra van korlátozva. A fürtön lévő többi szolgáltatás, például az SSH és az Ambari az interneten keresztül is elérhető. További információ a HDInsighttal elérhető nyilvános portokról: [A HDInsight által használt portok és URI-k](hdinsight-hadoop-port-settings-for-services.md).

Azure-beli virtuális hálózat, majd az abban lévő Kafka- és Spark-fürtök létrehozásához hajtsa végre a következő lépéseket:

1. Az alábbi gombbal jelentkezzen be az Azure szolgáltatásba, és nyissa meg a sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>

    Az Azure Resource Manager-sablon a következő helyen található: **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json** .

    Ez a sablon a következő erőforrásokat hozza létre:

   * Egy Kafka on HDInsight 3.6-fürt.
   * Egy Spark 2.2.0 on HDInsight 3.6-fürt.
   * Egy Azure virtuális hálózat, amely tartalmazza a HDInsight-fürtöket.

     > [!IMPORTANT]  
     > Az oktatóanyagban alkalmazott strukturált stream használatához a Spark 2.2.0 on HDInsight 3.6 szükséges. Ha a Spark on HDInsight korábbi verzióját használja, hibák lépnek fel a notebook használatakor.

2. A következő információkkal töltheti ki a **Testreszabott sablon** szakaszban lévő bejegyzéseket:

    | Beállítás | Érték |
    | --- | --- |
    | Subscription | Az Azure-előfizetése |
    | Resource group | Az erőforrásokat tartalmazó erőforráscsoport. |
    | Location | Az az Azure-régió, amelyben az erőforrások létrejönnek. |
    | Spark-fürt neve | A Spark-fürt neve. Az első hat karakternek a Kafka-fürt nevétől eltérőnek kell lennie. |
    | Kafka-fürt neve | A Kafka-fürt neve. Az első hat karakternek a Spark-fürt nevétől eltérőnek kell lennie. |
    | Fürt bejelentkezési felhasználóneve | A fürtök rendszergazdai felhasználóneve. |
    | Fürt bejelentkezési jelszava | A fürtök rendszergazdai felhasználójának jelszava. |
    | SSH-felhasználónév | A fürtökhöz létrehozandó SSH-felhasználó. |
    | SSH-jelszó | Az SSH-felhasználó jelszava. |
   
    ![A testreszabott sablon képernyőképe](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Olvassa át a **használati feltételeket**, majd válassza az **Elfogadom a fenti feltételeket és kikötéseket** lehetőséget.

4. Végül jelölje be a **Rögzítés az irányítópulton** elemet, majd válassza a **Vásárlás** lehetőséget. 

> [!NOTE]  
> A fürtök létrehozása 20 percig is eltarthat.

## <a name="use-spark-structured-streaming"></a>Spark strukturált streaming használata

Ez a példa bemutatja, hogyan használható a Spark strukturált Stream és a Kafka on HDInsight. A szolgáltatás a New York City által biztosított taxis utakon tárolt információk alapján működik.  A jegyzetfüzet által használt adatkészletből [2016 zöld taxis adat](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb)található.

1. A gazdagép adatainak összegyűjtése. A következő curl-és [jQ](https://stedolan.github.io/jq/) -parancsok használatával szerezheti be a Kafka-ZooKeeper és a közvetítő gazdagépeit. A parancsok Windows-parancssorhoz készültek, és más környezetekben kisebb eltérésekre lesz szükség. Cserélje `KafkaCluster` le a nevet a Kafka-fürt nevére, `KafkaPassword` és a fürt bejelentkezési jelszavára. Továbbá cserélje le `C:\HDI\jq-win64.exe` a-t a jQ-telepítés tényleges elérési útjára. Adja meg a parancsokat egy Windows-parancssorban, és mentse a kimenetet a későbbi lépésekben való használatra.

    ```cmd
    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword
    
    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"
    
    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

2. A webböngészőben csatlakozzon a Spark-fürtön lévő Jupyter notebookhoz. A következő URL-címben cserélje le a `CLUSTERNAME` elemet a __Spark__-fürt nevére.

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Amikor a rendszer kéri, írja be a fürt létrehozásakor használt bejelentkezési (rendszergazdai) nevet és jelszót.

3. Jegyzetfüzet létrehozásához válassza az **új > Spark** elemet.

4. A notebook által használt csomagok betöltéséhez írja be az alábbi adatokat egy jegyzetfüzet-cellába. Futtassa a parancsot a **CTRL + ENTER billentyűkombinációval**.

    ```
    %%configure -f
    {
        "conf": {
            "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
            "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
        }
    }
    ```

5. Hozzon létre egy Kafka-témakört. Szerkessze az alábbi parancsot úgy `YOUR_ZOOKEEPER_HOSTS` , hogy az első lépésben kibontott Zookeeper-gazdagépi adatokat cseréli le. A `tripdata` témakör létrehozásához adja meg az Jupyter notebook szerkesztett parancsát.

    ```scala
    %%bash
    export KafkaZookeepers="YOUR_ZOOKEEPER_HOSTS"

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic tripdata --zookeeper $KafkaZookeepers
    ```

6. A taxis utakon tárolt adatkérések. Adja meg a parancsot a következő cellában a New York-i taxis utakon tárolt adattöltéshez. A rendszer betölti az adatokat egy dataframe, majd a dataframe megjeleníti a cella kimenetét.

    ```scala
    import spark.implicits._

    // Load the data from the New York City Taxi data REST API for 2016 Green Taxi Trip Data
    val url="https://data.cityofnewyork.us/resource/pqfs-mqru.json"
    val result = scala.io.Source.fromURL(url).mkString
    
    // Create a dataframe from the JSON data
    val taxiDF = spark.read.json(Seq(result).toDS)
    
    // Display the dataframe containing trip data
    taxiDF.show()
    ```

7. Adja meg a Kafka-közvetítő gazdagépek adatait. Cserélje `YOUR_KAFKA_BROKER_HOSTS` le a és a közvetítő gazdagépek adatait az 1. lépésben kinyert adatokra.  Adja meg a szerkesztett parancsot a következő Jupyter Notebook cellában.

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"
    
    println("Finished setting Kafka broker and topic configuration.")
    ```

8. Küldje el az adatgyűjtést a Kafka-nek. A következő parancsban a program `vendorid` a Kafka-üzenet kulcs értékeként használja a mezőt. A kulcsot a Kafka az adatparticionáláskor használja. Az összes mezőt a Kafka-üzenetben egy JSON-karakterlánc értékként tárolja a rendszer. Adja meg a következő parancsot a Jupyter-ben, hogy egy batch-lekérdezéssel mentse az adataikat a Kafka-ba.

    ```scala
    // Select the vendorid as the key and save the JSON string as the value.
    val query = taxiDF.selectExpr("CAST(vendorid AS STRING) as key", "to_JSON(struct(*)) AS value").write.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("topic", kafkaTopic).save()

    println("Data sent to Kafka")
    ```

9. Séma deklarálása. A következő parancs bemutatja, hogyan használhat sémát JSON-adatok a Kafka-ből való olvasásakor. Adja meg a parancsot a következő Jupyter-cellában.

    ```scala
    // Import bits useed for declaring schemas and working with JSON data
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Define a schema for the data
    val schema = (new StructType).add("dropoff_latitude", StringType).add("dropoff_longitude", StringType).add("extra", StringType).add("fare_amount", StringType).add("improvement_surcharge", StringType).add("lpep_dropoff_datetime", StringType).add("lpep_pickup_datetime", StringType).add("mta_tax", StringType).add("passenger_count", StringType).add("payment_type", StringType).add("pickup_latitude", StringType).add("pickup_longitude", StringType).add("ratecodeid", StringType).add("store_and_fwd_flag", StringType).add("tip_amount", StringType).add("tolls_amount", StringType).add("total_amount", StringType).add("trip_distance", StringType).add("trip_type", StringType).add("vendorid", StringType)
    // Reproduced here for readability
    //val schema = (new StructType)
    //   .add("dropoff_latitude", StringType)
    //   .add("dropoff_longitude", StringType)
    //   .add("extra", StringType)
    //   .add("fare_amount", StringType)
    //   .add("improvement_surcharge", StringType)
    //   .add("lpep_dropoff_datetime", StringType)
    //   .add("lpep_pickup_datetime", StringType)
    //   .add("mta_tax", StringType)
    //   .add("passenger_count", StringType)
    //   .add("payment_type", StringType)
    //   .add("pickup_latitude", StringType)
    //   .add("pickup_longitude", StringType)
    //   .add("ratecodeid", StringType)
    //   .add("store_and_fwd_flag", StringType)
    //   .add("tip_amount", StringType)
    //   .add("tolls_amount", StringType)
    //   .add("total_amount", StringType)
    //   .add("trip_distance", StringType)
    //   .add("trip_type", StringType)
    //   .add("vendorid", StringType)
    
    println("Schema declared")
    ```

10. Válassza az adatforrást, és indítsa el az adatfolyamot. A következő parancs azt mutatja be, hogyan lehet lekérdezni a Kafka adatait batch-lekérdezés használatával, majd az eredményeket a Spark-fürtön lévő HDFS írni. Ebben a példában a `select` lekéri az üzenet (érték) mezőt a Kafka-ből, és a sémát alkalmazza rá. Ezután a rendszer a HDFS (WASB vagy ADL) írja a parketta formátumát. Adja meg a parancsot a következő Jupyter-cellában.

    ```scala
    // Read a batch from Kafka
    val kafkaDF = spark.read.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()
    
    // Select data and write to file
    val query = kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip").write.format("parquet").option("path","/example/batchtripdata").option("checkpointLocation", "/batchcheckpoint").save()
    
    println("Wrote data to file")
    ```

11. A következő Jupyter-cellában található parancs megadásával ellenőrizheti, hogy a fájlok létrejöttek-e. Felsorolja a `/example/batchtripdata` könyvtárban található fájlokat.

    ```scala
    %%bash
    hdfs dfs -ls /example/batchtripdata
    ```

12. Az előző példában egy batch-lekérdezést használtunk, az alábbi parancs azt mutatja be, hogyan végezheti el ugyanezt egy adatfolyam-lekérdezés használatával. Adja meg a parancsot a következő Jupyter-cellában.

    ```scala
    // Stream from Kafka
    val kafkaStreamDF = spark.readStream.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()
    
    // Select data from the stream and write to file
    kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip").writeStream.format("parquet").option("path","/example/streamingtripdata").option("checkpointLocation", "/streamcheckpoint").start.awaitTermination(30000)
    println("Wrote data to file")
    ```

13. Futtassa a következő cellát annak ellenőrzéséhez, hogy a fájlokat az adatfolyam-lekérdezés írta-e be.

    ```scala
    %%bash
    hdfs dfs -ls /example/streamingtripdata
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a jelen oktatóanyag által létrehozott erőforrásokat, akkor törölje az erőforráscsoportot. Az erőforráscsoport törlésekor a kapcsolódó HDInsight-fürt, valamint az esetlegesen az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

Az erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az __Erőforráscsoportok__ lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő __Továbbiak__ gombra (...).
3. Válassza az __Erőforráscsoport törlése__ elemet, és erősítse meg a választását.

> [!WARNING]  
> A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban.
> 
> A Kafka on HDInsight-fürt törlése a Kafkában tárolt összes adatot is törli.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja [Apache Spark strukturált adatfolyamot](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) a HDInsight-on található [Apache Kafka](https://kafka.apache.org/) adatok írásához és olvasásához. A következő hivatkozásra kattintva megtudhatja, hogyan [](https://storm.apache.org/) használhatja a Apache Stormt a Kafka használatával.

> [!div class="nextstepaction"]
> [Apache Storm használata a Apache Kafka](hdinsight-apache-storm-with-kafka.md)
