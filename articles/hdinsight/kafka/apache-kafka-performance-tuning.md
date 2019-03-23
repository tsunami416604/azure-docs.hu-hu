---
title: Az Apache Kafka HDInsight-fürtök esetén a teljesítmény optimalizálása
description: Technikák áttekintést nyújt az Azure HDInsight az Apache Kafka-munkaterhelések optimális.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/21/2019
ms.openlocfilehash: 903cd8921801ffb47dd73f48e507f30aa0b6dccc
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373150"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Az Apache Kafka HDInsight-fürtök esetén a teljesítmény optimalizálása

Ez a cikk biztosít a HDInsight az Apache Kafka számítási feladatok teljesítményének optimalizálásához néhány javaslatot. A fókusz a előállítói és a közvetítő konfigurációs van. A teljesítmény méréséhez különböző módon, és a alkalmazni optimalizálás az üzleti igényeknek megfelelően függ.

## <a name="architecture-overview"></a>Az architektúra áttekintése

Kafka-témakörökhöz rekordok szervezésére szolgálnak. Rekordok gyártók által előállított, és a fogyasztók által igénybe vett. Gyártók küldése rekordok Kafka-közvetítőkhöz majd az adatok tárolásához. A HDInsight-fürt mindegyik feldolgozó csomópontja egy Kafka-közvetítő.

A témakörök particionálják a rekordokat a közvetítők között. A rekordok felhasználásakor partíciónként legfeljebb egy fogyasztó használható, az adatok párhuzamos feldolgozása érdekében.

Replikáció segítségével partíciók ismétlődő csomópontok között. Ez védelmet nyújt a csomópontok (közvetítők) leállások ellen. Egy adott partíció között a csoport replikák partíció vezetőként van kijelölve. Az előállítói forgalmat a csomópontok vezetőjéhez irányítja a rendszer a ZooKeeper által kezelt állapot segítségével.

## <a name="identify-your-scenario"></a>A szituáció azonosítása

Az Apache Kafka teljesítmény két fő szempontja – átviteli sebességgel és késéssel van. Átviteli sebesség a maximális sebesség adatok feldolgozhatók. Nagyobb átviteli sebesség általában jobb. Késései tárolt vagy lekért adatok szükséges időt. Kisebb késés általában jobb. Keresés, az átviteli sebesség, késés és az alkalmazás-infrastruktúra költségei közötti egyensúlyt kihívást jelenthet. A teljesítményre vonatkozó követelmények valószínűleg megegyeznek a következő három gyakori helyzet, nagy átviteli sebességet, közel valós idejű vagy mindkettő szükségességének alapján egyikét:

* Nagy teljesítményű, kis késésű. Ebben a forgatókönyvben a nagy átviteli sebességet és közel valós idejű (~ 100 ezredmásodperc) is szükség van. Ez az alkalmazástípus egy példát a szolgáltatás rendelkezésre állásának figyelésére szolgáló.
* Nagy teljesítményű, nagy a késés. Ebben a forgatókönyvben nagy átviteli sebességű (~1.5 GB/s) igényel, de nagyobb késést (< 250 ms) működését. Ez az alkalmazástípus egy példa, telemetriai adatok adatok gyűjtése a közel valós idejű folyamatok például a security és behatolásérzékelés észlelési alkalmazásokat.
* Az alacsony átviteli sebesség, kis késés. Ebben a forgatókönyvben alacsony késés (< 10 ms) igényel a valós idejű feldolgozásra, de tűri alacsonyabb átviteli sebességet. Ez az alkalmazástípus egyik példája online nyelvhelyesség -ellenőrzést.

## <a name="producer-configurations"></a>Gyártó konfigurációk

A következő szakaszok fog kiemelnek néhányat a legfontosabb konfigurációs tulajdonságok a Kafka gyártók teljesítmény optimalizálása érdekében. Az összes konfigurációs tulajdonság részletes leírását lásd: [Apache Kafka dokumentációja előállítói konfigurációkban](https://kafka.apache.org/documentation/#producerconfigs).

### <a name="batch-size"></a>Köteg mérete

Az Apache Kafka gyártók csoportok (úgynevezett kötegek) egy egységként kell menteni az egyetlen tárfiókba partíció küldött üzenetek összeállításához. Kötegméret kell jelen lennie ahhoz, hogy a csoport továbbított bájtok számát jelenti. Növelje a `batch.size` paraméter növeléséhez, mivel ez csökkenti a protokollterhelés a hálózat és az i/o-kérések feldolgozását. Világos terhelés alatt megnövekedett köteg mérete növelhető Kafka küldési késleltetés, készen áll a batch megvárja, amíg az előállítói. Nagy terhelés alatt azt javasoljuk, hogy növelje a kötegméret átviteli sebességgel és késéssel javítása érdekében.

### <a name="producer-required-acknowledgements"></a>Nyugtázás a gyártó szükséges

Az előállítói szükséges `acks` konfigurációja határozza meg, hogy egy írása előtt a partíció vezető szükséges nyugták befejeződött. Ez a beállítás hatással van az adatok megbízhatóságának és értékeket vesz igénybe `0`, `1`, vagy `-1`. Az érték `-1` azt jelenti, hogy az összes replika nyugtázást kell érkeznie, az írási befejezése előtt. Beállítás `acks = -1` nyújt adatvesztés, de elleni garantálható is eredmények nagyobb késést és alacsonyabb átviteli sebességet. Ha az alkalmazás követelményeinek nagyobb átviteli sebességet igényelnek, próbálja meg a beállítás `acks = 0` vagy `acks = 1`. Ne feledje, hogy nem az összes replika bosszankodnak csökkentheti az adatok megbízhatóságának.

### <a name="compression"></a>Tömörítés

A Kafka producer üzenetek tömörítendő közvetítők küldés előtt konfigurálható. A `compression.type` beállítás határozza meg a tömörítési kodek használható. Támogatott tömörítési kodek "gzip," "snappy," és "lz4." Tömörítés előnyös, és figyelembe kell venni, ha egy korlátozás a lemezkapacitást.

A két gyakran használt tömörítési kodek között `gzip` és `snappy`, `gzip` rendelkezik egy újabb tömörítési aránya, amely alacsonyabb lemezhasználat cserébe magasabb CPU-terhelést eredményez. A `snappy` kodek kevesebb tömörítést kisebb biztosít. Eldöntheti, melyik használandó kodek alapján broker lemez vagy az előállítót CPU korlátozások. `gzip` adatok ötször nagyobb gyakorisággal lehet tömörítése `snappy`.

Az Adattömörítés használata megnöveli a lemezen tárolt rekordok száma. Azt is növelheti CPU többletterhelést azokban az esetekben, ahol a az előállítói és a közvetítő által használt tömörítési formátumok közötti eltérés van. az adatok elküldése előtt tömörített kell kell, és majd kibontani a feldolgozás előtt.

## <a name="broker-settings"></a>Munkamenet-átvitelszervező-beállítások

A következő szakaszok ki vannak emelve a Kafka-közvetítőkhöz a teljesítmény optimalizálása érdekében a legfontosabb beállítások némelyike. Az összes részletes magyarázatát beállítások közvetítse, lásd: [Apache Kafka dokumentációja előállítói konfigurációkban](https://kafka.apache.org/documentation/#producerconfigs).


### <a name="number-of-disks"></a>Lemezek száma

Storage-lemez iops-t (bemeneti/kimeneti műveletek másodpercenként) korlátozott, és olvasási/írási bájt / másodperc. Új partíciókat hoznak létre, amikor a Kafka minden egyes új partíció tárolja a a lemezen a legkevesebb meglévő partíciók kiegyenlítése azokat az elérhető lemezek között. Annak ellenére, hogy a tárolási stratégia, az egyes lemezek partícióreplikák több száz feldolgozásakor a Kafka egyszerűen telítsük az elérhető lemez átviteli sebességet. Itt az egyensúlyt a teljesítmény és költség között van. Ha az alkalmazás nagyobb adatátviteli kapacitást igényel, hozzon létre egy fürtöt több felügyelt lemezek közvetítőnként. HDInsight jelenleg nem támogatja a felügyelt lemezek hozzáadása egy futó fürt. További információ a felügyelt lemezek számának konfigurálásához: [a HDInsight-beli Apache kafka tárolójának és skálázhatóságának konfigurálása](apache-kafka-scalability.md). A fürtben található csomópontok növekvő tárhely költség megszegéseinek.

### <a name="number-of-topics-and-partitions"></a>Témák és partíciók száma

A Kafka gyártók témaköröket írni. A Kafka-fogyasztók témakörök olvasni. A témakör a napló, amely a lemezen lévő adatstruktúra társul. A Kafka fűzi hozzá a témakör a napló végére egy gyártó származó rekordokat. A témakör a napló futó több fájl sok partíció áll. Ezeket a fájlokat, vannak, a Kafka-fürt több csomópontján elosztva. A fogyasztók olvasni azok kiadása ütemben történik, a Kafka-témaköröket és és ki tudja választani a témakör naplóban pozíciójukat (eltolásnak).

Mindegyik Kafka partíció egy naplófájlt a rendszer, és termelő szálak írhat több naplók egyszerre. Hasonlóképpen mivel minden egyes felhasználói szál üzeneteket olvas az egyik partíció, felhasználása több partícióról származó történik párhuzamosan is.

Növelje a partíció sűrűsége (közvetítőnként partíciók száma) hozzáadása egy metaadat-műveletek és partíció kérés/válasz között a partíció vezető, a követők száma kapcsolódó terhelés. Még az adatok áramlása hiányában partícióreplikák továbbra is adatok beolvasása a vezetői, amely a küldési extra feldolgozási eredményez, és a hálózaton keresztül küldött fogadási kérelmekre küld.

Az Apache Kafka-fürtök 1.1-es és újabb a HDInsight, azt javasoljuk, hogy legfeljebb 1000 partíciók közvetítőnként, beleértve a replikák. Közvetítőnként partíciók számának növelésével csökkenti az átviteli sebesség és a témakör elérhetetlensége is okozhatják. A Kafka partíció-támogatást további információkért lásd: [a hivatalos Apache Kafka-blogbejegyzés az 1.1.0-s verzió támogatott partíciók számának növekedése](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). További témakörök módosításával kapcsolatos információkért lásd: [Apache Kafka: módosítja a témakörök](https://kafka.apache.org/documentation/#basic_ops_modify_topic).

### <a name="number-of-replicas"></a>Replikák száma

Magasabb szintű replikációs tényező a partíció vezető, a követőinek között további kéréseket eredményez. Ennek következtében, magasabb szintű replikációs tényezőt használ fel, több lemez- és CPU további kérések kezelésére növelése írási késést és az átviteli sebesség növekedését.

Azt javasoljuk, hogy az Azure HDInsight Kafka legalább 3 x replikációs használjon. A legtöbb Azure-régióban kell három tartalék tartományt, de a felhasználók csak két tartalék tartomány régiókban, 4 x replikációs kell használnia.

Replikációs kapcsolatban lásd: [Apache Kafka: replikációs](https://kafka.apache.org/documentation/#replication) és [Apache Kafka: növelje a replikációs tényező](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor).

## <a name="next-steps"></a>További lépések

* [Napi több trillió esemény feldolgozása az Apache Kafka on Azure használatával](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Mi az Apache Kafka on HDInsight?](apache-kafka-introduction.md)
