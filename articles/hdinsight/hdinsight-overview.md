---
title: Mi az Azure HDInsight
description: Bevezetés a HDInsight, valamint az Apache Hadoop és az Apache Spark technológiai verem és összetevők, beleértve a Kafka, Hive, Storm és HBase a big data-elemzés.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 31e8506067133304144614ff58974ee21e9680be
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241219"
---
# <a name="what-is-azure-hdinsight"></a>Mi az az Azure HDInsight?

Az Azure HDInsight egy felügyelt, teljes spektrumú, nyílt forráskódú elemzési szolgáltatás a felhőben a vállalatok számára. Használhat nyílt forráskódú keretrendszereket, például a Hadoop, az Apache Spark, az Apache Hive, az LLAP, az Apache Kafka, az Apache Storm, az R és más.

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Mi a HDInsight, és mik azok a Hadoop-technológiák?

Az Azure HDInsight a Hadoop-összetevők felhőalapú elosztása. Az Azure HDInsight segítségével könnyen, gyorsan és költséghatékonyan dolgozható fel nagy mennyiségű adat. Használhat olyan népszerű, nyílt forráskódú keretrendszereket, mint a Hadoop, a Spark, a Hive, az LLAP, a Kafka, a Storm, az R stb. Ezekkel a keretrendszerekkel a forgatókönyvek széles körét alkalmazhatja, köztük kinyerési, átalakítási és betöltési (ETL) feladatokat, adatraktározást, gépi tanulást és az IoT-t.

A HDInsighton elérhető Hadoop-technológiák összetevőinek megtekintése: [A HDInsighttal elérhető összetevők és verziók](./hdinsight-component-versioning.md). További tudnivalók a HDInsightban használt Hadoopról [az Azure-szolgáltatások HDInsightra vonatkozó oldalán](https://azure.microsoft.com/services/hdinsight/) olvashatók.

## <a name="what-is-big-data"></a>Mik azok a big data típusú adatok?

A big data gyűjtése egyre nagyobb mennyiségben és sebességgel, minden korábbinál többféle formátumban történik. Ezek lehetnek előzményadatok (azaz tárolt) vagy valós idejű adatok (vagyis a forrásból streamelt adatok). A big data leggyakoribb alkalmazási helyzetei: [A HDInsight használatára vonatkozó forgatókönyvek](#scenarios-for-using-hdinsight).

## <a name="why-should-i-use-azure-hdinsight"></a>Miért érdemes használni az Azure HDInsight?

Ez a szakasz felsorolja az Azure HDInsight képességeit.

|Képesség  |Leírás  |
|---------|---------|
|Natív felhőalapú     |     Az Azure HDInsight segítségével optimalizált fürtöket hozhat létre az Azure-ban a következőkhöz: [Hadoop](./hadoop/apache-hadoop-linux-tutorial-get-started.md), [Spark](./spark/apache-spark-jupyter-spark-sql.md), [Interaktív lekérdezés (LLAP)](./interactive-query/apache-interactive-query-get-started.md), [Kafka](./kafka/apache-kafka-get-started.md), [Storm](./storm/apache-storm-tutorial-get-started-linux.md), [HBase](./hbase/apache-hbase-tutorial-get-started-linux.md) és  [ML-szolgáltatások](./r-server/r-server-overview.md). A HDInsight egy végpontok közötti SLA-t is biztosít az összes éles környezetben futó számítási feladathoz.  |
|Alacsony költségű és méretezhető     | A HDInsight lehetővé teszi a számítási feladatok fel- vagy [leskálázását.](./hdinsight-administer-use-portal-linux.md#scale-clusters) Csökkentheti a költségeket, ha  [igény szerinti fürtöket hoz létre,](./hdinsight-hadoop-create-linux-clusters-adf.md)és csak azért fizet, amit használ. A feladatokat adatfolyamatok létrehozásával is működtetheti. A leválasztott számítás és tárolás jobb teljesítményt és rugalmasságot biztosít. |
|Biztonságos és megfelelő    | Az [Azure Virtual Network](./hdinsight-plan-virtual-network-deployment.md), a [titkosítás](./hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) és az [Azure Active Directoryval való integráció](./domain-joined/hdinsight-security-overview.md) segítségével a HDInsight lehetővé teszi a vállalati adategységek védelmét. A HDInsight továbbá megfelel a legnépszerűbb iparági és kormányzati [megfelelőségi szabványoknak](https://azure.microsoft.com/overview/trusted-cloud).        |
|Figyelés    | Az Azure HDInsight integrálható [az Azure Monitor naplóival,](./hdinsight-hadoop-oms-log-analytics-tutorial.md) hogy egyetlen felületet biztosítson, amellyel az összes fürtje figyelheti.        |
|Globális rendelkezésre állás | A HDInsight több régióban érhető [el,](https://azure.microsoft.com/regions/services/) mint bármely más big data-elemzési ajánlat. Az Azure HDInsight elérhető az Azure Governmentben, Kínában, és Németországban is, így megfelelhet a vállalati igényeknek a főbb szuverén területeken. |  
|Termelékenység     |  Az Azure HDInsight számos hatékony eszközt biztosít a Hadoop és a Spark használatához a választott fejlesztési környezetben. Ez a fejlesztési környezet lehet a [Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md), a [VSCode](./hdinsight-for-vscode.md), az [Eclipse](./spark/apache-spark-eclipse-tool-plugin.md) vagy az [IntelliJ](./spark/apache-spark-intellij-tool-plugin.md) a Scala, a Python, az R, a Java és a .NET támogatásához. Az adatelemzők is együttműködhetnek olyan népszerű notebookok használatával, mint a [Jupyter](./spark/apache-spark-jupyter-notebook-kernels.md) vagy a [Zeppelin](./spark/apache-spark-zeppelin-notebook.md).    |
|Bővíthetőség     |  A HDInsight-fürtöket bővítheti telepített összetevőkkel (Hue, Presto stb.) [szkriptműveletek](./hdinsight-hadoop-customize-cluster-linux.md) használatával, [élcsomópontok felvételével](./hdinsight-apps-use-edge-node.md) vagy [más big data minősítéssel rendelkező alkalmazások integrálásával](./hdinsight-apps-install-applications.md). A HDInsight [egykattintásos](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) üzembe helyezéssel biztosít zökkenőmentes integrációt a legnépszerűbb big data-megoldásokkal.|

## <a name="scenarios-for-using-hdinsight"></a>A HDInsight használatára vonatkozó forgatókönyvek

Az Azure HDInsight számos, a big data koncepción alapuló adatfeldolgozási forgatókönyvben alkalmazható. Ezek lehetnek előzményadatok (már összegyűjtött és tárolt adatok) vagy valós idejű adatok (közvetlenül a forrásból streamelt adatok). Az ilyen adatok feldolgozásának forgatókönyveit a következő kategóriákban lehet összegezni:

### <a name="batch-processing-etl"></a>Kötegelt feldolgozás (ETL)

A kinyerési, átalakítási és betöltési (ETL) folyamat során a rendszer strukturálatlan és strukturált adatokat nyer ki heterogén adatforrásokból. Ezután strukturált formátumúvá alakítja azokat, majd betölti egy adattárba. Az átalakított adatok adatelemzéshez vagy adatraktározáshoz használhatók.

### <a name="data-warehousing"></a>Adatraktározás

A HDInsight segítségével petabájtos nagyságrendű interaktív lekérdezéseket végezhet strukturált vagy strukturálatlan adatokon. Modelleket is létrehozhat BI-eszközökhöz való csatlakoztatással. További információkért [olvassa el ezt az ügyféltörténetet](https://customers.microsoft.com/story/milliman).

![HDInsight-architektúra: Adatraktározás](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png "HDInsight Adattárház architektúra")

### <a name="internet-of-things-iot"></a>Eszközök internetes hálózata (IoT)

A HDInsight segítségével valós időben, különböző eszközökről fogadott streamelési adatokat dolgozhat fel. További információkért [olvassa el ezt az Azure-blogbejegyzést, amely az Azure Managed Disksben HDInsight alatt futó Apache Kafka nyilvános előzetes verzióját jelenti be](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/).

![HDInsight-architektúra: Eszközök internetes hálózata](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-iot.png "HDInsight IoT architektúra")

### <a name="data-science"></a>Adatelemzés

A HDInsight segítségével olyan alkalmazásokat hozhat létre, amelyek kritikus fontosságú elemzéseket nyernek ki az adatokból. Az Azure Machine Learning segítségével ezen felül a vállalata várható trendjeiről is kaphat előrejelzéseket. További információkért [olvassa el ezt az ügyféltörténetet](https://customers.microsoft.com/story/pros).

![HDInsight-architektúra: Adatelemzés](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-science.png "HDInsight adatelemzési architektúra")

### <a name="hybrid"></a>Hibrid

A HDInsight segítségével kiterjesztheti a meglévő helyszíni big data-infrastruktúráját az Azure-ba, hogy kihasználhassa a felhő fejlett elemzési képességeit.

![HDInsight-architektúra: Hibrid](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png "HDInsight hibrid architektúra")

## <a name="cluster-types-in-hdinsight"></a>Fürttípusok a HDInsightban

A HDInsight adott fürttípusokat és fürttestreszabási képességeket is tartalmaz, például lehetővé teszi összetevők, segédprogramok és nyelvek hozzáadását. A HDInsight a következő fürttípusokat kínálja:

|Fürt típusa | Leírás |
|---|---|
|[Apache Hadoop](./hadoop/apache-hadoop-introduction.md)|HdFS-t, YARN erőforrás-kezelést és egy egyszerű MapReduce programozási modellt használó keretrendszer a kötegadatok párhuzamos feldolgozásához és elemzéséhez.|
|[Apache Spark](./spark/apache-spark-overview.md)|Nyílt forráskódú, párhuzamos feldolgozási keretrendszer, amely támogatja a memórián belüli feldolgozást a big data-elemző alkalmazások teljesítményének növelése érdekében. Lásd: [Mi az Apache Spark a HDInsightban?](./spark/apache-spark-overview.md)|
|[Apache HBase](./hbase/apache-hbase-overview.md)|A Hadoopra épülő NoSQL-adatbázis, amely véletlenszerű hozzáférést és erős konzisztenciát biztosít nagy mennyiségű strukturálatlan és félig strukturált adathoz – potenciálisan több milliárd sornyi oszlopszor. Lásd: [Mi a HDInsight-alapú HBase?](./hbase/apache-hbase-overview.md)|
|[ML-szolgáltatások](./r-server/r-server-overview.md)|Párhuzamos, elosztott R folyamatok üzemeltetésére és kezelésére szolgáló kiszolgáló. Skálázható, elosztott elemzési módszereket kínál az adatszakértők, statisztikusok és R-programozók számára a HDInsightban. Tekintse meg az [ML-szolgáltatások használatának áttekintését a HDInsighton](./r-server/r-server-overview.md).|
|[Apache Storm](./storm/apache-storm-overview.md)|Elosztott, valós idejű számítási rendszer nagy adatfolyamok gyors feldolgozásához. A Storm a HDInsightban felügyelt fürtként érhető el. Lásd: [Analyze real-time sensor data using Storm and Hadoop](./storm/apache-storm-overview.md) (Valós idejű érzékelőadatok elemzése a Storm és a Hadoop segítségével).|
|[Apache interaktív lekérdezés](./interactive-query/apache-interactive-query-get-started.md)|Memórián belüli gyorsítótárazás interaktív és gyorsabb Hive-lekérdezésekhez. Lásd: [Az interaktív lekérdezés használata a HDInsightban](./interactive-query/apache-interactive-query-get-started.md).|
|[Apache Kafka](./kafka/apache-kafka-introduction.md)|Nyílt forráskódú platform, amely streamelési adatfolyamatok és alkalmazások készítésére szolgál. A Kafka egy Üzenetsor funkciót is biztosít, amelynek segítségével közzétehet adatstreameket vagy feliratkozhat rájuk. Lásd: [A HDInsight alatt futó Apache Kafka bemutatása](./kafka/apache-kafka-introduction.md).|

## <a name="open-source-components-in-hdinsight"></a>Nyílt forráskódú összetevők a HDInsightban

Az Azure HDInsight lehetővé teszi, hogy nyílt forráskódú keretrendszerekkel, például a Hadoop, a Spark, a Hive, az LLAP, a Kafka, a Storm, a HBase és az R fürtöket hozzon létre. Ezek a klaszterek, alapértelmezés szerint, jön más nyílt forráskódú összetevők, amelyek szerepelnek a fürt, mint az [Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md), [Avro](https://avro.apache.org/docs/current/spec.html), [Apache Hive](https://hive.apache.org), [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog/), [Apache Mahout](https://mahout.apache.org/), [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Phoenix](https://phoenix.apache.org/), [Apache Pig](https://pig.apache.org/), [Apache, Apache , Apache](https://sqoop.apache.org/) [Tez](https://tez.apache.org/), [Apache Oozie](https://oozie.apache.org/), [Apache ZooKeeper](https://zookeeper.apache.org/).  

## <a name="programming-languages-in-hdinsight"></a>Programozási nyelvek a HDInsightban

A HDInsight-fürtök, például többek közt a Spark, a HBase, a Kafka, és a Hadoop, számos programozási nyelvet támogatnak. Egyes programozási nyelvek nincsenek alapértelmezés szerint telepítve. Az alapértelmezés szerint nem telepített tárak, modulok vagy csomagok esetén [parancsfájlművelettel telepítse az összetevőt.](./hdinsight-hadoop-script-actions-linux.md)

|Programozási nyelv  |Információ  |
|---------|---------|
|Alapértelmezés szerint támogatott programozási nyelvek     | Alapértelmezés szerint a HDInsight-fürtök a következőket támogatják:<ul><li>Java</li><li>Python</li><li>.NET</li><li>Indítás</li></ul>  |
|JVM (Java virtuális gép) nyelvek     | A Java-alapú virtuális gépeken (JVM) a Javán kívül számos más nyelv is futtatható. Előfordulhat azonban, hogy egyes nyelvek futtatásához további összetevőket is telepítenie kell a fürtön. A HDInsight-fürtök az alábbi JVM-alapú nyelveket támogatják: <ul><li>Clojure</li><li>Jython (Python a Javához)</li><li>Scala</li></ul>     |
|Hadoop-specifikus nyelvek     | A HDInsight-fürtök az alábbi, a Hadoop-technológiákra jellemző nyelveket támogatják: <ul><li>Pig Latin a Pig-feladatokhoz</li><li>HiveQL a Hive-feladatokhoz és a SparkSQL-hez</li></ul>        |

## <a name="development-tools-for-hdinsight"></a>A HDInsight fejlesztői eszközei

A HDInsight fejlesztői eszközei (köztük az IntelliJ, az Eclipse, a Visual Studio Code és a Visual Studio) használatával HDInsight-adatlekérdezéseket és feladatokat szerkeszthet és küldhet be Azure-ral zökkenőmentesen együttműködve.

* [Az Azure-eszköztár az IntelliJ-hez](./spark/apache-spark-intellij-tool-plugin.md)

* [Az Azure eszközkészlete az Eclipse-hez](./spark/apache-spark-eclipse-tool-plugin.md)

* [A VS Code-hoz készült Azure HDInsight-eszközök](./hdinsight-for-vscode.md)

* [A Visual Studióhoz készült Azure Data Lake-eszközök](./hadoop/apache-hadoop-visual-studio-tools-get-started.md)

## <a name="business-intelligence-on-hdinsight"></a>Üzleti intelligencia a HDInsighton

A jól ismert üzletiintelligencia- (BI-) eszközök a Power Query beépülő modul vagy a Microsoft Hive ODBC-illesztő segítségével kérik le, elemzik és jelentik a HDInsight rendszerébe integrált adatokat:

* [Az Apache Spark BI adatvizualizációs eszközeinek használata az Azure HDInsighttal](./spark/apache-spark-use-bi-tools.md)

* [Az Apache Hive-adatok megjelenítése a Microsoft Power BI szolgáltatással az Azure HDInsightban](./hadoop/apache-hadoop-connect-hive-power-bi.md)

* [Interaktív lekérdezéses Hive-adatok vizualizálása a Microsoft Power BI segítségével az Azure HDInsightban](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

* [Az Excel csatlakoztatása az Apache Hadoophoz a Power Queryvel](./hadoop/apache-hadoop-connect-excel-power-query.md) (Windows szükséges)

* [Az Excel csatlakoztatása az Apache Hadoophoz a Microsoft Hive ODBC illesztőprogrammal](./hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) (Windows szükséges)

## <a name="next-steps"></a>További lépések

* [Apache Hadoop-fürt létrehozása a HDInsightban](./hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
