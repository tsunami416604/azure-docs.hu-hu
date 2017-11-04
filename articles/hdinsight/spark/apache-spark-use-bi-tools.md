---
title: "Adatok képi megjelenítés eszközökkel on Azure HDInsight Spark BI |} Microsoft Docs"
description: "Adatok képi megjelenítés eszközök segítségével analytics Apache Spark BI használata a HDInsight-fürtökön"
keywords: "Apache spark üzleti intelligencia, spark bi, spark adatábrázolási, spark üzleti intelligencia"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1448b536-9bc8-46bc-bbc6-d7001623642a
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: nitinme
ms.openlocfilehash: d1d5405a635b9f990f53b2bf32c8270a71a0f344
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="apache-spark-bi-using-data-visualization-tools-with-azure-hdinsight"></a>Apache Spark BI adatok képi megjelenítés eszközökkel Azure hdinsightban

Útmutató adatok képi megjelenítés eszközöket, például a Power BI és a Tableau használatával elemezheti a nyers minta adatkészletet, Apache Spark BI használata a HDInsight-fürtökön.

> [!NOTE]
> Ebben a cikkben leírt Üzletiintelligencia-eszközök kapcsolattal nem támogatott Spark 2.1 a Azure HDInsight 3.6 Preview-ban. Csak a Spark verziók 1.6-os és 2.0 (HDInsight 3.4, 3.5 rendre) támogatottak.
>

Ez az oktatóanyag egy HDInsight Spark-fürt Jupyter notebook, is érhető el. A notebook élmény lehetővé teszi a Python kódtöredékek futtassa a notebook magát. Jegyzetfüzet belül az oktatóprogram elvégzéséhez Spark-fürt létrehozása, indítsa el a Jupyter notebook (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), és futtassa a notebook **használata BI eszközök az Apache Spark on HDInsight.ipynb** alatt a **Python** mappa.

## <a name="prerequisites"></a>Előfeltételek

* A HDInsight az Apache Spark-fürt. Útmutatásért lásd: [létrehozása az Apache Spark on Azure hdinsight clusters](apache-spark-jupyter-spark-sql.md).


## <a name="hivetable"></a>Adatok előkészítése Spark adatábrázolási

Ebben a szakaszban használjuk a [Jupyter](https://jupyter.org) egy HDInsight Spark-fürt, amely a nyers mintaadatok feldolgozni, és mentse egy tábla feladatok futtatása a notebook. A mintaadatok egy CSV-fájlt (hvac.csv) elérhető alapértelmezés szerint minden fürtön. Az adatok táblázatként mentése után a következő szakaszban használjuk Üzletiintelligencia-eszközök a tábla csatlakozhat, és hajtsa végre az adatmegjelenítéseket.

> [!NOTE]
> Ha hajtja végre a lépéseket a cikkben található utasítások végrehajtása után [interaktív lekérdezések futtatására egy HDInsight Spark-fürt](apache-spark-load-data-run-query.md), ugorjon az alábbi 8. lépés.
>

1. Az [Azure portál](https://portal.azure.com/) kezdőpultján kattintson a Spark-fürthöz tartozó csempére (ha rögzítette azt a kezdőpulton). A fürtöt a következő helyről is megkeresheti: **Browse All (Összes tallózása)** > **HDInsight Clusters** (HDInsight-fürtök).   

2. A Spark-fürt panelén kattintson a **Fürt irányítópultja Dashboard** lehetőségre, majd a **Jupyter Notebook** elemre. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

   > [!NOTE]
   > A fürthöz tartozó Jupyter notebookot az alábbi URL-cím böngészőben történő megnyitásával is elérheti. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   >
   >

3. Hozzon létre egy notebookot. Kattintson a **New** (Új), majd a **PySpark** elemre.

    ![Jupyter notebook létrehozása az Apache Spark BI](./media/apache-spark-use-bi-tools/create-jupyter-notebook-for-spark-bi.png "Apache Spark bi Jupyter notebook létrehozása")

4. Az új notebook létrejött, és Untitled.pynb néven nyílt meg. A felső részen kattintson a notebook nevére, és adjon meg egy könnyen megjegyezhető nevet.

    ![Adjon meg egy nevet a notebook Apache Spark bi](./media/apache-spark-use-bi-tools/jupyter-notebook-name-for-spark-bi.png "adjon meg egy nevet a notebook Apache Spark BI")

5. Mivel a notebook PySpark kernel használatával jött létre, explicit módon semmilyen tartalmat nem kell létrehozni. Az első kódcella futtatásakor a Spark- és Hive-környezetek automatikusan létrejönnek. Ennek az első lépése a jelen forgatókönyvhöz szükséges típusok importálása. Ehhez az szükséges, vigye a kurzort a cella, és nyomja le az **SHIFT + ENTER**.

        from pyspark.sql import *

6. Töltse be a mintaadatokat egy ideiglenes táblába. Spark-fürt HDInsightban történő létrehozásakor a **hvac.csv** mintaadatfájl a hozzá tartozó tárfiókba lesz átmásolva a következő helyre: **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    Egy üres cellába, illessze be a következő kódrészletet, és nyomja le az **SHIFT + ENTER**. Ezt a kódrészletet regisztrálja az adatokat a következő táblába **hvac**.

        # Create an RDD from sample data
        hvacText = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        # Create a schema for our data
        Entry = Row('Date', 'Time', 'TargetTemp', 'ActualTemp', 'BuildingID')

        # Parse the data and create a schema
        hvacParts = hvacText.map(lambda s: s.split(',')).filter(lambda s: s[0] != 'Date')
        hvac = hvacParts.map(lambda p: Entry(str(p[0]), str(p[1]), int(p[2]), int(p[3]), int(p[6])))

        # Infer the schema and create a table       
        hvacTable = sqlContext.createDataFrame(hvac)
        hvacTable.registerTempTable('hvactemptable')
        dfw = DataFrameWriter(hvacTable)
        dfw.saveAsTable('hvac')

7. Győződjön meg arról, hogy a tábla létrehozása sikeresen megtörtént. Használhatja a `%%sql` Hive futtatásához magic közvetlenül lekérdezi. A `%%sql` funkcióval, illetve a PySpark kernellel elérhető egyéb funkciókkal kapcsolatos további információkat [A Spark HDInsight-fürtökkel használt Jupyter notebookokban elérhető kernelek](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic) című részben talál.

        %%sql
        SHOW TABLES

    Hasonló kimenetnek látja, lent látható módon:

        +---------------+-------------+
        |tableName      |isTemporary  |
        +---------------+-------------+
        |hvactemptable  |true        |
        |hivesampletable|false        |
        |hvac           |false        |
        +---------------+-------------+

    Csak azokat a táblákat, amelyek a FALSE értékre a **isTemporary** oszlop, amely a metaadattárhoz tárolódnak, és elérhető az Üzletiintelligencia-eszközök hive táblák. Ebben az oktatóanyagban nem csatlakozni a **hvac** létrehozott tábla.

8. Győződjön meg arról, hogy a tábla tartalmazza-e a kívánt adatokat. Egy üres cellába a notebook, másolja a következő kódrészletet, és nyomja le az **SHIFT + ENTER**.

        %%sql
        SELECT * FROM hvac LIMIT 10

9. Állítsa le a notebook az erőforrások kijelölése. Ehhez a notebook **File** (Fájl) menüjében kattintson a **Close and Halt** (Bezárás és leállítás) elemre.

## <a name="powerbi"></a>A Power BI használata Spark adatábrázolási

> [!NOTE]
> Ez a szakasz esetén csak a HDInsight 3.4 Spark 1.6-os és Spark 2.0 a HDInsight 3.5 alkalmazható.
>
>

A mentést követően az adatok táblázatként, a Power BI segítségével csatlakozzon a adataihoz, és jelenítheti meg, hogy hozzon létre a jelentések, irányítópultok, stb.

1. Győződjön meg arról, hogy hozzáfér a Power bi-ban. A Power bi-ban az ingyenes kiadásra előfizetői kaphat [http://www.powerbi.com/](http://www.powerbi.com/).

2. Jelentkezzen be [a Power BI](http://www.powerbi.com/).

3. A bal oldali ablaktábla alján kattintson **adatok beolvasása**.

4. A a **adatok beolvasása** lap **importálása vagy adatok kapcsolódás**, a **adatbázisok**, kattintson a **beolvasása**.

    ![Adatok beolvasása a Power bi-bA az Apache Spark BI](./media/apache-spark-use-bi-tools/apache-spark-bi-import-data-power-bi.png "adatok beszerzése Apache Spark BI Power BI-bA")

5. A következő képernyőn kattintson a **a Spark on Azure HDInsight** majd **Connect**. Amikor a rendszer kéri, adja meg a fürt URL-CÍMÉT (`mysparkcluster.azurehdinsight.net`) és a hitelesítő adatokat. csatlakozzon a fürthöz.

    ![Kapcsolódás az Apache Spark BI](./media/apache-spark-use-bi-tools/connect-to-apache-spark-bi.png "Apache Spark BI kapcsolódni")

    A kapcsolat létrejötte után a Power BI elindítja adatok importálását a HDInsight Spark-fürt.

6. A Power BI importálja az adatokat, és hozzáadja a **Spark** adatkészlet alapján a **adatkészletek** fejléc. Kattintson az adatkészlet nyisson meg egy új munkalapra lesznek adatok megjelenítéséhez. A munkalap jelentést is mentheti. Munkalap mentése a **fájl** menüben kattintson a **mentése**.

    ![A Power BI irányítópult Apache Spark BI csempéjén](./media/apache-spark-use-bi-tools/apache-spark-bi-tile-dashboard.png "Apache Spark BI csempét a Power BI-irányítópulton")
7. Figyelje meg, hogy a **mezők** listáján a jobb oldali listák a **hvac** korábban létrehozott tábla. Bontsa ki az alábbi táblázatban a mezőket a táblázatban, korábban definiált jegyzetfüzet.

      ![Apache Spark BI irányítópulton lévő táblák listázása](./media/apache-spark-use-bi-tools/apache-spark-bi-display-tables.png "Apache Spark BI irányítópulton lévő táblák listázása")

8. Cél hőmérséklet és a tényleges hőmérséklet rendszerbeli minden közötti eltérés megjelenítendő képi megjelenítés létrehozása. Adatok megjelenítése, válassza ki a **területdiagram** (vörös téglalappal látható). A tengely fogd és vidd megadhatók a **BuildingID** eleménél **tengely**, és **ActualTemp**/**TargetTemp** a mezők **érték**.

    ![Hozzon létre a Spark Apache Spark BI használatával adatmegjelenítésekkel](./media/apache-spark-use-bi-tools/apache-spark-bi-add-value-columns.png "Spark létrehozása az adatmegjelenítéseket Apache Spark BI használatával")

9. Alapértelmezés szerint a képi megjelenítés jeleníti meg az összegük **ActualTemp** és **TargetTemp**. Mindkét a mezőket, a legördülő menüben válassza a **átlagos** le tényleges átlagosan és a cél hőmérsékletek mindkét épületekhez.

    ![Hozzon létre a Spark Apache Spark BI használatával adatmegjelenítésekkel](./media/apache-spark-use-bi-tools/apache-spark-bi-average-of-values.png "Spark létrehozása az adatmegjelenítéseket Apache Spark BI használatával")

10. Az adatok vizuális kell lennie ezen a képernyőfelvételen hasonló. Helyezze a kurzort a képi megjelenítés eszköztipp vonatkozó adatok eléréséhez.

    ![Hozzon létre a Spark Apache Spark BI használatával adatmegjelenítésekkel](./media/apache-spark-use-bi-tools/apache-spark-bi-area-graph.png "Spark létrehozása az adatmegjelenítéseket Apache Spark BI használatával")

11. Kattintson a **mentése** a felső menüben, és adja meg a jelentés neve. A Látványelem is rögzítheti. A képi megjelenítés rögzíti, amikor tárolása az irányítópulton, a legutóbbi értékét egy pillanat alatt követheti nyomon.

   Az ugyanahhoz az adatkészlethez tartozó és az adatok pillanatképe az irányítópulton rögzítheti őket annyi képi megjelenítések adhat hozzá. Emellett a HDInsight Spark-fürtök csatlakoznak a Power BI közvetlen kapcsolódás. Ez biztosítja, hogy Power BI mindig rendelkezik a lehető legfrissebb adatokat a fürtről, így Önnek nem kell ütemezhetők be frissítések az adatkészlet.

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

