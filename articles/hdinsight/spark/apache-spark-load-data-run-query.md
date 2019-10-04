---
title: 'Oktatóanyag: Adatterhelés és lekérdezések futtatása Apache Spark-fürtön az Azure HDInsight'
description: Oktatóanyag – Ismerje meg, hogyan tölthetők be az adathalmazok, és hogyan futtathat interaktív lekérdezéseket a Spark-fürtökön az Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.author: hrasheed
ms.date: 05/16/2019
ms.openlocfilehash: 109ed1a2ef22e498c2d19fd2e4f1848f289e9b55
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735276"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Oktatóanyag: Adatterhelés és lekérdezések futtatása egy Apache Spark-fürtön az Azure HDInsight

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre dataframe egy csv-fájlból, és hogyan futtathat interaktív Spark SQL-lekérdezéseket egy [Apache Spark](https://spark.apache.org/) -fürtön az Azure HDInsight. A Sparkban az adathalmazok olyan elosztott adatgyűjtemények, amelyek megnevezett oszlopokba vannak rendezve. Az adathalmazok elméleti szinten azonosak a relációs adatbázisokban található táblákkal vagy R/Python-adathalmazokkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Adathalmaz létrehozása egy CSV-fájlból
> * Lekérdezések futtatása az adathalmazon

## <a name="prerequisites"></a>Előfeltételek

Apache Spark-fürt megléte a HDInsightban. Lásd: [Apache Spark-fürt létrehozása](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Jupyter notebook létrehozása

A Jupyter Notebook egy interaktív notebook-környezet, amely számos programozási nyelvet támogat. A notebook lehetővé teszi az adatai használatát, a kódok és markdown-szövegek egyesítését, valamint egyszerű vizualizációk elvégzését. 

1. Szerkessze az `https://SPARKCLUSTER.azurehdinsight.net/jupyter` URL- `SPARKCLUSTER` címet a Spark-fürt nevére való lecseréléssel. Ezután írja be a szerkesztett URL-címet egy webböngészőbe. Ha a rendszer kéri, adja meg a fürthöz tartozó bejelentkezési hitelesítő adatokat.

2. A Jupyter weblapon válassza az **új** > **PySpark** lehetőséget a jegyzetfüzet létrehozásához. 

   ![Jupyter notebook létrehozása interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter notebook létrehozása interaktív Spark SQL-lekérdezés futtatásához")

   A rendszer létrehoz egy új jegyzetfüzetet, és a névtelen név (`Untitled.ipynb`) nevet nyitja meg.

    > [!NOTE]  
    > Ha a notebookot a PySpark-kernellel hozza létre, a `spark`-munkamenet az első kódcella futtatásakor automatikusan jön létre. A munkamenetet nem szükséges manuálisan létrehoznia.

## <a name="create-a-dataframe-from-a-csv-file"></a>Adathalmaz létrehozása egy CSV-fájlból

Az alkalmazások közvetlenül hozhatnak létre adathalmazokat a távoli tárhelyeken (például Azure Storage vagy Azure Data Lake Storage) lévő fájlokból vagy mappákból, Hive-táblákból, illetve a Spark által támogatott egyéb adatforrásokból (például Cosmos DB, Azure SQL DB, DW stb.). A következő képernyőképen az oktatóanyaghoz használt HVAC.csv fájl pillanatfelvétele látható. Ez a csv-fájl minden HDInsight Spark-fürtön megtalálható. Az adatok néhány épület hőmérséklet-változását rögzítik.
    
![Az interaktív Spark SQL-lekérdezés adatainak pillanatképe](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Az interaktív Spark SQL-lekérdezés adatainak pillanatképe")

1. Illessze be a következő kódot a Jupyter jegyzetfüzet egyik üres cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a kód futtatásához. A kód importálja az alábbi forgatókönyvhöz szükséges típusokat:

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

3. A notebook menüsávban navigáljon a **fájl** > **Mentés és ellenőrzőpont**elemre.

4. Ha most kezd bele a [következő oktatóanyagba](apache-spark-use-bi-tools.md), hagyja nyitva a notebookot. Ha nem, állítsa le a jegyzetfüzetet a fürterőforrások felszabadításához: a jegyzetfüzet menüsorában navigáljon a **fájl** >  **bezárásához és leállításához**.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A HDInsight az adatait és Jupyter jegyzetfüzeteit az Azure Storage vagy a Azure Data Lake Storage tárolja, így biztonságosan törölheti a fürtöt, ha az nincs használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket. Ha azt tervezi, hogy rögtön elvégzi a következő oktatóanyagot is, akkor érdemes lehet megtartani a fürtöt.

Nyissa meg az Azure Portalon a fürtöt, és válassza a **Törlés** lehetőséget.

![HDInsight-fürt törlése](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésekor a rendszer a HDInsight Spark-fürtöt és az alapértelmezett tárfiókot is törli.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre dataframe egy csv-fájlból, és hogyan futtathat interaktív Spark SQL-lekérdezéseket egy Apache Spark-fürtön az Azure HDInsight. A következő cikkből megtudhatja, hogy a Apache Sparkban regisztrált információk Hogyan tölthetők be egy BI Analytics-eszközbe, például Power BIba.

> [!div class="nextstepaction"]
> [Adatok elemzése BI-eszközökkel](apache-spark-use-bi-tools.md)