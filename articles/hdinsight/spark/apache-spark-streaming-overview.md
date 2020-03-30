---
title: Spark-streamelés az Azure HDInsightban
description: Az Apache Spark Streaming alkalmazások használata HDInsight Spark-fürtökön.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/20/2019
ms.openlocfilehash: 521d72642a27995d096402a4ca0e4af632b0788c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74406266"
---
# <a name="overview-of-apache-spark-streaming"></a>Az Apache Spark Streaming áttekintése

[Apache szikra](https://spark.apache.org/) A streamelés adatfolyam-feldolgozást biztosít a HDInsight Spark-fürtökön, és garantálja, hogy minden bemeneti eseményt pontosan egyszer dolgoz fel a rendszer, még akkor is, ha csomóponthiba lép fel. A Spark Stream egy hosszú ideig futó feladat, amely bemeneti adatokat fogad a legkülönbözőbb forrásokból, beleértve az Azure Event Hubs, az Azure IoT Hub, [az Apache Kafka, az Apache](https://kafka.apache.org/) [Flume,](https://flume.apache.org/)a Twitter, a [ZeroMQ,](http://zeromq.org/)a nyers TCP-szoftvercsatornák, vagy [az Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) fájlrendszerek figyelése. A kizárólag eseményvezérelt folyamatokkal ellentétben a Spark Stream időablakokba, például egy 2 másodperces szeletbe kötegeli a bemeneti adatokat, majd az egyes adatkötegeket térképsegítségével, csökkenti, illeszti és kibontja a műveleteket. A Spark Stream ezután az átalakított adatokat fájlrendszerekbe, adatbázisokba, irányítópultokra és a konzolra írja.

![Streamfeldolgozás HDInsight-mal és Spark Streaming-el](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark Streaming alkalmazások nak meg kell várnia a másodperc tört részét, hogy összegyűjtse az egyes *mikro-köteg* események, mielőtt elküldi a kötegfeldolgozásra. Ezzel szemben egy eseményvezérelt alkalmazás minden eseményt azonnal feldolgoz. Spark Streamelés késés általában néhány másodperc alatt. A mikroköteg-megközelítés előnyei a hatékonyabb adatfeldolgozás és az egyszerűbb aggregált számítások.

## <a name="introducing-the-dstream"></a>Bemutatkozik a DStream

A Spark Streaming a bejövő adatok folyamatos adatfolyamát jelenti egy DStream *nevű, különálló adatfolyam* használatával. A DStream létrehozható bemeneti forrásokból, például az Event Hubs vagy a Kafka, vagy egy másik DStream átalakítások alkalmazásával.

A DStream egy absztrakciós réteget biztosít a nyers eseményadatok tetején.

Kezdje egyetlen eseménnyel, mondja ki a hőmérséklet-leolvasást egy csatlakoztatott termosztátból. Amikor ez az esemény megérkezik a Spark Streaming-alkalmazás, az esemény megbízható módon tárolódik, ahol több csomóponton replikálódik. Ez a hibatűrések biztosítják, hogy egyetlen csomópont meghibásodása nem eredményezi az esemény elvesztését. A Spark-mag olyan adatstruktúrát használ, amely a fürt több csomópontja között osztja el az adatokat, ahol minden csomópont általában saját adatokat tart fenn a memóriában a legjobb teljesítmény érdekében. Ezt az adatstruktúrát *rugalmas elosztott adatkészletnek* (RDD) nevezzük.

Minden RDD a felhasználó által meghatározott időkereten keresztül, a *kötegelt intervallumon*keresztül gyűjtött eseményeket jelöli. Ahogy minden kötegelt intervallum eltelik, egy új RDD jön létre, amely az adott intervallum összes adatát tartalmazza. A rdd-k folyamatos készletét a dstream gyűjti. Ha például a kötegidőköz egy másodperc hosszú, a DStream minden másodpercben egy olyan RDD-t tartalmazó köteget bocsát ki, amely az adott másodperc alatt bevitt összes adatot tartalmazza. A DStream feldolgozásakor a hőmérsékleti esemény megjelenik az egyik ilyen kötegben. A Spark Streaming alkalmazás feldolgozza az eseményeket tartalmazó kötegeket, és végső soron az egyes RDD-kben tárolt adatokra hat.

![Példa DStream hőmérsékleti események](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>A Spark Streaming alkalmazás szerkezete

A Spark Streaming alkalmazás egy hosszú ideig futó alkalmazás, amely adatokat fogad bea betöltési forrásokból, átalakításokat alkalmaz az adatok feldolgozásához, majd lelöki az adatokat egy vagy több célhelyre. A Spark Streaming alkalmazás szerkezete egy statikus és egy dinamikus rész. A statikus rész határozza meg, hogy az adatok honnan származnak, milyen feldolgozást kell végezni az adatokon, és hogy az eredményeknek hová kell menniük. A dinamikus rész fut az alkalmazás a végtelenségig, várva a stop jelet.

A következő egyszerű alkalmazás például egy szövegsort kap egy TCP-foglalaton keresztül, és megszámolja, hogy az egyes szavak hányszor jelennek meg.

### <a name="define-the-application"></a>Az alkalmazás meghatározása

Az alkalmazáslogika-definíció négy lépésből áll:

1. Hozzon létre egy StreamingContext.
2. Hozzon létre egy DStream a StreamingContext.
3. Átalakítások alkalmazása a DStreamre.
4. Adja ki az eredményeket.

Ez a definíció statikus, és az alkalmazás futtatásáig a rendszer nem dolgoz fel adatokat.

#### <a name="create-a-streamingcontext"></a>Streamingkörnyezet létrehozása

Hozzon létre egy StreamingContext a SparkContext, amely a fürtre mutat. A StreamingContext létrehozásakor megadhatja a köteg méretét másodpercben, például:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>DStream létrehozása

A StreamingContext-példánysegítségével hozzon létre egy bemeneti DStream a bemeneti forráshoz. Ebben az esetben az alkalmazás figyeli az új fájlok megjelenését a HDInsight-fürthöz csatlakoztatott alapértelmezett tárolóban.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Átalakítások alkalmazása

A feldolgozást a DStream-en transzformációk alkalmazásával valósítja meg. Ez az alkalmazás kap egy sor szöveget egy időben a fájlt, osztja minden sort szavakra, majd használja a térkép-reduce mintát, hogy hány alkalommal jelenik meg az egyes szavakat.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Kimeneti eredmények

A kimeneti műveletek alkalmazásával tolja ki az átalakítási eredményeket a célrendszerekbe. Ebben az esetben a számításon való minden egyes futtatás eredménye a konzol kimenetére kerül.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Az alkalmazás futtatása

Indítsa el a streamelési alkalmazást, és futtassa, amíg a végződtetési jel nem érkezik.

```
ssc.start()
ssc.awaitTermination()
```

A Spark Stream API-val kapcsolatos részletekért, valamint az általa támogatott eseményforrásokról, átalakításokról és kimeneti műveletekről az [Apache Spark Streaming programozási útmutatóban](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)talál.

A következő mintaalkalmazás önálló, így futtathatja egy [Jupyter notebookban.](apache-spark-jupyter-notebook-kernels.md) Ebben a példában létrehoz egy modell adatforrást a DummySource osztályban, amely egy számláló értékét és az aktuális időt öt másodpercenként ezredmásodpercben adja ki. Egy új StreamingContext objektum kötegidőköze 30 másodperc. Minden alkalommal, amikor egy köteg jön létre, a streamelési alkalmazás megvizsgálja az RDD előállított, átalakítja az RDD egy Spark DataFrame, és létrehoz egy ideiglenes tábla a DataFrame felett.

```
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
```

Várjon körülbelül 30 másodpercet a fenti alkalmazás elindítása után.  Ezután rendszeresen lekérdezheti a DataFrame-et a kötegben lévő aktuális értékhalmaz megtekintéséhez, például ezzel az SQL-lekérdezéssel:

```sql
%%sql
SELECT * FROM demo_numbers
```

Az eredményül kapott kimenet a következőképpen néz ki:

| érték | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Hat érték van, mivel a DummySource 5 másodpercenként létrehoz egy értéket, és az alkalmazás 30 másodpercenként köteget bocsát ki.

## <a name="sliding-windows"></a>Csúszó ablakok

Ha a DStream-en egy bizonyos időszakon keresztül összesített számításokat szeretne végezni, például az átlagos hőmérséklet lehívásához az elmúlt két másodpercben, *használhatja* a Spark Streaming ben foglalt csúszóablak-műveleteket. A csúszó ablak időtartama (az ablak hossza) és az ablak tartalmának kiértékelésének időköze (a diaidőköz).

A csúszó ablakok átfedhetik egymást, például megadhat egy két másodperces ablakot, amely minden második csúszással csúszhat. Ez azt jelenti, hogy minden alkalommal, amikor összesítési számítást hajt végre, az ablak az előző ablak utolsó egy másodpercéből származó adatokat, valamint a következő egy másodpercben lévő új adatokat tartalmazza.

![Példa kezdeti ablak hőmérsékleti eseményekre](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Példa ablak hőmérsékletesemények után csúszik](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

A következő példa frissíti a dummysource-t használó kódot, hogy összegyűjtse a kötegeket egy egyperces időtartamú és egyperces diával rendelkező ablakba.

```
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

// Process batches in 1 minute windows
stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
    .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Az első perc után 12 tétel van - hat tétel az ablakban összegyűjtött két kötegből.

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

A Spark Streaming API-ban elérhető csúszóablak-funkciók közé tartozik az ablak, a countByWindow, a reduceByWindow és a countByValueAndWindow. Ezekről a függvényekről további információt [a DStreamek átalakításai](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Ellenőrzőpontok használata

A rugalmasság és a hibatűrés biztosítása érdekében a Spark Streaming ellenőrzőpontokon alapul annak érdekében, hogy az adatfolyam-feldolgozás megszakítás nélkül folytatódjon, még a csomóponthibák esetén is. A HDInsightban a Spark ellenőrzőpontokat hoz létre a tartós tároláshoz (Azure Storage vagy Data Lake Storage). Ezek az ellenőrzőpontok a streamelési alkalmazás metaadatait, például a konfigurációt, az alkalmazás által meghatározott műveleteket és a várólistára helyezett, de még fel nem dolgozott kötegeket tárolja. Bizonyos esetekben az ellenőrzőpontok is tartalmazzák az adatok mentése az RDD-kben, hogy gyorsabban újraépítse az adatok állapotát, mi van jelen a Spark által kezelt RDD-k.

## <a name="deploying-spark-streaming-applications"></a>Spark Streaming-alkalmazások telepítése

Általában egy Spark Streaming-alkalmazást helyileg hoz létre egy JAR-fájlba, majd telepíti a Spark hdinsight-alapú üzembe helyezéséhez a JAR-fájl HDInsight-fürthöz csatlakoztatott alapértelmezett tárolóra másolásával. Az alkalmazást a fürtből elérhető LIVY REST API-kkal indíthatja el egy POST-művelet használatával. A POST törzse tartalmaz egy JSON-dokumentumot, amely biztosítja a JAR elérési útját, annak az osztálynak a nevét, amelynek fő metódusa meghatározza és futtatja a streamelési alkalmazást, valamint opcionálisan a feladat erőforrás-követelményeit (például a végrehajtók, a memória és a magok számát) , valamint az alkalmazáskód által igényelt konfigurációs beállításokat.

![Spark Streaming-alkalmazás telepítése](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Az összes alkalmazás állapota is ellenőrizhető egy GET kéréssel egy LIVY-végpontmal szemben. Végül egy futó alkalmazást leállíthat egy DELETE-kérelem kiadásával a LIVY-végponton. A LIVY API-val kapcsolatos részletekért tekintse [meg az Apache LIVY távoli feladatait](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>További lépések

* [Apache Spark-fürt létrehozása a HDInsightban](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Apache Spark Streaming programozási útmutató](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Az Apache LIVY segítségével távolról indíthatapache Spark-feladatokat](apache-spark-livy-rest-interface.md)
