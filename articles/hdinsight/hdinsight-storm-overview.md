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
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 1155f534869f240c1567fa8b791dbcb7750c8a40
ms.lasthandoff: 03/31/2017


---
# <a name="introduction-to-apache-storm-on-hdinsight-real-time-analytics-for-hadoop"></a>A HDInsight alatt futó Apache Storm bemutatása: Valós idejű elemzések a Hadoop használatával

A HDInsight alatt futó Apache Storm lehetővé teszi, hogy elosztott, valós idejű elemzési megoldásokat hozzon létre az Azure-on.

Az Apache Storm egy elosztott, nagy hibatűrésű, nyílt forráskódú számítási rendszer, amely segítségével valós időben dolgozhat fel adatokat a Hadoop használatával. A Storm-megoldások emellett garantált adatfeldolgozást is biztosítanak, amely képes visszajátszani az elsőre sikeresen fel nem dolgozott adatokat.

## <a name="how-does-storm-work"></a>Hogyan működik a Storm?

Az Apache Storm a HDInsightból vagy a Hadoopból ismerős MapReduce-feladatok helyett **topológiákat** futtat. A topológiák több összetevőből állnak, amelyek egy irányított aciklikus gráfba (DAG) vannak rendezve. Az alábbi ábrán az összetevők közti adatfolyamok láthatók egy alapszintű szószámlálási topológiában.

![Példa az összetevők elrendezésére egy Storm-topológiában](./media/hdinsight-storm-overview/wordcount-topology.png)

* A __spout__-összetevők adatokat importálnak a topológiába. Egy vagy több streamet sugároznak a topológiába.

* A __bolt__-összetevők a spout- vagy más bolt-összetevők által sugárzott streameket dolgozzák fel. A boltok esetleg új streameket is sugározhatnak a topológiába. A boltok felelősek az adatok állandó tárolókba, például HDFS- vagy HBase-tárolókba történő kiírásáért.

## <a name="why-use-storm-on-hdinsight"></a>Miért érdemes a HDInsight alatt futó Stormot használni?

A HDInsight alatt futó Apache Storm fő előnyei a következők:

* Felügyelt szolgáltatásként működik, szolgáltatásiszint-szerződésben garantált 99,9%-os elérhetőséggel.

* Könnyen testre szabható a szkriptek fürtön történő futtatásával a létrehozás során vagy után. További információ: [HDInsight-fürtök testre szabása szkriptműveletekkel](hdinsight-hadoop-customize-cluster-linux.md).

* Tetszőleges nyelv használata: a Storm-összetevők számos nyelven megírhatók, például **Java**, **C#** és **Python** nyelven.

    * A Visual Studio integrálható a HDInsighttal C#-topológiák fejlesztéséhez, felügyeletéhez és figyeléséhez. További információ: [C# Storm-topológiák fejlesztése a HDInsight Tools for Visual Studio szolgáltatással](hdinsight-storm-develop-csharp-visual-studio-topology.md).

    * Támogatja a **Trident** Java-kezelőfelületet. Ez a kezelőfelület lehetővé teszi olyan Storm-topológiák létrehozását, amelyek támogatják az üzenetek pontosan egyszeri feldolgozását, a tranzakciós adattároló-megőrzést és számos gyakori stream analytics-műveletet.

* A fürt könnyen méretezhető felfelé és lefelé: a futó Storm-topológiák befolyásolása nélkül adhat hozzá vagy távolíthat el feldolgozó csomópontokat.

* Integráció a következő Azure-szolgáltatásokkal:

    * Event Hubs

    * Virtual Network

    * SQL Database

    * Azure Storage

    * DocumentDB

    * Több HDInsight-fürt képességeit is biztonságosan kihasználhatja az Azure Virtual Network használatával: létrehozhat HDInsight-, HBase- vagy Hadoop-fürtöket használó elemzőfolyamatokat.

A valós idejű elemzési megoldásaikhoz Apache Stormot használó vállalatok listája itt található: [Az Apache Stormot használó vállalatok](https://storm.apache.org/documentation/Powered-By.html)

A Storm használatának kezdő lépéseit itt találja: [A HDInsighton futó Storm bemutatása][gettingstarted].

## <a name="ease-of-creation"></a>Könnyű létrehozás

Egy HDInsight alatt futó új Storm-fürtöt percek alatt kiépíthet. További információkat a Storm-fürtök létrehozásáról [a HDInsight alatt futó Storm bemutatásában](hdinsight-apache-storm-tutorial-get-started-linux.md) talál.

## <a name="ease-of-use"></a>Könnyű használat

* __Secure Shell-kapcsolatok__: A HDInsight-fürt átjárócsomópontjai az interneten keresztül az SSH használatával érhetők el. Ez lehetővé teszi, hogy közvetlenül a fürtön futtasson parancsokat.

  További információ: [Az SSH használata HDInsighttal](hdinsight-hadoop-linux-use-ssh-unix.md).

* __Webes kapcsolatok__: A HDInsight-fürtökön elérhető az Ambari webes felület. Az Ambari webes felület lehetővé teszi a fürtön futó szolgáltatások egyszerű figyelését, konfigurálását és kezelését. A HDInsight alatt futó Stormon emellett a Storm felhasználói felület is elérhető, amellyel böngészőből figyelhetők és kezelhetők a futó Storm-topológiák.

  További információk: [A HDInsight kezelése az Ambari webes kezelőfelületen](hdinsight-hadoop-manage-ambari.md) és [Figyelés és kezelés a Storm kezelői felülettel](hdinsight-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui).

* __Azure PowerShell és CLI__: Az Azure PowerShell és az Azure parancssori felület (CLI) egyaránt biztosítanak olyan parancssori eszközöket, amelyek segítségével az ügyfélrendszerről használható a HDInsight és az egyéb Azure-szolgáltatások.

* __Visual Studio-integráció__: A Data Lake Tools for Visual Studio projektsablonokat tartalmaz C# Storm-topológiák létrehozásához az SCP.Net keretrendszer használatával. A Data Lake Tools emellett megoldások üzembe helyezéséhez, figyeléséhez és felügyeletéhez is tartalmaz eszközöket a HDInsight alatt futó Stormhoz.

  További információ: [C# Storm-topológiák fejlesztése a HDInsight Tools for Visual Studio szolgáltatással](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integráció más Azure-szolgáltatásokkal

* __Azure Data Lake Store__: Egy példát találhat a Data Lake Store és a Storm együttes használatára [az Azure Data Lake a HDInsight alatt futó Apache Stormmal történő használatát](hdinsight-storm-write-data-lake-store.md) bemutató cikkben.

* __Azure Event Hubs__: Az Azure Event Hubs és a Storm együttes használatára vonatkozó példáért lásd az alábbi dokumentumokat:

    * [Java-alapú topológia fejlesztése HDInsight alatt futó Stormhoz](hdinsight-storm-develop-java-topology.md)

    * [Az Azure Event Hubs-eseményközpontok eseményeinek feldolgozása a HDInsight alatt futó Stormmal (C#)](hdinsight-storm-develop-csharp-event-hub-topology.md)

* Az __SQL DB__, __DocumentDB__, __EventHub__ és __HBase__ használatára vonatkozó példákat az Azure Data Lake Tools for Visual Studio tartalmazza sablonokként. További információk: [C#-topológiák fejlesztése a HDInsight alatt futó Stormmal](hdinsight-storm-develop-csharp-visual-studio-topology.md).

## <a name="reliability"></a>Megbízhatóság

Az Apache Storm garantálja, hogy mindig minden bejövő üzenetet feldolgoz, még akkor is, ha az adatok elemzése több száz csomópont között oszlik meg.

A **Nimbus csomó** hasonló szolgáltatásokat nyújt, mint a Hadoop JobTracker, és a **Zookeeper** szolgáltatáson keresztül oszt ki feladatokat a fürt további csomópontjai között. A Zookeeper-csomópontok koordinációt biztosítanak a fürt számára, és elősegítik a kommunikációt a Nimbus és a feldolgozó csomópontokon futó **Supervisor** folyamat között. Ha egy feldolgozó csomópont leáll, arról a Nimbus csomópont értesítést kap, és kiosztja a feladatot és a kapcsolódó adatokat egy másik csomópontnak.

Az alapértelmezett konfiguráció szerint az Apache Storm csak egyetlen Nimbus csomóponttal rendelkezik. A HDInsight alatt futó Storm két Nimbus csomópontot futtat. Ha az elsődleges csomópont meghibásodik, a HDInsight-fürt átvált a másodlagos csomópontra, amíg az elsődleges csomópont helyreállítása be nem következik.

![Diagram: Nimbus, Zookeeper és Supervisor](./media/hdinsight-storm-overview/nimbus.png)

## <a name="scale"></a>Méretezés

Habár a fürtben található csomópontok számát a létrehozáskor megadhatja, elképzelhető, hogy a munkaterheléshez igazodva később növelni vagy csökkenteni szeretné majd a fürt méretét. A HDInsight-fürtök esetében lehetőség van a fürtben található csomópontok számának megváltoztatására, akár még adatfeldolgozás közben is.

> [!NOTE]
> A méretezés során hozzáadott új csomópontok kihasználásához újra egyensúlyba kell hoznia a fürtméret növelése előtt elindított topológiákat.

## <a name="support"></a>Támogatás

A HDInsight alatt futó Stormhoz éjjel-nappal teljes körű, vállalati szintű támogatás áll rendelkezésre. A HDInsight alatt futó Storm emellett szolgáltatásiszint-szerződésben garantált 99,9%-os elérhetőséggel rendelkezik. Ez azt jelenti, hogy a fürt garantáltan az idő legalább 99,9%-ában elérhető lesz kívülről.

További információk: [Azure-ügyfélszolgálat](https://azure.microsoft.com/support/options/).

## <a name="common-use-cases"></a>Gyakori használati helyzetek

Az alábbiakban néhány gyakori forgatókönyvet ismertetünk, amelyek esetén a HDInsight alatt futó Apache Storm használható. 

* Eszközök internetes hálózata (IoT)
* Csalások észlelése
* Közösségi hálók adatainak elemzése
* Kinyerési, átalakítási és betöltési (ETL) feladatok
* Hálózatfigyelés
* Keresés
* Mobilmarketing

Ha valós felhasználási forgatókönyvekkel kapcsolatos információkra kíváncsi, olvassa e [a Storm vállalati felhasználását](https://storm.apache.org/documentation/Powered-By.html) ismertető dokumentumot.

## <a name="development"></a>Fejlesztés

A __Data Lake Tools for Visual Studio__ lehetővé teszi a .NET-fejlesztők számára, hogy __C#__ nyelven tervezzenek és valósítsanak meg topológiákat. Létrehozhatók Java- és C#-összetevőket egyaránt használó hibrid topológiák is.

További információk: [C#-topológiák fejlesztése HDInsight alatt futó Stormra a Visual Studio használatával](hdinsight-storm-develop-csharp-visual-studio-topology.md)

__Java__-megoldásokat tetszőleges IDE használatával is fejleszthet. További információk: [Java-topológiák fejlesztése a HDInsight alatt futó Stormmal](hdinsight-storm-develop-java-topology.md).

A Python is használható Storm-összetevők fejlesztéséhez. További információkért lásd [a Storm-topológiák Python segítségével a HDInsighton való fejlesztésével](hdinsight-storm-develop-python-topology.md) kapcsolatos témakört.

## <a name="common-development-patterns"></a>Gyakori fejlesztési minták

### <a name="guaranteed-message-processing"></a>Garantált üzenetfeldolgozás

A Storm különböző szinteken biztosít garantált üzenetfeldolgozást. Például egy alapszintű Storm-alkalmazás „legalább egyszeri” feldolgozást tud garantálni, míg a Trident „pontosan egyszeri” feldolgozást.

További információk: [Adatfeldolgozási garancia](https://storm.apache.org/about/guarantees-data-processing.html) az apache.org webhelyen.

### <a name="ibasicbolt"></a>IBasicBolt

Gyakori műveleti minta egy bemeneti rekord olvasása, nulla vagy több rekord kibocsátása, majd a bemeneti rekord nyugtázása rögtön a művelet végrehajtásának végén. A Storm lehetővé teszi ennek a mintának az automatizálását az [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) felület segítségével.

### <a name="joins"></a>Illesztések

Az adatstreamek alkalmazások közötti csatlakoztatásának különböző módjai. Például összeillesztheti több adatfolyam minden rekordját egy új adatfolyammá, vagy összeilleszthet csupán rekordkötegeket egy bizonyos ablak alapján. Az illesztés mindkét módszer esetén a [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) használatával történik, amely egy mód annak definiálására, hogyan legyenek átirányítva a rekordok a boltokhoz.

A következő Java-példában az „1”, „2” és „3” jelű összetevőktől eredő rekordok a fieldsGrouping használatával vannak átirányítva a **MyJoiner** bolthoz.

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batching"></a>Kötegelés

A Storm biztosít egy belső időzítő mechanizmust, az úgynevezett „tick tuple” rekordórajelet, amely segítségével x másodpercenként kibocsátható egy köteg.

Példa egy rekordórajel C#-összetevőből való használatára: [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

A Trident ezzel szemben a rekordok kötegenként történő feldolgozásán alapul.

### <a name="caching"></a>Gyorsítótárazás

A memóriában történő gyorsítótárazás gyakran használatos a feldolgozást felgyorsító mechanizmusként, mivel a memóriában tartja a gyakran használt objektumokat. Mivel a topológiák több csomópont, és az egyes csomópontokon belül is több folyamat között oszlanak meg, érdemes megfontolni a [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) használatát. A `fieldsGrouping` biztosítja, hogy a gyorsítótárban való kereséshez használt mezőket tartalmazó rekordok mindig ugyanahhoz a folyamathoz legyenek irányítva. Ezzel a csoportosítási funkcióval elkerülhető, hogy a különböző folyamatok ismétlődő gyorsítótár-bejegyzéseket hozzanak létre.

### <a name="streaming-top-n"></a>A legfelső n számú elem továbbítása adatfolyamként

Ha egy topológia az „első n elem” értékének kiszámításától függ, a legjobb, ha az első N értéket párhuzamosan számítja ki. Ezután összesítse a számítások eredményét egy globális értékben. Ezt a műveletet úgy hajthatja végre, hogy a [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) használatával a mezőket egyesével irányítja át a párhuzamos feldolgozásra, majd azokat átirányítja egy olyan bolthoz, amely globálisan meghatározza a felső n elem értékét.

Példa az „első n” elem értékének kiszámítására: [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) példa.

## <a name="logging"></a>Naplózás

A Storm az Apache Log4j használatával naplózza az információkat. Alapértelmezés szerint nagy mennyiségű adat rögzítése történik, aminek az áttekintése nehézségekkel járhat. A Storm-topológia részeként hozzáadhat egy naplózáskonfigurációs fájlt, amely a naplózás működését vezérli.

A naplózás konfigurálását bemutató példatopológiát a HDInsight alatt futó Stormra vonatkozó [Java-alapú WordCount](hdinsight-storm-develop-java-topology.md) példában tekintheti meg.

## <a name="next-steps"></a>Következő lépések

További információk a HDInsight alatt futó Apache Storm valós idejű elemzési megoldásairól:

* [A HDInsighton futó Storm bemutatása][gettingstarted]
* [HDInsight alatt futó Storm példatopológiái](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md

