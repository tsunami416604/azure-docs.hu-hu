---
title: Adatok elemzése az Azure Data Lake Storage Gen1 Apache Spark használatával
description: Az Azure Data Lake Storage Gen1 tárolt adatok elemzése a Spark-feladatok futtatása
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.openlocfilehash: 97a9d688eaa607df9677b6e1e2e3759cbe53bd5c
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122537"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>A Data Lake Storage Gen1 adatok elemzése a HDInsight Spark-fürt használatával

Ebben az oktatóanyagban használja [Jupyter Notebook](https://jupyter.org/) egy feladatot, amely adatokat olvas be egy Data Lake-tárfiókra futtatása HDInsight Spark-fürtön elérhető.

## <a name="prerequisites"></a>Előfeltételek

* Az Azure Data Lake Storage Gen1 fiók. Kövesse az utasításokat, [Azure Data Lake Storage Gen1 használatának első lépései az Azure portal használatával](../../data-lake-store/data-lake-store-get-started-portal.md).

* Az Azure HDInsight Spark-fürt a Data Lake Storage Gen1 tárolóként. Kövesse az utasításokat, [a rövid útmutató: A HDInsight-fürtök beállítása](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    
## <a name="prepare-the-data"></a>Az adatok előkészítése

> [!NOTE]  
> Nem kell végrehajtani ezt a lépést, ha a HDInsight-fürt a Data Lake Storage alapértelmezett tárolóként hozott létre. A Fürtlétrehozási folyamat néhány adatot a Data Lake Storage-fiókban a fürt létrehozásakor megadott hozzáadja. Folytassa a szakasz használata a HDInsight Spark-fürt a Data Lake Storage.

Ha során létrehozott egy HDInsight-fürt a Data Lake Storage további tárterületet, és az alapértelmezett tároló Azure Storage-Blobból, meg kell először másolja át néhány adatot a Data Lake Storage-fiókba. A minta az Azure Storage-Blobokból származó adatokat a HDInsight-fürthöz társított is használhatja. Használhatja a [ADLCopy eszköz](https://aka.ms/downloadadlcopy) ennek a végrehajtására. Töltse le és telepítse az eszközt a hivatkozásból.

1. Nyisson meg egy parancssort, és navigáljon ahhoz a könyvtárhoz, ahol az AdlCopy telepítve van, általában `%HOMEPATH%\Documents\adlcopy`.

2. Futtassa a következő parancsot egy adott blobot másol a forrás-tároló a Data Lake Storage:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Másolás a **HVAC.csv** minta adatfájl található **/HdiSamples/HdiSamples/SensorSampleDatahvac/** az Azure Data Lake Storage-fiókba. A kódtöredék hasonlóan kell kinéznie:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > Győződjön meg arról, hogy a fájl elérési útja és neve a megfelelő kis-és nagybetűk.

3. Adja meg a hitelesítő adatait az Azure-előfizetés alapján, amelyeket a Data Lake-tárfiókra van kéri. Az alábbi kódrészlethez hasonló kimenet jelenik meg:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Az adatfájl (**HVAC.csv**) gyermekmappát másolandó **/hvac** a Data Lake Storage-fiókban.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Egy HDInsight Spark-fürt használata a Data Lake Storage Gen1

1. Az a [az Azure Portal](https://portal.azure.com/), kezdőpultján kattintson az Apache Spark-fürthöz tartozó csempére (ha rögzítette azt a kezdőpulton). A fürtöt a következő helyről is megkeresheti: **Browse All (Összes tallózása)** > **HDInsight Clusters** (HDInsight-fürtök).

2. A Spark-fürt panelén kattintson a **Quick Links** (Gyorshivatkozások) lehetőségre, majd a **Cluster Dashboard** (Fürt irányítópultja) panelen a **Jupyter Notebook** elemre. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

   > [!NOTE]  
   > A fürthöz tartozó Jupyter notebookot az alábbi URL-cím böngészőben történő megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Hozzon létre új notebookot. Kattintson a **New** (Új), majd a **PySpark** elemre.

    ![Új Jupyter notebook létrehozása](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Új Jupyter notebook létrehozása")

4. Mivel a notebook PySpark kernel használatával jött létre, explicit módon semmilyen tartalmat nem kell létrehozni. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek. Ennek az első lépése a jelen forgatókönyvhöz szükséges típusok importálása. Ehhez illessze be a következő kódrészletet a cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt.

        from pyspark.sql.types import *

    Minden alkalommal, amikor a Jupyterben feladatot futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik. A feladat befejezését követően ez a jel üres körre változik.

     ![A Jupyter notebook feladat állapota](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "A Jupyter notebook feladat állapota")

5. Mintaadatok betöltése az egy ideiglenes táblát használ a **HVAC.csv** a Data Lake Storage Gen1 fiók másolt fájl. Elérheti az adatokat a Data Lake Storage-fiókban a következő URL-minta használatával.

   * Ha a Data Lake Storage Gen1 alapértelmezett tárolóként, HVAC.csv lesz hasonló, a következő URL-elérési úton:

           adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

       Vagy, például a következő akkor használhatja rövidített formátumban is használhatja:

           adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

   * Ha a Data Lake Storage kiegészítő tárolóként, HVAC.csv lesz a helyen, ahová másolta, például:

           adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     Egy üres cellába, illessze be az alábbi példakód, cserélje le **MYDATALAKESTORE** a Data Lake Storage-fiók nevét, és nyomja le az **SHIFT + ENTER**. Ez a kódpélda az adatokat a **hvac** nevű ideiglenes táblába regisztrálja.

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

6. Mivel PySpark kernelt használ, most közvetlenül futtathat SQL-lekérdezést az imént létrehozott **hvac** ideiglenes táblán, a `%%sql` funkció használatával. További információ a `%%sql` Magic Quadrant, valamint kernellel a PySpark kernellel elérhető egyéb funkciókkal lásd [használt az Apache Spark HDInsight-fürtök Jupyter notebookokban elérhető kernelek](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. A feladat sikeres végrehajtását követően alapértelmezés szerint az alábbi táblázatos kimenet jelenik meg.

      ![A lekérdezési eredmény táblázati kimenete](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "A lekérdezési eredmény táblázati kimenete")

     Az eredményeket egyéb megjelenítési formákban is megtekintheti. Az azonos kimenethez tartozó területgrafikon például az alábbihoz hasonlóan fog kinézni.

     ![A lekérdezési eredmény területgrafikonja](./media/apache-spark-use-with-data-lake-store/jupyter-area-output.png "A lekérdezési eredmény területgrafikonja")

8. Az alkalmazás futtatását követően állítsa le a notebookot az erőforrások felszabadítása érdekében. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ezzel leállítja és bezárja a notebookot.


## <a name="next-steps"></a>További lépések

* [Hozzon létre egy különálló Scala Apache Spark-fürtön futó alkalmazás](apache-spark-create-standalone-application.md)
* [HDInsight Tools használata az IntelliJ-hez készült Azure-eszközkészlet az Apache Spark-alkalmazások HDInsight Spark Linux-fürt létrehozása](apache-spark-intellij-tool-plugin.md)
* [Az Azure Toolkit for Eclipse Apache Spark-alkalmazások HDInsight Spark Linux-fürt létrehozása HDInsight-eszközök használata](apache-spark-eclipse-tool-plugin.md)
* [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtök](../hdinsight-hadoop-use-data-lake-storage-gen2.md)
