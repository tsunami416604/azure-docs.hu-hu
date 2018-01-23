---
title: "Az Azure HDInsight léptékű Streaming |} Microsoft Docs"
description: "Hogyan használható az adatfolyam méretezhető HDInsight-fürtökkel."
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
ms.date: 01/19/2018
ms.author: ramoha
ms.openlocfilehash: 46b5723805ab5d8bc1cf5b5183d9501cd3e4e3a2
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2018
---
# <a name="streaming-at-scale-in-hdinsight"></a>Adatfolyam-léptékű a Hdinsightban

Mozgásérzékelő – az adatok a valós idejű big data-megoldások jár el. Ezeket az adatokat általában legértékesebb érkezési a időpontban. Ha a bejövő adatfolyam nagyobb, mint a pillanatban kezelhető, szükség lehet szabályozás erőforrások le. Azt is megteheti HDInsight-fürtök legfeljebb az adatfolyam-megoldás felel meg a csomópontok az igény szerinti hozzáadásával.

Egy vagy több adatforrást egy adatfolyam-továbbítási alkalmazást, az események (néha a másodpercenként több millió) gyorsan fogyasztanak nélkül minden hasznos információt generál. A bejövő események kezeli a *adatfolyam pufferelés*is néven *esemény queuing*, például egy szolgáltatás [Kafka](kafka/apache-kafka-introduction.md) vagy [Event Hubs](https://azure.microsoft.com/services/event-hubs/). Miután összegyűjtötte az eseményeket, majd elemezheti belül valós idejű elemzési rendszert használ az adatok a *adatfolyam feldolgozása* réteg, például a [Storm](storm/apache-storm-overview.md) vagy [Spark Streaming](spark/apache-spark-streaming-overview.md). A feldolgozott adatok tárolhatók a hosszú távú tárolási rendszerek, például [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/), és jeleníti meg valós időben az üzleti intelligenciát biztosító irányítópulton, például a [Power BI](https://powerbi.microsoft.com), Tableau, vagy egy egyéni weblap .

![HDInsight adatfolyam-minták](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Apache Kafka biztosít a nagy átviteli, alacsony késésű üzenet-várólista szolgáltatás, és most az Apache csomag része a nyitott forrás szoftver (OSS). Kafka használja a közzététel és előfizetés üzenetkezelési modellt és a tárolók adatstreamek particionált biztonságosan fürtben elosztott, a replikált. Kafka lineárisan arányosan, ahogy a növeli a teljesítményt.

További információkért lásd: [a HDInsight bemutatása Apache Kafka](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Apache Storm egy elosztott, nagy hibatűrésű, nyílt forráskódú számítási rendszer, amely valós idejű Hadoop adatstreamek feldolgozására optimalizálva. Az esemény adatainak core mértékegysége rekordot, amely egy nem módosítható, a kulcs/érték párok halmaza. Egy unbounded sorozat rekordokat formában adatfolyam, amely egy Spout származik. A Spout becsomagolja egy adatfolyam-továbbítási adatforrás (például Kafka), és megfelelően kibocsát rekordokat. A storm-topológia egy olyan karakterlánc, az ezekbe az adatfolyamokba átalakításokat.

További információkért lásd: [Mi az Azure HDInsight alatt futó Apache Storm?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Spark Streaming

Spark Streaming bővítménye, Spark, amely lehetővé teszi a kötegelt feldolgozásra használó ugyanazt a kódot ismét felhasználni. Kötegelt mind ugyanabban az alkalmazásban interaktív lekérdezések kombinálhatja. Ellentétben a Storm, Spark Streamelési biztosít állapotalapú alkalmazások és szolgáltatások pontosan-szemantikáját feldolgozása után. Ha együtt a [Kafka közvetlen API](http://spark.apache.org/docs/latest/streaming-kafka-integration.html), amely biztosítja, hogy minden Kafka adatokat fogadta a Spark Streaming pontosan egyszer, akkor előfordulhat végpont pontosan eléréséhez-egyszer biztosítja, hogy az. Spark Streaming szintjeiről egyik hibatűrő platformképességei helyreállítása csomópontok gyorsan meghibásodott, a fürtön belül több csomópont alatt használata.

További információkért lásd: [Újdonságok Spark Streaming?](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>A fürt méretezése

Bár a csomópontok számát a fürt létrehozása során adhat meg, érdemes lehet növekedhet és csökkenhet a fürt a munkaterheléshez. HDInsight-fürtök esetében engedélyezi, hogy [a fürtben található csomópontok számának megváltoztatására](hdinsight-administer-use-management-portal.md#scale-clusters). Spark-fürtjei törölhetők az adatvesztés, mivel minden adatokat az Azure Storage vagy a Data Lake Store tárolja.

Számos előnnyel jár megszakító technológiák. Például Kafka-e egy esemény pufferelés technológia, így nagyon IO intenzív, és nem kell nagy feldolgozási kapacitása révén. Szemben például a Spark Streaming adatfolyam processzorok számítási igényű, nagyobb teljesítményű virtuális gépeket igénylő. Azzal, hogy ezek a technológiák a különböző fürtökben le, méretezheti őket egymástól függetlenül közben a virtuális gépek használata ajánlott.

### <a name="scale-the-stream-buffering-layer"></a>Az adatfolyam réteg pufferelés méretezése

Az Event Hubs és a Kafka technológiák pufferelés adatfolyam mindkettő használja a partíciók, és ezek a partíciók olvasásának fogyasztók. A bemeneti átviteli skálázáshoz kell a partíciók száma vertikális felskálázásával és növekvő párhuzamossági partíciók hozzáadása tartalmaz. Az Event Hubs a partíciók száma nem módosítható a telepítés utáni, fontos, hogy a cél skála szem előtt indítsa el. Kafka, lehetősége a [adja hozzá a partíciók](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), Kafka az adatfeldolgozás közben is. Kafka használatával újból hozzárendelheti azokat a partíciókat, eszközt biztosít `kafka-reassign-partitions.sh`. A HDInsight lehetővé egy [eszköz újraelosztás címtárpartíció replikájának](https://github.com/hdinsight/hdinsight-kafka-tools), `rebalance_rackaware.py`. Ez az eszköz rebalancing meghívja a `kafka-reassign-partitions.sh` úgy, hogy minden egyes replikának egy külön tartalék tartomány és a frissítési tartomány, így Kafka állvány használatát, és az egyre hibatűrést eszközt.

### <a name="scale-the-stream-processing-layer"></a>Az adatfolyam feldolgozása réteg méretezése

Apache Storm- és Spark Streaming támogatja a munkavégző csomópontok felvételét a fürtök, még akkor is, amíg az adatok feldolgozása folyamatban van.

A Storm a méretezés során hozzáadott új csomópontok kihasználásához, szüksége egyensúlyba bármely Storm-topológiák elindítása előtt a fürt mérete. Ez újraelosztás végezhető a Storm használatának webes felhasználói felületén vagy a parancssori felület. További információkért lásd: a [alatt futó Apache Storm-dokumentáció](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Apache Spark három kulcsfontosságú paraméterrel használja a környezet, attól függően, hogy az alkalmazás követelmények konfigurálása: `spark.executor.instances`, `spark.executor.cores`, és `spark.executor.memory`. Egy *végrehajtó* folyamat, amely egy Spark-alkalmazáshoz nincs elindítva. Az executor a munkavégző csomópont fut, és az alkalmazás feladatok elvégzéséért felelős. A végrehajtója és a végrehajtó használatos egyes fürtök alapértelmezett száma számítása a feldolgozó csomópontok és a munkavégző csomópont méretének száma alapján történik. Ezek az adatok tárolódnak a `spark-defaults.conf`fájl minden egyes fürtcsomóponton head.

A fürt szintjén minden olyan alkalmazásnál, amely a fürtön futtatni, és minden egyes alkalmazáshoz is megadható konfigurálhatja a következő három paramétert. További információkért lásd: [erőforrások kezelése az Apache Spark-fürtök](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>További lépések

* [Ismerkedés a HDInsight alatt futó Apache Storm](storm/apache-storm-tutorial-get-started-linux.md)
* [HDInsight alatt futó Apache Storm példatopológiái](storm/apache-storm-example-topology.md)
* [A Spark on HDInsight bemutatása](spark/apache-spark-overview.md)
* [A HDInsight Apache Kafka kezdődnie](kafka/apache-kafka-get-started.md)