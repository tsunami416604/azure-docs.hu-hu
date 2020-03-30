---
title: Spark Streaming & pontosan egyszeri eseményfeldolgozás - Azure HDInsight
description: Az Apache Spark Streaming beállítása egy esemény egyszeri és egyszeri feldolgozásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/15/2018
ms.openlocfilehash: ee4f9b84e822cb370e5fe3d55fcceb9c8a9f2ab9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74228971"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Hozzon létre Apache Spark Streaming feladatokat pontosan egyszeri eseményfeldolgozással

Stream feldolgozó alkalmazások különböző megközelítéseket, hogyan kezelik az üzenetek et, miután néhány hiba a rendszerben:

* Legalább egyszer: Minden üzenet feldolgozása garantált, de előfordulhat, hogy a rendszer többször is feldolgozza.
* Legfeljebb egyszer: Minden üzenet feldolgozható vagy nem. Ha egy üzenetet feldolgoz, az csak egyszer kerül feldolgozásra.
* Pontosan egyszer: Minden üzenet garantáltan egyszer és csak egyszer kerül feldolgozásra.

Ez a cikk bemutatja, hogyan konfigurálhatja a Spark Streaming pontosan egyszeri feldolgozás eléréséhez.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Pontosan egyszeri szemantika az Apache Spark Streaming segítségével

Először gondolja át, hogyan indul újra az összes rendszerhiba-pont a probléma után, és hogyan kerülheti el az adatvesztést. A Spark Streaming alkalmazás:

* Bemeneti forrás.
* Egy vagy több fogadó folyamat, amely adatokat a bemeneti forrásból lekéri.
* Az adatokat feldolgozó feladatok.
* Egy kimeneti mosogató.
* A hosszú ideig futó feladatot kezelő illesztőprogram-folyamat.

A pontosan egyszeri szemantika megköveteli, hogy egyetlen adat se vesszen el, és hogy az üzenetfeldolgozás újraindítható, függetlenül attól, hogy hol fordul elő a hiba.

### <a name="replayable-sources"></a>Újrajátszható források

A forrás a Spark Streaming alkalmazás olvassa az eseményeket kell *replayable.* Ez azt jelenti, hogy azokban az esetekben, amikor az üzenetet beolvasták, de aztán a rendszer meghibásodott, mielőtt az üzenet megőrizhető vagy feldolgozható lett volna, a forrásnak újra meg kell adnia ugyanazt az üzenetet.

Az Azure-ban az Azure Event Hubs és az [Apache Kafka](https://kafka.apache.org/) a HDInsight-on is biztosít visszajátszási forrásokat. Egy másik példa a visszajátszható forrás egy hibatűrő fájlrendszer, például [az Apache Hadoop HDFS,](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)az Azure Storage blobok vagy az Azure Data Lake Storage, ahol az összes adatot örökre tárolják, és bármikor újra olvashatja az adatokat teljes egészében.

### <a name="reliable-receivers"></a>Megbízható vevőkészülékek

A Spark Streaming, források, például az Event Hubs és a Kafka *megbízható vevő,* ahol minden vevő nyomon követi a folyamat a forrás olvasása. A megbízható vevő állapotának megőrzése hibatűrő tároló, akár [az Apache ZooKeeper,](https://zookeeper.apache.org/) vagy a Spark Streaming ellenőrzőpontok hdfs-re írt. Ha egy ilyen vevő meghibásodik, és később újraindul, akkor ott folytathatja, ahol abbahagyta.

### <a name="use-the-write-ahead-log"></a>A "Írás előre" napló használata

A Spark Streamelés támogatja a Write-Ahead napló használatát, ahol minden egyes fogadott esemény először a Spark ellenőrzőpont-címtárába kerül a hibatűrő tárolóban, majd egy rugalmas elosztott adatkészletben (RDD) tárolódik. Az Azure-ban a hibatűrő tároló HDFS az Azure Storage vagy az Azure Data Lake Storage által támogatott. A Spark Streaming alkalmazásban a Write-Ahead Log minden fogadószámára `spark.streaming.receiver.writeAheadLog.enable` engedélyezve `true`van a konfigurációs beállítás beállításával. Az előre írási napló hibatűrést biztosít mind az illesztőprogram, mind a végrehajtók hibáihoz.

Az eseményadatokon futó dolgozók esetében minden RDD definíció szerint replikált és több dolgozó között oszlik meg. Ha egy feladat sikertelen, mert a dolgozó, aki azt futtató összeomlott, a feladat újraindul egy másik dolgozó, amely rendelkezik az esemény adatok replikáját, így az esemény nem vész el.

### <a name="use-checkpoints-for-drivers"></a>Ellenőrzőpontok használata az illesztőprogramok számára

A feladat-illesztőprogramokat újra kell indítani. Ha a Spark Streaming-alkalmazást futtató illesztőprogram összeomlik, az összes futó fogadók, feladatok és az eseményadatokat tároló RDD-k leveszi. Ebben az esetben képesnek kell lennie a feladat előrehaladásának mentésére, hogy később folytathassa. Ez úgy érhető el, ellenőrzőpontok a irányított aciklikus gráf (DAG) a DStream rendszeresen a hibatűrő tároló. A DAG metaadatok közé tartozik a streamelési alkalmazás létrehozásához használt konfiguráció, az alkalmazást meghatározó műveletek, valamint a várólistára helyezett, de még be nem fejezett kötegek. Ez a metaadat lehetővé teszi, hogy egy sikertelen illesztőprogram újrainduljon az ellenőrzőpont adataiból. Amikor az illesztőprogram újraindul, új vevőket indít el, amelyek maguk is helyreállítja az eseményadatokat az RDD-kbe a Write-Ahead naplóból.

Ellenőrzőpontok vannak engedélyezve a Spark Streaming két lépésben engedélyezett.

1. A StreamingContext objektumban konfigurálja az ellenőrzőpontok tárolási útvonalát:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    A HDInsightban ezeket az ellenőrzőpontokat a fürthöz csatlakoztatott alapértelmezett tárolóra kell menteni, akár az Azure Storage-ba, akár az Azure Data Lake Storage-ba.

2. Ezután adjon meg egy ellenőrzőpont-időközt (másodpercben) a DStreamen. Minden egyes időközönként a bemeneti eseményből származó állapotadatok a tárolóban maradnak. A megőrzött állapotadatok csökkenthetik a forrásesemény állapotának újraépítésekor szükséges számításokat.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Idempotens mosogatók használata

A célfogadónak, amelyre a feladat az eredményeket írja, képesnek kell lennie arra, hogy kezelje azt a helyzetet, amelyben ugyanazt az eredményt többször kapta meg. A fogadónak képesnek kell lennie az ilyen ismétlődő eredmények észlelésére, és figyelmen kívül kell hagynia azokat. Egy *idempotens* fogadó lehet hívni többször ugyanazzal az adattal, állapotváltozás nélkül.

Idempotens fogadók hozhat létre olyan logika megvalósításával, amely először ellenőrzi a bejövő eredmény létezését az adattárban. Ha az eredmény már létezik, az írási kell, hogy sikeresnek tűnjön a Spark-feladat szempontjából, de a valóságban az adattár figyelmen kívül hagyta az ismétlődő adatokat. Ha az eredmény nem létezik, majd a fogadó kell beszúrnia ezt az új eredményt a tárolóba.

Például használhat egy tárolt eljárást az Azure SQL Database használatával, amely eseményeket illeszt be egy táblába. Ez a tárolt eljárás először kulcsmezők szerint keresi meg az eseményt, és csak akkor, ha nem található egyező esemény a táblába szúrt rekord.

Egy másik példa egy particionált fájlrendszer használata, például az Azure Storage blobok vagy az Azure Data Lake Storage. Ebben az esetben a fogadó logika nem kell ellenőriznie a fájl létezését. Ha az eseményt képviselő fájl létezik, a rendszer egyszerűen felülírja ugyanazokkal az adatokkal. Ellenkező esetben egy új fájl jön létre a számított elérési úton.

## <a name="next-steps"></a>További lépések

* [Apache Spark streamelés – áttekintés](apache-spark-streaming-overview.md)
* [Magas rendelkezésre állású Apache Spark Streaming-feladatok létrehozása az Apache Hadoop YARN-ban](apache-spark-streaming-high-availability.md)
