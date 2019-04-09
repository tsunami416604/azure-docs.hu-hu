---
title: 'Oktatóanyag: Adatok betöltése, és lekérdezéseket futtathat az Azure HDInsight az Apache Spark-fürt '
description: Megtudhatja, hogyan tölthet be adatokat és futtathat interaktív lekérdezéseket Spark-fürtökön az Azure HDInsightban.
services: azure-hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.author: hrasheed
ms.date: 04/03/2019
ms.openlocfilehash: 18f5d34e50a4ed4ed82a3ceb4740d594ce4bd78d
ms.sourcegitcommit: b4ad15a9ffcfd07351836ffedf9692a3b5d0ac86
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59058220"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Oktatóanyag: Adatok betöltése, és lekérdezéseket futtathat az Azure HDInsight az Apache Spark-fürt

Ebből az oktatóanyagból megtudhatja, hogyan hozzon létre dataframe-csv-fájlból, és hogyan futtathat interaktív Spark SQL-lekérdezéseket az egy [Apache Spark](https://spark.apache.org/) Azure HDInsight-fürt. A Sparkban az adathalmazok olyan elosztott adatgyűjtemények, amelyek megnevezett oszlopokba vannak rendezve. Az adathalmazok elméleti szinten azonosak a relációs adatbázisokban található táblákkal vagy R/Python-adathalmazokkal.
 
Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Adathalmaz létrehozása egy CSV-fájlból
> * Lekérdezések futtatása az adathalmazon

## <a name="prerequisites"></a>Előfeltételek

* Végezze el az [Apache Spark-fürt az Azure HDInsightban történő létrehozását](apache-spark-jupyter-spark-sql.md) ismertető oktatóanyagot.

## <a name="create-a-dataframe-from-a-csv-file"></a>Adathalmaz létrehozása egy CSV-fájlból

Az alkalmazások közvetlenül hozhatnak létre adathalmazokat a távoli tárhelyeken (például Azure Storage vagy Azure Data Lake Storage) lévő fájlokból vagy mappákból, Hive-táblákból, illetve a Spark által támogatott egyéb adatforrásokból (például Cosmos DB, Azure SQL DB, DW stb.). A következő képernyőképen az oktatóanyaghoz használt HVAC.csv fájl pillanatfelvétele látható. Ez a csv-fájl minden HDInsight Spark-fürtön megtalálható. Az adatok néhány épület hőmérséklet-változását rögzítik.
    
![Az interaktív Spark SQL-lekérdezés adatainak pillanatképe](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Az interaktív Spark SQL-lekérdezés adatainak pillanatképe")


1. Nyissa meg az Előfeltételek szakaszban létrehozott Jupyter notebookot, és az új notebook létrehozása PySpark.

    > [!NOTE]  
    > Ha a notebookot a PySpark-kernellel hozza létre, a `spark`-munkamenet az első kódcella futtatásakor automatikusan jön létre. A munkamenetet nem szükséges manuálisan létrehoznia.

2. Illessze be a következő kódot a notebook egy üres cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a kód futtatásához. A kód importálja az alábbi forgatókönyvhöz szükséges típusokat:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Amikor a Jupyterben interaktív lekérdezést futtatunk, a böngésző ablakának vagy lapjának címsorában a **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik. A feladat befejezése után ez a jel üres körre változik.

    ![Az interaktív Spark SQL-lekérdezés állapota](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Az interaktív Spark SQL-lekérdezés állapota")

3. Hozzon létre egy adathalmazt és egy ideiglenes táblát (**hvac**) a következő kód futtatásával. 

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

## <a name="run-queries-on-the-dataframe"></a>Lekérdezések futtatása az adathalmazon

A tábla létrehozása után az adatokon interaktív lekérdezéseket futtathat.

1. Futtassa a következő kódot a notebook egy üres cellájában:

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Az alábbi táblázatos kimenet jelenik meg.

     ![Az interaktív Spark-lekérdezési eredmény táblázati kimenete](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Az interaktív Spark-lekérdezési eredmény táblázati kimenete")

2. Az eredményeket egyéb megjelenítési formákban is megtekintheti. Az azonos kimenethez tartozó területgrafikon megjelenítéséhez válassza az **Area** (Terület) lehetőséget, majd állítsa be a további értékeket az ábra szerint.

    ![Az interaktív Spark-lekérdezési eredmény területgrafikonja](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Az interaktív Spark-lekérdezési eredmény területgrafikonja")

3. Lépjen a notebook menüsoron **fájl** > **mentése és ellenőrzőpont**.

4. Ha most kezd bele a [következő oktatóanyagba](apache-spark-use-bi-tools.md), hagyja nyitva a notebookot. Ha nem, állítsa le a notebookot a fürt erőforrásainak felszabadítása érdekében: lépjen a notebook menüsoron **fájl** >  **zárja be és Halt**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A HDInsight az adatok és a Jupyter notebookok tárolódnak az Azure Storage vagy az Azure Data Lake Storage, így nyugodtan törölheti a fürt Ha nem használja. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. Ha azt tervezi, hogy rögtön elvégzi a következő oktatóanyagot is, akkor érdemes lehet megtartani a fürtöt.

Nyissa meg az Azure Portalon a fürtöt, és válassza a **Törlés** lehetőséget.

![HDInsight-fürt törlése](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésekor a rendszer a HDInsight Spark-fürtöt és az alapértelmezett tárfiókot is törli.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> * Hozzon létre egy Apache Spark dataframe.
> * Spark SQL-lekérdezések futtatása az adathalmazon

Folytassa a következő cikkben tekintheti meg az adatokat, az Apache Spark regisztrált hogyan tölthetők be egy BI elemzőeszköz, mint a Power BI. 
> [!div class="nextstepaction"]
> [Adatok elemzése BI-eszközökkel](apache-spark-use-bi-tools.md)
