---
title: "Az Azure HDInsight-alapú Spark bemutatása| Microsoft Docs"
description: "Ez a cikk bemutatja a HDInsight-alapú Spark rendszert, és azokat az eseteket, amelyekben a HDInsight-alapú Spark-fürt használható."
keywords: "mi az apache spark, spark fürt, a spark bemutatása, hdinsight-alapú spark"
services: hdinsight
documentationcenter: 
author: maxluk
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 82334b9e-4629-4005-8147-19f875c8774e
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/07/2017
ms.author: maxluk
ms.openlocfilehash: eafa3962e335cbb781ff0e1497374c008ac564df
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="introduction-to-spark-on-hdinsight"></a>A HDInsight-alapú Spark bemutatása

Ez a cikk a HDInsight-alapú Spark rendszert mutatja be. Az <a href="http://spark.apache.org/" target="_blank">Apache Spark</a> egy nyílt forráskódú, párhuzamos feldolgozást végző keretrendszer, amely támogatja a memóriabeli feldolgozást a big data elemző alkalmazások teljesítményének növelése érdekében. A HDInsight-alapú Spark-fürt az Azure Storage-dzsal (WASB) és az Azure Data Lake Store-ral kompatibilis. A jelenleg az Azure-ban tárolt adatait így egy Spark-fürtön keresztül is könnyen feldolgozhatja.

A HDInsight-alapú Spark-fürt létrehozásával egyben olyan Azure-beli számítási erőforrásokat is létrehoz, amelyeken a Spark telepítve és konfigurálva van. Egy Spark-fürt HDInsightban történő létrehozása nagyjából 10 percet vesz igénybe. A feldolgozásra váró adatokat az Azure Storage vagy az Azure Data Lake Store tárolja. Lásd: [Az Azure Storage és a HDInsight együttes használata](../hdinsight-hadoop-use-blob-storage.md).

![Spark: egyesített keretrendszer](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="spark-vs-traditional-mapreduce"></a>A Spark és a hagyományos MapReduce

Mitől olyan gyors a Spark? Miben különbözik az Apache Spark architektúrája a hagyományos MapReduce-étól, amelynek köszönhetően jobb teljesítményt tud nyújtani az adatok megosztásához?

![A hagyományos MapReduce és Spark](./media/apache-spark-overview/mapreduce-vs-spark.png)

a Spark alapelemeket nyújt a memórián belüli fürtszámításhoz. A Spark-feladatok a memóriába töltenek és gyorsítótáraznak adatokat, és ismételten lekérdezik azokat a lemezalapú rendszereknél sokkal gyorsabban. A Spark a Scala programozási nyelvbe is integrálható, így a helyi gyűjteményekhez hasonlóan módosíthatja az elosztott adatkészleteket. Nem kell mindent térképként rendszerezni és csökkenteni a műveletek számát.

A Sparkban a műveletek közötti adatmegosztás gyorsabb, mivel az adatok a memóriában vannak tárolva. Ezzel szemben a Hadoop a HDFS-en keresztül oszt meg adatokat, amelynek a feldolgozása tovább tart.

## <a name="what-is-apache-spark-on-azure-hdinsight"></a>Mi az Azure HDInsight-alapú Apache Spark?
A HDInsight-alapú Spark-fürtök teljes körűen felügyelt Spark szolgáltatást nyújtanak. A HDInsight-alapú Spark-fürt létrehozásának előnyeit ez a lista foglalja össze.

| Szolgáltatás | Leírás |
| --- | --- |
| A Spark-fürtök könnyen létrehozhatók |Az Azure Portal, az Azure PowerShell vagy a HDInsight .NET SDK használatával percek alatt létrehozható egy új Spark-fürt a HDInsight platformon. Lásd: [Spark-fürt a HDInsightban – első lépések](apache-spark-jupyter-spark-sql.md) |
| Könnyű használat |A HDInsight-alapú Spark-fürt tartalmazza a Jupyter és a Zeppelin notebookokat. Ezeket a notebookokat interaktív adatfeldolgozásra és -vizualizációra használhatja.|
| REST API-k |A HDInsight-alapú Spark-fürtök tartalmazzák a [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server) REST API-alapú Spark-feladatkiszolgálót, amely a feladatok távoli elküldését és figyelését teszi lehetővé. |
| Az Azure Data Lake Store támogatása | A HDInsight-alapú Spark-fürt konfigurálható az Azure Data Lake Store kiegészítő és elsődleges tárhelyként való használatára is (elsődleges tárhelyként való használatra csak HDInsight 3.5-fürtök esetén). További információk a Data Lake Store-ról: [Áttekintés: Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md). |
| Integráció az Azure-szolgáltatásokkal |A HDInsight-alapú Spark-fürt Azure Event Hubs-összekötőt is tartalmaz. Az Event Hubs használatával az ügyfelek streamelési alkalmazásokat állíthatnak elő a már a Spark részeként elérhető [Kafka](http://kafka.apache.org/) mellett. |
| Támogatás az R Serverhez | Az R Server on HDInsight Spark-fürtjei elosztott R-számítások futtatására állíthatók be a Spark-fürtre jellemző sebesség mellett. További információk: [Get started using R Server on HDInsight](../r-server/r-server-get-started.md) (R Server on HDInsight – első lépések). |
| Integráció külső integrált fejlesztői környezetekkel (IDE) | Az olyan IDE-k számára, mint az IntelliJ IDEA és az Eclipse, a HDInsight beépülő modulokat nyújt, amelyeken keresztül alkalmazások hozhatók létre és küldhetők el a HDInsight-alapú Spark-fürthöz. További információ: [Az IntelliJ IDEA-hoz készült Azure-eszközkészlet használata](apache-spark-intellij-tool-plugin.md) és [Az Eclipse-hez készült Azure eszközkészlet használata](apache-spark-eclipse-tool-plugin.md).|
| Egyidejű lekérdezések |A HDInsight-alapú Spark-fürtök támogatják az egyidejű lekérdezéseket. Ezzel lehetővé válik, hogy az egyetlen, illetve a több felhasználótól és alkalmazástól származó több lekérdezés ugyanazokat a fürterőforrásokat használja. |
| Gyorsítótárazás SSD meghajtókon |Az adatok gyorsítótárazása történhet a memóriában vagy a fürtcsomópontokhoz kapcsolt SSD meghajtókon is. A memóriában történő gyorsítótárazás biztosítja a legjobb lekérdezési teljesítményt, ugyanakkor drága lehet; az SSD meghajtókon történő gyorsítótárazással pedig úgy javítható hatékonyan a lekérdezési teljesítmény, hogy ahhoz nincs szükség a memória teljes adatkészletéhez igazodó méretű fürt létrehozására. |
| Integráció BI-eszközökkel |A HDInsight-alapú Spark-fürtök összekötőket biztosítanak az olyan adatelemző BI-eszközök számára, mint a [Power BI](http://www.powerbi.com/) és a [Tableau](http://www.tableau.com/products/desktop). |
| Előre betöltött Anaconda-könyvtárak |A HDInsight Spark-fürtjei előre telepített Anaconda-könyvtárakkal rendelkeznek. Az [Anaconda](http://docs.continuum.io/anaconda/) közel 200 könyvtárat biztosít például a Machine Learning szolgáltatáshoz, az adatok elemzéséhez vagy a megjelenítéshez. |
| Méretezhetőség |Habár a fürtben található csomópontok számát a létrehozáskor megadhatja, elképzelhető, hogy a munkaterheléshez igazodva később növelni vagy csökkenteni szeretné majd a fürt méretét. A HDInsight-fürtök esetében lehetőség van a fürtben található csomópontok számának megváltoztatására. A Spark-fürtök ezenkívül adatvesztés nélkül törölhetők, hiszen minden adatot az Azure Storage vagy a Data Lake Store tárol. |
| Napi 24 órás támogatás a hét minden napján |A HDInsight-alapú Spark-fürtökhöz a hét minden napján 24 órás vállalati szintű ügyfélszolgálat, valamint az SLA által garantált 99,9%-os üzemidő jár. |

## <a name="spark-cluster-architecture"></a>Spark-fürtarchitektúra

Itt láthatja a Spark-fürtarchitektúrát és annak működését:

![Spark-fürtarchitektúra](./media/apache-spark-overview/spark-architecture.png)

Az átjárócsomóponton található az alkalmazások számát kezelő Spark-főkiszolgáló, az alkalmazások a Spark-illesztőre vannak leképezve. A Spark-főkiszolgáló minden alkalmazást a megfelelő módon kezel. A Spark a Mesos, YARN vagy a Spark fürtkezelőre telepíthető, amely alkalmazások számára foglal le feldolgozó csomópont erőforrásokat. A HDInsightban a Spark a YARN fürtkezelő segítségével fut. A fürtben lévő erőforrásokat a Spark-főkiszolgáló kezeli a HDInsightban. Ez azt jelenti, hogy a Spark-főkiszolgáló ismeri, hogy mely erőforrások (például a memória) foglaltak vagy érhetőek el a feldolgozó csomóponton.

Az illesztő futtatja a felhasználó fő funkcióját, és végrehajtja a különféle párhuzamos műveleteket a feldolgozó csomópontokon. Ezután az illesztő összegyűjti a műveletek eredményeit. A feldolgozó csomópontok adatokat írnak a Hadoop elosztott fájlrendszerbe (HDFS-ből), és adatokat olvasnak onnan. A feldolgozó csomópontok a memóriában lévő átalakított adatokat is gyorsítótárazzák rugalmas elosztott adatkészletekként (RDD-kként).

Miután az alkalmazás létrejött a Spark-főkiszolgálón, a Spark-főkiszolgáló lefoglalja az erőforrásokat az alkalmazások számára egy Spark-illesztő nevű végrehajtandó művelet létrehozásával. A Spark-illesztő a SparkContext elemet is létrehozza, és megkezdi az RDD-k létrehozását. Az RDD-k metaadatai a Spark-illesztőn vannak tárolva.

A Spark-illesztő csatlakozik a Spark-főkiszolgálóhoz, és a feladata az alkalmazások átalakítása irányított gráfokká (DAG) az egyes feladatokhoz, amelyek a feldolgozó csomópontok végrehajtó folyamatain belül lesznek végrehajtva. Mindegyik alkalmazás saját végrehajtó folyamatokkal rendelkezik, amelyek az alkalmazás teljes időtartamáig működnek, és több szálon futtatnak feladatokat.

## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>Melyek a Spark on HDInsight használati esetei?
A HDInsight-alapú Spark-fürtök a következő főbb forgatókönyvek megvalósítását teszik lehetővé:

### <a name="interactive-data-analysis-and-bi"></a>Interaktív adatelemzés és BI
[Oktatóanyag megtekintése](apache-spark-use-bi-tools.md)

A HDInsight-alapú Apache Spark az Azure Storage vagy az Azure Data Lake Store tárhelyein tárolja az adatokat. Az üzleti szakértők és a legfontosabb döntéshozók ezen adatok alapján elemzéseket végezhetnek és jelentéseket állíthatnak elő, illetve a Microsoft Power BI használatával az elemzett adatokból interaktív jelentéseket készíthetnek. Az elemzők a fürtbeli tárolón található strukturálatlan, illetve részben strukturált adatokból kiindulva, notebookok használatával sémát adhatnak meg, majd a Microsoft PowerBI használatával adatmodelleket építhetnek fel. A HDInsight-alapú Spark-fürtök számos olyan külső BI-eszközt is támogatnak, mint például a Tableau, így ideális platformot jelentenek az adatelemzők, az üzleti szakértők és a fő döntéshozók számára.

### <a name="spark-machine-learning"></a>Spark Machine Learning
[Oktatóanyag megtekintése: Épület-hőmérsékletek előrejelzése HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)

[Oktatóanyag megtekintése: Élelmiszervizsgálati eredmények előrejelzése](apache-spark-machine-learning-mllib-ipython.md)

Az Apache Spark tartalmazza a Sparkra épülő [MLlib](http://spark.apache.org/mllib/) Machine Learning-könyvtárat, amelyet egy HDInsight-alapú Spark-fürtből használhat. A HDInsight-alapú Spark-fürt tartalmazza a Python által terjesztett, számos gépi tanulási csomaggal rendelkező Anaconda rendszert. Ha ehhez hozzáveszi a Jupyter és Zeppelin notebookok beépített támogatását is, csúcskategóriás környezetet kap a gépi tanulási alkalmazásokhoz.

### <a name="spark-streaming-and-real-time-data-analysis"></a>Spark-alapú streamelés és valós idejű adatelemzés
[Oktatóanyag megtekintése](apache-spark-eventhub-streaming.md)

A HDInsight-alapú Spark széles körű támogatást nyújt a valós idejű elemzési megoldások kiépítéséhez. Amíg a Spark számos adatforrást (például Kafka, Flume, Twitter, ZeroMQ vagy TCP-szoftvercsatornák) támogató összekötőkkel rendelkezik, a Spark on HDInsight az Azure Event Hubs eseményközpontokból származó adatok magas szintű feldolgozását is támogatja. Az Event Hubs az Azure legnépszerűbb várólista-szolgáltatása. Az azonnal használható Event Hubs-támogatással a HDInsight-alapú Spark-fürtök ideális platformot nyújtanak a valós idejű elemzési folyamatok kiépítéséhez.

## <a name="next-steps"></a>Milyen összetevők találhatóak egy Spark-fürtben?
A HDInsight-alapú Spark-fürtök tartalmazzák az alábbi, a fürtökön alapértelmezés szerint elérhető összetevőket.

* [Spark mag](https://spark.apache.org/docs/1.5.1/). A következőket tartalmazza: Spark mag, Spark SQL, Spark streamelési API-k, GraphX és MLlib.
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter notebook](https://jupyter.org)
* [Zeppelin notebook](http://zeppelin-project.org/)

A HDInsight-alapú Spark-fürtök [ODBC-illesztőt](http://go.microsoft.com/fwlink/?LinkId=616229) is biztosítanak az HDInsight-alapú Spark-fürtökhöz olyan BI-eszközökből történő csatlakozáshoz, mint a Microsoft Power BI és a Tableau.

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?
A HDInsight-alapú Spark-fürt létrehozásának első lépései. Lásd: [Rövid útmutató: Linux rendszerű HDInsight-alapú Spark-fürt létrehozása és interaktív lekérdezés futtatása a Jupyter használatával](apache-spark-jupyter-spark-sql.md). 

## <a name="next-steps"></a>Következő lépések
### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: A Spark on HDInsight használata valós idejű streamelési alkalmazások összeállítására](apache-spark-eventhub-streaming.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
* [Az Apache Spark ismert problémái az Azure HDInsightban](apache-spark-known-issues.md).
