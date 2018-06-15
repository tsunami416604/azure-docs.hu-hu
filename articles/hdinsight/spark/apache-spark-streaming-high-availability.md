---
title: Hozzon létre magas rendelkezésre állású Spark Streaming feladatok a YARN - Azure HDInsight |} Microsoft Docs
description: Hogyan állítható be a Spark Streaming egy magas rendelkezésre állású forgatókönyv esetén.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ramoha
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: ramoha
ms.openlocfilehash: bbb4da02cbe4b0685c715c4cd6bd7b15c6b5cce0
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165315"
---
# <a name="create-high-availability-spark-streaming-jobs-with-yarn"></a>Hozzon létre magas rendelkezésre állású Spark Streaming feladatok YARN

Spark Streaming lehetővé teszi a méretezhető, nagy átviteli, hibatűrő alkalmazások feldolgozásakor az adatfolyamok végrehajtásához. Spark Streaming alkalmazások HDInsight Spark-fürtön csatlakozhat számos különféle adatforrások, például az Azure Event Hubs, Azure IoT Hub, Kafka, Flume, Twitter, ZeroMQ, nyers TCP-szoftvercsatornák vagy figyeli a változásokat a HDFS fájlrendszer. Spark Streaming támogatja a garantált, hogy bármely adott esemény feldolgozása pontosan egyszer, akár egy Csomóponthiba a hibatűrés.

Spark Streaming hoz létre a hosszan futó feladatok során, amelyek képesek végrehajthatók az adatokat, és majd leküldéses fájlrendszerek, adatbázisok, az irányítópultok és a konzol az eredményeket. Spark Streaming dolgozza fel a micro-kötegek az adatok összegyűjtésével első események köteg egy megadott időszakban. Ezt követően, hogy a kötegelt továbbítja a feldolgozás és a kimeneti. Kötegelt időközök általában egy második hányada vannak definiálva.

![Spark Streaming](./media/apache-spark-streaming-high-availability/spark-streaming.png)

## <a name="dstreams"></a>DStreams

Spark Streaming jelöli állandó folyama miatt adatokat egy *diszkretizált adatfolyam* (DStream). A DStream bemeneti forrásból, mint az Event Hubs vagy Kafka, vagy a másik DStream átalakítások alkalmazásával hozhatók létre. Az esemény a Spark Streaming kérelem érkezik, az esemény tárolja egy megbízható módon. Ez azt jelenti, hogy az eseményadatok replikálódik, hogy több csomópont egy példányát. Ez biztosítja, hogy bármely egyetlen csomópont hibája nem esemény elvesztését eredményezi.

A Spark mag használ *rugalmas adatkészleteket elosztott* (RDDs). RDDs adatok szét több csomópont, a fürtben, amelyben minden csomópont általában végzi az adatok teljesen memórián belüli a legjobb teljesítmény érdekében. Minden egyes RDD egy kötegelt időszakban összegyűjtött események jelöli. A kötegelt időtartam, amikor egy adott időszakban az adatokat tartalmazó új RDD Spark Streaming eredményez. A folyamatos készlete RDDs be egy DStream gyűjti. Spark Streaming alkalmazás dolgozza fel az egyes kötegekben RDD tárolt adatok.

![Spark DStream](./media/apache-spark-streaming-high-availability/DStream.png)

## <a name="spark-structured-streaming-jobs"></a>Spark strukturált folyamatos átviteli feladatok

Spark strukturált Streaming bevezetett Spark 2.0, elemzési motor folyamatos strukturált adatok számára. Spark strukturált Streaming használja a kötegelés motor API-k sparksql-hez. Csakúgy, mint a Spark Streaming, Spark strukturált Streaming fut, a számítások micro-kötegek folyamatosan érkező adatok keresztül. Adatok adatfolyam Spark strukturált Streaming jelöli egy bemeneti tábla korlátlan sorait. Ez azt jelenti, hogy a bemeneti tábla továbbra is nő, ahogy új adat érkezik. A bemeneti táblázat folyamatosan hosszú ideig futó lekérdezés dolgoz fel, és az eredményeket egy táblázatot írt.

![Spark Streaming strukturált](./media/apache-spark-streaming-high-availability/structured-streaming.png)

Strukturált adatfolyam adatok megérkezik a rendszer, és azonnal van okozhatnak a bemeneti táblázatban. A bemeneti táblázaton műveleteket lekérdezéseket írhat. A lekérdezés egy másik táblát, az eredmények táblázatában elő. Az eredmények táblázatában tartalmazza a lekérdezést, ahol egy külső adattárral küldött adatok megrajzolásához eredményei egy relációs adatbázisban. A *eseményindító időköz* állítja be az időzítést, amikor a bemeneti tábla adatok feldolgozása a. Alapértelmezés szerint strukturált Streaming dolgozza fel az adatokat, amint megérkeznek. Azonban beállíthatja úgy is, a streamelési adatok feldolgozása a kötegek időalapú több idő jusson, futtassa az eseményindító. Az adatok az eredmények táblázatában előfordulhat, hogy teljesen frissíteni kell minden alkalommal, amikor nincs elérhető új adat, hogy tartalmazza a kimeneti adatok a folyamatos átviteli lekérdezés kezdete óta (*teljes mód*), vagy csupán azokat az adatokat, amelyek a legutóbbi új csak tartalmaz a lekérdezés feldolgozása volt idő (*hozzáfűzéssel*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Spark Streaming hibatűrő feladatok létrehozása

A Spark Streaming-feladatok a magas rendelkezésre állású környezet létrehozása, először meghibásodása esetén a helyreállításhoz az egyes feladatok kódolása. Ilyen önálló helyreállított feladatok olyan hibatűrő.

RDDs rendelkezik, amely segítséget nyújt a magas rendelkezésre állású és hibatűrő Spark Streaming feladatok több tulajdonságok:

* Kötegek, bemeneti adatokat egy DStream RDDs tárolja a rendszer automatikusan replikálja a memóriában hibatűrést.
* Mindaddig, amíg az adott munkavégző csomópontokat miatti worker elveszett adatok is recomputed különböző munkavállalók replikált bemeneti adatokból.
* Gyors tartalék helyreállítási hibák vagy elvetésének helyreállítás történik, a memóriában számítási keresztül belül egy második, akkor fordulhat elő.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Pontosan-egyszer a Spark Streaming szemantikájának módosítása

Létrehoz egy alkalmazást, amely feldolgozza az eseményekhez, miután (és csak egyszer), érdemes lehet hogyan meghibásodási ponttal minden rendszer újraindítása után egy játékhoz, és hogyan adatvesztés elkerülése érdekében. Pontosan-szemantikáját szükséges, hogy adatok nem vesztek bármikor, és a üzenet feldolgozása újraindítható, függetlenül attól, ahol a hiba akkor fordul elő, ha. Lásd: [Spark Streaming hozzon létre feladatokat a pontosan-egyszer esemény feldolgozása](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-yarn"></a>Spark Streaming és YARN

A HDInsight fürt munkahelyi koordinálnia *még egy másik erőforrás egyeztető* (YARN). Magas rendelkezésre állás kialakítása a Spark Streaming tartalmazza a technikák, a Spark Streaming és YARN összetevők.  Az alábbiakban látható egy példa konfiguráció YARN használatával. 

![YARN architektúra](./media/apache-spark-streaming-high-availability/yarn-arch.png)

A következő szakaszok ismertetik tervezéséhez kapcsolódó szempontokat tartalmazza ezt a konfigurációt.

### <a name="plan-for-failures"></a>Hibák tervezése

A magas rendelkezésre állású YARN konfiguráció létrehozásához meg kell terveznie a lehetséges végrehajtó vagy illesztőprogram hibát. Spark Streaming feladatok szükséges további konfigurációs és a telepítési adatok garancia követelményeket is. Egy adatfolyam-továbbítási alkalmazást Előfordulhat például, annak ellenére, hogy semmilyen hiba lép fel a gazdarendszer adatfolyam-továbbítási vagy a HDInsight-fürt garantálni vállalati követelmények miatt egy nulla--adatvesztés.

Ha egy **végrehajtó** meghiúsul, a feladatok és a fogadó újraindul Spark által automatikusan, így nincs szükség konfigurációs változás.

Azonban ha egy **illesztőprogram** nem sikerül, akkor a társított végrehajtója összes sikertelen, és minden beérkezett blokkok és számítási eredmények elvesznek. Egy illesztőprogram-hiba helyreállításához használja *DStream ellenőrzőpontok* leírtak [Spark Streaming hozzon létre feladatokat a pontosan-egyszer esemény feldolgozása](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). DStream ellenőrzőpontok időnként a *irányított aciklikus diagramhoz* (DAG) a hibatűrő tárhelyen, többek között a Azure Storage DStreams.  Ellenőrzőpontok használata lehetővé teszi, hogy a Spark strukturált Streaming az ellenőrzőpont-adatok sikertelen illesztőprogram újraindítására.  Az illesztőprogram újraindítás új végrehajtója elindul, és a fogadók is újraindul.

Illesztőprogramok DStream ellenőrzőpontok helyreállításához:

* A konfigurációs beállítás konfigurálása illesztőprogram automatikus újraindítása a yarn RENDSZERÉN `yarn.resourcemanager.am.max-attempts`.
* Egy ellenőrzőpont könyvtár beállítása a HDFS-kompatibilis fájl rendszerben `streamingContext.checkpoint(hdfsDirectory)`.
* Csökkentse a számukat a forráskód ellenőrzőpontot használja a helyreállításhoz, például:

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

* Az írási előre napló (WAL) engedélyezése az elveszett adatok helyreállításának konfigurálása `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")`, és a bemeneti DStreams memórián belüli replikáció letiltása `StorageLevel.MEMORY_AND_DISK_SER`.

Összefoglalva, ellenőrzőpontok + WAL + megbízható fogadók használva lesz képes biztosítani a "legalább egyszeri" adat-helyreállítás:

* Ha pontosan, mindaddig, amíg fogadott legyen adatvesztés, és a kimeneti vagy idempotent vagy tranzakciós.
* Pontosan egyszer az új Kafka közvetlen módszert használó Kafka replikált napló, hanem fogadók vagy WALs.

### <a name="typical-concerns-for-high-availability"></a>A magas rendelkezésre állás jellemző vonatkozik

* Azonban nehezebb a folyamatos átviteli feladat, mint a kötegelt feladatok figyelése. Spark Streaming feladatok általában hosszan futó, és YARN naplók nem összesíteni, amíg a feladat befejeződik.  Alkalmazás vagy a Spark frissítés során elvesznek Spark ellenőrzőpontokat, és akkor törölnie kell az ellenőrzőpont-könyvtár frissítése során.

* A YARN fürt mód illesztőprogramok futtatásához, még akkor is, ha egy ügyfél nem tud konfigurálása. Az illesztőprogramok automatikus újraindítást beállítása:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark és a Spark Streaming felhasználói felületén konfigurálhatók metrikák rendszerrel kell rendelkeznie. Is használhatja, további könyvtárak, például a grafit/Grafana irányítópult metrikák letölteni, például a "num rekordok feldolgozott", "Memória/GC használat illesztőprogram & végrehajtója", "teljes késleltetés", "kihasználtságát a fürt" és így tovább. A strukturált Streaming 2.1-es vagy újabb verziója, használható `StreamingQueryListener` további metrikák gyűjtéséhez.

* Hosszan futó feladatot kell szegmentálhatja.  Amikor Spark Streaming alkalmazás a fürthöz, a YARN várólista, ahol a feladat futtatásakor meg kell határozni. Használhatja a [YARN kapacitás Feladatütemező](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) várólisták külön hosszú ideig futó feladatok küldéséhez.

* Az adatfolyam-továbbítási alkalmazást szabályosan le. Ha a eltolások ismert, és minden alkalmazás-állapot tárolása kívülről, majd programozott módon leállíthatja az adatfolyam-továbbítási alkalmazás megfelelő helyén. Egyik módszer, hogy használja a "a szál hurkok" Spark, ellenőrzésével egy külső jelzőhöz minden *n* másodperc. Használhatja a *jelzőfájl* , amely az alkalmazás indításakor HDFS létrehozni, akkor távolítja el, amikor le kívánja állítani. A jelölő fájl módszert használja a külső alkalmazás-t meghívó kód hasonló külön szálban használja:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>További lépések

* [Spark Streaming áttekintése](apache-spark-streaming-overview.md)
* [A Spark Streaming feladatok pontosan létrehozása – egyszer esemény feldolgozása](apache-spark-streaming-exactly-once.md)
* [A folyamatos átviteli feladatok a yarn RENDSZERÉN hosszan futó Spark](http://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/) 
* [Strukturált Streaming: Tartalék hibatűrő szemantikáját](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Diszkretizált adatfolyamok: Hibatűrő modellt méretezhető adatfolyam feldolgozása](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
