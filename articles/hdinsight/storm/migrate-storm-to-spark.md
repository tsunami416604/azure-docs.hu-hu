---
title: Az Azure HDInsight 3.6 Apache Storm áttelepítése a HDInsight 4.0 Apache Sparkra
description: A különbségek és a migrációs folyamat áttelepítése Apache Storm számítási feladatok Spark Streaming vagy Spark strukturált streamelés.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/16/2019
ms.openlocfilehash: 916c54c3739d1164e4e9c1db67aa1f4e0dbd0c6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157791"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Az Azure HDInsight 3.6 Apache Storm áttelepítése a HDInsight 4.0 Apache Sparkra

Ez a dokumentum bemutatja, hogyan telepítheti át az Apache Storm-számítási feladatokat a HDInsight 3.6-on a HDInsight 4.0-ra. A HDInsight 4.0 nem támogatja az Apache Storm fürttípusát, és át kell telepítenie egy másik streamelési adatplatformra. Két megfelelő lehetőség az Apache Spark Streaming és a Spark structured streaming. Ez a dokumentum ismerteti a különbségeket ezek a platformok között, és azt is javasolja, hogy az Apache Storm-számítási feladatok áttelepítése munkafolyamatot.

## <a name="storm-migration-paths-in-hdinsight"></a>Viharáttelepítési útvonalak a HDInsightban

Ha az Apache Stormból szeretne áttérni a HDInsight 3.6-on, több lehetősége is van:

* Spark streaming a HDInsight 4.0-s
* Spark strukturált streamelése a HDInsight 4.0-s
* Azure Stream Analytics

Ez a dokumentum útmutatót biztosít az Apache Stormról a Spark Streaming és a Spark strukturált streamelése között való áttelepítéshez.

> [!div class="mx-imgBorder"]
> ![HDInsight Storm áttelepítési útvonal](./media/migrate-storm-to-spark/storm-migration-path.png)

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Az Apache Storm és a Spark Streaming, a Spark strukturált streamelésének összehasonlítása

Az Apache Storm különböző szinteken biztosít garantált üzenetfeldolgozást. Például egy egyszerű Storm-alkalmazás garantálja legalább egyszer feldolgozás, és [a Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) tudja garantálni, hogy pontosan egyszer feldolgozása. A Spark Streaming és a Spark strukturált streamelése garantálja, hogy minden bemeneti esemény feldolgozása pontosan egyszer történik, még akkor is, ha csomóponthiba történik. A Storm rendelkezik egy modellel, amely minden egyes eseményt feldolgoz, és használhatja a Micro Batch modellt a Tridenttel is. A Spark Streaming és a Spark strukturált streamelése mikro-kötegelt feldolgozási modellt biztosít.

|  |Storm |Spark-streamelés | Spark strukturált streamelés|
|---|---|---|---|
|**Eseményfeldolgozási garancia**|Legalább egyszer <br> Pontosan egyszer (Trident) |[Pontosan egyszer](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Pontosan egyszer](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Feldolgozási modell**|Valós idejű <br> Mikro köteg (szigony) |Mikro tétel |Mikro tétel |
|**Eseményidő-támogatás**|[Igen](https://storm.apache.org/releases/2.0.0/Windowing.html)|Nem|[Igen](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Nyelvek**|Java, stb.|Scala, Jáva, Python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Spark streaming vs Spark strukturált streamelés

A Spark strukturált streamelése felváltja a Spark Streaming (DStreams) szolgáltatást. A strukturált streamelés továbbra is megkapja a fejlesztéseket és a karbantartást, míg a DStreams csak karbantartási módban lesz. **Megjegyzés: kell linkeket hangsúlyozni ezt a pontot**. Strukturált streamelési nem rendelkezik annyi funkcióval, mint a DStreams a források és a fogadók, hogy támogatja a dobozból, ezért értékelje ki a követelmények kiválasztásához a megfelelő Spark stream feldolgozási beállítást.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Streamelés (egyeseményes) feldolgozás és mikroköteges feldolgozás

A Storm olyan modellt biztosít, amely minden egyes eseményt feldolgoz. Ez azt jelenti, hogy az összes bejövő rekordot a mint megérkeznek, a mint megérkeznek, a mint megérkeznek, a mint azok megérkeznek. Spark Streaming alkalmazások nak meg kell várnia a másodperc tört részét, hogy összegyűjtse az egyes mikro-köteg események, mielőtt elküldi a kötegfeldolgozásra. Ezzel szemben egy eseményvezérelt alkalmazás minden eseményt azonnal feldolgoz. Spark Streamelés késés általában néhány másodperc alatt. A mikroköteg-megközelítés előnyei a hatékonyabb adatfeldolgozás és az egyszerűbb aggregált számítások.

> [!div class="mx-imgBorder"]
> ![streamelés és mikrokötegű feldolgozás](./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png)

## <a name="storm-architecture-and-components"></a>Vihararchitektúra és -összetevők

A Storm-topológiák több összetevőből állnak, amelyek egy irányított aciklikus gráfba (DAG) vannak rendezve. Az adatáramlás a gráf összetevői között zajlik. Minden összetevőbe egy vagy több stream érkezik be, valamint egy vagy több streamet sugároz.

|Összetevő |Leírás |
|---|---|
|Kifolyó|Adatok at hoz egy topológiába. Egy vagy több streamet sugároznak a topológiába.|
|Csavar|A spoutokból vagy más csavarokból kibocsátott adatfolyamokat használja fel. A boltok opcionálisan streameket is sugározhatnak a topológiába. A boltok felelősek az adatok külső szolgáltatásokba vagy tárolókba, például HDFS-, Kafka- vagy HBase-tárolókba történő kiírásáért.|

> [!div class="mx-imgBorder"]
> ![a viharkomponensek kölcsönhatása](./media/migrate-storm-to-spark/apache-storm-components.png)

A Storm a következő három démonból áll, amelyek a Storm-fürt működését tartják.

|Daemon |Leírás |
|---|---|
|Nimbus|A Hadoop JobTracker-hez hasonlóan a fürt körül a kód terjesztése, valamint a feladatok gépekhez való hozzárendelése és a hibák figyelése felelős.|
|Zookeeper|Fürtkoordinációra szolgál.|
|Felügyelő|Figyeli a gépéhez rendelt munkát, és elindítja és leállítja a munkavégző folyamatokat a Nimbus irányelvei alapján. Minden munkavégző folyamat egy topológia egy részhalmazát hajtja végre. A felhasználó alkalmazáslogikája (spouts és bolt) itt fut.|

> [!div class="mx-imgBorder"]
> ![nimbus, zookeeper és supervisor démonok](./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png)

## <a name="spark-streaming-architecture-and-components"></a>Spark Streaming architektúra és összetevők

A következő lépések összefoglalják, hogyan működnek együtt az összetevők a Spark Streamelésben (DStream) és a Spark strukturált streamelésében:

* Spark Streaming indításakor az illesztőprogram elindítja a feladatot a végrehajtó.
* A végrehajtó adatfolyamot fogad egy adatfolyam-adatforrásból.
* Amikor a végrehajtó adatfolyamokat fogad, az adatfolyamot blokkokra osztja, és a memóriában tartja őket.
* Az adatblokkok replikálódnak más végrehajtókra.
* A feldolgozott adatok ezután a céladattárban tárolódnak.

> [!div class="mx-imgBorder"]
> ![szikra-streamelési útvonal a kimenethez](./media/migrate-storm-to-spark/spark-streaming-to-output.png)

## <a name="spark-streaming-dstream-workflow"></a>Spark Streaming (DStream) munkafolyamat

Ahogy minden kötegelt intervallum eltelik, egy új RDD jön létre, amely az adott intervallum összes adatát tartalmazza. A rdd-k folyamatos készleteit a program egy DStream-be gyűjti. Ha például a kötegidőköz egy másodperc hosszú, a DStream minden másodpercben egy olyan RDD-t tartalmazó köteget bocsát ki, amely az adott másodperc alatt bevitt összes adatot tartalmazza. A DStream feldolgozásakor a hőmérsékleti esemény megjelenik az egyik ilyen kötegben. A Spark Streaming alkalmazás feldolgozza az eseményeket tartalmazó kötegeket, és végső soron az egyes RDD-kben tárolt adatokra hat.

> [!div class="mx-imgBorder"]
> ![szikrafolyamatos feldolgozási kötegek](./media/migrate-storm-to-spark/spark-streaming-batches.png)

A Spark Streaming szolgáltatással elérhető különböző átalakításokról a [DStreamek átalakításai](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams)című témakörben talál.

## <a name="spark-structured-streaming"></a>Szikra strukturált streamelés

A Spark strukturált adatfolyama egy adatfolyamot jelent, amely részletesen nincs korlátozás nélkül. A tábla az új adatok megérkezésével folyamatosan növekszik. Ezt a bemeneti táblát egy hosszú ideig futó lekérdezés folyamatosan feldolgozza, és az eredményeket egy kimeneti táblába küldi.

Strukturált streamelési, adatok érkeznek a rendszerbe, és azonnal egy bemeneti táblába. Olyan lekérdezéseket írhat (a DataFrame és az Adatkészlet API-k használatával), amelyek műveleteket hajtanak végre ezen a bemeneti táblán.

A lekérdezés kimenete *eredménytáblát*eredményez, amely a lekérdezés eredményeit tartalmazza. Az eredmények táblájából adatokat rajzolhat egy külső adattárhoz, egy ilyen relációs adatbázishoz.

A bemeneti táblából történő adatfeldolgozás időzítését az eseményindító-időköz szabályozza. Alapértelmezés szerint az eseményindító-időköz nulla, így a strukturált streamelés megpróbálja feldolgozni az adatokat, amint megérkezik. A gyakorlatban ez azt jelenti, hogy amint strukturált streamelés befejeződött az előző lekérdezés futtatásának feldolgozása, elindítja egy másik feldolgozási futtatás ellen az újonnan fogadott adatok. Beállíthatja, hogy az eseményindító időközönként fusson, így a streamelési adatok időalapú kötegekben lesznek feldolgozva.

> [!div class="mx-imgBorder"]
> ![adatok feldolgozása strukturált adatfolyamban](./media/migrate-storm-to-spark/structured-streaming-data-processing.png)

> [!div class="mx-imgBorder"]
> ![programozási modell strukturált streameléshez](./media/migrate-storm-to-spark/structured-streaming-model.png)

## <a name="general-migration-flow"></a>Általános áttelepítési folyamat

A Stormból a Sparkba javasolt áttelepítési folyamat a következő kezdeti architektúrát feltételezi:

* A Kafka adatfolyam-adatforrásként
* A Kafka és a Storm ugyanazon a virtuális hálózaton van telepítve
* A Storm által feldolgozott adatok egy adatgyűjtőbe vannak írva, például az Azure Storage vagy az Azure Data Lake Storage Gen2.

    > [!div class="mx-imgBorder"]
    > ![a feltételezett aktuális környezet diagramja](./media/migrate-storm-to-spark/presumed-current-environment.png)

Az alkalmazás Stormból a Spark streamelési API-k egyikére való áttelepítéséhez tegye a következőket:

1. **Új fürt telepítése.** Üzembe helyezhet egy új HDInsight 4.0 Spark-fürtöt ugyanabban a virtuális hálózatban, és telepítse a Spark Streaming vagy Spark strukturált streamelési alkalmazást, és tesztelje alaposan.

    > [!div class="mx-imgBorder"]
    > ![új spark-telepítés a HDInsightban](./media/migrate-storm-to-spark/new-spark-deployment.png)

1. **Ne kelljen a régi Storm-fürtön fogyasztania.** A meglévő storm, állítsa le a streamelési adatforrásból származó adatok fogyasztását, és várja meg, amíg az adatok a célfogadóba történő írás befejeződik.

    > [!div class="mx-imgBorder"]
    > ![az aktuális fürt önfeledtsének leállítása](./media/migrate-storm-to-spark/stop-consuming-current-cluster.png)

1. **Kezdje el fogyasztani az új Spark-fürtön.** Indítsa el az adatok streamelését egy újonnan üzembe helyezett HDInsight 4.0 Spark-fürtről. Ebben az időben, a folyamat átveszi a fogyasztása a legújabb Kafka offset.

    > [!div class="mx-imgBorder"]
    > ![elkezd consuming az új fürtön](./media/migrate-storm-to-spark/start-consuming-new-cluster.png)

1. **Szükség szerint távolítsa el a régi fürtöt.** Miután a kapcsoló befejeződött és megfelelően működik, távolítsa el a régi HDInsight 3.6 Storm fürt szükség szerint.

    > [!div class="mx-imgBorder"]
    > ![a régi HDInsight-fürtök eltávolítása szükség szerint](./media/migrate-storm-to-spark/remove-old-clusters1.png)

## <a name="next-steps"></a>További lépések

A Stormról, a Spark Streamingről és a Spark strukturált streameléséről az alábbi dokumentumokban talál további információt:

* [Az Apache Spark Streaming áttekintése](../spark/apache-spark-streaming-overview.md)
* [Az Apache Spark strukturált streamelésének áttekintése](../spark/apache-spark-structured-streaming-overview.md)