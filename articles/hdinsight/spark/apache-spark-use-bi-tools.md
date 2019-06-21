---
title: 'Oktatóanyag: A Power BI használatával az Azure HDInsight az Apache Spark-adatok elemzése '
description: Oktatóanyag – a Microsoft Power BI segítségével ábrázolhatja az Apache Spark-adatait tárolja a HDInsight-fürtök
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: tutorial
ms.date: 05/16/2019
ms.openlocfilehash: d5296fe19cef9e8881d39bd9e59eb4c40d049959
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296188"
---
# <a name="tutorial-analyze-apache-spark-data-using-power-bi-in-hdinsight"></a>Oktatóanyag: A Power BI használata a HDInsight az Apache Spark-adatok elemzése

Ebben az oktatóanyagban megismerheti, hogyan használható [Microsoft Power BI](https://powerbi.microsoft.com/) Apache Spark-fürtön az adatok megjelenítése [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Spark-adatok vizualizációja a Power BI használatával

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

* Végezze el a cikk [oktatóanyag: Adatok betöltése, és lekérdezéseket futtathat az Azure HDInsight az Apache Spark-fürt](./apache-spark-load-data-run-query.md).

* [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/).

* Nem kötelező: [A Power BI próba-előfizetés](https://app.powerbi.com/signupredirect?pbi_source=web).

## <a name="verify-the-data"></a>Az adatok ellenőrzése

A [Jupyter Notebook](https://jupyter.org/) , amelyet a [előző oktatóanyagban](apache-spark-load-data-run-query.md) kód létrehozása egy `hvac` tábla. Ez a táblázat alapján a CSV-fájl érhető el az összes HDInsight Spark-fürtök, `\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv`. Az adatok ellenőrzéséhez kövesse az alábbi eljárást.

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

1. Nyissa meg a Power BI Desktop alkalmazást. Ha megnyílik, zárja be az indítási kezdőképernyőjén.

2. Az a **kezdőlap** lapot, navigáljon a **adatok lekérése** > **több...** .

    ![Adatok lekérése a Power BI Desktopba a HDInsight Apache Sparkból](./media/apache-spark-use-bi-tools/hdinsight-spark-power-bi-desktop-get-data.png "Adatok lekérése a Power BI-ba az Apache Spark BI-ból")

3. Adja meg `Spark` jelölje be a keresőmezőbe, **Azure HDInsight Spark**, majd válassza ki **Connect**.

    ![Adatok lekérése a Power BI-ba az Apache Spark BI-ból](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "Adatok lekérése a Power BI-ba az Apache Spark BI-ból")

4. Adja meg a fürt URL-címe (a képernyő `mysparkcluster.azurehdinsight.net`) az a **kiszolgáló** szövegmező.

5. A **adatkapcsolati mód**válassza **DirectQuery**. Ezután kattintson az **OK** gombra.

    A Sparkkal bármelyik adatkapcsolati módot használhatja. A DirectQuery használatakor a módosítások anélkül jelennek meg a jelentésekben, hogy a teljes adatkészletet frissíteni kellene. Ha importálja az adatokat, a módosítások megtekintéséhez frissítenie kell az adatkészletet. További információ a DirectQuery használatának módjáról és idejéről: [DirectQuery használata a Power BI-ban](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/).

6. Adja meg a HDInsight bejelentkezési fiók adatait, majd válassza ki **Connect**. Az alapértelmezett fióknév az *admin*.

7. Válassza ki a `hvac` táblát, várja meg, hogy az adatok előnézetét, és válassza ki **terhelés**.

    ![Spark-fürt felhasználóneve és jelszava](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark-fürt felhasználóneve és jelszava")

    A Power BI Desktop rendelkezik a Spark-fürthöz való csatlakozáshoz és adatok a `hvac`-táblából való letöltéséhez szükséges információkkal. A tábla és annak oszlopai a **Mezők** panelen jelennek meg.

8. A célhőmérséklet és a tényleges hőmérséklet közötti eltérés vizualizációja az egyes épületek esetén:

    1. A **VIZUALIZÁCIÓK** panelen válassza a **Területdiagram** lehetőséget.

    2. Húzza a **BuildingID** mezőt a **Tengely** területre, az **ActualTemp** és a **TargetTemp** mezőket pedig az **Érték** területre.

        ![Spark-adatvizualizációk létrehozása az Apache Spark BI használatával](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Spark-adatvizualizációk létrehozása az Apache Spark BI használatával")

        A diagram a következőképpen fog kinézni:

        ![Spark-adatvizualizációk létrehozása az Apache Spark BI használatával](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph-sum.png "Spark-adatvizualizációk létrehozása az Apache Spark BI használatával")

        Alapértelmezés szerint a vizualizáció az **ActualTemp** és a **TargetTemp** mezők összegét jeleníti meg. Válassza ki a lefelé mutató nyílra a **ActualTemp** és **TragetTemp** a megjelenítések ablaktáblán látható **Sum** van kiválasztva.

    3. Válassza ki a lefelé nyíl melletti **ActualTemp** és **TragetTemp** a Vizualizációk ablaktáblán válassza ki a **átlagos** , az átlagos tényleges, és a cél hőmérsékletek minden létrehozásához.

        ![Spark-adatvizualizációk létrehozása az Apache Spark BI használatával](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Spark-adatvizualizációk létrehozása az Apache Spark BI használatával")

        Az adatok vizualizációjának a képernyőképen láthatóhoz hasonlóan kell kinéznie. Ha az egérmutatót a vizualizáció fölé viszi, megtekintheti a kapcsolódó adatokhoz tartozó eszköztippeket.

        ![Spark-adatvizualizációk létrehozása az Apache Spark BI használatával](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Spark-adatvizualizációk létrehozása az Apache Spark BI használatával")

9. Navigáljon a **fájl** > **mentése**, adja meg a nevét `BuildingTemperature` a fájlhoz, majd válassza ki **mentése**.

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

Ezzel rögzítette a vizualizációt az irányítópulton – felvehet további vizualizációkat is a jelentésbe, amelyeket ugyanezen az irányítópulton rögzíthet. Jelentések és irányítópultok kapcsolatos további információkért lásd: [Power BI-jelentések](https://powerbi.microsoft.com/documentation/powerbi-service-reports/) és [Power BI-irányítópultok](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag befejezése után érdemes törölni a fürtöt. A HDInsight az Azure Storage szolgáltatásban tárolja az adatokat, így biztonságosan törölhet olyan fürtöket, amelyek nincsenek használatban. Ráadásul a HDInsight-fürtök akkor is díjkötelesek, amikor éppen nincsenek használatban. Mivel a fürt költsége a sokszorosa a tároló költségeinek, gazdaságossági szempontból is ésszerű törölni a használaton kívüli fürtöket.

Törölje a fürtöt, tekintse meg a [törlése egy HDInsight-fürtön a böngészőben, a PowerShell vagy az Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megtudhatta, hogyan használható [Microsoft Power BI](https://powerbi.microsoft.com/) Apache Spark-fürtön az adatok megjelenítése [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/). A következő cikkben megismerkedhet a Sparkban regisztrált adatok olyan BI-elemzési eszközökbe történő áthúzásának lehetőségével, mint például a Power BI.

> [!div class="nextstepaction"]
> [Az Apache Spark streamelési feladat futtatása](apache-spark-eventhub-streaming.md)