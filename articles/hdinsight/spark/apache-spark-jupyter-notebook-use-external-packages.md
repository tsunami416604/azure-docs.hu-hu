---
title: "Egyéni Maven-csomagok használata Jupyter a Spark on Azure HDInsight |} Microsoft Docs"
description: "Lépésenkénti útmutató HDInsight Spark-fürtjei Jupyter notebookok elérhető konfigurálása egyéni Maven-csomagok használata."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2a8bc545-064e-436f-8b5f-e67c26cfbf98
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 93b29480b51f7a664411ffa92ed07c26b2ebafca
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Külső csomagok használata Jupyter notebookok Apache Spark-fürt a HDInsight
> [!div class="op_single_selector"]
> * [Cella magic használatával](apache-spark-jupyter-notebook-use-external-packages.md)
> * [Parancsfájl művelet segítségével](apache-spark-python-package-installation.md)
>
>

Jupyter notebook beállításáról az Apache Spark-fürt a HDInsight használatához a külső, közösségi hozzájárult **maven** csomagok, amelyek nem tartalmazza a fürt, a-kész. 

Kereshet az [Maven-tárház](http://search.maven.org/) csomagok rendelkezésre álló teljes listáját. Más forrásból is megkapható elérhető csomagok listáját. Például közösségi hozzájárult csomagok teljes listája megtalálható [Spark csomagok](http://spark-packages.org/).

Ebből a cikkből megtanulhatja, hogyan használható a [spark-fürt megosztott kötetei szolgáltatás](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) a Jupyter notebookkal csomag.

## <a name="prerequisites"></a>Előfeltételek
Az alábbiakkal kell rendelkeznie:

* A HDInsight az Apache Spark-fürt. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Külső csomagok használata Jupyter notebookokkal
1. Az [Azure portál](https://portal.azure.com/) kezdőpultján kattintson a Spark-fürthöz tartozó csempére (ha rögzítette azt a kezdőpulton). A fürtöt a következő helyről is megkeresheti: **Browse All (Összes tallózása)** > **HDInsight Clusters** (HDInsight-fürtök).   

2. A Spark-fürt panelén kattintson a **Quick Links** (Gyorshivatkozások) lehetőségre, majd a **Cluster Dashboard** (Fürt irányítópultja) panelen a **Jupyter Notebook** elemre. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

    > [!NOTE]
    > A fürthöz tartozó Jupyter notebookot az alábbi URL-cím böngészőben történő megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
    > 
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
    > 

3. Hozzon létre új notebookot. Kattintson a **új**, és kattintson a **Spark**.
   
    ![Új Jupyter notebook létrehozása](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Új Jupyter notebook létrehozása")

4. Az új notebook létrejött, és Untitled.pynb néven nyílt meg. A felső részen kattintson a notebook nevére, és adjon meg egy könnyen megjegyezhető nevet.
   
    ![Adjon nevet a notebooknak](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Adjon nevet a notebooknak")

5. Használhatja a `%%configure` magic a notebook egy külső csomag konfigurálása. Külső csomagok használata jegyzetfüzetek, győződjön meg arról, meghívja a `%%configure` magic az első kódcella a. Ez biztosítja, hogy a rendszermag a munkamenet akkor kezdődik, a csomag használatára van konfigurálva.

    >[!IMPORTANT] 
    >Ha elfelejti a kernel konfigurálása az első cellába, használhatja a `%%configure` rendelkező a `-f` paraméter, de a munkamenet újraindul, és az összes folyamatban lévő elvesznek.

    | HDInsight-verzió | Parancs |
    |-------------------|---------|
    |A HDInsight 3.3-as és a HDInsight 3.4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|
    | A HDInsight 3.5-ös és a HDInsight 3.6. | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}`|

6. A fenti kódrészletet a maven koordinátáit a külső package Maven központi tárházban vár. Ezt a kódrészletet a `com.databricks:spark-csv_2.10:1.4.0` van a maven koordináta **spark-fürt megosztott kötetei szolgáltatás** csomag. Itt látható, hogyan hozható létre csomag a koordináták százalékosan.
   
    a. Keresse meg a csomagot a Maven-tárházat. Ebben az oktatóanyagban használjuk [spark-fürt megosztott kötetei szolgáltatás](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. A tárház gyűjtse össze a értékeinek **GroupId**, **artifactid szakaszát**, és **verzió**. Győződjön meg arról, hogy gyűjtse össze az értékek megegyeznek-e a fürt. Ebben az esetben a Scala 2.10 és Spark 1.4.0 csomag használjuk, de szükség lehet a fürt különböző verzióinak a megfelelő Scala vagy Spark verzió kiválasztása. Talál a Scala verzió a fürtön futó `scala.util.Properties.versionString` a Spark Jupyter kernel vagy Spark elküldése. Talál a Spark-verzió a fürtön futó `sc.version` használt Jupyter notebookokban.
   
    ![Külső csomagok használata Jupyter notebook](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "külső csomagok használata Jupyter notebook")
   
    c. A következő három érték, egymástól kettősponttal összefűzésére (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0

7. Futtassa a kód cellába, amelynek a `%%configure` magic. Ezzel az alapul szolgáló Livy munkamenet a megadott csomag konfigurálja. A notebook későbbi celláiban most már használhatja a csomag alább látható módon.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    HDInsight 3.6 a következő kódrészlettel kell használnia.

        val df = spark.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

8. Ezt követően futtathatja részletek, például alább látható módon, az előző lépésben létrehozott dataframe származó adatok megtekintéséhez.
   
        df.show()
   
        df.select("Time").count()

## <a name="seealso"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](apache-spark-machine-learning-mllib-ipython.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Alkalmazások létrehozása és futtatása
* [Önálló alkalmazás létrehozása a Scala használatával](apache-spark-create-standalone-application.md)
* [Feladatok távoli futtatása Spark-fürtön a Livy használatával](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Eszközök és bővítmények

* [Külső python-csomagok használata Jupyter notebookok Apache Spark-fürtök HDInsight Linux rendszeren](apache-spark-python-package-installation.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark Scala-alkalmazások létrehozásához és elküldéséhez](apache-spark-intellij-tool-plugin.md)
* [Az IntelliJ IDEA HDInsight-eszközei beépülő moduljának használata Spark-alkalmazások távoli hibaelhárításához](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Zeppelin notebookok használata Spark-fürttel HDInsighton](apache-spark-zeppelin-notebook.md)
* [Jupyter notebookokhoz elérhető kernelek a HDInsight Spark-fürtjében](apache-spark-jupyter-notebook-kernels.md)
* [A Jupyter telepítése a számítógépre, majd csatlakozás egy HDInsight Spark-fürthöz](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Erőforrások kezelése
* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)
