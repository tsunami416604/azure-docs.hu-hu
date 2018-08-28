---
title: Magas rendelkezésre állású Spark Streamelési feladatok létrehozása a YARN - Azure HDInsight-ban
description: Hogyan állítható be a Spark Streaming, magas rendelkezésre állású forgatókönyv esetén.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/26/2018
ms.openlocfilehash: 0725c70668ca3089028bff5fc1d8374c6bfdecde
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43051991"
---
# <a name="create-high-availability-spark-streaming-jobs-with-yarn"></a>Magas rendelkezésre állású Spark Streamelési feladatok létrehozása a YARN

Spark Streaming megvalósítását teszi feldolgozása adatfolyamok, skálázható, nagy átviteli sebességű és hibatűrő alkalmazásokat. HDInsight Spark-fürtön a Spark Streamelési alkalmazások csatlakozhat különböző adatforrások, például az Azure Event Hubs, Azure IoT Hub, a Kafka, Flume, Twitter, ZeroMQ, nyers TCP-szoftvercsatornák, vagy egy megfigyelési a HDFS-fájlrendszer, a módosításokat. Spark Streaming támogatja az, hogy egy adott eseményre egyszer dolgozza fel pontosan, még akkor is csomóponthibával garantálunk hibatűrést.

Spark Streaming hoz létre a hosszan futó feladatok, mely során is tudja átalakításokat az adatokhoz, és majd továbbítsa az eredményeket feladatszinten csatlakoztathatók, adatbázisok, az irányítópultok és a konzol. Spark Streaming folyamatok micro-kötegek az adatokat, begyűjtenie az első batch események egy meghatározott idő alatt. Ezután a batch a rendszer küldi a feldolgozás és a kimenet. A Batch időintervallumok általában a másodperc tört vannak meghatározva.

![Spark Streaming](./media/apache-spark-streaming-high-availability/spark-streaming.png)

## <a name="dstreams"></a>DStreams

Spark Streaming jelöli az adatok folyamatos továbbítása egy *diszkretizált stream* (DStream). Ez DStream bemeneti forrásból, például az Event Hubs vagy a Kafka, vagy egy másik DStream az átalakítások alkalmazásával hozható létre. Amikor egy esemény a Spark Streaming kérelem érkezik, az esemény megbízhatóan tárolódik. Az eseményadatokat, replikálódik, hogy több csomópont egy példányát. Ez biztosítja, hogy a hiba bármely egyetlen csomópont nem a elvesztését eredményezi az esemény.

A Spark mag használ *rugalmas elosztott adatkészleteken* (rdd-kként). Rdd-k adatok elosztása több csomópontra a fürtben, amelyben minden egyes csomópont általában végzi az adatok teljesen memórián belüli a legjobb teljesítmény érdekében. Minden egyes RDD egy kötegelt időszakban összegyűjtött események jelöli. A batch időtartam, amikor egy adott időszakban az összes adatokat tartalmazó új RDD Spark Streaming eredményez. Az rdd-kként folyamatos készletét gyűjti be egy DStream. A Spark Streaming alkalmazás dolgozza fel az egyes kötegek RDD tárolt adatokat.

![A Spark DStream](./media/apache-spark-streaming-high-availability/DStream.png)

## <a name="spark-structured-streaming-jobs"></a>A Spark strukturált Stream feladatok

A Spark strukturált Stream jelent a Spark 2.0 elemzési modulként streamelési strukturált adatok számára. A Spark strukturált Stream használja a motor API-k kötegelés sparksql-hez. Csakúgy, mint a Spark Streaming, Spark strukturált Stream fut, a számítások micro-kötegek folyamatosan érkező adatok felett. Spark strukturált Stream jelöli az adatfolyamot, korlátlan számú sort egy bemeneti táblát. Ez azt jelenti, hogy a bemeneti tábla mennyiségével növekszik új adatok érkeznek. A bemeneti tábla egy hosszan futó lekérdezést folyamatosan dolgoz fel, és az eredmények kimeneti táblát írja.

![A Spark strukturált Stream](./media/apache-spark-streaming-high-availability/structured-streaming.png)

Strukturált Streamelés adatok szolgáltatáshoz érkezik, a rendszer, és azonnal a bemeneti tábla be lett töltve. A bemeneti tábla kapcsolatos művelet-végrehajtási lefutó lekérdezéseket írhat. A lekérdezés kimenet egy másik, az eredmények táblázatában nevű táblát. Az eredmények táblázatában tartalmazza, amelyből rajzoljon egy külső adattárolója elküldendő adatok, a lekérdezés eredményeit egy relációs adatbázisban. A *eseményindító időköz* állítja be az adatok feldolgozásakor a bemeneti tábla ütemezését. Alapértelmezés szerint strukturált Stream feldolgozza az adatokat, amint megérkeznek. Ugyanakkor beállíthatja úgy is az eseményindító futtathatók több idő, így a streamadatok feldolgozása időalapú kötegekben. Az adatok az eredmények táblázatában előfordulhat, hogy teljesen frissíteni kell minden alkalommal, hogy van-e új adatokat tartalmaz a kimeneti adatok a streamelési lekérdezés kezdete óta (*teljes mód*), vagy csak csak az utolsó óta új adatokat tartalmaz a lekérdezés feldolgozásának ideje (*hozzáfűzéssel*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Hibatűrő Spark Streamelési feladatok létrehozása

Egy magas rendelkezésre állású környezetben, a Spark Streamelési feladatok létrehozása, először kódolási hiba esetén a helyreállítás az egyes feladatok. Az ilyen helyi helyreállított feladatok és hibatűrő.

Rdd-k rendelkezik, amely segítséget nyújt a magas rendelkezésre állású és hibatűrő Spark Streamelési feladatok számos tulajdonságát:

* A rendszer automatikusan replikálja az rdd-k egy DStream tárolja, a bemeneti adatok kötegek a hibatűrés memóriában.
* Feldolgozó hiba miatt elveszett adatok is recomputed a replikált bemeneti adatok különböző feldolgozókon, mindaddig, amíg az adott munkavégző csomópontok érhetők el.
* Gyors tartalék helyreállítási hibák vagy elvetésének helyreállítás történik, a memóriában számítási keresztül belül egy második, akkor fordulhat elő.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Pontosan-egyszer szemantika a Spark Streaming

Hozzon létre egy alkalmazást, minden egyes esemény egyszer (és csak egyszer), fontolja meg az összes, a rendszer pontokat újraindítása után probléma, és hogyan adatok elvesztését elkerülheti. Pontosan-szemantika megkövetelése, hogy bármikor adatvesztés, pedig a üzenetfeldolgozás újraindítható, függetlenül attól, ahol a hiba akkor fordul elő, ha. Lásd: [létrehozása Spark Streamelési feladatok az pontosan – egyszeri esemény feldolgozása](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-yarn"></a>Spark Streaming és YARN

HDInsight, a fürt munkahelyi koordinálja *még egy másik Resource Negotiator* (YARN). Tervezés magas rendelkezésre állású Spark Streamelési tartalmazza a technikák, a Spark Streaming és YARN-összetevők.  Az alábbiakban látható egy példa konfiguráció látható YARN használatával. 

![YARN-architektúra](./media/apache-spark-streaming-high-availability/yarn-arch.png)

A következő szakaszok ismertetik a kialakítási szempontok ehhez a konfigurációhoz.

### <a name="plan-for-failures"></a>Hibák tervezése

A magas rendelkezésre állású YARN konfiguráció létrehozásához, meg kell terveznie a lehetséges végrehajtó vagy illesztőprogram hibát. Más Spark Streamelési feladatok is szükség további konfigurációs és beállítási adatok garancia követelményeknek. A streamelési alkalmazások előfordulhat például, annak ellenére, hogy semmilyen hiba, amely akkor fordul elő, a üzemeltetési streamelési system vagy a HDInsight-fürt biztosítása egy nulla-az-adatvesztést üzleti követelmény.

Ha egy **végrehajtó** meghiúsul, a feladatok és a fogadók vannak Spark automatikusan újrainduljon, így nem szükséges konfiguráció módosul.

Azonban ha egy **illesztőprogram** sikertelen, akkor az összes hozzá tartozó társított végrehajtóval nem sikerül, és az összes fogadott blokkok és a számítás eredménye elvész. Illesztőprogram hibát helyreállíthatja, használja a *DStream ellenőrzőpontok használata* leírtak szerint [létrehozása Spark Streamelési feladatok az pontosan – egyszeri esemény feldolgozása](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). Időnként DStream ellenőrzőpontok használata a *irányított aciklikus diagramhoz* (DAG), például az Azure Storage és hibatűrő tárolási DStreams.  Ellenőrzőpontok használata lehetővé teszi, hogy a Spark strukturált Stream, indítsa újra a sikertelen illesztőprogram, a checkpoint alapján.  Az illesztőprogram-újraindítás új végrehajtóval elindul, és a fogadók is újraindul.

Illesztőprogramok DStream ellenőrzőpontok helyreállításához:

* YARN-illesztőprogram automatikus újraindítás konfigurálja a konfigurációs beállítás `yarn.resourcemanager.am.max-attempts`.
* Ellenőrzőpont könyvtár beállítása egy HDFS-kompatibilis fájlrendszer `streamingContext.checkpoint(hdfsDirectory)`.
* Átstrukturálása a forráskódot, és használjon ellenőrzőpontokat a helyreállításhoz, például:

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

* Elveszett adatok helyreállításának konfigurálása a írási előre log (WAL) engedélyezésével `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`, és tiltsa le a bemeneti DStreams memórián belüli replikálása `StorageLevel.MEMORY_AND_DISK_SER`.

Összefoglalva, használja az ellenőrzőpontok használata, a WAL + megbízható fogadók, lesz, hogy a "legalább egyszeri" adat-helyreállítás:

* Pontosan egyszeri, mindaddig, amíg ne legyen adatvesztés a fogadott és a kimenetek idempotens vagy tranzakciós.
* Pontosan egyszer az új Kafka közvetlen megközelítéssel használó Kafka egy replikált log helyett a fogadók vagy WALs.

### <a name="typical-concerns-for-high-availability"></a>Magas rendelkezésre állás jellemző megjegyzése

* Nehezebb, mint a batch-feladatok folyamatos átviteli feladat figyeléséhez. Spark Streamelési feladatok jellemzően hosszan futó, és a YARN naplók nem összesíti, a feladat befejezéséig.  Alkalmazás- vagy Spark-frissítések során elvesznek Spark ellenőrzőpontokat, és törölje a jelet az ellenőrzőpont-könyvtár frissítése során kell.

* Állítsa be a YARN-fürt üzemmód illesztőprogramok futtatni, még akkor is, ha az ügyfélnek nem sikerül. Illesztőprogramok automatikus újraindítást beállítása:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark és a Spark Streaming felhasználói felületén konfigurálhatók metrikák rendszerrel kell rendelkeznie. Is használhatja, további kódtárak, például a grafit/Grafana letöltéséhez irányítópult-metrikáit, mint például "num rekordok feldolgozása", ' Memória/globális Katalógus használata illesztőprogram & végrehajtóval', "teljes késés", "felhasználás a fürt" és így tovább. Strukturált Streamelés a 2.1-es vagy újabb verziót, a használható `StreamingQueryListener` további metrikák begyűjtéséhez.

* Hosszan futó feladatok kell szegmentálhatja.  A Spark Streaming alkalmazás elküldésekor a fürthöz, a YARN várólista, ahol a feladat fut, meg kell határozni. Használhat egy [YARN kapacitás Scheduler](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) külön üzenetsorokat hosszú ideig futó feladatok elküldéséhez.

* A streamelési alkalmazás szabályosan leáll. Az eltolások ismert, és minden alkalmazás állapotának külső erőforrásfájlban, majd programozott módon állítsa le a megfelelő helyen streamelési alkalmazását. Egyik módszer az, hogy használja a "thread hurkokat" a Spark, ellenőrzésével egy külső jelzőt a minden *n* másodperc. Használhatja egy *jelölőfájl* , amely a HDFS létrehozott, az alkalmazás indításakor, akkor távolítja el, amikor le szeretné. A egy jelölő fájl módszert használja használjon egy külön szál a Spark-alkalmazás, amely meghívja a kód ehhez hasonló:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>További lépések

* [Spark Streamelés – áttekintés](apache-spark-streaming-overview.md)
* [A Spark Streamelési feladatok létrehozása pontosan – egyszeri esemény feldolgozása](apache-spark-streaming-exactly-once.md)
* [Hosszan futó Spark Streamelési feladatok a yarn RENDSZERÉN](http://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/) 
* [Strukturált Stream: Tartalék hibatűrő szemantika](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Diszkretizált Streamek: Egy hibatűrő modellt méretezhető Stream-feldolgozás](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
