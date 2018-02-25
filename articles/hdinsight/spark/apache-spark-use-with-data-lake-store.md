---
title: "Adatok elemzése az Azure Data Lake Store az Apache Spark használatával |} Microsoft Docs"
description: "Azure Data Lake Store-ban tárolt adatok elemzése a Spark-feladatok futtatása"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1f174323-c17b-428c-903d-04f0e272784c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 6e44ffc26bbb618e0ff14d412ed77e4827232b42
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-store"></a>A Data Lake Store adatok elemzése a HDInsight Spark-fürt használatával

Ebben az oktatóanyagban Jupyter notebook érhető el a HDInsight Spark-fürtjei futtatásához használt egy feladatot, amely egy Data Lake Store-fiók olvassa be az adatokat.

## <a name="prerequisites"></a>Előfeltételek

* Azure Data Lake Store-fiók. Kövesse [Az Azure Data Lake Store használatának első lépései az Azure Portal használatával](../../data-lake-store/data-lake-store-get-started-portal.md) című témakör utasításait.

* Az Azure HDInsight Spark-fürt a Data Lake Store tárolóként Kövesse az utasításokat, [HDInsight-fürtök létrehozása az Azure-portál használatával a Data Lake Store](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

    
## <a name="prepare-the-data"></a>Adatok előkészítése

> [!NOTE]
> Nem kell végrehajtania ezt a lépést, ha a Data Lake Store alapértelmezett tárolóként hozott létre a HDInsight-fürthöz. A Fürtlétrehozási folyamat néhány adatot hozzáadja a fürt létrehozásakor megadott Data Lake Store-fiókban. Váltson át a szakasz [használata a HDInsight Spark-fürt a Data Lake Store](#use-an-hdinsight-spark-cluster-with-data-lake-store).
>
>

HDInsight-fürtök Data Lake Store további tárhely és az Azure Storage-Blobba alapértelmezett tárolóként hozta létre, ha először másolja át néhány adatot a Data Lake Store-fiók. A minta a HDInsight-fürthöz kapcsolódó adatokat az Azure Storage-Blobból is használhatja. Használhatja a [ADLCopy eszköz](http://aka.ms/downloadadlcopy) ehhez. Töltse le és telepítse az eszközt a hivatkozásból.

1. Nyisson meg egy parancssort, és keresse meg azt a könyvtárat, AdlCopy futtató, általában `%HOMEPATH%\Documents\adlcopy`.

2. A következő parancsot egy adott blob átmásolja a forrás-tároló egy Data Lake Store:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Másolás a **HVAC.csv** minta adatfájl **/HdiSamples/HdiSamples/SensorSampleData/hvac/** az Azure Data Lake Store-fiókba. A kódrészletet hasonlóan kell kinéznie:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]
   > Ellenőrizze, hogy a megfelelő esetben van a fájl elérési útja és neve.
   >
   >
3. Az Azure-előfizetés alapján, amely rendelkezik a Data Lake Store-fiók a hitelesítő adatok megadását kéri. Az alábbi kódrészletben hasonló kimenetnek jelenik meg:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Az adatfájl (**HVAC.csv**) egy mappában kerülnek **/hvac** a Data Lake Store-fiókban.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-store"></a>A Data Lake Store HDInsight Spark-fürt használatára

1. Az [Azure portál](https://portal.azure.com/) kezdőpultján kattintson a Spark-fürthöz tartozó csempére (ha rögzítette azt a kezdőpulton). A fürtöt a következő helyről is megkeresheti: **Browse All (Összes tallózása)** > **HDInsight Clusters** (HDInsight-fürtök).

2. A Spark-fürt panelén kattintson a **Quick Links** (Gyorshivatkozások) lehetőségre, majd a **Cluster Dashboard** (Fürt irányítópultja) panelen a **Jupyter Notebook** elemre. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

   > [!NOTE]
   > A fürthöz tartozó Jupyter notebookot az alábbi URL-cím böngészőben történő megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Hozzon létre új notebookot. Kattintson a **New** (Új), majd a **PySpark** elemre.

    ![Új Jupyter notebook létrehozása](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Új Jupyter notebook létrehozása")

4. Mivel a notebook PySpark kernel használatával jött létre, explicit módon semmilyen tartalmat nem kell létrehozni. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek. Ennek az első lépése a jelen forgatókönyvhöz szükséges típusok importálása. Ehhez illessze be a következő kódrészletet a cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt.

        from pyspark.sql.types import *

    Minden alkalommal, amikor a Jupyterben feladatot futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik. A feladat befejezését követően ez a jel üres körre változik.

     ![A Jupyter notebook feladat állapota](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "A Jupyter notebook feladat állapota")

5. Mintaadatok betöltése az egy ideiglenes táblát használ a **HVAC.csv** fájl másolása a Data Lake Store-fiók. Az adatok a Data Lake Store-fiókot a következő URL-cím minta használatával érheti el.

    * Ha a Data Lake Store alapértelmezett tárolóként, HVAC.csv az elérési útját, a következő URL-cím lesz:

            adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

        Vagy is használhatja a rövidített formátumban, például a következőket:

            adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

    * Ha Data Lake Store további tárolóként, HVAC.csv lesz a helyen, ahol másolta, például:

            adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     A cella üres, illessze be a következő kódrészlet példa, hogy lecseréli **MYDATALAKESTORE** a Data Lake Store-fiók nevét, és nyomja le az **SHIFT + ENTER**. Ez a kódpélda az adatokat a **hvac** nevű ideiglenes táblába regisztrálja.

            # Load the data. The path below assumes Data Lake Store is default storage for the Spark cluster
            hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            # Create the schema
            hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

            # Parse the data in hvacText
            hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

            # Create a data frame
            hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

            # Register the data fram as a table to run queries against
            hvacdf.registerTempTable("hvac")

6. Mivel PySpark kernelt használ, most közvetlenül futtathat SQL-lekérdezést az imént létrehozott **hvac** ideiglenes táblán, a `%%sql` funkció használatával. A `%%sql` funkcióval, illetve a PySpark kernellel elérhető egyéb funkciókkal kapcsolatos további információkat [A Spark HDInsight-fürtökkel használt Jupyter notebookokban elérhető kernelek](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic) című részben talál.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. A feladat sikeres végrehajtását követően alapértelmezés szerint az alábbi táblázatos kimenet jelenik meg.

      ![A lekérdezési eredmény táblázati kimenete](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "A lekérdezési eredmény táblázati kimenete")

     Az eredményeket egyéb megjelenítési formákban is megtekintheti. Az azonos kimenethez tartozó területgrafikon például az alábbihoz hasonlóan fog kinézni.

     ![A lekérdezési eredmény területgrafikonja](./media/apache-spark-use-with-data-lake-store/jupyter-area-output.png "A lekérdezési eredmény területgrafikonja")

8. Az alkalmazás futtatását követően állítsa le a notebookot az erőforrások felszabadítása érdekében. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Ezzel leállítja és bezárja a notebookot.


## <a name="next-steps"></a>További lépések

* [Önálló Scala alkalmazás futtatását a Apache Spark-fürt létrehozása](apache-spark-create-standalone-application.md)
* [Azure eszköztára IntelliJ HDInsight Tools használatával Spark-alkalmazások HDInsight Spark Linux-fürt létrehozása](apache-spark-intellij-tool-plugin.md)
* [Az Eclipse Azure eszközkészlet a HDInsight Tools használatával Spark-alkalmazások HDInsight Spark Linux-fürt létrehozása](apache-spark-eclipse-tool-plugin.md)
