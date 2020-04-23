---
title: 'Oktatóanyag: Apache Spark Streaming & Apache Kafka – Azure HDInsight'
description: Megtudhatja, hogyan használhatja az Apache Spark streamelést az adatok az Apache Kafkába való betöltéséhez, illetve az onnan való exportálásához. Ebben az oktatóanyagban egy Jupyter notebookkal streamelünk adatokat a Spark on HDInsightból.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,seodec18
ms.date: 04/22/2020
ms.openlocfilehash: 5fa25f54faecbc7caf130ffeb0d24c3d8fef7e09
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084804"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Oktatóanyag: Az Apache Spark strukturált stream használata az Apache Kafkával a HDInsighton

Ez az oktatóanyag bemutatja, hogyan használhatja [az Apache Spark strukturált streamelést](https://spark.apache.org/docs/latest/structured-streaming-programming-guide) az Apache [Kafka](./kafka/apache-kafka-introduction.md) használatával az Azure HDInsight szolgáltatásban.

A Spark Structured Streaming egy Spark SQL-re épülő adatfolyam-feldolgozó motor. Lehetővé teszi, hogy ugyanúgy fejezze ki a streamszámításokat, mint a kötegelt számításokat a statikus adatok esetében.  

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Fürtök létrehozása Azure Resource Manager-sablon használatával
> * Spark strukturált streamelésének használata a Kafkával

Ha végzett a jelen dokumentumlépéseivel, ne felejtse el törölni a fürtöket a többletköltségek elkerülése érdekében.

## <a name="prerequisites"></a>Előfeltételek

* jq, egy parancssori JSON processzor.  Lásd: [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).

* A [Jupyter-jegyzetfüzetek](https://jupyter.org/) használata a Spark használatával a HDInsighton. További információ: [Adatok betöltése és lekérdezések futtatása az Apache Spark hdinsight-dokumentumon.](spark/apache-spark-load-data-run-query.md)

* A Scala programozási nyelv ismerete. Az oktatóanyagban használt kód Scala nyelven van megírva.

* A Kafka-témakörök létrehozásának ismerete. További információ: Az [Apache Kafka a HDInsight gyorsindítási](kafka/apache-kafka-get-started.md) dokumentumban.

> [!IMPORTANT]  
> A dokumentum lépéseihez egy olyan Azure-erőforráscsoport szükséges, amely Spark on HDInsight- és Kafka on HDInsight-fürtöt is tartalmaz. Mindkét fürt Azure virtuális hálózatban található, így a Spark-fürt közvetlenül kommunikálhat a Kafka-fürttel.
>
> A kényelmes használat érdekében ez a dokumentum tartalmaz egy hivatkozást egy olyan sablonra, amellyel az összes szükséges Azure-erőforrás létrehozható.
>
> A HDInsight virtuális hálózatban való használatáról további információt a [HDInsight virtuális hálózat ának megtervezése](hdinsight-plan-virtual-network-deployment.md) című dokumentumban talál.

## <a name="structured-streaming-with-apache-kafka"></a>Strukturált streamelés az Apache Kafka segítségével

A Spark strukturált stream egy, a Spark SQL-motorra épülő streamfeldolgozó rendszer. Strukturált streamelés használataesetén a streamelési lekérdezéseket ugyanúgy írhat, mint a kötegelt lekérdezéseket.

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

A streamelési művelet is használ `awaitTermination(30000)`, amely leállítja a stream után 30.000 ms.

A strukturált streamelés a Kafkával való használatához a projektnek függőségi viszonyban kell lennie az `org.apache.spark : spark-sql-kafka-0-10_2.11` csomaggal. A csomag verziójának egyeznie kell a Spark on HDInsight verziójával. A Spark 2.2.0 esetén (a HDInsight 3.6-ban érhető el) [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar)a különböző projekttípusok függőségi információit a következő helyen találja meg: .

Az oktatóanyaghoz használt Jupyter-jegyzetfüzet esetében a következő cella tölti be ezt a csomagfüggőséget:

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

Az Apache Kafka a HDInsight-on nem biztosít hozzáférést a Kafka brókerekhez a nyilvános interneten keresztül. A Kafkát használó minden eszköznek ugyanabban az Azure virtuális hálózatban kell lennie. Ebben az oktatóanyagban a Kafka- és a Spark-fürtök is ugyanabban az Azure virtuális hálózatban szerepelnek.

Az alábbi ábra a Spark és a Kafka közötti kommunikáció áramlását mutatja be.

![Azure virtuális hálózatban lévő Spark- és Kafka-fürtök ábrája](./media/hdinsight-apache-kafka-spark-structured-streaming/apache-spark-kafka-vnet.png)

> [!NOTE]  
> A Kafka szolgáltatás a virtuális hálózaton belüli kommunikációra van korlátozva. A fürtön lévő többi szolgáltatás, például az SSH és az Ambari az interneten keresztül is elérhető. További információ a HDInsighttal elérhető nyilvános portokról: [A HDInsight által használt portok és URI-k](hdinsight-hadoop-port-settings-for-services.md).

Azure-beli virtuális hálózat, majd az abban lévő Kafka- és Spark-fürtök létrehozásához hajtsa végre a következő lépéseket:

1. Az alábbi gombbal jelentkezzen be az Azure szolgáltatásba, és nyissa meg a sablont az Azure Portalon.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-kafka-spark-structured-streaming/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Az Azure Resource Manager **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**sablon a helyen található.

    Ez a sablon a következő erőforrásokat hozza létre:

   * Egy Kafka on HDInsight 3.6-fürt.
   * Egy Spark 2.2.0 on HDInsight 3.6-fürt.
   * Egy Azure virtuális hálózat, amely tartalmazza a HDInsight-fürtöket.

     > [!IMPORTANT]  
     > Az oktatóanyagban alkalmazott strukturált stream használatához a Spark 2.2.0 on HDInsight 3.6 szükséges. Ha a Spark on HDInsight korábbi verzióját használja, hibák lépnek fel a notebook használatakor.

2. A következő információkkal töltheti ki a **Testreszabott sablon** szakaszban lévő bejegyzéseket:

    | Beállítás | Érték |
    | --- | --- |
    | Előfizetés | Az Azure-előfizetése |
    | Erőforráscsoport | Az erőforrásokat tartalmazó erőforráscsoport. |
    | Hely | Az az Azure-régió, amelyben az erőforrások létrejönnek. |
    | Spark-fürt neve | A Spark-fürt neve. Az első hat karakternek a Kafka-fürt nevétől eltérőnek kell lennie. |
    | Kafka-fürt neve | A Kafka-fürt neve. Az első hat karakternek a Spark-fürt nevétől eltérőnek kell lennie. |
    | Fürt bejelentkezési felhasználóneve | A fürtök rendszergazdai felhasználóneve. |
    | Fürt bejelentkezési jelszava | A fürtök rendszergazdai felhasználójának jelszava. |
    | SSH-felhasználónév | A fürtökhöz létrehozandó SSH-felhasználó. |
    | SSH-jelszó | Az SSH-felhasználó jelszava. |

    ![A testreszabott sablon képernyőképe](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Olvassa el az **Általános Szerződési Feltételeket**, majd válassza **az Elfogadom a fent meghatározott feltételeket**.

4. Válassza a **Beszerzés** lehetőséget.

> [!NOTE]  
> A fürtök létrehozása 20 percig is eltarthat.

## <a name="use-spark-structured-streaming"></a>A Spark strukturált streamelésének használata

Ez a példa bemutatja, hogyan használhatja a Spark strukturált streamelés a Kafka a HDInsight. Ez használ adatokat taxi utak, amely biztosítja a New York City.  A notebook által használt adatkészlet a [2016-os Green Taxi Trip Data -tól származik.](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb)

1. Gyűjtse össze a gazdatestet. Használja a curl és [jq](https://stedolan.github.io/jq/) parancsokat alább megszerezni a Kafka ZooKeeper és bróker házigazdák információkat. A parancsok windowsos parancssorba vannak tervezve, más környezetekben kisebb eltérésekre lesz szükség. Cserélje `KafkaCluster` le a Kafka-fürt `KafkaPassword` nevét és a fürt bejelentkezési jelszavát. Is, `C:\HDI\jq-win64.exe` cserélje ki a tényleges elérési utat a jq telepítés. Írja be a parancsokat a Windows parancssorába, és mentse a kimenetet későbbi lépésekben való használatra.

    ```cmd
    REM Enter cluster name in lowercase

    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

1. Egy webböngészőből keresse `https://CLUSTERNAME.azurehdinsight.net/jupyter`meg `CLUSTERNAME` a , ahol a fürt neve. Amikor a rendszer kéri, írja be a fürt létrehozásakor használt bejelentkezési (rendszergazdai) nevet és jelszót.

1. Jegyzetfüzet létrehozásához válassza **az Új > a Spark** lehetőséget.

1. A Spark streamelése mikrokötegelést, ami azt jelenti, hogy az adatok kötegek és végrehajtók az adatkötegeken futnak. Ha a végrehajtó a köteg feldolgozásához szükséges időnél kevesebb tétlen időtúltöltéssel rendelkezik, akkor a végrehajtók folyamatosan hozzáadódnak és törlődnek. Ha a végrehajtók tétlen időtúltöltése nagyobb, mint a köteg időtartama, a végrehajtó soha nem lesz eltávolítva. Ezért **azt javasoljuk, hogy tiltsa le a dinamikus lefoglalás beállításával spark.dynamicAllocation.enabled hamis, amikor a streamelési alkalmazások futtatásakor.**

    A Jegyzetfüzet által használt csomagok betöltése a következő adatok nak a Jegyzetfüzet cellába való beírásával. Futtassa a parancsot a **CTRL + ENTER**billentyűkombinációval.

    ```configuration
    %%configure -f
    {
        "conf": {
            "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
            "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11",
            "spark.dynamicAllocation.enabled": false
        }
    }
    ```

1. Hozza létre a Kafka témakört. Az alábbi parancs szerkesztése az első lépésben kinyert Zookeeper gazdagép adatainak cseréjével. `YOUR_ZOOKEEPER_HOSTS` A témakör létrehozásához írja be a szerkesztett parancsot a `tripdata` Jupyter-jegyzetfüzetbe.

    ```scala
    %%bash
    export KafkaZookeepers="YOUR_ZOOKEEPER_HOSTS"

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic tripdata --zookeeper $KafkaZookeepers
    ```

1. A taxiutak adatainak lekérése. Írja be a parancsot a következő cellába a New York-i taxiutak adatainak betöltéséhez. Az adatok betöltődnek egy adatkeretbe, majd az adatkeret cellakimenetként jelenik meg.

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

1. Állítsa be a Kafka bróker házigazdák információkat. Cserélje `YOUR_KAFKA_BROKER_HOSTS` ki a bróker házigazdák információkat kivont lépésben 1.  Írja be a szerkesztett parancsot a következő Jupyter Notebook cellába.

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"

    println("Finished setting Kafka broker and topic configuration.")
    ```

1. Küldd el az adatokat Kafkának. A következő parancsban `vendorid` a mező a Kafka üzenet kulcsértéke. A kulcsot a Kafka használja az adatok particionálásakor. Az összes mező json karakterláncként tárolódik a Kafka üzenetben. Írja be a következő parancsot a Jupyter ben, hogy az adatokat kötegelt lekérdezéssel mentse a Kafka programba.

    ```scala
    // Select the vendorid as the key and save the JSON string as the value.
    val query = taxiDF.selectExpr("CAST(vendorid AS STRING) as key", "to_JSON(struct(*)) AS value").write.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("topic", kafkaTopic).save()

    println("Data sent to Kafka")
    ```

1. Séma deklarálása. A következő parancs bemutatja, hogyan kell használni a sémát json adatok olvasásakor kafka. Írja be a parancsot a következő Jupyter cellába.

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

1. Jelölje ki az adatokat, és indítsa el az adatfolyamot. A következő parancs bemutatja, hogyan lehet adatokat beolvasni a Kafkából kötegelt lekérdezéssel. És ezután írja ki az eredményeket a HdFS a Spark-fürtön. Ebben a példában a `select` kafka üzenet (értékmező) lekéri az üzenetet, és alkalmazza a sémát. Az adatokat ezután a HDFS (WASB vagy ADL) programba írják parketta formátumban. Írja be a parancsot a következő Jupyter cellába.

    ```scala
    // Read a batch from Kafka
    val kafkaDF = spark.read.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data and write to file
    val query = kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip").write.format("parquet").option("path","/example/batchtripdata").option("checkpointLocation", "/batchcheckpoint").save()

    println("Wrote data to file")
    ```

1. A fájlok létrehozásának ellenőrzését a következő Jupyter-cellába való beírással ellenőrizheti. Felsorolja a fájlokat `/example/batchtripdata` a könyvtárban.

    ```scala
    %%bash
    hdfs dfs -ls /example/batchtripdata
    ```

1. Míg az előző példa kötegelt lekérdezést használt, a következő parancs bemutatja, hogyan kell ugyanezt csinálni egy streamelési lekérdezés használatával. Írja be a parancsot a következő Jupyter cellába.

    ```scala
    // Stream from Kafka
    val kafkaStreamDF = spark.readStream.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data from the stream and write to file
    kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip").writeStream.format("parquet").option("path","/example/streamingtripdata").option("checkpointLocation", "/streamcheckpoint").start.awaitTermination(30000)
    println("Wrote data to file")
    ```

1. Futtassa a következő cellát annak ellenőrzéséhez, hogy a fájlokat a streamelési lekérdezés írta-e.

    ```scala
    %%bash
    hdfs dfs -ls /example/streamingtripdata
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni szeretné a jelen oktatóanyag által létrehozott erőforrásokat, akkor törölje az erőforráscsoportot. Az erőforráscsoport törlése a társított HDInsight-fürtis törlődik. És az erőforráscsoporthoz társított egyéb erőforrások.

Az erőforráscsoport eltávolítása az Azure Portallal:

1. Az [Azure Portalon](https://portal.azure.com/)bontsa ki a bal oldali menüt a szolgáltatások menüjének megnyitásához, majd válassza az __Erőforráscsoportok__ lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán lévő __Továbbiak__ gombra (...).
3. Válassza az __Erőforráscsoport törlése__ elemet, és erősítse meg a választását.

> [!WARNING]  
> A HDInsight-fürt számlázása a fürt létrehozásakor kezdődik és a fürt törlésekor fejeződik be. A számlázás percalapú, ezért mindig érdemes törölni a fürtöt, ha az már nincs használatban.
>
> A Kafka on HDInsight-fürt törlése a Kafkában tárolt összes adatot is törli.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja az Apache Spark strukturált streamelését. Adatok írása és olvasása az Apache Kafka-ból a HDInsight-on. Az alábbi linken megtudhatja, hogyan használhatja az Apache Stormot a Kafkával.

> [!div class="nextstepaction"]
> [Apache Storm használata az Apache Kafka segítségével](hdinsight-apache-storm-with-kafka.md)
