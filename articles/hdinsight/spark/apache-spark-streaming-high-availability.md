---
title: Magas rendelkezésre állású Spark Streaming-feladatok a YARN-ban – Azure HDInsight
description: Az Apache Spark Streaming beállítása magas rendelkezésre állású forgatókönyvhöz az Azure HDInsightban
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: ac51b77e1ffc2b476b0a73dac9b6917552a86ce4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807153"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Hozzon létre magas rendelkezésre állású Apache Spark Streaming feladatokat a YARN-nal

[Apache szikra](https://spark.apache.org/) A streamelés lehetővé teszi, hogy méretezhető, nagy átviteli sebességű, hibatűrő alkalmazásokat valósítson meg az adatfolyamok feldolgozásához. A HDInsight Spark-fürtön lévő Spark Streaming-alkalmazásokat különböző adatforrásokhoz csatlakoztathatja, például az Azure Event Hubs, az Azure IoT Hub, [az Apache Kafka,](https://kafka.apache.org/) [az Apache Flume, a](https://flume.apache.org/)Twitter, a [ZeroMQ,](http://zeromq.org/)a nyers TCP-szoftvercsatornákat, vagy figyelheti az [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) fájlrendszert a változásokról. A Spark Streaming támogatja a hibatűrést azzal a garanciával, hogy egy adott esemény feldolgozása pontosan egyszer történik, még csomóponthiba esetén is.

A Spark Streaming hosszú ideig futó feladatokat hoz létre, amelyek során átalakításokat alkalmazhat az adatokra, majd leküldéses az eredményeket a fájlrendszerekre, adatbázisokra, irányítópultokra és a konzolra. A Spark Streaming feldolgozza az adatok mikro-kötegeit, először egy adott időintervallumon keresztül gyűjti az események kötegét. Ezután a köteg et elküldi a rendszer feldolgozásra és kimenetre. A kötegidőintervallumok általában a másodperc tört részealatt vannak definiálva.

![Spark Streaming](./media/apache-spark-streaming-high-availability/apache-spark-streaming.png)

## <a name="dstreams"></a>DStreams

A Spark Streaming egy *különálló adatfolyam* (DStream) használatával folyamatos adatfolyamot jelöl. Ez a DStream bemeneti forrásokból, például az Event Hubs vagy a Kafka, vagy egy másik DStream-en alkalmazott átalakítások alkalmazásával hozható létre. Amikor egy esemény megérkezik a Spark Streaming alkalmazás, az esemény megbízható módon tárolódik. Ez azt, hogy az eseményadatok replikálása úgy történik, hogy több csomópont nak legyen róla egy másolata. Ez biztosítja, hogy egyetlen csomópont meghibásodása nem eredményezi az esemény elvesztését.

A Spark mag *rugalmas elosztott adatkészleteket* (RDDs) használ. Az RDD-k a fürt több csomópontján keresztül osztják el az adatokat, ahol az egyes csomópontok általában a legjobb teljesítmény érdekében teljesen a memóriában tartják az adatokat. Minden RDD egy kötegelt intervallumon keresztül gyűjtött eseményeket jelöl. A kötegelt időköz elteltek kor a Spark Streaming egy új RDD-t hoz létre, amely az adott intervallum összes adatát tartalmazza. Ez a folyamatos RDD-k gyűjtik a DStream. A Spark Streaming alkalmazás feldolgozza az egyes kötegek RDD-jében tárolt adatokat.

![Szikra DStream](./media/apache-spark-streaming-high-availability/apache-spark-dstream.png)

## <a name="spark-structured-streaming-jobs"></a>Spark strukturált streamelési feladatok

A Spark strukturált streamelése a Spark 2.0-ban analitikus motorként került bevezetésre a strukturált adatok streameléséhez. A Spark structured streaming a SparkSQL kötegelési motor API-kat használja. A Spark Streaminghez is a Spark strukturált streamelése a folyamatosan érkező mikroadat-kötegeken keresztül futtatja a számításait. A Spark strukturált streamelése adatfolyamot jelöl korlátlan sorokkal rendelkező bemeneti táblaként. Ez azt, hogy a bemeneti tábla az új adatok érkezésével folyamatosan növekszik. Ezt a bemeneti táblát egy hosszú ideig futó lekérdezés folyamatosan feldolgozza, és az eredményeket egy kimeneti táblába írják ki.

![Szikra strukturált streamelés](./media/apache-spark-streaming-high-availability/structured-streaming.png)

Strukturált streamelési, adatok érkeznek a rendszerbe, és azonnal a bemeneti táblába. Olyan lekérdezéseket ír, amelyek műveleteket hajtanak végre ezen a bemeneti táblán. A lekérdezés kimenete egy másik táblát, az Úgynevezett Eredménytáblát eredményez. Az Eredmények tábla a lekérdezés eredményeit tartalmazza, amelyekből adatokat kell megrajzolni egy külső adattárba, például relációs adatbázist. Az *eseményindító-időköz* beállítja az adatok beviteli táblából történő feldolgozásának időzítését. Alapértelmezés szerint strukturált streamelési feldolgozza az adatokat, amint megérkezik. Azonban azt is beállíthatja, hogy az eseményindító hosszabb időközönként fusson, így a streamelési adatok időalapú kötegekben lesznek feldolgozva. Az Eredmények táblázatban szereplő adatok minden alkalommal frissíthetők, amikor új adatok jelennek meg, így azok tartalmazzák az összes kimeneti adatot a streamelési lekérdezés kezdete óta (*teljes mód*), vagy csak azokat az adatokat tartalmazzák, amelyek a lekérdezés legutóbbi feldolgozása óta újak (*hozzáfűzési mód*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Hibatűrő Spark Streaming-feladatok létrehozása

A Spark Streaming-feladatok magas rendelkezésre állású környezetének létrehozásához kezdje az egyes feladatok kódolásával a helyreállításhoz hiba esetén. Az ilyen önbehajtási munkák hibatűrőek.

Az RDD-k számos tulajdonsággal rendelkeznek, amelyek segítik a magas rendelkezésre állású és hibatűrő Spark Streaming-feladatokat:

* Az RDD-kben DStream ként tárolt bemeneti adatok kötegei automatikusan replikálódnak a memóriában a hibatűrés érdekében.
* A dolgozói hiba miatt elveszett adatok újrakiszámíthatók a különböző dolgozók replikált bemeneti adataiból, feltéve, hogy ezek a munkavégző csomópontok rendelkezésre állnak.
* Gyors hiba recovery is előfordulhat egy másodpercen belül, a helyreállítás hibák / csellengők történik számítással a memóriában.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Pontosan egyszer szemantika a Spark Streaming

Ha olyan alkalmazást szeretne létrehozni, amely az egyes eseményeket egyszer (és csak egyszer) dolgozza fel, fontolja meg, hogyan indul újra az összes rendszerhiba-pont a probléma után, és hogyan kerülheti el az adatvesztést. Pontosan egyszer szemantika megköveteli, hogy nem adat veszik el bármely ponton, és hogy az üzenet feldolgozása újraindítható, függetlenül attól, hogy hol a hiba történik. Lásd: [Spark Streaming-feladatok létrehozása pontosan egyszer eseményfeldolgozással.](apache-spark-streaming-exactly-once.md)

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark Streaming és Apache Hadoop YARN

A HDInsightban a fürtmunkát *egy másik erőforrás-tárgyaló* (YARN) koordinálja. A Spark Streaming magas rendelkezésre állásának tervezése magában foglalja a Spark Streaming és a YARN-összetevők technikáit is.  Az alábbiakban egy YARN-t használó konfigurációt mutatunk be.

![YARN architektúra](./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png)

Az alábbi szakaszok a konfiguráció tervezési szempontjait ismertetik.

### <a name="plan-for-failures"></a>Hibák megtervezése

YarN-konfiguráció létrehozásához a magas rendelkezésre állás érdekében meg kell terveznie egy lehetséges végrehajtó vagy illesztőprogram meghibásodását. Egyes Spark Streaming-feladatok is tartalmaznak adatgarancia-követelményeket, amelyek további konfigurációt és beállításokat igényelnek. Például egy streamelési alkalmazás rendelkezhet egy üzleti követelmény nulla adatvesztés garancia ellenére bármilyen hiba, amely a streamelési rendszer vagy a HDInsight-fürt.

Ha egy **végrehajtó** meghibásodik, a feladatokat és a fogadók automatikusan újraindítja a Spark, így nincs szükség konfigurációs módosításra.

Ha azonban egy **illesztőprogram** meghibásodik, akkor az összes társított végrehajtó sikertelen lesz, és az összes fogadott blokk és számítási eredmény elvész. Az illesztőprogram-meghibásodás ból való helyreállításhoz használja a *DStream ellenőrzőpontokat* a [Spark-streamelési feladatok létrehozása című részben leírtak szerint, pontosan egyszer eseményfeldolgozással.](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers) A DStream ellenőrzőpontok rendszeres időközönként menti a DStreamek *irányított aciklikus grafikonját* (DAG) a hibatűrő tárolóba, például az Azure Storage-ba.  Ellenőrzőpontok lehetővé teszi, hogy a Spark strukturált streamelési indítsa újra a sikertelen illesztőprogramot az ellenőrzőpont-információk.  Ez az illesztőprogram újraindítása elindítja az új végrehajtókat, és újraindítja a fogadókat is.

A DStream ellenőrzőpontokkal rendelkező illesztőprogramok helyreállítása:

* Konfigurálja az illesztőprogram automatikus újraindítását `yarn.resourcemanager.am.max-attempts`a YARN-on a konfigurációs beállítással.
* Állítson be ellenőrzőpont-könyvtárat egy HDFS-kompatibilis fájlrendszerben a segítségével. `streamingContext.checkpoint(hdfsDirectory)`
* A forráskód átstrukturálása ellenőrzőpontok használatával, például:

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* Az elveszett adatok helyreállításának konfigurálása a használatával `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`történő írási napló (WAL) `StorageLevel.MEMORY_AND_DISK_SER`engedélyezésével és a memóriában lévő replikáció letiltásával a dstreambemeneti adatok használatával.

Összefoglalva, a checkpointing + WAL + megbízható vevőkészülékek használatával "legalább egyszer" adathelyreállítást tud biztosítani:

* Pontosan egyszer, mindaddig, amíg a fogadott adatok nem vesznek el, és a kimenetek vagy idempotens vagy tranzakciós.
* Pontosan egyszer, az új Kafka Direct megközelítéssel, amely a Kafka-t replikált naplóként használja, nem pedig fogadók vagy WAL-ok használatával.

### <a name="typical-concerns-for-high-availability"></a>A magas rendelkezésre állással kapcsolatos tipikus aggodalmak

* A streamelési feladatok at nehezebb figyelni, mint a kötegelt feladatokat. Spark Streamelési feladatok általában hosszú ideig fut, és yarn nem összesíti a naplókat, amíg a feladat befejeződik.  A Spark-ellenőrzőpontok elvesznek az alkalmazás vagy a Spark-frissítések során, és a frissítés során törölnie kell az ellenőrzőpont-címt.

* Konfigurálja a YARN fürtmódot úgy, hogy az illesztőprogramok akkor is fussanak, ha egy ügyfél meghibásodik. Az illesztőprogramok automatikus újraindításának beállítása:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* A Spark és a Spark Streamelésfelhasználói felület konfigurálható metrikarendszerrel rendelkezik. További kódtárakat is használhat, például a Grafit/Grafana irányítópult-metrikákat, például a "feldolgozott num records", a "memory/GC usage on driver & végrehajtók", a "teljes késleltetés", a "fürt kihasználtsága", és így tovább. A strukturált streamelési 2.1-es vagy újabb verzióban további metrikák összegyűjtésére is használható. `StreamingQueryListener`

* A hosszú ideig futó feladatokat kell szegmentálnia.  Amikor egy Spark Streaming-alkalmazást küld a fürtnek, a YARN-várólistát, ahol a feladat fut, meg kell határozni. A [YARN kapacitásütemező](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) segítségével hosszú ideig futó feladatokat küldhet külön várólistákba.

* Állítsa le a streamelési alkalmazást szabályosan. Ha az eltolások ismertek, és az összes alkalmazásállapot külsőleg tárolódik, akkor programozott módon leállíthatja a streamelési alkalmazást a megfelelő helyen. Az egyik technika az, hogy a "menet horgok" a Spark, ellenőrzésével egy külső zászló *n* másodpercenként. Az alkalmazás indításakor használhatja a HDFS fájlon létrehozott *jelölőfájlt* is, majd eltávolíthatja, ha le szeretné állítani. A jelölőfájl-megközelítéshez használjon egy külön szálat a Spark-alkalmazásban, amely ehhez hasonló kódot hív meg:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>További lépések

* [Apache Spark streamelés – áttekintés](apache-spark-streaming-overview.md)
* [Apache Spark Streaming-feladatok létrehozása pontosan egyszeri eseményfeldolgozással](apache-spark-streaming-exactly-once.md)
* [Hosszú ideig futó Apache Spark streaming feladatok a YARN-on](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/)
* [Strukturált streamelés: Hibatűrő szemantika](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Különálló adatfolyamok: Hibatűrő modell méretezhető adatfolyam-feldolgozáshoz](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
