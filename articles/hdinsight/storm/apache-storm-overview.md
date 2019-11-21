---
title: Mi az az Apache Storm? – Azure HDInsight
description: Az Apache Stormmal valós időben dolgozhat fel adatfolyamokat. Az Azure HDInsighttal könnyen létrehozhat Storm-fürtöket az Azure-felhőben. A Visual Studióval C# használatával hozhat létre Storm-megoldásokat, amelyeket a HDInsight Storm-fürtjeiben helyezhet üzembe.
author: hrasheed-msft
ms.reviewer: jasonh
keywords: apache storm használati esetek,storm-fürt,mi az apache storm
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: overview
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 9c7e49fe522859f97f00f760822d5eef60db5f69
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228858"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Mi az Azure HDInsight alatt futó Apache Storm?

Az [Apache Storm](https://storm.apache.org/) egy elosztott, nagy hibatűrésű, nyílt forráskódú számítási rendszer. You can use Storm to process streams of data in real time with [Apache Hadoop](https://hadoop.apache.org/). A Storm-megoldások emellett garantált adatfeldolgozást is biztosítanak, amely képes visszajátszani az elsőre sikeresen fel nem dolgozott adatokat.

## <a name="why-use-apache-storm-on-hdinsight"></a>Why use Apache Storm on HDInsight?

A HDInsight alatt futó Storm a következő szolgáltatásokat biztosítja:

* __99%-os szolgáltatói szerződés (SLA) a Storm üzemidejével kapcsolatban__: További információkért lásd [a HDInsight szolgáltatói szerződésével kapcsolatos információkat ismertető](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/) dokumentumot.

* Könnyen testre szabható a szkriptek Storm-fürtön történő futtatásával a létrehozás során vagy után. További információ: [HDInsight-fürtök testre szabása szkriptműveletekkel](../hdinsight-hadoop-customize-cluster-linux.md).

* **Megoldások létrehozása több nyelven**: A Storm-összetevőket tetszőleges nyelven megírhatja, például Java, C# és Python nyelven is.

    * A Visual Studio és a HDInsight integrációjával biztosítja a C#-topológiák fejlesztését, felügyeletét és figyelését. További információ: [C# Storm-topológiák fejlesztése a HDInsight Tools for Visual Studio szolgáltatással](apache-storm-develop-csharp-visual-studio-topology.md).

    * Támogatja a Trident Java-felületet. Olyan Storm-topológiákat hozhat létre, amelyek támogatják az üzenetek pontosan egyszeri feldolgozását, a tranzakciós adattároló-megőrzést és számos gyakori Stream Analytics-műveletet.

* **Dinamikus méretezés**: A futó Storm-topológiák befolyásolása nélkül adhat hozzá vagy távolíthat el feldolgozó csomópontokat.

    * A méretezési műveletek során hozzáadott új csomópontok használatához kapcsolja ki, majd kapcsolja be újra a futó topológiákat.

* **Create streaming pipelines using multiple Azure services**: Storm on HDInsight integrates with other Azure services such as Event Hubs, SQL Database, Azure Storage, and Azure Data Lake Storage.

    For an example solution that integrates with Azure services, see [Process events from Event Hubs with Apache Storm on HDInsight](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

A valós idejű elemzési megoldásaikhoz Apache Stormot használó vállalatok listája itt található: [Az Apache Stormot használó vállalatok](https://storm.apache.org/documentation/Powered-By.html).

To get started using Storm, see [Create and monitor an Apache Storm topology in Azure HDInsight](apache-storm-quickstart.md).

## <a name="how-does-apache-storm-work"></a>How does Apache Storm work

Storm runs topologies instead of the [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html)  jobs that you might be familiar with. A Storm-topológiák több összetevőből állnak, amelyek egy irányított aciklikus gráfba (DAG) vannak rendezve. Az adatáramlás a gráf összetevői között zajlik. Minden összetevőbe egy vagy több stream érkezik be, valamint egy vagy több streamet sugároz. Az alábbi ábrán az összetevők közti adatfolyamok láthatók egy alapszintű szószámlálási topológiában:

![Példa az összetevők elrendezésére egy Storm-topológiában](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* A spout-összetevők adatokat importálnak a topológiába. Egy vagy több streamet sugároznak a topológiába.

* A bolt-összetevők a spout- vagy más bolt-összetevők által sugárzott streameket dolgozzák fel. A boltok opcionálisan streameket is sugározhatnak a topológiába. A boltok felelősek az adatok külső szolgáltatásokba vagy tárolókba, például HDFS-, Kafka- vagy HBase-tárolókba történő kiírásáért.

## <a name="reliability"></a>Megbízhatóság

Az Apache Storm garantálja, hogy mindig minden bejövő üzenetet feldolgoz, még akkor is, ha az adatok elemzése több száz csomópont között oszlik meg.

The Nimbus node provides functionality similar to the Apache Hadoop JobTracker, and it assigns tasks to other nodes in a cluster through [Apache ZooKeeper](https://zookeeper.apache.org/). A Zookeeper-csomópontok koordinációt biztosítanak a fürt számára, és elősegítik a kommunikációt a Nimbus és a feldolgozó csomópontokon futó Supervisor folyamat között. Ha egy feldolgozó csomópont leáll, arról a Nimbus csomópont értesítést kap, és kiosztja a feladatot és a kapcsolódó adatokat egy másik csomópontnak.

Az alapértelmezett konfiguráció szerint az Apache Storm-fürtök csak egyetlen Nimbus csomóponttal rendelkeznek. A HDInsight alatt futó Storm két Nimbus csomóponttal rendelkezik. Ha az elsődleges csomópont meghibásodik, a Storm-fürt átvált a másodlagos csomópontra, amíg az elsődleges csomópont helyreállítása be nem következik. A következő ábra a Storm on HDInsight feladatfolyam-konfigurációját mutatja be:

![Diagram: Nimbus, Zookeeper és Supervisor](./media/apache-storm-overview/storm-diagram-nimbus.png)

## <a name="ease-of-creation"></a>Könnyű létrehozás

Az új Storm-fürtök percek alatt létrehozhatók a HDInsightban. For more information on creating a Storm cluster, see [Create Apache Hadoop clusters using the Azure portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

## <a name="ease-of-use"></a>Egyszerű használat

* __Secure Shell- (SSH-) kapcsolatok__: A Storm-fürt átjárócsomópontjai az interneten keresztül az SSH használatával érhetők el. Az SSH használatával közvetlenül futtathat parancsokat a fürtön.

  További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

* __Webes kapcsolatok__: Minden HDInsight-fürtön elérhető az Ambari webes felület. Az Ambari webes felülettel egyszerűen figyelheti, konfigurálhatja és kezelheti a fürtön futó szolgáltatásokat. A Storm felhasználói felülete a Storm-fürtökön is elérhető. A Storm felhasználói felülettel böngészőből figyelhetők és kezelhetők a futó Storm-topológiák.

  For more information, see the [Manage HDInsight using the Apache Ambari Web UI](../hdinsight-hadoop-manage-ambari.md) and [Monitor and manage using the Apache Storm UI](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-a-topology-using-the-storm-ui) documents.

* __Azure PowerShell and Azure Classic CLI__: PowerShell and classic CLI both provide command-line utilities that you can use from your client system to work with HDInsight and other Azure services.

* __Visual Studio integration__: Azure Data Lake Tools for Visual Studio include project templates for creating C# Storm topologies by using the SCP.NET framework. A Data Lake Tools emellett megoldások üzembe helyezéséhez, figyeléséhez és felügyeletéhez is tartalmaz eszközöket a HDInsight alatt futó Stormhoz.

  További információ: [C# Storm-topológiák fejlesztése a HDInsight Tools for Visual Studio szolgáltatással](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integráció más Azure-szolgáltatásokkal

* __Azure Data Lake Storage__: For an example of using Data Lake Storage with a Storm cluster, see [Use Azure Data Lake Storage with Apache Storm on HDInsight](apache-storm-write-data-lake-store.md).

* __Event Hubs__: Az Event Hubs és egy Storm-fürt együttes használatára vonatkozó példáért tekintse meg az alábbi dokumentumokat:

    * [Process events from Azure Event Hubs with Apache Storm on HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

    * [Process events from Azure Event Hubs with Apache Storm on HDInsight (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __SQL Database__, __Cosmos DB__, __Event Hubs__ és __HBase__: A Data Lake Tools for Visual Studio tartalmaz példasablonokat. For more information, see [Develop a C# topology for Apache Storm on HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="support"></a>Támogatás

A HDInsight alatt futó Stormhoz teljes körű, vállalati szintű, folyamatos támogatás áll rendelkezésre. A HDInsight alatt futó Storm emellett szolgáltatásiszint-szerződésben garantált 99,9%-os elérhetőséggel rendelkezik. Ennek értelmében a Microsoft garantálja, hogy az egyes Storm-fürtök az idő legalább 99,9%-ában elérhetők lesznek kívülről.

További információk: [Azure-ügyfélszolgálat](https://azure.microsoft.com/support/options/).

## <a name="apache-storm-use-cases"></a>Apache Storm használati esetek

Az alábbiakban néhány gyakori forgatókönyvet ismertetünk, amelyek esetén a HDInsight alatt futó Storm használható:

* Eszközök internetes hálózata (IoT)
* Csalások észlelése
* Közösségi hálók adatainak elemzése
* Kinyerés, átalakítás és betöltés (ETL)
* Hálózatfigyelés
* Search
* Mobilmarketing

For information about real-world scenarios, see the [How companies are using Apache Storm](https://storm.apache.org/Powered-By.html) document.

## <a name="development"></a>Fejlesztés

A Data Lake Tools for Visual Studio használatával a .NET-fejlesztők C# nyelven tervezhetnek és valósíthatnak meg topológiákat. Létrehozhatók Java- és C#-összetevőket egyaránt használó hibrid topológiák is.

További információk: [C#-topológiák fejlesztése HDInsight alatt futó Apache Stormra a Visual Studio használatával](apache-storm-develop-csharp-visual-studio-topology.md)

Java-megoldásokat is fejleszthet tetszőleges IDE használatával. For more information, see [Develop Java topologies for Apache Storm on HDInsight](apache-storm-develop-java-topology.md).

A Python is használható Storm-összetevők fejlesztéséhez. For more information, see [Develop Apache Storm topologies using Python on HDInsight](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Gyakori fejlesztési minták

### <a name="guaranteed-message-processing"></a>Garantált üzenetfeldolgozás

Az Apache Storm különböző szinteken biztosít garantált üzenetfeldolgozást. For example, a basic Storm application can guarantee at-least-once processing, and [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) can guarantee exactly once processing.

További információk: [Adatfeldolgozási garancia](https://storm.apache.org/about/guarantees-data-processing.html) az apache.org webhelyen.

### <a name="ibasicbolt"></a>IBasicBolt

The pattern of reading an input tuple, emitting zero or more tuples, and then acknowledging the input tuple immediately at the end of the execute method is common. A Storm lehetővé teszi ennek a mintának az automatizálását az [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html) felület segítségével.

### <a name="joins"></a>Illesztések

Az adatstreamek alkalmazások közötti csatlakoztatásának különböző módjai. Például összeillesztheti több stream minden rekordját egy új streammé, vagy összeilleszthet csupán rekordkötegeket egy bizonyos ablak alapján. Az illesztés mindkét módszer esetén a [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) használatával történik. Ez egy mód annak definiálására, hogyan legyenek átirányítva a rekordok a boltokhoz.

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

Storm uses [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) to log information. Alapértelmezés szerint nagy mennyiségű adat rögzítése történik, aminek az áttekintése nehézségekkel járhat. A Storm-topológia részeként hozzáadhat egy naplózáskonfigurációs fájlt, amely a naplózás működését vezérli.

A naplózás konfigurálását bemutató példatopológiát a HDInsight alatt futó Stormra vonatkozó [Java-alapú WordCount](apache-storm-develop-java-topology.md) példában tekintheti meg.

## <a name="next-steps"></a>Következő lépések

Learn more about real-time analytics solutions with Apache Storm on HDInsight:

* [Create and monitor an Apache Storm topology in Azure HDInsight](apache-storm-quickstart.md)
* [HDInsight alatt futó Apache Storm példatopológiái](apache-storm-example-topology.md)
