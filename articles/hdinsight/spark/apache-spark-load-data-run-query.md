---
title: 'Oktatóanyag: Adatok betöltése & lekérdezések futtatása az Apache Sparkkal – Azure HDInsight'
description: Oktatóanyag – Ismerje meg, hogyan tölthet be adatokat, és futtathat interaktív lekérdezéseket az Azure HDInsight Spark-fürtjein.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/12/2020
ms.openlocfilehash: 5eb6788a558e4429296731f1693edd18bf92f98f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77198888"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Oktatóanyag: Adatok betöltése és lekérdezések futtatása egy Apache Spark-fürtön az Azure HDInsightban

Ebben az oktatóanyagban megtudhatja, hogyan hozhat létre egy dataframe-et egy csv-fájlból, és hogyan futtathat interaktív Spark SQL-lekérdezéseket egy [Apache Spark-fürtön](https://spark.apache.org/) az Azure HDInsightban. A Sparkban az adathalmazok olyan elosztott adatgyűjtemények, amelyek megnevezett oszlopokba vannak rendezve. Az adathalmazok elméleti szinten azonosak a relációs adatbázisokban található táblákkal vagy R/Python-adathalmazokkal.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Adathalmaz létrehozása egy CSV-fájlból
> * Lekérdezések futtatása az adathalmazon

## <a name="prerequisites"></a>Előfeltételek

Apache Spark-fürt megléte a HDInsightban. Lásd: [Apache Spark-fürt létrehozása.](./apache-spark-jupyter-spark-sql-use-portal.md)

## <a name="create-a-jupyter-notebook"></a>Jupyter notebook létrehozása

A Jupyter Notebook egy interaktív notebook-környezet, amely számos programozási nyelvet támogat. A notebook lehetővé teszi az adatai használatát, a kódok és markdown-szövegek egyesítését, valamint egyszerű vizualizációk elvégzését.

1. Az URL-cím `https://SPARKCLUSTER.azurehdinsight.net/jupyter` `SPARKCLUSTER` szerkesztésével a Spark-fürt nevét. Ezután írja be a szerkesztett URL-t egy webböngészőben. Ha a rendszer kéri, adja meg a fürthöz tartozó bejelentkezési hitelesítő adatokat.

2. A Jupyter weblapon válassza az **Új** > **PySpark** lehetőséget jegyzetfüzet létrehozásához.

   ![Jupyter-jegyzetfüzet létrehozása interaktív Spark SQL-lekérdezés futtatásához](./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png "Jupyter-jegyzetfüzet létrehozása interaktív Spark SQL-lekérdezés futtatásához")

   Új jegyzetfüzet jön létre és nyílik meg`Untitled.ipynb`Névtelen( néven.

    > [!NOTE]  
    > Ha a notebookot a PySpark-kernellel hozza létre, a `spark`-munkamenet az első kódcella futtatásakor automatikusan jön létre. A munkamenetet nem szükséges manuálisan létrehoznia.

## <a name="create-a-dataframe-from-a-csv-file"></a>Adathalmaz létrehozása egy CSV-fájlból

Az alkalmazások közvetlenül a távoli tárolón lévő fájlokból vagy mappákból, például az Azure Storage-ból vagy az Azure Data Lake Storage-ból hozhatnak létre adatkereteket; egy Hive-táblából; vagy a Spark által támogatott más adatforrásokból, például a Cosmos DB, az Azure SQL DB, a DW és így tovább. A következő képernyőképen az oktatóanyaghoz használt HVAC.csv fájl pillanatfelvétele látható. Ez a csv-fájl minden HDInsight Spark-fürtön megtalálható. Az adatok néhány épület hőmérséklet-változását rögzítik.

![Adatok pillanatképe interaktív Spark SQL-lekérdezéshez](./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png "Adatok pillanatképe interaktív Spark SQL-lekérdezéshez")

1. Illessze be a következő kódot a Jupyter-jegyzetfüzet egy üres cellájába, majd a **SHIFT + ENTER** billentyűkombinációval futtassa a kódot. A kód importálja az alábbi forgatókönyvhöz szükséges típusokat:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    Amikor a Jupyterben interaktív lekérdezést futtatunk, a böngésző ablakának vagy lapjának címsorában a **(Foglalt)** állapot jelenik meg a notebook neve mellett. A jobb felső sarokban lévő **PySpark** felirat mellett ekkor egy teli kör is megjelenik. A feladat befejezése után ez a jel üres körre változik.

    ![Interaktív Spark SQL-lekérdezés állapota](./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png "Interaktív Spark SQL-lekérdezés állapota")

1. Figyelje meg, hogy a munkamenet-azonosító t visszaadta. A fenti képen a munkamenet-azonosító 0. Ha szükséges, lekérheti a munkamenet `https://CLUSTERNAME.azurehdinsight.net/livy/sessions/ID/statements` részleteit, ha a FÜRTSORNEVÉhez navigál, ahol a Spark-fürt neve, és az azonosító a munkamenet-azonosító száma.

1. Hozzon létre egy adathalmazt és egy ideiglenes táblát (**hvac**) a következő kód futtatásával.

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

     ![Az interaktív Spark-lekérdezés eredményének táblakimenete](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png "Az interaktív Spark-lekérdezés eredményének táblakimenete")

2. Az eredményeket egyéb megjelenítési formákban is megtekintheti. Az azonos kimenethez tartozó területgrafikon megjelenítéséhez válassza az **Area** (Terület) lehetőséget, majd állítsa be a további értékeket az ábra szerint.

    ![Az interaktív Spark-lekérdezés eredményének területdiagramja](./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png "Az interaktív Spark-lekérdezés eredményének területdiagramja")

3. A jegyzetfüzet menüsorából keresse meg a **Fájlmentés** > **és az Ellenőrzőpont lapot.**

4. Ha most kezd bele a [következő oktatóanyagba](apache-spark-use-bi-tools.md), hagyja nyitva a notebookot. Ha nem, állítsa le a jegyzetfüzetet a fürt erőforrásainak felszabadításához: a jegyzetfüzet menüsorából válassza a >  **Fájlbezárás és a Leállítás**lapot. **File**

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A HDInsight segítségével az adatok és a Jupyter-jegyzetfüzetek az Azure Storage vagy az Azure Data Lake Storage tárolóban tárolódnak, így biztonságosan törölheti a fürtöt, ha nincs használatban. A HDInsight-fürtért is díjat számítunk fel, még akkor is, ha nincs használatban. Mivel a fürt díjai sokszor több, mint a tárolási díjak, célszerű törölni a fürtöket, ha nincsenek használatban. Ha azt tervezi, hogy rögtön elvégzi a következő oktatóanyagot is, akkor érdemes lehet megtartani a fürtöt.

Nyissa meg az Azure Portalon a fürtöt, és válassza a **Törlés** lehetőséget.

![HDInsight-fürt törlése](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "HDInsight-fürt törlése")

Az erőforráscsoport nevét kiválasztva is megnyílik az erőforráscsoport oldala, ahol kiválaszthatja az **Erőforráscsoport törlése** elemet. Az erőforráscsoport törlésekor a rendszer a HDInsight Spark-fürtöt és az alapértelmezett tárfiókot is törli.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre egy dataframe-et egy csv-fájlból, és hogyan futtathat interaktív Spark SQL-lekérdezéseket egy Apache Spark-fürtön az Azure HDInsightban. A következő cikkben megtekintheti, hogy az Apache Sparkban regisztrált adatok hogyan behúzhatók egy BI-elemző eszközbe, például a Power BI-ba.

> [!div class="nextstepaction"]
> [Adatok elemzése BI-eszközökkel](apache-spark-use-bi-tools.md)
