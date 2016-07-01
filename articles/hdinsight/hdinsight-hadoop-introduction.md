<properties
    pageTitle="Mi a felhőalapú Hadoop? A HDInsight bemutatása | Microsoft Azure"
    description="Mi a felhőalapú Hadoop, és hogyan kezelhető a HDInsightban? A Hadoop összetevőinek és a big data típusú adatok elemzésének bemutatása."
    keywords="big data analysis,introduction to hadoop,what is hadoop,hadoop in the cloud"
    services="hdinsight"
    documentationCenter=""
    authors="cjgronlund"
    manager="paulettm"
    editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/29/2016"
   ms.author="cgronlun"/>


# Mi a felhőalapú Hadoop? A HDInsightban található és a big data típusú adatok elemzésére szolgáló Hadoop-összetevők bemutatása

Megismerkedhet a Hadooppal és rendszerével, valamint a big data típusú adatokkal az Azure HDInsight rendszerén belül. Megtudhatja, hogyan működik a Hadoop a HDInsightban, és melyek a big data típusú adatok elemzéséhez szükséges Hadoop-összetevők, gyakori fogalmak és forgatókönyvek. Továbbá olyan, a Hadooppal kapcsolatos oktatóanyagokkal, dokumentációkkal és forrásanyagokkal ismerkedhet meg, amelyek segítenek használni a felhőalapú Hadoop szolgáltatást a HDInsightban.

## Mi a felhőalapú Hadoop szolgáltatás a HDInsightban?

Az Azure HDInsight felhőalapú, felügyelt Apache Hadoop-fürtöket helyez üzembe és tesz elérhetővé, amelyek egy nagy mértékben megbízható és hozzáférhető szoftver-keretrendszert biztosítanak a big data feldolgozásához, elemzéséhez és jelentésekbe foglalásához. A HDInsight a **Hortonworks Data Platform (HDP)** Hadoop-terjesztést használja. A Hadoop gyakran hivatkozik a Hadoop-összetevők teljes rendszerére, amely magában foglalja az Apache HBase, Apache Spark és Apache Storm eszközöket, illetve a Hadoop alá tartozó más technológiákat. A részleteket lásd a lenti [A Hadoop ökoszisztémájának áttekintése a HDInsightban](#overview) című szakaszban.


## Mik azok a big data típusú adatok?
A big data az adatok egyre növekvő kötetekben, egyre nagyobb sebességgel történő gyűjtésére utal, és strukturálatlan formátumok egyre bővülő választéka és változatos szemantikai környezetek jellemzik.

A big data kifejezés bármilyen nagyobb digitális információhalmazra utalhat egy Twitter-hírcsatorna szövegeitől vagy egy ipari berendezés érzékelőitől származó információktól kezdve egy online katalógusban végzett vásárlói keresésekig és vásárlásokig. A big data típusú adatok lehetnek előzményadatok (azaz tárolt adatok) vagy valós idejű adatok (azaz közvetlenül a forrásból streamelt adatok).

Ahhoz, hogy a big data típusú adatok a gyakorlatban is használható információkkal vagy részletekkel szolgáljanak, a vonatkozó adatok összegyűjtése és a megfelelő kérdések feltétele mellett elengedhetetlen ezen adatok hozzáférhetősége, megtisztítása, elemzése és hasznosítható formában történő bemutatása. Ebben segíthet a HDInsight rendszerében a Hadoop big data elemzése.


## <a name="overview"></a>A Hadoop ökoszisztémájának áttekintése a HDInsightban

A HDInsight a gyorsan fejlődő Apache Hadoop technológiák felhőalapú megvalósítása a Microsoft Azure felületén, és az első számú megoldás a big data típusú adatok elemzésére. Tartalmazza többek között az Apache Spark, a HBase, a Storm, a Pig, a Hive, a Sqoop, az Oozie és az Ambari implementációit. A HDInsight emellett olyan üzletiintelligencia- (BI-) eszközökkel is integrálható, mint a Power BI, az Excel, az SQL Server Analysis Services és az SQL Server Reporting Services.

### Fürtök a Linuxon

Az Azure HDInsight Hadoop-fürtöket helyez üzembe és tesz elérhetővé a felhőben, **Linux rendszereken**. A részletekért tekintse meg a lenti táblázatot.

Kategória | Hadoop on Linux
---------| -------------------
**Fürt operációs rendszere** | Ubuntu 12.04 Long Term Support (LTS)
**Fürt típusa** | Hadoop, Spark, HBase, Storm
**Környezet** | Azure portál, Azure parancssori felület (CLI), Azure PowerShell
**Fürt felhasználói felülete** | Ambari
**Távelérés** | Secure Shell (SSH), REST API, ODBC, JDBC



### Hadoop, HBase, Spark, Storm és személyre szabott fürtök

A HDInsight az Apache Hadoop, a Spark, a HBase és a Storm fürtökhöz kínál különböző konfigurációkat. A [fürtöket parancsfájlműveletekkel is testreszabhatja](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop** („Query” számítási feladat): megbízható adattárolást biztosít a [HDFS](#HDFS) és egy egyszerű [MapReduce](#mapreduce) programozási modell segítségével az adatok párhuzamos feldolgozásához és elemzéséhez.

* **<a target="_blank" href="http://spark.apache.org/">Apache Spark</a>**: egy párhuzamos feldolgozást végző keretrendszer, amely támogatja a memórián belüli feldolgozást a big data elemző alkalmazások, az SQL Spark folyamatok, a streamelési adatok és a gépi tanulás teljesítményének növelése érdekében. Lásd: [Overview: What is Apache Spark in HDInsight?](hdinsight-apache-spark-overview.md) (Áttekintés: Mi az Apache Spark a HDInsightban?)

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>** („NoSQL” számítási feladat): egy Hadoopra épülő NoSQL-adatbázis, amely véletlenszerű hozzáférést és nagymértékű következetességet biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatok számára, akár egy több milliárd sorból és több millió oszlopból álló táblázat esetén is. Lásd: [Overview of HBase on HDInsight](hdinsight-hbase-overview.md) (A HDInsight-alapú HBase áttekintése).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>** („Stream” számítási feladat): egy elosztott, valós idejű számítási rendszer a nagy méretű adatstreamek gyors feldolgozására. A Storm a HDInsightban felügyelt fürtként érhető el. Lásd: [Analyze real-time sensor data using Storm and Hadoop](hdinsight-storm-sensor-data-analysis.md) (Valós idejű érzékelőadatok elemzése a Storm és a Hadoop segítségével).

#### Példák testreszabási parancsfájlokra

A parancsfájlműveletek olyan parancsfájlok, amelyek a fürtök kiépítése során futnak, és segítségükkel további összetevők telepíthetők a fürtön. A Linux-alapú fürtök esetében ezek a Bash parancsfájlok.

A HDInsight csapat az alábbi parancsfájlpéldákat biztosítja:

* [Hue](hdinsight-hadoop-hue-linux.md): a fürtökkel folytatott kommunikációhoz használható webalkalmazás-készlet. Csak Linux fürtökön használható.

* [Giraph](hdinsight-hadoop-giraph-install-linux.md): diagramfeldolgozás dolgok vagy személyek közötti kapcsolatok modellezésére.

* [R](hdinsight-hadoop-r-scripts-linux.md): egy nyílt forráskódú nyelv és környezet, amely statisztikai számításokhoz használható a gépi tanulási folyamatok során.

* [Solr](hdinsight-hadoop-solr-install-linux.md): egy vállalati szintű keresési platform, ami teljes szöveges keresést tesz lehetővé az adatokon.

Az egyéni parancsfájlművelet-fejlesztéssel kapcsolatos további információkért lásd: [Script Action development with HDInsight](hdinsight-hadoop-script-actions-linux.md) (Parancsfájlműveletek fejlesztése a HDInsighttal).

## HDInsight Standard és HDInsight Prémium

A HDInsight a big data felhőajánlatokat kétféle – Standard és Prémium – kategóriában biztosítja. A HDInsight Standard egy vállalati szintű fürtöt biztosít, amelyet a vállalkozások a big data számítási feladatokat futtatásához használhatnak. A HDInsight Prémium ezen felül fejlett elemzési és biztonsági képességeket biztosít a HDInsight-fürtök számára. További információért lásd: [Azure HDInsight Prémium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## Melyek a Hadoop összetevői és segédprogramjai?

A HDInsight-fürtök az alábbi összetevőket és segédprogramokat tartalmazzák.

* **[Ambari](#ambari)**: fürtkiépítés, kezelés, megfigyelés és segédprogramok.

* **[Avro](#avro)** (Microsoft .NET könyvtár az Avro-hoz): adatszerializálás a Microsoft .NET-környezethez.

* **[Hive és HCatalog](#hive)**: SQL-szerű (Structured Query Language) lekérdezés, valamint egy táblázat- és tárolókezelési réteg.

* **[Mahout](#mahout)**: gépi tanulás.

* **[MapReduce](#mapreduce)**: örökölt keretrendszer a Hadoop által elosztott feldolgozáshoz és erőforrás-kezeléshez. Lásd még: [YARN](#yarn), a következő generációs erőforrás-keretrendszer.

* **[Oozie](#oozie)**: munkafolyamat-kezelés.

* **[Phoenix](#phoenix)**: HBase-en alapuló relációs adatbázisréteg.

* **[Pig](#pig)**: egyszerűbb parancsfájlkezelés MapReduce-átalakításokhoz.

* **[Sqoop](#sqoop)**: adatok importálása és exportálása.

* **[Tez](#tez)**: lehetővé teszi, hogy az adatigényes folyamatok hatékonyan és méretezhetően fussanak.

* **[YARN](#yarn)**: a Hadoop alap függvénytárának része és a MapReduce szoftverkeretrendszer következő generációja.

* **[ZooKeeper](#zookeeper)**: elosztott rendszerek folyamatait koordinálja.

> [AZURE.NOTE] Az egyes összetevőkkel és verziókkal kapcsolatos további információkért lásd: [What's new in the Hadoop cluster versions provided by HDInsight?][component-versioning] (A HDInsight által biztosított Hadoop-fürtverziók újdonságai).

### <a name="ambari"></a>Ambari

Az Apache Ambari az Apache Hadoop-fürtök kiépítésére, kezelésére és megfigyelésére szolgál. Operátori eszközök intuitív gyűjteményét és egy robusztus API-készletet foglal magában, amelyek elfedik a Hadoop összetettségét, és leegyszerűsítik a fürtök működését. A Linux-alapú HDInsight-fürtök az Ambari webes felhasználói felületet és az Ambari REST API-t, a Windows-alapú fürtök pedig a REST API egy adott részét biztosítják. Az Ambari Views on HDInsight fürtök beépülő felhasználói felületi képességeket kínálnak.

Lásd: [Manage HDInsight clusters using Ambari](hdinsight-hadoop-manage-ambari.md) (HDInsight-fürtök kezelése az Ambari segítségével) (csak Linux esetén), [Monitor Hadoop clusters in HDInsight using the Ambari API](hdinsight-monitor-use-ambari-api.md) (Hadoop-fürtök megfigyelése a HDInsightban az Ambari API segítségével) és <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">referenciák az Apache Ambari API-hoz</a>.

### <a name="avro"></a>Avro (Microsoft .NET könyvtár az Avro-hoz)

Az Avro Microsoft .NET könyvtára Apache Avro kompakt bináris adatcsere-formátumot alkalmaz a Microsoft .NET környezetben végrehajtott szerializációkhoz. A <a target="_blank" href="http://www.json.org/">JavaScript Object Notation (JSON)</a> segítségével meghatároz egy nyelvtől független sémát, amely lehetővé teszi a nyelvi együttműködést, azaz az egyik nyelvben szerializált adatok olvashatók lesznek egy másik nyelvben is. Erről a formátumról az <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Apache Avro specifikációjában</a> találhat további információt.
Az Avro-fájlok formátuma támogatja az elosztott MapReduce programozási modellt. A fájlok „feloszthatók”, azaz bármely pontot kikereshet egy fájlban, és egy adott blokktól kezdheti a beolvasást. További információk: [Serialize data with the Microsoft .NET Library for Avro](hdinsight-dotnet-avro-serialization.md) (Adatszerializálás az Avro Microsoft .NET könyvtára segítségével).


### <a name="hdfs"></a>HDFS

A Hadoop elosztott fájlrendszer (HDFS) egy olyan elosztott fájlrendszer, amely a MapReduce és a YARN mellett a Hadoop-rendszer magját képezi. A HDFS a HDInsight Hadoop-fürtjeinek szabványos fájlrendszere.

### <a name="hive"></a>Hive és HCatalog

<a target="_blank" href="http://hive.apache.org/">Az Apache Hive</a> egy Hadoopra épülő adatraktár-szoftver, amely a HiveQL nevű SQL-szerű nyelv használatával teszi lehetővé az elosztott tárolókban található nagy méretű adatkészletek lekérdezését és kezelését. A Hive, akárcsak a Pig, egy MapReduce-ra épülő absztrakció. Futtatása során a Hive a lekérdezéseket MapReduce-feladatok sorozatává alakítja. A Hive elméleti szinten közelebb áll egy relációs adatbázis-kezelési rendszerhez, mint a Pig, ezért jobban használható strukturáltabb adatokhoz. Strukturálatlan adatok esetén a Pig a megfelelőbb választás. Lásd: [Use Hive with Hadoop in HDInsight](hdinsight-use-hive.md) (A Hive és a Hadoop együttes használata a HDInsightban).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Az Apache HCatalog</a> a Hadoop táblázat- és tárolókezelési rétege, amely relációs nézetben mutatja be az adatokat a felhasználóknak. A HCatalogban bármely olyan formátumban olvashat és írhat fájlokat, amelyhez Hive SerDe (szerializáló-deszerializáló) írható.

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Az Apache Mahout</a> a Hadoopon futó gépi tanulási algoritmusok méretezhető könyvtára. A gépi tanulást segítő alkalmazások statisztikai alapelvek segítségével tanítják meg a rendszereket arra, hogy tanuljanak az adatokból, és hogyan alapozzák a korábbi eredményekre a későbbi viselkedési formákat. Lásd: [Generate movie recommendations using Mahout on Hadoop](hdinsight-mahout.md) (Filmajánlók létrehozása a Hadoop-alapú Mahout segítségével).

### <a name="mapreduce"></a>MapReduce
A MapReduce a Hadoop örökölt szoftver-keretrendszere, amely big data készletek kötegelt párhuzamos feldolgozását biztosító alkalmazások írására szolgál. A MapReduce felosztja a nagy adatkészleteket, és kulcs-érték párokba rendezi az adatokat a feldolgozáshoz.

A [YARN](#yarn) a Hadoop következő generációs erőforrás-kezelője és alkalmazás-keretrendszere, amely MapReduce 2.0 néven is ismert. A MapReduce-feladatok a YARN rendszerén futnak.

A MapReduce rendszerről a Hadoop Wiki <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> című részében talál további információkat.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Az Apache Oozie</a> egy munkafolyamat-koordinációs rendszer, amely a Hadoop-feladatokat kezeli. Integrálva van a Hadoop-veremmel, és támogatja a Hadoop-feladatokat a MapReduce, a Pig, a Hive és a Sqoop számára. Ezen felül használható rendszerspecifikus feladatok, például Java programok vagy héjparancsfájlok ütemezésére. Lásd: [Use a time-based Oozie Coordinator with Hadoop](hdinsight-use-oozie-coordinator-time.md) (Időalapú Oozie-koordinátor használata a Hadooppal).

### <a name="phoenix"></a>Phoenix
<a  target="_blank" href="http://phoenix.apache.org/">Az Apache Phoenix</a> egy relációs adatbázisréteg, amely a HBase-re épül. A Phoenix egy olyan JDBC-illesztőt tartalmaz, amely lehetővé teszi a felhasználók számára az SQL-táblák közvetlen lekérdezését és kezelését. A Phoenix a MapReduce használata helyett natív NoSQL API-hívásokká alakítja a lekérdezéseket és egyéb kifejezéseket, ezáltal gyorsabb alkalmazásokat tesz lehetővé a NoSQL-tárolókon. Lásd: [Use Apache Phoenix and SQuirreL with HBase clusters](hdinsight-hbase-phoenix-squirrel.md) (Az Apache Phoenix és az SQuirreL használata HBase-fürtökkel).


### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Az Apache Pig</a> egy magas szintű platform, amely a Pig Latin nevezetű egyszerű parancsnyelv használata révén összetett MapReduce-átalakításokat tesz lehetővé nagy méretű adatkészleteken. A Pig lefordítja a Pig Latin parancsfájlokat, így azok a Hadoop rendszerben is futtathatók. A Pig Latin kiterjesztésére felhasználó által megadott funkciókat (UDF) is létrehozhat. Lásd: [Use Pig with Hadoop to analyze an Apache log file](hdinsight-use-pig.md) (Apache-naplófájlok elemzése a Pig és a Hadoop együttes használatával).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Az Apache Sqoop</a> egy olyan eszköz, amely a lehető leghatékonyabb módon biztosít tömeges adatátvitelt a Hadoop és a relációs adatbázisok (például egy SQL) vagy más strukturált adattárolók között. Lásd: [Use Sqoop with Hadoop](hdinsight-use-sqoop.md) (A Sqoop és a Hadoop együttes használata).

### <a name="tez"></a>Tez
<a  target="_blank" href="http://tez.apache.org/">Az Apache Tez</a> a Hadoop YARN rendszerre épülő alkalmazás-keretrendszer, amely végrehajtja az általános adatfeldolgozás összetett, aciklikus diagramjait. Rugalmasabb és hatékonyabb, mint elődje, a MapReduce keretrendszer, és lehetővé teszi az adatigényes folyamatok, például a Hive méretezhető, hatékony futtatását. Lásd a [Use Apache Tez for improved performance](hdinsight-use-hive.md#usetez) (Teljesítménynövelés az Apache Tez használatával) című szakaszt a Hive és a HiveQL használatával foglalkozó témakörben.

### <a name="yarn"></a>YARN
Az Apache YARN a MapReduce következő generációja (MapReduce 2.0 vagy MRv2), amely a MapReduce kötegelt feldolgozáson túl nagyobb méretezhetőséggel és valós idejű feldolgozással támogatja az adatfeldolgozási forgatókönyveket. A YARN erőforrás-kezelést és egy elosztott alkalmazás-keretrendszert biztosít. A MapReduce-feladatok a YARN rendszerén futnak.

A YARN rendszerről az <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a> (Következő generációs Apache Hadoop MapReduce (YARN)) témakörben talál további információkat.


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Az Apache ZooKeeper</a> adatregiszterek (znode-ok) közös hierarchikus névterével koordinálja a nagy méretű elosztott rendszerek folyamatait. A znode-ok kis mennyiségű metaadatot (állapot, hely, konfiguráció stb.) tartalmaznak, amelyek a folyamatok koordinálásához szükségesek.

## Programozási nyelvek a HDInsight rendszerében

A HDInsight-fürtök (Hadoop-, HBase-, Storm- és Spark-fürtök) számos programozási nyelvet támogatnak, de némelyik nincs alapértelmezés szerint telepítve. Az alapértelmezés szerint nem telepített könyvtárak, modulok vagy csomagok esetén az összetevő egy parancsfájlművelettel telepíthető. Lásd: [Script action development with HDInsight](hdinsight-hadoop-script-actions-linux.md) (Parancsfájlműveletek fejlesztése a HDInsighttal).

### Alapértelmezés szerint támogatott programozási nyelvek

Alapértelmezés szerint a HDInsight-fürtök a következőket támogatják:

* Java

* Python

Parancsfájlműveletekkel további nyelvek telepíthetők, lásd: [Script action development with HDInsight](hdinsight-hadoop-script-actions-linux.md) (Parancsfájlműveletek fejlesztése a HDInsighttal).

### JVM (Java virtuális gép) nyelvek

Egy Java virtuális gép (JVM) segítségével a Java nyelven kívül számos más nyelv is futtatható, azonban előfordulhat, hogy egyes nyelvek futtatásához további összetevőket is telepíteni kell a fürtön.

A HDInsight-fürtök az alábbi JVM-alapú nyelveket támogatják:

* Clojure

* Jython (Python a Javához)

* Scala

### Hadoop-specifikus nyelvek

A HDInsight-fürtök az alábbi, a Hadoop-rendszerre jellemző nyelveket támogatják:

* Pig Latin a Pig-feladatokhoz

* HiveQL a Hive-feladatokhoz és a SparkSQL-hez


## <a name="advantage"></a>A felhőalapú Hadoop előnyei

Az Azure felhőalapú ökoszisztémájának részeként a HDInsight Hadoop eszköze számos előnyt kínál, többek között a következőket:

* A Hadoop-fürtök automatikus kiépítése. A HDInsight-fürtök létrehozása sokkal egyszerűbb, mint a Hadoop-fürtök manuális konfigurálása. További részletek: [Provision Hadoop clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) (Hadoop-fürtök kiépítése a HDInsightban).

* A legmodernebb Hadoop-összetevők. További részletekért lásd: [What's new in the Hadoop cluster versions provided by HDInsight?][component-versioning] (A HDInsight által biztosított Hadoop-fürtverziók újdonságai).

* A fürtök magas fokú rendelkezésre állása és megbízhatósága.  A Hadoop-fürtök egy másodlagos átjárócsomóponttal bővültek, amelyet a HDInsight fejlesztett ki a szolgáltatás rendelkezésre állásának növelésére. A Hadoop-fürtök hagyományos implementációi jellemzően csak egy átjárócsomóponttal rendelkeznek. A HDInsight egy másodlagos átjárócsomópont hozzáadásával szüntette meg ezt a hibaérzékeny pontot. Egy új HA-fürtkonfigurációra való átváltás nem változtat a fürt árán, csak akkor, ha az ügyfelek egy alapértelmezett nagy méretű csomópont helyett egy extra nagy átjárócsomóponttal hoznak létre fürtöket.

    További részletek: [Availability and reliability of Hadoop clusters in HDInsight](hdinsight-high-availability-linux.md) (A Hadoop fürtök rendelkezésre állása és megbízhatósága a HDInsightban).

* Hatékony és gazdaságos adattárolás az Azure Blob Storage szolgáltatással, amely egy Hadoop-kompatbilis megoldás. További részletek: [Use Azure Blob storage with Hadoop in HDInsight](hdinsight-hadoop-use-blob-storage.md) (Az Azure Blob Storage és a Hadoop együttes használata a HDInsightban).

* Integráció más Azure-szolgáltatásokkal, beleértve a [Web Apps](../documentation/services/app-service/web/) és az [SQL Database](../documentation/services/sql-database/) szolgáltatásokat.

* További virtuálisgép-méretek. A HDInsight-fürtök különböző virtuálisgép-típusokon és -méreteken érhetők el. A HDInsight-fürtök mostantól képesek kihasználni az általános célra készült A2 és A7 közötti méreteket, az SSD-meghajtókkal és 60 százalékkal gyorsabb processzorokkal ellátott D sorozatú csomópontokat, valamint a hálózatkezelést InfiniBand-támogatással gyorsító A8 és A9 méreteket. Az Apache HBase on Azure HDInsight ügyfelei a D sorozat nagyobb memória-konfigurációival növelhetik a teljesítményt. Az Apache Storm on Azure HDInsight ügyfelei szintén részesülnek a nagyobb hivatkozási adatkészletek betöltésére szolgáló több memóriával, illetve a processzort gyorsító nagyobb átviteli sebességgel járó előnyökben.

* Fürtméretezés. A fürtméretezés lehetővé teszi, hogy módosítsa egy futtatott HDInsight-fürt csomópontjainak a számát, annak törlése vagy ismételt létrehozása nélkül.

* Virtual Network támogatása. A HDInsight-fürtök és az Azure Virtual Network együttes használatával támogatható azoknak a felhőben található erőforrásoknak vagy hibrid forgatókönyveknek az elkülönítése, amelyek a felhőalapú erőforrásokat kötik össze az adatközpontban találhatókkal.

* Alacsony kezdőköltség. Indítson el egy [ingyenes próbaverziót](/pricing/free-trial/), vagy tekintse át a [HDInsight díjszabásának részleteit](/pricing/details/hdinsight/).


A HDInsight Hadoop eszközének előnyeiről az [Azure-szolgáltatások HDInsight-ra vonatkozó lapján][marketing-page] olvashat.



## <a id="resources"></a>A big data elemzésről, a Hadoopról és a HDInsight-ról további információkkal szolgáló erőforrások

A felhőalapú Hadoopról és a big data elemzésekről az alábbi forrásanyagok szolgáltatnak további információkat.


### Hadoop-dokumentáció a HDInsight-hoz

* [HDInsight-dokumentáció](https://azure.microsoft.com/documentation/services/hdinsight/): az Azure HDInsight dokumentációs lapja, amely cikkekre, videókra és egyéb forrásanyagokra mutató hivatkozásokat tartalmaz.

* [A HDInsight használatának első lépései Linux rendszeren](hdinsight-hadoop-linux-tutorial-get-started.md): gyors üzembe helyezési útmutató HDInsight Hadoop-fürtök kiépítéséhez Linux rendszeren és Hive mintalekérdezések futtatásához.

* [A Linux-alapú Storm használatának első lépései a HDInsight-on](hdinsight-apache-storm-tutorial-get-started-linux.md): gyors üzembe helyezési útmutató egy HDInsight-fürtön alapuló Storm kiépítéséhez és Storm mintatopológiák futtatásához.

* [A HDInsight kiépítése Linux-rendszeren](hdinsight-hadoop-provision-linux-clusters.md): útmutató egy HDInsight Hadoop-fürt Linux rendszeren történő kiépítéséhez az Azure portálon, az Azure parancssori felületen vagy az Azure PowerShell szolgáltatáson keresztül.

* [A HDInsight használata Linux-rendszeren](hdinsight-hadoop-linux-information.md): gyors tippek az Azure-ban kiépített Hadoop Linux-fürtök használatához.

* [HDInsight-fürtök kezelése az Ambari segítségével](hdinsight-hadoop-manage-ambari.md): útmutató a HDInsight-ra épülő Linux-alapú Hadoop-fürt megfigyeléséhez és kezeléséhez az Ambari Web vagy az Ambari REST API segítségével.


### Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: további információk az Apache Hadoop szoftverkönyvtárról, amely keretrendszerként szolgál a számítógépek több fürtjére kiterjedő nagy adatkészletek elosztott feldolgozásához.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: további információk a Hadoop-alkalmazások által használt elsődleges tárolórendszer, azaz a Hadoop elosztott fájlrendszer felépítéséről és kialakításáról.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce oktatóanyag</a>: további információk a Hadoop-alkalmazások írására szolgáló programozási keretrendszerről, amely gyorsan és párhuzamosan dolgozza fel a számítási csomópontok nagy fürtjein található nagy mennyiségű adatot.

### SQL Database az Azure-on

* [Azure SQL Database](/documentation/services/sql-database/): az SQL Database adatbázisra vonatkozó dokumentáció, oktatóanyagok és videók.

* [SQL Database az Azure portálon](../sql-database/sql-database-manage-portal.md): egy kis méretű és könnyen használható adatbázis-kezelő eszköz az SQL Database felhőbeli kezeléséhez.

* [Adventure Works az SQL Database-hez](http://msftdbprodsamples.codeplex.com/releases/view/37304): Az SQL Database mintaadatbázisának letöltési oldala.

### Microsoft üzleti intelligencia (Windows rendszeren működő HDInsight-hoz)

Az ismerős üzletiintelligencia- (BI-) eszközök, mint például az Excel, a PowerPivot, az SQL Server Analysis Services és az SQL Server Reporting Services, a Power Query beépülő modul vagy a Microsoft Hive ODBC-illesztő segítségével kérik le, elemzik és jelentik a HDInsight rendszerébe integrált adatokat.

Ezek az üzletiintelligencia-eszközök segíthetnek a big data elemzések elvégzésében:

* [Az Excel és a Hadoop csatlakoztatása a Power Query segítségével](hdinsight-connect-excel-power-query.md): megtudhatja, hogyan csatlakoztathatja az Excelt a HDInsight-fürthöz kapcsolódó adatokat tároló Azure Storage-fiókhoz az Excelhez készült Microsoft Power Query segítségével.

* [Az Excel és a Hadoop csatlakoztatása a Microsoft Hive ODBC-illesztő segítségével](hdinsight-connect-excel-hive-ODBC-driver.md): megtudhatja, hogyan importálhat adatokat a HDInsight-ból a Microsoft Hive ODBC-illesztővel.

* [Microsoft Cloud platform](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): további információt szerezhet az Office 365-höz készült Power BI szolgáltatásról, letöltheti az SQL Server próbaverzióját, és beállíthatja a SharePoint Server 2013-at és az SQL Server BI-t.

* <a target="_blank" href="http://msdn.microsoft.com/library/hh231701.aspx">További információk az SQL Server Analysis Services szolgáltatásról</a>.

* <a target="_blank" href="http://msdn.microsoft.com/library/ms159106.aspx">Információk az SQL Server Reporting Services szolgáltatásról</a>.


### Hadoop-megoldások kipróbálása big data elemzésekhez (Windows rendszeren működő HDInsight-hoz)

A szervezet adatain végrehajtott big data elemzések hasznos, a vállalatára vonatkozó információkkal szolgálnak. Néhány példa:

* [HVAC-érzékelőadatok elemzése](hdinsight-hive-analyze-sensor-data.md): megtudhatja, hogyan elemezhet érzékelőadatokat a Hive és a HDInsight (Hadoop) együttes használatával, és hogyan jelenítheti meg az adatokat a Microsoft Excelben. Ez a példa a HVAC-rendszerek által előállított előzményadatokat a Hive segítségével dolgozza fel annak kimutatásához, hogy mely rendszerek nem képesek megbízhatóan fenntartani egy megadott hőmérsékletet.

* [Webhelyek naplóinak elemzése a Hive és a HDInsight együttes használatával](hdinsight-hive-analyze-website-log.md): megtudhatja, hogy a HDInsight HiveQL eszközével hogyan elemezhet webhelynaplókat, hogy képet kapjon a külső webhelyekről egy nap alatt érkező látogatások gyakoriságáról, valamint összegezni tudja a felhasználók által a webhelyen tapasztalt hibákat.

* [Érzékelőadatok valós idejű elemzése a HDInsightban (Hadoop) a Storm és a HBase segítségével](hdinsight-storm-sensor-data-analysis.md): megtudhatja, hogyan hozhat létre egy olyan megoldást, amely a HDInsightban egy Storm-fürt használatával dolgozza fel az Azure Event Hubs érzékelőadatait, majd a feldolgozott adatokat közel valós idejű információként jeleníti meg egy webalapú irányítópulton.


[marketing-page]: ../services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/



<!--HONumber=Jun16_HO2-->


