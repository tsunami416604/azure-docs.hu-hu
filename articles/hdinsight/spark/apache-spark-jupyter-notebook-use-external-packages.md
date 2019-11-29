---
title: Egyéni Maven-csomagok használata a Jupyter a Sparkban – Azure HDInsight
description: Részletes útmutató a HDInsight Spark-fürtökkel elérhető Jupyter-jegyzetfüzetek egyéni Maven-csomagok használatához való konfigurálásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: cec94b2ecb18bc9e8cceb24a21967a3c829d78a5
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561734"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Külső csomagok használata Jupyter notebookokkal Apache Spark-fürtökben a HDInsight-ben

> [!div class="op_single_selector"]
> * [A Cell Magic használata](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Parancsfájl-művelet használata](apache-spark-python-package-installation.md)

Megtudhatja, hogyan konfigurálhat egy [Jupyter Notebookt](https://jupyter.org/) Apache Spark-fürtön a HDInsight olyan külső, Közösség által támogatott Apache **Maven** -csomagok használatára, amelyek nem szerepelnek a fürtben.

A [Maven-tárházban](https://search.maven.org/) megkeresheti a rendelkezésre álló csomagok teljes listáját. Lekérheti az egyéb forrásokból származó elérhető csomagok listáját is. A közösségi hozzájárulású csomagok teljes listája például a [Spark-csomagokban](https://spark-packages.org/)érhető el.

Ebből a cikkből megtudhatja, hogyan használhatja a [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) csomagot a Jupyter notebook használatával.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

* A Jupyter-notebookok és a HDInsighton futó Spark használatának ismerete. További információ: [adatok betöltése és lekérdezések futtatása Apache Spark a HDInsight-on](./apache-spark-load-data-run-query.md).

* A fürtök elsődleges tárolójának [URI-sémája](../hdinsight-hadoop-linux-information.md#URI-and-scheme) . Ez `wasb://` az Azure Storage-hoz, `abfs://` Azure Data Lake Storage Gen2 vagy `adl://` Azure Data Lake Storage Gen1 számára. Ha a biztonságos átvitel engedélyezve van az Azure Storage vagy a Data Lake Storage Gen2 számára, akkor az URI `wasbs://` vagy `abfss://`, illetve a [biztonságos átvitel](../../storage/common/storage-require-secure-transfer.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Külső csomagok használata Jupyter notebookokkal

1. Navigáljon `https://CLUSTERNAME.azurehdinsight.net/jupyter`, ahol a `CLUSTERNAME` a Spark-fürt neve.

1. Hozzon létre új notebookot. Válassza az **új**, majd a **Spark**lehetőséget.

    ![Új Spark Jupyter-jegyzetfüzet létrehozása](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Új Jupyter notebook létrehozása")

1. Az új notebook létrejött, és Untitled.pynb néven nyílt meg. Válassza ki a jegyzetfüzet nevét a tetején, és adjon meg egy rövid nevet.

    ![Adja meg a jegyzetfüzet nevét](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "A notebook elnevezése")

1. A `%%configure` Magic használatával konfigurálhatja a jegyzetfüzetet külső csomag használatára. Külső csomagokat használó jegyzetfüzetekben ügyeljen arra, hogy az első kód cellájában meghívja a `%%configure` magict. Ez biztosítja, hogy a kernel a csomag használatára legyen konfigurálva a munkamenet elindítása előtt.

    >[!IMPORTANT]  
    >Ha elfelejti a rendszermag konfigurálását az első cellában, használhatja a `%%configure`t a `-f` paraméterrel, de a rendszer újraindítja a munkamenetet, és minden folyamat elvész.

    | HDInsight verziója | Parancs |
    |-------------------|---------|
    | HDInsight 3,5 és HDInsight 3,6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |HDInsight 3,3 és HDInsight 3,4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. A fenti kódrészlet a Maven Central adattárában lévő külső csomaghoz tartozó Maven-koordinátákat várja. Ebben a kódrészletben `com.databricks:spark-csv_2.11:1.5.0` a **Spark-CSV** csomag Maven-koordinátája. A csomagok koordinátáit a következőképpen hozhatja létre.

    a. Keresse meg a csomagot a Maven-tárházban. Ebben a cikkben a [Spark-CSV-](https://mvnrepository.com/artifact/com.databricks/spark-csv)t használjuk.

    b. A tárházból Gyűjtse össze a **GroupID**, a **ArtifactId**és a **verzió**értékeit. Győződjön meg arról, hogy az összegyűjtött értékek megfelelnek a fürtnek. Ebben az esetben a Scala 2,11 és a Spark 1.5.0 csomagot használjuk, de előfordulhat, hogy a fürt megfelelő Scala vagy Spark verziójának különböző verzióit kell kiválasztania. A Scala-verziót a fürtön a Spark Jupyter kernelen vagy a Spark beküldéskor `scala.util.Properties.versionString` futtatásával tekintheti meg. A Spark-verziót a fürtön a Jupyter-jegyzetfüzetek `sc.version` futtatásával tekintheti meg.

    ![Külső csomagok használata Jupyter notebooktal](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Külső csomagok használata Jupyter notebooktal")

    c. Összefűzi a három értéket kettősponttal elválasztva ( **:** ).

        com.databricks:spark-csv_2.11:1.5.0

1. Futtassa a kód cellát a `%%configure` Magic. Ezzel konfigurálja a mögöttes Livy-munkamenetet a megadott csomag használatára. A jegyzetfüzet következő celláiban már használhatja a csomagot az alább látható módon.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    A 3,4-es és az alábbi HDInsight az alábbi kódrészletet kell használnia.

        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Ezután az előző lépésben létrehozott dataframe származó adatok megtekintéséhez futtathatja a kódrészleteket, például alább látható.

        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>Lásd még:

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Alkalmazási helyzetek

* [Apache Spark BI: interaktív adatelemzés végrehajtása a Spark on HDInsight és a BI Tools használatával](apache-spark-use-bi-tools.md)
* [Apache Spark a Machine Learning használatával: a Spark in HDInsight használata az építési hőmérséklet elemzésére a HVAC-adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learning használatával: az élelmiszer-ellenőrzési eredmények előrejelzéséhez használja a Spark in HDInsight](apache-spark-machine-learning-mllib-ipython.md)
* [Webhely-naplózási elemzés Apache Spark használatával a HDInsight-ben](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Külső Python-csomagok használata Jupyter notebookokkal Apache Spark-fürtökön a HDInsight Linux rendszeren](apache-spark-python-package-installation.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata a Apache Spark alkalmazások távoli hibakereséséhez](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin notebookok használata Apache Spark-fürttel a HDInsight-on](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek Apache Spark-fürtben HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
