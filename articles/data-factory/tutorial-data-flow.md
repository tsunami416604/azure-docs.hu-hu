---
title: Adatok átalakítása leképezési adatfolyam használatával
description: Ez az oktatóanyag lépésenkénti utasításokat tartalmaz az Azure Data Factory használatával az adatok térképezési adatfolyammal történő átalakításához
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/07/2019
ms.openlocfilehash: e6ca8007a96cc63b51b4f79b69029cbf0799e71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979180"
---
# <a name="transform-data-using-mapping-data-flows"></a>Adatok átalakítása leképezési adatfolyamokkal

Ha még csak ismerkedik az Azure Data Factory használatával, olvassa el [az Azure Data Factory használatának első lépéseit](introduction.md) ismertető cikket.

Ebben az oktatóanyagban az Azure Data Factory felhasználói felület (UX) használatával hozzon létre egy folyamatot, amely adatokat másol és alakít át egy Azure Data Lake Storage (ADLS) Gen2-forrásból egy ADLS Gen2-fogadóba a leképezési adatfolyam használatával. Az oktatóanyag konfigurációs mintája kibontható az adatok leképezési adatfolyam használatával történő átalakításakor

Az oktatóanyag során a következő lépéseket hajtja végre:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy folyamatot adatfolyam-tevékenységgel.
> * Négy átalakítással hozhat létre leképezési adatfolyamot.
> * A folyamat próbafuttatása
> * Adatfolyam-tevékenység figyelése

## <a name="prerequisites"></a>Előfeltételek
* **Azure-előfizetés**. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* **Egy Azure Storage-fiók**. Az ADLS-tárolót *forrásként* és *fogadóadattárként* használhatja. Ha még nem rendelkezik tárfiókkal, tekintse meg az [Azure Storage-fiók létrehozásának](../storage/common/storage-account-create.md) lépéseit ismertető cikket.

A fájl, hogy mi átez a bemutató MoviesDB.csv, amely [megtalálható itt](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Ha be szeretné olvasni a fájlt a GitHubról, másolja a tartalmat egy általad választott szövegszerkesztőbe, hogy helyileg mentsd .csv fájlként. A fájl feltöltése a tárfiókba, [lásd: Blobok feltöltése az Azure Portalon.](../storage/blobs/storage-quickstart-blobs-portal.md) A példák egy "minta-adatok" nevű tárolóra hivatkoznak.

## <a name="create-a-data-factory"></a>Data factory létrehozása

Ebben a lépésben hozzon létre egy adat-előállító, és nyissa meg a Data Factory UX hozzon létre egy folyamatot az adat-előállítóban.

1. Nyissa meg **a Microsoft Edge** vagy a Google **Chrome**. Jelenleg a Data Factory felhasználói felülete csak a Microsoft Edge és a Google Chrome böngészőkben támogatott.
2. A bal oldali menüben válassza az **Erőforrás-elemzési** > **Analytics** > **adatgyár**létrehozása lehetőséget:

   ![Data Factory kiválasztása az „Új” ablaktáblán](./media/doc-common-process/new-azure-data-factory-menu.png)

3. Az **Új adat-előállító** lap **Név** mezőjében adja meg az **ADFTutorialDataFactory** értéket.

   Az Azure-adat-előállító nevének *globálisan egyedinek*kell lennie. Ha a név értékével kapcsolatos hibaüzenet kap, adjon meg másik nevet az adat-előállítóhoz. (például yournameADFTutorialDataFactory). A Data Factory-összetevők elnevezési szabályait a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.

     ![Új adat-előállító](./media/doc-common-process/name-not-available-error.png)
4. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni.
5. **Erőforráscsoport:** hajtsa végre a következő lépések egyikét:

    a. Válassza **a Meglévő használata**lehetőséget, és válasszon ki egy meglévő erőforráscsoportot a legördülő listából.

    b. Válassza **az Új létrehozása**lehetőséget, és írja be egy erőforráscsoport nevét. 
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg az [Erőforráscsoportok használata az Azure-erőforrások kezeléséhez](../azure-resource-manager/management/overview.md) ismertető cikket. 
6. A **Verzió** résznél válassza a **V2** értéket.
7. A **Hely** területen válassza ki az adat-előállító helyét. A legördülő listán csak a támogatott helyek jelennek meg. Adattárak (például az Azure Storage és az SQL Database) és a számítási (például Az Azure HDInsight) által használt adatgyár lehet más régiókban.
8. Kattintson a **Létrehozás** gombra.
9. A létrehozás befejezése után megjelenik az értesítés az Értesítésközpontban. Válassza **az Ugrás az erőforráshoz** lehetőséget az Adatgyár lapra való navigáláshoz.
10. A Data Factory felhasználói felületének külön lapon történő elindításához válassza a **Létrehozás és figyelés** csempét.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Folyamat létrehozása adatfolyam-tevékenységgel

Ebben a lépésben hozzon létre egy folyamatot, amely egy adatfolyam-tevékenységet tartalmaz.

1. Az **Első lépések** lapon válassza a **Folyamat létrehozása** lehetőséget.

   ![Folyamat létrehozása](./media/doc-common-process/get-started-page.png)

1. A folyamat **Általános** lapján adja meg a **TransformMovies** parancsot a folyamat **nevéhez.**
1. A gyári felső sávon csúsztassa be az **Adatfolyam hibakeresési** csúszkáját. A hibakeresési mód lehetővé teszi az átalakítási logika interaktív tesztelését egy élő Spark-fürt ellen. Az adatfolyam-fürtök bemelegedése 5–7 percet vesz igénybe, és a felhasználóknak ajánlott először bekapcsolni a hibakeresést, ha adatfolyam-fejlesztést terveznek. További információt a [Hibakeresési mód című témakörben talál.](concepts-data-flow-debug-mode.md)

    ![Adatfolyam-tevékenység](media/tutorial-data-flow/dataflow1.png)
1. A **Tevékenységek** ablaktáblán bontsa ki az **Áthelyezés és átalakítás** harmonikát. Húzza az **adatfolyam-tevékenységet** az ablaktáblából a folyamatvászonra.

    ![Adatfolyam-tevékenység](media/tutorial-data-flow/activity1.png)
1. Az **Adatfolyam hozzáadása** előugró ablakban jelölje be **az Új adatfolyam létrehozása** lehetőséget, majd nevezze el az adatfolyamtransformMovies **nevet.** Ha végzett, kattintson a Befejezés gombra.

    ![Adatfolyam-tevékenység](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Átalakítási logika létrehozása az adatfolyamvásznon

Az adatfolyam létrehozása után a rendszer automatikusan elküldi az adatfolyam vászonra. Ebben a lépésben olyan adatfolyamot hozhat létre, amely a moviesDB.csv-t az ADLS tárolóban veszi, és összesíti a vígjátékok átlagos minősítését 1910 és 2000 között. Ezután ezt a fájlt visszaírja az ADLS-tárolóba.

1. Az adatfolyamvásznon a **Forrás hozzáadása** mezőre kattintva adjon hozzá egy forrást.

    ![Adatfolyam-vászon](media/tutorial-data-flow/dataflow2.png)
1. Nevezze el a forrás **MoviesDB**. Új forrásadatkészlet létrehozásához kattintson az **Új** gombra.

    ![Adatfolyam-vászon](media/tutorial-data-flow/dataflow3.png)
1. Válassza az **Azure Data Lake Storage Gen2 lehetőséget.** Kattintson a Folytatás gombra.

    ![Adatkészlet](media/tutorial-data-flow/dataset1.png)
1. Válassza **a Tagolt szöveg lehetőséget.** Kattintson a Folytatás gombra.

    ![Adatkészlet](media/tutorial-data-flow/dataset2.png)
1. Nevezze el az **adatkészletMoviesDB**. A csatolt szolgáltatás legördülő menüben válassza az **Új**lehetőséget.

    ![Adatkészlet](media/tutorial-data-flow/dataset3.png)
1. A csatolt szolgáltatás létrehozási képernyőjén nevezze el az ADLS gen2 kapcsolt szolgáltatást **ADLSGen2** néven, és adja meg a hitelesítési módszert. Ezután adja meg a kapcsolat hitelesítő adatait. Ebben az oktatóanyagban a Fiókkulcsot használjuk a tárfiókhoz való csatlakozáshoz. A **Kapcsolat tesztelése** gombra kattintva ellenőrizheti, hogy helyesen adta-e meg a hitelesítő adatokat. Ha végzett, kattintson a Létrehozás gombra.

    ![Csatolt szolgáltatás](media/tutorial-data-flow/ls1.png)
1. Miután visszatért az adatkészlet létrehozási képernyőjére, írja be, hogy a fájl hol található a **Fájl elérési útja** mezőben. Ebben az oktatóanyagban a moviesDB.csv fájl tárolóminta-adatokban található. Mivel a fájl fejlécekkel rendelkezik, jelölje be **az Első sor fejlécként jelölőnégyzetet.** Válassza **a Kapcsolat/tároló lehetőséget,** ha a fejlécsémát közvetlenül a tárolóban lévő fájlból szeretné importálni. Ha végzett, kattintson az OK gombra.

    ![Adathalmazok](media/tutorial-data-flow/dataset4.png)
1. Ha a hibakeresési fürt elindult, lépjen a forrásátalakítás **Adatelőnézet** lapjára, és kattintson a **Frissítés** gombra az adatok pillanatképének lefelvételéhez. Az adatelőnézet segítségével ellenőrizheti, hogy az átalakítás megfelelően van-e konfigurálva.

    ![Adatfolyam-vászon](media/tutorial-data-flow/dataflow4.png)
1. Az adatfolyamvásznon lévő forráscsomópont mellett kattintson a plusz ikonra egy új átalakítás hozzáadásához. Az első hozzáadni kívánt átalakítás **egy szűrő**.

    ![Adatfolyam-vászon](media/tutorial-data-flow/dataflow5.png)
1. Nevezze el a **szűrőátalakításfilterYears**nevet. Kattintson a Szűrés gomb melletti **kifejezésmezőre** a kifejezésszerkesztő megnyitásához. Itt adhatja meg a szűrési feltételt.

    ![Szűrés](media/tutorial-data-flow/filter1.png)
1. Az adatfolyam-kifejezésszerkesztő lehetővé teszi, hogy interaktívan hozzon létre kifejezéseket a különböző átalakítások. A kifejezések tartalmazhatnak beépített függvényeket, a bemeneti sémából származó oszlopokat és a felhasználó által definiált paramétereket. A kifejezések létrehozásáról az [Adatfolyam-kifejezésszerkesztő](concepts-data-flow-expression-builder.md)című témakörben talál további információt.

    Ebben a tutorial, azt szeretnénk, hogy kiszűrje a filmeket a műfaj vígjáték, hogy jött ki az évek között 1910 és 2000. Mivel az év jelenleg egy karakterlánc, a ```toInteger()``` függvény használatával egész számmá kell konvertálnia. Használja a nagyobb vagy egyenlő (>=) és kisebb, mint (<=) operátorok összehasonlítani a literális év értékeivel 1910 és 200-. Ezeket a kifejezéseket a (&&) operátorral együtt egyesítse. A kifejezés a következőképpen jön ki:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Ahhoz, hogy megtalálja, mely filmek ```rlike()``` vígjátékok, akkor a funkciót, hogy megtalálja minta "Vígjáték" az oszlop műfajok. Union a rlike kifejezés az év összehasonlítása kap:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Ha egy hibakeresési fürt aktív, ellenőrizheti a logikát a **Frissítés** gombra kattintva a kifejezés kimenetének megtekintéséhez a használt bemenetekhez képest. Több helyes válasz van arra, hogyan valósíthatja meg ezt a logikát az adatfolyam-kifejezés nyelvhasználatával.

    ![Szűrés](media/tutorial-data-flow/filter2.png)

    Kattintson a **Mentés és befejezés** gombra, ha végzett a kifejezéssel.

1. Az **adatelőnézet beolvasása** a szűrő megfelelő működésének ellenőrzéséhez.

    ![Szűrés](media/tutorial-data-flow/filter3.png)
1. A következő hozzáadni kívánt átalakítás a **Sémamódosító** **összesített** átalakítása.

    ![Összesítés](media/tutorial-data-flow/agg1.png)
1. Nevezze el az összesített **transzformációt AggregateComedyRatings**. A **Csoport szerint** lapon válassza az **évet** a legördülő menüből, hogy csoportosítsa az összesítéseket a film kihirdetésének éve szerint.

    ![Összesítés](media/tutorial-data-flow/agg2.png)
1. Nyissa meg az **Összesítések** lapot. A bal oldali szövegmezőben nevezze el az **AverageComedyRating összesített oszlopot.** Kattintson a jobb kifejezés mezőre az összesítő kifejezés beviteléhez a kifejezésszerkesztőn keresztül.

    ![Összesítés](media/tutorial-data-flow/agg3.png)
1. Az **oszlopminősítés**átlagának lehívásához használja az ```avg()``` összesítő függvényt. Mivel a **minősítés** ```avg()``` egy karakterlánc, és egy numerikus bemenetet vesz ```toInteger()``` fel, az értéket a függvényen keresztül egy számra kell konvertálnunk. Ez a kifejezés így néz ki:

    ```avg(toInteger(Rating))```

    Ha végzett, kattintson a **Mentés és befejezés** gombra.

    ![Összesítés](media/tutorial-data-flow/agg4.png)
1. Az **adatelőnézet lapon** megtekintheti az átalakítási kimenetet. Figyeljük meg, hogy csak két oszlop van, **év** és **AverageComedyRating**.

    ![Összesítés](media/tutorial-data-flow/agg3.png)
1. Ezután hozzá szeretne adni egy **Fogadó** átalakítást a **Cél csoportban.**

    ![Sink (Fogadó)](media/tutorial-data-flow/sink1.png)
1. Nevezze el a mosogató **mosogató**. Kattintson **az Új** gombra a fogadó adatkészlet létrehozásához.

    ![Sink (Fogadó)](media/tutorial-data-flow/sink2.png)
1. Válassza az **Azure Data Lake Storage Gen2 lehetőséget.** Kattintson a Folytatás gombra.

    ![Adatkészlet](media/tutorial-data-flow/dataset1.png)
1. Válassza **a Tagolt szöveg lehetőséget.** Kattintson a Folytatás gombra.

    ![Adatkészlet](media/tutorial-data-flow/dataset2.png)
1. Nevezze el a sink adatkészlet **MoviesSink**. Csatolt szolgáltatás esetén válassza ki a 6. Adja meg azt a kimeneti mappát, amelybe az adatokat írni szeretné. Ebben az oktatóanyagban a "kimenet" mappába írunk a "mintaadatok" tárolóban. A mappának nem kell előzetesen léteznie, és dinamikusan létrehozható. Az **Első sor beállítása fejlécként** true-ként, és válassza a **Nincs** lehetőséget az **Importálás sémához**. Kattintson a Finish (Befejezés) gombra.

    ![Sink (Fogadó)](media/tutorial-data-flow/sink3.png)

Most már befejezte az adatfolyam kiépítését. Készen áll, hogy lefuttassa a csővezetéken.

## <a name="running-and-monitoring-the-data-flow"></a>Az adatfolyam futtatása és figyelése

A folyamat közzététel előtt hibakeresést okozhat. Ebben a lépésben elindítja az adatfolyam-folyamat hibakeresési futtatását. Bár az adatok előnézete nem írja az adatokat, a hibakeresési futtatás adatokat ír a fogadó célhelyére.

1. Menjen a csővezeték vászonra. A hibakeresési futtatás elindításához kattintson a **Hibakeresés** gombra.

    ![Folyamat](media/tutorial-data-flow/pipeline1.png)
1. Az adatfolyam-tevékenységek folyamathiba-felderítése az aktív hibakeresési fürtöt használja, de az inicializálás még mindig legalább egy percet vesz igénybe. A folyamatot a Kimenet lapon **követheti** nyomon. Miután a futtatás sikeres volt, kattintson a szemüveg ikonra a figyelési ablaktábla megnyitásához.

    ![Folyamat](media/tutorial-data-flow/pipeline2.png)
1. A figyelési ablaktáblán láthatja az egyes átalakítási lépésekben eltöltött sorok és idő számát.

    ![Figyelés](media/tutorial-data-flow/pipeline3.png)
1. Kattintson egy átalakításra, hogy részletes információkat kapjon az oszlopokról és az adatok particionálásáról.

    ![Figyelés](media/tutorial-data-flow/pipeline4.png)

Ha követte a bemutató helyesen, meg kellett volna írni 83 sor és 2 oszlop a mosogató mappába. Ellenőrizheti, hogy az adatok helyesek-e a blob storage ellenőrzésével.

## <a name="next-steps"></a>További lépések

Az oktatóanyagban lévő folyamat olyan adatfolyamot futtat, amely összesíti a komédiák 1910 és 2000 között történő átlagos minősítését, és az adatokat az ADLS-be írja. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adat-előállító létrehozása
> * Hozzon létre egy folyamatot adatfolyam-tevékenységgel.
> * Négy átalakítással hozhat létre leképezési adatfolyamot.
> * A folyamat próbafuttatása
> * Adatfolyam-tevékenység figyelése

További információ az [adatfolyam-kifejezés nyelvéről.](data-flow-expression-functions.md)
