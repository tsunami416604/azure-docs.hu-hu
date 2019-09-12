---
title: 'Oktatóanyag: Apache Spark-adatelemzés az Azure HDInsight Power BI használatával '
description: Oktatóanyag – a Microsoft Power BI használata a tárolt HDInsight-fürtök megjelenítéséhez Apache Spark
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/16/2019
ms.openlocfilehash: 319668ac81b390f9a8bf70da36dd3cf1508f5c37
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885171"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Oktatóanyag: Apache Spark-adatelemzés a HDInsight Power BI használatával

Ebből az oktatóanyagból megtudhatja, hogyan jelenítheti meg a [Microsoft Power BIt](https://powerbi.microsoft.com/) az [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)lévő Apache Spark-fürtökben lévő adatmegjelenítéshez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Spark-adatok vizualizációja a Power BI használatával

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Fejezze be a [cikk oktatóanyagát: Adatterhelés és lekérdezések futtatása egy Apache Spark-fürtön az Azure](./apache-spark-load-data-run-query.md)HDInsight-ben.

* [Power bi Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Nem kötelező: [Power bi próbaverziós előfizetés](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Az adatok ellenőrzése

Az [előző oktatóanyagban](apache-spark-load-data-run-query.md) létrehozott `hvac` [Jupyter notebook](https://jupyter.org/) kódot tartalmaz egy tábla létrehozásához. Ez a táblázat az összes HDInsight Spark-fürtön `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`elérhető CSV-fájlon alapul. Az adatok ellenőrzéséhez kövesse az alábbi eljárást.

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

1. Nyissa meg a Power BI Desktop alkalmazást. Ha meg van nyitva, zárjuk be a megnyíló indítási képernyőt.

2. A **Kezdőlap** lapon navigáljon az >  **adatok lekérése** **továbbiak..** .

    ![Adatok lekérése a Power BI Desktopba a HDInsight Apache Sparkból](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Adatok lekérése a Power BI-ba az Apache Spark BI-ból")

3. A `Spark` keresőmezőbe írja be a **Azure HDInsight Spark**, majd válassza a **kapcsolat**lehetőséget.

    ![Adatok lekérése a Power BI-ba az Apache Spark BI-ból](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Adatok lekérése a Power BI-ba az Apache Spark BI-ból")

4. Adja meg a fürt URL-címét ( `mysparkcluster.azurehdinsight.net`az űrlapon) a **kiszolgáló** szövegmezőben.

5. Az **adatkapcsolati mód**területen válassza a **DirectQuery**lehetőséget. Ezután kattintson az **OK** gombra.

    A Sparkkal bármelyik adatkapcsolati módot használhatja. A DirectQuery használatakor a módosítások anélkül jelennek meg a jelentésekben, hogy a teljes adatkészletet frissíteni kellene. Ha importálja az adatokat, a módosítások megtekintéséhez frissítenie kell az adatkészletet. További információ a DirectQuery használatának módjáról és idejéről: [DirectQuery használata a Power BI-ban](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Adja meg a HDInsight bejelentkezési fiókjának adatait, majd válassza a **kapcsolat**lehetőséget. Az alapértelmezett fióknév az *admin*.

7. Válassza ki `hvac` a táblázatot, várjon, amíg megtekinti az adatelőnézetet, majd válassza a **Betöltés**lehetőséget.

    ![Spark-fürt felhasználóneve és jelszava](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark-fürt felhasználóneve és jelszava")

    A Power BI Desktop rendelkezik a Spark-fürthöz való csatlakozáshoz és adatok a `hvac`-táblából való letöltéséhez szükséges információkkal. A tábla és annak oszlopai a **Mezők** panelen jelennek meg.

8. A célhőmérséklet és a tényleges hőmérséklet közötti eltérés vizualizációja az egyes épületek esetén:

    1. A **VIZUALIZÁCIÓK** panelen válassza a **Területdiagram** lehetőséget.

    2. Húzza a **BuildingID** mezőt a **Tengely** területre, az **ActualTemp** és a **TargetTemp** mezőket pedig az **Érték** területre.

        ![érték oszlopok hozzáadása](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "érték oszlopok hozzáadása")

        A diagram a következőképpen fog kinézni:

        ![körzeti gráf összege](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "körzeti gráf összege")

        Alapértelmezés szerint a vizualizáció az **ActualTemp** és a **TargetTemp** mezők összegét jeleníti meg. Válassza ki a **ActualTemp** és a **TragetTemp** melletti lefelé mutató nyilat a vizualizációk ablaktáblán, amelyen a **Sum (összeg** ) lehetőség van kiválasztva.

    3. Válassza ki a **ActualTemp** és a **TragetTemp** melletti lefelé mutató nyilat a vizualizációk ablaktáblán, és válassza az **átlag** lehetőséget az egyes épületek tényleges és megcélzott hőmérsékletének lekéréséhez.

        ![értékek átlaga](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "értékek átlaga")

        Az adatok vizualizációjának a képernyőképen láthatóhoz hasonlóan kell kinéznie. Ha az egérmutatót a vizualizáció fölé viszi, megtekintheti a kapcsolódó adatokhoz tartozó eszköztippeket.

        ![körzeti gráf](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "körzeti gráf")

9. Navigáljon a **fájl** > **mentése**mezőbe, írja `BuildingTemperature` be a fájl nevét, majd kattintson a **Mentés**gombra.

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

Ezzel rögzítette a vizualizációt az irányítópulton – felvehet további vizualizációkat is a jelentésbe, amelyeket ugyanezen az irányítópulton rögzíthet. A jelentésekkel és irányítópultokkal kapcsolatos további információkért tekintse meg a Power BI Power BI és [irányítópultok](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/) [jelentései](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) című témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Fürt törléséhez tekintse [meg a HDInsight-fürt törlése a böngészőben, a PowerShell vagy az Azure CLI használatával](../hdinsight-delete-cluster.md)című témakört.

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtudhatta, hogyan jelenítheti meg a [Microsoft Power BIt](https://powerbi.microsoft.com/) az [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/)lévő Apache Spark-fürtökben lévő információk megjelenítéséhez. A következő cikkben megismerkedhet a Sparkban regisztrált adatok olyan BI-elemzési eszközökbe történő áthúzásának lehetőségével, mint például a Power BI.

> [!div class="nextstepaction"]
> [Apache Spark streaming-feladatok futtatása](apache-spark-eventhub-streaming.md)