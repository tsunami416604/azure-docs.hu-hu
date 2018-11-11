---
title: A Spark Streamelési feladatok létrehozása pontosan – egyszeri esemény feldolgozása – Azure HDInsight
description: Hogyan állítható be a Spark Streaming dolgozni egy eseményt, egyszer és csak egyszer.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 6c39eb02e9610e0020ab2abe8a192dabf0b768d9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51241316"
---
# <a name="create-spark-streaming-jobs-with-exactly-once-event-processing"></a>A Spark Streamelési feladatok létrehozása pontosan – egyszeri esemény feldolgozása

Stream-feldolgozó alkalmazások többféle hogyan azokat újra feldolgozási üzenetek kezeléséhez néhány hiba után a rendszer hajtsa végre:

* Legalább egyszer: üzenetek garantáltan dolgozható fel, de azt lehet feldolgozni egynél többször.
* Legfeljebb egyszer: Előfordulhat, hogy minden üzenetet, vagy nem dolgozható fel. Ha egy üzenet feldolgozása történik, azt csak egyszer dolgozza fel.
* Pontosan egyszer: üzenetek garantáltan egyszer és csak egyszer lehet feldolgozni.

Ez a cikk bemutatja, hogyan konfigurálhatja a Spark Streaming pontosan eléréséhez-egyszeri feldolgozását.

## <a name="exactly-once-semantics-with-spark-streaming"></a>Pontosan-egyszer szemantika a Spark Streaming

Először vegye figyelembe az összes, a rendszer pontokat újraindítása után probléma, és hogyan adatok elvesztését elkerülheti. A Spark Streaming alkalmazás rendelkezik:

* Egy bemeneti forrás
* Egy vagy több fogadó folyamatokat, amelyek a bemeneti forrásból származó adatok lekérése
* Feladatokat, amelyek az adatok feldolgozása
* Egy kimeneti fogadóba
* Egy illesztőprogram-folyamat, amely kezeli a hosszan futó feladat

Pontosan-szemantika megkövetelése, hogy bármikor adatvesztés, pedig a üzenetfeldolgozás újraindítható, függetlenül attól, ahol a hiba akkor fordul elő, ha.

### <a name="replayable-sources"></a>Replayable források

A Spark Streaming alkalmazás éppen olvas az események forrása lehet *replayable*. Ez azt jelenti, hogy azokban az esetekben, ahol az üzenet megtörtént, de majd a rendszer nem sikerült, mielőtt az üzenet volt maradnak meg vagy feldolgozott, a forrás kell megadnia ugyanazt az üzenetet újra.

Az Azure-ban az Azure Event Hubs és a Kafka on HDInsight biztosítanak replayable források. Egy másik példa replayable forrás és hibatűrő fájlrendszer, például HDFS, az Azure Storage-blobokkal, vagy az Azure Data Lake Store, ahol az összes adatért tartja, és bármikor, újra olvassa el ebben az esetben az adatok.

### <a name="reliable-receivers"></a>Megbízható fogadók

A Spark Streaming, adatforrások, az Event Hubs és a Kafka rendelkezik *megbízható fogadók*, ahol minden egyes fogadó nyomon követi az előrehaladási állapotát a forrás olvasása. A megbízható fogadó továbbra is fennáll az állapotában és hibatűrő tárolási, ZooKeeper belül vagy a Spark Streaming ellenőrzőpontok HDFS írt be. Ha például egy fogadó nem sikerül, és későbbi újraindítását, folytathatja a munkát, ahol abbahagyta.

### <a name="use-the-write-ahead-log"></a>Az írási előre bejelentkezési

Spark Streaming írási előre napló, ahol minden egyes fogadott események először írt ellenőrzőpont directory Spark-és hibatűrő tárolási és tárolja az egy rugalmas elosztott adatkészlet (RDD) használatát támogatja. Az Azure-ban a hibatűrő tárolási HDFS támogatásával az Azure Storage vagy az Azure Data Lake Store. Az a Spark Streaming-alkalmazás az írási előre napló engedélyezve van az összes fogadók beállításával a `spark.streaming.receiver.writeAheadLog.enable` a konfigurációs beállítás `true`. Az írási előre napló hibák az illesztőprogram és a végrehajtóval hibatűrést biztosít.

A munkavállalók feladatokat futtat az eseményadatokat minden RDD-definíció is replikálódnak, és a feldolgozó elosztva által van. Ha egy feladat sikertelen lesz, mivel a feldolgozó fut, lefagyott, a feladat egy másik feldolgozón, ahol az eseményadatok replikája újraindul, így az esemény, nem elveszett.

### <a name="use-checkpoints-for-drivers"></a>Az ellenőrzőpontok használata illesztőprogramok

A feladat illesztőprogramok kell újraindítható. Ha az illesztőprogram, a Spark Streaming alkalmazást futtató összeomlik, vesz igénybe azt minden futó fogadók, feladatok és bármely rdd-kként eseményadatok tárolására. Ebben az esetben meg kell tudni menteni a feladat állapotát, így később folytathatja. Mindez ellenőrzőpontok használata által az irányított aciklikus Graph (DAG), a rendszeres időközönként, és hibatűrő tárolási DStream. A DAG-metaadatokban létrehozásához használt az adatfolyam-továbbítási alkalmazást, a műveleteket, meghatározzák az alkalmazást, és bármely kötegek, amelyek várólistára, de még nem fejezték be a konfigurációt. A metaadatok lehetővé teszi, hogy újra kell indítani a checkpoint alapján sikertelen illesztőprogramot. Az illesztőprogram újraindítását követően, hogy magukat az adat-helyreállítást esemény újra üzembe a írási előre naplóból rdd-k új fogadók indítja el.

Az ellenőrzőpontok a Spark Streaming két lépésben engedélyezve vannak. 

1. A StreamingContext objektumban adja meg az ellenőrzőpontok tároló elérési útja:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    A HDInsight az alapértelmezett tároló csatlakozik a fürthöz, az Azure Storage vagy az Azure Data Lake Store ezeket az ellenőrzőpontokat kell menteni.

2. Ezután a DStream adjon meg egy ellenőrzőpont időköz (másodpercben). Időközönként a bemeneti eseményt származó állapotadatokat megőrzi a storage. Megőrzött állapot adatait csökkentheti a számítási van szükség, amikor az állapot, a forrás-eseményből származó újraépítése.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Idempotens fogadóként használni

A cél fogadó, amelyhez a feladat eredménye ír tudja kezelni a helyzetet, ahol ellátja azt ugyanaz az eredmény csak egyszer kell lennie. A fogadó ilyen ismétlődő eredményeket, és figyelmen kívül hagyja azokat képesnek kell lennie. Egy *idempotens* fogadó nem hívható meg többször állapotának módosítása nélkül az adatokról.

Logika, amely először ellenőrzi, hogy létezik-e az adattárhoz bejövő eredményez az életbe léptetésével idempotens fogadóként is létrehozhat. Ha már létezik az eredmény, az írási meg kell jelennie az internetböngésző szempontjából a Spark-feladat sikeres, de a valóságban az adattárhoz figyelmen kívül hagyja az ismétlődő adatokat. Ha az eredmény nem létezik, majd a fogadó kell beszúrni új eredmény tárhelyét. 

Például kreditért igénybe Azure SQL Database-események szúr be egy tábla egy tárolt eljárást. Az esemény kulcsmezők, és csak akkor, ha nem található egyező esemény a rekord a táblába beszúrt nem először megkeresi ezt a tárolt eljárást.

Egy másik példa, particionált fájlrendszer, mint az Azure Storage-blobok, illetve az Azure Data Lake store használata. Ebben az esetben a fogadó logika nem kell ellenőriznie a fájl létezik-e. Ha a fájlt, amely az esemény létezik, egyszerűen felül az adatokról. Ellenkező esetben egy új fájl jön létre számított elérési úton.

## <a name="next-steps"></a>További lépések

* [Spark Streamelés – áttekintés](apache-spark-streaming-overview.md)
* [Magas rendelkezésre állású Spark Streamelési feladatok létrehozása a YARN-ban](apache-spark-streaming-high-availability.md)
