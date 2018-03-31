---
title: A Power BI-irányítópultot az Azure Stream Analytics |} Microsoft Docs
description: A valós idejű streamelési Power BI-Irányítópult segítségével gyűjtse össze az üzleti intelligencia, és a Stream Analytics-feladat nagy mennyiségű adatok elemzését.
keywords: elemzések irányítópultján, valós idejű irányítópulton
services: stream-analytics
documentationcenter: ''
author: jseb225
manager: ryanw
ms.assetid: fe8db732-4397-4e58-9313-fec9537aa2ad
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/27/2017
ms.author: jeanb
ms.openlocfilehash: e1f1d960c312362e0e0cd6d2f83599c28c8c3f05
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>A Stream Analytics és a Power BI: az adatfolyamként történő adatok valós idejű elemzési irányítópult
Az Azure Stream Analytics lehetővé teszi, hogy a vezető üzleti intelligencia eszközök közül az egyik kihasználhatja [Microsoft Power BI](https://powerbi.com/). Ebből a cikkből megismerheti, hogyan üzleti intelligencia eszközök használatával létrehozni a Power BI kimenetként az Azure Stream Analytics-feladatok. Azt is megtudhatja, hogyan történő létrehozásáról és használatáról a valós idejű irányítópulton.

Ez a cikk továbbra is fennáll, a Stream Analytics [valós idejű csalások felderítéséhez](stream-analytics-real-time-fraud-detection.md) oktatóanyag. Azt, hogy az oktatóanyagban létrehozott munkafolyamat épül, és hozzáadja a Power BI kimeneti, hogy egy Streaming Analytics-feladat által észlelt rosszindulatú telefonhívásokat jelenítheti meg. 

Figyelheti az [videó](https://www.youtube.com/watch?v=SGUpT-a99MA) , amely bemutatja, hogy ebben a forgatókönyvben.


## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy a következő:

* Egy Azure-fiók.
* A Power BI egy fiókot. A munkahelyi vagy iskolai fiók is használhatja.
* A befejezett verzióját a [valós idejű csalások felderítéséhez](stream-analytics-real-time-fraud-detection.md) oktatóanyag. Az oktatóanyag fiktív telefonhívások metaadatokat hoz létre alkalmazást tartalmaz. Az oktatóanyagban létrehoz egy eseményközpontot, és a streamelési telefonhívás adatokat küldeni az eseményközpontba. Írhat egy lekérdezést, amely észleli a csalárd hívások (egyszerre ugyanazon több, különböző helyeken hívások). 


## <a name="add-power-bi-output"></a>A Power BI-kimenet hozzáadása
A valós idejű csalások észlelése az oktatóanyagban a kimeneti küldött Azure Blob Storage tárolóban. Ebben a szakaszban adja hozzá egy kimeneti, hogy információkat küld a Power bi-bA.

1. Nyissa meg a korábban létrehozott Streaming Analytics-feladat az Azure-portálon. Ha a javasolt név, a feladat neve `sa_frauddetection_job_demo`.

2. Válassza ki a **kimenetek** közepén a feladat irányítópult mezőbe, majd válassza ki **+ Hozzáadás**.

3. A **kimeneti Alias**, adja meg `CallStream-PowerBI`. Használhat egy másik nevet. Ha így tesz, jegyezze fel, mert később szüksége nevét. 

4. A **gyűjtése**, jelölje be **Power BI**.

   ![Hozzon létre egy kimeneti Power bi-ban](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut.png)

5. Kattintson a **engedélyezik**.

    Egy ablak, ahol megadhatja a Azure hitelesítő adatait a munkahelyi vagy iskolai fiókkal. 

    ![Adja meg a hitelesítő adatokat a hozzáférés a Power bi-hoz](./media/stream-analytics-power-bi-dashboard/authorize-area.png)

6. Adja meg a hitelesítő adatokat. Ne feledje, és megadta a hitelesítő adatait, amikor a hozzáférése a Streaming Analytics-feladathoz a Power BI területen is van-e megadva.

7. Amikor visszatér a **új kimeneti** panelen adja meg a következő adatokat:

    * **Munkaterület csoport**: Munkaterület kiválasztása a Power BI-bérlőhöz, hol szeretné létrehozni az adatkészletet.
    * **A DataSet neve**: Adjon meg `sa-dataset`. Használhat egy másik nevet. Ha így tesz, jegyezze fel a azt későbbi használatra.
    * **Tábla neve**: Adjon meg `fraudulent-calls`. Jelenleg a Power BI kimenetét a Stream Analytics-feladatok lehet csak egy tábla a DataSet adatkészlet.

    ![PBI munkaterület](./media/stream-analytics-power-bi-dashboard/create-pbi-ouptut-with-dataset-table.png)

    > [!WARNING]
    > Ha a Power BI egy adatkészlet és a tábla, mint a Stream Analytics-feladat az azonos nevű, a rendszer felülírja a már meglévőket.
    > Azt javasoljuk, hogy explicit módon hozzon létre a DataSet adatkészlet és a tábla a Power BI-fiókjába. Akkor jönnek létre automatikusan a Stream Analytics-feladat indítása és a feladat szivattyúzó kimeneti elindul a Power BI-bA. A feladat lekérdezési eredmények nem ad vissza, ha a DataSet adatkészlet és a tábla nem jönnek létre.
    >

8. Kattintson a **Create** (Létrehozás) gombra.

Az adatkészlet a következő beállításokkal jön létre:

* **defaultRetentionPolicy: BasicFIFO**: adata FIFO, amely legfeljebb 200 000 sorok.
* **defaultMode: pushStreaming**: A dataset támogatja az adatfolyam csempék és jelentés-alapú látványelemek hagyományos (más néven leküldéses).

Az egyéb jelzőkkel jelenleg nem hozható létre adathalmazokat.

További információ a Power BI-adatkészletek: a [Power BI REST API](https://msdn.microsoft.com/library/mt203562.aspx) hivatkozás.


## <a name="write-the-query"></a>A lekérdezés írása

1. Zárja be a **kimenetek** panelt és térjen vissza a feladat panelen.

2. Kattintson a **lekérdezés** mezőbe. 

3. Adja meg a következő lekérdezést. Ez a lekérdezés hasonlít az észlelés csalás oktatóanyag létrehozott önillesztés lekérdezés. A különbség az, hogy ez a lekérdezés eredményei elküldi a létrehozott új kimeneti (`CallStream-PowerBI`). 

    >[!NOTE]
    >Ha Ön volt nevezze el a bemeneti `CallStream` csalás észlelés oktatóanyagban helyettesítse be a nevet `CallStream` a a **FROM** és **csatlakozás** záradékok a lekérdezésben.

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

4. Kattintson a **Save** (Mentés) gombra.


## <a name="test-the-query"></a>A lekérdezés tesztelése
Ez a szakasz az opcionális de javasolt. 

1. Ha a TelcoStreaming alkalmazás jelenleg nem fut, indítsa el az alábbiak szerint:

    * Nyisson meg egy parancsablakot.
    * Nyissa meg azt a mappát, amelyben a telcogenerator.exe és a módosított telcodatagen.exe.config fájlokat is.
    * Futtassa az alábbi parancsot:

            telcodatagen.exe 1000 .2 2

2. Az a **lekérdezés** panelen kattintson a Tovább gombra a pont a `CallStream` adja meg, és válassza ki **az adatokat a bemeneti**.

3. Adja meg, hogy adatokat, és kattintson a három perc alatt érkezett **OK**. Várjon, amíg értesítést kap, hogy az adatok mintavételezett.

4. Kattintson a **teszt** , és győződjön meg arról, hogy eredményeket kap.


## <a name="run-the-job"></a>A feladat futtatása

1. Győződjön meg arról, hogy fut-e a TelcoStreaming alkalmazást.

2. Zárja be a **lekérdezés** panelen.

3. A feladat panelen kattintson **Start**.

    ![A Stream Analytics-feladat indítása](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

A Streaming Analytics-feladat elindul, a bejövő adatfolyam a csalárd hívások keres. A feladat is létrehozza a dataset és a tábla a Power bi-ban, és elindítja a csalárd hívásainak kapcsolatos adatok küldése.


## <a name="create-the-dashboard-in-power-bi"></a>Az irányítópult létrehozása a Power bi-ban

1. Ugrás a [powerbi.com webhelyen](https://powerbi.com) , és jelentkezzen be munkahelyi vagy iskolai fiókjával. A Stream Analytics-feladat lekérdezés kiírathatja a eredményeket, jelenik meg, hogy a dataset már létrejött:

    ![Folyamatos átviteli adatkészletet a Power bi-ban](./media/stream-analytics-power-bi-dashboard/streaming-dataset.png)

2. A munkaterületen kattintson  **+ &nbsp;létrehozása**.

    ![A Power BI-munkaterület létrehozása gomb](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Hozzon létre egy új irányítópult, és adjon neki nevet `Fraudulent Calls`.

    ![Hozzon létre egy irányítópultot, és adjon neki egy nevet a Power BI munkaterület](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Kattintson az ablak tetején **Hozzáadás csempe**, jelölje be **egyéni STREAMING adatok**, és kattintson a **következő**.

    ![Egyéni adatfolyam-adatkészlet](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. A **a DATSETS**, válassza az adatkészlet majd **következő**.

    ![A folyamatos átviteli adatkészletet](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. A **képi megjelenítés típus**, jelölje be **kártya**, majd a a **mezők** listáról válassza ki **fraudulentcalls**.

    ![Az új csempe a képi megjelenítés részletei](./media/stream-analytics-power-bi-dashboard/add-fraud.png)

7. Kattintson a **Tovább** gombra.

8. Töltse ki a csempe adatait, például a title és a felirat.

    ![Cím és az új csempe alcíme](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Kattintson az **Alkalmaz** gombra.

    Most már rendelkezik egy csalás számláló!

    ![Csalás számláló](./media/stream-analytics-power-bi-dashboard/fraud-counter.png)

8. Kövesse a lépéseket, hogy újra vegye fel a csempe (4. lépés kezdve). Most, tegye a következőket:

    * Amikor elér **képi megjelenítés típus**, jelölje be **vonaldiagram**. 
    * Tengely hozzáadása, és válassza ki **windowend**. 
    * Adjon hozzá egy értéket, és válassza ki **fraudulentcalls**.
    * A **megjelenítendő időkerete**, válassza ki az elmúlt 10 perc.

    ![A vonaldiagram csempe létrehozása](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Kattintson a **következő**, cím és alcíme hozzáadása, és kattintson a **alkalmaz**.

    A Power BI-irányítópultot most kétféle nézetét biztosítja az adatok kapcsolatos csalárd hívások, a streamelési adatok észlelhető.

    ![A Power BI irányítópultjáról és azokról a csalárd hívások két csempe befejeződött](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>További információ a Power BI szolgáltatásról

Ez az oktatóanyag bemutatja, hogyan csak néhány típusú képi megjelenítések egy adatkészlet létrehozásához. A Power BI segítségével a szervezet más felhasználói üzleti intelligencia eszközök létrehozása. További ötletek lásd a következőket:

* Egy másik példa a Power BI-irányítópultot, tekintse meg a [első lépések a Power BI](https://youtu.be/L-Z_6P56aas?t=1m58s) videó.
* Streaming Analytics konfigurálásával kapcsolatos további információkat a feladat kimenete Power bi-ba, és a Power BI csoportokat használ, tekintse át a [Power BI](stream-analytics-define-outputs.md#power-bi) szakasza a [Stream Analytics kimenete](stream-analytics-define-outputs.md) cikk. 
* Általában a Power BI használatával kapcsolatos információkért lásd: [Power BI-irányítópultjain](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/).


## <a name="learn-about-limitations-and-best-practices"></a>További tudnivalók a korlátozások és ajánlott eljárások
Jelenleg a Power BI hívható nagyjából egyszer száma másodpercenként. Adatfolyam-továbbítási látványelemek 15 KB-os csomagokat támogatja. Felett marad, hogy a folyamatos átviteli látványelemek sikertelen (de leküldéses folytatja a működést). Ezek a korlátozások miatt a Power BI adatmodelljeinek legtöbb természetes azokra az esetekre, ahol az Azure Stream Analytics does jelentős mennyiségű adat terhelés csökkentését. Azt javasoljuk, hogy egy Átfedésmentes ablak vagy Hopping ablak segítségével győződjön meg arról, hogy az adatleküldés legfeljebb egy leküldéses másodpercenként, és, hogy a lekérdezés az átviteli követelmények belül fájljai.

A következő egyenlet számítja ki ahhoz, hogy megkapja az ablak másodperc értékét használhatja:

![Equation1](./media/stream-analytics-power-bi-dashboard/equation1.png)  

Példa:

* Rendelkezik egy másodperces időközönként adatküldés 1000 eszközök.
* A Power BI Pro SKU, amely támogatja a óránként 1 000 000 sorok használ.
* Közzé szeretné tenni a eszközönként átlagos adatok mennyisége Power bi-bA.

Ennek eredményeképpen az egyenlet:

![Equation2](./media/stream-analytics-power-bi-dashboard/equation2.png)  

Ebben a konfigurációban módosíthatja az eredeti lekérdezést a következőhöz:

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


### <a name="renew-authorization"></a>Engedély megújítása
Ha a jelszó megváltozott a feladat meg lett létrehozva, vagy utolsó hitelesített, akkor újból hitelesítésre a Power BI-fiókjába. Ha Azure multi-factor Authentication az Azure Active Directory (Azure AD) bérlő van beállítva, akkor is meg kell újítsa meg a Power BI engedélyezési kéthetente. Ha nem újítja meg, például a feladat kimenetére hiánya probléma volt megjelenik vagy egy `Authenticate user error` műveletet rögzít.

Hasonlóképpen ha egy feladat indítása után a jogkivonat lejárt, hiba történik, és a feladat sikertelen. A probléma megoldásához, hogy fut a feladat leállítása, majd lépjen a Power BI-kimenet. Adatvesztés elkerülése érdekében válassza ki a **újra a portálon** hivatkozásra, és indítsa újra a feladatot a **feladat utolsó befejezési időpontja**.

Az engedélyezési frissítése a Power BI, után egy zöld riasztás jelenik meg megfelelően, hogy a probléma megoldódott-e az engedélyezési területen.

## <a name="get-help"></a>Segítség kérése
Ha további segítségre van szüksége, próbálkozzon a [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Az Azure Stream Analytics lekérdezési nyelvi referencia](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Az Azure Stream Analytics felügyeleti REST API-referencia](https://msdn.microsoft.com/library/azure/dn835031.aspx)
