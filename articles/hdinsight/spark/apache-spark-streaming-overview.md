---
title: "Mi az Azure HDInsight Spark adatfolyamként történő |} Microsoft Docs"
description: "Spark Streaming alkalmazások használata a HDInsight Spark-fürtök."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: raghavmohan
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2017
ms.author: ramoha
ms.openlocfilehash: 9debf78480e6c77ab23581754350fb95f34f8fc4
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="overview-of-spark-streaming"></a>Spark Streaming áttekintése

Spark Streaming biztosít az adatfolyam adatfeldolgozás HDInsight Spark fürtök garanciális, hogy a bemeneti esemény feldolgozása pontosan egyszer, még akkor is, ha a csomóponton hiba történik. A Spark adatfolyama egy hosszan futó feladatot, amely megkapja a bemeneti adatok széles körét móddal, többek között az Azure Event Hubs, egy Azure IoT Hub, Kafka, Flume, Twitter, ZeroMQ, az TCP-szoftvercsatornák nyers, vagy a HDFS fájlrendszerek ellenőrzése. Egy kizárólag eseményvezérelt eljárással szemben Spark adatfolyam bemeneti adatok kötegek idő Windows, mint 2 másodperces szelet, és majd átalakítja az egyes kötegekben térkép használt adatok, csökkentheti, csatlakozás és a műveletek kibontásához. Ezután ír az átalakított adatok fájlrendszerek, adatbázisok, az irányítópultok és a konzol.

![A HDInsight- és Spark Streamelési streamfeldolgozó](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark Streaming alkalmazások várakoznia kell egy második gyűjtéséhez mindegyik néhány hányada *micro-kötegelt* események küldése adott kötegelt feldolgozás előtt. Ezzel szemben egy eseményvezérelt alkalmazás dolgoz fel minden esemény azonnal. Spark Streaming késés általában néhány másodperc alatt áll. A kötegelt micro megközelítés előnyeit hatékonyabb az adatfeldolgozás és egyszerűbb összesített számítások.

## <a name="introducing-the-dstream"></a>A DStream bemutatása

Spark Streaming jelenti. a bejövő adatok állandó folyama egy *diszkretizált adatfolyam* egy DStream nevezik. Egy DStream bemeneti forrásból, mint az Event Hubs vagy Kafka, vagy egy másik DStream átalakítások alkalmazásával hozhatók létre.

Egy DStream biztosít a nyers eseményadatok felső szintjének absztrakciós réteget. 

Egy adott eseményhez indításához mondja ki a csatlakoztatott termosztát olvasásakor hőmérséklet. Amikor ezt az eseményt a Spark Streaming kérelem érkezik, az esemény egy megbízható módon tárolja, ez azt jelenti, hogy replikálódik több csomópont. A hibatűrés biztosítja, hogy bármely egyetlen csomópont hibája nem esemény elvesztését eredményezi. A Spark mag olyan adatszerkezet, amely adatok elosztja a fürt több csomópontja használ, amelyben minden csomópont általában végzi a saját adatok memóriában a legjobb teljesítmény érdekében. Ezen adatok szerkezete nevezik egy *rugalmas elosztott dataset* (RDD).

Minden egyes RDD nevű időköze a felhasználó által definiált időkereteket keresztül összegyűjtött események jelöli. Mivel minden egyes időköze eltelte után egy új RDD elő, amely tartalmazza az adott időköz az adatokat. A folyamatos készlete RDDs egy DStream történő összegyűjtése. Például ha a időköze 1 másodperc hosszú, a DStream megfelelően kibocsát egy kötegben minden második tartalmazó egy RDD, amely tartalmazza mindazokat az adatokat, hogy másodpercen belül okozhatnak. A DStream feldolgozásakor a hőmérséklet esemény jelenik meg, ezek a tételek egyikében. Spark Streaming alkalmazás dolgozza fel a kötegek, amelyek tartalmazzák az eseményeket, és végső soron kezelje az egyes RDD tárolt adatok.

![Példa DStream hőmérséklet-események ](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Spark Streaming alkalmazás szerkezete
Spark Streaming alkalmazás egy hosszan futó alkalmazás, amely a bemeneti forrásból származó adatokat fogad, az adatok feldolgozására átalakítások vonatkozik, és majd leküldéses értesítések az adatok egy vagy több helyre. Spark Streaming alkalmazás szerkezete két fő részből áll. Először, ha az adatok származik, milyen feldolgozási végrehajtandó az adatokat, és ha meghatározásához nyissa meg az eredményeket kell. Másodszor, az alkalmazás futtatása határozatlan ideig stopjelzést vár.

Például ez egy egyszerű alkalmazást, amely egy szövegsor fogad egy TCP szoftvercsatorna keresztül, és megjeleníti a szám, ahányszor minden szó jelenik meg.

### <a name="define-the-application"></a>Az alkalmazás meghatározása

A logika definícióját négy lépésből áll:

1. Hozzon létre egy StreamingContext.
2. Hozzon létre egy DStream a StreamingContext.
3. A DStream végrehajthatók.
4. Az eredményeket.

Ez a definíció statikus, és nem adatot dolgoz fel, amíg az alkalmazás futtatásához.

#### <a name="create-a-streamingcontext"></a>Hozzon létre egy StreamingContext
Hozzon létre egy StreamingContext a SparkContext mutat, a fürt. Egy StreamingContext létrehozásakor megadhatja a Köteg mérete (másodpercben), például:

    val ssc = new StreamingContext(spark, Seconds(1))

#### <a name="create-a-dstream"></a>Hozzon létre egy DStream
A létrehozott StreamingContext példányát használja, hozzon létre egy bemeneti DStream a bemeneti forráshoz. Ebben az esetben azt megtekintése a alapértelmezett tárolót a HDInsight-fürthöz kapcsolódó új fájlokat megjelenítési.

    val lines = ssc.textFileStream("/uploads/2017/01/")

#### <a name="apply-transformations"></a>Alkalmazásához
A feldolgozás a DStream a átalakítások alkalmazásával valósítja meg. Az alkalmazás egy szövegsor egyszerre kap a fájl minden egyes sorban felosztja a szavak és majd egy map-reduce mintát használ, hogy hányszor minden szó jelenik meg.

    val words = lines.flatMap(_.split(" "))
    val pairs = words.map(word => (word, 1))
    val wordCounts = pairs.reduceByKey(_ + _)

#### <a name="output-results"></a>Kimeneti eredmények
Küldje le az átalakítási eredmények a cél rendszerek kimeneti műveletek alkalmazásával. Ebben az esetben egyes futtatása a számítás eredményét megmutatjuk a konzol kimeneti.

    wordCounts.print()

### <a name="run-the-application"></a>Az alkalmazás futtatása
Indítsa el az adatfolyam-továbbítási alkalmazást, és fut, amíg egy lezáró jel érkezik.

    ssc.start()            
    ssc.awaitTermination()

További részletek a Spark-adatfolyam API együtt a eseményforrások átalakítások és kimeneti műveletek támogatja lásd: [Spark Streaming programozásával foglalkozó útmutatójában](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

A következő mintaalkalmazás önálló,, így a futtatható belül egy [Jupyter Notebook](apache-spark-jupyter-notebook-kernels.md). A példában tudjuk létrehozni a osztály, amely a számláló értékét DummySource utánzatait adatforrás és az aktuális idő ezredmásodpercben öt másodpercenként. Egy új StreamingContext objektum, amely rendelkezik egy 30 másodperces időköze létrehozhatunk. Minden alkalommal, amikor egy kötegelt jön létre, azt az előállított RDD megvizsgálja, átalakítja a Spark DataFrame és ideiglenes táblát hoz létre a DataFrame keresztül.

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

Majd rendszeres időközönként a kötegelt az értékek jelenlegi készlete megjelenítéséhez DataFrame lekérdezheti azt. Ebben az esetben a következő SQL-lekérdezés használjuk:

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

Várható biztonsági hat értékek, mivel a DummySource létrehoz egy értéket 5 másodpercentként, és azt bocsát ki egy köteget 30 másodpercenként.

## <a name="sliding-windows"></a>A késleltetett windows
Ha azt szeretné, hogy összesített számítások végrehajtásához a DStream bizonyos idő alatt, például az átlaghőmérséklet lekérni az utolsó 2 másodpercen keresztül a Spark Streaming mellékelt mozgó ablak műveletek is használhatja. A csúszóablak van definiálva, időtartammal (időtartam) és az időköz, amely során az ablak tartalmát vannak értékeli ki (a diák időköz).

Ezek a késleltetett windows átfedhetik például, meghatározhatja, hogy egy ablak 1 másodpercenként diák hosszúságú 2 másodpercben. Ez azt jelenti, hogy minden alkalommal, amikor egy összesítő számítás elvégzése, az ablak tartalmazza az utolsó 1 másodperc az előző ablak adatait, valamint a következő 1 másodperc múlva új adatokat.

![Példa kezdeti ablak hőmérséklet-események](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Példa ablak után késleltetett hőmérséklet-események](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

A következő példában frissítjük a kódot, amely a DummySource használ a kötegek összegyűjtésére egy ablakban egy perces időtartamra és egy egy perces menüpontban húzással állíthatja be.

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

Az első perc után az így kapott 12 bejegyzések - az egyes ablakban gyűjtött két köteg hat bejegyzéseket.

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

A Spark Streamelési API elérhető mozgó ablak Funkciók ablak, countByWindow, reduceByWindow és countByValueAndWindow tartalmazza. Ezek a funkciók témakör [a DStreams átalakítások](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Ellenőrzőpontok használata
Képes biztosítani a rugalmasság és a hibatűrés, Spark Streaming támaszkodnak az ellenőrzőpontok használatával biztosítását, hogy az adatfolyam feldolgozása még állásuk csomóponthibák megszakításmentes folytatásához. A Hdinsightban Spark tartós tárhelyre (Azure Storage vagy a Data Lake Store) ellenőrzőpontot hoz létre. Ezeket az ellenőrzőpontokat az adatfolyam-továbbítási alkalmazásadatokat, például a konfigurációt, az alkalmazás, és várólistára volt, de még nem feldolgozott kötegek definiált műveletek metaadatait tárolja. Bizonyos esetekben az ellenőrzőpontok is tartalmazza az adatok mentése a lerövidítheti a szükséges időt, hogy mi is szerepel a Spark által kezelt RDDs adatait állapotának RDDs.

## <a name="deploying-spark-streaming-applications"></a>Spark Streaming-alkalmazások telepítése
Általában a a Spark Streaming alkalmazás helyi összeállítása, és ezután telepítse a Spark on HDInsight a JAR-fájlra, amely tartalmazza az alkalmazás az alapértelmezett Storage a HDInsight-fürthöz kapcsolódó másolásával. Az alkalmazás majd elindíthatja a LIVY REST API-k használatával elérhető a fürtből. A POST műveletet azt, hogy tartalmazza-e a FELADÁS egy vagy több törzsét, amely a útvonalat biztosít a JAR, amelynek fő metódus határozza meg, és futtatja az adatfolyam-továbbítási alkalmazást, és szükség esetén a feladat (például utólagos erőforrás-követelményei az osztály nevét a JSON-dokumentum ecutors, memória és -magok), és minden olyan konfigurációs beállítást az alkalmazás kódjában igényel.

![Példa ablak után késleltetett hőmérséklet-események](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Az összes alkalmazás állapotát ellenőrizni lehessen a LIVY végpont egy GET kérelmet a. A DELETE kérelmet a élő végpont kiállításával végül is állítsa le a futó alkalmazások. További részletek a LIVY API: [LIVY távoli feladatok](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Következő lépések

* [Az Apache Spark-fürt létrehozása hdinsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Spark Streaming programozási útmutatója](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Indítsa el a távoli LIVY Spark feladatokhoz](apache-spark-livy-rest-interface.md)