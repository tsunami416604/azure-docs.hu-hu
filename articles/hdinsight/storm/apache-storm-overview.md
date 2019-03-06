---
title: Mi az az Apache Storm? – Azure HDInsight
description: Az Apache Stormmal valós időben dolgozhat fel adatfolyamokat. Az Azure HDInsighttal könnyen létrehozhat Storm-fürtöket az Azure-felhőben. A Visual Studióval C# használatával hozhat létre Storm-megoldásokat, amelyeket a HDInsight Storm-fürtjeiben helyezhet üzembe.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
keywords: apache storm használati esetek,storm-fürt,mi az apache storm
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: ce458328967337976ed713f78ead24e2f1fcdb96
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451274"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Mi az Azure HDInsight alatt futó Apache Storm?

Az [Apache Storm](https://storm.apache.org/) egy elosztott, nagy hibatűrésű, nyílt forráskódú számítási rendszer. A Storm segítségével valós időben dolgozhat fel adatfolyamokat [Apache Hadoop](https://hadoop.apache.org/). A Storm-megoldások emellett garantált adatfeldolgozást is biztosítanak, amely képes visszajátszani az elsőre sikeresen fel nem dolgozott adatokat.

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

## <a name="why-use-apache-storm-on-hdinsight"></a>Miért érdemes a HDInsight Apache Storm használható?

A HDInsight alatt futó Storm a következő szolgáltatásokat biztosítja:

* __99 %-os szolgáltatói szerződés (SLA) a Storm üzemidejével kapcsolatban__: További információkért lásd [a HDInsight szolgáltatói szerződésével kapcsolatos információkat ismertető](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/) dokumentumot.

* Könnyen testre szabható a szkriptek Storm-fürtön történő futtatásával a létrehozás során vagy után. További információ: [HDInsight-fürtök testre szabása szkriptműveletekkel](../hdinsight-hadoop-customize-cluster-linux.md).

* **Megoldások létrehozása több nyelven**: A Storm-összetevőket tetszőleges nyelven megírhatja, például Java, C# és Python nyelven.

    * A Visual Studio és a HDInsight integrációjával biztosítja a C#-topológiák fejlesztését, felügyeletét és figyelését. További információ: [C# Storm-topológiák fejlesztése a HDInsight Tools for Visual Studio szolgáltatással](apache-storm-develop-csharp-visual-studio-topology.md).

    * Támogatja a Trident Java-felületet. Olyan Storm-topológiákat hozhat létre, amelyek támogatják az üzenetek pontosan egyszeri feldolgozását, a tranzakciós adattároló-megőrzést és számos gyakori Stream Analytics-műveletet.

* **Dinamikus méretezés**: A futó Storm-topológiák befolyásolása nélkül adhat hozzá vagy távolíthat el feldolgozó csomópontokat.

    > [!NOTE]  
    > A méretezési műveletek során hozzáadott új csomópontok használatához kapcsolja ki, majd kapcsolja be újra a futó topológiákat.

* **Több Azure-szolgáltatások használata streamelési folyamatokat hozhat létre**: A HDInsight alatt futó Storm integrálható az Event Hubs, az SQL Database, Azure Storage és Azure Data Lake Storage hasonló más Azure-szolgáltatásokat.

    Egy példa megoldás, amely az Azure-szolgáltatásokkal integrálható, lásd: [dolgozza fel az Event hubs-Eseményközpontokból a HDInsight-alapú Apache Storm](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/).

A valós idejű elemzési megoldásaikhoz Apache Stormot használó vállalatok listája itt található: [Az Apache Stormot használó vállalatok](https://storm.apache.org/documentation/Powered-By.html).

A Storm használatának megkezdéséhez lásd [Apache Storm on HDInsight – első lépések][gettingstarted].

## <a name="how-does-apache-storm-work"></a>Hogyan működik az Apache Storm

A Storm helyett topológiákat futtat a [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html) feladatokat, akkor ismernie kell. A Storm-topológiák több összetevőből állnak, amelyek egy irányított aciklikus gráfba (DAG) vannak rendezve. Az adatáramlás a gráf összetevői között zajlik. Minden összetevőbe egy vagy több stream érkezik be, valamint egy vagy több streamet sugároz. Az alábbi ábrán az összetevők közti adatfolyamok láthatók egy alapszintű szószámlálási topológiában:

![Példa az összetevők elrendezésére egy Storm-topológiában](./media/apache-storm-overview/example-apache-storm-topology-diagram.png)

* A spout-összetevők adatokat importálnak a topológiába. Egy vagy több streamet sugároznak a topológiába.

* A bolt-összetevők a spout- vagy más bolt-összetevők által sugárzott streameket dolgozzák fel. A boltok opcionálisan streameket is sugározhatnak a topológiába. A boltok felelősek az adatok külső szolgáltatásokba vagy tárolókba, például HDFS-, Kafka- vagy HBase-tárolókba történő kiírásáért.

## <a name="reliability"></a>Megbízhatóság

Az Apache Storm garantálja, hogy mindig minden bejövő üzenetet feldolgoz, még akkor is, ha az adatok elemzése több száz csomópont között oszlik meg.

A Nimbus csomópont az Apache Hadoop JobTracker hasonló funkcionalitást biztosít, és rendeli hozzá a feladatokat a fürt más csomópontjaira [Apache ZooKeeper](https://zookeeper.apache.org/). A Zookeeper-csomópontok koordinációt biztosítanak a fürt számára, és elősegítik a kommunikációt a Nimbus és a feldolgozó csomópontokon futó Supervisor folyamat között. Ha egy feldolgozó csomópont leáll, arról a Nimbus csomópont értesítést kap, és kiosztja a feladatot és a kapcsolódó adatokat egy másik csomópontnak.

Az alapértelmezett konfiguráció szerint az Apache Storm-fürtök csak egyetlen Nimbus csomóponttal rendelkeznek. A HDInsight alatt futó Storm két Nimbus csomóponttal rendelkezik. Ha az elsődleges csomópont meghibásodik, a Storm-fürt átvált a másodlagos csomópontra, amíg az elsődleges csomópont helyreállítása be nem következik. A következő ábra a Storm on HDInsight feladatfolyam-konfigurációját mutatja be:

![Diagram: Nimbus, Zookeeper és Supervisor](./media/apache-storm-overview/nimbus.png)

## <a name="ease-of-creation"></a>Könnyű létrehozás

Az új Storm-fürtök percek alatt létrehozhatók a HDInsightban. További információkat a Storm-fürtök létrehozásáról [a HDInsight alatt futó Storm bemutatásában](apache-storm-tutorial-get-started-linux.md) talál.

## <a name="ease-of-use"></a>Könnyű használat

* __Secure Shell (SSH-) kapcsolatok__: Elérheti a Storm-fürt átjárócsomópontjaiból az interneten keresztül az SSH használatával. Az SSH használatával közvetlenül futtathat parancsokat a fürtön.

  További információ: [Az SSH használata HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md).

* __Webes kapcsolatok__: Az összes HDInsight-fürtök az Ambari webes felhasználói Felületet biztosítanak. Az Ambari webes felülettel egyszerűen figyelheti, konfigurálhatja és kezelheti a fürtön futó szolgáltatásokat. A Storm felhasználói felülete a Storm-fürtökön is elérhető. A Storm felhasználói felülettel böngészőből figyelhetők és kezelhetők a futó Storm-topológiák.

  További információkért lásd: a [kezelése HDInsight az Apache Ambari webes kezelőfelületen](../hdinsight-hadoop-manage-ambari.md) és [figyelése és kezelése az Apache Storm kezelői felülettel](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-storm-ui) dokumentumokat.

* __Az Azure PowerShell és az Azure klasszikus parancssori felület__: PowerShell és a klasszikus parancssori felület egyaránt biztosítanak olyan parancssori segédprogramok, használhatja a HDInsight és más Azure-szolgáltatásokhoz az ügyfél rendszerből.

* __Visual Studio-integráció__: Az Azure Data Lake Tools for Visual Studio projektsablonokat létrehozása C# Storm-topológiák az SCP.Net keretrendszer használatával. A Data Lake Tools emellett megoldások üzembe helyezéséhez, figyeléséhez és felügyeletéhez is tartalmaz eszközöket a HDInsight alatt futó Stormhoz.

  További információ: [C# Storm-topológiák fejlesztése a HDInsight Tools for Visual Studio szolgáltatással](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="integration-with-other-azure-services"></a>Integráció más Azure-szolgáltatásokkal

* __Az Azure Data Lake Storage__: Példa egy Storm-fürt a Data Lake Storage használatával, lásd: [használata Azure Data Lake Storage a HDInsight-alapú Apache Storm](apache-storm-write-data-lake-store.md).

* __Event Hubs__: Például egy Event Hubs és egy Storm-fürt használatával lásd az alábbi példákat:

    * [Események feldolgozása az Azure Event Hubsból az Apache Storm on HDInsight (Java)](https://azure.microsoft.com/resources/samples/hdinsight-java-storm-eventhub/)

    * [Dolgozza fel az Azure Event hubs Eseményközpontokból a HDInsight-alapú Apache Storm (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __Az SQL Database__, __Cosmos DB__, __az Event Hubs__, és __HBase__: Tartalmaz példasablonokat a Data Lake Tools for Visual Studio. További információkért lásd: [Develop egy C# topológia a Apache Storm on HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

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

További információ valós felhasználási forgatókönyvekről: a [vállalatok hogyan használják az Apache Storm](https://storm.apache.org/documentation/Powered-By.html) dokumentumot.

## <a name="development"></a>Fejlesztés

A Data Lake Tools for Visual Studio használatával a .NET-fejlesztők C# nyelven tervezhetnek és valósíthatnak meg topológiákat. Létrehozhatók Java- és C#-összetevőket egyaránt használó hibrid topológiák is.

További információk: [C#-topológiák fejlesztése HDInsight alatt futó Apache Stormra a Visual Studio használatával](apache-storm-develop-csharp-visual-studio-topology.md)

Java-megoldásokat is fejleszthet tetszőleges IDE használatával. További információkért lásd: [Java-topológiák fejlesztése HDInsight az Apache stormmal](apache-storm-develop-java-topology.md).

A Python is használható Storm-összetevők fejlesztéséhez. További információkért lásd: [Apache Storm-topológiák fejlesztése a HDInsight pythonnal](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Gyakori fejlesztési minták

### <a name="guaranteed-message-processing"></a>Garantált üzenetfeldolgozás

Az Apache Storm különböző szinteken biztosít garantált üzenetfeldolgozást. Például egy alapszintű Storm-alkalmazás tud garantálni, legalább egyszeri feldolgozást, és [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) garantálható a pontosan egyszeri feldolgozását.

További információk: [Adatfeldolgozási garancia](https://storm.apache.org/about/guarantees-data-processing.html) az apache.org webhelyen.

### <a name="ibasicbolt"></a>IBasicBolt

A minta egy bemeneti rekord olvasása, nulla vagy több rekord kibocsátó és a bemeneti rekord azonnal az execute metódus végén majd bosszankodnak szokás. A Storm lehetővé teszi ennek a mintának az automatizálását az [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html) felület segítségével.

### <a name="joins"></a>Illesztések

Az adatstreamek alkalmazások közötti csatlakoztatásának különböző módjai. Például összeillesztheti több stream minden rekordját egy új streammé, vagy összeilleszthet csupán rekordkötegeket egy bizonyos ablak alapján. Az illesztés mindkét módszer esetén a [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) használatával történik. Ez egy mód annak definiálására, hogyan legyenek átirányítva a rekordok a boltokhoz.

A következő Java-példában az „1”, „2” és „3” jelű összetevőktől eredő rekordok a fieldsGrouping használatával vannak átirányítva a MyJoiner bolthoz:

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

### <a name="batches"></a>Kötegek

A Storm rendelkezik egy belső időzítő mechanizmussal, az úgynevezett „tick tuple” rekordórajellel. Ennek segítségével beállíthatja, hogy a topológia milyen gyakran bocsásson ki egy-egy rekordórajelet.

Példa egy rekordórajel C#-összetevőből való használatára: [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Gyorsítótárak

A memóriában történő gyorsítótárazás gyakran használatos a feldolgozást felgyorsító mechanizmusként, mivel a memóriában tartja a gyakran használt objektumokat. Mivel a topológiák több csomópont, és az egyes csomópontokon belül is több folyamat között oszlanak meg, érdemes megfontolni a [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) használatát. A `fieldsGrouping` használata biztosítja, hogy a gyorsítótárban való kereséshez használt mezőket tartalmazó rekordok mindig ugyanahhoz a folyamathoz legyenek irányítva. Ezzel a csoportosítási funkcióval elkerülhető, hogy a különböző folyamatok ismétlődő gyorsítótár-bejegyzéseket hozzanak létre.

### <a name="stream-top-n"></a>A „legfelső N” számú elem streamelése

Ha egy topológia az első N elem értékének kiszámításától függ, az első N értéket párhuzamosan számítsa ki. Ezután összesítse a számítások eredményét egy globális értékben. Ezt a műveletet a [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) használatával, a mezők párhuzamos feldolgozáshoz való továbbításával hajthatja végre. Ezt követően továbbíthat egy bolthoz, amely globálisan meghatározza a legfelső N számú elem értékét.

Példa az első N elem értékének kiszámítására: [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java) példa.

## <a name="logging"></a>Naplózás

Használja a Storm [Apache Log4j 2](https://logging.apache.org/log4j/2.x/) naplózza az adatokat. Alapértelmezés szerint nagy mennyiségű adat rögzítése történik, aminek az áttekintése nehézségekkel járhat. A Storm-topológia részeként hozzáadhat egy naplózáskonfigurációs fájlt, amely a naplózás működését vezérli.

A naplózás konfigurálását bemutató példatopológiát a HDInsight alatt futó Stormra vonatkozó [Java-alapú WordCount](apache-storm-develop-java-topology.md) példában tekintheti meg.

## <a name="next-steps"></a>További lépések

További információ a HDInsight Apache Storm valós idejű elemzési megoldásairól:

* [Bevezetés a HDInsight-alapú Apache Storm használatába][gettingstarted]
* [HDInsight alatt futó Apache Storm példatopológiái](apache-storm-example-topology.md)

[stormtrident]: https://storm.apache.org/documentation/Trident-API-Overview.html
[samoa]: https://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.apache.org/documentation/Tutorial.html
[gettingstarted]: apache-storm-tutorial-get-started-linux.md
