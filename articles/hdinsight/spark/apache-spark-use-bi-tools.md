---
title: 'Tutorial: Analyze Azure HDInsight Apache Spark data with Power BI'
description: Tutorial - Use Microsoft Power BI to visualize Apache Spark data stored HDInsight clusters
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 10/03/2019
ms.openlocfilehash: 3fd1405d8421d71f52d9cd215dd055ce1595abd0
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327273"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Tutorial: Analyze Apache Spark data using Power BI in HDInsight

In this tutorial, you learn how to use [Microsoft Power BI](https://powerbi.microsoft.com/) to visualize data in an Apache Spark cluster in [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Spark-adatok vizualizációja a Power BI használatával

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Complete the article [Tutorial: Load data and run queries on an Apache Spark cluster in Azure HDInsight](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Optional: [Power BI trial subscription](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Az adatok ellenőrzése

The [Jupyter Notebook](https://jupyter.org/) that you created in the [previous tutorial](apache-spark-load-data-run-query.md) includes code to create an `hvac` table. This table is based on the CSV file available on all HDInsight Spark clusters at `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Az adatok ellenőrzéséhez kövesse az alábbi eljárást.

1. Illessze be a Jupyter notebookból a következő kódot, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt. A kód segítségével a táblák meglétét ellenőrizhetjük.

    ```PySpark
    %%sql
    SHOW TABLES
    ```

    A kimenet a következőképpen fog kinézni:

    ![Táblák megjelenítése a Sparkban](./media/apache-spark-use-bi-tools/apache-spark-show-tables.png)

    Ha az oktatóanyag elindítása előtt már bezárta a notebookot, a `hvactemptable` törlődik, így az nem szerepel a kimenetben.  A BI-eszközökkel csak a metaadattárban tárolt Hive-táblákhoz (ezeket **Hamis** érték jelzi az **isTemporary** oszlopban) lehet hozzáférni. Ebben az oktatóanyagban kapcsolatot létesít az Ön által létrehozott **HVAC**-táblával.

2. Illessze be a következő kódot egy üres cellába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt. A kód elvégzi a tábla adatainak ellenőrzését.

    ```PySpark
    %%sql
    SELECT * FROM hvac LIMIT 10
    ```

    A kimenet a következőképpen fog kinézni:

    ![A HVAC-tábla sorainak megjelenítése a Sparkban](./media/apache-spark-use-bi-tools/apache-spark-select-limit.png)

3. A notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre. Állítsa le a notebookot az erőforrások felszabadításához.

## <a name="visualize-the-data"></a>Az adatok vizualizációja

Ebben a szakaszban a Power BI-t fogja használni a vizualizációk, jelentések és irányítópultok a Spark-fürt adataiból való létrehozásához.

### <a name="create-a-report-in-power-bi-desktop"></a>Jelentés létrehozása a Power BI Desktopban

A Spark használatba vételének első lépései a csatlakozás a fürthöz a Power BI Desktopban, az adatok betöltése a fürtből, és az ezen adatokon alapuló, alapszintű vizualizáció létrehozása.

> [!NOTE]  
> A jelen cikkben bemutatott összekötő jelenleg előzetes verzióban érhető el. Visszajelzéseit a [Power BI-közösség](https://community.powerbi.com/) webhelyén vagy a [Power BI Ideas](https://ideas.powerbi.com/forums/265200-power-bi-ideas) oldalon keresztül küldheti el.

1. Nyissa meg a Power BI Desktopot. Close the start-up splash screen if it opens.

2. From the **Home** tab, navigate to **Get Data** > **More..** .

    ![Get data into Power BI Desktop from HDInsight Apache Spark](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Get data into Power BI from Apache Spark BI")

3. Enter `Spark` in the search box, select **Azure HDInsight Spark**, and then select **Connect**.

    ![Get data into Power BI from Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Get data into Power BI from Apache Spark BI")

4. Enter your cluster URL (in the form `mysparkcluster.azurehdinsight.net`) in the **Server** text box.

5. Under **Data connectivity mode**, select **DirectQuery**. Ezután kattintson az **OK** gombra.

    A Sparkkal bármelyik adatkapcsolati módot használhatja. A DirectQuery használatakor a módosítások anélkül jelennek meg a jelentésekben, hogy a teljes adatkészletet frissíteni kellene. Ha importálja az adatokat, a módosítások megtekintéséhez frissítenie kell az adatkészletet. További információ a DirectQuery használatának módjáról és idejéről: [DirectQuery használata a Power BI-ban](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Enter the HDInsight login account information, then select **Connect**. Az alapértelmezett fióknév az *admin*.

7. Select the `hvac` table, wait to see a preview of the data, and then select **Load**.

    ![Spark cluster user name and password](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark cluster user name and password")

    A Power BI Desktop rendelkezik a Spark-fürthöz való csatlakozáshoz és adatok a `hvac`-táblából való letöltéséhez szükséges információkkal. A tábla és annak oszlopai a **Mezők** panelen jelennek meg.

8. A célhőmérséklet és a tényleges hőmérséklet közötti eltérés vizualizációja az egyes épületek esetén:

    1. A **VIZUALIZÁCIÓK** panelen válassza a **Területdiagram** lehetőséget.

    2. Húzza a **BuildingID** mezőt a **Tengely** területre, az **ActualTemp** és a **TargetTemp** mezőket pedig az **Érték** területre.

        ![add value columns](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "add value columns")

        A diagram a következőképpen fog kinézni:

        ![area graph sum](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "area graph sum")

        Alapértelmezés szerint a vizualizáció az **ActualTemp** és a **TargetTemp** mezők összegét jeleníti meg. Select the down arrow next to **ActualTemp** and **TragetTemp** in the Visualizations pane, you can see **Sum** is selected.

    3. Select the down arrows next to **ActualTemp** and **TragetTemp** in the Visualizations pane, select **Average** to get an average of actual and target temperatures for each building.

        ![average of values](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "average of values")

        Az adatok vizualizációjának a képernyőképen láthatóhoz hasonlóan kell kinéznie. Ha az egérmutatót a vizualizáció fölé viszi, megtekintheti a kapcsolódó adatokhoz tartozó eszköztippeket.

        ![area graph](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "area graph")

9. Navigate to **File** > **Save**, enter the name `BuildingTemperature` for the file, then select **Save**.

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>A jelentés közzététele a Power BI szolgáltatásban (opcionális)

A Power BI szolgáltatás segítségével jelentéseket és irányítópultokat oszthat meg a vállalaton belül. Ebben a szakaszban először az adatkészlet és a jelentés közzétételét végezzük el. Ezután rögzíteni fogjuk a jelentést egy irányítópulton. Dashboards are typically used to focus on a subset of data in a report. You have only one visualization in your report, but it's still useful to go through the steps.

1. Nyissa meg a Power BI Desktopot.

1. A **Kezdőlapon** kattintson a **Közzététel** gombra.

    ![Közzététel a Power BI Desktopból](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Közzététel a Power BI Desktopból")

1. Válasszon ki egy munkaterületet az adatkészlet és a jelentés közzétételéhez, majd kattintson a **Kiválasztás** gombra. A következő képen az alapértelmezett **Saját munkaterület** mező van kiválasztva.

    ![Select workspace to publish dataset and report to](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Select workspace to publish dataset and report to") 

1. Ha a közzététel sikeresen megtörtént, kattintson a **„BuildingTemperature.pbix” fájl megnyitása a Power BI-ban** elemre.

    ![Publish success, click to enter credentials](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Publish success, click to enter credentials") 

1. A Power BI szolgáltatás felületén kattintson a **Hitelesítő adatok megadása** elemre.

    ![Enter credentials in Power BI service](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Enter credentials in Power BI service")

1. Kattintson a **Hitelesítő adatok szerkesztése** gombra.

    ![Edit credentials in Power BI service](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Edit credentials in Power BI service")

1. Adja meg a HDInsight bejelentkezési fiókadatait, majd kattintson a **Bejelentkezés** gombra. Az alapértelmezett fióknév az *admin*.

    ![Sign in to Spark cluster](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Sign in to Spark cluster")

1. A bal oldali panelen lépjen a **Munkaterületek** > **Saját munkaterület** > **JELENTÉSEK** részre, majd kattintson a **BuildingTemperature** elemre.

    ![Report listed under reports in left pane](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Report listed under reports in left pane")

    A **BuildingTemperature** elemnek a bal oldali ablaktábla **ADATKÉSZLETEK** területén is meg kell jelennie.

    A Power BI Desktopban létrehozott vizualizáció mostantól a Power BI szolgáltatásban is elérhető. 

1. Vigye az egérmutatót a vizualizáció fölé, majd kattintson a jobb felső sarokban lévő rögzítés ikonra.

    ![Report in the Power BI service](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Report in the Power BI service")

1. Válassza az „Új irányítópult” lehetőséget, adja meg a `Building temperature` nevet, majd kattintson a **Rögzítés** gombra.

    ![Pin to new dashboard](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Pin to new dashboard")

1. A jelentésben kattintson az **Ugrás az irányítópultra** gombra.

Ezzel rögzítette a vizualizációt az irányítópulton – felvehet további vizualizációkat is a jelentésbe, amelyeket ugyanezen az irányítópulton rögzíthet. For more information about reports and dashboards, see [Reports in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) and [Dashboards in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag befejezése után érdemes törölni a fürtöt. With HDInsight, your data is stored in Azure Storage, so you can safely delete a cluster when it isn't in use. You're also charged for an HDInsight cluster, even when it isn't in use. Since the charges for the cluster are many times more than the charges for storage, it makes economic sense to delete clusters when they aren't in use.

To delete a cluster, see [Delete an HDInsight cluster using your browser, PowerShell, or the Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Következő lépések

In this tutorial, you learned how to use [Microsoft Power BI](https://powerbi.microsoft.com/) to visualize data in an Apache Spark cluster in [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). Advance to the next article to see you can create a machine learning application.

> [!div class="nextstepaction"]
> [Create a machine learning application](./apache-spark-ipython-notebook-machine-learning.md)