---
title: Egyéni Maven-csomagok használata a Jupyter rel a Sparkban – Azure HDInsight
description: A HDInsight Spark-fürtökkel elérhető Jupyter-jegyzetfüzetek konfigurálása az egyéni Maven-csomagok használatának lépésenkénti útmutatója.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: cec94b2ecb18bc9e8cceb24a21967a3c829d78a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74561734"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Külső csomagok használata Jupyter-jegyzetfüzetekkel az Apache Spark-fürtökben a HDInsighton

> [!div class="op_single_selector"]
> * [A sejtmágia használata](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Parancsfájl-művelet használata](apache-spark-python-package-installation.md)

Ismerje meg, hogyan konfigurálhat egy [Jupyter notebookot](https://jupyter.org/) a HDInsight-alapú Apache Spark-fürtben olyan külső, közösség által biztosított Apache **maven csomagok használatára,** amelyek nem szerepelnek a fürtbeépített csomagjaiban.

A [Maven-tárházban](https://search.maven.org/) megkeresheti a rendelkezésre álló csomagok teljes listáját. Más forrásokból is beszerezheti a rendelkezésre álló csomagok listáját. Például a közösség által készített csomagok teljes listája elérhető a [Spark-csomagokban.](https://spark-packages.org/)

Ebben a cikkben megtudhatja, hogyan használhatja a [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) csomagot a Jupyter notebook.

## <a name="prerequisites"></a>Előfeltételek

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

* A Jupyter-notebookok és a HDInsighton futó Spark használatának ismerete. További információ: [Adatok betöltése és lekérdezések futtatása az Apache Spark segítségével a HDInsight on.](./apache-spark-load-data-run-query.md)

* A fürtök [elsődleges tárolóURI-séma.](../hdinsight-hadoop-linux-information.md#URI-and-scheme) Ez az `wasb://` Azure Storage, `abfs://` az Azure Data `adl://` Lake Storage Gen2 vagy az Azure Data Lake Storage Gen1 esetében lesz. Ha az Azure Storage vagy a Data Lake Storage Gen2 biztonságos átvitele engedélyezve van, az URI a biztonságos átvitel lesz, `wasbs://` illetve `abfss://`a Lásd még: biztonságos [átvitel.](../../storage/common/storage-require-secure-transfer.md)

## <a name="use-external-packages-with-jupyter-notebooks"></a>Külső csomagok használata Jupyter notebookokkal

1. Keresse `https://CLUSTERNAME.azurehdinsight.net/jupyter` meg, hogy hol `CLUSTERNAME` van a Spark-fürt neve.

1. Hozzon létre új notebookot. Válassza az **Új**lehetőséget, majd a **Spark**lehetőséget.

    ![Új Spark Jupyter-jegyzetfüzet létrehozása](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Új Jupyter notebook létrehozása")

1. Az új notebook létrejött, és Untitled.pynb néven nyílt meg. Jelölje ki a jegyzetfüzet nevét a tetején, és adjon meg egy rövid nevet.

    ![A notebook elnevezése](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "A notebook elnevezése")

1. A `%%configure` mágiával konfigurálhatja a notebookot egy külső csomag használatára. A külső csomagokat használó jegyzetfüzetekben győződjön `%%configure` meg arról, hogy az első kódcellában hívja a varázslatot. Ez biztosítja, hogy a rendszermag úgy legyen beállítva, hogy a munkamenet megkezdése előtt használja a csomagot.

    >[!IMPORTANT]  
    >Ha elfelejtjük beállítani a rendszermagot az `%%configure` első `-f` cellában, használhatjuk a paramétert, de ez újraindítja a munkamenetet, és minden folyamat elvész.

    | HDInsight verzió | Parancs |
    |-------------------|---------|
    | HDInsight 3.5 és HDInsight 3.6 esetén | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |HDInsight 3.3 és HDInsight 3.4 esetén | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. A fenti kódrészlet a Maven központi adattárban lévő külső csomag maven koordinátáit várja. Ebben a kódrészletben `com.databricks:spark-csv_2.11:1.5.0` a **spark-csv** csomag maven koordinátája. Így kell megalkotni egy csomag koordinátáit.

    a. Keresse meg a csomagot a Maven repositoryban. Ehhez a cikkhez [szikra-csv-t](https://mvnrepository.com/artifact/com.databricks/spark-csv)használunk.

    b. A tárházból gyűjtse össze a **GroupId**, **ArtifactId**és **Version**értékét. Győződjön meg arról, hogy az összegyűjtött értékek megegyeznek a fürttel. Ebben az esetben egy Scala 2.11 és Spark 1.5.0 csomagot használunk, de előfordulhat, hogy különböző verziókat kell kiválasztania a megfelelő Scala vagy Spark-verzióhoz a fürtben. A Scala-verziót a fürtön `scala.util.Properties.versionString` a Spark Jupyter kernelen vagy a Spark submit-on futtatva találhatja meg. A Spark-verzió a fürtön jupyter-jegyzetfüzetek futtatásával. `sc.version`

    ![Külső csomagok használata a Jupyter notebookkal](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Külső csomagok használata a Jupyter notebookkal")

    c. A három érték összefűzése kettősponttal (**: )** elválasztva

        com.databricks:spark-csv_2.11:1.5.0

1. Futtasd le `%%configure` a kódcellát a mágiával. Ez konfigurálja az alapul szolgáló Livy munkamenetet a megadott csomag használatára. A jegyzetfüzet későbbi celláiban most már használhatja a csomagot, az alábbiak szerint.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    A HDInsight 3.4-es és az azt követő műveleteknél a következő kódrészletet kell használnia.

        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Ezután futtathatja a kódrészleteket, mint az alábbi ábrán látható, az előző lépésben létrehozott adatkeret ből származó adatok megtekintéséhez.

        df.show()
   
        df.select("Time").count()

## <a name="see-also"></a><a name="seealso"></a>Lásd még

* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek

* [Apache Spark bi-val: Interaktív adatelemzés a Spark használatával a HDInsightban az üzletiintelligencia-eszközökkel](apache-spark-use-bi-tools.md)
* [Apache Spark gépi tanulással: Használja a Sparkot a HDInsightban az épület hőmérsékletének elemzéséhez a fűtés- és légtechnikai adatok használatával](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark a Machine Learningsegítségével: A Spark használata a HDInsightban az élelmiszer-ellenőrzési eredmények előrejelzéséhez](apache-spark-machine-learning-mllib-ipython.md)
* [Webhelynapló-elemzés az Apache Spark használatával a HDInsightban](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása

* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Külső python-csomagok használata Jupyter notebookokkal az Apache Spark-fürtökben HDInsight Linuxon](apache-spark-python-package-installation.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight eszközök beépülő moduljával távolról debugelje az Apache Spark alkalmazásokat](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Apache Zeppelin-jegyzetfüzetek használata Apache Spark-fürttel a HDInsighton](apache-spark-zeppelin-notebook.md)
* [A Jupyter notebookhoz elérhető kernelek az Apache Spark-fürtHDInsighthoz](apache-spark-jupyter-notebook-kernels.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
