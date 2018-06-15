---
title: Mi az az Azure HDInsight-alapú Apache Spark? | Microsoft Docs
description: Ez a cikk bemutatja a HDInsight-alapú Spark rendszert, és azokat az eseteket, amelyekben a HDInsight-alapú Spark-fürt használható.
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 82334b9e-4629-4005-8147-19f875c8774e
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: overview
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: 7e01915a63e0e260dc85893bdf65cf7b86000cef
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33771218"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Mi az az Azure HDInsight-alapú Apache Spark?

Az *Apache Spark* egy párhuzamos feldolgozást végző keretrendszer, amely támogatja a memóriabeli feldolgozást a big data elemző alkalmazások teljesítményének növelése érdekében. Az Azure HDInsight-alapú Apache Spark az Apache Hadoop felhőbeli megvalósítása, a Microsoft terméke. A HDInsight segítségével leegyszerűsíthető a Spark-fürtök létrehozása és konfigurálása az Azure-ban. A HDInsight-alapú Spark-fürtök kompatibilisek az Azure Storage és az Azure Data Lake Store szolgáltatásokkal is. A HDInsight-alapú Spark-fürtök segítségével tehát elvégezheti az Azure-ban tárolt adatok feldolgozását. További információ az egyes összetevőkről és a verziószámozásról: [A Hadoop összetevői és verziói az Azure HDInsightban](../hdinsight-component-versioning.md).

![Spark: egyesített keretrendszer](./media/apache-spark-overview/hdinsight-spark-overview.png)


## <a name="what-is-spark"></a>Mi a Spark?

a Spark alapelemeket nyújt a memórián belüli fürtszámításhoz. A Spark-feladatokkal az adatok betölthetők és gyorsítótárazhatók a memóriába, majd ismétlődő jelleggel lekérdezhetők. A memóriában történő feldolgozás sokkal gyorsabb, mint a lemezalapú alkalmazások, így például a Hadoop esetében, amely az adatok megosztását HDFS-en keresztül végzi. A Spark a Scala programozási nyelvbe is integrálható, így a helyi gyűjteményekhez hasonlóan módosíthatja az elosztott adatkészleteket. Nem kell mindent térképként rendszerezni és csökkenteni a műveletek számát.

![A hagyományos MapReduce és Spark](./media/apache-spark-overview/mapreduce-vs-spark.png)

A HDInsight-alapú Spark-fürtök teljes körűen felügyelt Spark szolgáltatást nyújtanak. A HDInsight-alapú Spark-fürt létrehozásának előnyeit ez a lista foglalja össze.

| Szolgáltatás | Leírás |
| --- | --- |
| Könnyű létrehozás |Az Azure Portal, az Azure PowerShell vagy a HDInsight .NET SDK használatával percek alatt létrehozható egy új Spark-fürt a HDInsight platformon. Lásd: [Spark-fürt a HDInsightban – első lépések](apache-spark-jupyter-spark-sql.md) |
| Könnyű használat |A HDInsight-alapú Spark-fürt tartalmazza a Jupyter és a Zeppelin notebookokat. Ezeket a notebookokat interaktív adatfeldolgozásra és -vizualizációra használhatja.|
| REST API-k |A HDInsight-alapú Spark-fürtök tartalmazzák a [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server) REST API-alapú Spark-feladatkiszolgálót, amely a feladatok távoli elküldését és figyelését teszi lehetővé. |
| Az Azure Data Lake Store támogatása | A HDInsight-alapú Spark-fürtök az Azure Data Lake Store-t elsődleges vagy kiegészítő tárterületként is használhatják. További információk a Data Lake Store-ról: [Áttekintés: Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md). |
| Integráció az Azure-szolgáltatásokkal |A HDInsight-alapú Spark-fürt Azure Event Hubs-összekötőt is tartalmaz. Az Event Hubs használatával streamelési alkalmazások állíthatók elő a Spark részeként már elérhető [Kafka](http://kafka.apache.org/) mellett. |
| Támogatás az R Serverhez | Az R Server in HDInsight Spark-fürtjei elosztott R-számítások futtatására állíthatók be a Spark-fürtre jellemző sebesség mellett. További információ: [R Server in HDInsight – első lépések](../r-server/r-server-get-started.md). |
| Integráció külső integrált fejlesztői környezetekkel (IDE) | A HDInsight számos olyan IDE beépülő modult biztosít, amely alkalmazások létrehozásához és egy HDInsight Spark-fürt számára történő elküldéséhez bizonyulhat hasznosnak. További információ: [Az IntelliJ IDEA-hoz készült Azure-eszközkészlet használata](apache-spark-intellij-tool-plugin.md) és [Az Eclipse-hez készült Azure eszközkészlet használata](apache-spark-eclipse-tool-plugin.md).|
| Egyidejű lekérdezések |A HDInsight-alapú Spark-fürtök támogatják az egyidejű lekérdezéseket. E képesség révén lehetővé válik, hogy az egyetlen, illetve a több felhasználótól és alkalmazástól származó több lekérdezés ugyanazokat a fürterőforrásokat használja. |
| Gyorsítótárazás SSD meghajtókon |Az adatok gyorsítótárazása történhet a memóriában vagy a fürtcsomópontokhoz kapcsolt SSD meghajtókon is. A memóriában történő gyorsítótárazás biztosítja a legjobb lekérdezési teljesítményt, ugyanakkor drága lehet. Az SSD-meghajtókon történő gyorsítótárazással viszont úgy javítható hatékonyan a lekérdezési teljesítmény, hogy ahhoz nincs szükség a memória teljes adatkészletéhez igazodó méretű fürt létrehozására. |
| Integráció BI-eszközökkel |A HDInsight-alapú Spark-fürtök összekötőket biztosítanak az olyan adatelemző BI-eszközök számára, mint a [Power BI](http://www.powerbi.com/). |
| Előre betöltött Anaconda-könyvtárak |A HDInsight-alapú Spark-fürtök előre telepített Anaconda-könyvtárakkal rendelkeznek. Az [Anaconda](http://docs.continuum.io/anaconda/) közel 200 könyvtárat biztosít például a Machine Learning szolgáltatáshoz, az adatok elemzéséhez vagy a megjelenítéshez. |
| Méretezhetőség | A HDInsight segítségével módosítható a fürtcsomópontok száma. A Spark-fürtök ezenkívül adatvesztés nélkül törölhetők, hiszen minden adatot az Azure Storage vagy a Data Lake Store tárol. |
| SLA |A HDInsight-alapú Spark-fürtökhöz a hét minden napján 24 órás ügyfélszolgálat, valamint az SLA által garantált 99,9%-os üzemidő jár. |

A HDInsight-alapú Spark-fürtök tartalmazzák az alábbi, a fürtökön alapértelmezés szerint elérhető összetevőket.

* [Spark mag](https://spark.apache.org/docs/1.5.1/). A következőket tartalmazza: Spark mag, Spark SQL, Spark streamelési API-k, GraphX és MLlib.
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter notebook](https://jupyter.org)
* [Zeppelin notebook](http://zeppelin-project.org/)

A HDInsight-alapú Spark-fürtök [ODBC-illesztőt](http://go.microsoft.com/fwlink/?LinkId=616229) is biztosítanak a HDInsight-alapú Spark-fürtökhöz olyan BI-eszközökből történő csatlakozáshoz, mint a Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Spark-fürtarchitektúra

![A HDInsight Spark architektúrája](./media/apache-spark-overview/spark-architecture.png)

A Spark összetevőinek szerepét legegyszerűbben a Spark HDInsight-fürtökön történő futtatásán keresztül érhetjük meg.

A Spark-alkalmazások független folyamatkészletekként futnak a fürtön, koordinálásukat az (illesztőprogramnak is nevezett) főprogram SparkContext objektuma végzi.

A SparkContext objektum különböző típusú fürtkezelőkhöz csatlakoztatható, amelyek az erőforrások lefoglalását végzik az egyes alkalmazásokban. Ilyen fürtkezelők például az Apache Mesos, az Apache YARN és a Spark fürtkezelő. A HDInsightban a Spark a YARN fürtkezelő segítségével fut. A csatlakoztatást követően a Spark beszerzi a fürt feldolgozó csomópontjainak végrehajtóit. E folyamatok számítások futtatását és adatok tárolását végzik az alkalmazás számára. Ezt követően a Spark elküldi az alkalmazás kódját a végrehajtóknak. A kódot a SparkContext objektum számára átadott JAR- vagy Python-fájlok határozzák meg. Végül a SparkContext objektum elküldi futtatásra a feladatokat a végrehajtóknak.

A SparkContext objektum futtatja a felhasználó fő funkcióját, és végrehajtja a különféle párhuzamos műveleteket a feldolgozó csomópontokon. Ezután a SparkContext objektum összegyűjti a műveletek eredményeit. A feldolgozó csomópontok adatokat írnak a Hadoop elosztott fájlrendszerbe (HDFS-ből), és adatokat olvasnak onnan. A feldolgozó csomópontok a memóriában lévő átalakított adatokat is gyorsítótárazzák rugalmas elosztott adatkészletekként (RDD-kként).

A SparkContext objektum csatlakozik a Spark-főkiszolgálóhoz, és a feladata az alkalmazások átalakítása irányított gráfokká (DAG) az egyes feladatokhoz, amelyek a feldolgozó csomópontok végrehajtó folyamatain belül lesznek végrehajtva. Mindegyik alkalmazás saját végrehajtó folyamatokkal rendelkezik, amelyek az alkalmazás teljes időtartamáig működnek, és több szálon futtatnak feladatokat.

## <a name="spark-in-hdinsight-use-cases"></a>Spark in HDInsight – Használati esetek

A HDInsight-alapú Spark-fürtök a következő főbb forgatókönyvek megvalósítását teszik lehetővé:

- Interaktív adatelemzés és BI

    A HDInsight-alapú Apache Spark az Azure Storage vagy az Azure Data Lake Store tárhelyein tárolja az adatokat. Az üzleti szakértők és a legfontosabb döntéshozók ezen adatok alapján elemzéseket végezhetnek és jelentéseket állíthatnak elő, illetve a Microsoft Power BI használatával az elemzett adatokból interaktív jelentéseket készíthetnek. Az elemzők a fürtbeli tárolón található strukturálatlan, illetve részben strukturált adatokból kiindulva, notebookok használatával sémát adhatnak meg, majd a Microsoft PowerBI használatával adatmodelleket építhetnek fel. A HDInsight-alapú Spark-fürtök számos olyan külső BI-eszközt is támogatnak, mint például a Tableau, így leegyszerűsítik az adatelemzők, az üzleti szakértők és a fő döntéshozók munkáját.

    [Oktatóanyag: Spark-adatok vizualizációja a Power BI használatával](apache-spark-use-bi-tools.md)
- Spark Machine Learning

    Az Apache Spark tartalmazza a Sparkra épülő [MLlib](http://spark.apache.org/mllib/) Machine Learning-könyvtárat, amelyet egy HDInsight-alapú Spark-fürtből használhat. A HDInsight-alapú Spark-fürt tartalmazza a Python által terjesztett, számos gépi tanulási csomaggal rendelkező Anaconda rendszert. Ha ehhez hozzáveszi a Jupyter és Zeppelin notebookok beépített támogatását is, a gépi tanulási alkalmazásokhoz megfelelő környezetet kap.

    [Oktatóanyag: Épület-hőmérsékletek előrejelzése HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md) [Oktatóanyag: Élelmiszer-vizsgálati eredmények előrejelzése](apache-spark-machine-learning-mllib-ipython.md)    
- Spark-alapú streamelés és valós idejű adatelemzés

    A HDInsight-alapú Spark széles körű támogatást nyújt a valós idejű elemzési megoldások kiépítéséhez. Amíg a Spark számos adatforrást (például Kafka, Flume, Twitter, ZeroMQ vagy TCP-szoftvercsatornák) támogató összekötőkkel rendelkezik, a Spark on HDInsight az Azure Event Hubs eseményközpontokból származó adatok magas szintű feldolgozását is támogatja. Az Event Hubs az Azure legnépszerűbb várólista-szolgáltatása. Az azonnal használható Event Hubs-támogatással a HDInsight-alapú Spark-fürtök ideális platformot nyújtanak a valós idejű elemzési folyamatok kiépítéséhez.
    
## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

A Spark in HDInsightról további információkat a következő cikkekben talál:

- [Rövid útmutató: HDInsight-alapú Spark-fürt létrehozása és interaktív lekérdezés futtatása a Jupyter használatával](./apache-spark-jupyter-spark-sql.md)
- [Oktatóanyag: Spark-feladatok futtatása a Jupyter használatával](./apache-spark-load-data-run-query.md)
- [Oktatóanyag: Adatok elemzése BI-eszközökkel](./apache-spark-use-bi-tools.md)
- [Oktatóanyag: Machine Learning a Spark használatával](./apache-spark-ipython-notebook-machine-learning.md)
- [Oktatóanyag: Scala Maven-alkalmazás létrehozása az IntelliJ használatával](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>További lépések

Ebben az áttekintésben néhány alapszintű információt ismertetünk az Azure HDInsight-alapú Apache Sparkról. Folytassa a következő cikkel, amelyből megtudhatja, hogyan hozhat létre HDInsight Spark-fürtöt és futtathat Spark SQL-lekérdezéseket:

- [Spark-fürt létrehozása a HDInsightban](./apache-spark-jupyter-spark-sql.md)