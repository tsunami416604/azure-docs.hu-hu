---
title: A Spark on Azure hdinsight Streaming |} Microsoft Docs
description: "Spark Streaming alkalmazások használata a HDInsight Spark-fürtök."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: maxluk
ms.openlocfilehash: a4cc2768f0d4217b2bd14938889e9b71c26009c9
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2018
---
# <a name="overview-of-spark-streaming"></a>Spark Streaming áttekintése

Spark Streaming biztosít az adatfolyam adatfeldolgozás HDInsight Spark fürtök garanciális, hogy a bemeneti esemény feldolgozása pontosan egyszer, még akkor is, ha a csomóponton hiba történik. A Spark adatfolyama egy hosszan futó feladatot, amely megkapja a bemeneti adatok széles körét móddal, többek között az Azure Event Hubs, egy Azure IoT Hub, Kafka, Flume, Twitter, ZeroMQ, az TCP-szoftvercsatornák nyers, vagy a HDFS fájlrendszerek ellenőrzése. Egy kizárólag eseményvezérelt eljárással szemben Spark adatfolyam bemeneti adatok kötegek idő Windows, mint 2 másodperces szelet, és majd átalakítja az egyes kötegekben térkép használt adatok, csökkentheti, csatlakozás és a műveletek kibontásához. A Spark adatfolyam majd ír az átalakított adatok fájlrendszerek, adatbázisok, az irányítópultok és a konzol.

![A HDInsight- és Spark Streamelési streamfeldolgozó](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark Streaming alkalmazások gyűjtéséhez mindegyik másodperc töredéke alatt meg kell várnia, *micro-kötegelt* események küldése adott kötegelt feldolgozás előtt. Ezzel szemben egy eseményvezérelt alkalmazás dolgoz fel minden esemény azonnal. Spark Streaming késés általában néhány másodperc alatt áll. A kötegelt micro megközelítés előnyeit hatékonyabb az adatfeldolgozás és egyszerűbb összesített számítások.

## <a name="introducing-the-dstream"></a>A DStream bemutatása

Spark Streaming jelenti. a bejövő adatok állandó folyama egy *diszkretizált adatfolyam* egy DStream nevezik. Egy DStream bemeneti forrásból, például az Event Hubs vagy Kafka, vagy egy másik DStream a átalakítások alkalmazásával hozhatók létre.

Egy DStream biztosít a nyers eseményadatok felső szintjének absztrakciós réteget. 

Egy adott eseményhez indításához mondja ki a csatlakoztatott termosztát olvasásakor hőmérséklet. Amikor ezt az eseményt a Spark Streaming kérelem érkezik, az esemény megbízható módot, ahol replikálódik több csomópont tárolódik. A hibatűrés biztosítja, hogy bármely egyetlen csomópont hibája nem esemény elvesztését eredményezi. A Spark mag olyan adatszerkezet, amely adatok elosztja a fürt több csomópontja használ, amelyben minden csomópont általában végzi a saját adatok memóriában a legjobb teljesítmény érdekében. Ezen adatok szerkezete nevezik egy *rugalmas elosztott dataset* (RDD).

Minden egyes RDD jelöli egy felhasználó által definiált időkereten hívása során gyűjtött eseményeket a *időköze*. Mivel minden egyes időköze eltelte után egy új RDD elő, amely tartalmazza az adott időköz az adatokat. A folyamatos készlete RDDs egy DStream történő összegyűjtése. Például ha a időköze 1 másodperc hosszú, a DStream megfelelően kibocsát egy kötegben minden második tartalmazó egy RDD, amely tartalmazza mindazokat az adatokat, hogy másodpercen belül okozhatnak. A DStream feldolgozásakor a hőmérséklet esemény jelenik meg, ezek a tételek egyikében. Spark Streaming alkalmazás dolgozza fel a kötegek, amelyek tartalmazzák az eseményeket, és végső soron kezelje az egyes RDD tárolt adatok.

![Példa DStream hőmérséklet-események ](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Spark Streaming alkalmazás szerkezete

Spark Streaming alkalmazás egy hosszan futó alkalmazás, amely a bemeneti forrásból származó adatokat fogad, az adatok feldolgozására átalakítások vonatkozik, és majd leküldéses értesítések az adatok egy vagy több helyre. Spark Streaming alkalmazás szerkezete rendelkezik egy statikus és dinamikus részét. A statikus részét határozza meg, ha az adatok származik, milyen feldolgozási végrehajtandó az adatokat, és ha kell nyissa meg az eredményeket. A dinamikus részben fut. Ha az alkalmazás határozatlan ideig stopjelzést vár.

Például a következő egyszerű alkalmazás szövegsor fogad egy TCP szoftvercsatorna keresztül, és megjeleníti a szám, ahányszor minden szó jelenik meg.

### <a name="define-the-application"></a>Az alkalmazás meghatározása

Az alkalmazás logika négy lépést van:

1. Hozzon létre egy StreamingContext.
2. Hozzon létre egy DStream a StreamingContext.
3. A DStream végrehajthatók.
4. Az eredményeket.

Ez a definíció statikus, és nem adatot dolgoz fel, amíg az alkalmazás futtatásához.

#### <a name="create-a-streamingcontext"></a>Hozzon létre egy StreamingContext

Hozzon létre egy StreamingContext a SparkContext mutat, a fürt. Egy StreamingContext létrehozásakor megadhatja a Köteg mérete (másodpercben), például:

    val ssc = new StreamingContext(spark, Seconds(1))

#### <a name="create-a-dstream"></a>Hozzon létre egy DStream

A StreamingContext példánnyal hozzon létre egy bemeneti DStream a bemeneti forráshoz. Ebben az esetben az alkalmazás az alapértelmezett Storage a HDInsight-fürthöz kapcsolódó új fájlok megjelenítési események követését.

    val lines = ssc.textFileStream("/uploads/2017/01/")

#### <a name="apply-transformations"></a>Alkalmazásához

A feldolgozás a DStream a átalakítások alkalmazásával valósítja meg. Ez az alkalmazás egy szövegsor egyszerre kap a fájl, soronként felosztja a szavakat, és majd egy map-reduce mintát használ, hogy hányszor minden szó jelenik meg.

    val words = lines.flatMap(_.split(" "))
    val pairs = words.map(word => (word, 1))
    val wordCounts = pairs.reduceByKey(_ + _)

#### <a name="output-results"></a>Kimeneti eredmények

Küldje le az átalakítási eredmények a cél rendszerek kimeneti műveletek alkalmazásával. Ebben az esetben a rendszer minden egyes futtatása a számítás eredményét nyomtatni a konzol kimeneti.

    wordCounts.print()

### <a name="run-the-application"></a>Az alkalmazás futtatása

Indítsa el az adatfolyam-továbbítási alkalmazást, és fut, amíg egy lezáró jel érkezik.

    ssc.start()            
    ssc.awaitTermination()

A Spark adatfolyam API-t a eseményforrások átalakítások és kimeneti műveletek támogatja, és tájékozódhat [Spark Streaming programozásával foglalkozó útmutatójában](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

A következő mintaalkalmazás önálló,, így a futtatható belül egy [Jupyter Notebook](apache-spark-jupyter-notebook-kernels.md). Ebben a példában próbaadatokat forrássá az osztályban, amelyek kimenete egy számláló és az aktuális idő ezredmásodpercben értéke öt másodpercenként DummySource hoz létre. Egy új StreamingContext objektumnak egy 30 másodperces időköze. Minden alkalommal, amikor egy kötegelt jön létre, az adatfolyam-továbbítási alkalmazást előállított RDD megvizsgálja a RDD konvertál egy Spark DataFrame és ideiglenes táblát hoz létre a DataFrame keresztül.

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

Majd lekérheti a kötegelt, az értékek jelenlegi készlete megjelenítéséhez rendszeresen DataFrame például használatával az SQL-lekérdezést:

    %%sql
    SELECT * FROM demo_numbers

Ennek kimenete a következőképpen néznek:

| érték | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Hat értékek vannak, mivel a DummySource létrehoz egy értéket 5 másodpercentként, és az alkalmazás megfelelően kibocsát egy kötegelt 30 másodpercenként.

## <a name="sliding-windows"></a>A késleltetett windows

Összesített számítások végrehajtásához a DStream bizonyos idő alatt, például az utolsó két másodpercen keresztül az átlaghőmérséklet eléréséhez használhatja a *ablak késleltetett* részét képező Spark Streaming műveletek. A csúszóablak rendelkezik egy duration (időtartam) és az időköz, amely során az ablak tartalma (a diák időköz) értékeli ki.

A késleltetett windows átfedhetik például, meghatározhatja, hogy egy ablakot, és két másodpercen belül, amely másodpercenként több diák használnak. Ez azt jelenti, hogy minden alkalommal, amikor egy összesítő számítás elvégzése, az ablak tartalmazza az utolsó egy másodperc az előző ablak adatait, valamint a következő egy második új adatokat.

![Példa kezdeti ablak hőmérséklet-események](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Példa ablak után késleltetett hőmérséklet-események](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

Az alábbi példa frissíti a kódot, amely a DummySource használ a kötegek összegyűjtésére egy ablakban egy perces időtartamra és egy egy perces menüpontban húzással állíthatja be.

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

Az első perc után nincsenek 12 - az egyes ablakban gyűjtött két köteg hat bejegyzéseket.

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

A Spark Streamelési API elérhető mozgó ablak Funkciók ablak, countByWindow, reduceByWindow és countByValueAndWindow tartalmazza. Ezeket a funkciókat a részletekért lásd: [a DStreams átalakítások](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Ellenőrzőpontok használata

Képes biztosítani a rugalmasság és a hibatűrés, Spark Streaming támaszkodnak az ellenőrzőpontok használatával győződjön meg arról, hogy az adatfolyam feldolgozása még állásuk csomóponthibák megszakításmentes folytatásához. A Hdinsightban Spark tartós tárhelyre (Azure Storage vagy a Data Lake Store) ellenőrzőpontot hoz létre. Ezeket az ellenőrzőpontokat az adatfolyam-továbbítási alkalmazásadatokat, például a konfigurációt, az alkalmazás, és várólistára volt, de még nem feldolgozott kötegek definiált műveletek metaadatait tárolja. Bizonyos esetekben az ellenőrzőpontok is tartalmazza az adatok mentése a gyorsabb újraépítése mi megtalálható-e a Spark által kezelt RDDs adatait állapotának RDDs.

## <a name="deploying-spark-streaming-applications"></a>Spark Streaming-alkalmazások telepítése

Általában létre olyan Spark Streaming-alkalmazás helyileg történő JAR-fájlra, és ezután telepítse a Spark on HDInsight úgy, hogy a HDInsight-fürtjét csatlakoztatott alapértelmezett tároló a JAR-fájlra. Megkezdheti az alkalmazás a LIVY REST API-khoz elérhető a fürtből, POST művelettel. A POST törzsét tartalmaz, amely a útvonalat biztosít a JAR, amelynek fő metódus határozza meg, és futtatja az adatfolyam-továbbítási alkalmazást, és szükség esetén a feladat (például végrehajtója, memória és -magok száma) erőforrás-követelményei az osztály nevét a JSON-dokumentum , és minden olyan konfigurációs beállítást az alkalmazás kódjában igényel.

![Spark Streaming alkalmazás telepítése](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Az összes alkalmazás állapotát ellenőrizni lehessen a LIVY végpont egy GET kérelmet a. A DELETE kérelmet a LIVY végpont kiállításával végül is állítsa le a futó alkalmazások. További részletek a LIVY API: [LIVY távoli feladatok](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>További lépések

* [Apache Spark-fürt létrehozása a Hdinsightban](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Spark Streaming programozási útmutatója](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Indítsa el a távoli LIVY Spark feladatokhoz](apache-spark-livy-rest-interface.md)