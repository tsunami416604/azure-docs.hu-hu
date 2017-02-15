---
title: "Mi a Hadoop? Bevezetés az Azure HDInsightba | Microsoft Docs"
description: "A HDInsight-alapú Hadoop-ökoszisztéma és -összetevők ismertetése. A HDInsight többek között a Hadoop, a Spark, a HBase és további big data-feldolgozó és -elemző alkalmazásokat foglal magában."
keywords: "big data elemzés, bevezetés a Hadoopba, mi az a Hadoop?, Hadoop-technológiák, Hadoop-ökoszisztéma"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/14/2016
ms.author: cgronlun
translationtype: Human Translation
ms.sourcegitcommit: 10d684bd6c9408f2fbd48a0a8804b011f098d71c
ms.openlocfilehash: 8f38b9eaf8bcb498a7983756cbf57f3e372d7fe0


---
# <a name="an-introduction-to-the-hadoop-ecosystem-on-azure-hdinsight"></a>Bevezetés a Hadoop-ökoszisztéma használatába az Azure HDInsightban
 Ebben a cikkben megismerkedhet az Azure HDInsight-alapú Hadoop-keretrendszerrel és annak ökoszisztémájával, valamint a big data fogalmával. Ismerje meg a Hadoop összetevőit, szakkifejezéseit és a big data jellegű adatok elemzésének menetét.

## <a name="what-is-hadoop-on-hdinsight"></a>Mi a HDInsight-alapú Hadoop-keretrendszer?
A Hadoop nyílt forráskódú szoftverek ökoszisztémája, amely keretrendszerként szolgál a big data jellegű adatkészletek számítógépfürtökön végzett elosztott feldolgozásához, tárolásához és elemzéséhez. Az Azure HDInsight a **Hortonworks Data Platform (HDP)** felhőben elérhető kiadásával készíti a Hadoop-komponenseket, magas megbízhatósággal és rendelkezésre állással épít ki felügyelt fürtöket, valamint vállalati szintű biztonságot és irányítást biztosít az Active Directory segítségével.  

A big data koncepción alapuló adatfeldolgozás eredeti nyílt forráskódú projektje az Apache Hadoop volt. Ezt követte a Hadoop technológiák közé sorolt kapcsolódó szoftverek és segédprogramok kifejlesztése, amilyen az Apache Hive, az Apache HBase, az Apache Spark, az Apache Kafka és sok más alkalmazás. A részleteket lásd [A Hadoop ökoszisztémájának áttekintése a HDInsightban](#overview) című szakaszban.

## <a name="what-is-big-data"></a>Mik azok a big data típusú adatok?
A big data kifejezés bármilyen nagyobb digitális információhalmazra utalhat egy Twitter-hírcsatorna szövegeitől vagy egy ipari berendezés érzékelőitől származó információktól kezdve egy webhelyen végzett vásárlói keresésekig és vásárlásokig. A big data típusú adatok lehetnek előzményadatok (azaz tárolt adatok) vagy valós idejű adatok (azaz közvetlenül a forrásból streamelt adatok). A big data az adatok egyre növekvő kötetekben, egyre nagyobb sebességgel történő gyűjtésére utal, és a formátumok egyre bővülő választéka jellemzi.

Ahhoz, hogy a big data modell használható tudásbázist és betekintést nyújtson, a megfelelő adatokat kell kigyűjtenie és a megfelelő kérdéseket kell feltennie. Gondoskodnia kell az adatok elérhetőségéről, tisztításáról, elemzéséről majd a használható módon történő megjelenítéséről. Ebben segíthet a HDInsight rendszerében a Hadoop big data elemzése.

## <a name="a-nameoverviewaoverview-of-the-hadoop-ecosystem-in-hdinsight"></a><a name="overview"></a>A Hadoop ökoszisztémájának áttekintése a HDInsightban
A HDInsight a big data típusú adatok elemzésére szolgáló, gyorsan fejlődő Apache Hadoop technológiák felhőalapú terjesztése a Microsoft Azure felületén. Tartalmazza többek között az Apache Spark, a HBase, a Kafka, a Storm, a Pig, a Hive, az Interaktív Hive, a Sqoop, az Oozie és az Ambari implementációit. A HDInsight emellett olyan üzletiintelligencia- (BI-) eszközökkel is integrálható, mint a Power BI, az Excel, az SQL Server Analysis Services és az SQL Server Reporting Services.

### <a name="hadoop-hbase-spark-kafka-interactive-hive-storm-customized-and-other-clusters"></a>Hadoop, HBase, Spark, Kafka, Interaktív Hive, Storm, egyéni és egyéb fürtök.
A HDInsight a következő fürttípusokat kínálja:

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**: megbízható adattárolást biztosít a [HDFS](#hdfs) és egy egyszerű [MapReduce](#mapreduce) programozási modell segítségével az adatok párhuzamos feldolgozásához és elemzéséhez.
* **[Apache Spark](http://spark.apache.org/)**: egy párhuzamos feldolgozást végző keretrendszer, amely támogatja a memórián belüli feldolgozást a big data elemző alkalmazások, az SQL Spark folyamatok, a streamelési adatok és a gépi tanulás teljesítményének növelése érdekében. Lásd: [Overview: What is Apache Spark in HDInsight?](hdinsight-apache-spark-overview.md) (Áttekintés: Mi az Apache Spark a HDInsightban?)
* **[Apache HBase](http://hbase.apache.org/)**: egy Hadoopra épülő NoSQL-adatbázis, amely közvetlen hozzáférést és nagymértékű következetességet biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatok számára, akár egy több milliárd sorból és több millió oszlopból álló táblázat esetén is. Lásd: [Overview of HBase on HDInsight](hdinsight-hbase-overview.md) (A HDInsight-alapú HBase áttekintése).
* **[Microsoft R Server](https://msdn.microsoft.com/en-us/microsoft-r/rserver)**: egy vállalati szintű kiszolgáló párhuzamos, elosztott R-folyamatok futtatásához. Skálázható, elosztott elemzési módszereket kínál az adatszakértők, statisztikusok és R-programozók számára a HDInsightban. Lásd: [A HDInsight-alapú R Server áttekintése](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-r-server-overview).
* **[Apache Storm](https://storm.incubator.apache.org/)**: egy elosztott, valós idejű számítási rendszer a nagy méretű adatfolyamok gyors feldolgozására. A Storm a HDInsightban felügyelt fürtként érhető el. Lásd: [Analyze real-time sensor data using Storm and Hadoop](hdinsight-storm-sensor-data-analysis.md) (Valós idejű érzékelőadatok elemzése a Storm és a Hadoop segítségével).
* **[Apache Interaktív Hive – előzetes verzió (avagy Hosszú és eredményes feldolgozást!)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: memóriában történő gyorsítótárazás az interaktív és gyorsabb Hive-lekérdezésekhez. Lásd: [Az Interactive Hive használata a HDInsightban](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-use-interactive-hive).
* **[Apache Kafka – előzetes verzió](https://kafka.apache.org/)**: egy nyílt forráskódú platform, streamadatfolyamatok és -alkalmazások létrehozásához. A Kafka egy Üzenetsor funkciót is biztosít, amelynek segítségével közzétehet adatstreameket vagy feliratkozhat rájuk. Lásd: [A HDInsight alatt futó Apache Kafka bemutatása](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-kafka-introduction).
* **[Tartományhoz csatlakozó fürtök – előzetes verzió](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-domain-joined-introduction)**: Egy Active Directory-tartományhoz csatlakozó fürt, amely hozzáférés-kezelést és irányítást biztosít az adatokhoz.
* **[Egyéni fürtök parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md)**: Olyan fürtök, amelyek az üzembe helyezés során parancsfájlok futtatásával telepítenek további összetevőket.

### <a name="example-customization-scripts"></a>Példák testreszabási parancsfájlokra
A parancsfájlműveletek olyan, Linux rendszeren futó Bash-parancsfájlok, amelyek a fürtök kiépítése során futnak, és segítségükkel további összetevők telepíthetők a fürtön.

A HDInsight csapat az alábbi parancsfájlpéldákat biztosítja:

* [Hue](hdinsight-hadoop-hue-linux.md): a fürtökkel folytatott kommunikációhoz használható webalkalmazás-készlet. Csak Linux fürtökön használható.
* [Giraph](hdinsight-hadoop-giraph-install-linux.md): diagramfeldolgozás dolgok vagy személyek közötti kapcsolatok modellezésére.
* [R](hdinsight-hadoop-r-scripts-linux.md): egy nyílt forráskódú nyelv és környezet, amely statisztikai számításokhoz használható a gépi tanulási folyamatok során.
* [Solr](hdinsight-hadoop-solr-install-linux.md): egy vállalati szintű keresési platform, ami teljes szöveges keresést tesz lehetővé az adatokon.

Az egyéni parancsfájlművelet-fejlesztéssel kapcsolatos további információkért lásd: [Script Action development with HDInsight](hdinsight-hadoop-script-actions-linux.md) (Parancsfájlműveletek fejlesztése a HDInsighttal).

## <a name="what-are-the-hadoop-components-and-utilities"></a>Melyek a Hadoop összetevői és segédprogramjai?
A HDInsight-fürtök az alábbi összetevőket és segédprogramokat tartalmazzák.

* **[Ambari](#ambari)**: fürtkiépítés, kezelés, megfigyelés és segédprogramok.
* **[Avro](#avro)** (Microsoft .NET könyvtár az Avro-hoz): adatszerializálás a Microsoft .NET-környezethez.
* **[Hive és HCatalog](#hive)**: SQL-szerű (Structured Query Language) lekérdezés, valamint egy táblázat- és tárolókezelési réteg.
* **[Mahout](#mahout)**: Méretezhető Machine Learning alkalmazásokhoz.
* **[MapReduce](#mapreduce)**: örökölt keretrendszer a Hadoop által elosztott feldolgozáshoz és erőforrás-kezeléshez. Lásd még: [YARN](#yarn), a következő generációs erőforrás-keretrendszer.
* **[Oozie](#oozie)**: munkafolyamat-kezelés.
* **[Phoenix](#phoenix)**: HBase-en alapuló relációs adatbázisréteg.
* **[Pig](#pig)**: egyszerűbb parancsfájlkezelés MapReduce-átalakításokhoz.
* **[Sqoop](#sqoop)**: adatok importálása és exportálása.
* **[Tez](#tez)**: lehetővé teszi, hogy az adatigényes folyamatok hatékonyan és méretezhetően fussanak.
* **[YARN](#yarn)**: a Hadoop alap függvénytárának része és a MapReduce szoftverkeretrendszer következő generációja.
* **[ZooKeeper](#zookeeper)**: elosztott rendszerek folyamatait koordinálja.

> [!NOTE]
> További információ az egyes összetevőkről és verziókról: [A Hadoop összetevői, verziószámozása és szolgáltatásajánlatai a HDInsightban][component-versioning]
>
>

### <a name="a-nameambariaambari"></a><a name="ambari"></a>Ambari
Az Apache Ambari az Apache Hadoop-fürtök kiépítésére, kezelésére és megfigyelésére szolgál. Operátori eszközök intuitív gyűjteményét és egy robusztus API-készletet foglal magában, amelyek elfedik a Hadoop összetettségét, és leegyszerűsítik a fürtök működését. A Linux-alapú HDInsight-fürtök az Ambari webes felhasználói felületet és az Ambari REST API-t, a Windows-alapú fürtök pedig a REST API egy adott részét biztosítják. Az Ambari Views on HDInsight fürtök beépülő felhasználói felületi képességeket kínálnak.

Lásd: [Manage HDInsight clusters using Ambari](hdinsight-hadoop-manage-ambari.md) (HDInsight-fürtök kezelése az Ambari segítségével) (csak Linux esetén), [Monitor Hadoop clusters in HDInsight using the Ambari API](hdinsight-monitor-use-ambari-api.md) (Hadoop-fürtök megfigyelése a HDInsightban az Ambari API segítségével) és <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">referenciák az Apache Ambari API-hoz</a>.

### <a name="a-nameavroaavro-microsoft-net-library-for-avro"></a><a name="avro"></a>Avro (Microsoft .NET könyvtár az Avro-hoz)
Az Avro Microsoft .NET könyvtára Apache Avro kompakt bináris adatcsere-formátumot alkalmaz a Microsoft .NET környezetben végrehajtott szerializációkhoz. A <a target="_blank" href="http://www.json.org/">JavaScript Object Notation (JSON)</a> segítségével meghatároz egy nyelvtől független sémát, amely lehetővé teszi a nyelvi együttműködést, azaz az egyik nyelvben szerializált adatok olvashatók lesznek egy másik nyelvben is. Erről a formátumról az <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Apache Avro specifikációjában</a> találhat további információt.
Az Avro-fájlok formátuma támogatja az elosztott MapReduce programozási modellt. A fájlok „feloszthatók”, azaz bármely pontot kikereshet egy fájlban, és egy adott blokktól kezdheti a beolvasást. További információk: [Serialize data with the Microsoft .NET Library for Avro](hdinsight-dotnet-avro-serialization.md) (Adatszerializálás az Avro Microsoft .NET könyvtára segítségével).

### <a name="a-namehdfsahdfs"></a><a name="hdfs"></a>HDFS
A Hadoop elosztott fájlrendszer (HDFS) egy olyan elosztott fájlrendszer, amely a MapReduce és a YARN mellett a Hadoop-rendszer magját képezi. A HDFS a HDInsight Hadoop-fürtjeinek szabványos fájlrendszere.

### <a name="a-namehiveahive--hcatalog"></a><a name="hive"></a>Hive és HCatalog
Az <a target="_blank" href="http://hive.apache.org/">Apache Hive</a> egy Hadoopra épülő adattárház-szoftver, amely a HiveQL nevű SQL-szerű nyelv használatával teszi lehetővé az elosztott tárolókban található nagy méretű adatkészletek lekérdezését és kezelését. A Hive, akárcsak a Pig, egy MapReduce-ra épülő absztrakció. Futtatása során a Hive a lekérdezéseket MapReduce-feladatok sorozatává alakítja. A Hive elméleti szinten közelebb áll egy relációs adatbázis-kezelési rendszerhez, mint a Pig, ezért jobban használható strukturáltabb adatokhoz. Strukturálatlan adatok esetén a Pig a megfelelőbb választás. Lásd: [Use Hive with Hadoop in HDInsight](hdinsight-use-hive.md) (A Hive és a Hadoop együttes használata a HDInsightban).

Az <a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> a Hadoop táblázat- és tárolókezelési rétege, amely relációs nézetben mutatja be az adatokat. A HCatalogban bármely olyan formátumban olvashat és írhat fájlokat, amelyhez Hive SerDe (szerializáló-deszerializáló) írható.

### <a name="a-namemahoutamahout"></a><a name="mahout"></a>Mahout
Az <a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> a Hadoopon futó Machine Learning algoritmusok méretezhető könyvtára. A gépi tanulást segítő alkalmazások statisztikai alapelvek segítségével tanítják meg a rendszereket arra, hogy tanuljanak az adatokból, és hogyan alapozzák a korábbi eredményekre a későbbi viselkedési formákat. Lásd: [Generate movie recommendations using Mahout on Hadoop](hdinsight-mahout.md) (Filmajánlók létrehozása a Hadoop-alapú Mahout segítségével).

### <a name="a-namemapreduceamapreduce"></a><a name="mapreduce"></a>MapReduce
A MapReduce a Hadoop örökölt szoftver-keretrendszere, amely big data készletek kötegelt párhuzamos feldolgozását biztosító alkalmazások írására szolgál. A MapReduce felosztja a nagy adatkészleteket, és kulcs-érték párokba rendezi az adatokat a feldolgozáshoz.

A [YARN](#yarn) a Hadoop következő generációs erőforrás-kezelője és alkalmazás-keretrendszere, amely MapReduce 2.0 néven is ismert. A MapReduce-feladatok a YARN rendszerén futnak.

A MapReduce rendszerről a Hadoop Wiki <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> című részében talál további információkat.

### <a name="a-nameoozieaoozie"></a><a name="oozie"></a>Oozie
Az <a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> egy munkafolyamat-koordinációs rendszer, amely a Hadoop-feladatokat kezeli. Integrálva van a Hadoop-veremmel, és támogatja a Hadoop-feladatokat a MapReduce, a Pig, a Hive és a Sqoop számára. Ezen felül használható rendszerspecifikus feladatok, például Java programok vagy héjparancsfájlok ütemezésére. Lásd: [Use Oozie with Hadoop](hdinsight-use-oozie.md) (Az Oozie és a Hadoop együttes használata).

### <a name="a-namephoenixaphoenix"></a><a name="phoenix"></a>Phoenix
Az <a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> egy relációs adatbázisréteg, amely a HBase-re épül. A Phoenix egy olyan JDBC-illesztőt tartalmaz, amely lehetővé teszi az SQL-táblák közvetlen lekérdezését és kezelését. A Phoenix a MapReduce használata helyett natív NoSQL API-hívásokká alakítja a lekérdezéseket és egyéb kifejezéseket, ezáltal gyorsabb alkalmazásokat tesz lehetővé a NoSQL-tárolókon. Lásd: [Use Apache Phoenix and SQuirreL with HBase clusters](hdinsight-hbase-phoenix-squirrel.md) (Az Apache Phoenix és az SQuirreL használata HBase-fürtökkel).

### <a name="a-namepigapig"></a><a name="pig"></a>Pig
Az <a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> egy magas szintű platform, amely a Pig Latin nevezetű egyszerű parancsnyelv használata révén összetett MapReduce-átalakításokat tesz lehetővé nagy méretű adatkészleteken. A Pig lefordítja a Pig Latin parancsfájlokat, így azok a Hadoop rendszerben is futtathatók. A Pig Latin kiterjesztésére felhasználó által megadott funkciókat (UDF) is létrehozhat. Lásd: [Use Pig with Hadoop](hdinsight-use-pig.md) (A Pig és a Hadoop együttes használata).

### <a name="a-namesqoopasqoop"></a><a name="sqoop"></a>Sqoop
Az <a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> egy olyan eszköz, amely a lehető leghatékonyabb módon biztosít tömeges adatátvitelt a Hadoop és relációs (például SQL) adatbázisok vagy más strukturált adattárolók között. Lásd: [Use Sqoop with Hadoop](hdinsight-use-sqoop.md) (A Sqoop és a Hadoop együttes használata).

### <a name="a-nametezatez"></a><a name="tez"></a>Tez
Az <a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> a Hadoop YARN rendszerre épülő alkalmazás-keretrendszer, amely végrehajtja az általános adatfeldolgozás összetett, aciklikus gráfjait. Rugalmasabb és hatékonyabb, mint elődje, a MapReduce keretrendszer, és lehetővé teszi az adatigényes folyamatok, például a Hive méretezhető, hatékony futtatását. Lásd a [Use Apache Tez for improved performance](hdinsight-use-hive.md#usetez) (Teljesítménynövelés az Apache Tez használatával) című szakaszt a Hive és a HiveQL használatával foglalkozó témakörben.

### <a name="a-nameyarnayarn"></a><a name="yarn"></a>YARN
Az Apache YARN a MapReduce következő generációja (MapReduce 2.0 vagy MRv2), amely a MapReduce kötegelt feldolgozáson túl nagyobb méretezhetőséggel és valós idejű feldolgozással támogatja az adatfeldolgozási forgatókönyveket. A YARN erőforrás-kezelést és egy elosztott alkalmazás-keretrendszert biztosít. A MapReduce-feladatok a YARN rendszerén futnak.

A YARN rendszerről az <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a> (Következő generációs Apache Hadoop MapReduce (YARN)) témakörben talál további információkat.

### <a name="a-namezookeeperazookeeper"></a><a name="zookeeper"></a>ZooKeeper
Az <a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> adatregiszterek (znode-ok) közös hierarchikus névterével koordinálja a nagy méretű elosztott rendszerek folyamatait. A znode-ok kis mennyiségű metaadatot (állapot, hely, konfiguráció stb.) tartalmaznak, amelyek a folyamatok koordinálásához szükségesek.

## <a name="programming-languages-on-hdinsight"></a>Programozási nyelvek a HDInsight rendszerében
A HDInsight-fürtök (Hadoop-, HBase-, Storm- és Spark-fürtök) számos programozási nyelvet támogatnak, de némelyik nincs alapértelmezés szerint telepítve. Az alapértelmezés szerint nem telepített könyvtárak, modulok vagy csomagok esetén az összetevő egy parancsfájlművelettel telepíthető. Lásd: [Script action development with HDInsight](hdinsight-hadoop-script-actions-linux.md) (Parancsfájlműveletek fejlesztése a HDInsighttal).

### <a name="default-programming-language-support"></a>Alapértelmezés szerint támogatott programozási nyelvek
Alapértelmezés szerint a HDInsight-fürtök a következőket támogatják:

* Java
* Python

Parancsfájlműveletekkel további nyelvek telepíthetők, lásd: [Script action development with HDInsight](hdinsight-hadoop-script-actions-linux.md) (Parancsfájlműveletek fejlesztése a HDInsighttal).

### <a name="java-virtual-machine-jvm-languages"></a>JVM (Java virtuális gép) nyelvek
Egy Java virtuális gép (JVM) segítségével a Java nyelven kívül számos más nyelv is futtatható, azonban előfordulhat, hogy egyes nyelvek futtatásához további összetevőket is telepíteni kell a fürtön.

A HDInsight-fürtök az alábbi JVM-alapú nyelveket támogatják:

* Clojure
* Jython (Python a Javához)
* Scala

### <a name="hadoop-specific-languages"></a>Hadoop-specifikus nyelvek
A HDInsight-fürtök az alábbi, a Hadoop-rendszerre jellemző nyelveket támogatják:

* Pig Latin a Pig-feladatokhoz
* HiveQL a Hive-feladatokhoz és a SparkSQL-hez

## <a name="a-nameadvantageaadvantages-of-hadoop-in-the-cloud"></a><a name="advantage"></a>A felhőalapú Hadoop előnyei
Az Azure felhőalapú ökoszisztémájának részeként a HDInsight Hadoop eszköze számos előnyt kínál, többek között a következőket:

* A Hadoop-fürtök automatikus kiépítése. A HDInsight-fürtök létrehozása sokkal egyszerűbb, mint a Hadoop-fürtök manuális konfigurálása. További részletek: [Provision Hadoop clusters in HDInsight](hdinsight-hadoop-provision-linux-clusters.md) (Hadoop-fürtök kiépítése a HDInsightban).
* A legmodernebb Hadoop-összetevők. További információ: [A Hadoop összetevői, verziószámozása és szolgáltatásajánlatai a HDInsightban][component-versioning].
* A fürtök magas fokú rendelkezésre állása és megbízhatósága. További részletek: [Availability and reliability of Hadoop clusters in HDInsight](hdinsight-high-availability-linux.md) (A Hadoop fürtök rendelkezésre állása és megbízhatósága a HDInsightban).
* Hatékony és gazdaságos adattárolás az Azure Blob Storage vagy Azure Data Lake Store szolgáltatásokkal, amelyek mindegyike Hadoop-kompatbilis megoldás. További részletek: [Az Azure Blob Storage és a Hadoop együttes használata a HDInsightban](hdinsight-hadoop-use-blob-storage.md) vagy [Use Data Lake Store with an HDInsight cluster](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal) (A Data Lake Store használata egy HDInsight-fürtön).
* Integráció más Azure-szolgáltatásokkal, beleértve a [Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/) és az [SQL Database](https://azure.microsoft.com/documentation/services/sql-database/) szolgáltatásokat.
* A HDInsight-fürtök futtatásához szükséges további virtuálisgép-méretek és -típusok. További információ: [A Hadoop összetevői, verziószámozása és szolgáltatásajánlatai a HDInsightban][component-versioning].
* Fürtméretezés. A fürtméretezés lehetővé teszi, hogy módosítsa egy futtatott HDInsight-fürt csomópontjainak a számát, annak törlése vagy ismételt létrehozása nélkül.
* Virtual Network támogatása. A HDInsight-fürtök és az Azure Virtual Network együttes használatával támogatható azoknak a felhőben található erőforrásoknak vagy hibrid forgatókönyveknek az elkülönítése, amelyek a felhőalapú erőforrásokat kötik össze az adatközpontban találhatókkal.
* Alacsony kezdőköltség. Indítson el egy [ingyenes próbaverziót](https://azure.microsoft.com/free/), vagy tekintse át a [HDInsight díjszabásának részleteit](https://azure.microsoft.com/pricing/details/hdinsight/).

A HDInsightban használt Hadoop előnyeiről az [Azure-szolgáltatások HDInsightra vonatkozó lapján][marketing-page] olvashat.

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard és HDInsight Prémium
A HDInsight a big data felhőajánlatokat kétféle – Standard és Prémium – kategóriában biztosítja. A HDInsight Standard egy vállalati szintű fürtöt biztosít, amelyet a vállalkozások a big data számítási feladatokat futtatásához használhatnak. A HDInsight Prémium ezen felül fejlett elemzési és biztonsági képességeket biztosít a HDInsight-fürtök számára. További információért lásd: [Azure HDInsight Prémium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a name="a-idresourcesaresources-for-learning-more-about-big-data-analysis-hadoop-and-hdinsight"></a><a id="resources"></a>A big data elemzésről, a Hadoopról és a HDInsight-ról további információkkal szolgáló források
A felhőalapú Hadoopról és a big data elemzésekről az alábbi forrásanyagok szolgáltatnak további információkat.

### <a name="hadoop-documentation-for-hdinsight"></a>Hadoop-dokumentáció a HDInsight-hoz
* [HDInsight-dokumentáció](https://azure.microsoft.com/documentation/services/hdinsight/): a Hadoop on Azure HDInsight dokumentációs lapja, amely cikkekre, videókra és egyéb forrásanyagokra mutató hivatkozásokat tartalmaz.
* [A Hadoop használatának első lépései a HDInsightban](hdinsight-hadoop-linux-tutorial-get-started.md): gyors üzembe helyezési útmutató HDInsight Hadoop-fürtök kiépítéséhez és Hive-mintalekérdezések futtatásához.
* [A Spark használatának első lépései a HDInsightban](hdinsight-apache-spark-jupyter-spark-sql.md): gyors üzembe helyezési útmutató Spark-fürt létrehozásához és interaktív Spark SQL-lekérdezések futtatásához.
* [R Server használata a HDInsighton](hdinsight-hadoop-r-server-get-started.md): Az R Server használatának megkezdése a HDInsight Premiumban.
* [HDInsight-fürtök kiépítése](hdinsight-hadoop-provision-linux-clusters.md): útmutató HDInsight Hadoop-fürt kiépítéséhez az Azure Portalon, az Azure parancssori felületen vagy az Azure PowerShellen keresztül.

### <a name="apache-hadoop"></a>Apache Hadoop
* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: további információk az Apache Hadoop szoftverkönyvtárról, amely keretrendszerként szolgál a big data jellegű adatkészletek számítógépfürtökön végzett elosztott feldolgozásához.
* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: további információk a Hadoop-alkalmazások által használt elsődleges tárolórendszer, azaz a Hadoop elosztott fájlrendszer felépítéséről és kialakításáról.
* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce oktatóanyag</a>: további információk a Hadoop-alkalmazások írására szolgáló programozási keretrendszerről, amely gyorsan és párhuzamosan dolgozza fel a számítási csomópontok nagy fürtjein található nagy mennyiségű adatot.

### <a name="microsoft-business-intelligence"></a>Microsoft üzleti intelligencia
Az ismerős üzletiintelligencia- (BI-) eszközök, mint például az Excel, a PowerPivot, az SQL Server Analysis Services és az SQL Server Reporting Services, a Power Query beépülő modul vagy a Microsoft Hive ODBC-illesztő segítségével kérik le, elemzik és jelentik a HDInsight rendszerébe integrált adatokat.

Ezek az üzletiintelligencia-eszközök segíthetnek a big data elemzések elvégzésében:

* [Az Excel és a Hadoop csatlakoztatása a Power Query segítségével](hdinsight-connect-excel-power-query.md): megtudhatja, hogyan csatlakoztathatja az Excelt a HDInsight-fürthöz kapcsolódó adatokat tároló Azure Storage-fiókhoz az Excelhez készült Microsoft Power Query segítségével. Használatához Windows-munkaállomás szükséges. Linux vagy Windows rendszerű fürtökön működik.
* [Az Excel és a Hadoop csatlakoztatása a Microsoft Hive ODBC-illesztő segítségével](hdinsight-connect-excel-hive-odbc-driver.md): megtudhatja, hogyan importálhat adatokat a HDInsight-ból a Microsoft Hive ODBC-illesztővel. Használatához Windows-munkaállomás szükséges. Linux vagy Windows rendszerű fürtökön működik.
* [Microsoft Cloud platform](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): további információt szerezhet az Office 365-höz készült Power BI szolgáltatásról, letöltheti az SQL Server próbaverzióját, és beállíthatja a SharePoint Server 2013-at és az SQL Server BI-t.
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx).
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx).

[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/



<!--HONumber=Dec16_HO2-->


