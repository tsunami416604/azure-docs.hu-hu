---
title: Az Azure Data Lake Storage Gen1 elemzése a HDInsight Apache Spark segítségével
description: Apache Spark-feladatok futtatása az Azure Data Lake Storage Gen1-ben tárolt adatok elemzéséhez
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: f7a6ab954aff1bcc2e3dae3fc035db4b136ccbbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73818166"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>A DATA Lake Storage Gen1 adatainak elemzése a HDInsight Spark-fürthasználatával

Ebben a cikkben a HDInsight Spark-fürtökkel elérhető [Jupyter-jegyzetfüzetet](https://jupyter.org/) használja egy Data Lake Storage-fiókból adatokat beolvasó feladat futtatásához.

## <a name="prerequisites"></a>Előfeltételek

* Azure Data Lake Storage Gen1 fiók. Kövesse az [Azure Data Lake Storage Gen1 használatának első lépéseit az Azure Portal használatával című útmutatóban.](../../data-lake-store/data-lake-store-get-started-portal.md)

* Azure HDInsight Spark-fürt a Data Lake Storage Gen1 tárolóként. Kövesse a [rövid útmutató utasításait: Fürtök beállítása a HDInsight ban.](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)

## <a name="prepare-the-data"></a>Az adatok előkészítése

> [!NOTE]  
> Ezt a lépést nem kell végrehajtania, ha a HDInsight-fürtöt a Data Lake Storage alapértelmezett tárolóként hozta létre. A fürt létrehozási folyamat ad hozzá néhány mintaadatokat a Data Lake Storage-fiók, amely a fürt létrehozása során megadott. Ugrás a HDInsight Spark-fürt használata a Data Lake Storage szakaszra.

Ha létrehozott egy HDInsight-fürtöt a Data Lake Storage-szal további tárként és az Azure Storage Blob alapértelmezett tárolóként, először át kell másolnia néhány mintaadatot a Data Lake Storage-fiókba. Használhatja a mintaadatokat az Azure Storage Blob a HDInsight-fürthöz társított. Ehhez használhatja az [ADLCopy eszközt.](https://www.microsoft.com/download/details.aspx?id=50358) Töltse le és telepítse az eszközt a linkről.

1. Nyisson meg egy parancssort, és keresse meg azt `%HOMEPATH%\Documents\adlcopy`a könyvtárat, ahol az AdlCopy telepítve van, általában .

2. A következő parancs futtatásával másolja az adott blobot a forrástárolóból a Data Lake Storage-ba:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Másolja a **HVAC.csv** mintaadatfájlt a **/HdiSamples/HdiSamples/SensorSampleData/hvac/** könyvtárba az Azure Data Lake Storage-fiókba. A kódrészletnek a következőkre kell kinéznie:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > Győződjön meg arról, hogy a fájl- és elérésiút-nevek a megfelelő nagybetűs sé.

3. A rendszer kéri, hogy adja meg az Azure-előfizetés hitelesítő adatait, amely alatt rendelkezik a Data Lake Storage-fiókkal. Az alábbi kódrészlethez hasonló kimenet jelenik meg:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Az adatfájl (**HVAC.csv**) a Data Lake Storage-fiók **/hvac** mappájába lesz másolva.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>HDInsight Spark-fürt használata a Data Lake Storage Gen1 szolgáltatással

1. Az [Azure Portalon](https://portal.azure.com/), a startboard, kattintson a csempe az Apache Spark-fürt (ha rögzítette azt a startboard). A fürthöz az Összes > **HDInsight-fürt böngészése**csoportban is navigálhat. **Browse All**

2. A Spark-fürt panelén kattintson a **Quick Links** (Gyorshivatkozások) lehetőségre, majd a **Cluster Dashboard** (Fürt irányítópultja) panelen a **Jupyter Notebook** elemre. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

   > [!NOTE]  
   > A fürthöz tartozó Jupyter notebookot az alábbi URL-cím böngészőben történő megnyitásával is elérheti. Cserélje le a **CLUSTERNAME-t** a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Hozzon létre új notebookot. Kattintson a **New** (Új), majd a **PySpark** elemre.

    ![Új Jupyter notebook létrehozása](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Új Jupyter notebook létrehozása")

4. Mivel a notebook PySpark kernel használatával jött létre, explicit módon semmilyen tartalmat nem kell létrehozni. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek. Ennek az első lépése a jelen forgatókönyvhöz szükséges típusok importálása. Ehhez illessze be a következő kódrészletet a cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt.

        from pyspark.sql.types import *

    Minden alkalommal, amikor a Jupyterben feladatot futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik. A feladat befejezését követően ez a jel üres körre változik.

     ![A Jupyter notebook feladat állapota](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "A Jupyter notebook feladat állapota")

5. Töltse be a mintaadatokat egy ideiglenes táblába a Data Lake Storage Gen1 fiókba másolt **HVAC.csv** fájl használatával. A Data Lake Storage-fiókban lévő adatok a következő URL-minta használatával érhetők el.

   * Ha a Data Lake Storage Gen1 az alapértelmezett tároló, a HVAC.csv a következő URL-hez hasonló elérési úton lesz:

           adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

       Vagy használhat rövidített formátumot is, például a következőket:

           adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

   * Ha a Data Lake Storage további tárolóként rendelkezik, a HVAC.csv azon a helyen lesz, ahol másolta, például:

           adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     Egy üres cellába illessze be a következő kódot, például cserélje le a **MYDATALAKESTORE-t** a Data Lake Storage-fiók nevére, és nyomja le **a SHIFT + ENTER billentyűkombinációt.** Ez a kódpélda az adatokat a **hvac** nevű ideiglenes táblába regisztrálja.

           # Load the data. The path below assumes Data Lake Storage is default storage for the Spark cluster
           hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

           # Create the schema
           hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

           # Parse the data in hvacText
           hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

           # Create a data frame
           hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

           # Register the data fram as a table to run queries against
           hvacdf.registerTempTable("hvac")

6. Mivel PySpark kernelt használ, most közvetlenül futtathat SQL-lekérdezést az imént létrehozott **hvac** ideiglenes táblán, a `%%sql` funkció használatával. A mágiáról, valamint a PySpark kernelben elérhető egyéb varázslatokról a `%%sql` [Jupyter-jegyzetfüzetekben elérhető Kernelek Apache Spark HDInsight-fürtökkel című](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)témakörben talál további információt.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. A feladat sikeres végrehajtását követően alapértelmezés szerint az alábbi táblázatos kimenet jelenik meg.

      ![A lekérdezés eredményének táblázatos kimenete](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "A lekérdezés eredményének táblázatos kimenete")

     Az eredményeket egyéb megjelenítési formákban is megtekintheti. Az azonos kimenethez tartozó területgrafikon például az alábbihoz hasonlóan fog kinézni.

     ![A lekérdezés eredményének területgrafikonja](./media/apache-spark-use-with-data-lake-store/jupyter-area-output1.png "A lekérdezés eredményének területgrafikonja")

8. Az alkalmazás futtatását követően állítsa le a notebookot az erőforrások felszabadítása érdekében. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ezzel leállítja és bezárja a notebookot.


## <a name="next-steps"></a>További lépések

* [Hozzon létre egy önálló Scala-alkalmazást az Apache Spark-fürtön futtatandó](apache-spark-create-standalone-application.md)
* [Az Azure Toolkit for IntelliJ HDInsight-eszközeinek használata Apache Spark-alkalmazások létrehozásához HDInsight Spark Linux-fürthöz](apache-spark-intellij-tool-plugin.md)
* [Az Azure Toolkit for Eclipse HDInsight eszközeinek használata Apache Spark-alkalmazások létrehozásához HDInsight Spark Linux-fürthöz](apache-spark-eclipse-tool-plugin.md)
* [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
