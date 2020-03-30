---
title: Adatfolyam-továbbítás az Azure HDInsightban
description: Az adatok streamelésének használata méretezhető Apache-fürtökkel az Azure HDInsightban.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 006310f1a0efa69881bbe6d6ea4403b9c50402e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435395"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Ipari léptékű streamelés a HDInsightban

A valós idejű big data-megoldások a mozgásban lévő adatokra hatnak. Ezek az adatok általában az érkezés időpontjában a legértékesebbek. Ha a bejövő adatfolyam nagyobb lesz, mint az adott pillanatban kezelhető, előfordulhat, hogy az erőforrásokat kell szabályoznia. Másik lehetőségként egy HDInsight-fürt is felskálázhatja, hogy megfeleljen a streamelési megoldásnak az igény szerinti csomópontok hozzáadásával.

Egy streamelési alkalmazásban egy vagy több adatforrás olyan eseményeket hoz létre (néha másodpercenként több millióban), amelyeket gyorsan kell bedolgozni anélkül, hogy bármilyen hasznos információt eldobnának. A bejövő eseményeket egy olyan szolgáltatás , mint az [Apache Kafka](kafka/apache-kafka-introduction.md) vagy az [Event Hubs,](https://azure.microsoft.com/services/event-hubs/) *streampufferezéssel*( *eseménysor-kezelőnek*is ) kezeli. Az események összegyűjtése után elemezheti az adatokat egy valós idejű elemzési rendszer használatával az *adatfolyam-feldolgozási* rétegen belül, például az [Apache Storm](storm/apache-storm-overview.md) vagy az Apache Spark [Streaming használatával.](spark/apache-spark-streaming-overview.md) A feldolgozott adatok tárolhatók a hosszú távú tárolási rendszerek, például [az Azure Data Lake Storage,](https://azure.microsoft.com/services/storage/data-lake-storage/)és valós időben jelenik meg egy üzleti intelligencia irányítópulton, például a Power [BI](https://powerbi.microsoft.com), Tableau, vagy egy egyéni weboldal.

![Azure HDInsight streamelési minták](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Az Apache Kafka nagy átviteli sebességű, alacsony késleltetésű üzenetsor-várólistás szolgáltatást biztosít, és most már az Apache open source szoftver (OSS) csomagjának része. A Kafka közzétételi és előfizetési üzenetkezelési modellt használ, és biztonságosan tárolja a particionált adatok streamjeit egy elosztott, replikált fürtben. A Kafka lineárisan skálázódik az átviteli fokkal növekvő mértékben.

További információ: [Az Apache Kafka bemutatása a HDInsight-on](kafka/apache-kafka-introduction.md)című témakörben talál.

## <a name="apache-storm"></a>Apache Storm

Az Apache Storm egy elosztott, hibatűrő, nyílt forráskódú számítási rendszer, amely a Hadoop-mal valós idejű adatfolyamok feldolgozására van optimalizálva. A bejövő események alapvető adategysége egy tuple, amely kulcs/érték párok nem módosítható készlete. Ezeknek a tuple-oknak a határtalan sorozata egy adatfolyamot képez, amely egy spoutból származik. Az spout körbefolyatja a streamelési adatforrást (például a Kafka), és tuples-t bocsát ki. A vihar topológia egy sor átalakulások ezeken a patakok.

További információ: [Mi az Apache Storm az Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Spark Streaming

A Spark Streaming a Spark bővítménye, amely lehetővé teszi, hogy újra ugyanazt a kódot, amely et a kötegelt feldolgozáshoz használja. A kötegelt és az interaktív lekérdezések kombinálhatók ugyanabban az alkalmazásban. A Stormtal ellentétben a Spark Streaming pontosan a szemantika feldolgozása után biztosít állapotalapú. Ha a [Kafka Direct API-val](https://spark.apache.org/docs/latest/streaming-kafka-integration.html)együtt használják, amely biztosítja, hogy a Spark Streaming minden Kafka-adatot pontosan egyszer kap, akkor pontosan egyszer lehet végpontok között elérni a garanciákat. A Spark Streaming egyik erőssége a hibatűrő képességek, a hibás csomópontok gyors helyreállítása, ha több csomópontot használnak a fürtön belül.

További információ: [Mi az Apache Spark Streaming?](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Fürt méretezése

Bár a létrehozás során megadhatja a fürt csomópontjainak számát, érdemes lehet a fürt növekedését vagy zsugorítását a munkaterhelésnek megfelelően. Az összes HDInsight-fürt lehetővé teszi [a fürt csomópontjainak számának módosítását.](hdinsight-administer-use-portal-linux.md#scale-clusters) Spark-fürtök adatvesztés nélkül eldobhatók, mivel az összes adat az Azure Storage vagy a Data Lake Storage tárolja.

A technológiák függetlenítésének előnyei vannak. Például a Kafka egy eseménypufferelő technológia, így nagyon intenzív io-intenzív, és nem igényel sok feldolgozási teljesítményt. Összehasonlításképpen, streamprocesszorok, például a Spark Streaming számításigényes, nagyobb teljesítményű virtuális gépeket igényel. Azáltal, hogy ezeket a technológiákat különböző fürtökre választják, egymástól függetlenül skálázhatja őket, miközben a virtuális gépek legjobb kihasználása.

### <a name="scale-the-stream-buffering-layer"></a>Az adatfolyam pufferpufferrétegének méretezése

Az adatfolyam-pufferelési technológiák Event Hubs és a Kafka egyaránt partíciókat használnak, és a fogyasztók olvasni ezekből a partíciókból. A bemeneti átviteli szint skálázása a partíciók számának növelését igényli, és a partíciók hozzáadása növekvő párhuzamosságt biztosít. Az Event Hubs-ban a partíciók száma nem módosítható az üzembe helyezés után, ezért fontos, hogy a célskálát szem előtt tartva kezdje. A Kafka segítségével [partíciókat is hozzáadhat,](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion)még akkor is, ha a Kafka adatokat dolgoz fel. Kafka egy eszköz, hogy újra `kafka-reassign-partitions.sh`partíciókat, . A HDInsight egy [partícióreplika-újrakiegyensúlyozó eszközt](https://github.com/hdinsight/hdinsight-kafka-tools)biztosít , `rebalance_rackaware.py`. Ez a kiegyensúlyozó `kafka-reassign-partitions.sh` eszköz úgy hívja meg az eszközt, hogy minden replika külön tartalék tartományban és frissítési tartományban található, így a Kafka rack tisztában van a kafka rack-el, és növeli a hibatűrést.

### <a name="scale-the-stream-processing-layer"></a>Az adatfolyam-feldolgozási réteg méretezése

Az Apache Storm és a Spark Streaming támogatása is támogatja a feldolgozócsomópontok hozzáadása a fürtjeikhez, még akkor is, ha az adatok feldolgozása folyamatban van.

A Storm méretezésével hozzáadott új csomópontok előnyeinek kihasználásához újra kell egyensúlyoznia a Storm-topológiákat, amelyek a fürt méretének növelése előtt kezdődtek. Ez a kiegyensúlyozás a Storm webes felhasználói felületével vagy a CLI-vel végezhető el. További információt az [Apache Storm dokumentációjában](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)talál.

Az Apache Spark az alkalmazáskövetelményeitől függően három kulcsfontosságú `spark.executor.instances`paramétert használ a környezet konfigurálásához: , `spark.executor.cores`, és `spark.executor.memory`. A *végrehajtó* egy spark-alkalmazáshoz elindított folyamat. A végrehajtó fut a munkavégző csomóponton, és felelős az alkalmazás feladatainak végrehajtásáért. A végrehajtók alapértelmezett száma és az egyes fürtök végrehajtói méretei a munkavégző csomópontok száma és a munkavégző csomópont mérete alapján kerülnek kiszámításra. Ezek a számok a `spark-defaults.conf`fájlban tárolódnak minden fürtfőcsomóponton.

Ez a három paraméter konfigurálható a fürt szintjén, a fürtön futó összes alkalmazáshoz, és minden egyes alkalmazáshoz megadható. További információ: [Az Erőforrások kezelése az Apache Spark-fürtökhöz](spark/apache-spark-resource-manager.md)című témakörben talál.

## <a name="next-steps"></a>További lépések

* [Apache Storm-topológia létrehozása és figyelése az Azure HDInsightban](storm/apache-storm-quickstart.md)
* [HDInsight alatt futó Apache Storm példatopológiái](storm/apache-storm-example-topology.md)
* [Bevezetés az Apache Spark ba a HDInsighton](spark/apache-spark-overview.md)
* [Az HDInsight alatt futó Apache Kafka használatának első lépései](kafka/apache-kafka-get-started.md)
