<properties
    pageTitle="A HDInsight alatt futó Apache Storm bemutatása | Microsoft Azure"
    description="Ismerkedjen meg az Apache Stormmal, és sajátítsa el, hogyan építhet a HDInsight alatt futó Storm használatával valós idejű adatelemző megoldásokat a felhőben."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="paulettm"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/18/2016"
   ms.author="larryfr"/>

#A HDInsight alatt futó Apache Storm bemutatása: Valós idejű elemzések a Hadoop használatával

A HDInsight alatt futó Apache Storm lehetővé teszi, hogy elosztott, valós idejű elemző megoldásokat hozzon létre az Azure környezetben az [Apache Hadoop](http://hadoop.apache.org) használatával.

##Mi az Apache Storm?

Az Apache Storm egy elosztott, nagy hibatűrésű, nyílt forráskódú számítási rendszer, amely segítségével valós időben dolgozhat fel adatokat a Hadoop használatával. A Storm megoldások emellett garantált adatfeldolgozást is biztosítanak, amely képes visszajátszani az elsőre sikeresen fel nem dolgozott adatokat.

##Miért érdemes a HDInsight alatt futó Stormot használni?

A HDInsight alatt futó Apache Storm egy felügyelt fürt, amely integrálva van az Azure környezetbe. A szolgáltatás fő előnyei a következők:

* Felügyelt szolgáltatásként működik, szolgáltatásiszint-szerződésben garantált 99,9%-os elérhetőséggel

* Tetszőleges nyelv használata: a **Java**, **C#** és **Python** nyelveken írt Storm-összetevőket is támogatja

    * Programozási nyelvek keverékének támogatása: az adatok Java használatával beolvashatók, majd a C használatával feldolgozhatók#
    
        > [AZURE.NOTE] A C#-topológiák csak a Windows-alapú HDInsight-fürtökön támogatottak.

    * Storm-topológiák létrehozása a **Trident** Java kezelőfelülettel, amelyek támogatják az üzenetek „pontosan egyszeri” feldolgozását, a „tranzakciós” adattároló-megőrzést, és számos gyakori stream analytics-műveletet

* Beépített felfelé és lefelé méretezési lehetőségek: a HDInsight-fürtök a futó Storm-topológiák befolyásolása nélkül méretezhetők

* Integráció további Azure-szolgáltatásokkal, mint az Event Hub, az Azure Virtual Network, az SQL Database, a Blob Storage és a DocumentDB

    * Több HDInsight-fürt képességet is kihasználhatja az Azure Virtual Network használatával: létrehozhat HDInsight-, HBase- vagy Hadoop-fürtöket használó elemzőfolyamatokat

A valós idejű elemzési megoldásaikhoz Apache Stormot használó vállalatok listája itt található: [Az Apache Stormot használó vállalatok](https://storm.apache.org/documentation/Powered-By.html)

A Storm használatának kezdő lépéseit itt találja: [A HDInsighton futó Storm bemutatása][gettingstarted]

###Könnyű kiépítés

Egy HDInsight alatt futó új Storm-fürtöt percek alatt kiépíthet. Adja meg a fürt nevét, méretét, rendszergazdai fiókját és a tárfiókot. Az Azure létrehozza a fürtöt, vele együtt pedig példatopológiákat és egy webes felügyeleti irányítópultot is.

> [AZURE.NOTE] A Storm-fürtök az [Azure parancssori felület](../xplat-cli-install.md) vagy az [Azure PowerShell](../powershell-install-configure.md) használatával is kiépíthetők.

Egy új Storm-fürt a kérelem benyújtásától számított 15 percen belül elkészül, készén a valós idejű elemzési folyamatok elvégzésére.

###Könnyű használat

A __Linux-alapú, HDInsight alatt futó Storm fürtök__ esetében a fürthöz az SSH használatával csatlakozhat, a topológiákat pedig a `storm` parancs használatával indíthatja el és kezelheti. Emellett az Ambari használatával figyelheti a Storm szolgáltatást, a Storm felhasználói felületén pedig figyelheti és kezelheti a futó topológiákat.

További információk a Linux-alapú Storm-fürtökkel való munkavégzésről: [A Linux-alapú HDInsight alatt futó Apache Storm bemutatása](hdinsight-apache-storm-tutorial-get-started-linux.md)

A __Windows-alapú, HDInsight alatt futó Storm-fürtök__ esetében a HDInsight Tools for Visual Studio használatával hozhat létre C#- és hibrid C#/Java-topológiákat, majd küldheti el ezeket a HDInsight alatt futó Storm-fürtre.  

![Storm projekt létrehozása](./media/hdinsight-storm-overview/createproject.png)

A HDInsight Tools for Visual Studio egy olyan kezelőfelülettel is rendelkezik, amelyen keresztül megfigyelhetők és felügyelhetők az egy fürtön található Storm-topológiák.

![A Storm felügyelete](./media/hdinsight-storm-overview/stormview.png)

Példa egy Storm-alkalmazás létrehozására a HDInsight Tools használatával: [C# Storm-topológiák fejlesztése a HDInsight Tools for Visual Studio szolgáltatással](hdinsight-storm-develop-csharp-visual-studio-topology.md)

További információk a HDInsight Tools for Visual Studio szolgáltatásról: [A HDInsight Tools for Visual Studio használatának kezdeti lépései](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md)

A HDInsight alatt futó Storm-fürtök mindegyike tartalmaz egy webalapú Storm-irányítópultot is, amelyen keresztül a fürtön futó Storm-topológiák elküldhetők, megfigyelhetők és kezelhetők.

![A Storm irányítópultja](./media/hdinsight-storm-overview/dashboard.png)

További információk a Storm irányítópultjának használatáról: [HDInsight alatt futó Apache Storm-topológiák üzembe helyezése és felügyelete](hdinsight-storm-deploy-monitor-topology.md)

A HDInsight alatt futó Storm könnyű integrációt biztosít az Azure Event Hubs-eseményközpontokkal az **Event Hub Spout** szolgáltatáson keresztül. Ezen összetevő legfrissebb verziója a következő címen érhető el: [https://github.com/hdinsight/hdinsight-storm-examples/tree/master/lib/eventhubs](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/lib/eventhubs) Az összetevővel kapcsolatban további információk a következő dokumentumokban találhatók:

* [Azure Event Hubs-eseményközpontokat használó C#-topológiák fejlesztése](hdinsight-storm-develop-csharp-event-hub-topology.md)

* [Azure Event Hubs-eseményközpontokat használó Java-topológiák fejlesztése](hdinsight-storm-develop-java-event-hub-topology.md)

###Megbízhatóság

Az Apache Storm garantálja, hogy mindig minden bejövő üzenet feldolgozásra kerüljön, még akkor is, ha az adatok elemzése több száz csomópont között oszlik meg.

A **Nimbus csomó** hasonló szolgáltatásokat nyújt, mint a Hadoop JobTracker, és a **Zookeeper** szolgáltatáson keresztül oszt ki feladatokat a fürt további csomópontjai között. A Zookeeper csomópontok koordinációt biztosítanak a fürt számára, és elősegítik a kommunikációt a Nimbus és a feldolgozó csomópontokon futó **Supervisor** folyamat között. Ha egy feldolgozó csomópont leáll, arról a Nimbus csomópont értesítést kap, és kiosztja a feladatot és a kapcsolódó adatokat egy másik csomópontnak.

Az alapértelmezett konfiguráció szerint az Apache Storm csak egyetlen Nimbus csomóponttal rendelkezik. A HDInsight alatt futó Storm két Nimbus csomópontot futtat. Ha az elsődleges csomópont meghibásodik, a HDInsight-fürt átvált a másodlagos csomópontra, amíg az elsődleges csomópont helyreállítása be nem következik.

![Diagram: Nimbus, Zookeeper és Supervisor](./media/hdinsight-storm-overview/nimbus.png)

###Méretezés

Habár a fürtben található csomópontok számát a létrehozáskor megadhatja, elképzelhető, hogy a munkaterheléshez igazodva később növelni vagy csökkenteni szeretné majd a fürt méretét. A HDInsight-fürtök esetében lehetőség van a fürtben található csomópontok számának megváltoztatására, akár még adatfeldolgozás közben is.

> [AZURE.NOTE] A méretezés során hozzáadott új csomópontok kihasználásához újra egyensúlyba kell hoznia a fürtméret növelése előtt elindított topológiákat.

###Támogatás

A HDInsight alatt futó Stormhoz éjjel-nappal teljes körű, vállalati szintű támogatás áll rendelkezésre. A HDInsight alatt futó Storm emellett szolgáltatásiszint-szerződésben garantált 99,9%-os elérhetőséggel rendelkezik. Ez azt jelenti, hogy a fürt garantáltan az idő legalább 99,9%-ában elérhető lesz kívülről.

##Valós idejű elemzések gyakori alkalmazási helyzetei

Az alábbiakban néhány gyakori forgatókönyvet ismertetünk, amelyek esetén a HDInsight alatt futó Apache Storm használható. Információ a való életben előforduló forgatókönyvekről: [A Storm vállalati felhasználása](https://storm.incubator.apache.org/documentation/Powered-By.html)

* Eszközök internetes hálózata (IoT)
* Csalások észlelése
* Közösségi hálók adatainak elemzése
* Kinyerési, átalakítási és betöltési (ETL) feladatok
* Hálózatfigyelés
* Keresés
* Mobilmarketing

##Hogyan dolgozza fel a HDInsight az adatokat?

Az Apache Storm a HDInsightból vagy a Hadoopból ismerős MapReduce-feladatok helyett **topológiákat** futtat. A HDInsight alatt futó Storm-fürtök kétféle csomópontot tartalmaznak: átjárócsomópontokat, amelyeken a **Nimbus** fut, és feldolgozó csomópontokat, amelyeken a **Supervisor** fut.

* **Nimbus**: A Hadoopból ismert JobTrackerhez hasonlóan az a feladata, hogy kódokat terjesszen a fürtön, feladatokat osszon ki a virtuális gépeknek, és figyelje az esetleges hibákat. A HDInsight két Nimbus csomóponttal rendelkezik, hogy a HDInsight alatt futó Storm szolgáltatásban semmilyen hibalehetőség ne legyen.

* **Supervisor**: Az egyes feldolgozó csomópontok felügyelői felelnek a csomópontok **feldolgozási folyamatainak** indításáért és leállításáért.

* **Feldolgozási folyamat**: A **topológia** egy részét futtatja. A futó topológiák számos feldolgozási folyamat között oszlanak meg a fürtön.

* **Topológia**: Meghatároz egy **adatfolyamokat** feldolgozó számítási grafikont. A MapReduce-feladatoktól eltérően a topológiák addig futnak, amíg le nem állítják őket.

* **Adatfolyam**: **Rekordok** kötetlen gyűjteménye. Az adatfolyamokat **spoutok** és **boltok** hozzák létre, és **boltok** használják fel.

* **Rekord**: Dinamikusan gépelt értékek elnevezett listája.

* **Spout**: Egy adatforrástól érkező adatok felhasználásával bocsát ki egy vagy több **adatfolyamot**.

    > [AZURE.NOTE] Az adatok sok esetben egy üzenetsorból (pl. a Kafkából), Azure Service Bus-üzenetsorokból vagy eseményközpontokból kerülnek beolvasásra. Az üzenetsor garantálja, hogy az adatok kimaradás esetén is megőrződjenek.

* **Bolt**: **Adatfolyamokat** használ fel, **rekordokat** dolgoz fel, és **adatfolyamokat** bocsáthat ki. A boltok felelősek azért is, hogy az adatok egy külső tárolóra legyenek kiírva, például egy üzenetsorba, a HDInsightra, a HBase-re, egy blobra vagy egyéb adattárolóra.

* **Apache Thrift**: Egy szoftveres keretrendszer méretezhető, többnyelvű szolgáltatások fejlesztéséhez. A használatával olyan szolgáltatásokat építhet, amelyek C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk és további nyelveken is működnek.

    * A **Nimbus** egy Thrift-szolgáltatás, a **topológiák** pedig Thrift-definíciók, így a topológiák többféle programnyelv használatával is fejleszthetők.

További információ a Storm összetevőiről: [Storm oktatóanyag][apachetutorial] az apache.org webhelyen.


##Milyen programnyelveket használhatok?

A HDInsight-fürtön futó Storm a C#, Java és Python nyelveket támogatja.

### C&#35;

A HDInsight Tools for Visual Studio lehetővé teszi a .NET-fejlesztők számára, hogy C# nyelven tervezzenek és valósítsanak meg topológiákat. Létrehozhatók Java- és C#-összetevőket egyaránt használó hibrid topológiák is.

További információk: [C#-topológiák fejlesztése HDInsight alatt futó Apache Stormra a Visual Studio használatával](hdinsight-storm-develop-csharp-visual-studio-topology.md)

###Java

A legtöbb Java-alapú példa, amelyekkel találkozik, egyszerű Java vagy Trident nyelvet használ. A Trident egy magas szintű absztrakció, amely megkönnyíti az olyan műveleteket, mint az illesztés, az összesítés, a csoportosítás és a szűrés. Azonban a Trident rekordkötegeket is képes kezelni, szemben a nyers Java megoldásokkal, amelyek a rekordokat egyenként dolgozzák fel.

További információ a Tridentről: [Trident-oktatóanyag](https://storm.incubator.apache.org/documentation/Trident-tutorial.html) az apache.org webhelyen.

Példák a Java- és Trident-topológiákra: [Storm-topológiapéldák listája](hdinsight-storm-example-topology.md) vagy HDInsight-fürtön található Storm-kezdőpéldák.

A Storm-kezdőpéldák a Linux-alapú fürtökön a __ /usr/hdp/current/storm-client/contrib/storm-starter__ könyvtárban, a Windows-alapú fürtökön pedig a **%storm_home%\contrib\storm-starter** könyvtárban találhatók.

##Melyek a gyakori fejlesztési minták?

###Garantált üzenetfeldolgozás

A Storm különböző szinteken biztosít garantált üzenetfeldolgozást. Például egy alapszintű Storm-alkalmazás „legalább egyszeri” feldolgozást tud garantálni, míg a Trident „pontosan egyszeri” feldolgozást.

További információk: [Adatfeldolgozási garancia](https://storm.apache.org/about/guarantees-data-processing.html) az apache.org webhelyen.

###IBasicBolt

Nagyon gyakori műveleti minta egy bemeneti rekord olvasása, nulla vagy több rekord kibocsátása, majd a bemeneti rekord nyugtázása rögtön a művelet végrehajtásának végén. A Storm lehetővé teszi ennek a mintának az automatizálását az [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) kezelőfelület segítségével.

###Illesztések

Két adatfolyam illesztésének módja az egyes alkalmazásokban különböző lehet. Például összeillesztheti több adatfolyam minden rekordját egy új adatfolyammá, vagy összeilleszthet csupán rekordkötegeket egy bizonyos ablak alapján. Az illesztés mindkét módszer esetén a [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) használatával történik, amely egy mód annak definiálására, hogyan legyenek átirányítva a rekordok a boltokhoz.

A következő Java-példában az „1”, „2” és „3” jelű összetevőktől eredő rekordok a fieldsGrouping használatával vannak átirányítva a **MyJoiner** bolthoz.

    builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));

###Kötegelés

A kötegelés többféleképpen is elvégezhető. Egy alapszintű Java Storm-topológia esetében használhat egy egyszerű számlálót, amellyel összekötegelhet x számú rekordot a kibocsátásuk előtt, vagy használhat egy belső időzítő mechanizmust, az úgynevezett rekord-órajelet, amely x másodpercenként hoz létre és bocsát ki egy köteget.

Egy példa a rekord-órajel használatára: [Érzékelőadatok elemzése a HDInsight alatt futó Storm és HBase használatával](hdinsight-storm-sensor-data-analysis.md)

A Trident ezzel szemben a rekordok kötegenként történő feldolgozásán alapul.

###Caching

A memóriában történő gyorsítótárazás gyakran használatos a feldolgozást felgyorsító mechanizmusként, mivel a memóriában tartja a gyakran használt objektumokat. Mivel a topológiák több csomópont, és az egyes csomópontokon belül is több folyamat között oszlanak meg, érdemes a [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) használatával biztosítani, hogy a gyorsítótárban való kereséshez használt mezőket tartalmazó rekordok mindig ugyanahhoz a folyamathoz legyenek irányítva. Így elkerülhető, hogy a különböző folyamatok duplikált gyorsítótár-bejegyzéseket hozzanak létre.

###A legfelső n számú elem továbbítása adatfolyamként

Ha egy topológia a „legfelső n számú elem” értékének kiszámításától függ (például a „legnépszerűbb 5 Twitter-trend”), a legjobb, ha a legfelső N számú értéket párhuzamosan számolja ki, majd e számítások eredményét összesíti egy globális értékben. Ezt úgy teheti meg, hogy a [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) használatával a mezőket egyesével irányítja át a párhuzamos boltokhoz (ezzel mezőérték alapján szétválasztva az adatokat), majd azokat átirányítja egy olyan bolthoz, amely globálisan meghatározza a legfelső n számú elem értékét.

Példa erre: [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) példa.

##Következő lépések

További információk a HDInsight alatt futó Apache Storm valós idejű elemzési megoldásairól:

* [A HDInsight alatt futó Storm bemutatása][gettingstarted]

* [HDInsight alatt futó Storm példatopológiái](hdinsight-storm-example-topology.md)

[stormtrident]: https://storm.incubator.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.incubator.apache.org/documentation/Tutorial.html
[gettingstarted]: hdinsight-apache-storm-tutorial-get-started-linux.md



<!--HONumber=Jun16_HO2-->


