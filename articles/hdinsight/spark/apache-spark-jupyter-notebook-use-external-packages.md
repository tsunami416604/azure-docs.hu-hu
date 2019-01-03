---
title: Egyéni Maven-csomagok használata Jupyterrel, a Spark on Azure HDInsight
description: Lépésenkénti útmutató HDInsight Spark-fürtökkel elérhető Jupyter-notebookok konfigurálása egyéni Maven-csomagok használata.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: hrasheed
ms.openlocfilehash: c0394025fef15b987f0777d157c465e83d860601
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53652132"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Külső csomagok használata Jupyter notebookok a HDInsight az Apache Spark-fürtök
> [!div class="op_single_selector"]
> * [Használatával](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Szkriptműveletek használatával](apache-spark-python-package-installation.md)

Ismerje meg, hogyan konfigurálhatja a [Jupyter Notebook](https://jupyter.org/) külső használatára a HDInsight az Apache Spark-fürt, a Közösség által biztosított Apache **maven** csomagokat, amelyek nem tartalmazza a fürt-a-beépített. 

Kereshet a [Maven tárházból](https://search.maven.org/) a teljes listát az elérhető csomagokat. Elérhető csomagok listáját a más forrásokból is beszerezheti. Például Közösség által biztosított csomagok teljes listája megtalálható [Spark csomagok](https://spark-packages.org/).

Ebben a cikkben, megtudhatja, hogyan használható a [spark-fürt megosztott kötetei szolgáltatás](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) a Jupyter notebookkal csomagot.

## <a name="prerequisites"></a>Előfeltételek
Az alábbiakkal kell rendelkeznie:

* Apache Spark-fürt megléte a HDInsightban. További útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsightban](apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Külső csomagok használata Jupyter notebookokkal
1. Az [Azure portál](https://portal.azure.com/) kezdőpultján kattintson a Spark-fürthöz tartozó csempére (ha rögzítette azt a kezdőpulton). A fürtöt a következő helyről is megkeresheti: **Browse All (Összes tallózása)** > **HDInsight Clusters** (HDInsight-fürtök).   

1. A Spark-fürt panelén kattintson a **Quick Links** (Gyorshivatkozások) lehetőségre, majd a **Cluster Dashboard** (Fürt irányítópultja) panelen a **Jupyter Notebook** elemre. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

    > [!NOTE]  
    > A fürthöz tartozó Jupyter notebookot az alábbi URL-cím böngészőben történő megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Hozzon létre új notebookot. Kattintson a **új**, és kattintson a **Spark**.
   
    ![Új Jupyter notebook létrehozása](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Új Jupyter notebook létrehozása")

1. Az új notebook létrejött, és Untitled.pynb néven nyílt meg. A felső részen kattintson a notebook nevére, és adjon meg egy könnyen megjegyezhető nevet.
   
    ![Adjon nevet a notebooknak](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Adjon nevet a notebooknak")

1. Használhatja a `%%configure` Magic Quadrant, konfigurálja a notebookot úgy, hogy egy külső csomagot használja. Külső csomagok használó jegyzetfüzetekben, ellenőrizze, hogy hívja a `%%configure` magic a az első kódcella. Ez biztosítja, hogy a kernel a csomag használatára, a munkamenet kezdete előtt van konfigurálva.

    >[!IMPORTANT]  
    >Ha elfelejti a kernel konfigurálása az első olyan cellára, használhatja a `%%configure` együtt a `-f` paraméter, de, amely újraindítja a munkamenetet, és folyamatban lévő elvész.

    | HDInsight-verzió | Parancs |
    |-------------------|---------|
    |A HDInsight 3.3-as és a HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | A HDInsight 3.5-ös és a HDInsight 3.6-os | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

1. A fenti kódrészletben a maven-koordinátái a Maven központi tárházból külső csomag vár. Ebben a kódrészletben `com.databricks:spark-csv_2.10:1.4.0` van a maven-koordináta az **spark-fürt megosztott kötetei szolgáltatás** csomagot. Itt látható, hogyan hozhat létre egy csomag koordinátáit.
   
    a. Keresse meg a csomagot a Maven tárházból. Ebben az oktatóanyagban használjuk [spark-fürt megosztott kötetei szolgáltatás](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. Gyűjtse össze a tárházban, a tartozó értékeket **GroupId**, **ArtifactId**, és **verzió**. Győződjön meg arról, hogy beszerezte az értékek egyeznek-e a fürt. Ebben az esetben egy Scala 2.10 és Spark 1.4.0-s csomagot használjuk, de előfordulhat, hogy ki kell választania a különböző verzióit a megfelelő Scala és Spark-verzió a fürtben. Talál a Scala-verziót a fürtön futtatásával `scala.util.Properties.versionString` a Spark Jupyter kernel, illetve Spark-submit. Keresheti meg, a Spark-verziót a fürtön futó `sc.version` Jupyter notebookokban.
   
    ![Külső csomagok használata Jupyter notebook](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "külső csomagok használata Jupyter notebook")
   
    c. Fűz össze adatokat a három, egymástól kettősponttal (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

1. Az a kódcella futtatásához a `%%configure` Magic Quadrant. Ez konfigurálja a mögöttes Livy-munkamenet a megadott csomag. A későbbi cellák a notebookot most már használhatja a csomag alább látható módon.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    A HDInsight 3.6-os az alábbi kódrészletet használjon.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Például a kódrészletek, majd futtathatja alább látható módon, az előző lépésben létrehozott adathalmazból az adatok megtekintéséhez.
   
        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>Lásd még:
* [Áttekintés: Az Apache Spark on Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Az Apache Spark és BI: Spark on HDInsight használatával, BI-eszközökkel interaktív adatelemzés végrehajtása](apache-spark-use-bi-tools.md)
* [Az Apache Spark és Machine Learning: A Spark használata a HDInsight HVAC-adatok épület-hőmérséklet elemzésére](apache-spark-ipython-notebook-machine-learning.md)
* [Az Apache Spark és Machine Learning: A HDInsight Spark használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a HDInsight az Apache Spark használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása egy Apache Spark-fürtön az Apache Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Külső python-csomagok használata Jupyter notebookokkal az Apache Spark-fürtök HDInsight Linux rendszeren](apache-spark-python-package-installation.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az Apache Spark-alkalmazások távoli hibakeresése az IntelliJ IDEA HDInsight-eszközei beépülő használata](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Az Apache Zeppelin notebookok használata a HDInsight Apache Spark-fürt](apache-spark-zeppelin-notebook.md)
* [Notebookokhoz elérhető kernelek Jupyter a HDInsight az Apache Spark-fürt](apache-spark-jupyter-notebook-kernels.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
