---
title: Mi az Apache Spark – Azure HDInsight
description: Ez a cikk bemutatja a HDInsight-alapú Spark rendszert, és azokat az eseteket, amelyekben a HDInsight-alapú Spark-fürt használható.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: overview
ms.date: 01/28/2019
ms.author: hrasheed
ms.openlocfilehash: c08672b178a9327b39414ca1c0e71653f62d1d07
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295797"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Mi az az Azure HDInsight-alapú Apache Spark?

Az Apache Spark az egy párhuzamos feldolgozást végző keretrendszer, amely támogatja a memórián belüli feldolgozást a big data elemző alkalmazások teljesítményének növelése érdekében. Az Azure HDInsight-alapú Apache Spark az Apache Spark felhőbeli megvalósítása, a Microsoft terméke. A HDInsight segítségével leegyszerűsíthető a Spark-fürtök létrehozása és konfigurálása az Azure-ban. A HDInsight Spark-fürtök Azure Storage és az Azure Data Lake Storage kompatibilisek. A HDInsight-alapú Spark-fürtök segítségével tehát elvégezheti az Azure-ban tárolt adatok feldolgozását. Az összetevők és a verzióinformációk: [Apache Hadoop-összetevők és verziók az Azure HDInsight](../hdinsight-component-versioning.md).

![Spark: egyesített keretrendszer](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="what-is-apache-spark"></a>Mi az Apache Spark?

a Spark alapelemeket nyújt a memórián belüli fürtszámításhoz. A Spark-feladatokkal az adatok betölthetők és gyorsítótárazhatók a memóriába, majd ismétlődő jelleggel lekérdezhetők. Memóriabeli számításokhoz sokkal gyorsabb, mint a lemez-alapú alkalmazásokhoz, mint a Hadoop, Hadoop elosztott fájlrendszer (HDFS) keresztül oszt meg adatokat. A Spark a Scala programozási nyelvbe is integrálható, így a helyi gyűjteményekhez hasonlóan módosíthatja az elosztott adatkészleteket. Nem kell mindent térképként rendszerezni és csökkenteni a műveletek számát.

![A hagyományos MapReduce és Spark](./media/apache-spark-overview/mapreduce-vs-spark.png)

A HDInsight-alapú Spark-fürtök teljes körűen felügyelt Spark szolgáltatást nyújtanak. A HDInsight-alapú Spark-fürt létrehozásának előnyeit ez a lista foglalja össze.

| Szolgáltatás | Leírás |
| --- | --- |
| Könnyű létrehozás |Az Azure Portal, az Azure PowerShell vagy a HDInsight .NET SDK használatával percek alatt létrehozható egy új Spark-fürt a HDInsight platformon. Lásd: [Apache Spark-fürt a HDInsight – első lépések](apache-spark-jupyter-spark-sql-use-portal.md). |
| Könnyű használat |A HDInsight Spark-fürt Jupyter- és Apache Zeppelin-jegyzetfüzeteket tartalmaznak. Ezeket a notebookokat interaktív adatfeldolgozásra és -vizualizációra használhatja.|
| REST API-k |A HDInsight Spark-fürtök tartalmazzák [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), egy REST API-alapú Spark-feladatkiszolgálót távoli feladatokat küldhet és felügyelhet. Lásd: [Apache Spark REST API használata egy HDInsight Spark-fürt távoli feladatok elküldéséhez](apache-spark-livy-rest-interface.md).|
| Az Azure Data Lake Storage támogatása | A HDInsight Spark-fürtök az Azure Data Lake Storage is használhatja az elsődleges tárolási vagy a további tárhely. További információ a Data Lake Storage: [az Azure Data Lake Storage áttekintése](../../data-lake-store/data-lake-store-overview.md). |
| Integráció az Azure-szolgáltatásokkal |A HDInsight-alapú Spark-fürt Azure Event Hubs-összekötőt is tartalmaz. Streamelési alkalmazásokat mellett az Event Hubs használatával hozhat létre [Apache Kafka](https://kafka.apache.org/), amely már elérhető a Spark részeként. |
| ML Server-támogatás | Az ML Server HDInsighton belüli támogatását az **ML-szolgáltatási** fürttípus biztosítja. Az ML-szolgáltatások fürtjei elosztott R-számítások futtatására állíthatók be a Spark-fürtre jellemző sebesség mellett. További információ: [ML Server HDInsighton belül – első lépések](../r-server/r-server-get-started.md). |
| Integráció külső integrált fejlesztői környezetekkel (IDE) | A HDInsight számos olyan IDE beépülő modult biztosít, amely alkalmazások létrehozásához és egy HDInsight Spark-fürt számára történő elküldéséhez bizonyulhat hasznosnak. További információkért lásd: [Azure-eszközkészlet használata az IntelliJ Idea](apache-spark-intellij-tool-plugin.md), [használata HDInsight VSCode,- és [Eclipse-hez készült Azure eszközkészlet használata](apache-spark-eclipse-tool-plugin.md).|
| Egyidejű lekérdezések |A HDInsight-alapú Spark-fürtök támogatják az egyidejű lekérdezéseket. E képesség révén lehetővé válik, hogy az egyetlen, illetve a több felhasználótól és alkalmazástól származó több lekérdezés ugyanazokat a fürterőforrásokat használja. |
| Gyorsítótárazás SSD meghajtókon |Az adatok gyorsítótárazása történhet a memóriában vagy a fürtcsomópontokhoz kapcsolt SSD meghajtókon is. A memóriában történő gyorsítótárazás biztosítja a legjobb lekérdezési teljesítményt, ugyanakkor drága lehet. Az SSD-meghajtókon történő gyorsítótárazással viszont úgy javítható hatékonyan a lekérdezési teljesítmény, hogy ahhoz nincs szükség a memória teljes adatkészletéhez igazodó méretű fürt létrehozására. |
| Integráció BI-eszközökkel |A HDInsight-alapú Spark-fürtök összekötőket biztosítanak az olyan adatelemző BI-eszközök számára, mint a [Power BI](https://www.powerbi.com/). |
| Előre betöltött Anaconda-könyvtárak |A HDInsight-alapú Spark-fürtök előre telepített Anaconda-könyvtárakkal rendelkeznek. Az [Anaconda](https://docs.continuum.io/anaconda/) közel 200 könyvtárat biztosít például a Machine Learning szolgáltatáshoz, az adatok elemzéséhez vagy a megjelenítéshez. |
| Méretezhetőség | A HDInsight segítségével módosítható a fürtcsomópontok száma. Emellett a Spark-fürtök törölhetők nincs adatvesztés, mivel az Azure Storage vagy a Data Lake Storage tárolt összes adatot. |
| SLA |A HDInsight-alapú Spark-fürtökhöz a hét minden napján 24 órás ügyfélszolgálat, valamint az SLA által garantált 99,9%-os üzemidő jár. |

A HDInsight Apache Spark-fürtök tartalmazzák a következő összetevőket, a fürtökön alapértelmezés szerint elérhető.

* [Spark mag](https://spark.apache.org/docs/latest/). A következőket tartalmazza: Spark mag, Spark SQL, Spark streamelési API-k, GraphX és MLlib.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter notebook](https://jupyter.org)
* [Apache Zeppelin notebook](http://zeppelin-project.org/)

A HDInsight-alapú Spark-fürtök [ODBC-illesztőt](https://go.microsoft.com/fwlink/?LinkId=616229) is biztosítanak a HDInsight-alapú Spark-fürtökhöz olyan BI-eszközökből történő csatlakozáshoz, mint a Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Spark-fürtarchitektúra

![A HDInsight Spark architektúrája](./media/apache-spark-overview/spark-architecture.png)

A Spark összetevőinek szerepét legegyszerűbben a Spark HDInsight-fürtökön történő futtatásán keresztül érhetjük meg.

A Spark-alkalmazások független folyamatkészletekként futnak a fürtön, koordinálásukat az (illesztőprogramnak is nevezett) főprogram SparkContext objektuma végzi.

A SparkContext objektum különböző típusú fürtkezelőkhöz csatlakoztatható, amelyek az erőforrások lefoglalását végzik az egyes alkalmazásokban. Ezek olyan fürtkezelők tartalmaznak [Apache Mesos](https://mesos.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), vagy a Spark-kezelőt. A HDInsightban a Spark a YARN fürtkezelő segítségével fut. A csatlakoztatást követően a Spark beszerzi a fürt feldolgozó csomópontjainak végrehajtóit. E folyamatok számítások futtatását és adatok tárolását végzik az alkalmazás számára. Ezt követően a Spark elküldi az alkalmazás kódját a végrehajtóknak. A kódot a SparkContext objektum számára átadott JAR- vagy Python-fájlok határozzák meg. Végül a SparkContext objektum elküldi futtatásra a feladatokat a végrehajtóknak.

A SparkContext objektum futtatja a felhasználó fő funkcióját, és végrehajtja a különféle párhuzamos műveleteket a feldolgozó csomópontokon. Ezután a SparkContext objektum összegyűjti a műveletek eredményeit. A feldolgozó csomópontok adatokat olvasnak és írnak a és a Hadoop elosztott fájlrendszerbe. A feldolgozó csomópontok a memóriában lévő átalakított adatokat is gyorsítótárazzák rugalmas elosztott adatkészletekként (RDD-kként).

A SparkContext objektum csatlakozik a Spark-főkiszolgálóhoz, és a feladata az alkalmazások átalakítása irányított gráfokká (DAG) az egyes feladatokhoz, amelyek a feldolgozó csomópontok végrehajtó folyamatain belül lesznek végrehajtva. Mindegyik alkalmazás saját végrehajtó folyamatokkal rendelkezik, amelyek az alkalmazás teljes időtartamáig működnek, és több szálon futtatnak feladatokat.

## <a name="spark-in-hdinsight-use-cases"></a>Spark in HDInsight – Használati esetek

A HDInsight-alapú Spark-fürtök a következő főbb forgatókönyvek megvalósítását teszik lehetővé:

- Interaktív adatelemzés és BI

    Az Apache Spark on HDInsight az Azure Storage vagy az Azure Data Lake Storage tárolja az adatokat. Az üzleti szakértők és a legfontosabb döntéshozók ezen adatok alapján elemzéseket végezhetnek és jelentéseket állíthatnak elő, illetve a Microsoft Power BI használatával az elemzett adatokból interaktív jelentéseket készíthetnek. Az elemzők a fürtbeli tárolón található strukturálatlan, illetve részben strukturált adatokból kiindulva, notebookok használatával sémát adhatnak meg, majd a Microsoft PowerBI használatával adatmodelleket építhetnek fel. A HDInsight-alapú Spark-fürtök számos olyan külső BI-eszközt is támogatnak, mint például a Tableau, így leegyszerűsítik az adatelemzők, az üzleti szakértők és a fő döntéshozók munkáját.

    [Oktatóanyag: A Power BI használata Spark-adatok megjelenítése](apache-spark-use-bi-tools.md)
- Spark Machine Learning

    Az Apache Spark tartalmazza a Sparkra épülő [MLlib](https://spark.apache.org/mllib/) Machine Learning-könyvtárat, amelyet egy HDInsight-alapú Spark-fürtből használhat. A HDInsight-alapú Spark-fürt tartalmazza a Python által terjesztett, számos gépi tanulási csomaggal rendelkező Anaconda rendszert. Ha ehhez hozzáveszi a Jupyter és Zeppelin notebookok beépített támogatását is, a gépi tanulási alkalmazásokhoz megfelelő környezetet kap.

    [Oktatóanyag: HVAC-adatok épület-hőmérsékletek előrejelzése](apache-spark-ipython-notebook-machine-learning.md)  
    [Oktatóanyag: Élelmiszervizsgálati eredmények előrejelzése](apache-spark-machine-learning-mllib-ipython.md)    
- Spark-alapú streamelés és valós idejű adatelemzés

    A HDInsight-alapú Spark széles körű támogatást nyújt a valós idejű elemzési megoldások kiépítéséhez. Amíg a Spark számos adatforrást (például Kafka, Flume, Twitter, ZeroMQ vagy TCP-szoftvercsatornák) támogató összekötőkkel rendelkezik, a Spark on HDInsight az Azure Event Hubs eseményközpontokból származó adatok magas szintű feldolgozását is támogatja. Az Event Hubs az Azure legnépszerűbb várólista-szolgáltatása. Az azonnal használható Event Hubs-támogatással a HDInsight-alapú Spark-fürtök ideális platformot nyújtanak a valós idejű elemzési folyamatok kiépítéséhez.

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

Használhatja a következő cikkekben talál további információt az Apache Spark on HDInsight:

- [Gyors útmutató: A HDInsight egy Apache Spark-fürt létrehozása és interaktív lekérdezés futtatása a Jupyter használatával](./apache-spark-jupyter-spark-sql-use-portal.md)
- [Oktatóanyag: A Jupyter használatával egy Apache Spark-feladat futtatása](./apache-spark-load-data-run-query.md)
- [Oktatóanyag: Adatok elemzése BI-eszközökkel](./apache-spark-use-bi-tools.md)
- [Oktatóanyag: Az Apache Spark használatával a gépi tanulás](./apache-spark-ipython-notebook-machine-learning.md)
- [Oktatóanyag: Alkalmazás létrehozása a Scala Maven használatával intellij-vel](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>További lépések

Ebben az áttekintésben néhány alapszintű információt ismertetünk az Azure HDInsight-alapú Apache Sparkról. Folytassa a következő cikkel, amelyből megtudhatja, hogyan hozhat létre HDInsight Spark-fürtöt és futtathat Spark SQL-lekérdezéseket:

- [A HDInsight egy Apache Spark-fürt létrehozása](./apache-spark-jupyter-spark-sql-use-portal.md)
