---
title: A Power BI irányítópultjának integrációja az Azure Stream Analytics szolgáltatással
description: Ez a cikk azt ismerteti, hogy miként jelenítheti meg az adatokat egy Azure Stream Analytics-feladatból valós idejű Power BI-irányítópult használatával.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/05/2019
ms.openlocfilehash: 8466fbcb4325dc244551a3b84fc20581366b7071
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851148"
---
# <a name="stream-analytics-and-power-bi-a-real-time-analytics-dashboard-for-streaming-data"></a>Stream Analytics és Power BI: Valós idejű elemzési irányítópult adatfolyam-továbbításhoz

Az Azure Stream Analytics lehetővé teszi, hogy kihasználja az egyik vezető üzleti intelligencia eszközt, a [Microsoft Power BI-t.](https://powerbi.com/) Ebben a cikkben megtudhatja, hogyan hozhat létre üzletiintelligencia-eszközöket a Power BI azure Stream Analytics-feladatok kimeneteként való használatával. Azt is megtudhatja, hogyan hozhat létre és használhat valós idejű irányítópultot.

Ez a cikk folytatódik a Stream Analytics [valós idejű csalásészlelési](stream-analytics-real-time-fraud-detection.md) oktatóanyagból. Az oktatóanyagban létrehozott munkafolyamatra épül, és hozzáad egy Power BI-kimenetet, hogy vizualizálhassa a Streaming Analytics-feladat által észlelt csalárd telefonhívásokat. 

Meg lehet nézni [egy videót,](https://www.youtube.com/watch?v=SGUpT-a99MA) amely bemutatja ezt a forgatókönyvet.


## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzálátna, győződjön meg róla, hogy rendelkezik az alábbiakkal:

* Egy Azure-fiók.
* A Power BI Pro fiókja. Használhat munkahelyi vagy iskolai fiókot.
* A [valós idejű csalásészlelési](stream-analytics-real-time-fraud-detection.md) oktatóanyag befejezett verziója. Az oktatóanyag tartalmaz egy alkalmazást, amely fiktív telefonhívás-metaadatokat generál. Az oktatóanyagban hozzon létre egy eseményközpontot, és küldje el a streamelési telefonhívás adatait az eseményközpontba. Olyan lekérdezést ír, amely észleli a csalárd hívásokat (hívásokat ugyanabból a számból ugyanabból az időpontból, különböző helyeken). 


## <a name="add-power-bi-output"></a>Power BI-kimenet hozzáadása
A valós idejű csalások észlelési oktatóanyag, a kimenet az Azure Blob storage küldésre. Ebben a szakaszban olyan kimenetet ad hozzá, amely adatokat küld a Power BI-nak.

1. Az Azure Portalon nyissa meg a korábban létrehozott Streamelési elemzési feladatot. Ha a javasolt nevet használta, `sa_frauddetection_job_demo`a feladat neve .

2. A bal oldali **menüben** válassza a Kimenetek lehetőséget a **Feladattopológia**csoportban. Ezután válassza a **+ Hozzáadás** és a **Power BI** lehetőséget a legördülő menüből.

3. Válassza **a + Power** > **BI**hozzáadása lehetőséget . Töltse ki az űrlapot a következő értékekkel, majd válassza az **Engedélyezés** lehetőséget:

   |**Beállítás**  |**Ajánlott érték**  |
   |---------|---------|
   |Kimeneti alias  |  CallStream-PowerBI  |
   |Adatkészlet neve  |   sa-adatkészlet  |
   |Tábla neve |  csalárd hívások  |

   ![A Stream Analytics kimenetének konfigurálása](media/stream-analytics-power-bi-dashboard/configure-stream-analytics-output.png)

   > [!WARNING]
   > Ha a Power BI olyan adatkészletet és táblát rendelkezik, amelynek neve megegyezik a Stream Analytics-feladatban megadottakkal, a rendszer felülírja a meglévőket.
   > Azt javasoljuk, hogy ne hozza létre kifejezetten ezt az adatkészletet és táblát a Power BI-fiókjában. Ezek automatikusan létrejönnek, amikor elindítja a Stream Analytics-feladatot, és a feladat elkezdi szivattyúzás kimenet power BI. Ha a feladatlekérdezés nem ad vissza eredményt, az adatkészlet és a tábla nem jön létre.
   >

4. Miután kiválasztotta az **Engedélyezés** lehetőséget, megjelenik egy előugró ablak, ahol meg kell adnia a hitelesítő adatait a Power BI-fiókja hitelesítéséhez. A sikeres hitelesítés után válassza a **Mentés** lehetőséget a beállítások mentéséhez.

8. Kattintson **a Létrehozás gombra.**

Az adatkészlet a következő beállításokkal jön létre:

* **defaultRetentionPolicy: BasicFIFO** - Az adatok FIFO, legfeljebb 200 000 sor.
* **defaultMode: pushStreaming** - Az adatkészlet támogatja mind a streamelési csempék és a hagyományos jelentésalapú vizualizációk (más néven push).

Jelenleg nem hozhat létre adatkészleteket más jelzőkkel.

A Power BI-adatkészletekről a [Power BI REST API-ra](https://msdn.microsoft.com/library/mt203562.aspx) vonatkozó útmutatóban talál további információt.


## <a name="write-the-query"></a>A lekérdezés írása

1. Zárja le a **Kimenetek panelt,** és térjen vissza a munkapanelhez.

2. Kattintson a **Lekérdezés** mezőre. 

3. Írja be a következő lekérdezést. Ez a lekérdezés hasonló a csalásészlelési oktatóanyagban létrehozott önillesztéses lekérdezéshez. A különbség az, hogy ez a lekérdezés`CallStream-PowerBI`eredményeket küld a létrehozott új kimenetnek ( ). 

    >[!NOTE]
    >Ha nem nevezte meg `CallStream` a bemenetet a csalásészlelési oktatóanyagban, helyettesítse a nevét `CallStream` a lekérdezés **FROM** és **JOIN** záradékai között.

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

4. Kattintson a **Mentés** gombra.


## <a name="test-the-query"></a>A lekérdezés tesztelése

Ez a szakasz nem kötelező, de ajánlott. 

1. Ha a TelcoStreaming alkalmazás jelenleg nem fut, indítsa el az alábbi lépésekkel:

    * Nyissa meg a parancssort.
    * Nyissa meg azt a mappát, ahol a telcogenerator.exe és a módosított telcodatagen.exe.config fájlok találhatók.
    * Futtassa az alábbi parancsot:

       `telcodatagen.exe 1000 .2 2`

2. A Stream Analytics-feladat **Lekérdezés** lapján kattintson a `CallStream` bemenet melletti poklokra, majd válassza **a Mintaadatok a bemenetből**lehetőséget.

3. Adja meg, hogy három percnyi adatot szeretne, majd kattintson az **OK**gombra. Várjon, amíg a rendszer értesíti arról, hogy az adatok mintavételezése befejeződött.

4. Kattintson **a Teszt** gombra, és tekintse át az eredményeket.

## <a name="run-the-job"></a>A feladat futtatása

1. Ellenőrizze, hogy fut-e a TelcoStreaming alkalmazás.

2. Nyissa meg a Stream Analytics-feladat **Áttekintő** lapját, és válassza a **Start**lehetőséget.

    ![A Stream Analytics feladat indítása](./media/stream-analytics-power-bi-dashboard/stream-analytics-sa-job-start-output.png)

A Streaming Analytics-feladat megkezdi a csalárd hívások keresését a bejövő adatfolyamban. A feladat létrehozza az adatkészletet és a táblát a Power BI-ban is, és elkezdi az adatok küldését a csalárd hívásokról.


## <a name="create-the-dashboard-in-power-bi"></a>Az irányítópult létrehozása a Power BI-ban

1. Lépjen [Powerbi.com](https://powerbi.com) és jelentkezzen be munkahelyi vagy iskolai fiókjával. Ha a Stream Analytics-feladat lekérdezése eredményeket ad ki, láthatja, hogy az adatkészlet már létrejött:

    ![Adatfolyam-adatkészlet helye a Power BI-ban](./media/stream-analytics-power-bi-dashboard/stream-analytics-streaming-dataset.png)

2. A munkaterületen kattintson a ** + &nbsp;Létrehozás gombra.**

    ![A Létrehozás gomb a Power BI-munkaterületen](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard.png)

3. Hozzon létre egy `Fraudulent Calls`új irányítópultot, és nevezze el.

    ![Irányítópult létrehozása és név a Power BI-munkaterületen](./media/stream-analytics-power-bi-dashboard/pbi-create-dashboard-name.png)

4. Az ablak tetején kattintson a **Csempe hozzáadása gombra,** válassza az **EGYÉNI ADATFOLYAM-ADATOK**lehetőséget, majd kattintson a **Tovább**gombra.

    ![Egyéni adatfolyam-adatfolyam-adatkészlet csempe a Power BI-ban](./media/stream-analytics-power-bi-dashboard/custom-streaming-data.png)

5. A **DATSETS csoportban**jelölje ki az adatkészletet, majd kattintson a **Tovább**gombra.

    ![A streamelési adatkészlet a Power BI-ban](./media/stream-analytics-power-bi-dashboard/your-streaming-dataset.png)

6. A **Képi megjelenítése típusa csoportban**válassza a **Kártya**lehetőséget, majd a **Mezők** listában válassza a csalárd **hívások lehetőséget.**

    ![Az új csempe képi megjelenítési részletei](./media/stream-analytics-power-bi-dashboard/add-fraudulent-calls-tile.png)

7. Kattintson a **Tovább** gombra.

8. Töltse ki a csempe részleteit, például a címet és a feliratot.

    ![Cím és felirat az új csempe](./media/stream-analytics-power-bi-dashboard/pbi-new-tile-details.png)

9. Kattintson az **Alkalmaz** gombra.

    Most van egy csalás számláló!

    ![Csaláselleni küzdelem a Power BI irányítópultján](./media/stream-analytics-power-bi-dashboard/power-bi-fraud-counter-tile.png)

8. A csempe hozzáadásához ismét kövesse a lépéseket (a 4. lépéssel kezdődően). Ezúttal tegye a következőket:

    * Amikor a **Képimegjelenítés típusa gombra**jut, válassza a **Vonaldiagram lehetőséget.** 
    * Adjon hozzá egy tengelyt, és válassza ki **windowend** lehetőséget. 
    * Adjon meg egy értéket, és válassza a **fraudulentcalls** lehetőséget.
    * A **Megjelenítendő időtartomány** beállításnál válassza ki az utolsó 10 percet.

      ![Vonaldiagram csempéjának létrehozása a Power BI-ban](./media/stream-analytics-power-bi-dashboard/pbi-create-tile-line-chart.png)

9. Kattintson **a Tovább**gombra, adjon hozzá egy címet és egy feliratot, majd kattintson **az Alkalmaz**gombra.

     A Power BI irányítópultja mostantól két nézetben is megtekinti a streamelési adatokban észlelt csalárd hívások adatait.

     ![Befejeződött a Power BI irányítópultja, amelyen két csempe látható a csalárd hívásokhoz](./media/stream-analytics-power-bi-dashboard/pbi-dashboard-fraudulent-calls-finished.png)


## <a name="learn-more-about-power-bi"></a>További információ a Power BI-ról

Ez az oktatóanyag bemutatja, hogyan hozhat létre csak néhány féle vizualizációt egy adatkészlethez. A Power BI segítségével más ügyfél-üzleti intelligencia eszközöket hozhat létre a szervezet számára. További ötletekért tekintse meg az alábbi forrásokat:

* A Power BI irányítópultjának egy másik példáját az Első lépések a [Power BI-val](https://youtu.be/L-Z_6P56aas?t=1m58s) című videóban tekinthető meg.
* A Streaming Analytics-feladat kimenetének Power BI-ba történő konfigurálásáról és a Power BI-csoportok használatáról a Stream Analytics kimenetek ről szóló cikk [Power BI](stream-analytics-define-outputs.md#power-bi) szakaszában olvashat [bővebben.](stream-analytics-define-outputs.md) 
* A Power BI általános használatáról az [Irányítópultok a Power BI-ban](https://powerbi.microsoft.com/documentation/powerbi-service-dashboards/)című témakörben talál további információt.


## <a name="learn-about-limitations-and-best-practices"></a>További információ a korlátozásokról és a bevált gyakorlatokról
Jelenleg a Power BI másodpercenként nagyjából egyszer hívható. A folyamatos átvitelű vizualizációk 15 KB-os csomagokat támogatnak. Azon túl, hogy a streamelési vizualizációk sikertelenek (de a leküldéses folytatódik). E korlátozások miatt a Power BI a legtermészetesebb esetekre is alkalmas, amikor az Azure Stream Analytics jelentős adatterhelés-csökkentést végez. Azt javasoljuk, hogy egy Tumbling ablak vagy hopping ablak annak érdekében, hogy az adatleküldéses legutolsó másodpercenként egy leküldéses, és hogy a lekérdezés az átviteli követelmények között landol.

A következő egyenlettel kiszámíthatja az ablak másodpercben történő értékét:

![Egyenlet az érték kiszámításához, hogy az ablak másodpercben](./media/stream-analytics-power-bi-dashboard/compute-window-seconds-equation.png)  

Példa:

* 1000 eszköz küld adatokat egy másodperces időközönként.
* A Power BI Pro termékváltozatát használja, amely óránként 1 000 000 sort támogat.
* Közzé szeretné tenni az eszközönkénti átlagos adatok mennyiségét a Power BI-ban.

Ennek eredményeképpen az egyenlet a következőképpen történik:

![Példakritériumokon alapuló egyenlet](./media/stream-analytics-power-bi-dashboard/power-bi-example-equation.png)  

Ebben a konfigurációban az eredeti lekérdezést a következőkre módosíthatja:

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
Ha a jelszó megváltozott a feladat létrehozása vagy utolsó hitelesítése óta, újra kell hitelesítenie a Power BI-fiókját. Ha az Azure Multi-Factor Authentication konfigurálva van az Azure Active Directory (Azure AD) bérlőn, kéthetente meg kell újítania a Power BI-hitelesítést is. Ha nem újítja meg, olyan tüneteket láthat, mint `Authenticate user error` például a kimenet hiánya vagy a műveletnaplók.

Hasonlóképpen, ha egy feladat a jogkivonat lejárta után indul el, hiba történik, és a feladat sikertelen lesz. A probléma megoldásához állítsa le a futó feladatot, és lépjen a Power BI kimenetére. Az adatvesztés elkerülése érdekében jelölje ki az **Engedélyezési hivatkozás megújítása** lehetőséget, majd indítsa újra a feladatot az Utolsó **leállított idő**ből.

Miután az engedélyezést frissítette a Power BI-val, zöld riasztás jelenik meg az engedélyezési területen, amely azt tükrözi, hogy a probléma megoldódott.

## <a name="get-help"></a>Segítségkérés
További segítségért próbálja ki [az Azure Stream Analytics fórumunkat.](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Bevezetés az Azure Stream Analytics szolgáltatásba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Az Azure Stream Analytics lekérdezési nyelvének hivatkozása](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics Management REST API-ra vonatkozó hivatkozás](https://msdn.microsoft.com/library/azure/dn835031.aspx)
