---
title: "A HDInsight alatt futó Apache Storm bemutatása | Microsoft Docs"
description: "A cikkből megismerheti a HDInsight alatt futó Apache Stormot."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 72d54080-1e48-4a5e-aa50-cce4ffc85077
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/31/2017
ms.author: larryfr
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 90043b27360cfea4235c4401bcba9e6a254b8627
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>A HDInsight alatt futó Apache Storm bemutatása: Valós idejű elemzések a Hadoop használatával

Az Azure HDInsight alatt futó Apache Storm használatával elosztott, valós idejű elemzési megoldásokat hozhat létre.

A Storm egy elosztott, nagy hibatűrésű, nyílt forráskódú számítási rendszer. A Storm segítségével valós időben dolgozhat fel adatokat a Hadoop használatával. A Storm-megoldások emellett garantált adatfeldolgozást is biztosítanak, amely képes visszajátszani az elsőre sikeresen fel nem dolgozott adatokat.

## <a name="how-does-storm-work"></a>Hogyan működik a Storm?

A Storm a HDInsightból vagy a Hadoopból esetleg ismerős MapReduce-feladatok helyett topológiákat futtat. A topológiák több összetevőből állnak, amelyek egy irányított aciklikus gráfba (DAG) vannak rendezve. Az alábbi ábrán az összetevők közti adatfolyamok láthatók egy alapszintű szószámlálási topológiában:

![Példa az összetevők elrendezésére egy Storm-topológiában](./media/hdinsight-storm-overview/wordcount-topology.png)

* A spout-összetevők adatokat importálnak a topológiába. Egy vagy több streamet sugároznak a topológiába.

* A bolt-összetevők a spout- vagy más bolt-összetevők által sugárzott streameket dolgozzák fel. A boltok opcionálisan új streameket is sugározhatnak a topológiába. A boltok felelősek az adatok állandó tárolókba, például HDFS- vagy HBase-tárolókba történő kiírásáért.

## <a name="why-use-storm-on-hdinsight"></a>Miért érdemes a HDInsight alatt futó Stormot használni?

A HDInsight alatt futó Storm fő előnyei a következők:

* Felügyelt szolgáltatásként működik, szolgáltatásiszint-szerződésben garantált 99,9%-os elérhetőséggel.

* Könnyen testre szabható a szkriptek fürtön történő futtatásával a létrehozás során vagy után. További információ: [HDInsight-fürtök testre szabása szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

* Többféle nyelvet támogat. A Storm-összetevőket tetszőleges nyelven megírhatja, például Java, C# és Python nyelven.

    * A Visual Studio és a HDInsight integrációjával biztosítja a C#-topológiák fejlesztését, felügyeletét és figyelését. További információ: [C# Storm-topológiák fejlesztése a HDInsight Tools for Visual Studio szolgáltatással](hdinsight-storm-develop-csharp-visual-studio-topology.md).

    * Támogatja a Trident Java-felületet. Olyan Storm-topológiákat hozhat létre, amelyek támogatják az üzenetek pontosan egyszeri feldolgozását, a tranzakciós adattároló-megőrzést és számos gyakori Stream Analytics-műveletet.

*  A fürtök egyszerűen méretezhetők felfelé és lefelé. A futó Storm-topológiák befolyásolása nélkül adhat hozzá vagy távolíthat el feldolgozó csomópontokat.

* Integráció a következő Azure-szolgáltatásokkal:

    * Azure Event Hubs

    * Azure Virtual Network

    * Azure SQL Database

    * Azure Storage

    * Azure Cosmos DB

* Több HDInsight-fürt képességeit is biztonságosan kihasználhatja a Virtual Network használatával. Létrehozhat HDInsight-, HBase- vagy Hadoop-fürtöket használó elemzőfolyamatokat.

A valós idejű elemzési megoldásaikhoz Stormot használó vállalatok listája itt található: [Az Apache Stormot használó vállalatok](https://storm.apache.org/documentation/Powered-By.html).

A Storm használatának kezdő lépéseit itt találja: [A HDInsighton futó Storm bemutatása][gettingstarted].

## <a name="ease-of-creation"></a>Könnyű létrehozás

Az új Storm-fürtök percek alatt kiépíthetők a HDInsightban. További információkat a Storm-fürtök létrehozásáról [a HDInsight alatt futó Storm bemutatásában](hdinsight-apache-storm-tutorial-get-started-linux.md) talál.

## <a name="ease-of-use"></a>Könnyű használat

* __Secure Shell- (SSH-) kapcsolatok__: A HDInsight-fürt átjárócsomópontjai az interneten keresztül az SSH használatával érhetők el. Az SSH használatával közvetlenül futtathat parancsokat a fürtön.

  További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

* __Webes kapcsolatok__: A HDInsight-fürtökön elérhető az Ambari webes felület. Az Ambari webes felülettel egyszerűen figyelheti, konfigurálhatja és kezelheti a fürtön futó szolgáltatásokat. A HDInsight alatt futó Stormon emellett a Storm felhasználói felülete is elérhető. A Storm felhasználói felülettel böngészőből figyelhetők és kezelhetők a futó Storm-topológiák.

  További információk: [A HDInsight kezelése az Ambari webes kezelőfelületen](hdinsight-hadoop-manage-ambari.md) és [Figyelés és kezelés a Storm kezelői felülettel](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui).

* __Azure PowerShell és Azure CLI__: A PowerShell és a parancssori felület (CLI) egyaránt biztosítanak olyan parancssori eszközöket, amelyek segítségével az ügyfélrendszerről használható a HDInsight és az egyéb Azure-szolgáltatások.

* __Visual Studio-integráció__: Az Azure Data Lake Tools for Visual Studio projektsablonokat tartalmaz C# Storm-topológiák létrehozásához az SCP.Net keretrendszer használatával. A Data Lake Tools emellett megoldások üzembe helyezéséhez, figyeléséhez és felügyeletéhez is tartalmaz eszközöket a HDInsight alatt futó Stormhoz.

  További információ: [C# Storm-topológiák fejlesztése a HDInsight Tools for Visual Studio szolgáltatással](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integráció más Azure-szolgáltatásokkal

* __Azure Data Lake Store__: Egy példát találhat a Data Lake Store és a Storm együttes használatára [az Azure Data Lake a HDInsight alatt futó Apache Stormmal történő használatát](hdinsight-storm-write-data-lake-store.md) bemutató cikkben.

* __Event Hubs__: Az Event Hubs és a Storm együttes használatára vonatkozó példáért lásd az alábbi dokumentumokat:

    * [Java-alapú topológia fejlesztése HDInsight alatt futó Stormhoz](hdinsight-storm-develop-java-topology.md)

    * [Az Azure Event Hubs-eseményközpontok eseményeinek feldolgozása a HDInsight alatt futó Stormmal (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md)

* __SQL Database__, __Cosmos DB__, __Event Hubs__ és __HBase__: A Data Lake Tools for Visual Studio tartalmaz példasablonokat. További információk: [C#-topológiák fejlesztése a HDInsight alatt futó Stormmal](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="reliability"></a>Megbízhatóság

A Storm garantálja, hogy mindig minden bejövő üzenetet feldolgoz, még akkor is, ha az adatok elemzése több száz csomópont között oszlik meg.

A Nimbus-csomópont hasonló szolgáltatásokat nyújt, mint a Hadoop JobTracker, és a Zookeeperen keresztül rendeli hozzá a feladatokat a fürt más csomópontjaihoz. A Zookeeper-csomópontok koordinációt biztosítanak a fürt számára, és elősegítik a kommunikációt a Nimbus és a feldolgozó csomópontokon futó Supervisor folyamat között. Ha egy feldolgozó csomópont leáll, arról a Nimbus csomópont értesítést kap, és kiosztja a feladatot és a kapcsolódó adatokat egy másik csomópontnak.

Az alapértelmezett konfiguráció szerint a Storm csak egyetlen Nimbus-csomóponttal rendelkezik. A HDInsight alatt futó Storm két Nimbus csomópontot futtat. Ha az elsődleges csomópont meghibásodik, a HDInsight-fürt átvált a másodlagos csomópontra, amíg az elsődleges csomópont helyreállítása be nem következik. A következő ábra a Storm on HDInsight feladatfolyam-konfigurációját mutatja be:

![Diagram: Nimbus, Zookeeper és Supervisor](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>Méretezés

Habár a fürtben található csomópontok számát a létrehozáskor megadhatja, elképzelhető, hogy a munkaterheléshez igazodva később növelni vagy csökkenteni szeretné majd a fürt méretét. Az összes HDInsight-fürtben módosíthatja a csomópontok számát, akár adatfeldolgozás közben is.

> [!NOTE]
> A méretezés során hozzáadott új csomópontok kihasználásához újra egyensúlyba kell hoznia a fürtméret növelése előtt elindított topológiákat.

## <a name="support"></a>Támogatás

A HDInsight alatt futó Stormhoz teljes körű, vállalati szintű, folyamatos támogatás áll rendelkezésre. A HDInsight alatt futó Storm emellett szolgáltatásiszint-szerződésben garantált 99,9%-os elérhetőséggel rendelkezik. Ez azt jelenti, hogy az egyes fürtök garantáltan az idő legalább 99,9%-ában elérhetők lesznek kívülről.

További információk: [Azure-ügyfélszolgálat](https://azure.microsoft.com/support/options/).

## <a name="common-use-cases"></a>Gyakori használati helyzetek

Az alábbiakban néhány gyakori forgatókönyvet ismertetünk, amelyek esetén a HDInsight alatt futó Storm használható. 

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

További információk: [C#-topológiák fejlesztése HDInsight alatt futó Stormra a Visual Studio használatával](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Java-megoldásokat is fejleszthet tetszőleges IDE használatával. További információk: [Java-topológiák fejlesztése a HDInsight alatt futó Stormmal](hdinsight-storm-develop-java-topology.md).

A Python is használható Storm-összetevők fejlesztéséhez. További információkért lásd [a Storm-topológiák Python segítségével a HDInsighton való fejlesztésével](hdinsight-storm-develop-python-topology.md) kapcsolatos témakört.

## <a name="common-development-patterns"></a>Gyakori fejlesztési minták

### <a name="guaranteed-message-processing"></a>Garantált üzenetfeldolgozás

A Storm különböző szinteken biztosít garantált üzenetfeldolgozást. Például egy alapszintű Storm-alkalmazás „legalább egyszeri” feldolgozást tud garantálni, míg a Trident „pontosan egyszeri” feldolgozást.

További információk: [Adatfeldolgozási garancia](https://storm.apache.org/about/guarantees-data-processing.html) az apache.org webhelyen.

### <a name="ibasicbolt"></a>IBasicBolt

Gyakori műveleti minta egy bemeneti rekord olvasása, nulla vagy több rekord kibocsátása, majd a bemeneti rekord nyugtázása rögtön a művelet végrehajtásának végén. A Storm lehetővé teszi ennek a mintának az automatizálását az [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) felület segítségével.

### <a name="joins"></a>Illesztések

Az adatstreamek alkalmazások közötti csatlakoztatásának különböző módjai. Például összeillesztheti több stream minden rekordját egy új streammé, vagy összeilleszthet csupán rekordkötegeket egy bizonyos ablak alapján. Az illesztés mindkét módszer esetén a [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) használatával történik, amely egy mód annak definiálására, hogyan legyenek átirányítva a rekordok a boltokhoz.

A következő Java-példában az „1”, „2” és „3” jelű összetevőktől eredő rekordok a fieldsGrouping használatával vannak átirányítva a MyJoiner bolthoz:

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batches"></a>Kötegek

A Storm biztosít egy belső időzítő mechanizmust, az úgynevezett „tick tuple” rekordórajelet, amely segítségével x másodpercenként kibocsátható egy köteg.

Példa egy rekordórajel C#-összetevőből való használatára: [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

A Trident a rekordok kötegenként történő feldolgozásán alapul.

### <a name="caches"></a>Gyorsítótárak

A memóriában történő gyorsítótárazás gyakran használatos a feldolgozást felgyorsító mechanizmusként, mivel a memóriában tartja a gyakran használt objektumokat. Mivel a topológiák több csomópont, és az egyes csomópontokon belül is több folyamat között oszlanak meg, érdemes megfontolni a [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) használatát. A `fieldsGrouping` használata biztosítja, hogy a gyorsítótárban való kereséshez használt mezőket tartalmazó rekordok mindig ugyanahhoz a folyamathoz legyenek irányítva. Ezzel a csoportosítási funkcióval elkerülhető, hogy a különböző folyamatok ismétlődő gyorsítótár-bejegyzéseket hozzanak létre.

### <a name="stream-top-n"></a>A „legfelső N” számú elem streamelése

Ha egy topológia az első N elem értékének kiszámításától függ, az első N értéket párhuzamosan számítsa ki. Ezután összesítse a számítások eredményét egy globális értékben. Ezt a műveletet úgy hajthatja végre, hogy a [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) használatával a mezőket egyesével irányítja át a párhuzamos feldolgozásra, majd azokat átirányítja egy olyan bolthoz, amely globálisan meghatározza a felső n elem értékét.

Példa az első N elem értékének kiszámítására: [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) példa.

## <a name="logging"></a>Naplózás

A Storm az Apache Log4j használatával naplózza az információkat. Alapértelmezés szerint nagy mennyiségű adat rögzítése történik, aminek az áttekintése nehézségekkel járhat. A Storm-topológia részeként hozzáadhat egy naplózáskonfigurációs fájlt, amely a naplózás működését vezérli.

A naplózás konfigurálását bemutató példatopológiát a HDInsight alatt futó Stormra vonatkozó [Java-alapú WordCount](hdinsight-storm-develop-java-topology.md) példában tekintheti meg.

## <a name="next-steps"></a>Következő lépések

További információk a HDInsight alatt futó Storm valós idejű elemzési megoldásairól:

* [Ismerkedés a HDInsight-alapú Stormmal][gettingstarted]
* [HDInsight alatt futó Storm példatopológiái](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

