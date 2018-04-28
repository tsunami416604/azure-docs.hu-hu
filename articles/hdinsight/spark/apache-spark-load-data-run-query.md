---
title: Egy Azure HDInsight Spark-fürt interaktív lekérdezések futtatására |} Microsoft Docs
description: HDInsight Spark rövid útmutató az Apache Spark-fürtök HDInsightban történő létrehozásáról.
keywords: spark gyorsútmutató,interaktív spark,interaktív lekérdezés,hdinsight spark,azure spark
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 11/29/2017
ms.author: jgao
ms.openlocfilehash: 177fb47c72e9abbafcda69416643fbd3848373bd
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="run-interactive-queries-on-spark-clusters-in-hdinsight"></a>A hdinsight Spark-fürtjei interaktív lekérdezések futtatására

Megtudhatja, hogyan Jupyter notebook használatához Spark SQL interaktív lekérdezések futtatásához egy Spark-fürt. 

[Jupyter notebook](http://jupyter-notebook.readthedocs.io/en/latest/notebook.html) egy webböngésző-alapú alkalmazás, amely kiterjeszti a konzol alapú interaktivitási élményének a weben. A Spark on HDInsight része a [Zeppelin notebook](apache-spark-zeppelin-notebook.md) is. Az oktatóanyag a Jupyter notebookot alkalmazza.

A Jupyter notebookok a HDInsight-fürtökön három kernelt támogatnak – **PySpark**, **PySpark3** és **Spark**. Az oktatóanyag a **PySpark** kernelt alkalmazza. A kernelekkel, valamint a **PySpark** használatának előnyeivel kapcsolatos további információkért lásd: [Jupyter notebookkernelek használata Apache Spark-fürtökkel a HDInsightban](apache-spark-jupyter-notebook-kernels.md). Zeppelin Notebook használatához tekintse meg a [használja Zeppelin notebookok az Apache Spark on Azure HDInsight fürt](./apache-spark-zeppelin-notebook.md).

Ebben az oktatóanyagban adatait kéri a csv-fájlban. Meg kell először adott adatok betöltése az Spark, a dataframe. Majd a Jupyter Notebook használatával dataframe lekérdezéseket is futtathat. 

## <a name="prerequisites"></a>Előfeltételek

* **Egy Azure HDInsight Spark-fürt**. Az utasításokért lásd: [Apache Spark-fürt létrehozása az Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Jupyter notebook PySpark használatával**. Az utasításokért lásd: [Jupyter Notebook létrehozása](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

## <a name="create-a-dataframe-from-a-csv-file"></a>Hozzon létre egy dataframe csv-fájlból

Alkalmazások egy az SQLContext egy meglévő RDD, olyan Hive táblát, vagy adatforrásokból hozhat létre dataframes. 

**Egy dataframe létrehozása a csv-fájlból**

1. Hozzon létre egy Jupyter notebook PySpark használata, ha még nem rendelkezik ilyennel. Az utasításokért lásd: [Jupyter Notebook létrehozása](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Illessze be a következő kódot a notebook a cella üres, és nyomja le az **SHIFT + ENTER** futtatja a kódot. A kód importálja az alábbi forgatókönyvhöz szükséges típusokat:

    ```PySpark
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```
    A PySpark kernel használatával hozzon létre a Spark jegyzetfüzet és Hive környezetek automatikusan létrejönnek az első kódcella futtatásakor. Nem kell explicit módon semmilyen tartalmat létrehozásához.

    Az interaktív lekérdezések futtatása a Jupyter, ha a webes böngésző ablakot vagy lapot felirat látható a **(foglalt)** állapota a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik. A feladat befejezése után ez a jel üres körre változik.

    ![Az interaktív Spark SQL-lekérdezés állapota](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Az interaktív Spark SQL-lekérdezés állapota")

3. Futtassa a következő kódot a dataframe és egy ideiglenes tábla létrehozása (**hvac**) futtassa a következő kódot: A kód nem bontsa ki az összes oszlop érhető el a CSV-fájlt. 

    ```PySpark
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
    ```
    Az alábbi képernyőfelvételen látható a HVAC.csv fájl pillanatképet. A csv-fájlt tartalmaz minden HDInsigt Spark-fürtön. Az adatok egy épületet hőmérséklet változatait rögzíti.

    ![Az adatok interaktív Spark SQL-lekérdezés pillanatkép](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "pillanatkép adatok interaktív Spark SQL-lekérdezés")

## <a name="run-queries-on-the-dataframe"></a>A dataframe kapcsolatos lekérdezések futtatása

Ha a táblázat létrejött, az interaktív lekérdezések futtathatja az adatokat.

**A lekérdezés futtatásához**

1. A cella üres, a notebook futtassa a következő kódot:

    ```PySpark
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Mivel a notebook PySpark kernel használják, akkor most közvetlenül futtathat egy interaktív SQL-lekérdezésben az ideiglenes táblán **hvac** használatával létrehozott a `%%sql` magic. A `%%sql` funkcióval, illetve a PySpark kernellel elérhető egyéb funkciókkal kapcsolatos további információkat [A Spark HDInsight-fürtökkel használt Jupyter notebookokban elérhető kernelek](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic) című részben talál.

   Alapértelmezés szerint az alábbi táblázatos kimenet jelenik meg.

     ![Az interaktív Spark-lekérdezési eredmény táblázati kimenete](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Az interaktív Spark-lekérdezési eredmény táblázati kimenete")

3. Az eredményeket egyéb megjelenítési formákban is megtekintheti. Az azonos kimenethez tartozó területgrafikon megtekintéséhez válasszon **terület** utána állítsa be más értékek látható módon.

    ![Az interaktív Spark-lekérdezési eredmény területgrafikonja](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Az interaktív Spark-lekérdezési eredmény területgrafikonja")

10. Az a **fájl** a notebook menüjében kattintson **mentése és ellenőrzőpont**. 

11. Mire az első a [következő oktatóanyagában](apache-spark-use-bi-tools.md) , hagyja a notebook megnyitva. Ha nem, állítsa le a notebook felszabadítása a fürt erőforrásait: az a **fájl** a notebook menüjében kattintson **zárja be és Halt**.

## <a name="next-step"></a>Következő lépés

Ebben a cikkben megtanulta, interaktív lekérdezések futtatása Spark Jupyter notebook használatával. A következő cikk megjelenítéséhez, hogyan lehet-e a Spark regisztrált adatok lekért olyan BI analytics eszközt, például a Power bi-ban és a Tableau továbblépés. 

> [!div class="nextstepaction"]
>[Adatok képi megjelenítés eszközökkel rendelkező Azure HDInsight Spark BI](apache-spark-use-bi-tools.md)




