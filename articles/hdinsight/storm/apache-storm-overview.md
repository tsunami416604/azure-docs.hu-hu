---
title: Mi az az Apache Storm? – Azure HDInsight
description: Az Apache Stormmal valós időben dolgozhat fel adatfolyamokat. Az Azure HDInsighttal könnyen létrehozhat Storm-fürtöket az Azure-felhőben. A Visual Studióval C# használatával hozhat létre Storm-megoldásokat, amelyeket a HDInsight Storm-fürtjeiben helyezhet üzembe.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/20/2020
ms.openlocfilehash: 44c0ca3a2fc16b805744678cc3358b4f5690766a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687653"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Mi az Azure HDInsight alatt futó Apache Storm?

Az [Apache Storm](https://storm.apache.org/) egy elosztott, nagy hibatűrésű, nyílt forráskódú számítási rendszer. A Storm segítségével valós időben dolgozhatja fel az adatfolyamokat az [Apache Hadoop](../hadoop/apache-hadoop-introduction.md)segítségével. A Storm-megoldások garantált adatfeldolgozást is biztosíthatnak, lehetővé téve az első alkalommal nem sikeresen feldolgozott adatok visszajátszását.

## <a name="why-use-apache-storm-on-hdinsight"></a>Miért érdemes használni az Apache Stormot a HDInsighton?

A HDInsight alatt futó Storm a következő szolgáltatásokat biztosítja:

* __99%-os szolgáltatásiszint-szerződés (SLA) a Storm üzemidejében:__ A Storm on HDInsight teljes folyamatos támogatást nyújt. A HDInsight alatt futó Storm emellett szolgáltatásiszint-szerződésben garantált 99,9%-os elérhetőséggel rendelkezik. Ennek értelmében a Microsoft garantálja, hogy az egyes Storm-fürtök az idő legalább 99,9%-ában elérhetők lesznek kívülről. További információk: [Azure-ügyfélszolgálat](https://azure.microsoft.com/support/options/). Lásd még: [A HDInsight-dokumentum SLA-információi.](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/)

* Könnyen testre szabható a szkriptek Storm-fürtön történő futtatásával a létrehozás során vagy után. További információ: [HDInsight-fürtök testre szabása szkriptműveletekkel](../hdinsight-hadoop-customize-cluster-linux.md).

* **Megoldások létrehozása több nyelven**: A Storm-összetevőket tetszőleges nyelven megírhatja, például Java, C# és Python nyelven is.

    * A Visual Studio és a HDInsight integrációjával biztosítja a C#-topológiák fejlesztését, felügyeletét és figyelését. További információ: [C# Storm-topológiák fejlesztése a HDInsight Tools for Visual Studio szolgáltatással](apache-storm-develop-csharp-visual-studio-topology.md).

    * Támogatja a Trident Java-felületet. Olyan Storm-topológiákat hozhat létre, amelyek támogatják az üzenetek pontosan egyszeri feldolgozását, a tranzakciós adattároló-megőrzést és számos gyakori Stream Analytics-műveletet.

* **Dinamikus méretezés**: A futó Storm-topológiák befolyásolása nélkül adhat hozzá vagy távolíthat el feldolgozó csomópontokat. Kapcsolja ki és aktiválja újra a futó topológiákat a méretezési műveletek során hozzáadott új csomópontok előnyeinek kihasználásához.

* **Streamelési folyamatok létrehozása több Azure-szolgáltatás használatával:** A Storm on HDInsight más Azure-szolgáltatásokkal integrálható. Például az Event Hubs, az SQL Database, az Azure Storage és az Azure Data Lake Storage. Az Azure-szolgáltatásokkal integrálható példa: [Események feldolgozása az Event Hubs ból az Apache Storm segítségével a HDInsight-on című témakört.](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

A valós idejű elemzési megoldásaikhoz Apache Stormot használó vállalatok listája itt található: [Az Apache Stormot használó vállalatok](https://storm.apache.org/Powered-By.html).

A Storm használatának első lépései az [Apache Storm-topológia létrehozása és figyelése az Azure HDInsightban című témakörben található.](apache-storm-quickstart.md)

## <a name="how-does-apache-storm-work"></a>Hogyan működik az Apache Storm?

A Storm az [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) feladatok helyett topológiákat futtat, amelyeket esetleg ismer. A Storm-topológiák több összetevőből állnak, amelyek egy irányított aciklikus gráfba (DAG) vannak rendezve. Az adatáramlás a gráf összetevői között zajlik. Minden összetevőbe egy vagy több stream érkezik be, valamint egy vagy több streamet sugároz. Az alábbi ábrán az összetevők közti adatfolyamok láthatók egy alapszintű szószámlálási topológiában:

![Példa az összetevők elrendezésére egy Storm-topológiában](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* A spout-összetevők adatokat importálnak a topológiába. Egy vagy több streamet sugároznak a topológiába.

* A bolt-összetevők a spout- vagy más bolt-összetevők által sugárzott streameket dolgozzák fel. A boltok opcionálisan streameket is sugározhatnak a topológiába. A boltok felelősek az adatok külső szolgáltatásokba vagy tárolókba, például HDFS-, Kafka- vagy HBase-tárolókba történő kiírásáért.

## <a name="reliability"></a>Megbízhatóság

Az Apache Storm garantálja, hogy mindig minden bejövő üzenetet feldolgoz, még akkor is, ha az adatok elemzése több száz csomópont között oszlik meg.

A Nimbus csomópont az Apache Hadoop JobTracker-hez hasonló funkciókat biztosít. A Nimbus az Apache ZooKeeper szolgáltatáson keresztül rendeli hozzá a feladatokat a fürt más csomópontjaihoz. Zookeeper csomópontok biztosítják a fürt és a segítő közötti kommunikáció tanai a Munkavégző csomópontok on A felügyeleti folyamat közötti kommunikáció t. Ha egy feldolgozó csomópont leáll, arról a Nimbus csomópont értesítést kap, és kiosztja a feladatot és a kapcsolódó adatokat egy másik csomópontnak.

Az alapértelmezett konfiguráció szerint az Apache Storm-fürtök csak egyetlen Nimbus csomóponttal rendelkeznek. A HDInsight alatt futó Storm két Nimbus csomóponttal rendelkezik. Ha az elsődleges csomópont meghibásodik, a Storm-fürt átvált a másodlagos csomópontra, amíg az elsődleges csomópont helyreállítása be nem következik. A következő ábra a Storm on HDInsight feladatfolyam-konfigurációját mutatja be:

![Diagram: Nimbus, Zookeeper és Supervisor](./media/apache-storm-overview/storm-diagram-nimbus.png)

## <a name="ease-of-use"></a>Könnyű használat

|Használat |Leírás |
|---|---|
|Biztonságos rendszerhéj (SSH) kapcsolat|A Storm-fürt főcsomópontjait az SSH használatával érheti el az interneten keresztül. Az SSH használatával közvetlenül futtathat parancsokat a fürtön. További információ: [SSH használata a HDInsight segítségével.](../hdinsight-hadoop-linux-use-ssh-unix.md)|
|Webes kapcsolat|Minden HDInsight-fürt biztosítja az Ambari webes felhasználói felületét. Az Ambari webes felülettel egyszerűen figyelheti, konfigurálhatja és kezelheti a fürtön futó szolgáltatásokat. A Storm felhasználói felülete a Storm-fürtökön is elérhető. A Storm felhasználói felülettel böngészőből figyelhetők és kezelhetők a futó Storm-topológiák. További információt a [HDInsight kezelése az Apache Ambari Web felhasználói felületén](../hdinsight-hadoop-manage-ambari.md) és figyelésével című témakörben [talál, és kezelheti az Apache Storm felhasználói felületének dokumentumait.](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-a-topology-using-the-storm-ui)|
|Azure PowerShell és Azure CLI|A PowerShell és az Azure CLI egyaránt biztosít parancssori segédprogramokat, amelyek segítségével az ügyfélrendszer hdinsight és más Azure-szolgáltatások.|
|Visual Studio integráció|Az Azure Data Lake Tools for Visual Studio projektsablonokat tartalmaz a C# Storm topológiák létrehozásához a SCP.NET keretrendszer használatával. A Data Lake Tools emellett megoldások üzembe helyezéséhez, figyeléséhez és felügyeletéhez is tartalmaz eszközöket a HDInsight alatt futó Stormhoz. További információ: [C# Storm-topológiák fejlesztése a HDInsight Tools for Visual Studio szolgáltatással](apache-storm-develop-csharp-visual-studio-topology.md).|

## <a name="integration-with-other-azure-services"></a>Integráció más Azure-szolgáltatásokkal

* __Azure Data Lake Storage__: Az [Azure Data Lake Storage használata az Apache Storm használatával a HDInsight webhelyen.](apache-storm-write-data-lake-store.md)

* __Event Hubs__: Az Event Hubs és egy Storm-fürt együttes használatára vonatkozó példáért tekintse meg az alábbi dokumentumokat:

    * [Események feldolgozása az Azure Event Hubs-ból az Apache Storm segítségével a HDInsight (Java) szolgáltatással](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

    * [Események feldolgozása az Azure Event Hubs-ból az Apache Storm segítségével a HDInsighton (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __SQL Database__, __Cosmos DB__, __Event Hubs__ és __HBase__: A Data Lake Tools for Visual Studio tartalmaz példasablonokat. További információ: [C# topológia fejlesztése apache storm hoz HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="apache-storm-use-cases"></a>Apache Storm használati esetek

Az alábbiakban néhány gyakori forgatókönyvet ismertetünk, amelyek esetén a HDInsight alatt futó Storm használható:

* Eszközök internetes hálózata (IoT)
* Csalások észlelése
* Közösségi hálók adatainak elemzése
* Kinyerés, átalakítás és betöltés (ETL)
* Hálózatfigyelés
* Keresés
* Mobilmarketing

A valós forgatókönyvekről a Vállalatok által az [Apache Storm-dokumentumot című](https://storm.apache.org/Powered-By.html) dokumentumban talál.

## <a name="development"></a>Fejlesztés

A Data Lake Tools for Visual Studio használatával a .NET-fejlesztők C# nyelven tervezhetnek és valósíthatnak meg topológiákat. Létrehozhatók Java- és C#-összetevőket egyaránt használó hibrid topológiák is. További információk: [C#-topológiák fejlesztése HDInsight alatt futó Apache Stormra a Visual Studio használatával](apache-storm-develop-csharp-visual-studio-topology.md)

Java-megoldásokat is fejleszthet tetszőleges IDE használatával. További információ: [Java topologies for Apache Storm on HDInsight](apache-storm-develop-java-topology.md).

A Python is használható Storm-összetevők fejlesztéséhez. További információ: [Apache Storm topológiák fejlesztése python használatával a HDInsight on](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Gyakori fejlesztési minták

### <a name="guaranteed-message-processing"></a>Garantált üzenetfeldolgozás

Az Apache Storm különböző szinteken biztosít garantált üzenetfeldolgozást. Például egy alap szintű Storm-alkalmazás garantálja legalább egyszer a feldolgozást, és a Trident pontosan egyszer tudja garantálni a feldolgozást. Lásd: [Garancia az adatfeldolgozásra](https://storm.apache.org/about/guarantees-data-processing.html) apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Gyakori a bemeneti törzs olvasása, nulla vagy több tuple suple staféta, majd a bemeneti törzs azonnali ellenőrzése a végrehajtási metódus végén. A Storm lehetővé teszi ennek a mintának az automatizálását az [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html) felület segítségével.

### <a name="joins"></a>Illesztések

Az adatstreamek alkalmazások közötti csatlakoztatásának különböző módjai. Például csatlakozhat minden egyes tuple-t több adatfolyamból egy új adatfolyamhoz, vagy csak egy adott ablak ban lévő törzskötegeket. Az illesztés mindkét módszer esetén a [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) használatával történik. Ez egy mód annak definiálására, hogyan legyenek átirányítva a rekordok a boltokhoz.

A következő Java-példában az „1”, „2” és „3” jelű összetevőktől eredő rekordok a fieldsGrouping használatával vannak átirányítva a MyJoiner bolthoz:

```java
builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));
```

### <a name="batches"></a>Kötegek

A Storm rendelkezik egy belső időzítő mechanizmussal, az úgynevezett „tick tuple” rekordórajellel. Ennek segítségével beállíthatja, hogy a topológia milyen gyakran bocsásson ki egy-egy rekordórajelet.

Példa egy rekordórajel C#-összetevőből való használatára: [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Gyorsítótárak

A memóriában történő gyorsítótárazás gyakran használatos a feldolgozást felgyorsító mechanizmusként, mivel a memóriában tartja a gyakran használt objektumokat. Mivel a topológiák több csomópont, és az egyes csomópontokon belül is több folyamat között oszlanak meg, érdemes megfontolni a [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) használatát. A `fieldsGrouping` használata biztosítja, hogy a gyorsítótárban való kereséshez használt mezőket tartalmazó rekordok mindig ugyanahhoz a folyamathoz legyenek irányítva. Ezzel a csoportosítási funkcióval elkerülhető, hogy a különböző folyamatok ismétlődő gyorsítótár-bejegyzéseket hozzanak létre.

### <a name="stream-top-n"></a>A „legfelső N” számú elem streamelése

Ha egy topológia az első N elem értékének kiszámításától függ, az első N értéket párhuzamosan számítsa ki. Ezután összesítse a számítások eredményét egy globális értékben. Ezt a műveletet a [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) használatával, a mezők párhuzamos feldolgozáshoz való továbbításával hajthatja végre. Ezt követően továbbíthat egy bolthoz, amely globálisan meghatározza a legfelső N számú elem értékét.

Példa az első N elem értékének kiszámítására: [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java) példa.

## <a name="logging"></a>Naplózás

Storm az Apache Log4j 2 segítségével naplózza az adatokat. Alapértelmezés szerint nagy mennyiségű adat rögzítése történik, aminek az áttekintése nehézségekkel járhat. A Storm-topológia részeként hozzáadhat egy naplózáskonfigurációs fájlt, amely a naplózás működését vezérli.

A naplózás konfigurálását bemutató példatopológiát a HDInsight alatt futó Stormra vonatkozó [Java-alapú WordCount](apache-storm-develop-java-topology.md) példában tekintheti meg.

## <a name="next-steps"></a>További lépések

Tudjon meg többet a valós idejű elemzési megoldásokról az Apache Storm segítségével a HDInsight-on:

* [Apache Storm-topológia létrehozása és figyelése az Azure HDInsightban](apache-storm-quickstart.md)
* [HDInsight alatt futó Apache Storm példatopológiái](apache-storm-example-topology.md)
