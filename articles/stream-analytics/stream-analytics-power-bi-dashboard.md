---
title: A Power BI irányítópult-integráció az Azure Stream Analytics
description: Ez a cikk ismerteti, hogyan valós idejű Power BI-irányítópult használatával jelenítheti meg az adatokat egy Azure Stream Analytics-feladatban.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: d7f67015d4df20ea39c1225d52be36340b8f65d1
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2018
ms.locfileid: "53556976"
---
# <a name="tutorial-stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Oktatóanyag: Stream Analytics és a Power bi-ban: A valós idejű elemzési irányítópultok, a streamelési adatok
Az Azure Stream Analytics lehetővé teszi, hogy a vezető Üzletiintelligencia-eszközök, egy [Microsoft Power BI](https://powerbi.com/). Ebből a cikkből megtudhatja, hogyan hozzon létre Üzletiintelligencia-eszközök a Power BI kimenetként a az Azure Stream Analytics-feladatokhoz. Azt is megtudhatja, hogyan hozhat létre és használhat a valós idejű irányítópultok.

Ez a cikk továbbra is a Stream Analyticsből származó [valós idejű csalásészlelés](stream-analytics-real-time-fraud-detection.md) oktatóanyag. Azt, hogy az oktatóanyagban létrehozott munkafolyamat épül, és a kimeneti Stream Analytics-feladat által észlelt rosszindulatú telefonhívásokat jelenítheti meg, hogy a Power BI hozzáadja. 

Megtekinthet [videó](https://www.youtube.com/watch?v=SGUpT-a99MA) annak szemléltetésére, hogy ebben a forgatókönyvben.


## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzálátna, győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Egy Azure-fiók.
* A Power bi-fiók. Egy munkahelyi vagy iskolai fiók is használhatja.
* A befejezett verzióját a [valós idejű csalásészlelés](stream-analytics-real-time-fraud-detection.md) oktatóanyag. Az oktatóanyag tartalmazza a fiktív telefonhívás metaadatokat hoz létre alkalmazást. Az oktatóanyagban létrehoz egy eseményközpontot, és a streamelési telefonhívás-adatok küldése az event hubs. Írhat egy lekérdezést, amely észleli a csaló hívások (az azonos számú egyszerre különböző helyeken hívásait). 


## <a name="add-power-bi-output"></a>A Power BI-kimenet hozzáadása
A csalások valós idejű észlelése az oktatóanyagban a kimeneti küld az Azure Blob storage. Ebben a szakaszban adjon hozzá egy kimeneti, amely adatokat küld a Power bi-ban.

1. Az Azure Portalon nyissa meg a korábban létrehozott Stream Analytics-feladat. Ha a javasolt nevet használt, a feladat neve `sa_frauddetection_job_demo`.

2. Válassza ki a **kimenetek** közepén a projekt irányítópultján mezőbe, majd **+ Hozzáadás**.

3. A **kimeneti Alias**, adja meg `CallStream-PowerBI`. Más nevet is használhat. Ha így tesz, jegyezze fel, mert később szüksége lesz a neve. 

4. A **fogadó**válassza **Power BI**.

   ![Hozzon létre egy kimenetet a Power bi-hoz](./media/stream-analytics-power-bi-dashboard/create-power-bi-ouptut.png)

5. Kattintson a **engedélyezése**.

    Egy ablak, ahol megadhatja az Azure-beli hitelesítő egy munkahelyi vagy iskolai fiókot. 

    ![Adja meg hitelesítő adatait a Power bi-bA a hozzáféréshez](./media/stream-analytics-power-bi-dashboard/power-bi-authorization-credentials.png)

6. Adja meg hitelesítő adatait. Vegye figyelembe, és megadta a hitelesítő adatait, akkor van is jogosultságot a Streaming Analytics-feladat a Power BI terület eléréséhez.

7. Amikor visszatér a **új kimenet** panelen adja meg a következőket:

    * **A csoport munkaterület**: Válasszon ki egy munkaterületet, ahol szeretné létrehozni az adatkészletet a Power BI bérlőben.
    * **Adatkészlet neve**:  Írja be a `sa-dataset` (igen) kifejezést. Más nevet is használhat. Ha így tesz, győződjön meg arról, jegyezze fel későbbi használatra.
    * **Tábla neve**: Írja be a `fraudulent-calls` (igen) kifejezést. A Stream Analytics-feladatok a Power BI-kimenet jelenleg csak egy tábla egy adatkészlet rendelkezhet.

    ![A Power BI-munkaterület adatkészletet és táblázat](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut-with-dataset-table.png)

    > [!WARNING]
    > Ha a Power BI egy adatkészletet és egy táblázatot, amely azokat a Stream Analytics-feladatban megadott nevével megegyező nevet kell, felülírják a már meglévőket.
    > Azt javasoljuk, hogy ne explicit módon hozzon létre ezzel az adatkészlettel és táblát a Power BI-fiókjában. Ezek automatikusan jönnek létre a Stream Analytics-feladat indítása és a feladat kimeneti szivattyútelepek elindítja a Power BI-bA. A feladat lekérdezés nem ad vissza eredményt, ha az adatkészlet és a táblázat nem jönnek létre.
    >

8. Kattintson a **Create** (Létrehozás) gombra.

Az adatkészlet jön létre a következő beállításokkal:

* **defaultRetentionPolicy: BasicFIFO**: Adatok FIFO, legfeljebb 200 000 sort.
* **defaultMode: pushStreaming**: Az adatkészlet támogatja a streamelési csempék és a hagyományos jelentés-alapú Vizualizációk (más néven) leküldés).

Az adatkészletek jelenleg nem hozható létre más jelzővel.

A Power BI-adatkészletek kapcsolatos további információkért lásd: a [Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx) hivatkozást.


## <a name="write-the-query"></a>A lekérdezés írása

1. Zárja be a **kimenetek** panelt és térjen vissza a feladat panelt.

2. Kattintson a **lekérdezés** mezőbe. 

3. Adja meg a következő lekérdezést. Ez a lekérdezés önillesztést lekérdezéshez, a visszaélések észleléséhez az oktatóanyagban létrehozott hasonlít. A különbség az, hogy ezt a lekérdezést elküldi az eredményeket a létrehozott új kimenet (`CallStream-PowerBI`). 

    >[!NOTE]
    >Ha ezt tette nincs a bemeneti `CallStream` csalásészlelés oktatóanyagban helyett írja be a `CallStream` a a **FROM** és **CSATLAKOZZON** záradékok a lekérdezésben.

        ```SQL
        /* Our criteria for fraud:
        Calls made from the same caller to two phone switches in different locations (for example, Australia and Europe) within five seconds */

        SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
        INTO "CallStream-PowerBI"
        FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
        JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime

        /* Where the caller is the same, as indicated by IMSI (International Mobile Subscriber Identity) */
        ON CS1.CallingIMSI = CS2.CallingIMSI

        /* ...and date between CS1 and CS2 is between one and five seconds */
        AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5

        /* Where the switch location is different */
        WHERE CS1.SwitchNum != CS2.SwitchNum
        GROUP BY TumblingWindow(Duration(second, 1))
        ```

4. Kattintson a **Save** (Mentés) gombra.


## <a name="test-the-query"></a>A lekérdezés tesztelése
Ez a szakasz az opcionális de javasolt. 

1. Ha a TelcoStreaming alkalmazást jelenleg nem fut, indítsa el az alábbi lépéseket:

    * Nyisson meg egy parancsablakot.
    * Lépjen abba a mappába, amelyeknél a telcogenerator.exe és a módosított telcodatagen.exe.config fájlokat.
    * Futtassa az alábbi parancsot:

       `telcodatagen.exe 1000 .2 2`

2. Az a **lekérdezés** panelen kattintson a Tovább gombra a pontra a `CallStream` adja meg, és válassza ki **mintaadatok bemenetből**.

3. Adja meg, hogy három perc alatt az adatokat, majd kattintson **OK**. Várjon, amíg a rendszer értesíti arról, hogy az adatok mintavételezése befejeződött.

4. Kattintson a **Tesztelés** elemre, és ellenőrizze, hogy megkapja-e az eredményeket.


## <a name="run-the-job"></a>A feladat futtatása

1. Győződjön meg arról, hogy fut-e a TelcoStreaming alkalmazást.

2. Zárja be a **lekérdezés** panelen.

3. A feladat panelen kattintson a **Start**.

    ![A Stream Analytics-feladat indítása](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

A Streaming Analytics-feladat elindul, a bejövő streamet a csaló hívások keres. A feladat is hoz létre az adatkészlet és a táblázat a Power bi-ban, és elindítja a csaló hívások rájuk vonatkozó adatok küldését.


## <a name="create-the-dashboard-in-power-bi"></a>Az irányítópult létrehozásához a Power bi-ban

1. Lépjen a [Powerbi.com](https://powerbi.com) , és jelentkezzen be munkahelyi vagy iskolai fiókjával. Ha a Stream Analytics-Feladatlekérdezés eredményeket, láthatja, hogy az adatkészlet már létrejött:

    ![Streamelési adatkészlet helye a Power bi-ban](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. A munkaterületén kattintson  **+ &nbsp;létrehozás**.

    ![A Power BI-munkaterület létrehozása gomb](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Hozzon létre egy új irányítópultot, és adja neki `Fraudulent Calls`.

    ![Hozzon létre egy irányítópultot, és adjon meg egy nevet a Power BI-munkaterület](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Kattintson az ablak tetején **csempe hozzáadása**válassza **egyedi STREAMELÉSI adatok**, és kattintson a **tovább**.

    ![Egyéni streamelési adatkészlet csempét a Power bi-ban](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. A **YOUR DATSETS**válassza ki a az adatkészlet, majd kattintson a **tovább**.

    ![A streamelési adatkészletet a Power bi-ban](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. Alatt **Vizualizáció típusának**válassza **kártya**, majd a **mezők** listáról válassza ki **fraudulentcalls**.

    ![Új csempe képi megjelenítés részletei](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Kattintson a **tovább**.

8. Adja meg például a cím és alcím csempe részletei.

    ![Cím és alcím új csempe](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Kattintson az **Alkalmaz** gombra.

    Most már rendelkezik egy csalás számláló!

    ![Csalás számláló Power BI-irányítópulton](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. Kövesse a lépéseket, hogy újra felvesz egy csempét (4. lépés-től induló). Ennek során tegye a következőket:

    * Amikor juthat el a **Vizualizáció típusának**válassza **vonaldiagram**. 
    * Adjon hozzá egy tengelyt, és válassza ki **windowend** lehetőséget. 
    * Adjon meg egy értéket, és válassza a **fraudulentcalls** lehetőséget.
    * A **Megjelenítendő időtartomány** beállításnál válassza ki az utolsó 10 percet.

    ![Vonaldiagram tartalmazó csempe létrehozása a Power bi-ban](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Kattintson a **tovább**, cím és alcím hozzáadása, és kattintson a **alkalmaz**.

    A Power BI-irányítópult mostantól lehetővé teszi az adatok kétféle nézetét kapcsolatos csaló hívásokat a streamelt adatokat az észlelt.

    ![Befejeződött a rosszindulatú hívások két csempét megjelenítő Power BI-irányítópult](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>További információ a Power BI szolgáltatásról

Ez az oktatóanyag bemutatja, hogyan hozhat létre csak néhány típusú Vizualizációk egy adatkészlet. Power BI segítségével könnyebben hozhat létre a szervezet más felhasználói Üzletiintelligencia-eszközök. További ötleteket találhat a következő forrásanyagokban talál:

* Power BI-irányítópult egy másik példa, tekintse meg a [Power BI – első lépések](https://youtu.be/L-Z_6P56aas?t=1m58s) videó.
* Stream Analytics konfigurálásával kapcsolatos további információkat a feladat kimeneti a Power bi-ba, és a Power BI-csoportok használata esetén tekintse át a [Power BI](stream-analytics-define-outputs.md#power-bi) szakaszában a [kiírja a Stream Analytics](stream-analytics-define-outputs.md) cikk. 
* Általában a Power BI használatával kapcsolatos információkért lásd: [Power BI-irányítópultok](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>Korlátozások és ajánlott eljárások ismertetése
Jelenleg a Power BI nem hívható meg nagyjából egyszer másodpercenként. Streamelési Vizualizációk 15 KB-os csomagokat támogatja. Efölött streamelési Vizualizációk sikertelen (de a leküldéses továbbra is működik). Ezek a korlátozások miatt a Power BI adatmodelljeinek legtöbb természetesen hol tárolódnak az Azure Stream Analytics a jelentős terhelés adatcsökkentés esetekben. Azt javasoljuk, hogy egy Átfedésmentes ablak vagy Hopping ablak segítségével győződjön meg arról, hogy adatleküldés legfeljebb egy leküldéses másodpercenként, és, hogy a lekérdezést hajtanak végre az átviteli sebességet megkövetelő belül.

Az alábbi egyenlet használatával számítja ki az ablak adni másodpercben értékét:

![Egyenlőségi ablak adni másodpercben érték kiszámításához](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Példa:

* Adatok küldése egy másodperces intervallumon 1000 eszközzel rendelkezik.
* A Power BI Pro támogató Termékváltozatot / óra 1 000 000 sort használja.
* A Power bi-bA eszközönként átlagos adatok mennyisége közzétenni kívánt.

Ennek eredményeképpen az egyenlet:

![Egyenlőségi feltétel példa alapján](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Ebben a konfigurációban módosíthatja az eredeti lekérdezés a következőhöz:

```SQL
    SELECT
        MAX(hmdt) AS hmdt,
        MAX(temp) AS temp,
        System.TimeStamp AS time,
        dspl
    INTO "CallStream-PowerBI"
    FROM
        Input TIMESTAMP BY time
    GROUP BY
        TUMBLINGWINDOW(ss,4),
        dspl
```

### <a name="renew-authorization"></a>Engedély megújítása
Ha a jelszó megváltozott, mivel a feladat létrehozásának vagy utolsó hitelesített, meg kell hitelesítse magát újra a Power BI-fiókjába. Ha az Azure multi-factor Authentication konfigurálva van az Azure Active Directory (Azure AD) bérlő, is újítsa meg a Power BI engedélyezési kéthetente kell. Ha nem újítja meg, például egy feladat kimenetének hiánya probléma sikerült megjelenik, vagy egy `Authenticate user error` a művelet naplókban.

Hasonlóképpen a jogkivonat lejárata után elindul egy feladat, ha hiba lép fel, és a feladat meghiúsul. A probléma megoldásához állítsa le a feladatot, hogy fut-e, és nyissa meg a Power BI-kimenet. Adatvesztés elkerülése érdekében válassza ki a **engedély megújítása** hivatkozásra, és indítsa újra a feladatot a **leállt legutóbbi**.

Miután az engedélyezés a Power bi-JAL frissítve lett, egy zöld riasztás jelenik meg az engedélyezési területen megfelelően, hogy a probléma megoldódott.

## <a name="get-help"></a>Segítségkérés
További segítségre van szüksége, próbálja meg [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Az Azure Stream Analytics lekérdezési nyelv leírása](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Az Azure Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)
