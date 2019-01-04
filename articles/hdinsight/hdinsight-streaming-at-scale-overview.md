---
title: Ipari léptékű streamelés az Azure HDInsight a
description: A HDInsight-fürtökkel méretezhető streamelési adatok felhasználási módját.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/19/2018
ms.openlocfilehash: 01db1de5c6b533c346ce35c8474d996213873d10
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002195"
---
# <a name="streaming-at-scale-in-hdinsight"></a>Ipari léptékű streamelés a HDInsightban

Valós idejű big data-megoldások a mozgásban lévő adatok cselekedhet. Ezeket az adatokat általában a legértékesebb érkezési a időpontban. Ha a bejövő adatfolyam nagyobb, mint az adott időpontban lehet kezelni, szükség lehet a sávszélesség-szabályozási le erőforrásokat. Azt is megteheti egy HDInsight-fürtöt is méretezhető a folyamatos átviteli megoldás igény szerinti csomópontok hozzáadásával.


A streamelési alkalmazások esetén egy vagy több adatforrás generál nélkül elvetését minden hasznos információt gyorsan betöltött-események (néha a másodpercenként több millió). A bejövő eseményeket kezeli a *streampufferelésnek*, más néven is néven *események üzenetsorba való helyezése*, például a szolgáltatás által [Apache Kafka](kafka/apache-kafka-introduction.md) vagy [az Event Hubs](https://azure.microsoft.com/services/event-hubs/). Miután összegyűjtötte az eseményeket, belül valós idejű elemzési rendszert használ az adatok elemzésével aztán a *adatfolyam-feldolgozás* réteg, mint például [Apache Storm](storm/apache-storm-overview.md) vagy [Apache Spark Streaming](spark/apache-spark-streaming-overview.md). A feldolgozott adatok tárolhatók a hosszú távú tárolási rendszerek, például [Azure Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/), és megjelennek az üzleti intelligencia irányítópulton, valós időben például [Power BI](https://powerbi.microsoft.com), Tableau vagy egy egyéni webhely oldal.


![HDInsight Streaming-minták](./media/hdinsight-streaming-at-scale-overview/HDInsight-streaming-patterns.png)

## <a name="apache-kafka"></a>Apache Kafka

Az Apache Kafka biztosít a nagy átviteli sebességű, alacsony késleltetésű üzenet üzenetsor-szolgáltatás, és most már az Apache, nyílt forrás Software (OSS) csomag része. A Kafka közzétételi használ, és fizessen elő a particionált adatok biztonságosan fürtben egy elosztott, a replikált üzenetkezelési modellt és a tárolók adatfolyamokat. A Kafka költségráfordításokkal egyenes arányban, mert növeli a teljesítményt.

További információkért lásd: [Apache Kafka bemutatása a HDInsight](kafka/apache-kafka-introduction.md).

## <a name="apache-storm"></a>Apache Storm

Az Apache Storm egy elosztott, hibatűrő, nyílt forráskódú számítási rendszer, amely a hadooppal valós idejű adatstreamek feldolgozására van optimalizálva. Az esemény adatait core mértékegysége egy rekord, amely egy kulcs/érték párok nem módosítható készletét. Egy rekordokat tartalmazó űrlapok korlátlan streameken működő sorozata, amely egy Stream-Spout származik. A Spout (pl.: Kafka) egy streamingadatforrást burkolja, és a rekordok bocsát ki. A storm-topológia ezekbe az adatfolyamokba átalakítások sorozata.

További információkért lásd: [Mi az Apache Storm on Azure HDInsight?](storm/apache-storm-overview.md).

## <a name="spark-streaming"></a>Spark Streaming

Spark Streaming az Spark, amely lehetővé teszi, hogy használja újra ugyanazt a kódot használhatja a kötegelt feldolgozáshoz kiterjesztése. Kombinálhatja a batch- és interaktív lekérdezések használata ugyanabban az alkalmazásban. Ellentétben a Storm, Spark Streaming biztosít állapotalapú pontosan-szemantika feldolgozása után. Az együttes alkalmazásával a [Kafka közvetlen API](https://spark.apache.org/docs/latest/streaming-kafka-integration.html), amely biztosítja, hogy minden Kafka-adatok által fogadott Spark Streaming pontosan egyszer, lehetőség a teljes körű pontosan elérése-egyszer garantálja. Egyik erőssége a Spark Streaming annak és hibatűrő képességeit, a helyreállítás gyorsan meghibásodott csomópontok, amikor több csomóponton vannak használatban a fürtön belül.

További információkért lásd: [Mi az Apache Spark Streaming?](hdinsight-spark-streaming-overview.md).

## <a name="scaling-a-cluster"></a>Fürt méretezése

Bár a is megadhat a csomópontok számát a fürt létrehozása során, érdemes megnöveljék vagy csökkentsék a számítási feladatok megfelelő a fürt. Az összes HDInsight-fürtök lehetővé teszik [a fürtben található csomópontok számának módosítását](hdinsight-administer-use-management-portal.md#scale-clusters). A Spark-fürtök nincs adatvesztés, is lehet elvetni, mert az Azure Storage vagy a Data Lake Storage tárolt összes adatot.

Nincsenek a megszakító technológiák előnyeit. Például a Kafka pufferelés technológiát, így nagyon IO-igényes, és nem kell annyit esemény a feldolgozási teljesítményt. Ezzel szemben például a Spark Streaming streamfeldolgozóknak olyan nagy számítási igényű, nagyobb teljesítményű virtuális gépeket kellene. Ezek a technológiák, különböző fürtök leválasztott sablonkonfigurációkat, azokat egymástól függetlenül lehessen méretezni okból rendszerbetöltést végrehajtani a virtuális gépek legjobb közben.

### <a name="scale-the-stream-buffering-layer"></a>A stream pufferelés réteg skálázása

Az Event Hubs és a Kafka technológiák pufferelés adatfolyam is használhatja a partíciók, és a fogyasztók olvasni azokat a partíciókat. A bemeneti átviteli sebesség szükséges a partíciók számának méretezése, és egyre nagyobb párhuzamosság partíciók hozzáadásával biztosít. Az Event hubs is – a partíciók száma nem módosítható üzembe helyezés után, ezért fontos szem előtt a céloldali révén elindításához. A Kafka, lehetőség [partíciók hozzáadásával](https://kafka.apache.org/documentation.html#basic_ops_cluster_expansion), míg a Kafka adatok feldolgozása folyamatban van. A Kafka újbóli hozzárendeléséhez a partíciók eszközt biztosít `kafka-reassign-partitions.sh`. HDInsight biztosít egy [eszköz újraegyensúlyozása címtárpartíció replikájának](https://github.com/hdinsight/hdinsight-kafka-tools), `rebalance_rackaware.py`. Az újraegyensúlyozás eszköz meghívja a `kafka-reassign-partitions.sh` eszközt úgy, hogy minden egyes replikának egy külön tartalék tartomány és a frissítési tartomány, így Kafka rack tudomása és egyre nagyobb hibatűrést.

### <a name="scale-the-stream-processing-layer"></a>Az adatfolyam-feldolgozási réteg skálázása

Apache Storm és Spark Streaming adatok feldolgozása közben is támogatja a fürtök hozzáadásának feldolgozó csomópontokat.

A Storm a méretezés során hozzáadott új csomópontok kihasználásához szüksége újraegyensúlyozására bármely a fürtméret előtt elindított Storm-topológiák. Az újraegyensúlyozás végezhető használ a Storm webes felhasználói felületen vagy a parancssori felület. További információkért lásd: a [Apache Storm-dokumentáció](https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

Az Apache Spark három fő paramétereket használja az alkalmazás követelményeitől függően, a környezet konfigurálása: `spark.executor.instances`, `spark.executor.cores`, és `spark.executor.memory`. Egy *végrehajtó* folyamat, amely egy Spark-alkalmazás elindul. Az executor a munkavégző csomóponton fut, és az alkalmazás feladatok elvégzéséért felelős. Az alapértelmezett száma végrehajtóval és az egyes fürtök végrehajtó mérete munkavégző csomópontok és a munkavégző csomópont méretét száma alapján számítjuk. Ezek a számok lesznek tárolva az `spark-defaults.conf`minden egyes fürt fő csomópontjának fájlt.

Ezeket a paramétereket, minden alkalmazás, amely a fürtön futnak, és minden egyes alkalmazáshoz is megadható a fürt szintjén konfigurálható. További információkért lásd: [-erőforrások kezelése, az Apache Spark-fürtök](spark/apache-spark-resource-manager.md).

## <a name="next-steps"></a>További lépések

* [A HDInsight Apache Storm használatának első lépései](storm/apache-storm-tutorial-get-started-linux.md)
* [HDInsight alatt futó Apache Storm példatopológiái](storm/apache-storm-example-topology.md)
* [Az Apache Spark on HDInsight bemutatása](spark/apache-spark-overview.md)
* [A HDInsight Apache Kafka használatának első lépései](kafka/apache-kafka-get-started.md)
