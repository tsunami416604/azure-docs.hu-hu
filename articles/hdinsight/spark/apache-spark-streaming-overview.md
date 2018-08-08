---
title: Spark Streamelés az Azure HDInsight a
description: Spark Streaming alkalmazások használata a HDInsight Spark-fürtön.
services: hdinsight
ms.service: hdinsight
author: maxluk
ms.author: maxluk
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/05/2018
ms.openlocfilehash: b3420737147f9ee67d5d2d021c28a98d34e209df
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617615"
---
# <a name="overview-of-spark-streaming"></a>A Spark Stream áttekintése

Spark Streaming nyújt adatfolyam-feldolgozás HDInsight Spark fürtök garantálja, hogy bármely bemeneti esemény feldolgozása pontosan egyszer, még akkor is, ha a csomóponton hiba történik. A Spark Stream egy hosszan futó feladatot, amely megkapja a bemeneti adatok számos forrásból, például az Azure Event Hubs, az Azure IoT Hub, a Kafka, Flume, Twitter, ZeroMQ, nyers TCP-szoftvercsatornák, vagy a figyelés HDFS feladatszinten csatlakoztathatók. Ellentétben a kizárólag eseményvezérelt folyamat egy Spark Stream kötegeli az bemeneti adatok az idő a windows, mint 2 másodperc szelet, és ezután alakítja át az egyes batch-leképezéssel adatok, csökkentheti, csatlakozzon, és bontsa ki az operations. A Spark Stream majd ír az átalakított adatok feladatszinten csatlakoztathatók, adatbázisok, az irányítópultok és a konzol.

![A Stream feldolgozása a HDInsight és a Spark Streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark Streamelési alkalmazásokat kell várnia, gyűjtéséhez mindegyik másodperc tört *irányuló mikrokötegelt* események feldolgozásához a batch küldése előtt. Ezzel szemben az eseményvezérelt alkalmazások dolgozza fel minden esemény azonnal. Spark Streaming késés általában néhány másodperc alatt áll. A irányuló mikrokötegelt megközelítés előnyeit adatfeldolgozási hatékonyabb és egyszerűbb összesített számítások.

## <a name="introducing-the-dstream"></a>A DStream bemutatása

Spark Streaming jelöli a beérkező adatokat az folyama egy *diszkretizált stream* egy DStream nevezik. Egy DStream bemeneti forrásból, például az Event Hubs vagy a Kafka, vagy egy másik DStream az átalakítások alkalmazásával hozható létre.

Egy DStream felett a nyers eseményadatok absztrakciós réteget biztosít. 

Kezdje egy egyszeri esemény, mondja ki a hőmérséklet egy csatlakoztatott thermostat olvasásakor. Amikor ezt az eseményt a Spark Streaming kérelem érkezik, az esemény megbízhatóan, több csomóponton replikálja, tárolódik. A hibatűrés biztosítja, hogy a hiba bármely egyetlen csomópont nem a elvesztését eredményezi az esemény. A Spark mag olyan adatszerkezet, amely elosztja a fürt több csomópontján az adatokat használ, amelyben minden egyes csomópont általában végzi a saját adatokat a memóriában a legjobb teljesítmény érdekében. Ezt az adatstruktúrát nevezzük egy *rugalmas elosztott adatkészlet* (RDD).

Minden egyes RDD jelöli nevű felhasználó által megadott időtartamon keresztül gyűjtött eseményeket a *kötegelési időkö*. Mivel minden egyes batch időtartam, egy új RDD időköztől számított, hogy az adatokat tartalmazó jön létre. Az rdd-kként folyamatos készletét gyűjti be egy DStream. Például ha a batch időköz hosszú egy második, a DStream bocsát ki a batch minden második tartalmazó egy RDD, amely során a második betöltött összes adatot tartalmazza. A DStream feldolgozásakor a hőmérséklet-esemény jelenik meg, ezek kötegek egyikében. Egy Spark Streaming-alkalmazás az eseményeket tartalmazó köteg feldolgozza, és végső soron az egyes RDD-ban tárolt adatokkal kapcsolatos funkcionál.

![Példa DStream hőmérséklet-események ](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Egy Spark Streaming-alkalmazás felépítése

A Spark Streaming alkalmazás-e egy hosszú ideig futó alkalmazás, amely betöltés forrásokból származó adatokat fogad, átalakításokat is az adatok feldolgozására vonatkozik, és majd leküldi az adatok egy vagy több célhelyre. A Spark Streaming-alkalmazás struktúra rendelkezik egy statikus és dinamikus részét. A statikus rész határozza meg, ha az adatok származnak, milyen feldolgozást az adatok, és ha kell, végezze az eredményeket. A dinamikus részt az alkalmazás határozatlan ideig vár a stopjelzést fut.

Például a következő egyszerű alkalmazás szövegsor TCP szoftvercsatorna keresztül kap, és minden szó jelenik meg, hányszor száma.

### <a name="define-the-application"></a>Az alkalmazás meghatározása

A logikai alkalmazás definíciójának négy lépésből áll:

1. Hozzon létre egy StreamingContext.
2. Hozzon létre egy DStream a StreamingContext.
3. Átalakítások DStream a alkalmazni.
4. Az eredményeket.

Ez a definíció statikus, és nincs adatot dolgoz fel, amíg az alkalmazás futtatásához.

#### <a name="create-a-streamingcontext"></a>Hozzon létre egy StreamingContext

Hozzon létre egy StreamingContext a sparkcontext elemet, amely a fürt mutat. Egy StreamingContext létrehozásakor megadhatja a Köteg mérete (másodpercben), például:

    val ssc = new StreamingContext(spark, Seconds(1))

#### <a name="create-a-dstream"></a>Hozzon létre egy DStream

A StreamingContext példánnyal hozzon létre egy bemeneti DStream a bemeneti forráshoz. Ebben az esetben az alkalmazás számára új fájlokat a csatlakoztatott a HDInsight-fürt alapértelmezett tárolója megjelenésének állapotfigyelőszolgáltatás.

    val lines = ssc.textFileStream("/uploads/2017/01/")

#### <a name="apply-transformations"></a>Átalakítások

Meg, hogy a feldolgozás alkalmazásának a DStream átalakításokat. Ezt az alkalmazást egyszerre több sornyi szöveg fogad a fájlt, az egyes sorok felosztja a szavak és map-reduce minta használatával minden egyes szó jelenik meg, hányszor száma.

    val words = lines.flatMap(_.split(" "))
    val pairs = words.map(word => (word, 1))
    val wordCounts = pairs.reduceByKey(_ + _)

#### <a name="output-results"></a>Kimeneti eredményei

Az átalakítási eredmények leküldése a cél rendszerek kimeneti műveletek alkalmazásával. Ebben az esetben a konzol kimenete az eredmény az egyes futtatások keresztül a számítási van nyomtatva.

    wordCounts.print()

### <a name="run-the-application"></a>Az alkalmazás futtatása

Indítsa el az adatfolyam-továbbítási alkalmazást, és futtassa addig, amíg egy lezáró jel érkezik.

    ssc.start()            
    ssc.awaitTermination()

További információ az Eseményforrások, átalakítás és kimeneti műveletek támogatja, a Spark Stream API: [Spark Streaming Programming Guide](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

A következő mintaalkalmazás az önálló, ezért is futtathatja azt egy [Jupyter Notebook](apache-spark-jupyter-notebook-kernels.md). Ebben a példában egy utánzatként funkcionáló adatforrás az osztály, amely egy számláló és az aktuális idő (MS) értékét öt másodpercenként DummySource hoz létre. Egy új StreamingContext objektumnak egy kötegelési időkö pedig 30 másodperc. Minden alkalommal, amikor egy naplólapot hoznak létre, a streamelési megvizsgálja az RDD előállított, az RDD alakítja át a Spark DataFrame-alkalmazások keresztül az adathalmaz egy ideiglenes táblát hoz létre.

    class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

        /** Start the thread that simulates receiving data */
        def onStart() {
            new Thread("Dummy Source") { override def run() { receive() } }.start()
        }

        def onStop() {  }

        /** Periodically generate a random number from 0 to 9, and the timestamp */
        private def receive() {
            var counter = 0  
            while(!isStopped()) {
                store(Iterator((counter, System.currentTimeMillis)))
                counter += 1
                Thread.sleep(5000)
            }
        }
    }

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process RDDs in the batch
    stream.foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
            .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()

Ezután lekérdezheti rendszeres időközönként, tekintse meg az aktuális készletét a batch az értékek az adathalmaz például az az SQL-lekérdezést:

    %%sql
    SELECT * FROM demo_numbers

A kimenet az alábbihoz hasonló:

| érték | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Hat értékek vannak, mivel a DummySource létrehoz egy értéket 5 másodpercenként, és az alkalmazás bocsát ki egy köteget Ez lehet 30 másodperc.

## <a name="sliding-windows"></a>Késleltetett windows

Összesített számításokat végezni a DStream bizonyos idő alatt, így például ha az átlaghőmérséklet lekérése az utolsó két másodpercen keresztül használhatja a *csúszóablakon* Spark Streaming részét képező műveleteket. Egy csúszóablakban időtartama van (az időszak hossza), és az időközt, amely során az ablak tartalmát is értékeli ki (a dia időköz).

Késleltetett windows átfedésben is vannak, például meghatározhat egy ablak, amely másodpercenként több diák két másodperc hosszúságú. Ez azt jelenti, hogy minden alkalommal, amikor az összesítés számítási hajt végre, az ablak tartalmazza az utolsó egy másodperc az előző időszak adatait, valamint a következő egy második új adatokat.

![Példa kezdeti ablak hőmérséklet-események](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Hőmérséklet-események után késleltetett példa ablak](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

Az alábbi példa frissíti a kódot, amely használja a DummySource gyűjtése a köteg egy-egy egy perces időtartamot és a egy egy perces dia ablakot.

    // A batch is created every 30 seconds
    val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

    // Set the active SQLContext so that we can access it statically within the foreachRDD
    org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

    // Create the stream
    val stream = ssc.receiverStream(new DummySource())

    // Process batches in 1 minute windows
    stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

        // Access the SQLContext and create a table called demo_numbers we can query
        val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
        _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
    } 

    // Start the stream processing
    ssc.start()

Az első perc után nincsenek 12 – az egyes ablakban gyűjtött két köteg hat bejegyzéseket.

| érték | time |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

A Spark Streamelési API-ban elérhető mozgó ablakfüggvények ablakban, countByWindow, reduceByWindow és countByValueAndWindow tartalmazza. Ezek a függvények a részletekért lásd: [DStreams átalakítások](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Ellenőrzőpontok használata

Rugalmasság és a hibatűrés biztosításához, a Spark Streaming szolgáltatás ellenőrzőpontok használata, győződjön meg arról, hogy adatfolyam-feldolgozás továbbra is megszakítás nélkül, csomóponthibák esetén is támaszkodik. A HDInsight Spark ellenőrzőpontok tartós tárolási, (Azure Storage vagy a Data Lake Store) hoz létre. Ezek az ellenőrzőpontok a streamelési alkalmazásadatokat, például a konfigurációt, az alkalmazást, és a rendszer várólistára, de még nem feldolgozott kötegek által meghatározott műveleteket metaadatok tárolására. Bizonyos esetekben az ellenőrzőpontokat is tartalmazza az adatok mentése a az rdd-k gyorsabban újraépítése származó mi szerepel az rdd-k Spark által kezelt adatok állapotát.

## <a name="deploying-spark-streaming-applications"></a>Spark Streaming-alkalmazások üzembe helyezéséhez

Általában a helyileg egy JAR-fájlba a Spark Streaming-alkalmazás létrehozása, és ezután telepítheti az a Spark on HDInsight a JAR-fájlt másolja a csatlakoztatott és a HDInsight-fürt alapértelmezett tárolója. Az alkalmazás a fürtről, a POST művelettel indítható LIVY REST API-kat. A bejegyzés törzse tartalmazza a JSON-dokumentumot, az elérési utat biztosít a JAR, amelynek fő metódus határozza meg, és az adatfolyam-továbbítási alkalmazást, és szükség esetén az erőforrás-igényű (például végrehajtóval, a memória és magok száma) a feladat fut az osztály neve , és minden olyan konfigurációs beállítást az alkalmazás kódjában igényel.

![A Spark Streaming-alkalmazás üzembe helyezése](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Az összes alkalmazás állapotát egy GET kérelmet a LIVY-végpont az is ellenőrizhető. Végül a futó alkalmazás leállíthatja a LIVY végponthoz törlési kérelem elküldése. További információ a LIVY API: [távoli feladatok a LIVYVEL](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>További lépések

* [A HDInsight egy Apache Spark-fürt létrehozása](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Spark Streamelés programozási útmutatója](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Indítsa el a Spark-feladatok a LIVYVEL távolról](apache-spark-livy-rest-interface.md)
