---
title: 'Oktatóanyag: Apache Spark-adatok elemzése Power BI használatával az Azure HDInsightban | Microsoft Docs'
description: HDInsight-fürtökben tárolt Spark-adatok vizualizációja a Microsoft Power BI használatával
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: jgao
ms.openlocfilehash: ece0132573f25f4d288309d2e7bb6710f8fd9519
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33771167"
---
# <a name="tutorial-analyze-spark-data-using-power-bi-in-hdinsight"></a>Oktatóanyag: Spark-adatok elemzése Power BI használatával a HDInsightban 

Ismerkedjen meg az Azure HDInsight Apache Spark-fürtjeiben tárolt adatok a Microsoft Power BI használatával való vizualizációjával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Spark-adatok vizualizációja a Power BI használatával

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* **Végezze el az [Oktatóanyag: Adatok betöltése és lekérdezések futtatása az Azure HDInsight egy Apache Spark-fürtjén](./apache-spark-load-data-run-query.md) cikkben leírtakat**.
* **Power BI**: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) és [Power BI próba-előfizetés](https://app.powerbi.com/signupredirect?pbi_source=web) (opcionális).


## <a name="verify-the-data"></a>Az adatok ellenőrzése

Az [előző oktatóanyagban](apache-spark-load-data-run-query.md) létrehozott Jupyter notebook tartalmazza a `hvac`-tábla létrehozásához szükséges kódot. Ez a tábla a **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv** fájlban megtalálható összes HDInsight Spark-fürt esetében rendelkezésre álló CSV-fájlon alapul. Az adatok ellenőrzéséhez kövesse az alábbi eljárást.

1. Illessze be a Jupyter notebookból a következő kódot, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt. A kód segítségével a táblák meglétét ellenőrizhetjük.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    A kimenet a következőképpen fog kinézni:

    ![Táblák megjelenítése a Sparkban](./media/apache-spark-use-bi-tools/show-tables.png)

    Ha az oktatóanyag elindítása előtt már bezárta a notebookot, a `hvactemptable` törlődik, így az nem szerepel a kimenetben.  A BI-eszközökkel csak a metaadattárban tárolt Hive-táblákhoz (ezeket **Hamis** érték jelzi az **isTemporary** oszlopban) lehet hozzáférni. Ebben az oktatóanyagban kapcsolatot létesít az Ön által létrehozott **HVAC**-táblával.

2. Illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt. A kód elvégzi a tábla adatainak ellenőrzését.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    A kimenet a következőképpen fog kinézni:

    ![A HVAC-tábla sorainak megjelenítése a Sparkban](./media/apache-spark-use-bi-tools/select-limit.png)

3. A notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Állítsa le a notebookot az erőforrások felszabadításához. 

## <a name="visualize-the-data"></a>Az adatok vizualizációja

Ebben a szakaszban a Power BI-t fogja használni a vizualizációk, jelentések és irányítópultok a Spark-fürt adataiból való létrehozásához. 

### <a name="create-a-report-in-power-bi-desktop"></a>Jelentés létrehozása a Power BI Desktopban
A Spark használatba vételének első lépései a csatlakozás a fürthöz a Power BI Desktopban, az adatok betöltése a fürtből, és az ezen adatokon alapuló, alapszintű vizualizáció létrehozása.

> [!NOTE]
> A jelen cikkben bemutatott összekötő jelenleg előzetes verzióban érhető el. Visszajelzéseit a [Power BI-közösség](https://community.powerbi.com/) webhelyén vagy a [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas) oldalon keresztül küldheti el.

1. Nyissa meg a [Power BI Desktopot](https://powerbi.microsoft.com/en-us/desktop/).
1. A **Kezdőlapon** kattintson az **Adatok lekérése**, majd a **Továbbiak gombra**.

    ![Adatok lekérése a Power BI Desktopba a HDInsight Apache Sparkból](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Adatok lekérése a Power BI-ba az Apache Spark BI-ból")


2. A keresőmezőbe írja be a `Spark` kifejezést, válassza az **Azure HDInsight Spark (bétaverzió)** elemet, majd kattintson a **Csatlakozás** elemre.

    ![Adatok lekérése a Power BI-ba az Apache Spark BI-ból](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Adatok lekérése a Power BI-ba az Apache Spark BI-ból")

3. Adja meg a fürt URL-címét (`mysparkcluster.azurehdinsight.net` formátumban), válassza a **DirectQuery** elemet, majd kattintson az **OK** gombra.

    A Sparkkal bármelyik adatkapcsolati módot használhatja. A DirectQuery használatakor a módosítások anélkül jelennek meg a jelentésekben, hogy a teljes adatkészletet frissíteni kellene. Ha importálja az adatokat, a módosítások megtekintéséhez frissítenie kell az adatkészletet. További információ a DirectQuery használatának módjáról és idejéről: [DirectQuery használata a Power BI-ban](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Adja meg a HDInsight bejelentkezési fiókadatait, majd kattintson a **Csatlakozás** gombra. Az alapértelmezett fióknév az *admin*.

5. Válassza ki a `hvac`-táblát, várjon, amíg megjelenik az adatok előnézete, majd kattintson a **Betöltés** gombra.

    ![Spark-fürt felhasználóneve és jelszava](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark-fürt felhasználóneve és jelszava")

    A Power BI Desktop rendelkezik a Spark-fürthöz való csatlakozáshoz és adatok a `hvac`-táblából való letöltéséhez szükséges információkkal. A tábla és annak oszlopai a **Mezők** panelen jelennek meg.  Tekintse meg a következő képernyőképet:

6. A célhőmérséklet és a tényleges hőmérséklet közötti eltérés vizualizációja az egyes épületek esetén: 

    1. A **VIZUALIZÁCIÓK** panelen válassza a **Területdiagram** lehetőséget. 
    2. Húzza a **BuildingID** mezőt a **Tengely** területre, az **ActualTemp** és a **TargetTemp** mezőket pedig az **Érték** területre.

        ![Spark-adatvizualizációk létrehozása az Apache Spark BI használatával](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Spark-adatvizualizációk létrehozása az Apache Spark BI használatával")

        A diagram a következőképpen fog kinézni:

        ![Spark-adatvizualizációk létrehozása az Apache Spark BI használatával](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Spark-adatvizualizációk létrehozása az Apache Spark BI használatával")

        Alapértelmezés szerint a vizualizáció az **ActualTemp** és a **TargetTemp** mezők összegét jeleníti meg. Ha az **ActualTemp** és a **TargetTemp** mezők melletti lefelé mutató nyílra kattint a Vizualizációk ablaktáblán, akkor láthatja, hogy az **Összeg** paraméter van kiválasztva.

    3. Ha az **ActualTemp** és a **TargetTemp** mezők melletti lefelé mutató nyílra kattint a Vizualizációk ablaktáblán, az **Átlag** lehetőség kiválasztásával megtekintheti az egyes épületek tényleges és célhőmérsékleteinek átlagát.

        ![Spark-adatvizualizációk létrehozása az Apache Spark BI használatával](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Spark-adatvizualizációk létrehozása az Apache Spark BI használatával")

        Az adatok vizualizációjának a képernyőképen láthatóhoz hasonlóan kell kinéznie. Ha az egérmutatót a vizualizáció fölé viszi, megtekintheti a kapcsolódó adatokhoz tartozó eszköztippeket.

        ![Spark-adatvizualizációk létrehozása az Apache Spark BI használatával](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Spark-adatvizualizációk létrehozása az Apache Spark BI használatával")

7. Kattintson a **Fájl**, majd a **Mentés** elemre, és adja meg a következő nevet a fájl számára: `BuildingTemperature.pbix`. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>A jelentés közzététele a Power BI szolgáltatásban (opcionális)

A Power BI szolgáltatás segítségével jelentéseket és irányítópultokat oszthat meg a vállalaton belül. Ebben a szakaszban először az adatkészlet és a jelentés közzétételét végezzük el. Ezután rögzíteni fogjuk a jelentést egy irányítópulton. Az irányítópultok jellemzően az adott jelentés adatai egy részének középpontba helyezésére szolgálnak. Ugyan a jelentésben csak egy vizualizáció szerepel, mégis hasznos lehet végigmenni a lépéseken.

1. Nyissa meg a Power BI Desktopot.
2. A **Kezdőlapon** kattintson a **Közzététel** gombra.

    ![Közzététel a Power BI Desktopból](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Közzététel a Power BI Desktopból")

2. Válasszon ki egy munkaterületet az adatkészlet és a jelentés közzétételéhez, majd kattintson a **Kiválasztás** gombra. A következő képen az alapértelmezett **Saját munkaterület** mező van kiválasztva.

    ![Munkaterület kiválasztása az adatkészlet és a jelentés közzétételéhez](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Munkaterület kiválasztása az adatkészlet és a jelentés közzétételéhez") 

3. Ha a közzététel sikeresen megtörtént, kattintson a **„BuildingTemperature.pbix” fájl megnyitása a Power BI-ban** elemre.

    ![Sikeres közzététel, kattintson a hitelesítő adatok megadásához](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Sikeres közzététel, kattintson a hitelesítő adatok megadásához") 

4. A Power BI szolgáltatás felületén kattintson a **Hitelesítő adatok megadása** elemre.

    ![Hitelesítő adatok megadása a Power BI szolgáltatásban](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Hitelesítő adatok megadása a Power BI szolgáltatásban")

5. Kattintson a **Hitelesítő adatok szerkesztése** gombra.

    ![Hitelesítő adatok szerkesztése a Power BI szolgáltatásban](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Hitelesítő adatok szerkesztése a Power BI szolgáltatásban")

6. Adja meg a HDInsight bejelentkezési fiókadatait, majd kattintson a **Bejelentkezés** gombra. Az alapértelmezett fióknév az *admin*.

    ![Bejelentkezés Spark-fürtre](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Bejelentkezés Spark-fürtre")

7. A bal oldali panelen lépjen a **Munkaterületek** > **Saját munkaterület** > **JELENTÉSEK** részre, majd kattintson a **BuildingTemperature** elemre.

    ![A bal oldali ablaktábla Jelentések területén szereplő jelentés](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "A bal oldali ablaktábla Jelentések területén szereplő jelentés")

    A **BuildingTemperature** elemnek a bal oldali ablaktábla **ADATKÉSZLETEK** területén is meg kell jelennie.

    A Power BI Desktopban létrehozott vizualizáció mostantól a Power BI szolgáltatásban is elérhető. 

8. Vigye az egérmutatót a vizualizáció fölé, majd kattintson a jobb felső sarokban lévő rögzítés ikonra.

    ![Jelentés a Power BI szolgáltatásban](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Jelentés a Power BI szolgáltatásban")

9. Válassza az „Új irányítópult” lehetőséget, adja meg a `Building temperature` nevet, majd kattintson a **Rögzítés** gombra.

    ![Rögzítés új irányítópulton](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Rögzítés új irányítópulton")

10. A jelentésben kattintson az **Ugrás az irányítópultra** gombra. 

Ezzel rögzítette a vizualizációt az irányítópulton – felvehet további vizualizációkat is a jelentésbe, amelyeket ugyanezen az irányítópulton rögzíthet. További információ a jelentésekről és az irányítópultokról: [Power BI-jelentések](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) és [Power BI-irányítópultok](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

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

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

- Spark-adatok vizualizációja a Power BI használatával.

A következő cikkben megismerkedhet a Sparkban regisztrált adatok olyan BI-elemzési eszközökbe történő áthúzásának lehetőségével, mint például a Power BI. 
> [!div class="nextstepaction"]
> [Spark streamelési feladat futtatása](apache-spark-eventhub-streaming.md)

