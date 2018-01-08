---
title: "Mi a HDInsight, a Hadoop- és a Spark-technológia? - Azure | Microsoft Docs"
description: "A HDInsight, a Hadoop- és Spark-technológiák, valamint az olyan összetevők, mint a big data-elemzéshez használt Kafka, Hive, Storm és HBase bemutatása."
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
ms.date: 12/13/2017
ms.author: cgronlun
ms.openlocfilehash: 49ec2f305c1aef50fcff977d60ff3ce0079ff5af
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="introduction-to-azure-hdinsight-the-hadoop-and-spark-technology-stack"></a>Az Azure HDInsight, a Hadoop- és Spark-technológiák bemutatása
Ez a cikk bemutatja az Azure HDInsight szolgáltatást, amely egy vállalatok számára készült, teljes mértékben felügyelt, teljes spektrumú, nyílt forráskódú elemezési szolgáltatás. Használhat olyan nyílt forráskódú keretrendszereket, mint a Hadoop, a Spark, a Hive, az LLAP, a Kafka, a Storm, az R stb. 

[!INCLUDE [hdinsight-price-change](../../../includes/hdinsight-enhancements.md)]

Az [Apache Hadoop](http://hadoop.apache.org/) volt a big data jellegű adatkészletek fürtökön végzett elosztott feldolgozásának, tárolásának és elemzésének eredeti nyílt forráskódú keretrendszere. A Hadoop-technológiák számos kapcsolódó szoftvert és segédprogramot tartalmaznak. Ilyen például az Apache Hive, a HBase, a Spark és a Kafka. A HDInsighton elérhető Hadoop-technológiák összetevőinek megtekintése: [A HDInsighttal elérhető összetevők és verziók][component-versioning]. További tudnivalók a HDInsightban használt Hadoopról [az Azure-szolgáltatások HDInsightra vonatkozó oldalán](https://azure.microsoft.com/services/hdinsight/) olvashatók.

Az [Apache Spark](http://spark.apache.org) egy nyílt forráskódú, párhuzamos feldolgozást végző keretrendszer, amely támogatja a memóriabeli feldolgozást a big data elemző alkalmazások teljesítményének növelése érdekében. További információ a HDInsight-alapú Sparkról: [Az Azure HDInsight-alapú Spark bemutatása](../spark/apache-spark-overview.md). 

<a href="https://ms.portal.azure.com/#create/Microsoft.HDInsightCluster" target="_blank"><img src="./media/apache-hadoop-introduction/deploy-to-azure.png" alt="Deploy an Azure HDInsight cluster"></a>

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Mi a HDInsight, és mik azok a Hadoop-technológiák? 
Az Azure HDInsight a [Hortonworks Data Platform (HDP)](https://hortonworks.com/products/data-center/hdp/) által fejlesztett Hadoop-összetevők felhőalapú terjesztett változata.  Az Azure HDInsight segítségével könnyen, gyorsan és költséghatékonyan dolgozható fel nagy mennyiségű adat. A nyílt forráskódú keretrendszerek használatával (pl. Hadoop, Spark, Hive, LLAP, Kafka, Storm, R stb.) számos különböző forgatókönyvet valósíthat meg (kinyerés, átalakítás, betöltés (ETL); adatraktározás; gépi tanulás; IoT).

## <a name="what-is-big-data"></a>Mik azok a big data típusú adatok?

A big data gyűjtése egyre nagyobb mennyiségben és sebességgel, többféle formátumban történik. Ezek lehetnek előzményadatok (azaz tárolt) vagy valós idejű adatok (vagyis a forrásból streamelt adatok). A big data leggyakoribb alkalmazási helyzetei: [A HDInsight használatára vonatkozó forgatókönyvek](#scenarios-for-using-hdinsight).

## <a name="why-should-i-use-hdinsight"></a>Miért érdemes használni a HDInsightot?

Ez a szakasz felsorolja az Azure HDInsight képességeit.


|Képesség  |Leírás  |
|---------|---------|
|Natív felhőalapú     |     Az Azure HDInsight segítségével optimalizált fürtöket hozhat létre az Azure-ban a következőkhöz: [Hadoop](apache-hadoop-linux-tutorial-get-started.md), [Spark](../spark/apache-spark-jupyter-spark-sql.md), [Interactive query (LLAP)](../interactive-query/apache-interactive-query-get-started.md), [Kafka](../kafka/apache-kafka-get-started.md), [Storm](../storm/apache-storm-tutorial-get-started-linux.md), [HBase](../hbase/apache-hbase-tutorial-get-started-linux.md) és  [R Server](../r-server/r-server-get-started.md). A HDInsight egy végpontok közötti SLA-t is biztosít az összes éles környezetben futó számítási feladathoz.  |
|Alacsony költségű és méretezhető     | A HDInsight segítségével a számítási feladatok vertikális [skálázását](../hdinsight-administer-use-portal-linux.md)  felfelé vagy lefelé is elvégezheti. Csökkentheti a költséget  [a fürtök igény szerinti létrehozása](../hdinsight-hadoop-create-linux-clusters-adf.md)  és a használatalapú fizetés révén. A feladatokat adatfolyamatok létrehozásával is működtetheti. A leválasztott számítás és tárolás jobb teljesítményt és rugalmasságot biztosít. |
|Biztonságos és megfelelő    | Az [Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md), a [titkosítás](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) és az [Azure Active Directory-val való integráció](../domain-joined/apache-domain-joined-introduction.md) segítségével a HDInsight lehetővé teszi a vállalati adategységek védelmét. A HDInsight továbbá megfelel a legnépszerűbb iparági és kormányzati [megfelelőségi szabványoknak](https://azure.microsoft.com/overview/trusted-cloud).        |
|Figyelés    | Az Azure HDInsight az [Azure Log Analytics](../hdinsight-hadoop-oms-log-analytics-tutorial.md) integrálásával egyetlen felületen teszi lehetővé az összes fürtjének monitorozását.        |
|Globális rendelkezésre állás | A HDInsight több  [régióban](https://azure.microsoft.com/regions/services/)  érhető el, mint bármelyik másik big data-elemzési ajánlat. Az Azure HDInsight elérhető az Azure Governmentben, Kínában, és Németországban is, így megfelelhet a vállalati igényeknek a főbb szuverén területeken. |  
|Termelékenység     |  Az Azure HDInsight számos hatékony eszközt biztosít a Hadoop és a Spark használatához az Ön által választott fejlesztési környezetben (pl.[Visual Studio](apache-hadoop-visual-studio-tools-get-started.md), az [Eclipse](../spark/apache-spark-eclipse-tool-plugin.md) vagy az [IntelliJ](../spark/apache-spark-intellij-tool-plugin.md)) Scala, Python, R, Java és .NET támogatásához. Az adatelemzők is együttműködhetnek olyan népszerű notebookok használatával, mint a [Jupyter](../spark/apache-spark-jupyter-notebook-kernels.md) vagy a [Zeppelin](../spark/apache-spark-zeppelin-notebook.md).    |
|Bővíthetőség     |  A HDInsight-fürtök bővítéséhez telepíthet összetevőket (Hue, Presto stb.) [szkriptműveletekkel](../hdinsight-hadoop-customize-cluster-linux.md), [felvehet élcsomópontokat](../hdinsight-apps-use-edge-node.md) vagy [integrálhat más big data minősítéssel rendelkező alkalmazásokkal](../hdinsight-apps-install-applications.md). A HDInsight [egykattintásos](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) üzembe helyezéssel biztosít zökkenőmentes integrációt a legnépszerűbb big data-megoldásokkal.|

## <a name="scenarios-for-using-hdinsight"></a>A HDInsight használatára vonatkozó forgatókönyvek

Az Azure HDInsight a Big Data koncepción alapuló adatfeldolgozás számos alkalmazási helyzetében használható. A big data gyűjtése egyre nagyobb mennyiségben és sebességgel, többféle formátumban történik. Ezek lehetnek előzményadatok (már összegyűjtött és tárolt adatok) vagy valós idejű adatok (közvetlenül a forrásból streamelt adatok). Az ilyen adatok feldolgozásának alkalmazási helyzeteit a következő kategóriákban lehet összegezni: 

### <a name="batch-processing-etl"></a>Kötegelt feldolgozás (ETL)

A kinyerési, átalakítási és betöltési (ETL) folyamat során a rendszer strukturálatlan és strukturált adatokat nyer ki heterogén adatforrásokból, strukturált formátumúvá alakítja azokat, majd betölti egy adattárba. Az átalakított adatok adatelemzéshez vagy adatraktározáshoz használhatók.

### <a name="internet-of-things-iot"></a>Eszközök internetes hálózata (IoT)

A HDInsight segítségével számos eszközről fogadott streamelési adatok dolgozhatók fel valós időben. További információkért [olvassa el ezt a blogbejegyzést](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

![HDInsight-architektúra: Eszközök internetes hálózata](./media/apache-hadoop-introduction/hdinsight-architecture-iot.png) 

### <a name="data-science"></a>Adatelemzés

A HDInsight segítségével olyan alkalmazásokat hozhat létre, amelyek kritikus fontosságú elemzéseket nyernek ki az adatokból. Az Azure Machine Learning segítségével ezen felül a vállalata várható trendjeiről is kaphat előrejelzéseket. További információkért [olvassa el ezt az ügyféltörténetet](https://customers.microsoft.com/story/pros).

![HDInsight-architektúra: Adatelemzés](./media/apache-hadoop-introduction/hdinsight-architecture-data-science.png)

### <a name="data-warehousing"></a>Adatraktározás

A HDInsight segítségével petabájtos nagyságrendű interaktív lekérdezéseket végezhet strukturált vagy strukturálatlan adatokon. Modelleket is létrehozhat BI-eszközökhöz való csatlakoztatással. További információkért [olvassa el ezt az ügyféltörténetet](https://customers.microsoft.com/story/milliman). 

![HDInsight-architektúra: Adatraktározás](./media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png)

### <a name="hybrid"></a>Hibrid

A HDInsight segítségével kiterjesztheti a meglévő helyszíni big data-infrastruktúráját az Azure-ba, hogy kihasználhassa a felhő fejlett elemzési képességeit.

![HDInsight-architektúra: Hibrid](./media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png)

## <a name="overview"></a>Fürttípusok a HDInsightban
A HDInsight adott fürttípusokat és fürttestreszabási képességeket is tartalmaz, például lehetővé teszi összetevők, segédprogramok és nyelvek hozzáadását.

### <a name="spark-kafka-interactive-query-hbase-customized-and-other-cluster-types"></a>Spark, Kafka, interaktív lekérdezés, HBase, egyéni és egyéb fürttípusok
A HDInsight a következő fürttípusokat kínálja:

* **[Apache Hadoop](https://wiki.apache.org/hadoop)**: A [HDFS](#hdfs)-t, [YARN](#yarn) erőforrás-kezelést, és egy egyszerű [MapReduce](#mapreduce) programozási modellt használ az adatok párhuzamos feldolgozásához és elemzéséhez.
* **[Apache Spark](http://spark.apache.org/)**: egy párhuzamos feldolgozást végző keretrendszer, amely támogatja a memórián belüli feldolgozást a big data elemző alkalmazások, az SQL Spark folyamatok, a streamelési adatok és a gépi tanulás teljesítményének növelése érdekében. Lásd: [Mi a HDInsight-alapú Apache Spark?](../spark/apache-spark-overview.md)
* **[Apache HBase](http://hbase.apache.org/)**: egy Hadoopra épülő NoSQL-adatbázis, amely közvetlen hozzáférést és nagymértékű következetességet biztosít a nagy mennyiségű strukturálatlan és félig strukturált adatok számára, akár egy több milliárd sorból és több millió oszlopból álló táblázat esetén is. Lásd: [Mi a HDInsight-alapú HBase?](../hbase/apache-hbase-overview.md)
* **[Microsoft R Server](https://msdn.microsoft.com/microsoft-r/rserver)**: párhuzamos, elosztott R-folyamatok üzemeltetésére és felügyeletére használt kiszolgáló. Skálázható, elosztott elemzési módszereket kínál az adatszakértők, statisztikusok és R-programozók számára a HDInsightban. Lásd: [A HDInsight-alapú R Server áttekintése](../r-server/r-server-overview.md).
* **[Apache Storm](https://storm.incubator.apache.org/)**: egy elosztott, valós idejű számítási rendszer a nagy méretű adatfolyamok gyors feldolgozására. A Storm a HDInsightban felügyelt fürtként érhető el. Lásd: [Analyze real-time sensor data using Storm and Hadoop](../storm/apache-storm-sensor-data-analysis.md) (Valós idejű érzékelőadatok elemzése a Storm és a Hadoop segítségével).
* **[Apache interaktív lekérdezés – előzetes verzió (avagy Hosszú és eredményes feldolgozást!)](https://cwiki.apache.org/confluence/display/Hive/LLAP)**: memóriában történő gyorsítótárazás az interaktív és gyorsabb Hive-lekérdezésekhez. Lásd: [Az interaktív lekérdezés használata a HDInsightban](../interactive-query/apache-interactive-query-get-started.md).
* **[Apache Kafka](https://kafka.apache.org/)**: nyílt forráskódú platform streamelt adatfolyamatok és alkalmazások létrehozásához. A Kafka egy Üzenetsor funkciót is biztosít, amelynek segítségével közzétehet adatstreameket vagy feliratkozhat rájuk. Lásd: [A HDInsight alatt futó Apache Kafka bemutatása](../kafka/apache-kafka-introduction.md).

## <a name="open-source-components-in-hdinsight"></a>Nyílt forráskódú összetevők a HDInsightban

Az Azure HDInsight lehetővé teszi fürtök létrehozását olyan nyílt forráskódú keretrendszerekkel, mint a Hadoop, a Spark, a Hive, az LLAP, a Kafka, a Storm, a HBase és az R. Ezekhez a fürtökhöz alapértelmezés szerint tartoznak más nyílt forráskódú összetevők is, például a következők: [Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md), [Avro](http://avro.apache.org/docs/current/spec.html), [Hive](http://hive.apache.org), [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog/), [Mahout](https://mahout.apache.org/), [MapReduce](http://wiki.apache.org/hadoop/MapReduce), [YARN](http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Phoenix](http://phoenix.apache.org/), [Pig](http://pig.apache.org/), [Sqoop](http://sqoop.apache.org/), [Tez](http://tez.apache.org/), [Oozie](http://oozie.apache.org/), [ZooKeeper](http://zookeeper.apache.org/).  


## <a name="programming-languages-on-hdinsight"></a>Programozási nyelvek a HDInsight rendszerében
A HDInsight-fürtök, mint például a Spark, a HBase, a Kafka, és a Hadoop, számos programozási nyelvet támogatnak, de ezek közül nem mindegyik van alapértelmezés szerint telepítve. Alapértelmezés szerint nem telepített könyvtárak, modulok vagy csomagok esetén [használjon szkriptműveletet az összetevő telepítéséhez](../hdinsight-hadoop-script-actions-linux.md). 

### <a name="default-programming-language-support"></a>Alapértelmezés szerint támogatott programozási nyelvek
Alapértelmezés szerint a HDInsight-fürtök a következőket támogatják:

* Java
* Python

[Szkriptműveletek](../hdinsight-hadoop-script-actions-linux.md) használatával további nyelvek is telepíthetők.

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

## <a name="business-intelligence-on-hdinsight"></a>Üzleti intelligencia a HDInsighton
A jól ismert üzletiintelligencia- (BI-) eszközök a Power Query beépülő modul vagy a Microsoft Hive ODBC-illesztő segítségével kérik le, elemzik és jelentik a HDInsight rendszerébe integrált adatokat:

* [Az Apache Spark BI adatvizualizációs eszközeinek használata az Azure HDInsighttal](../spark/apache-spark-use-bi-tools.md)
* [Hive-adatok vizualizálása a Microsoft Power BI segítségével az Azure HDInsightban](apache-hadoop-connect-hive-power-bi.md) 
* [Interaktív lekérdezéses Hive-adatok vizualizálása a Microsoft Power BI segítségével az Azure HDInsightban](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)
* [Az Excel és a Hadoop csatlakoztatása a Power Query segítségével](apache-hadoop-connect-excel-power-query.md): Megtudhatja, hogyan csatlakoztathatja az Excelt a HDInsight-fürtből származó adatokat tároló Azure Storage-fiókhoz az Excelhez készült Microsoft Power Queryvel. Használatához Windows-munkaállomás szükséges. 
* [Az Excel és a Hadoop csatlakoztatása a Microsoft Hive ODBC-illesztő segítségével](apache-hadoop-connect-excel-hive-odbc-driver.md): megtudhatja, hogyan importálhat adatokat a HDInsight-ból a Microsoft Hive ODBC-illesztővel. Használatához Windows-munkaállomás szükséges. 
* [Microsoft Cloud platform](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): további információt szerezhet az Office 365-höz készült Power BI szolgáltatásról, letöltheti az SQL Server próbaverzióját, és beállíthatja a SharePoint Server 2013-at és az SQL Server BI-t.
* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx)
* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx)


## <a name="next-steps"></a>További lépések

* [A Hadoop használatának első lépései a HDInsightban](apache-hadoop-linux-tutorial-get-started.md)
* [A Spark használatának első lépései a HDInsightban](../spark/apache-spark-jupyter-spark-sql.md)
* [A HDInsighton futó Kafka használatának első lépései](../kafka/apache-kafka-get-started.md)
* [A HDInsighton futó Storm használatának első lépései](../storm/apache-storm-tutorial-get-started-linux.md)
* [A HDInsighton futó HBase használatának első lépései](../hbase/apache-hbase-tutorial-get-started-linux.md)
* [A HDInsighton futó interaktív lekérdezések (LLAP) használatának első lépései](../interactive-query/apache-interactive-query-get-started.md)
* [A HDInsighton futó R Server használatának első lépései](../r-server/r-server-get-started.md)
* [HDInsight-fürtök kezelése](../hdinsight-administer-use-portal-linux.md)
* [HDInsight-fürtök védelme](../domain-joined/apache-domain-joined-introduction.md)
* [HDInsight-fürtök monitorozása](../hdinsight-hadoop-oms-log-analytics-tutorial.md)


[component-versioning]: ../hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/