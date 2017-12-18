---
title: "Mi az az Apache Storm – Azure HDInsight? | Microsoft Docs"
description: "Az Apache Stormmal valós időben dolgozhat fel adatfolyamokat. Az Azure HDInsighttal könnyen létrehozhat Storm-fürtöket az Azure-felhőben. A Visual Studióval C# használatával hozhat létre Storm-megoldásokat, amelyeket a HDInsight Storm-fürtjeiben helyezhet üzembe."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
keywords: "apache storm használati esetek,storm-fürt,mi az apache storm"
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/08/2017
ms.author: larryfr
ms.openlocfilehash: 2232ae8a838ae2d7feb9a66e0953f006bf45c644
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2017
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Mi az Azure HDInsight alatt futó Apache Storm?

Az [Apache Storm](http://storm.apache.org/) egy elosztott, nagy hibatűrésű, nyílt forráskódú számítási rendszer. A Storm segítségével valós időben dolgozhat fel adatstreameket a Hadooppal. A Storm-megoldások emellett garantált adatfeldolgozást is biztosítanak, amely képes visszajátszani az elsőre sikeresen fel nem dolgozott adatokat.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

## <a name="why-use-storm-on-hdinsight"></a>Miért érdemes a HDInsight alatt futó Stormot használni?

A HDInsight alatt futó Storm a következő szolgáltatásokat biztosítja:

* __99%-os szolgáltatói szerződés (SLA) a Storm üzemidejével kapcsolatban__: További információkért lásd [a HDInsight szolgáltatói szerződésével kapcsolatos információkat ismertető](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/) dokumentumot.

* Könnyen testre szabható a szkriptek Storm-fürtön történő futtatásával a létrehozás során vagy után. További információ: [HDInsight-fürtök testre szabása szkriptműveletekkel](../hdinsight-hadoop-customize-cluster-linux.md).

* **Megoldások létrehozása több nyelven**: A Storm-összetevőket tetszőleges nyelven megírhatja, például Java, C# és Python nyelven is.

    * A Visual Studio és a HDInsight integrációjával biztosítja a C#-topológiák fejlesztését, felügyeletét és figyelését. További információ: [C# Storm-topológiák fejlesztése a HDInsight Tools for Visual Studio szolgáltatással](apache-storm-develop-csharp-visual-studio-topology.md).

    * Támogatja a Trident Java-felületet. Olyan Storm-topológiákat hozhat létre, amelyek támogatják az üzenetek pontosan egyszeri feldolgozását, a tranzakciós adattároló-megőrzést és számos gyakori Stream Analytics-műveletet.

* **Dinamikus méretezés**: A futó Storm-topológiák befolyásolása nélkül adhat hozzá vagy távolíthat el feldolgozó csomópontokat.

    > [!NOTE]
    > A méretezési műveletek során hozzáadott új csomópontok használatához kapcsolja ki, majd kapcsolja be újra a futó topológiákat.

* **Streamingfolyamatok létrehozása több Azure-szolgáltatás használatával**: a HDInsight alatt futó Storm olyan egyéb Azure-szolgáltatásokkal integrálható, mint például az Event Hubs, az SQL Database, az Azure Storage vagy az Azure Data Lake Store.

    Példa Azure-szolgáltatásokkal integrálható megoldásra: [Az Event Hubsról fogadott események feldolgozása a HDInsight alatt futó Stormmal](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

A valós idejű elemzési megoldásaikhoz Apache Stormot használó vállalatok listája itt található: [Az Apache Stormot használó vállalatok](https://storm.apache.org/documentation/Powered-By.html).

A Storm használatának kezdő lépéseit itt találja: [A HDInsighton futó Storm bemutatása][gettingstarted].

## <a name="how-does-storm-work"></a>Hogyan működik a Storm?

A Storm az esetleg ismerős MapReduce-feladatok helyett topológiákat futtat. A Storm-topológiák több összetevőből állnak, amelyek egy irányított aciklikus gráfba (DAG) vannak rendezve. Az adatáramlás a gráf összetevői között zajlik. Minden összetevőbe egy vagy több stream érkezik be, valamint egy vagy több streamet sugároz. Az alábbi ábrán az összetevők közti adatfolyamok láthatók egy alapszintű szószámlálási topológiában:

![Példa az összetevők elrendezésére egy Storm-topológiában](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* A spout-összetevők adatokat importálnak a topológiába. Egy vagy több streamet sugároznak a topológiába.

* A bolt-összetevők a spout- vagy más bolt-összetevők által sugárzott streameket dolgozzák fel. A boltok opcionálisan streameket is sugározhatnak a topológiába. A boltok felelősek az adatok külső szolgáltatásokba vagy tárolókba, például HDFS-, Kafka- vagy HBase-tárolókba történő kiírásáért.

## <a name="reliability"></a>Megbízhatóság

Az Apache Storm garantálja, hogy mindig minden bejövő üzenetet feldolgoz, még akkor is, ha az adatok elemzése több száz csomópont között oszlik meg.

A Nimbus-csomópont hasonló szolgáltatásokat nyújt, mint a Hadoop JobTracker, és a Zookeeperen keresztül rendeli hozzá a feladatokat a fürt más csomópontjaihoz. A Zookeeper-csomópontok koordinációt biztosítanak a fürt számára, és elősegítik a kommunikációt a Nimbus és a feldolgozó csomópontokon futó Supervisor folyamat között. Ha egy feldolgozó csomópont leáll, arról a Nimbus csomópont értesítést kap, és kiosztja a feladatot és a kapcsolódó adatokat egy másik csomópontnak.

Az alapértelmezett konfiguráció szerint az Apache Storm-fürtök csak egyetlen Nimbus csomóponttal rendelkeznek. A HDInsight alatt futó Storm két Nimbus csomóponttal rendelkezik. Ha az elsődleges csomópont meghibásodik, a Storm-fürt átvált a másodlagos csomópontra, amíg az elsődleges csomópont helyreállítása be nem következik. A következő ábra a Storm on HDInsight feladatfolyam-konfigurációját mutatja be:

![Diagram: Nimbus, Zookeeper és Supervisor](./media/apache-storm-overview/nimbus.png)

## <a name="ease-of-creation"></a>Könnyű létrehozás

Az új Storm-fürtök percek alatt létrehozhatók a HDInsightban. További információkat a Storm-fürtök létrehozásáról [a HDInsight alatt futó Storm bemutatásában](apache-storm-tutorial-get-started-linux.md) talál.

## <a name="ease-of-use"></a>Könnyű használat

* __Secure Shell- (SSH-) kapcsolatok__: A Storm-fürt átjárócsomópontjai az interneten keresztül az SSH használatával érhetők el. Az SSH használatával közvetlenül futtathat parancsokat a fürtön.

  További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

* __Webes kapcsolatok__: Minden HDInsight-fürtön elérhető az Ambari webes felület. Az Ambari webes felülettel egyszerűen figyelheti, konfigurálhatja és kezelheti a fürtön futó szolgáltatásokat. A Storm felhasználói felülete a Storm-fürtökön is elérhető. A Storm felhasználói felülettel böngészőből figyelhetők és kezelhetők a futó Storm-topológiák.

  További információt az alábbi dokumentumokban találhat: [A HDInsight kezelése az Ambari webes kezelőfelületen](../hdinsight-hadoop-manage-ambari.md) és [Figyelés és kezelés a Storm kezelői felülettel](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui).

* __Azure PowerShell és Azure CLI__: A PowerShell és a parancssori felület (CLI) egyaránt biztosítanak olyan parancssori eszközöket, amelyek segítségével az ügyfélrendszerről használható a HDInsight és az egyéb Azure-szolgáltatások.

* __Visual Studio-integráció__: Az Azure Data Lake Tools for Visual Studio projektsablonokat tartalmaz C# Storm-topológiák létrehozásához az SCP.Net keretrendszer használatával. A Data Lake Tools emellett megoldások üzembe helyezéséhez, figyeléséhez és felügyeletéhez is tartalmaz eszközöket a HDInsight alatt futó Stormhoz.

  További információ: [C# Storm-topológiák fejlesztése a HDInsight Tools for Visual Studio szolgáltatással](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integráció más Azure-szolgáltatásokkal

* __Azure Data Lake Store__: Egy példát találhat a Data Lake Store és egy Storm-fürt együttes használatára [az Azure Data Lake a HDInsight alatt futó Apache Stormmal történő használatát](apache-storm-write-data-lake-store.md) bemutató cikkben.

* __Event Hubs__: Az Event Hubs és egy Storm-fürt együttes használatára vonatkozó példáért tekintse meg az alábbi dokumentumokat:

    * [Az Azure Event Hubs-eseményközpontok eseményeinek feldolgozása a HDInsight alatt futó Stormmal (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/)

    * [Az Azure Event Hubs-eseményközpontok eseményeinek feldolgozása a HDInsight alatt futó Stormmal (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __SQL Database__, __Cosmos DB__, __Event Hubs__ és __HBase__: A Data Lake Tools for Visual Studio tartalmaz példasablonokat. További információk: [C#-topológiák fejlesztése a HDInsight alatt futó Stormmal](apache-storm-develop-csharp-visual-studio-topology.md).

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
* Keresés
* Mobilmarketing

Ha valós felhasználási forgatókönyvekkel kapcsolatos információkra kíváncsi, olvassa e [a Storm vállalati felhasználását](https://storm.apache.org/documentation/Powered-By.html) ismertető dokumentumot.

## <a name="development"></a>Fejlesztés

A Data Lake Tools for Visual Studio használatával a .NET-fejlesztők C# nyelven tervezhetnek és valósíthatnak meg topológiákat. Létrehozhatók Java- és C#-összetevőket egyaránt használó hibrid topológiák is.

További információk: [C#-topológiák fejlesztése HDInsight alatt futó Stormra a Visual Studio használatával](apache-storm-develop-csharp-visual-studio-topology.md)

Java-megoldásokat is fejleszthet tetszőleges IDE használatával. További információk: [Java-topológiák fejlesztése a HDInsight alatt futó Stormmal](apache-storm-develop-java-topology.md).

A Python is használható Storm-összetevők fejlesztéséhez. További információkért lásd [a Storm-topológiák Python segítségével a HDInsighton való fejlesztésével](apache-storm-develop-python-topology.md) kapcsolatos témakört.

## <a name="common-development-patterns"></a>Gyakori fejlesztési minták

### <a name="guaranteed-message-processing"></a>Garantált üzenetfeldolgozás

Az Apache Storm különböző szinteken biztosít garantált üzenetfeldolgozást. Például egy alapszintű Storm-alkalmazás „legalább egyszeri” feldolgozást tud garantálni, míg a Trident „pontosan egyszeri” feldolgozást.

További információk: [Adatfeldolgozási garancia](https://storm.apache.org/about/guarantees-data-processing.html) az apache.org webhelyen.

### <a name="ibasicbolt"></a>IBasicBolt

Gyakori műveleti minta egy bemeneti rekord olvasása, nulla vagy több rekord kibocsátása, majd a bemeneti rekord nyugtázása rögtön a művelet végrehajtásának végén. A Storm lehetővé teszi ennek a mintának az automatizálását az [IBasicBolt](https://storm.apache.org/releases/1.1.0/javadocs/org/apache/storm/topology/IBasicBolt.html) felület segítségével.

### <a name="joins"></a>Illesztések

Az adatstreamek alkalmazások közötti csatlakoztatásának különböző módjai. Például összeillesztheti több stream minden rekordját egy új streammé, vagy összeilleszthet csupán rekordkötegeket egy bizonyos ablak alapján. Az illesztés mindkét módszer esetén a [fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) használatával történik. Ez egy mód annak definiálására, hogyan legyenek átirányítva a rekordok a boltokhoz.

A következő Java-példában az „1”, „2” és „3” jelű összetevőktől eredő rekordok a fieldsGrouping használatával vannak átirányítva a MyJoiner bolthoz:

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batches"></a>Kötegek

A Storm rendelkezik egy belső időzítő mechanizmussal, az úgynevezett „tick tuple” rekordórajellel. Ennek segítségével beállíthatja, hogy a topológia milyen gyakran bocsásson ki egy-egy rekordórajelet.

Példa egy rekordórajel C#-összetevőből való használatára: [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Gyorsítótárak

A memóriában történő gyorsítótárazás gyakran használatos a feldolgozást felgyorsító mechanizmusként, mivel a memóriában tartja a gyakran használt objektumokat. Mivel a topológiák több csomópont, és az egyes csomópontokon belül is több folyamat között oszlanak meg, érdemes megfontolni a [fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) használatát. A `fieldsGrouping` használata biztosítja, hogy a gyorsítótárban való kereséshez használt mezőket tartalmazó rekordok mindig ugyanahhoz a folyamathoz legyenek irányítva. Ezzel a csoportosítási funkcióval elkerülhető, hogy a különböző folyamatok ismétlődő gyorsítótár-bejegyzéseket hozzanak létre.

### <a name="stream-top-n"></a>A „legfelső N” számú elem streamelése

Ha egy topológia az első N elem értékének kiszámításától függ, az első N értéket párhuzamosan számítsa ki. Ezután összesítse a számítások eredményét egy globális értékben. Ezt a műveletet a [fieldsGrouping](http://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) használatával, a mezők párhuzamos feldolgozáshoz való továbbításával hajthatja végre. Ezt követően továbbíthat egy bolthoz, amely globálisan meghatározza a legfelső N számú elem értékét.

Példa az első N elem értékének kiszámítására: [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java) példa.

## <a name="logging"></a>Naplózás

A Storm az Apache Log4j használatával naplózza az információkat. Alapértelmezés szerint nagy mennyiségű adat rögzítése történik, aminek az áttekintése nehézségekkel járhat. A Storm-topológia részeként hozzáadhat egy naplózáskonfigurációs fájlt, amely a naplózás működését vezérli.

A naplózás konfigurálását bemutató példatopológiát a HDInsight alatt futó Stormra vonatkozó [Java-alapú WordCount](apache-storm-develop-java-topology.md) példában tekintheti meg.

## <a name="next-steps"></a>Következő lépések

További információk a HDInsight alatt futó Storm valós idejű elemzési megoldásairól:

* [Bevezetés a HDInsight-alapú Apache Storm használatába][gettingstarted]
* [HDInsight alatt futó Apache Storm példatopológiái](apache-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: apache-storm-tutorial-get-started-linux.md
