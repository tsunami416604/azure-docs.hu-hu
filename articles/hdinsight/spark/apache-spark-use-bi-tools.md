---
title: 'Oktatóanyag: Az Azure HDInsight Apache Spark-adatok elemzése a Power BI-val'
description: Oktatóanyag – A Microsoft Power BI használata az Apache Spark tárolt HDInsight-fürtjeinek megjelenítéséhez
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 04/21/2020
ms.openlocfilehash: dd0b4d1b0998bd4b13a17fb8345b87924bc27e1d
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869970"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Oktatóanyag: Apache Spark-adatok elemzése a POWER BI használatával a HDInsightban

Ebben az oktatóanyagban megtudhatja, hogyan jelenítheti meg az adatokat az Azure HDInsight ban az Apache Spark-fürtökben az Azure Power BI használatával.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Spark-adatok vizualizációja a Power BI használatával

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

* Végezze el az [Oktatóanyag: Adatok betöltése és lekérdezések futtatása az Azure HDInsight egy Apache Spark-fürtjén cikkben leírtakat](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Nem kötelező: [Power BI próbaverziós előfizetés](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Az adatok ellenőrzése

Az [előző oktatóanyagban](apache-spark-load-data-run-query.md) létrehozott [Jupyter-jegyzetfüzet](https://jupyter.org/) tartalmazza `hvac` a tábla létrehozásához kívánt kódot. Ez a táblázat az összes HDInsight Spark-fürtön `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`elérhető CSV-fájlon alapul. Az adatok ellenőrzéséhez kövesse az alábbi eljárást.

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

1. Nyissa meg a Power BI Desktopot. Zárja be az indítási kezdőképet, ha megnyílik.

2. A **Kezdőlap** lapon keresse meg az Adatok további >  **bekerülése****lapot. .**.

    ![Adatok beszerezése a Power BI Desktopba a HDInsight Apache Sparktól](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Adatok beszerezése a Power BI-ba az Apache Spark BI-tól")

3. Írja `Spark` be a keresőmezőbe az **Azure HDInsight Spark**lehetőséget, majd a **Csatlakozás**lehetőséget.

    ![Adatok beszerezése a Power BI-ba az Apache Spark BI-tól](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Adatok beszerezése a Power BI-ba az Apache Spark BI-tól")

4. Írja be a fürt `mysparkcluster.azurehdinsight.net`URL-címét (az űrlapon) a **Kiszolgáló** mezőbe.

5. Az **Adatkapcsolat módban**válassza a **DirectQuery**lehetőséget. Ezután kattintson az **OK** gombra.

    A Sparkkal bármelyik adatkapcsolati módot használhatja. A DirectQuery használatakor a módosítások anélkül jelennek meg a jelentésekben, hogy a teljes adatkészletet frissíteni kellene. Ha importálja az adatokat, a módosítások megtekintéséhez frissítenie kell az adatkészletet. További információ a DirectQuery használatának módjáról és idejéről: [DirectQuery használata a Power BI-ban](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Adja meg a HDInsight bejelentkezési fiók adatait, majd válassza a **Csatlakozás lehetőséget.** Az alapértelmezett fióknév az *admin*.

7. Jelölje `hvac` ki a táblázatot, várja meg az adatok előnézetét, majd válassza a **Betöltés**lehetőséget.

    ![Spark-fürt felhasználóneve és jelszava](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark-fürt felhasználóneve és jelszava")

    A Power BI Desktop rendelkezik a Spark-fürthöz való csatlakozáshoz és adatok a `hvac`-táblából való letöltéséhez szükséges információkkal. A tábla és annak oszlopai a **Mezők** panelen jelennek meg.

8. A célhőmérséklet és a tényleges hőmérséklet közötti eltérés vizualizációja az egyes épületek esetén:

    1. A **VIZUALIZÁCIÓK** panelen válassza a **Területdiagram** lehetőséget.

    2. Húzza a **BuildingID** mezőt a **Tengely** területre, az **ActualTemp** és a **TargetTemp** mezőket pedig az **Érték** területre.

        ![hozzáadott értékoszlopok](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "hozzáadott értékoszlopok")

        A diagram a következőképpen fog kinézni:

        ![területdiagram összege](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "területdiagram összege")

        Alapértelmezés szerint a vizualizáció az **ActualTemp** és a **TargetTemp** mezők összegét jeleníti meg. Jelölje ki az **ActualTemp** és a **TragetTemp** melletti lefelé mutató nyilat a Vizualizációk ablaktáblában, és láthatja, hogy az **Összeg** ki van jelölve.

    3. Válassza ki az **ActualTemp** és a **TragetTemp** elem melletti lefelé mutató nyilakat a Vizualizációk ablaktáblában, és válassza az **Átlag** lehetőséget az egyes épületek tényleges és célhőmérsékletének összegzéséhez.

        ![értékek átlaga](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "értékek átlaga")

        Az adatok vizualizációjának a képernyőképen láthatóhoz hasonlóan kell kinéznie. Ha az egérmutatót a vizualizáció fölé viszi, megtekintheti a kapcsolódó adatokhoz tartozó eszköztippeket.

        ![területdiagram](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "területdiagram")

9. Nyissa meg a > **Fájlmentés**gombot, írja be a fájl nevét, `BuildingTemperature` majd válassza a **Mentés gombot.** **File**

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>A jelentés közzététele a Power BI szolgáltatásban (opcionális)

A Power BI szolgáltatás segítségével jelentéseket és irányítópultokat oszthat meg a vállalaton belül. Ebben a szakaszban először az adatkészlet és a jelentés közzétételét végezzük el. Ezután rögzíteni fogjuk a jelentést egy irányítópulton. Az irányítópultok általában a jelentés adatainak egy részhalmazára összpontosítanak. Csak egy vizualizáció van a jelentésben, de még mindig hasznos, ha végigmegy a lépéseken.

1. Nyissa meg a Power BI Desktopot.

1. A **Kezdőlap** lapon válassza a **Közzététel**gombot.

    ![Közzététel a Power BI Desktopról](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "Közzététel a Power BI Desktopból")

1. Jelölje ki azt a munkaterületet, ahol közzé szeretné tenni az adatkészletet, majd válassza a **Kijelölés lehetőséget.** A következő képen az alapértelmezett **Saját munkaterület** mező van kiválasztva.

    ![Válassza ki a munkaterületet az adatkészlet közzétételéhez és a jelentéshez](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "Válassza ki a munkaterületet az adatkészlet közzétételéhez és a jelentéshez")

1. A közzététel sikeresse, válassza **a "BuildingTemperature.pbix megnyitása" lehetőséget a Power BI-ban.**

    ![Sikeres közzététel, kattintson a hitelesítő adatok megadására](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "Sikeres közzététel, kattintson a hitelesítő adatok megadására")

1. A Power BI szolgáltatásban válassza a **Hitelesítő adatok megadása**lehetőséget.

    ![Hitelesítő adatok megadása a Power BI szolgáltatásban](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "Hitelesítő adatok megadása a Power BI szolgáltatásban")

1. Válassza **a Hitelesítő adatok szerkesztése lehetőséget.**

    ![Hitelesítő adatok szerkesztése a Power BI szolgáltatásban](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Hitelesítő adatok szerkesztése a Power BI szolgáltatásban")

1. Adja meg a HDInsight bejelentkezési fiókadatait, majd válassza **a Bejelentkezés**lehetőséget. Az alapértelmezett fióknév az *admin*.

    ![Bejelentkezés a Spark-fürtbe](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "Bejelentkezés a Spark-fürtbe")

1. A bal oldali ablaktáblában nyissa meg **a Munkaterületek** > **saját munkaterület-jelentések** > **lehetőséget,** majd válassza **a BuildingTemperature**lehetőséget.

    ![Jelentés a bal oldali ablaktáblában található jelentések alatt](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "Jelentés a bal oldali ablaktáblában található jelentések alatt")

    A **BuildingTemperature** elemnek a bal oldali ablaktábla **ADATKÉSZLETEK** területén is meg kell jelennie.

    A Power BI Desktopban létrehozott vizualizáció mostantól a Power BI szolgáltatásban is elérhető.

1. Vigye a mutatót a képi megjelenítés fölé, majd jelölje ki a jobb felső sarokban lévő pin ikont.

    ![Jelentés a Power BI szolgáltatásban](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "Jelentés a Power BI szolgáltatásban")

1. Válassza az "Új irányítópult" `Building temperature`lehetőséget, írja be a nevet , majd a **Rögzítés**lehetőséget.

    ![Rögzítés az új irányítópultra](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "Rögzítés az új irányítópultra")

1. A jelentésben válassza az Ugrás az **irányítópultra**lehetőséget.

Ezzel rögzítette a vizualizációt az irányítópulton – felvehet további vizualizációkat is a jelentésbe, amelyeket ugyanezen az irányítópulton rögzíthet. A jelentésekről és az irányítópultokról a [Jelentések a Power BI-ban](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) és [az Irányítópultok a Power BI-ban](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)című témakörben talál további információt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight segítségével az adatok az Azure Storage-ban tárolódnak, így biztonságosan törölheti a fürtöt, ha nincs használatban. A HDInsight-fürtért is díjat kell fizetnie, még akkor is, ha nincs használatban. Mivel a fürt díjai sokszor több, mint a tárolási díjak, célszerű törölni a fürtöket, ha nincsenek használatban.

Fürt törléséről a [HDInsight-fürt törlése a böngésző, a PowerShell vagy az Azure CLI használatával című](../hdinsight-delete-cluster.md)témakörben jelenik meg.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtanulta, hogyan használhatja a Microsoft Power BI-t az Azure HDInsight Apache Spark-fürtjében lévő adatok megjelenítésére. Tekintse át a következő cikket, és tekintse meg, hogy létrehozhat egy gépi tanulási alkalmazást.

> [!div class="nextstepaction"]
> [Gépi tanulási alkalmazás létrehozása](./apache-spark-ipython-notebook-machine-learning.md)