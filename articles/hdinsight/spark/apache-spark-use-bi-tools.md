---
title: "Adatok képi megjelenítés eszközökkel on Azure HDInsight Spark BI |} Microsoft Docs"
description: "Adatok képi megjelenítés eszközök segítségével analytics Apache Spark BI használata a HDInsight-fürtökön"
keywords: "Apache spark üzleti intelligencia, spark bi, spark adatábrázolási, spark üzleti intelligencia"
services: hdinsight
documentationcenter: 
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: jgao
ms.openlocfilehash: fabf48da80cf44e82068d180c896ebbca7078d18
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI adatok képi megjelenítés eszközökkel Azure hdinsightban

Útmutató az Azure HDInsight az Apache Spark-fürt adatok megjelenítése Power BI és a Tableau használatával.

## <a name="prerequisites"></a>Előfeltételek

* A HDInsight az Apache Spark-fürt. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md).
* A fürt mintaadatok. Útmutatásért lásd: [interaktív lekérdezések futtatására egy HDInsight Spark-fürt](apache-spark-load-data-run-query.md).
* A Power BI: [Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop/) és [Power BI próba-előfizetés](https://app.powerbi.com/signupredirect?pbi_source=web) (nem kötelező).
* Tableau: [Tableau asztali](http://www.tableau.com/products/desktop) és [Microsoft Spark ODBC-illesztőprogram](http://go.microsoft.com/fwlink/?LinkId=616229).


## <a name="hivetable"></a>Tekintse át a mintaadatokat

A létrehozott Jupyter notebook a [az oktatóanyag előző](apache-spark-load-data-run-query.md) létrehozásához kódot tartalmazza egy `hvac` tábla. Ez a táblázat alapul a CSV-fájl érhető el az összes HDInsight Spark-fürtök: **\HdiSamples\HdiSamples\SensorSampleData\hvac\hvac.csv**. Tekintsük át az adatokat a Spark-fürt létrehozása a képi megjelenítés előtt.

1. Győződjön meg arról, hogy létezik-e a várt táblázat. Egy üres cellába a notebook, másolja a következő kódrészletet, és nyomja le az **SHIFT + ENTER**.

        %%sql
        SHOW TABLES

    Megjelenik az alábbihoz hasonló kimenetnek:

    ![A Spark táblák megjelenítése](./media/apache-spark-use-bi-tools/show-tables.png)

    Ha korábban bezárta a notebook az oktatóanyag elindítása előtt `hvactemptable` törlődnek, így nem szerepel a kimenetet.
    Csak hive táblák találhatók a metaadattárhoz (által jelzett **hamis** alatt a **isTemporary** oszlop) érhetők el a BI-eszközökkel. Ebben az oktatóanyagban nem csatlakozni a **hvac** létrehozott tábla.

2. Győződjön meg arról, hogy a tábla a várt adatokat tartalmazza. Egy üres cellába a notebook, másolja a következő kódrészletet, és nyomja le az **SHIFT + ENTER**.

        %%sql
        SELECT * FROM hvac LIMIT 10

    Megjelenik az alábbihoz hasonló kimenetnek:

    ![A Spark hvac tábla azon sorait megjelenítése](./media/apache-spark-use-bi-tools/select-limit.png)

3. Állítsa le a notebook az erőforrások kijelölése. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre.

## <a name="powerbi"></a>A Power BI használata Spark adatábrázolási

Most, hogy ellenőrizte, hogy, hogy létezik-e a várt adatokat, használhatja a Power BI ebből képi megjelenítéseket, jelentések és irányítópultok létrehozásához. Ebben a cikkben azt kell egy egyszerű példa, statikus adatok megjelenítése, de tudassa velünk, a Megjegyzések Ha azt szeretné, összetettebb adatfolyam példák.

### <a name="create-a-report-in-power-bi-desktop"></a>Hozzon létre egy jelentést a Power BI Desktop
Az első végzett munka során Spark lépésekre csatlakozzon a fürthöz, a Power BI Desktopban, adatok betöltése az a fürt és az adatok alapján alapvető képi megjelenítés létrehozása.

> [!NOTE]
> Az ebben a cikkben bemutatott összekötő jelenleg előzetes verzióban érhetők, de javasoljuk, hogy azt használja, és bármely visszajelzést keresztül kaphatnak a [Power BI-Közösség](https://community.powerbi.com/) hely vagy [Power BI ötleteket](https://ideas.powerbi.com/forums/265200-power-bi-ideas).

1. A Power BI Desktopban a a **Home** lapra, majd **adatok beolvasása**, majd **további**.

2. Keresse meg `Spark`, jelölje be **Azure HDInsight Spark**, majd kattintson a **Connect**.

    ![Adatok beolvasása a Power bi-bA az Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "adatok beolvasása a Power bi-bA az Apache Spark BI")

3. Adja meg a fürt URL-CÍMÉT (formájában `mysparkcluster.azurehdinsight.net`) elemre, jelölje be **DirectQuery**, majd kattintson a **OK**.

    ![Kapcsolódás az Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "Apache Spark BI kapcsolódni")

    > [!NOTE]
    > Spark vagy csatlakozási mód használatával. DirectQuery használatakor módosítások megjelennek jelentések a teljes adatkészlet frissítése nélkül. Ha importálja az adatokat, frissítenie kell az adatkészlet módosítások megtekintéséhez. További információt és a DirectQuery használatával, lásd: [DirectQuery használatával a Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-directquery-about/). 

4. Adja meg a HDInsight bejelentkezési fiók adatait a **felhasználónév** és **jelszó** (az alapértelmezett fiók `admin`), majd kattintson a **Connect**.

    ![Fürt felhasználónevet és jelszót Spark](./media/apache-spark-use-bi-tools/user-password.png "Spark fürt felhasználónév és jelszó")

5. Válassza ki a `hvac` tábla, és várjon, amíg az adatok villámnézetének megtekintéséhez. Kattintson a **terhelés**.

    ![Fürt felhasználónevet és jelszót Spark](./media/apache-spark-use-bi-tools/apache-spark-bi-select-table.png "Spark fürt felhasználónév és jelszó")

    A Power BI Desktop most már kapcsolódnia kell a Spark fürt és az adatok betöltése az összes adatot a `hvac` tábla. A táblázat és az oszlopait jelennek meg a **MEZŐK** ablaktáblán.

    ![Apache Spark BI irányítópulton lévő táblák listázása](./media/apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "Apache Spark BI irányítópulton lévő táblák listázása")

7. Cél hőmérséklet és a tényleges hőmérséklet rendszerbeli minden közötti eltérés megjelenítendő képi megjelenítés létrehozása: 

    1. Az a **képi megjelenítések** ablaktáblán válassza előbb **területdiagram**. Húzza a **BuildingID** mezőről **tengely**, majd húzza a **ActualTemp** és **TargetTemp** mező a **érték**.

        ![Hozzon létre a Spark Apache Spark BI használatával adatmegjelenítésekkel](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Spark létrehozása az adatmegjelenítéseket Apache Spark BI használatával")

    2. Alapértelmezés szerint a képi megjelenítés jeleníti meg az összegük **ActualTemp** és **TargetTemp**. Mind a mezőket, a legördülő listában válassza ki a **átlagos** tényleges átlagosan és a cél hőmérsékletek lekérése minden épület.

        ![Hozzon létre a Spark Apache Spark BI használatával adatmegjelenítésekkel](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Spark létrehozása az adatmegjelenítéseket Apache Spark BI használatával")

    3. Az adatok vizuális kell lennie ezen a képernyőfelvételen hasonló. Helyezze a kurzort a képi megjelenítés eszköztipp vonatkozó adatok eléréséhez.

        ![Hozzon létre a Spark Apache Spark BI használatával adatmegjelenítésekkel](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Spark létrehozása az adatmegjelenítéseket Apache Spark BI használatával")

11. Kattintson a **fájl** majd **mentése**, és adja meg a nevét `spark.pbix` a fájlt. 

### <a name="publish-the-report-to-the-power-bi-service-optional"></a>A jelentés közzététele a Power BI-ban (nem kötelező)
Már teljesen működőképes jelentést a Power BI Desktopban, és le is van, de sok ember szeretné, hogy a Power BI szolgáltatásba, így könnyen megosztható a jelentések és irányítópultok a szervezeten belüli előnyeit. 

Ebben a szakaszban beállíthatja a DataSet adatkészlet és a jelentést, amely létrehozta a Power BI Desktop-fájlban található. A képi megjelenítés a jelentésből irányítópultokhoz majd rögzíti. Irányítópultok általában használják; a jelentésben szereplő adatok egy részét összpontosíthat a jelentésben csak egy képi megjelenítés rendelkezik, de továbbra is hasznos lehet a szükséges lépéseket.

1. A Power BI Desktopban a a **Home** lapra, majd **közzététel**.

    ![A Power BI Desktop közzététele](./media/apache-spark-use-bi-tools/apache-spark-bi-publish.png "a Power BI Desktop közzététele")

2. Az adatkészlet közzététele egy munkaterület kiválasztása és a jelentést, majd kattintson a **válasszon**. Az alábbi ábrán, az alapértelmezett **saját munkaterület** van kiválasztva.

    ![Válassza ki a DataSet adatkészlet közzététele, és jelentse a munkaterület](./media/apache-spark-use-bi-tools/apache-spark-bi-select-workspace.png "adatkészlet közzététele, és jelentse a Select munkaterület") 

3. Miután a Power BI Desktop visszaad egy üzenetet, kattintson **nyissa meg a Power BI-ban "spark.pbix"**.

    ![Közzététel sikeres, kattintson a hitelesítő adatok](./media/apache-spark-use-bi-tools/apache-spark-bi-publish-success.png "közzététele sikeres, kattintson a hitelesítő adatok") 

4. A Power BI-ban, kattintson a **adja meg a hitelesítő adatok**.

    ![Adja meg a hitelesítő adatait a Power BI-ban](./media/apache-spark-use-bi-tools/apache-spark-bi-enter-credentials.png "adja meg a hitelesítő adatait a Power BI-ban")

5. Kattintson a **hitelesítő adatok szerkesztése**.

    ![A Power BI-ban hitelesítő adatok szerkesztése](./media/apache-spark-use-bi-tools/apache-spark-bi-edit-credentials.png "Power BI-ban hitelesítő adatok szerkesztése")

6. Adja meg a HDInsight bejelentkezési fiók adatait (általában az alapértelmezett `admin` a Power BI Desktop használt fiókkal), majd kattintson a **bejelentkezés**.

    ![Jelentkezzen be a Spark-fürt](./media/apache-spark-use-bi-tools/apache-spark-bi-sign-in.png "jelentkezzen be a Spark-fürt")

7. A bal oldali ablaktáblában lépjen **munkaterületek** > **saját munkaterület** > **jelentések**, majd kattintson a **spark**.

    ![A jelentés jelentések bal oldali ablaktáblában felsorolt](./media/apache-spark-use-bi-tools/apache-spark-bi-service-left-pane.png "jelentések bal oldali ablaktáblában felsorolt jelentés")

    Emellett meg kell jelennie **spark** tartozó **ADATKÉSZLETEK** a bal oldali ablaktáblán.

8. A Látványelem a Power BI Desktopban létrehozott már elérhető a szolgáltatásban. PIN-kód Látványelem-irányítópulthoz, vigye a visual, majd kattintson a PIN-kód ikonra.

    ![A Power BI-jelentés](./media/apache-spark-use-bi-tools/apache-spark-bi-service-report.png "a Power BI-jelentés")

9. Válassza ki az "Új irányítópult", adja meg a nevét `SparkDemo`, majd kattintson a **PIN-kód**.

    ![Rögzítés az irányítópulton új](./media/apache-spark-use-bi-tools/apache-spark-bi-pin-dashboard.png "rögzítés az irányítópulton új")

10. Kattintson a jelentés **irányítópult megnyitásához**. 

    ![Nyissa meg az irányítópulton](./media/apache-spark-use-bi-tools/apache-spark-bi-open-dashboard.png "nyissa meg az irányítópulton")

A visual rögzítve az irányítópulton – az egyéb látványelemek felvétele a jelentésbe, és az azonos irányítópulton rögzítheti őket. További információ a jelentések és irányítópultok: [jelentéseket a Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-reports/)és [Power BI-irányítópultjain](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).

## <a name="tableau"></a>A Spark adatábrázolási Tableau asztal használata

> [!NOTE]
> Ez a szakasz esetén csak az Azure HDInsight létrehozott Spark 1.5.2-fürtök alkalmazható.
>
>

1. Telepítés [Tableau asztali](http://www.tableau.com/products/desktop) az Apache Spark BI oktatóanyag futtató számítógépen.

2. Győződjön meg arról, hogy számítógépen is van telepítve a Microsoft Spark ODBC-illesztőprogram. Az illesztőprogram telepítése [Itt](http://go.microsoft.com/fwlink/?LinkId=616229).

1. Tableau asztal indításakor. Kattintson a bal oldali panelen való kapcsolódásra, közül **Spark SQL**. Ha a bal oldali ablaktáblán alapértelmezés szerint nem szerepel a Spark SQL, megtalálhatja azt kattintson **több kiszolgálók**.
2. A Spark SQL kapcsolati párbeszédpanelen adja meg az értékeket, a képernyőfelvételen látható módon, és kattintson a **OK**.

    ![Csatlakozás az Apache Spark BI fürthöz](./media/apache-spark-use-bi-tools/connect-to-tableau-apache-spark-bi.png "csatlakozás az Apache Spark BI fürthöz")

    A hitelesítési legördülő listákból **Microsoft Azure HDInsight szolgáltatás** lehetőség csak akkor, ha telepítette a [Microsoft Spark ODBC-illesztőprogram](http://go.microsoft.com/fwlink/?LinkId=616229) azon a számítógépen.
3. A következő képernyőn a a **séma** legördülő menüben kattintson a **található** ikonra, végül **alapértelmezett**.

    ![Található séma a következő Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-find-schema-apache-spark-bi.png "Apache Spark BI keresés sémája")
4. Az a **tábla** , majd kattintson a **található** ikonra kattintva ismét elérhető a fürt összes Hive táblák listázása. Megjelenik a **hvac** létrehozott korábban a notebook tábla.

    ![Az Apache Spark BI tábla található](./media/apache-spark-use-bi-tools/tableau-find-table-apache-spark-bi.png "Apache Spark bi keresési tábla")
5. Áthúzással a tábla a jobb felső mezőbe. Tableau importálja az adatokat, és a séma példának a vörös téglalappal alapján jeleníti meg.

    ![Vegyen fel táblák a Tableau Apache Spark BI](./media/apache-spark-use-bi-tools/tableau-add-table-apache-spark-bi.png "Tableau Apache Spark bi táblák hozzáadása")
6. Kattintson a **Munka1** lap bal alsó. Ellenőrizze, hogy a átlagos cél és a tényleges összes épület-hőmérsékletek jelennek meg. minden egyes dátum képi megjelenítés. Húzza **dátum** és **azonosító létrehozása** való **oszlopok** és **tényleges Temp**/**céloz Temp** való **sorok**. A **jelek**, jelölje be **terület** Spark adatábrázolási interaktív terület használandó.

     ![Adja hozzá a mezőket a Spark adatábrázolási](./media/apache-spark-use-bi-tools/spark-data-visualization-add-fields.png "Spark adatábrázolási mezők hozzáadása")
7. Alapértelmezés szerint a hőmérséklet mezők láthatók szerint összesítést. Ha az átlaghőmérséklet inkább megjeleníteni kívánt, akkor megteheti a legördülő listán, a alább látható módon.

    ![Átlagos Spark adatok vizuális megjelenítéshez tartozó hőmérséklet érvénybe](./media/apache-spark-use-bi-tools/spark-data-visualization-average-temperature.png "átlagos Spark adatok vizuális megjelenítéshez tartozó hőmérséklet igénybe")

8. Akkor is super-adhat egy hőmérséklet-leképezést keresztül, a másik cél- és a tényleges hőmérsékletek közötti különbség jobb arculatának eléréséhez. Mozgassa az egeret a alacsonyabb terület térkép sarkába, keretein belül a leíró alakzat kiemelt piros kör látható. A térkép húzza a többi leképezési felső, és az egérrel kiadási, amikor megjelenik a kijelölt piros téglalap alakú.

    ![A Spark adatábrázolási maps egyesítése](./media/apache-spark-use-bi-tools/spark-data-visualization-merge-maps.png "egyesítési leképezi a Spark adatábrázolási")

     Az adatok vizuális kell módosítani a képernyőfelvételen látható módon:

    ![A Spark adatábrázolási tableau kimeneti](./media/apache-spark-use-bi-tools/spark-data-visualization-tableau-output.png "Spark adatábrázolási Tableau kimenete")
9. Kattintson a **mentése** a munkalap mentéséhez. Irányítópultok létrehozása, és egy vagy több felvétele.

## <a name="next-steps"></a>Következő lépések

Amennyiben megtudta, hogyan hozzon létre egy fürtöt, hozzon létre a Spark adatkeretek lekérdezni adatokat, és majd érheti el, hogy az adatok Üzletiintelligencia-eszközök. Most már megtekintheti kezelheti a fürt erőforrásait, és egy HDInsight Spark-fürtben futó összes feladatot hibakeresési utasításokat.

* [Apache Spark-fürt erőforrásainak kezelése az Azure HDInsightban](apache-spark-resource-manager.md)
* [Apache Spark-fürtön futó feladatok nyomon követése és hibakeresése a HDInsightban](apache-spark-job-debugging.md)

