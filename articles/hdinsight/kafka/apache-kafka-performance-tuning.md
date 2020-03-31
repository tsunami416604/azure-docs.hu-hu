---
title: Apache Kafka HDInsight-fürtök teljesítményoptimalizálása
description: Áttekintést nyújt az Apache Kafka számítási feladatok azure HDInsight-on való optimalizálásának technikáiról.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/19/2019
ms.openlocfilehash: 752068af531c4a0ecc832d266f88105c14452ecb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75494922"
---
# <a name="performance-optimization-for-apache-kafka-hdinsight-clusters"></a>Apache Kafka HDInsight-fürtök teljesítményoptimalizálása

Ez a cikk néhány javaslatot ad az Apache Kafka számítási feladatok teljesítményének optimalizálására a HDInsightban. A hangsúly a gyártó és a bróker konfigurációjának beállításán van. A teljesítmény mérésének különböző módjai vannak, és az alkalmazott optimalizálások az üzleti igényektől függenek.

## <a name="architecture-overview"></a>Az architektúra áttekintése

A Kafka témakörök a rekordok rendszerezésére szolgálnak. A rekordokat előállítók hozzák létre, és fogyasztók használják fel. A gyártók rekordokat küldenek a Kafka brókereknek, amelyek aztán tárolják az adatokat. A HDInsight-fürt mindegyik feldolgozó csomópontja egy Kafka-közvetítő.

A témakörök particionálják a rekordokat a közvetítők között. A rekordok felhasználásakor partíciónként legfeljebb egy fogyasztó használható, az adatok párhuzamos feldolgozása érdekében.

A replikáció a csomópontok közötti partíciók duplikálására szolgál. Ez védelmet nyújt a csomópont (közvetítő) kimaradások ellen. A replikák csoportjában egyetlen partíció van kijelölve a partícióvezetőjeként. Az előállítói forgalmat a csomópontok vezetőjéhez irányítja a rendszer a ZooKeeper által kezelt állapot segítségével.

## <a name="identify-your-scenario"></a>A szituáció azonosítása

Az Apache Kafka teljesítményének két fő aspektusa van: az átviteli teljesítmény és a késleltetés. Az átviteli sebesség az adatok feldolgozásának maximális sebessége. A nagyobb átviteli átmenő általában jobb. A késés az az idő, amíg az adatok tárolása vagy beolvasása szükséges. Az alacsonyabb késleltetés általában jobb. Az átviteli képesség, a késés és az alkalmazás infrastruktúrájának költsége közötti megfelelő egyensúly megtalálása kihívást jelenthet. A teljesítménykövetelmények valószínűleg megfelelnek az alábbi három gyakori helyzet egyikének, attól függően, hogy nagy átviteli teljesítményre, alacsony késésre vagy mindkettőre van-e szükség:

* Nagy átviteli sebességű, alacsony késleltetésű. Ebben a forgatókönyvben nagy átviteli és alacsony késésű (~100 ezredmásodperc) szükséges. Az ilyen típusú alkalmazás egy példa a szolgáltatás rendelkezésre állásának figyelése.
* Nagy átviteli sebességű, nagy késleltetésű. Ebben a forgatókönyvben nagy átviteli sebességre (~1,5 GBps) van szükség, de nagyobb (< 250 ms) is képes elviselni. Az ilyen típusú alkalmazások például a közel valós idejű folyamatok, például a biztonsági és behatolásészlelési alkalmazások telemetriai adatok betöltése.
* Alacsony átviteli képesség, alacsony késés. Ebben a forgatókönyvben a valós idejű feldolgozáshoz alacsony késésre (< 10 ms) van szükség, de az alacsonyabb átviteli feszültséget elviselni. Ilyen típusú alkalmazás például az online helyesírás- és nyelvhelyesség-ellenőrzés.

## <a name="producer-configurations"></a>Gyártói konfigurációk

A következő szakaszok kiemelik a legfontosabb konfigurációs tulajdonságokat a Kafka-gyártók teljesítményének optimalizálása érdekében. Az összes konfigurációs tulajdonság részletes magyarázatát az [Apache Kafka dokumentációjában találja a gyártókonfigurációkról.](https://kafka.apache.org/documentation/#producerconfigs)

### <a name="batch-size"></a>Köteg mérete

Az Apache Kafka-gyártók összeállítják az üzenetek csoportjait (úgynevezett kötegeket), amelyeket egy egységként küldenek egyetlen tárolópartíción. A kötegméret azoknak a bájtszámnak a száma, amelynek jelen kell lennie a csoport átvitele előtt. A `batch.size` paraméter növelése növelheti az átviteli terhelést, mert csökkenti a hálózati és i/o-kérelmek feldolgozási terhelését. Fényterhelés esetén a megnövekedett tételméret növelheti a Kafka küldési késleltetést, mivel a gyártó arra vár, hogy egy tétel készen álljon. Nagy terhelés esetén ajánlott növelni a köteg méretét az átviteli teljesítmény és a késés javítása érdekében.

### <a name="producer-required-acknowledgments"></a>A gyártónak nyugtázást kellett megkövetelnie

A gyártó `acks` által igényelt konfiguráció határozza meg a partícióvezetője által igényelt nyugták számát, mielőtt az írási kérelem befejezettnek minősülne. Ez a beállítás befolyásolja az adatok `0` `1`megbízhatóságát, és a , vagy `-1`a értékét veszi igénybe. Az azt `-1` jelenti, hogy az írás befejezése előtt az összes kópiából nyugtát kell fogadni. A `acks = -1` beállítás erősebb garanciákat nyújt az adatvesztés ellen, de nagyobb késést és alacsonyabb átviteli forgalmat is eredményez. Ha az alkalmazáskövetelmények nagyobb átviteli `acks = 0` igényt `acks = 1`igényelnek, próbálja meg a beállítást, vagy a. Ne feledje, hogy az összes kópia nem ismerése csökkentheti az adatok megbízhatóságát.

### <a name="compression"></a>Tömörítés

A Kafka-gyártó beállítható úgy, hogy tömörítse az üzeneteket, mielőtt elküldi őket a brókereknek. A `compression.type` beállítás a használandó tömörítési kodeket adja meg. A támogatott tömörítési kodekek a következők: "gzip", "snappy" és "lz4". A tömörítés hasznos, és figyelembe kell venni, ha van egy korlátozás a lemez kapacitását.

A két általánosan használt tömörítő `snappy` `gzip` kodek közül, `gzip` és magasabb tömörítési aránnyal rendelkezik, ami alacsonyabb lemezhasználatot eredményez a nagyobb CPU-terhelés árán. A `snappy` kodek kevesebb tömörítést és kevesebb CPU-terhelést biztosít. A brókerlemez vagy a gyártó processzorának korlátai alapján eldöntheti, hogy melyik kodeket használja. `gzip`ötször nagyobb sebességgel tömörítheti `snappy`az adatokat, mint a .

Az adattömörítés használata növeli a lemezen tárolható rekordok számát. Ez is növelheti a CPU terhelést azokban az esetekben, ahol van egy eltérés a tömörítési formátumok által használt gyártó és a bróker. mivel az adatokat a küldés előtt tömöríteni kell, majd a feldolgozás előtt ki kell bontani.

## <a name="broker-settings"></a>Közvetítői beállítások

A következő szakaszok kiemelik a legfontosabb beállításokat a Kafka brókerek teljesítményének optimalizálása érdekében. Az összes brókerbeállítás részletes magyarázatát az [Apache Kafka dokumentációjában találja a gyártói konfigurációkról.](https://kafka.apache.org/documentation/#producerconfigs)

### <a name="number-of-disks"></a>Lemezek száma

A tárolólemezek korlátozott IOPS-értéktel (bemeneti/kimeneti műveletek másodpercenként) rendelkeznek, és olvasási/írási bájt/másodperc. Új partíciók létrehozásakor a Kafka tárolja az egyes új partíciókat a lemezen a legkevesebb meglévő partícióval, hogy kiegyensúlyozza őket a rendelkezésre álló lemezek között. A tárolási stratégia ellenére, amikor minden lemezen több száz partícióreplikát dolgoz fel, a Kafka könnyedén telítheti a rendelkezésre álló lemezátviteli kapacitást. A kompromisszum itt az átviteli és a költség között van. Ha az alkalmazás nagyobb átviteli, hozzon létre egy fürtet több felügyelt lemezek et ügynökenként. A HDInsight jelenleg nem támogatja a felügyelt lemezek hozzáadása a futó fürthöz. A felügyelt lemezek számának konfigurálásáról a [Tárolás és méretezhetőség konfigurálása az Apache Kafka számára a HDInsight szolgáltatásban](apache-kafka-scalability.md)című témakörben talál további információt. Ismerje meg a fürt csomópontjaiszámára a tárhely növelésének költségvonzatait.

### <a name="number-of-topics-and-partitions"></a>Témakörök és partíciók száma

Kafka termelők írni témákat. Kafka fogyasztók olvasni témákban. A témakör egy naplóhoz van társítva, amely a lemezen lévő adatstruktúra. A Kafka rekordokat fűz a gyártó(k)ból a témakörnapló végére. A témakörnapló számos partícióból áll, amelyek több fájlra vannak elosztva. Ezek a fájlok viszont több Kafka fürtcsomóponton is elvannak osztva. A fogyasztók olvasni Kafka témák at lépésszám, és kiválaszthatja a helyzetüket (offset) a témakör napló.

Minden Kafka partíció egy naplófájl a rendszeren, és a termelői szálak egyszerre több naplóba is írhatnak. Hasonlóképpen, mivel minden fogyasztói szál beolvassa az üzeneteket egy partícióról, a több partícióból történő fogyasztás párhuzamosan is kezeli.

A partíció sűrűségének növelése (a partíciók száma közvetítőnként) hozzáad egy terhelést a metaadat-műveletek és partíciókérés/válasz a partíció vezetője és követői között. Még az adatok átáramlása hiányában is a partícióreplikák továbbra is lekérik az adatokat a vezetőktől, ami további feldolgozást eredményez a hálózaton keresztüli küldési és fogadási kérelmekhez.

Az Apache Kafka 1.1-es és újabb fürtjei esetén a HDInsightban azt javasoljuk, hogy közvetítőnként legfeljebb 1000 partíciót, beleértve a replikákat is. A közvetítőnkénti partíciók számának növelése csökkenti az átviteli szintet, és a témakör elérhetetlenségét is okozhatja. További információ a Kafka partíció támogatás, lásd [a hivatalos Apache Kafka blogbejegyzést a számának növekedése támogatott partíciók változat 1.1.0](https://blogs.apache.org/kafka/entry/apache-kafka-supports-more-partitions). A témakörök módosításáról az [Apache Kafka: témakörökben](https://kafka.apache.org/documentation/#basic_ops_modify_topic)olvashat.

### <a name="number-of-replicas"></a>Replikák száma

A nagyobb replikációs tényező további kéréseket eredményez a partícióvezetője és követői között. Ennek következtében a nagyobb replikációs tényező több lemezt és processzort használ fel a további kérések kezeléséhez, növelve az írási késést és csökkentve az átviteli terhelést.

Azt javasoljuk, hogy legalább 3x replikációt használjon a Kafka az Azure HDInsight.We recommend that you use at least 3x replication for Kafka in Azure HDInsight. A legtöbb Azure-régió három tartalék tartománnyal rendelkezik, de a csak két tartalék tartománnyal rendelkező régiókban a felhasználóknak 4-es replikációt kell használniuk.

A replikációról további információt az [Apache Kafka: replication](https://kafka.apache.org/documentation/#replication) and [Apache Kafka: increasing replication factor ( Apache Kafka)](https://kafka.apache.org/documentation/#basic_ops_increase_replication_factor)című témakörben talál.

## <a name="next-steps"></a>További lépések

* [Napi több trillió esemény feldolgozása az Apache Kafka on Azure használatával](https://azure.microsoft.com/blog/processing-trillions-of-events-per-day-with-apache-kafka-on-azure/)
* [Mi az a HDInsight-alapú Apache Kafka?](apache-kafka-introduction.md)
