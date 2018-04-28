---
title: Adatok képi megjelenítés eszközökkel on Azure HDInsight Spark BI |} Microsoft Docs
description: Adatok képi megjelenítés eszközök segítségével analytics Apache Spark BI használata a HDInsight-fürtökön
keywords: Apache spark üzleti intelligencia, spark bi, spark adatábrázolási, spark üzleti intelligencia
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 02/14/2018
ms.author: jgao
ms.openlocfilehash: 0e728e17a64acd990b301bac8139c7bb395a3098
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI adatok képi megjelenítés eszközökkel Azure hdinsightban

Ismerje meg, hogyan használható [Microsoft Power BI](http://powerbi.microsoft.com) Azure HDInsight az Apache Spark-fürt adatainak megjelenítéséhez.

## <a name="prerequisites"></a>Előfeltételek

* **Fejezze be a cikk [interaktív lekérdezések futtatására a hdinsight Spark-fürtjei](./apache-spark-load-data-run-query.md)**.
* **A Power BI**: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) és [Power BI próba-előfizetés](https://app.powerbi.com/signupredirect?pbi_source=web) (nem kötelező).


## <a name="hivetable"></a>Ellenőrizze az adatokat

A létrehozott Jupyter notebook a [az oktatóanyag előző](apache-spark-load-data-run-query.md) létrehozásához kódot tartalmazza egy `hvac` tábla. Ez a táblázat alapul a CSV-fájl érhető el az összes HDInsight Spark-fürtök: **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. A következő eljárással ellenőrizze az adatokat.

1. A Jupyter notebook, az alábbi kódot, és nyomja le az **SHIFT + ENTER**. A kód ellenőrzi, hogy létezik-e a táblázatokat.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    A kimenet a következőképpen fog kinézni:

    ![A Spark táblák megjelenítése](./media/apache-spark-use-bi-tools/show-tables.png)

    Ha korábban bezárta a notebook az oktatóanyag elindítása előtt `hvactemptable` törlődnek, így nem szerepel a kimenetet.
    Csak Hive táblák találhatók a metaadattárhoz (által jelzett **hamis** alatt a **isTemporary** oszlop) érhetők el a BI-eszközökkel. Ebben az oktatóanyagban csatlakozhat a **hvac** létrehozott tábla.

2. Illessze be a következő kódot a cella üres, és nyomja le az **SHIFT + ENTER**. A kód azt ellenőrzi, hogy az adatok a táblázatban.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    A kimenet a következőképpen fog kinézni:

    ![A Spark hvac tábla azon sorait megjelenítése](./media/apache-spark-use-bi-tools/select-limit.png)

3. A notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Állítsa le a notebook az erőforrások kijelölése. 















## <a name="powerbi"></a>Használja a Power bi-ban

Ebben a szakaszban a Power bi-ban a Spark-fürt adatokból képi megjelenítéseket, jelentések és irányítópultok létrehozásához használhat. 

### <a name="create-a-report-in-power-bi-desktop"></a>Hozzon létre egy jelentést a Power BI Desktop
Az első végzett munka során Spark lépésekre csatlakozzon a fürthöz, a Power BI Desktopban, adatok betöltése az a fürt és az adatok alapján alapvető képi megjelenítés létrehozása.

> [!NOTE]
> Az ebben a cikkben bemutatott összekötő jelenleg előzetes verzió. Bármely visszajelzést keresztül kaphatnak a [Power BI-Közösség](https://community.powerbi.com/) hely vagy [Power BI ötleteket](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. Nyissa meg [a Power BI Desktopban](https://powerbi.microsoft.com/en-us/desktop/).
1. Az a **Home** lapra, majd **adatok beolvasása**, majd **további**.

    ![Adatok beolvasása a Power BI Desktop HDInsight az Apache Spark on az](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "adatok beolvasása a Power bi-bA az Apache Spark BI")


2. Adja meg `Spark` be a keresőmezőbe, válassza ki a **Azure HDInsight Spark (béta)**, és kattintson a **Connect**.

    ![Adatok beolvasása a Power bi-bA az Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "adatok beolvasása a Power bi-bA az Apache Spark BI")

3. Adja meg a fürt URL-CÍMÉT (formájában `mysparkcluster.azurehdinsight.net`) elemre, jelölje be **DirectQuery**, és kattintson a **OK**.

    Spark vagy adatok csatlakozási mód használatával. DirectQuery használatakor módosítások megjelennek jelentések a teljes adatkészlet frissítése nélkül. Ha importálja az adatokat, frissítenie kell az adatkészlet módosítások megtekintéséhez. További információt és a DirectQuery használatával, lásd: [DirectQuery használatával a Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Adja meg a HDInsight bejelentkezési fiók adatait, majd kattintson a **Connect**. Az alapértelmezett fiók neve *admin*.

5. Válassza ki a `hvac` tábla, várja meg, hogy az adatok villámnézetét, és kattintson **terhelés**.

    ![Fürt felhasználónevet és jelszót Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark fürt felhasználónév és jelszó")

    A Power BI Desktop rendelkezik a Spark fürt és az adatok betöltése a való kapcsolódáshoz szükséges adatokat a `hvac` tábla. A táblázat és az oszlopait jelennek meg a **mezők** ablaktáblán.  Tekintse meg az alábbi képernyőfelvételen:

6. Cél hőmérséklet és a tényleges hőmérséklet rendszerbeli minden közötti eltérés megjelenítése: 

    1. Az a **képi megjelenítések** ablaktáblán válassza előbb **területdiagram**. 
    2. Húzza a **BuildingID** mezőről **tengely**, majd húzza a **ActualTemp** és **TargetTemp** mező a **érték**.

        ![Hozzon létre a Spark Apache Spark BI használatával adatmegjelenítésekkel](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Spark létrehozása az adatmegjelenítéseket Apache Spark BI használatával")

        A diagram néz ki:

        ![Hozzon létre a Spark Apache Spark BI használatával adatmegjelenítésekkel](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Spark létrehozása az adatmegjelenítéseket Apache Spark BI használatával")

        Alapértelmezés szerint a képi megjelenítés jeleníti meg az összegük **ActualTemp** és **TargetTemp**. Jelölje be a lefelé mutató nyílra a **ActualTemp** és **TragetTemp** a képi megjelenítések ablaktáblában látható **Sum** van kiválasztva.

    3. Kattintson a Tovább gombra a lefelé nyílra **ActualTemp** és **TragetTemp** képi megjelenítések ablaktáblában jelölje ki **átlagos** le tényleges átlagosan és a cél hőmérsékletek minden felépítését.

        ![Hozzon létre a Spark Apache Spark BI használatával adatmegjelenítésekkel](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Spark létrehozása az adatmegjelenítéseket Apache Spark BI használatával")

        Az adatok vizuális kell ezen a képernyőfelvételen hasonló. Helyezze a kurzort a képi megjelenítés eszköztipp vonatkozó adatok eléréséhez.

        ![Hozzon létre a Spark Apache Spark BI használatával adatmegjelenítésekkel](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Spark létrehozása az adatmegjelenítéseket Apache Spark BI használatával")

7. Kattintson a **fájl** majd **mentése**, és adja meg a nevét `BuildingTemperature.pbix` a fájlt. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>A jelentés közzététele a Power BI-ban (nem kötelező)

A Power BI szolgáltatás lehetővé teszi a jelentések és irányítópultok megosztani a szervezet. Ebben a szakaszban először beállíthatja a DataSet adatkészlet és a jelentést. Ezt követően a jelentés-irányítópulthoz rögzíti. Irányítópultok általában használják; a jelentésben szereplő adatok egy részét összpontosíthat a jelentésben csak egy képi megjelenítés rendelkezik, de továbbra is hasznos lehet a szükséges lépéseket.

1. Nyissa meg a Power BI Desktop.
2. Az a **Home** lapra, majd **közzététel**.

    ![A Power BI Desktop közzététele](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "a Power BI Desktop közzététele")

2. Az adatkészlet közzététele egy munkaterület kiválasztása és a jelentést, majd kattintson a **válasszon**. Az alábbi ábrán, az alapértelmezett **saját munkaterület** van kiválasztva.

    ![Válassza ki a DataSet adatkészlet közzététele, és jelentse a munkaterület](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "adatkészlet közzététele, és jelentse a Select munkaterület") 

3. Miután a közzététel sikeres van, kattintson **nyitott "BuildingTemperature.pbix" Power BI-ban**.

    ![Közzététel sikeres, kattintson a hitelesítő adatok](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "közzététele sikeres, kattintson a hitelesítő adatok") 

4. A Power BI-ban, kattintson a **adja meg a hitelesítő adatok**.

    ![Adja meg a hitelesítő adatait a Power BI-ban](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "adja meg a hitelesítő adatait a Power BI-ban")

5. Kattintson a **hitelesítő adatok szerkesztése**.

    ![A Power BI-ban hitelesítő adatok szerkesztése](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Power BI-ban hitelesítő adatok szerkesztése")

6. Adja meg a HDInsight bejelentkezési fiók adatait, és kattintson **bejelentkezés**. Az alapértelmezett fiók neve *admin*.

    ![Jelentkezzen be a Spark-fürt](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "jelentkezzen be a Spark-fürt")

7. A bal oldali ablaktáblában lépjen **munkaterületek** > **saját munkaterület** > **jelentések**, majd kattintson a **BuildingTemperature**.

    ![A jelentés jelentések bal oldali ablaktáblában felsorolt](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "jelentések bal oldali ablaktáblában felsorolt jelentés")

    Emellett meg kell jelennie **BuildingTemperature** tartozó **ADATKÉSZLETEK** a bal oldali ablaktáblán.

    A Látványelem a Power BI Desktopban létrehozott már elérhető a Power BI-ban. 

8. A kurzorral rámutat a képi megjelenítés, és kattintson a jobb felső sarokban a PIN-kód ikonra.

    ![A Power BI-jelentés](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "a Power BI-jelentés")

9. Válassza ki az "Új irányítópult", adja meg a nevét `Building temperature`, majd kattintson a **PIN-kód**.

    ![Rögzítés az irányítópulton új](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "rögzítés az irányítópulton új")

10. Kattintson a jelentés **irányítópult megnyitásához**. 

A visual rögzítve az irányítópulton – az egyéb látványelemek felvétele a jelentésbe, és az azonos irányítópulton rögzítheti őket. További információ a jelentések és irányítópultok: [jelentéseket a Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/)és [Power BI-irányítópultjain](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

<!--
## <a name="tableau"></a>Use Tableau Desktop 

> [!NOTE]
> This section is applicable only for Spark 1.5.2 clusters created in Azure HDInsight.
>
>

1. Install [Tableau Desktop](http://www.tableau.com/products/desktop) on the computer where you are running this Apache Spark BI tutorial.

2. Make sure that computer also has Microsoft Spark ODBC driver installed. You can install the driver from [here](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Launch Tableau Desktop. In the left pane, from the list of server to connect to, click **Spark SQL**. If Spark SQL is not listed by default in the left pane, you can find it by click **More Servers**.
2. In the Spark SQL connection dialog box, provide the values as shown in the screenshot, and then click **OK**.

    ![Connect to a cluster for Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "Connect to a cluster for Apache Spark BI")

    The authentication drop-down lists **Microsoft Azure HDInsight Service** as an option, only if you installed the [Microsoft Spark ODBC Driver](http://go.microsoft.com/fwlink/?LinkId=616229) on the computer.
3. On the next screen, from the **Schema** drop-down, click the **Find** icon, and then click **default**.

    ![Find schema for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Find schema for Apache Spark BI")
4. For the **Table** field, click the **Find** icon again to list all the Hive tables available in the cluster. You should see the **hvac** table you created earlier using the notebook.

    ![Find table for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Find table for Apache Spark BI")
5. Drag and drop the table to the top box on the right. Tableau imports the data and displays the schema as highlighted by the red box.

    ![Add tables to Tableau for Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Add tables to Tableau for Apache Spark BI")
6. Click the **Sheet1** tab at the bottom left. Make a visualization that shows the average target and actual temperatures for all buildings for each date. Drag **Date** and **Building ID** to **Columns** and **Actual Temp**/**Target Temp** to **Rows**. Under **Marks**, select **Area** to use an area map for Spark data visualization.

     ![Add fields for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Add fields for Spark data visualization")
7. By default, the temperature fields are shown as aggregate. If you want to show the average temperatures instead, you can do so from the drop-down, as shown in the following screenshot:

    ![Take average of temperature for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "Take average of temperature for Spark data visualization")

8. You can also super-impose one temperature map over the other to get a better feel of difference between target and actual temperatures. Move the mouse to the corner of the lower area map until you see the handle shape highlighted in a red circle. Drag the map to the other map on the top and release the mouse when you see the shape highlighted in red rectangle.

    ![Merge maps for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "Merge maps for Spark data visualization")

     Your data visualization should change as shown in the screenshot:

    ![Tableau output for Spark data visualization](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Tableau output for Spark data visualization")
9. Click **Save** to save the worksheet. You can create dashboards and add one or more sheets to it.
-->

## <a name="next-steps"></a>További lépések

Amennyiben megtudta, hogyan hozzon létre egy fürtöt, hozzon létre a Spark adatkeretek lekérdezni adatokat, és majd érheti el, hogy az adatok Üzletiintelligencia-eszközök. Most már megtekintheti kezelheti a fürt erőforrásait, és egy HDInsight Spark-fürtben futó összes feladatot hibakeresési utasításokat.

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

