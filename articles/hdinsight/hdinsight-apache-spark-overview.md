---
title: "Az Apache Spark on Azure HDInsight áttekintése | Microsoft Docs"
description: "Bevezetés az Apache Spark on HDInsight használatába, valamint az Apache Spark on HDInsight az alkalmazásokban történő használatát bemutató forgatókönyvek."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 82334b9e-4629-4005-8147-19f875c8774e
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: edfdba3105aba1079d3c707e7320770c4a999a32
ms.lasthandoff: 03/15/2017


---
# <a name="overview-apache-spark-on-hdinsight"></a>Áttekintés: Apache Spark on HDInsight

Az <a href="http://spark.apache.org/" target="_blank">Apache Spark</a> egy nyílt forráskódú, párhuzamos feldolgozást végző keretrendszer, amely támogatja a memóriabeli feldolgozást a big data elemző alkalmazások teljesítményének növelése érdekében. A Spark feldolgozási motorjára a nagy sebesség, a könnyű használat és a kifinomult elemzési képességek jellemzőek. A Spark memóriában tárolt számítási képességei jól funkcionálnak a Machine Learning és a Graph számítások iteratív algoritmusaival együtt. A Spark az Azure Blob Storage (WASB) szolgáltatással is kompatibilis, így a jelenleg az Azure-ban tárolt adatainak feldolgozását a Sparkon keresztül elvégezheti.

Amikor Spark-fürtöt hoz létre a HDInsightban, olyan Azure számítási erőforrásokat is létrehoz, amelyeken a Spark telepítve és konfigurálva van. Egy Spark-fürt HDInsightban történő létrehozása nagyjából tíz percet vesz igénybe. A feldolgozásra kijelölt adatok az Azure Storage-ban vannak tárolva. Lásd [az Azure Storage és a HDInsight együttes használatát][hdinsight-storage] ismertető cikket.

![Apache Spark on Azure HDInsight](./media/hdinsight-apache-spark-overview/hdispark.architecture.png "Apache Spark on Azure HDInsight")

**Meg szeretne ismerkedni az Apache Spark on Azure HDInsight alkalmazással?** Lásd: [Gyorsindítás: Spark-fürt létrehozása HDInsight rendszeren, valamint mintaalkalmazások futtatása a Jupyter használatával](hdinsight-apache-spark-jupyter-spark-sql.md).

> [!NOTE]
> Az aktuális kiadással kapcsolatos ismert problémák és korlátozások listáját lásd: [Az Apache Spark ismert problémái az Azure HDInsightban](hdinsight-apache-spark-known-issues.md).
> 
> 

## <a name="why-use-spark-on-azure-hdinsight"></a>Miért válassza a Spark on Azure HDInsight alkalmazást?
Az Azure HDInsight teljes körűen felügyelt Spark szolgáltatást biztosít. A Spark on HDInsight használatának előnyei:

| Szolgáltatás | Leírás |
| --- | --- |
| A fürtök könnyen létrehozhatók |Az Azure felügyeleti portál, az Azure PowerShell vagy a HDInsight .NET SDK használatával percek alatt létrehozható egy új Spark-fürt a HDInsight platformon. Lásd: [Spark-fürt a HDInsightban – első lépések](hdinsight-apache-spark-jupyter-spark-sql.md) |
| Könnyű használat |A Spark on HDInsight-fürtök előre konfigurált Jupyter notebookokat tartalmaznak. Ezeket interaktív adatfeldolgozásra és -megjelenítésre használhatja. A Jupyter notebook URL-je: https://CLUSTERNAME.azurehdinsight.net/jupyter. Cserélje le a **CLUSTERNAME** kifejezést a Spark HDInsight-fürt nevére. |
| REST API-k |A Spark on HDInsight tartalmazza a [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server) REST API-alapú Spark-feladatkiszolgálót, amely a futó feladatok távoli elküldésére és megfigyelésére használható. |
| Az Azure Data Lake Store támogatása |A Spark on HDInsight az Azure Data Lake Store további tárterületként, vagy elsődleges tárterületként (csak HDInsight 3.5-fürtökkel) történő használatára konfigurálható. További információk a Data Lake Store-ról: [Áttekintés: Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). |
| Integráció az Azure-szolgáltatásokkal |A Spark on HDInsight az Azure Event Hubs felé összekötővel rendelkezik. Az Event Hubs használatával az ügyfelek streamelési alkalmazásokat állíthatnak elő a már a Spark részeként elérhető [Kafka](http://kafka.apache.org/) mellett. |
| Támogatás az R Serverhez |Az R Server on HDInsight Spark-fürtjei elosztott R-számítások futtatására állíthatók be a Spark-fürtre jellemző sebesség mellett. További információk: [Get started using R Server on HDInsight](hdinsight-hadoop-r-server-get-started.md) (R Server on HDInsight – első lépések). |
| IntelliJ IDEA-integráció |Az IntelliJ HDInsight beépülő modulját alkalmazások létrehozásához és egy HDInsight Spark-fürt számára történő elküldéséhez használhatja. További információk: [Use HDInsight Tools Plugin for IntelliJ IDEA to create Spark applications for HDInsight Spark Linux cluster (Spark-alkalmazások HDInsight Spark Linux-fürt számára történő létrehozása az IntelliJ IDEA HDInsight-eszközök beépülő moduljának használatával)](hdinsight-apache-spark-intellij-tool-plugin.md). |
| Egyidejű lekérdezések |A Spark on HDInsight támogatja az egyidejű lekérdezéseket. Ezzel lehetővé válik, hogy az egyetlen, illetve a több felhasználótól és alkalmazástól származó több lekérdezés ugyanazokat a fürterőforrásokat használja. |
| Gyorsítótárazás SSD meghajtókon |Az adatok gyorsítótárazása történhet a memóriában vagy a fürtcsomópontokhoz kapcsolt SSD meghajtókon is. A memóriában történő gyorsítótárazás biztosítja a legjobb lekérdezési teljesítményt, ugyanakkor drága lehet; az SSD meghajtókon történő gyorsítótárazással pedig úgy javítható hatékonyan a lekérdezési teljesítmény, hogy ahhoz nincs szükség a memória teljes adatkészletéhez igazodó méretű fürt létrehozására. |
| Integráció BI-eszközökkel |Adatelemzési célra a Spark for HDInsight olyan összekötőket biztosít a BI-eszközök számára, mint a [Power BI](http://www.powerbi.com/) és a [Tableau](http://www.tableau.com/products/desktop). |
| Előre betöltött Anaconda-könyvtárak |A HDInsight Spark-fürtjei előre telepített Anaconda-könyvtárakkal rendelkeznek. Az [Anaconda](http://docs.continuum.io/anaconda/) közel 200 könyvtárat biztosít például a Machine Learning szolgáltatáshoz, az adatok elemzéséhez vagy a megjelenítéshez. |
| Méretezhetőség |Habár a fürtben található csomópontok számát a létrehozáskor megadhatja, elképzelhető, hogy a munkaterheléshez igazodva később növelni vagy csökkenteni szeretné majd a fürt méretét. A HDInsight-fürtök esetében lehetőség van a fürtben található csomópontok számának megváltoztatására. A Spark-fürtök emellett adatvesztés nélkül törölhetők, mivel az összes adat tárolása az Azure Blob Storage szolgáltatásban történik. |
| Napi&24; órás támogatás a hét minden napján |A Spark on HDInsight a hét minden napján napi 24 órában teljes körű vállalati szintű támogatást és szolgáltatásiszint-szerződésben garantált 99,9%-os üzemidőt biztosít. |

## <a name="what-are-the-use-cases-for-spark-on-hdinsight"></a>Melyek a Spark on HDInsight használati esetei?
Az Apache Spark on HDInsight az alábbi főbb forgatókönyvek megvalósítását teszi lehetővé.

### <a name="interactive-data-analysis-and-bi"></a>Interaktív adatelemzés és BI
[Oktatóanyag megtekintése](hdinsight-apache-spark-use-bi-tools.md)

Az Apache Spark on HDInsight az adattárolást Azure-blobokban végzi. Az üzleti szakértők és a legfontosabb döntéshozók ezen adatok alapján elemzéseket végezhetnek és jelentéseket állíthatnak elő, illetve a Microsoft Power BI használatával az elemzett adatokból interaktív jelentéseket készíthetnek. Az elemzők az Azure Storage-ban található strukturálatlan/részben strukturált adatokból kiindulva, notebookok használatával sémát adhatnak meg, majd a Microsoft Power BI használatával adatmodelleket építhetnek fel. A Spark on HDInsight számos olyan külső BI-eszközt is támogat, mint a Tableau, a Qlikview és az SAP Lumira, és ezzel ideális platform az adatelemzők, az üzleti szakértők és a fő döntéshozók számára.

### <a name="iterative-machine-learning"></a>Iteratív Machine Learning
[Oktatóanyag megtekintése: Épület-hőmérsékletek előrejelzése HVAC-adatok használatával](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

[Oktatóanyag megtekintése: Élelmiszervizsgálati eredmények előrejelzése](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

Az Apache Spark rendelkezik a Sparkra épülő [MLlib](http://spark.apache.org/mllib/) Machine Learning-könyvtárral is. Ezenfelül a Spark on HDInsight része a számos Machine Learning-csomaggal rendelkező Python elosztási rendszer, az Anaconda is. Mindezeknek és a Jupyter notebookok beépített támogatásának köszönhetően egy csúcskategóriás környezet áll rendelkezésére Machine Learning-alkalmazások létrehozására.  

### <a name="streaming-and-real-time-data-analysis"></a>Streamelés és valós idejű adatelemzés
[Oktatóanyag megtekintése](hdinsight-apache-spark-eventhub-streaming.md)

A valós idejű adatelemzés a forgatókönyvek egész skáláját kiszolgálja, kezdve az azonnali adatfeldolgozás műveleti időt csökkentő képességétől a valódi streamelési megoldások kiépítéséig. A Spark on HDInsight széles körű támogatást biztosít a valós idejű elemzési megoldások kiépítéséhez. Amíg a Spark számos adatforrást (például Kafka, Flume, Twitter, ZeroMQ vagy TCP-szoftvercsatornák) támogató összekötőkkel rendelkezik, a Spark on HDInsight az Azure Event Hubs eseményközpontokból származó adatok magas szintű feldolgozását is támogatja. Az Event Hubs az Azure legnépszerűbb várólista-szolgáltatása. Az azonnal használható Event Hubs-támogatás a Spark on HDInsightot ideális platformmá teszi a valós idejű elemzési folyamatok kiépítéséhez.

## <a name="next-steps"></a>Milyen összetevők találhatóak egy Spark-fürtben?
A Spark on HDInsight az alábbi, a fürtökön alapértelmezés szerint elérhető összetevőkből áll.

* [Spark mag](https://spark.apache.org/docs/1.5.1/). A következőket tartalmazza: Spark mag, Spark SQL, Spark streamelési API-k, GraphX és MLlib.
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter Notebook](https://jupyter.org)

A Spark on HDInsight [ODBC-illesztőt](http://go.microsoft.com/fwlink/?LinkId=616229) is biztosít a Spark-fürtökhöz történő kapcsolódáshoz a HDInsight felületén olyan BI-eszközökből, mint a Microsoft Power BI és a Tableau.

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?
Kezdjük Spark-fürt létrehozásával HDInsight Linux platformon. Lásd: [Gyorsindítás: Spark-fürt létrehozása HDInsight Linux rendszeren, valamint mintaalkalmazások futtatása a Jupyter használatával](hdinsight-apache-spark-jupyter-spark-sql.md). 

## <a name="next-steps"></a>Következő lépések
### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](hdinsight-apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: A Spark on HDInsight használata valós idejű streamelési alkalmazások összeállítására](hdinsight-apache-spark-eventhub-streaming.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](hdinsight-apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Külső csomagok használata Jupyter notebookokkal](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](hdinsight-apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](hdinsight-apache-spark-job-debugging.md)

[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

