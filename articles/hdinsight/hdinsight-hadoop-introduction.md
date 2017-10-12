---
title: "Mi a HDInsight, a Hadoop technológia és a fürt? - Azure | Microsoft Docs"
description: "A HDInsight és a Hadoop-technológiák bemutatása, olyan összetevők ismertetésével, mint a big data-elemzéshez használt Spark, Kafka, Hive és HBase szolgáltatás."
keywords: "azure hadoop, hadoop azure, hadoop bevezetés, hadoop bemutatása, hadoop-technológiák, bevezetés a hadoop szolgáltatásba, a hadoop bemutatása, mi az a hadoop-fürt, mi a hadoop-fürt, mire való a hadoop"
services: hdinsight
documentationcenter: 
author: cjgronlund
manager: jhubbard
editor: cgronlun
ms.assetid: e56a396a-1b39-43e0-b543-f2dee5b8dd3a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: cgronlun
ms.openlocfilehash: e5ed09ddb1556e6c76813e71bcb31cf4f792b616
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-hdinsight-the-hadoop-technology-stack-and-hadoop-clusters"></a>Az Azure HDInsight, a Hadoop-technológiák és a Hadoop-fürtök bemutatása
 Ez a cikk bemutatja az Azure HDInsight szolgáltatást, amely a Hadoop-technológiák felhőalapú terjesztett változata. Itt olvashat arról is, hogy mi a Hadoop-fürt, és mikor lehet használni. 

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Mi a HDInsight, és mik azok a Hadoop-technológiák? 
Az Azure HDInsight a [Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/) által fejlesztett Hadoop-összetevők felhőalapú terjesztett változata. Az [Apache Hadoop](http://hadoop.apache.org/) volt a big data jellegű adatkészletek számítógépfürtökön végzett elosztott feldolgozásának, tárolásának és elemzésének eredeti nyílt forráskódú keretrendszere. 


A Hadoop-technológiák számos kapcsolódó szoftvert és segédprogramot tartalmaznak. Ilyen például az Apache Hive, a HBase, a Spark és a Kafka. A HDInsighton elérhető Hadoop-technológiák összetevőinek megtekintése: [A HDInsighttal elérhető összetevők és verziók][component-versioning]. További tudnivalók a HDInsightban használt Hadoopról [az Azure-szolgáltatások HDInsightra vonatkozó oldalán](https://azure.microsoft.com/services/hdinsight/) olvashatók.

## <a name="what-is-a-hadoop-cluster-and-when-do-you-use-it"></a>Mi a Hadoop-fürt, és mikor lehet használni?
A *Hadoop* egy fürttípus is, amely a következőkkel rendelkezik:

* YARN a feladatütemezéshez és az erőforrás-kezeléshez
* MapReduce a párhuzamos feldolgozáshoz
* Hadoop elosztott fájlrendszer (HDFS)
  
A Hadoop-fürtöket leggyakrabban a tárolt adatok kötegelt feldolgozására használják. A HDInsightban található egyéb típusú fürtök további képességekkel rendelkeznek: A Spark a gyorsabb, memórián belüli feldolgozás miatt egyre népszerűbb. További részletek: [Fürttípusok a HDInsightban](#overview).

## <a name="what-is-big-data"></a>Mik azok a big data típusú adatok?
A big data kifejezés bármilyen nagyobb digitális információhalmazra alkalmazható, mint például:

* Ipari berendezés érzékelőadatai
* Webhelyről gyűjtött, felhasználói tevékenységre vonatkozó információ
* Twitter-hírcsatorna

A big data gyűjtése egyre nagyobb mennyiségben és sebességgel, többféle formátumban történik. Ezek lehetnek előzményadatok (azaz tárolt) vagy valós idejű adatok (vagyis a forrásból streamelt adatok). 

## <a name="overview"></a>Fürttípusok a HDInsightban
A HDInsight adott fürttípusokat és fürttestreszabási képességeket is tartalmaz, például lehetővé teszi összetevők, segédprogramok és nyelvek hozzáadását.

### <a name="spark-kafka-interactive-query-hbase-customized-and-other-cluster-types"></a>Spark, Kafka, interaktív lekérdezés, HBase, egyéni és egyéb fürttípusok
A HDInsight a következő fürttípusokat kínálja:

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**: A [HDFS](#hdfs)-t, [YARN](#yarn) erőforrás-kezelést, és egy egyszerű [MapReduce](#mapreduce) programozási modellt használ az adatok párhuzamos feldolgozásához és elemzéséhez.
* **[Apache Spark](http://spark.apache.org/)**: egy párhuzamos feldolgozást végző keretrendszer, amely támogatja a memórián belüli feldolgozást a big data elemző alkalmazások, az SQL Spark folyamatok, a streamelési adatok és a gépi tanulás teljesítményének növelése érdekében. Lásd: [Mi a HDInsight-alapú Apache Spark?](hdinsight-apache-spark-overview.md)
* **[Apache HBase](http://hbase.apache.org/)**: egy Hadoopra épülő NoSQL-adatbázis, amely közvetlen hozzáférést és nagymértékű következetességet biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatok számára, akár egy több milliárd sorból és több millió oszlopból álló táblázat esetén is. Lásd: [Mi a HDInsight-alapú HBase?](hdinsight-hbase-overview.md)
* **[Microsoft R Server](https://msdn.microsoft.com/microsoft-r/rserver)**: párhuzamos, elosztott R-folyamatok üzemeltetésére és felügyeletére használt kiszolgáló. Skálázható, elosztott elemzési módszereket kínál az adatszakértők, statisztikusok és R-programozók számára a HDInsightban. Lásd: [A HDInsight-alapú R Server áttekintése](hdinsight-hadoop-r-server-overview.md).
* **[Apache Storm](https://storm.incubator.apache.org/)**: egy elosztott, valós idejű számítási rendszer a nagy méretű adatfolyamok gyors feldolgozására. A Storm a HDInsightban felügyelt fürtként érhető el. Lásd: [Analyze real-time sensor data using Storm and Hadoop](hdinsight-storm-sensor-data-analysis.md) (Valós idejű érzékelőadatok elemzése a Storm és a Hadoop segítségével).
* **[Apache interaktív lekérdezés – előzetes verzió (avagy Hosszú és eredményes feldolgozást!)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: memóriában történő gyorsítótárazás az interaktív és gyorsabb Hive-lekérdezésekhez. Lásd: [Az interaktív lekérdezés használata a HDInsightban](hdinsight-hadoop-use-interactive-hive.md).
* **[Apache Kafka](https://kafka.apache.org/)**: nyílt forráskódú platform streamelt adatfolyamatok és alkalmazások létrehozásához. A Kafka egy Üzenetsor funkciót is biztosít, amelynek segítségével közzétehet adatstreameket vagy feliratkozhat rájuk. Lásd: [A HDInsight alatt futó Apache Kafka bemutatása](hdinsight-apache-kafka-introduction.md).

A következő módszerekkel is konfigurálhat fürtöket:
* **[Tartományhoz csatlakozó fürtök – előzetes verzió](hdinsight-domain-joined-introduction.md)**: Egy Active Directory-tartományhoz csatlakozó fürt, amely hozzáférés-kezelést és irányítást biztosít az adatokhoz.
* **[Egyéni fürtök parancsfájlműveletekkel](hdinsight-hadoop-customize-cluster-linux.md)**: Olyan fürtök, amelyek az üzembe helyezés során parancsfájlok futtatásával telepítenek további összetevőket.

### <a name="example-cluster-customization-scripts"></a>Szkriptpéldák a fürtök testreszabásához
A szkriptműveletek olyan, Linux rendszeren futó Bash-szkriptek, amelyek a fürtök kiépítése során futnak, és segítségükkel további összetevők telepíthetők a fürtön. 

A HDInsight csapat az alábbi parancsfájlpéldákat biztosítja:

* **[Hue](hdinsight-hadoop-hue-linux.md)**: a fürtökkel folytatott kommunikációhoz használható webalkalmazás-készlet. Csak Linux fürtökön használható.
* **[Giraph](hdinsight-hadoop-giraph-install-linux.md)**: diagramfeldolgozási rendszer dolgok vagy személyek közötti kapcsolatok modellezésére.
* **[Solr](hdinsight-hadoop-solr-install-linux.md)**: vállalati szintű keresési platform, amely teljes szöveges keresést tesz lehetővé az adatokon.

Az egyéni parancsfájlművelet-fejlesztéssel kapcsolatos további információkért lásd: [Script Action development with HDInsight](hdinsight-hadoop-script-actions-linux.md) (Parancsfájlműveletek fejlesztése a HDInsighttal).

## <a name="components-and-utilities-on-hdinsight-clusters"></a>A HDInsight-fürtök összetevői és segédprogramjai
A HDInsight-fürtök a következő összetevőket és segédprogramokat tartalmazzák:

* **[Ambari](#ambari)**: fürtkiépítés, kezelés, megfigyelés és segédprogramok.
* **[Avro](#avro)** (Microsoft .NET könyvtár az Avro-hoz): adatszerializálás a Microsoft .NET-környezethez. 
* **[Hive és HCatalog](#hive)**: SQL-szerű lekérdezés, valamint egy táblázat- és tárolókezelési réteg.
* **[Mahout](#mahout)**: Méretezhető Machine Learning alkalmazásokhoz.
* **[MapReduce](#mapreduce)**: örökölt keretrendszer a Hadoop által elosztott feldolgozáshoz és erőforrás-kezeléshez. Lásd: [YARN](#yarn).
* **[Oozie](#oozie)**: munkafolyamat-kezelés.
* **[Phoenix](#phoenix)**: HBase-en alapuló relációs adatbázisréteg.
* **[Pig](#pig)**: egyszerűbb parancsfájlkezelés MapReduce-átalakításokhoz.
* **[Sqoop](#sqoop)**: adatok importálása és exportálása.
* **[Tez](#tez)**: lehetővé teszi, hogy az adatigényes folyamatok hatékonyan és méretezhetően fussanak.
* **[YARN](#yarn)**: a Hadoop-alapfüggvénytárhoz tartozó erőforrás-kezelési architektúra.
* **[ZooKeeper](#zookeeper)**: elosztott rendszerek folyamatait koordinálja.

> [!NOTE]
> További tudnivalók az egyes összetevőkről és verziókról: [A Hadoop összetevői és verziói a HDInsightban][component-versioning]
>
>

### <a name="ambari"></a>Ambari
Az Apache Ambari az Apache Hadoop-fürtök kiépítésére, kezelésére és figyelésére szolgál. Operátori eszközök intuitív gyűjteményét és egy robusztus API-készletet foglal magában, amelyek elfedik a Hadoop összetettségét, és leegyszerűsítik a fürtök működését. A Linux-alapú HDInsight-fürtök az Ambari webes felhasználói felületet és az Ambari REST API-t egyaránt biztosítják. Az Ambari Views on HDInsight fürtök beépülő felhasználói felületi képességeket kínálnak.
Lásd: [HDInsight-fürtök kezelése az Ambari használatával](hdinsight-hadoop-manage-ambari.md) és <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">referencia az Apache Ambari API-hoz</a>.

### <a name="avro"></a>Avro (Microsoft .NET könyvtár az Avro-hoz)
Az Avro Microsoft .NET könyvtára Apache Avro kompakt bináris adatcsere-formátumot alkalmaz a Microsoft .NET környezetben végrehajtott szerializációkhoz. Nyelvfüggetlen sémát határoz meg annak érdekében, hogy az egyik nyelven szerializált adatok egy másik nyelven is olvashatók legyenek. Erről a formátumról az <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Apache Avro specifikációjában</a> találhat további információt. Az Avro-fájlok formátuma támogatja az elosztott MapReduce programozási modellt: a fájlok „feloszthatók”, azaz bármely pontot kikereshet egy fájlban, és egy adott blokktól kezdheti a beolvasást. További információk: [Serialize data with the Microsoft .NET Library for Avro](hdinsight-dotnet-avro-serialization.md) (Adatszerializálás az Avro Microsoft .NET könyvtára segítségével). Hamarosan a Linux-alapú fürtök támogatása is elérhető lesz.

### <a name="hdfs"></a>HDFS
A Hadoop elosztott fájlrendszer (HDFS) az a fájlrendszer, amely a YARN és a MapReduce mellett a Hadoop-technológia központi eleme. Ez a HDInsight Hadoop-fürtjeinek szabványos fájlrendszere. Lásd: [Adatok lekérdezése HDFS-kompatibilis tárhelyről](hdinsight-hadoop-use-blob-storage.md).

### <a name="hive"></a>Hive és HCatalog
Az <a target="_blank" href="http://hive.apache.org/">Apache Hive</a> egy Hadoopra épülő adattárház-szoftver, amely a HiveQL nevű SQL-szerű nyelv használatával teszi lehetővé az elosztott tárolókban található nagy méretű adatkészletek lekérdezését és kezelését. A Hive a Pighez hasonlóan egy MapReduce-ra épülő absztrakció, amely a lekérdezéseket MapReduce-feladatok sorozatára fordítja le. A Hive a Pignél közelebb áll egy relációsadatbázis-kezelési rendszerhez, és strukturáltabb adatokhoz használják. Strukturálatlan adatok esetén a Pig a megfelelőbb választás. Lásd: [Use Hive with Hadoop in HDInsight](hdinsight-use-hive.md) (A Hive és a Hadoop együttes használata a HDInsightban).

Az <a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> a Hadoop táblázat- és tárolókezelési rétege, amely relációs nézetben mutatja be az adatokat. A HCatalogban bármely olyan formátumban olvashat és írhat fájlokat, amely használható a Hive SerDe-vel (szerializáló-deszerializáló).

### <a name="mahout"></a>Mahout
Az <a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> a Hadoopon futó gépi tanulási algoritmusok könyvtára. A gépi tanulást segítő alkalmazások statisztikai alapelvek segítségével tanítják meg a rendszereket arra, hogy tanuljanak az adatokból, és hogyan alapozzák a korábbi eredményekre a későbbi viselkedési formákat. Lásd: [Generate movie recommendations using Mahout on Hadoop](hdinsight-mahout.md) (Filmajánlók létrehozása a Hadoop-alapú Mahout segítségével).

### <a name="mapreduce"></a>MapReduce
A MapReduce a Hadoop örökölt szoftver-keretrendszere, amely big data készletek kötegelt párhuzamos feldolgozását biztosító alkalmazások írására szolgál. A MapReduce felosztja a nagy adatkészleteket, és kulcs-érték párokba rendezi az adatokat a feldolgozáshoz. A MapReduce-feladatok a [YARN](#yarn) rendszerén futnak. Lásd a Hadoop Wiki <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> szócikkét.

### <a name="oozie"></a>Oozie
Az <a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> egy munkafolyamat-koordinációs rendszer, amely a Hadoop-feladatokat kezeli. Integrálva van a Hadoop-veremmel, és támogatja a Hadoop-feladatokat a MapReduce, a Pig, a Hive és a Sqoop számára. Ezen felül használható rendszerspecifikus feladatok, például Java programok vagy héjparancsfájlok ütemezésére. Lásd: [Use Oozie with Hadoop](hdinsight-use-oozie-linux-mac.md) (Az Oozie és a Hadoop együttes használata).

### <a name="phoenix"></a>Phoenix
Az <a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> egy relációs adatbázisréteg, amely a HBase-re épül. A Phoenix egy olyan JDBC-illesztőt tartalmaz, amely lehetővé teszi az SQL-táblák közvetlen lekérdezését és kezelését. A Phoenix a MapReduce használata helyett natív NoSQL API-hívásokká alakítja a lekérdezéseket és egyéb kifejezéseket, ezáltal gyorsabb alkalmazásokat tesz lehetővé a NoSQL-tárolókon. Lásd: [Use Apache Phoenix and SQuirreL with HBase clusters](hdinsight-hbase-phoenix-squirrel.md) (Az Apache Phoenix és az SQuirreL használata HBase-fürtökkel).

### <a name="pig"></a>Pig
Az <a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> magas szintű platform, amely a Pig Latin nevű egyszerű szkriptnyelv használatával összetett MapReduce-átalakításokat tesz lehetővé nagy méretű adatkészleteken. A Pig lefordítja a Pig Latin parancsfájlokat, így azok a Hadoop rendszerben is futtathatók. A Pig Latin kiterjesztésére felhasználó által megadott funkciókat (UDF) is létrehozhat. Lásd: [Use Pig with Hadoop](hdinsight-use-pig.md) (A Pig és a Hadoop együttes használata).

### <a name="sqoop"></a>Sqoop
Az <a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> olyan eszköz, amely a lehető leghatékonyabban biztosít tömeges adatátvitelt a Hadoop és a relációs adatbázisok (például SQL) vagy más strukturált adattárolók között. Lásd: [Use Sqoop with Hadoop](hdinsight-use-sqoop.md) (A Sqoop és a Hadoop együttes használata).

### <a name="tez"></a>Tez
Az <a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> a Hadoop YARN rendszerre épülő alkalmazás-keretrendszer, amely végrehajtja az általános adatfeldolgozás összetett, aciklikus gráfjait. Rugalmasabb és hatékonyabb, mint elődje, a MapReduce keretrendszer, és lehetővé teszi az adatigényes folyamatok, például a Hive méretezhető, hatékony futtatását. Lásd a [Use Apache Tez for improved performance](hdinsight-use-hive.md#usetez) (Teljesítménynövelés az Apache Tez használatával) című szakaszt a Hive és a HiveQL használatával foglalkozó témakörben.

### <a name="yarn"></a>YARN
Az Apache YARN a MapReduce következő generációja (MapReduce 2.0 vagy MRv2), amely a MapReduce kötegelt feldolgozáson túl nagyobb méretezhetőséggel és valós idejű feldolgozással támogatja az adatfeldolgozási forgatókönyveket. A YARN erőforrás-kezelést és egy elosztott alkalmazás-keretrendszert biztosít. A MapReduce-feladatok a YARN rendszerén futnak. Lásd: <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="zookeeper"></a>ZooKeeper
Az <a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> adatregiszterek (znode-ok) közös hierarchikus névterével koordinálja a nagy méretű elosztott rendszerek folyamatait. A znode-ok kis mennyiségű metaadatot (állapot, hely, konfiguráció stb.) tartalmaznak, amelyek a folyamatok koordinálásához szükségesek. Tekintsen meg egy példát [a ZooKeeper, egy HBase-fürt és az Apache Phoenix](hdinsight-hbase-phoenix-squirrel-linux.md) használatára. 

## <a name="programming-languages-on-hdinsight"></a>Programozási nyelvek a HDInsight rendszerében
A HDInsight-fürtök, mint például a Spark, a HBase, a Kafka, és a Hadoop, számos programozási nyelvet támogatnak, de ezek közül nem mindegyik van alapértelmezés szerint telepítve. Alapértelmezés szerint nem telepített könyvtárak, modulok vagy csomagok esetén [használjon szkriptműveletet az összetevő telepítéséhez](hdinsight-hadoop-script-actions-linux.md). 

### <a name="default-programming-language-support"></a>Alapértelmezés szerint támogatott programozási nyelvek
Alapértelmezés szerint a HDInsight-fürtök a következőket támogatják:

* Java
* Python

[Szkriptműveletek](hdinsight-hadoop-script-actions-linux.md) használatával további nyelvek is telepíthetők.

### <a name="java-virtual-machine-jvm-languages"></a>JVM (Java virtuális gép) nyelvek
A Java-alapú virtuális gépeken (JVM) a Javán kívül számos más nyelv is futtatható, azonban előfordulhat, hogy egyes nyelvek futtatásához további összetevőket is telepítenie kell a fürtön.

A HDInsight-fürtök az alábbi JVM-alapú nyelveket támogatják:

* Clojure
* Jython (Python a Javához)
* Scala

### <a name="hadoop-specific-languages"></a>Hadoop-specifikus nyelvek
A HDInsight-fürtök az alábbi, a Hadoop-technológiákra jellemző nyelveket támogatják:

* Pig Latin a Pig-feladatokhoz
* HiveQL a Hive-feladatokhoz és a SparkSQL-hez

## <a name="hdinsight-standard-and-hdinsight-premium"></a>HDInsight Standard és HDInsight Prémium
A HDInsight a big data felhőajánlatokat kétféle – Standard és Prémium – kategóriában biztosítja. A HDInsight Standard egy vállalati szintű fürtöt biztosít, amelyet a vállalkozások a big data számítási feladatokat futtatásához használhatnak. A Standard képességekre épülő HDInsight Prémium magasabb szintű elemzési és biztonsági képességeket biztosít a HDInsight-fürt számára. További információért lásd: [Azure HDInsight Prémium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)

## <a name="microsoft-business-intelligence-and-hdinsight"></a>A Microsoft üzleti intelligencia és a HDInsight
A jól ismert üzletiintelligencia- (BI-) eszközök a Power Query beépülő modul vagy a Microsoft Hive ODBC-illesztő segítségével kérik le, elemzik és jelentik a HDInsight rendszerébe integrált adatokat:

* [Az Excel és a Hadoop csatlakoztatása a Power Query segítségével](hdinsight-connect-excel-power-query.md): Megtudhatja, hogyan csatlakoztathatja az Excelt a HDInsight-fürtből származó adatokat tároló Azure Storage-fiókhoz az Excelhez készült Microsoft Power Queryvel. Használatához Windows-munkaállomás szükséges. 
* [Az Excel és a Hadoop csatlakoztatása a Microsoft Hive ODBC-illesztő segítségével](hdinsight-connect-excel-hive-odbc-driver.md): megtudhatja, hogyan importálhat adatokat a HDInsight-ból a Microsoft Hive ODBC-illesztővel. Használatához Windows-munkaállomás szükséges. 
* [Microsoft Cloud platform](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): további információt szerezhet az Office 365-höz készült Power BI szolgáltatásról, letöltheti az SQL Server próbaverzióját, és beállíthatja a SharePoint Server 2013-at és az SQL Server BI-t.
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>Következő lépések

* [A Hadoop használatának első lépései a HDInsightban](hdinsight-hadoop-linux-tutorial-get-started.md): gyors üzembe helyezési útmutató HDInsight Hadoop-fürtök kiépítéséhez és Hive-mintalekérdezések futtatásához.
* [A Spark használatának első lépései a HDInsightban](hdinsight-apache-spark-jupyter-spark-sql.md): gyors üzembe helyezési útmutató Spark-fürt létrehozásához és interaktív Spark SQL-lekérdezések futtatásához.
* [R Server használata a HDInsighton](hdinsight-hadoop-r-server-get-started.md): Az R Server használatának megkezdése a HDInsight Premiumban.
* [HDInsight-fürtök kiépítése](hdinsight-hadoop-provision-linux-clusters.md): útmutató HDInsight Hadoop-fürt kiépítéséhez az Azure Portalon, az Azure parancssori felületen vagy az Azure PowerShellen keresztül.


[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/