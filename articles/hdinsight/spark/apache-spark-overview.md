---
title: Mi az Apache Spark - Azure HDInsight
description: Ez a cikk bemutatja a HDInsight-alapú Spark rendszert, és azokat az eseteket, amelyekben a HDInsight-alapú Spark-fürt használható.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: overview
ms.date: 02/25/2020
ms.openlocfilehash: 83bfeb85d110f7a84720e943e28f5e014e4c3888
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "77605045"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Mi az az Azure HDInsight-alapú Apache Spark?

Az Apache Spark egy párhuzamos feldolgozási keretrendszer, amely támogatja a memórián belüli feldolgozást a big data-analitikus alkalmazások teljesítményének növelése érdekében. Az Azure HDInsight-alapú Apache Spark az Apache Spark felhőbeli megvalósítása, a Microsoft terméke. A HDInsight segítségével leegyszerűsíthető a Spark-fürtök létrehozása és konfigurálása az Azure-ban. A HDInsight Spark-fürtjei kompatibilisek az Azure Storage szolgáltatással és az Azure Data Lake Storage szolgáltatással. A HDInsight-alapú Spark-fürtök segítségével tehát elvégezheti az Azure-ban tárolt adatok feldolgozását. Az összetevőkről és a verziószámozási információkról az [Apache Hadoop összetevői és verziói az Azure HDInsightban című témakörben talál.](../hdinsight-component-versioning.md)

![Spark: egyesített keretrendszer](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="what-is-apache-spark"></a>Mi az az Apache Spark?

a Spark alapelemeket nyújt a memórián belüli fürtszámításhoz. A Spark-feladatokkal az adatok betölthetők és gyorsítótárazhatók a memóriába, majd ismétlődő jelleggel lekérdezhetők. A memórián belüli számítástechnika sokkal gyorsabb, mint a lemezalapú alkalmazások, például a Hadoop, amely megosztja az adatokat a Hadoop elosztott fájlrendszeren (HDFS) keresztül. A Spark a Scala programozási nyelvbe is integrálható, így a helyi gyűjteményekhez hasonlóan módosíthatja az elosztott adatkészleteket. Nem kell mindent térképként rendszerezni és csökkenteni a műveletek számát.

![Hagyományos MapReduce vs. Spark](./media/apache-spark-overview/map-reduce-vs-spark1.png)

A HDInsight-alapú Spark-fürtök teljes körűen felügyelt Spark szolgáltatást nyújtanak. A HDInsight-alapú Spark-fürt létrehozásának előnyeit ez a lista foglalja össze.

| Szolgáltatás | Leírás |
| --- | --- |
| Könnyű létrehozás |Az Azure Portal, az Azure PowerShell vagy a HDInsight .NET SDK használatával percek alatt létrehozható egy új Spark-fürt a HDInsight platformon. Lásd: [Az Apache Spark-fürt első lépései a HDInsightban.](apache-spark-jupyter-spark-sql-use-portal.md) |
| Könnyű használat |A HDInsight Spark-fürtje idáig tartalmaz Jupyter és Apache Zeppelin-jegyzetfüzeteket. Ezeket a notebookokat interaktív adatfeldolgozásra és -vizualizációra használhatja. Lásd: [Apache Zeppelin notebookok használata az Apache Spark és](apache-spark-zeppelin-notebook.md) az Adatok [betöltése és lekérdezések futtatása Apache Spark-fürtön.](apache-spark-load-data-run-query.md)|
| REST API-k |A HDInsight Spark-fürtjei közé tartozik az [Apache Livy,](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)egy REST API-alapú Spark-feladatkiszolgáló, amely távolról küldi el és figyeli a feladatokat. Lásd: [Az Apache Spark REST API használata távoli feladatok beküldéséhez egy HDInsight Spark-fürtbe.](apache-spark-livy-rest-interface.md)|
| Az Azure Data Lake Storage támogatása | A HDInsight Spark-fürtjei az Azure Data Lake Storage-t is használhatják elsődleges tárolóként vagy további tárolóként. A Data Lake Storage szolgáltatásról az [Azure Data Lake Storage áttekintése című témakörben olvashat bővebben.](../../data-lake-store/data-lake-store-overview.md) |
| Integráció az Azure-szolgáltatásokkal |A HDInsight-alapú Spark-fürt Azure Event Hubs-összekötőt is tartalmaz. Streamelési alkalmazásokat hozhat létre az Event Hubs használatával, az [Apache Kafka](https://kafka.apache.org/)mellett, amely már elérhető a Spark részeként. |
| ML Server-támogatás | Az ML Server HDInsighton belüli támogatását az **ML-szolgáltatási** fürttípus biztosítja. Az ML-szolgáltatások fürtjei elosztott R-számítások futtatására állíthatók be a Spark-fürtre jellemző sebesség mellett. További információ: [Mi az Ml-szolgáltatások az Azure HDInsightban.](../r-server/r-server-overview.md) |
| Integráció külső integrált fejlesztői környezetekkel (IDE) | A HDInsight számos olyan IDE beépülő modult biztosít, amely alkalmazások létrehozásához és egy HDInsight Spark-fürt számára történő elküldéséhez bizonyulhat hasznosnak. További információ: [Azure Toolkit for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md), [Use Spark & Hive Tools for VSCode](../hdinsight-for-vscode.md), and [Use Azure Toolkit for Eclipse](apache-spark-eclipse-tool-plugin.md).|
| Egyidejű lekérdezések |A HDInsight-alapú Spark-fürtök támogatják az egyidejű lekérdezéseket. E képesség révén lehetővé válik, hogy az egyetlen, illetve a több felhasználótól és alkalmazástól származó több lekérdezés ugyanazokat a fürterőforrásokat használja. |
| Gyorsítótárazás SSD meghajtókon |Az adatok gyorsítótárazása történhet a memóriában vagy a fürtcsomópontokhoz kapcsolt SSD meghajtókon is. A memóriában történő gyorsítótárazás biztosítja a legjobb lekérdezési teljesítményt, ugyanakkor drága lehet. Az SSD-meghajtókon történő gyorsítótárazással viszont úgy javítható hatékonyan a lekérdezési teljesítmény, hogy ahhoz nincs szükség a memória teljes adatkészletéhez igazodó méretű fürt létrehozására. Lásd: [Az Apache Spark-i számítási feladatok teljesítményének javítása az Azure HDInsight IO-gyorsítótár használatával.](apache-spark-improve-performance-iocache.md) |
| Integráció BI-eszközökkel |A HDInsight-alapú Spark-fürtök összekötőket biztosítanak az olyan adatelemző BI-eszközök számára, mint a [Power BI](https://www.powerbi.com/). |
| Előre betöltött Anaconda-könyvtárak |A HDInsight-alapú Spark-fürtök előre telepített Anaconda-könyvtárakkal rendelkeznek. [Az Anaconda](https://docs.continuum.io/anaconda/) közel 200 kódtárat biztosít a gépi tanuláshoz, adatelemzéshez, vizualizációhoz és így tovább. |
| Méretezhetőség | A HDInsight lehetővé teszi a fürtcsomópontok számának dinamikus módosítását az Automatikus skálázás funkcióval. Lásd: [Az Azure HDInsight-fürtök automatikus méretezése.](../hdinsight-autoscale-clusters.md) A Spark-fürtök adatvesztés nélkül is eldobhatók, mivel az összes adat az Azure Storage vagy a Data Lake Storage tárolja. |
| SLA |A HDInsight-alapú Spark-fürtökhöz a hét minden napján 24 órás ügyfélszolgálat, valamint az SLA által garantált 99,9%-os üzemidő jár. |

A HDInsight ban található Apache Spark-fürtök a következő összetevőket tartalmazzák, amelyek alapértelmezés szerint elérhetők a fürtökön.

* [Spark mag](https://spark.apache.org/docs/latest/). A következőket tartalmazza: Spark mag, Spark SQL, Spark streamelési API-k, GraphX és MLlib.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter notebook](https://jupyter.org)
* [Apache Zeppelin notebook](http://zeppelin-project.org/)

A HDInsight-alapú Spark-fürtök [ODBC-illesztőt](https://go.microsoft.com/fwlink/?LinkId=616229) is biztosítanak a HDInsight-alapú Spark-fürtökhöz olyan BI-eszközökből történő csatlakozáshoz, mint a Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Spark-fürtarchitektúra

![A HDInsight Spark architektúrája](./media/apache-spark-overview/hdi-spark-architecture.png)

A Spark összetevői könnyen megérthetők a Spark HDInsight-fürtökön való futásának megértésével.

A Spark-alkalmazások független folyamatkészletekként futnak a fürtön, koordinálásukat az (illesztőprogramnak is nevezett) főprogram SparkContext objektuma végzi.

A SparkContext objektum különböző típusú fürtkezelőkhöz csatlakoztatható, amelyek az erőforrások lefoglalását végzik az egyes alkalmazásokban. Ezek a fürtkezelők közé tartozik [az Apache Mesos](https://mesos.apache.org/), [az Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)vagy a Spark cluster manager. A HDInsightban a Spark a YARN fürtkezelő segítségével fut. A csatlakoztatást követően a Spark beszerzi a fürt feldolgozó csomópontjainak végrehajtóit. E folyamatok számítások futtatását és adatok tárolását végzik az alkalmazás számára. Ezt követően a Spark elküldi az alkalmazás kódját a végrehajtóknak. A kódot a SparkContext objektum számára átadott JAR- vagy Python-fájlok határozzák meg. Végül a SparkContext objektum elküldi futtatásra a feladatokat a végrehajtóknak.

A SparkContext objektum futtatja a felhasználó fő funkcióját, és végrehajtja a különféle párhuzamos műveleteket a feldolgozó csomópontokon. Ezután a SparkContext objektum összegyűjti a műveletek eredményeit. A munkavégző csomópontok adatokat olvasnak és írnak a Hadoop elosztott fájlrendszeréből. A feldolgozó csomópontok a memóriában lévő átalakított adatokat is gyorsítótárazzák rugalmas elosztott adatkészletekként (RDD-kként).

A SparkContext objektum csatlakozik a Spark-főkiszolgálóhoz, és a feladata az alkalmazások átalakítása irányított gráfokká (DAG) az egyes feladatokhoz, amelyek a feldolgozó csomópontok végrehajtó folyamatain belül lesznek végrehajtva. Mindegyik alkalmazás saját végrehajtó folyamatokkal rendelkezik, amelyek az alkalmazás teljes időtartamáig működnek, és több szálon futtatnak feladatokat.

## <a name="spark-in-hdinsight-use-cases"></a>Spark in HDInsight – Használati esetek

A HDInsight-alapú Spark-fürtök a következő főbb forgatókönyvek megvalósítását teszik lehetővé:

### <a name="interactive-data-analysis-and-bi"></a>Interaktív adatelemzés és BI

A HDInsightban lévő Apache Spark az Azure Storage vagy az Azure Data Lake Storage szolgáltatásban tárolja az adatokat. Az üzleti szakértők és a legfontosabb döntéshozók ezen adatok alapján elemzéseket végezhetnek és jelentéseket állíthatnak elő, illetve a Microsoft Power BI használatával az elemzett adatokból interaktív jelentéseket készíthetnek. Az elemzők a fürtbeli tárolón található strukturálatlan, illetve részben strukturált adatokból kiindulva, notebookok használatával sémát adhatnak meg, majd a Microsoft PowerBI használatával adatmodelleket építhetnek fel. A HDInsight-alapú Spark-fürtök számos olyan külső BI-eszközt is támogatnak, mint például a Tableau, így leegyszerűsítik az adatelemzők, az üzleti szakértők és a fő döntéshozók munkáját.

* [Oktatóanyag: Spark-adatok vizualizációja a Power BI használatával](apache-spark-use-bi-tools.md)

### <a name="spark-machine-learning"></a>Spark Machine Learning

Az Apache Spark tartalmazza a Sparkra épülő [MLlib](https://spark.apache.org/mllib/) Machine Learning-könyvtárat, amelyet egy HDInsight-alapú Spark-fürtből használhat. Spark-fürt a HDInsightban is tartalmaz Anaconda, a Python-disztribúció különböző típusú csomagok gépi tanuláshoz. Ha ehhez hozzáveszi a Jupyter és Zeppelin notebookok beépített támogatását is, a gépi tanulási alkalmazásokhoz megfelelő környezetet kap.

* [Oktatóanyag: Az épület hőmérsékletének előrejelzése a fűtés- és légtechnikai adatok használatával](apache-spark-ipython-notebook-machine-learning.md)  
* [Oktatóanyag: Az élelmiszer-ellenőrzési eredmények előrejelzése](apache-spark-machine-learning-mllib-ipython.md)

### <a name="spark-streaming-and-real-time-data-analysis"></a>Spark-alapú streamelés és valós idejű adatelemzés

A HDInsight-alapú Spark széles körű támogatást nyújt a valós idejű elemzési megoldások kiépítéséhez. Amíg a Spark számos adatforrást (például Kafka, Flume, Twitter, ZeroMQ vagy TCP-szoftvercsatornák) támogató összekötőkkel rendelkezik, a Spark on HDInsight az Azure Event Hubs eseményközpontokból származó adatok magas szintű feldolgozását is támogatja. Az Event Hubs az Azure legnépszerűbb várólista-szolgáltatása. Az azonnal használható Event Hubs-támogatással a HDInsight-alapú Spark-fürtök ideális platformot nyújtanak a valós idejű elemzési folyamatok kiépítéséhez.

* [Az Apache Spark Streaming áttekintése](apache-spark-streaming-overview.md)
* [Az Apache Spark strukturált streamelésének áttekintése](apache-spark-structured-streaming-overview.md)

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

Az alábbi cikkek segítségével többet is megtudhat az Apache Sparkról a HDInsightban:

* [Gyorsútmutató: Hozzon létre egy Apache Spark-fürtöt a HDInsightban, és futtasson interaktív lekérdezést a Jupyter használatával](./apache-spark-jupyter-spark-sql-use-portal.md)
* [Oktatóanyag: Futtasson egy Apache Spark-feladatot a Jupyter használatával](./apache-spark-load-data-run-query.md)
* [Oktatóanyag: Adatok elemzése üzletiintelligencia-eszközökkel](./apache-spark-use-bi-tools.md)
* [Oktatóanyag: Gépi tanulás az Apache Spark használatával](./apache-spark-ipython-notebook-machine-learning.md)
* [Oktatóanyag: Hozzon létre egy Scala Maven alkalmazást az IntelliJ használatával](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>Következő lépések

Ebben az áttekintésben néhány alapszintű információt ismertetünk az Azure HDInsight-alapú Apache Sparkról. Folytassa a következő cikkel, amelyből megtudhatja, hogyan hozhat létre HDInsight Spark-fürtöt és futtathat Spark SQL-lekérdezéseket:

* [Apache Spark-fürt létrehozása a HDInsightban](./apache-spark-jupyter-spark-sql-use-portal.md)
