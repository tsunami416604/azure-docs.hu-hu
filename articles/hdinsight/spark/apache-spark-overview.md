---
title: Mi az Apache Spark – Azure HDInsight
description: Ez a cikk bemutatja a HDInsight-alapú Spark rendszert, és azokat az eseteket, amelyekben a HDInsight-alapú Spark-fürt használható.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: overview
ms.date: 02/25/2020
ms.openlocfilehash: 83bfeb85d110f7a84720e943e28f5e014e4c3888
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605045"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Mi az az Azure HDInsight-alapú Apache Spark?

A Apache Spark egy párhuzamos feldolgozási keretrendszer, amely támogatja a memóriabeli feldolgozást a Big-adatanalitikai alkalmazások teljesítményének növelése érdekében. Az Azure HDInsight-alapú Apache Spark az Apache Spark felhőbeli megvalósítása, a Microsoft terméke. A HDInsight segítségével leegyszerűsíthető a Spark-fürtök létrehozása és konfigurálása az Azure-ban. A HDInsight-alapú Spark-fürtök kompatibilisek az Azure Storage és a Azure Data Lake Storage szolgáltatással. A HDInsight-alapú Spark-fürtök segítségével tehát elvégezheti az Azure-ban tárolt adatok feldolgozását. Az összetevők és a verziószámozási információk: [Apache Hadoop összetevők és verziók az Azure HDInsight-ben](../hdinsight-component-versioning.md).

![Spark: egyesített keretrendszer](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="what-is-apache-spark"></a>Mi az az Apache Spark?

a Spark alapelemeket nyújt a memórián belüli fürtszámításhoz. A Spark-feladatokkal az adatok betölthetők és gyorsítótárazhatók a memóriába, majd ismétlődő jelleggel lekérdezhetők. A memórián belüli számítástechnika sokkal gyorsabb, mint a lemezes alkalmazások, például a Hadoop, amelyek az Hadoop elosztott fájlrendszeren (HDFS) keresztül osztják meg az adatmegosztást. A Spark a Scala programozási nyelvbe is integrálható, így a helyi gyűjteményekhez hasonlóan módosíthatja az elosztott adatkészleteket. Nem kell mindent térképként rendszerezni és csökkenteni a műveletek számát.

![Hagyományos MapReduce vs. Spark](./media/apache-spark-overview/map-reduce-vs-spark1.png)

A HDInsight-alapú Spark-fürtök teljes körűen felügyelt Spark szolgáltatást nyújtanak. A HDInsight-alapú Spark-fürt létrehozásának előnyeit ez a lista foglalja össze.

| Funkció | Leírás |
| --- | --- |
| Könnyű létrehozás |Az Azure Portal, az Azure PowerShell vagy a HDInsight .NET SDK használatával percek alatt létrehozható egy új Spark-fürt a HDInsight platformon. Lásd: Ismerkedés [a Apache Spark-fürttel a HDInsight-ben](apache-spark-jupyter-spark-sql-use-portal.md). |
| Könnyű használat |A HDInsight-beli Spark-fürt tartalmazza a Jupyter-és az Apache Zeppelin-jegyzetfüzeteket. Ezeket a notebookokat interaktív adatfeldolgozásra és -vizualizációra használhatja. Lásd: az [Apache Zeppelin notebookok használata a Apache Spark](apache-spark-zeppelin-notebook.md) és az [adatbetöltések és lekérdezések futtatása Apache Spark fürtön](apache-spark-load-data-run-query.md).|
| REST API-k |A HDInsight-beli Spark-fürtök közé tartozik az [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), egy REST API-alapú Spark-feladat, amely a feladatok távoli elküldésére és figyelésére szolgál. Lásd: [Apache Spark REST API használata távoli feladatok HDInsight Spark-fürtbe való küldéséhez](apache-spark-livy-rest-interface.md).|
| Azure Data Lake Storage támogatása | A HDInsight-alapú Spark-fürtök az elsődleges tárolóként vagy további tárterületként is használhatják a Azure Data Lake Storage. További információ a Data Lake Storageről: [Azure Data Lake Storage áttekintése](../../data-lake-store/data-lake-store-overview.md). |
| Integráció az Azure-szolgáltatásokkal |A HDInsight-alapú Spark-fürt Azure Event Hubs-összekötőt is tartalmaz. A Event Hubs a [Apache Kafka](https://kafka.apache.org/)mellett is létrehozhat adatfolyam-alkalmazásokat, amelyek már elérhetők a Spark részeként. |
| ML Server-támogatás | Az ML Server HDInsighton belüli támogatását az **ML-szolgáltatási** fürttípus biztosítja. Az ML-szolgáltatások fürtjei elosztott R-számítások futtatására állíthatók be a Spark-fürtre jellemző sebesség mellett. További információ: [Mi az a ml Services az Azure HDInsight-ben](../r-server/r-server-overview.md). |
| Integráció külső integrált fejlesztői környezetekkel (IDE) | A HDInsight számos olyan IDE beépülő modult biztosít, amely alkalmazások létrehozásához és egy HDInsight Spark-fürt számára történő elküldéséhez bizonyulhat hasznosnak. További információ: [use Azure TOOLKIT for INTELLIJ Idea](apache-spark-intellij-tool-plugin.md) [(Spark & kaptár Tools for VSCode](../hdinsight-for-vscode.md)) és a [Azure Toolkit for Eclipse használata](apache-spark-eclipse-tool-plugin.md).|
| Egyidejű lekérdezések |A HDInsight-alapú Spark-fürtök támogatják az egyidejű lekérdezéseket. E képesség révén lehetővé válik, hogy az egyetlen, illetve a több felhasználótól és alkalmazástól származó több lekérdezés ugyanazokat a fürterőforrásokat használja. |
| Gyorsítótárazás SSD meghajtókon |Az adatok gyorsítótárazása történhet a memóriában vagy a fürtcsomópontokhoz kapcsolt SSD meghajtókon is. A memóriában történő gyorsítótárazás biztosítja a legjobb lekérdezési teljesítményt, ugyanakkor drága lehet. Az SSD-meghajtókon történő gyorsítótárazással viszont úgy javítható hatékonyan a lekérdezési teljesítmény, hogy ahhoz nincs szükség a memória teljes adatkészletéhez igazodó méretű fürt létrehozására. Lásd: [Apache Spark számítási feladatok teljesítményének javítása az Azure HDINSIGHT IO cache használatával](apache-spark-improve-performance-iocache.md). |
| Integráció BI-eszközökkel |A HDInsight-alapú Spark-fürtök összekötőket biztosítanak az olyan adatelemző BI-eszközök számára, mint a [Power BI](https://www.powerbi.com/). |
| Előre betöltött Anaconda-könyvtárak |A HDInsight-alapú Spark-fürtök előre telepített Anaconda-könyvtárakkal rendelkeznek. Az [anaconda](https://docs.continuum.io/anaconda/) a gépi tanuláshoz, az adatelemzéshez, a vizualizációhoz és egyebekhez közelítő 200-kódtárat biztosít. |
| Méretezhetőség | A HDInsight lehetővé teszi a fürtcsomópontok számának dinamikus módosítását az autoscale funkció használatával. Lásd: az [Azure HDInsight-fürtök automatikus méretezése](../hdinsight-autoscale-clusters.md). Emellett a Spark-fürtöket nem lehet adatvesztés nélkül eldobni, mert az összes tárolt adatmennyiség az Azure Storage-ban vagy a Data Lake Storageban van tárolva. |
| SLA |A HDInsight-alapú Spark-fürtökhöz a hét minden napján 24 órás ügyfélszolgálat, valamint az SLA által garantált 99,9%-os üzemidő jár. |

A HDInsight Apache Spark-fürtök a következő összetevőket tartalmazzák, amelyek alapértelmezés szerint a fürtökön elérhetők.

* [Spark mag](https://spark.apache.org/docs/latest/). A következőket tartalmazza: Spark mag, Spark SQL, Spark streamelési API-k, GraphX és MLlib.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter notebook](https://jupyter.org)
* [Apache Zeppelin notebook](http://zeppelin-project.org/)

A HDInsight-alapú Spark-fürtök [ODBC-illesztőt](https://go.microsoft.com/fwlink/?LinkId=616229) is biztosítanak a HDInsight-alapú Spark-fürtökhöz olyan BI-eszközökből történő csatlakozáshoz, mint a Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Spark-fürtarchitektúra

![A HDInsight Spark architektúrája](./media/apache-spark-overview/hdi-spark-architecture.png)

Könnyen megismerheti a Spark összetevőit azzal, hogy megértette, hogyan fut a Spark a HDInsight-fürtökön.

A Spark-alkalmazások független folyamatkészletekként futnak a fürtön, koordinálásukat az (illesztőprogramnak is nevezett) főprogram SparkContext objektuma végzi.

A SparkContext objektum különböző típusú fürtkezelőkhöz csatlakoztatható, amelyek az erőforrások lefoglalását végzik az egyes alkalmazásokban. Ezek a fürtcsomópontok az [Apache mesos](https://mesos.apache.org/), a [Apache Hadoop a fonal](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)vagy a Spark cluster Manager alkalmazást tartalmazzák. A HDInsightban a Spark a YARN fürtkezelő segítségével fut. A csatlakoztatást követően a Spark beszerzi a fürt feldolgozó csomópontjainak végrehajtóit. E folyamatok számítások futtatását és adatok tárolását végzik az alkalmazás számára. Ezt követően a Spark elküldi az alkalmazás kódját a végrehajtóknak. A kódot a SparkContext objektum számára átadott JAR- vagy Python-fájlok határozzák meg. Végül a SparkContext objektum elküldi futtatásra a feladatokat a végrehajtóknak.

A SparkContext objektum futtatja a felhasználó fő funkcióját, és végrehajtja a különféle párhuzamos műveleteket a feldolgozó csomópontokon. Ezután a SparkContext objektum összegyűjti a műveletek eredményeit. A feldolgozó csomópontok a és a Hadoop elosztott fájlrendszerből származó adatok olvasását és írását. A feldolgozó csomópontok a memóriában lévő átalakított adatokat is gyorsítótárazzák rugalmas elosztott adatkészletekként (RDD-kként).

A SparkContext objektum csatlakozik a Spark-főkiszolgálóhoz, és a feladata az alkalmazások átalakítása irányított gráfokká (DAG) az egyes feladatokhoz, amelyek a feldolgozó csomópontok végrehajtó folyamatain belül lesznek végrehajtva. Mindegyik alkalmazás saját végrehajtó folyamatokkal rendelkezik, amelyek az alkalmazás teljes időtartamáig működnek, és több szálon futtatnak feladatokat.

## <a name="spark-in-hdinsight-use-cases"></a>Spark in HDInsight – Használati esetek

A HDInsight-alapú Spark-fürtök a következő főbb forgatókönyvek megvalósítását teszik lehetővé:

### <a name="interactive-data-analysis-and-bi"></a>Interaktív adatelemzés és BI

A HDInsight Apache Spark az Azure Storage-ban vagy Azure Data Lake Storageban tárolja az adattárolást. Az üzleti szakértők és a legfontosabb döntéshozók ezen adatok alapján elemzéseket végezhetnek és jelentéseket állíthatnak elő, illetve a Microsoft Power BI használatával az elemzett adatokból interaktív jelentéseket készíthetnek. Az elemzők a fürtbeli tárolón található strukturálatlan, illetve részben strukturált adatokból kiindulva, notebookok használatával sémát adhatnak meg, majd a Microsoft PowerBI használatával adatmodelleket építhetnek fel. A HDInsight-alapú Spark-fürtök számos olyan külső BI-eszközt is támogatnak, mint például a Tableau, így leegyszerűsítik az adatelemzők, az üzleti szakértők és a fő döntéshozók munkáját.

* [Oktatóanyag: Spark-adatok vizualizációja a Power BI használatával](apache-spark-use-bi-tools.md)

### <a name="spark-machine-learning"></a>Spark Machine Learning

Az Apache Spark tartalmazza a Sparkra épülő [MLlib](https://spark.apache.org/mllib/) Machine Learning-könyvtárat, amelyet egy HDInsight-alapú Spark-fürtből használhat. A HDInsight-beli Spark-fürt magában foglalja az Anacondat, egy Python-disztribúciót, amely különböző típusú csomagokat tartalmaz a gépi tanuláshoz. Ha ehhez hozzáveszi a Jupyter és Zeppelin notebookok beépített támogatását is, a gépi tanulási alkalmazásokhoz megfelelő környezetet kap.

* [Oktatóanyag: építési hőmérsékletek előrejelzése HVAC-adatszolgáltatásokkal](apache-spark-ipython-notebook-machine-learning.md)  
* [Oktatóanyag: az élelmiszer-ellenőrzési eredmények előrejelzése](apache-spark-machine-learning-mllib-ipython.md)

### <a name="spark-streaming-and-real-time-data-analysis"></a>Spark-alapú streamelés és valós idejű adatelemzés

A HDInsight-alapú Spark széles körű támogatást nyújt a valós idejű elemzési megoldások kiépítéséhez. Amíg a Spark számos adatforrást (például Kafka, Flume, Twitter, ZeroMQ vagy TCP-szoftvercsatornák) támogató összekötőkkel rendelkezik, a Spark on HDInsight az Azure Event Hubs eseményközpontokból származó adatok magas szintű feldolgozását is támogatja. Az Event Hubs az Azure legnépszerűbb várólista-szolgáltatása. Az azonnal használható Event Hubs-támogatással a HDInsight-alapú Spark-fürtök ideális platformot nyújtanak a valós idejű elemzési folyamatok kiépítéséhez.

* [A Apache Spark streaming áttekintése](apache-spark-streaming-overview.md)
* [Strukturált streaming Apache Spark áttekintése](apache-spark-structured-streaming-overview.md)

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

A következő cikkekből többet is megtudhat a HDInsight Apache Sparkáról:

* [Gyors útmutató: Apache Spark-fürt létrehozása a HDInsight-ben és interaktív lekérdezés futtatása a Jupyter használatával](./apache-spark-jupyter-spark-sql-use-portal.md)
* [Oktatóanyag: Apache Spark-feladatok futtatása a Jupyter használatával](./apache-spark-load-data-run-query.md)
* [Oktatóanyag: az adatelemzés a BI-eszközökkel](./apache-spark-use-bi-tools.md)
* [Oktatóanyag: gépi tanulás Apache Spark használatával](./apache-spark-ipython-notebook-machine-learning.md)
* [Oktatóanyag: Scala Maven-alkalmazás létrehozása a IntelliJ használatával](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>További lépések

Ebben az áttekintésben néhány alapszintű információt ismertetünk az Azure HDInsight-alapú Apache Sparkról. Folytassa a következő cikkel, amelyből megtudhatja, hogyan hozhat létre HDInsight Spark-fürtöt és futtathat Spark SQL-lekérdezéseket:

* [Apache Spark-fürt létrehozása a HDInsight-ben](./apache-spark-jupyter-spark-sql-use-portal.md)
