---
title: Az Azure Stream Analytics-lekérdezések hibaelhárítása
description: Ez a cikk bemutatja a lekérdezések az Azure Stream Analytics-feladatok hibaelhárításához technikákat.
services: stream-analytics
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7a1e440a8dc8f518e272df9e126771df54390ed5
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53161984"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Az Azure Stream Analytics-lekérdezések hibaelhárítása

Ez a cikk ismerteti a gyakori problémák a Stream Analytics-lekérdezések és hogyan háríthatók el őket.

## <a name="query-is-not-producing-expected-output"></a>Lekérdezés nem állít elő várható kimenete 
1.  Vizsgálja meg a hibákat a helyi tesztelés:
    - Az a **lekérdezés** lapon jelölje be **teszt**. A letöltött mintaadatok használatával [a lekérdezés teszteléséhez](stream-analytics-test-query.md). Vizsgáljon meg minden hibát, és próbálja meg kijavítani a őket.   
    - Emellett [tesztelje a lekérdezést közvetlenül a élő bemeneti](stream-analytics-live-data-local-testing.md) Stream Analytics tools for Visual Studio használatával.

2.  Ha [ **Timestamp By**](https://msdn.microsoft.com/library/azure/mt573293.aspx), győződjön meg arról, hogy az események időbélyege későbbi, mint a [feladat kezdési ideje](stream-analytics-out-of-order-and-late-events.md).

3.  Megszüntetheti a közös buktatókat, például:
    - A [ **ahol** ](https://msdn.microsoft.com/library/azure/dn835048.aspx) a lekérdezésben záradék kiszűrte az összes esemény meggátolja, hogy a kimenetet generált.
    - A [ **CAST** ](https://msdn.microsoft.com/azure/stream-analytics/reference/cast-azure-stream-analytics) függvény futása, a feladat sikertelenségét okozó. Írja be a cast hibák elkerülése érdekében használja [ **TRY_CAST** ](https://msdn.microsoft.com/azure/stream-analytics/reference/try-cast-azure-stream-analytics) helyette.
    - Ablakfüggvények használatakor, várjon, amíg a teljes ablak időtartamot a lekérdezés kimenetének megtekintéséhez.
    - Az események időbélyege megelőzi a feladat megkezdési ideje, és ezért azokat a rendszer eldobja.

4.  Győződjön meg arról, esemény sorbarendezésre házirendek megtörténik az elvárt módon. Nyissa meg a **beállítások** válassza [ **események rendezése**](stream-analytics-out-of-order-and-late-events.md). A házirend *nem* használata esetén alkalmazza a **tesztelése** gombra a lekérdezés teszteléséhez. Ez az eredmény egy különbség a tesztelés böngészőben és a feladat az éles üzemben futó. 

5. Hibakeresési naplózási és diagnosztikai naplók segítségével:
    - Használat [Auditnaplók](../azure-resource-manager/resource-group-audit.md), és a szűrőt a azonosíthatja és elháríthatja a hibákat.
    - Használat [feladat-diagnosztikai naplók](stream-analytics-job-diagnostic-logs.md) alapján azonosíthatja és elháríthatja a hibákat.

## <a name="job-is-consuming-too-many-streaming-units"></a>Feladat túl sok Streamelési egységet is használja.
Győződjön meg arról, előnyeit folyamatkezelést biztosítja az Azure Stream Analytics szolgáltatásban. A további [lekérdezési folyamatokkal méretezési](stream-analytics-parallelization.md) Stream Analytics-feladatok a bemeneti partíció konfigurálásával, valamint hangolása az analytics-lekérdezés definícióját.

## <a name="debug-queries-progressively"></a>Lekérdezések fokozatosan hibakeresése

A valós idejű feldolgozásához az adatok néz ki a lekérdezés közepén ismerete hasznos lehet. Be- vagy egy Azure Stream Analytics-feladatot fázisaiban többször olvashatók, mert extra SELECT INTO utasítások írhat. Ezzel a köztes adatokat jelenít meg storage-ba, és vizsgálja meg az adatok helyességét hasonlóan lehetővé teszi *tekintse meg a változók* ehhez Amikor hibakeresést egy programot.

A következő példalekérdezés az Azure Stream Analytics-feladat egy adatfolyam-bemenetre, két referenciaadat típusú bemenetek és az Azure Table Storage-kimenet rendelkezik. A lekérdezés csatlakozik az event hubs és a két hivatkozás blobok nevét és kategóriáját információkat adatait:

![Stream Analytics a SELECT INTO. példalekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Vegye figyelembe, hogy a feladat fut, de nincsenek események előállítása folyamatban van a kimenetben. Az a **figyelés** csempén látható itt, láthatja, hogy a bemeneti van olyan adatokat, de nem tudja, melyik lépés a **CSATLAKOZZON** lehet elvetni az összes esemény miatt.

![A Stream Analytics figyelése csempe](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
Ebben az esetben néhány további SELECT INTO utasítások a "bejelentkezés" a köztes ILLESZTÉS eredményei és a bemenetről beolvasott adatokat adhat hozzá.

Ebben a példában lehetőségekkel bővült két új "ideiglenes kimenetek." Ezek lehetnek bármilyen fogadóba Öntől. Azure Storage itt példaként használjuk:

![Stream Analytics-lekérdezés extra SELECT INTO utasítások hozzáadása](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Majd módosíthatja a lekérdezést, ehhez hasonló:

![Átdolgozta válassza ki az Stream Analytics-lekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Most indítsa el újra a feladatot, és biztosítani, hogy néhány percig. Majd lekérdezheti temp1 és a Visual Studio Cloud Explorer előállítására a következő táblázatok temp2:

**temp1 tábla**
![a SELECT INTO temp1 tábla Stream Analytics-lekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 tábla**
![a SELECT INTO temp2 tábla Stream Analytics-lekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Amint láthatja, temp1 és a temp2 egyaránt rendelkeznek az adatokat, és az oszlop helyesen van-e feltöltve temp2. Azonban mert nem szerepel továbbra is megjeleníthető adat a kimenetben, valami probléma:

![A SELECT INTO output1 tartalmazó tábla nem data Stream Analytics-lekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Az adatok mintavételezésének, biztos lehet majdnem biztos, hogy a probléma van a második való CSATLAKOZÁST. Töltse le a referencia-adatok a blobból, és tekintse meg:

![A SELECT INTO "ref" Stream Analytics lekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Amint láthatja, a GUID, amely a referenciaadatok formátuma formátuma eltér az [a] oszlopában temp2. Ezért az adatok nem érkeznek output1 a várt módon.

Javítsa ki az adatok formátumát, töltse fel a blob hivatkoznak, és próbálkozzon újra:

![A SELECT INTO ideiglenes tábla Stream Analytics-lekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Ebben az esetben az adatok a kimenetben formázott és kitölti a várt módon.

![A SELECT INTO végső táblázatba Stream Analytics-lekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Segítségkérés

További segítségre van szüksége, próbálja meg [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)