---
title: "Egy Azure HDInsight Spark-fürt interaktív lekérdezések futtatására |} Microsoft Docs"
description: "HDInsight Spark rövid útmutató az Apache Spark-fürtök HDInsightban történő létrehozásáról."
keywords: "spark gyorsútmutató,interaktív spark,interaktív lekérdezés,hdinsight spark,azure spark"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: nitinme
ms.openlocfilehash: ef9154b0d400ff23c53460454c886ab90e290f0c
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="run-interactive-queries-on-an-hdinsight-spark-cluster"></a>Interaktív lekérdezések futtatására egy HDInsight Spark-fürt

Ebben a cikkben segítségével Jupyter notebook interaktív Spark SQL-lekérdezések futtatása Spark-fürt. Jupyter notebook egy webböngésző-alapú alkalmazás, amely a konzol alapú interaktivitási élményének kibővíti az interneten. További információkért lásd: [a Jupyter notebook](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html).

A jelen oktatóanyag esetében használja a **PySpark** interaktív Spark SQL-lekérdezés futtatása a Jupyter notebook a kernel. A HDInsight-fürtökön Jupyter notebookok is támogatja a két más kernelek - **PySpark3** és **Spark**. További információ a kernelek, és a használatának előnyeit **PySpark**, lásd: [használata Jupyter notebook kernelek az Apache Spark hdinsight-fürtök](apache-spark-jupyter-notebook-kernels.md).

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure HDInsight Spark-fürt**. Útmutatásért lásd: [Apache Spark-fürt létrehozása az Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="create-a-jupyter-notebook-to-run-interactive-queries"></a>Interaktív lekérdezések futtatása Jupyter notebook létrehozása

Lekérdezések futtatása, amely elérhető a fürthöz rendelt tárolási alapértelmezés szerint mintaadatok használjuk. Azonban meg kell először adott adatok betöltése az Spark, a dataframe. Miután a dataframe, rajta a Jupyter notebook használatával lekérdezéseket is futtathat. Ebben a szakaszban, olvassa el:

* A Spark dataframe minta adatkészlet regisztrálásához.
* A dataframe kapcsolatos lekérdezések futtatása.

1. Nyissa meg az [Azure portált](https://portal.azure.com/). Ha rögzítette a fürtöt az irányítópulton, a fürt paneljének megnyitásához kattintson a fürt csempéjére az irányítópulton.

    Ha nem rögzítette a fürtöt az irányítópulton, a bal oldali panelen kattintson a **HDInsight-fürtök** elemre, majd a létrehozott fürtre.

3. A **Gyorshivatkozások** menüben kattintson a **Fürt irányítópultjai** lehetőségre, majd a **Jupyter Notebook** elemre. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

   ![A Jupyter notebook megnyitása interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-load-data-run-query/hdinsight-spark-start-jupyter-interactive-spark-sql-query.png "A Jupyter notebook megnyitása interaktív Spark SQL-lekérdezés futtatásához")

   > [!NOTE]
   > A fürthöz tartozó Jupyter notebookot az alábbi URL-cím böngészőben történő megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >
3. Hozzon létre egy notebookot. Kattintson a **New** (Új), majd a **PySpark** elemre.

   ![Jupyter notebook létrehozása interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-Spark-SQL-query.png "Jupyter notebook létrehozása interaktív Spark SQL-lekérdezés futtatásához")

   Az új notebook létrejött, és Untitled(Untitled.pynb) néven nyílt meg.

4. Ha a felső részen a notebook nevére kattint, megadhat egy könnyen megjegyezhető nevet.

    ![A Jupyter notebook elnevezése, amelyből interaktív Spark lekérdezést futtat](./media/apache-spark-load-data-run-query/hdinsight-spark-jupyter-notebook-name.png "A Jupyter notebook elnevezése, amelyből interaktív Spark lekérdezést futtat")

5. Illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt annak futtatásához. A kód importálja az alábbi forgatókönyvhöz szükséges típusokat:

        from pyspark.sql import *
        from pyspark.sql.types import *

    Mivel a notebook PySpark kernel használatával jött létre, explicit módon semmilyen tartalmat nem kell létrehozni. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek.

    ![Az interaktív Spark SQL-lekérdezés állapota](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Az interaktív Spark SQL-lekérdezés állapota")

    Minden alkalommal, amikor a Jupyterben interaktív lekérdezést futtat, a webböngésző ablakának címsorában **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik. A feladat befejezése után ez a jel üres körre változik.

6. A Spark-fürt betölteni az adatokat, mielőtt tudassa velünk keresse meg a pillanatképe. Ebben az oktatóanyagban használt mintaadatok érhető el az összes HDInsight Spark-fürtök a CSV-fájlként **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Az adatok egy épületet hőmérséklet változatait rögzíti. Az alábbiakban az adatok első néhány sor.

    ![Az adatok interaktív Spark SQL-lekérdezés pillanatkép](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "pillanatkép adatok interaktív Spark SQL-lekérdezés")

6. Hozzon létre egy dataframe és egy ideiglenes tábla (**hvac**) a következő kód futtatásával. Ebben az oktatóanyagban nem létrehozni az oszlopokat az ideiglenes tábla át a nyers adatok CSV képest. 

        # Create an RDD from sample data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create a schema for our data
        Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')

        # Parse the data and create a schema
        hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
        hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))
        
        # Infer the schema and create a table       
        hvacTable = sqlContext.createDataFrame(hvac)
        hvacTable.registerTempTable('hvactemptable')
        dfw = DataFrameWriter(hvacTable)
        dfw.saveAsTable('hvac')

7. A tábla létrehozása után az adatok interaktív lekérdezés futtatása a következő kódot használja.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

   Mivel PySpark kernelt használ, most közvetlenül futtathat interaktív SQL-lekérdezést az imént létrehozott **hvac** ideiglenes táblán, a `%%sql` funkció használatával. A `%%sql` funkcióval, illetve a PySpark kernellel elérhető egyéb funkciókkal kapcsolatos további információkat [A Spark HDInsight-fürtökkel használt Jupyter notebookokban elérhető kernelek](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic) című részben talál.

   Alapértelmezés szerint az alábbi táblázatos kimenet jelenik meg.

     ![Az interaktív Spark-lekérdezési eredmény táblázati kimenete](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Az interaktív Spark-lekérdezési eredmény táblázati kimenete")

    Az eredményeket egyéb megjelenítési formákban is megtekintheti. Az azonos kimenethez tartozó területgrafikon például az alábbihoz hasonlóan fog kinézni.

    ![Az interaktív Spark-lekérdezési eredmény területgrafikonja](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Az interaktív Spark-lekérdezési eredmény területgrafikonja")

9. Az alkalmazás futtatása után állítsa le a notebookot a fürt erőforrásainak felszabadítása érdekében. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre.

## <a name="next-step"></a>Következő lépés

Ebben a cikkben megtanulta, interaktív lekérdezések futtatása Spark Jupyter notebook használatával. A következő cikk megjelenítéséhez, hogyan lehet-e a Spark regisztrált adatok lekért olyan BI analytics eszközt, például a Power bi-ban és a Tableau továbblépés. 

> [!div class="nextstepaction"]
>[Adatok képi megjelenítés eszközökkel rendelkező Azure HDInsight Spark BI](apache-spark-use-bi-tools.md)




