---
title: 'Oktatóanyag: Adatok betöltése, és lekérdezéseket futtathat az Azure HDInsight az Apache Spark-fürt '
description: Megtudhatja, hogyan tölthet be adatokat és futtathat interaktív lekérdezéseket Spark-fürtökön az Azure HDInsightban.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.author: hrasheed
ms.date: 05/16/2019
ms.openlocfilehash: 09509b32320fb10b8ab3d563442b6d0fb44ad34e
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65909200"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Oktatóanyag: Adatok betöltése, és lekérdezéseket futtathat az Azure HDInsight az Apache Spark-fürt

Ebből az oktatóanyagból megtudhatja, hogyan hozzon létre dataframe-csv-fájlból, és hogyan futtathat interaktív Spark SQL-lekérdezéseket az egy [Apache Spark](https://spark.apache.org/) Azure HDInsight-fürt. A Sparkban az adathalmazok olyan elosztott adatgyűjtemények, amelyek megnevezett oszlopokba vannak rendezve. Az adathalmazok elméleti szinten azonosak a relációs adatbázisokban található táblákkal vagy R/Python-adathalmazokkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Adathalmaz létrehozása egy CSV-fájlból
> * Lekérdezések futtatása az adathalmazon

## <a name="prerequisites"></a>Előfeltételek

Apache Spark-fürt megléte a HDInsightban. Lásd: [hozzon létre egy Apache Spark-fürt](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Jupyter notebook létrehozása

A Jupyter Notebook egy interaktív notebook-környezet, amely számos programozási nyelvet támogat. A notebook lehetővé teszi az adatai használatát, a kódok és markdown-szövegek egyesítését, valamint egyszerű vizualizációk elvégzését. 

1. Szerkessze az URL-címet `https://SPARKCLUSTER.azurehdinsight.net/jupyter` lecserélésével `SPARKCLUSTER` a Spark-fürt nevére. Ezután írja be a szerkesztett URL-CÍMÉT egy webböngészőben. Ha a rendszer kéri, adja meg a fürthöz tartozó bejelentkezési hitelesítő adatokat.

2. Válassza ki a Jupyter weblapról **új** > **PySpark** , hozzon létre egy notebookot. 

   ![Jupyter notebook létrehozása interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter notebook létrehozása interaktív Spark SQL-lekérdezés futtatásához")

   Egy új notebook létrejött, és névtelen nevű megnyitott (`Untitled.ipynb`).

    > [!NOTE]  
    > Ha a notebookot a PySpark-kernellel hozza létre, a `spark`-munkamenet az első kódcella futtatásakor automatikusan jön létre. A munkamenetet nem szükséges manuálisan létrehoznia.

## <a name="create-a-dataframe-from-a-csv-file"></a>Adathalmaz létrehozása egy CSV-fájlból

Az alkalmazások közvetlenül hozhatnak létre adathalmazokat a távoli tárhelyeken (például Azure Storage vagy Azure Data Lake Storage) lévő fájlokból vagy mappákból, Hive-táblákból, illetve a Spark által támogatott egyéb adatforrásokból (például Cosmos DB, Azure SQL DB, DW stb.). A következő képernyőképen az oktatóanyaghoz használt HVAC.csv fájl pillanatfelvétele látható. Ez a csv-fájl minden HDInsight Spark-fürtön megtalálható. Az adatok néhány épület hőmérséklet-változását rögzítik.
    
![Az interaktív Spark SQL-lekérdezés adatainak pillanatképe](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Az interaktív Spark SQL-lekérdezés adatainak pillanatképe")

1. Illessze be a következő kódot egy üres cellába, a Jupyter notebookot, és nyomja le az **SHIFT + ENTER** a kód futtatásához. A kód importálja az alábbi forgatókönyvhöz szükséges típusokat:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Amikor a Jupyterben interaktív lekérdezést futtatunk, a böngésző ablakának vagy lapjának címsorában a **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik. A feladat befejezése után ez a jel üres körre változik.

    ![Az interaktív Spark SQL-lekérdezés állapota](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Az interaktív Spark SQL-lekérdezés állapota")

2. Hozzon létre egy adathalmazt és egy ideiglenes táblát (**hvac**) a következő kód futtatásával. 

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

Ebben az oktatóanyagban megtudhatta, hogyan hozzon létre dataframe-csv-fájlból, és hogyan interaktív Spark SQL-lekérdezések futtatásához az Azure HDInsight az Apache Spark-fürt. Folytassa a következő cikkben tekintheti meg az adatokat, az Apache Spark regisztrált hogyan tölthetők be egy BI elemzőeszköz, mint a Power BI.

> [!div class="nextstepaction"]
> [Adatok elemzése BI-eszközökkel](apache-spark-use-bi-tools.md)