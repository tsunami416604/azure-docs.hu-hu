---
title: "Az Apache Spark-fürt használatának első lépései az Azure HDInsightban | Microsoft Docs"
description: "Részletes útmutatók Apache Spark-fürt gyors létrehozásához a HDInsightban, majd a Jupyter notebookokból származó Spark SQL használata interaktív lekérdezések futtatására."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 91f41e6a-d463-4eb4-83ef-7bbb1f4556cc
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/13/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: f43a08a7096476fea79884163f89441e7e0c34cf
ms.lasthandoff: 03/17/2017


---
# <a name="get-started-create-apache-spark-cluster-in-azure-hdinsight-and-run-interactive-queries-using-spark-sql"></a>Első lépések: Apache Spark-fürt létrehozása Azure HDInsight rendszeren és interaktív lekérdezések futtatása Spark SQL használatával

Útmutató [Apache Spark](hdinsight-apache-spark-overview.md)-fürt gyors létrehozásához a HDInsightban, majd [Jupyter](https://jupyter.org) notebook használatához Spark SQL interaktív lekérdezések futtatására a Spark-fürtön.

   ![Ismerkedés az Apache Spark in HDInsight használatával](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png "Ismerkedés az Apache Spark in HDInsight használatával oktatóanyag. Szemléltetett lépések: tárfiók létrehozása; fürt létrehozása; Spark SQL-utasítások futtatása")

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Az oktatóanyag elindításához Azure-előfizetéssel kell rendelkeznie. Lásd: [Ingyenes Azure-fiók létrehozása még ma](https://azure.microsoft.com/free).

## <a name="create-a-spark-cluster"></a>Spark-fürt létrehozása
Ebben a szakaszban egy Spark-fürtöt hozhat létre a HDInsightban egy [Azure Resource Manager-sablonnal](https://azure.microsoft.com/resources/templates/101-hdinsight-spark-linux/). Egyéb fürtlétrehozási módszerek: [HDInsight-fürtök létrehozása](hdinsight-hadoop-provision-linux-clusters.md).

1. Az alábbi képre kattintva megnyithatja a sablont az Azure Portalon.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-spark-linux%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-jupyter-spark-sql/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Írja be a következő értékeket:

    ![Spark-fürt létrehozása a HDInsightban egy Azure Resource Manager-sablonnal](./media/hdinsight-apache-spark-jupyter-spark-sql/create-spark-cluster-in-hdinsight-using-azure-resource-manager-template.png "Spark-fürt létrehozása a HDInsightban egy Azure Resource Manager-sablonnal").

    * **Előfizetés**: Válassza ki a fürthöz használni kívánt Azure-előfizetést.
    * **Erőforráscsoport**: Hozzon létre egy erőforráscsoportot, vagy válasszon ki egy meglévőt. Az erőforráscsoport kezeli a projektek Azure-erőforrásait.
    * **Hely**: Válasszon egy helyet az erőforráscsoportnak.  A rendszer ezt a helyet használja az alapértelmezett fürttárolóhoz és a HDInsight-fürthöz is.
    * **Fürt neve**: Adjon nevet a létrehozni kívánt Hadoop-fürtnek.
    * **Spark-verzió**Válassza ki a Spark-verziót, amelyet telepíteni kíván a fürtön.
    * **A fürt bejelentkezési neve és jelszava**: Az alapértelmezett bejelentkezési név az admin.
    * **SSH-felhasználónév és -jelszó**.

   Jegyezze fel ezeket az értékeket.  Az oktatóanyag későbbi részében szüksége lesz rájuk.

3. Jelölje be az **Elfogadom a fenti feltételeket** és a **Rögzítés az irányítópulton** lehetőséget, majd kattintson a **Vásárlás** elemre. Ekkor egy új csempe jelenik meg Submitting deployment for Template deployment (Üzemelő példány elküldése sablon üzemelő példányhoz) címmel. A fürt létrehozása nagyjából 20 percet vesz igénybe.

> [!NOTE]
> A cikkben leírt eljárás létrehoz egy Spark-fürtöt, amely [Azure Storage-blobokat használ fürttárolóként](hdinsight-hadoop-use-blob-storage.md). Olyan Spark-fürt is létrehozható, amely az alapértelmezett tárolóként használt Azure Storage-blobok mellett az [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) szolgáltatást is használja további tárolóként. Útmutatás: [Create an HDInsight cluster with Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md) (HDInsight-fürt létrehozása a Data Lake Store-ral).
>
>

## <a name="run-a-spark-sql-query"></a>Spark SQL-lekérdezés futtatása

Ebben a szakaszban Jupyter notebookot fog használni Spark SQL-lekérdezések futtatásához a Spark-fürtön. A HDInsight Spark-fürtjei három kernelt biztosítanak a Jupyter notebookkal történő használathoz. Ezek a következők:

* **PySpark** (Pythonban írt alkalmazások esetén)
* **PySpark3** (Python3-ban írt alkalmazások esetén)
* **Spark** (Scalában írt alkalmazások esetén)

Ebben a cikkben a **PySpark** kernelt fogja használni. A kernelekkel kapcsolatos további információkért lásd: [Jupyter notebookkernelek használata Apache Spark-fürtökkel a HDInsightban](hdinsight-apache-spark-jupyter-notebook-kernels.md). A PySpark kernel használatának néhány fontosabb előnye:

* A Spark- és Hive-környezetek automatikusan be vannak állítva.
* Az SQL- vagy Hive-lekérdezések közvetlen, megelőző kódrészletek nélkül történő futtatásához olyan cellafunkciókat használhat, mint például a `%%sql`.
* Az SQL- vagy Hive-lekérdezések kimenetének megjelenítése automatikusan történik.

### <a name="create-jupyter-notebook-with-pyspark-kernel"></a>Jupyter notebook létrehozása PySpark kernellel

1. Nyissa meg az [Azure portált](https://portal.azure.com/).

2. Ha rögzítette a fürtöt az irányítópulton, a fürt paneljének megnyitásához kattintson a fürt csempéjére az irányítópulton.

    Ha nem rögzítette a fürtöt az irányítópulton, a bal oldali panelen kattintson a **HDInsight-fürtök** elemre, majd a létrehozott fürtre.

3. A **Gyorshivatkozások** menüben kattintson a **Fürt irányítópultjai** lehetőségre, majd a **Jupyter Notebook** elemre. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

   ![A HDInsight-fürt irányítópultjai](./media/hdinsight-apache-spark-jupyter-spark-sql/hdinsight-azure-portal-cluster-dashboards.png "A HDInsight-fürt irányítópultjai")

   > [!NOTE]
   > A fürthöz tartozó Jupyter notebookot az alábbi URL-cím böngészőben történő megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Hozzon létre egy notebookot. Kattintson a **New** (Új), majd a **PySpark** elemre.

   ![Jupyter notebook létrehozása](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Jupyter notebook létrehozása")

   Az új notebook létrejött, és Untitled(Untitled.pynb) néven nyílt meg.

4. Ha a felső részen a notebook nevére kattint, megadhat egy könnyen megjegyezhető nevet.

    ![Adjon nevet a notebooknak](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Adjon nevet a notebooknak")

5. Illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt annak végrehajtásához. A kód importálja az alábbi forgatókönyvhöz szükséges típusokat:

        from pyspark.sql.types import *

    Mivel a notebook PySpark kernel használatával jött létre, explicit módon semmilyen tartalmat nem kell létrehozni. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek.

    ![A Jupyter notebook feladat állapota](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "A Jupyter notebook feladat állapota")

    Minden alkalommal, amikor a Jupyterben feladatot futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik. A feladat befejezése után ez a jel üres körre változik.

6. Regisztráljon egy minta-adatkészletet ideiglenes táblaként (**hvac**) a következő kód futtatásával.

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

        # Register the data fram as a table to run queries against
        hvacdf.registerTempTable("hvac")

    A Spark-fürtök a HDInsightban tartalmaznak egy mintaadat-fájlt (**hvac.csv**) a **\HdiSamples\HdiSamples\SensorSampleData\hvac** mappában.

7. Az adatok lekérdezéséhez futtassa a következő kódot.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Mivel PySpark kernelt használ, most közvetlenül futtathat SQL-lekérdezést az imént létrehozott **hvac** ideiglenes táblán, a `%%sql` funkció használatával. A `%%sql` funkcióval, illetve a PySpark kernellel elérhető egyéb funkciókkal kapcsolatos további információkat [A Spark HDInsight-fürtökkel használt Jupyter notebookokban elérhető kernelek](hdinsight-apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic) című részben talál.

   Alapértelmezés szerint az alábbi táblázatos kimenet jelenik meg.

     ![A lekérdezési eredmény táblázati kimenete](./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "A lekérdezési eredmény táblázati kimenete")

    Az eredményeket egyéb megjelenítési formákban is megtekintheti. Az azonos kimenethez tartozó területgrafikon például az alábbihoz hasonlóan fog kinézni.

    ![A lekérdezési eredmény területgrafikonja](./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "A lekérdezési eredmény területgrafikonja")

9. Az alkalmazás futtatása után állítsa le a notebookot a fürt erőforrásainak felszabadítása érdekében. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre.

## <a name="troubleshoot"></a>Hibaelhárítás

Az alábbi szakasz a HDInsight-fürtök használatakor felmerülő gyakori problémákat ismerteti.

### <a name="access-control-requirements"></a>A hozzáférés-vezérlésre vonatkozó követelmények
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="delete-the-cluster"></a>A fürt törlése
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="see-also"></a>Lásd még:
* [Overview: Apache Spark on Azure HDInsight (Áttekintés: Apache Spark on Azure HDInsight)](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Forgatókönyvek
* [Spark és BI: Interaktív adatelemzés végrehajtása a Spark on HDInsight használatával, BI-eszközökkel](hdinsight-apache-spark-use-bi-tools.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az épület-hőmérséklet elemzésére HVAC-adatok alapján](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark és Machine Learning: A Spark on HDInsight használata az élelmiszervizsgálati eredmények előrejelzésére](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark Streaming: A Spark on HDInsight használata valós idejű streamelési alkalmazások összeállítására](hdinsight-apache-spark-eventhub-streaming.md)
* [A webhelynapló elemzése a Spark on HDInsight használatával](hdinsight-apache-spark-custom-library-website-log-analysis.md)
* [Az Application Insights telemetriai adatainak elemzése a Spark on HDInsight használatával](hdinsight-spark-analyze-application-insight-logs.md)

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

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

