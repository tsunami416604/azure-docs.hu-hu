---
title: Válassza ki azokat az Azure Stream Analytics-lekérdezések hibakeresése
description: Ez a cikk ismerteti az Azure Stream Analytics-feladat közepes lekérdezés minta a lekérdezés szintaxisa a SELECT INTO utasítások használatával.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/20/2017
ms.openlocfilehash: b056d4c29464451d3dc0ef62437f934535820489
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697992"
---
# <a name="debug-queries-by-using-select-into-statements"></a>Hibakeresés a lekérdezések a SELECT INTO utasítások használatával

A valós idejű feldolgozásához az adatok néz ki a lekérdezés közepén ismerete hasznos lehet. Be- vagy egy Azure Stream Analytics-feladatot fázisaiban többször olvashatók, mert extra SELECT INTO utasítások írhat. Ezzel a köztes adatokat jelenít meg storage-ba, és vizsgálja meg az adatok helyességét hasonlóan lehetővé teszi *tekintse meg a változók* ehhez Amikor hibakeresést egy programot.

## <a name="use-select-into-to-check-the-data-stream"></a>A SELECT INTO használata az adatfolyam ellenőrzéséhez

A következő példalekérdezés az Azure Stream Analytics-feladat egy adatfolyam-bemenetre, két referenciaadat típusú bemenetek és az Azure Table Storage-kimenet rendelkezik. A lekérdezés csatlakozik az event hubs és a két hivatkozás blobok nevét és kategóriáját információkat adatait:

![A SELECT INTO. példalekérdezés](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Vegye figyelembe, hogy a feladat fut, de nincsenek események előállítása folyamatban van a kimenetben. Az a **figyelés** csempén látható itt, láthatja, hogy a bemeneti van olyan adatokat, de nem tudja, melyik lépés a **CSATLAKOZZON** lehet elvetni az összes esemény miatt.

![A figyelés csempe](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)
 
Ebben az esetben néhány további SELECT INTO utasítások a "bejelentkezés" a köztes ILLESZTÉS eredményei és a bemenetről beolvasott adatokat adhat hozzá.

Ebben a példában lehetőségekkel bővült két új "ideiglenes kimenetek." Ezek lehetnek bármilyen fogadóba Öntől. Azure Storage itt példaként használjuk:

![Extra SELECT INTO utasítások hozzáadása](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

Majd módosíthatja a lekérdezést, ehhez hasonló:

![Átdolgozta SELECT INTO lekérdezést](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Most indítsa el újra a feladatot, és biztosítani, hogy néhány percig. Majd lekérdezheti temp1 és a Visual Studio Cloud Explorer előállítására a következő táblázatok temp2:

**temp1 tábla**
![a SELECT INTO temp1 tábla](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2 tábla**
![a SELECT INTO temp2 tábla](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Amint láthatja, temp1 és a temp2 egyaránt rendelkeznek az adatokat, és az oszlop helyesen van-e feltöltve temp2. Azonban mert nem szerepel továbbra is megjeleníthető adat a kimenetben, valami probléma:

![A SELECT INTO output1 tábla adatot nem tartalmazó](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Az adatok mintavételezésének, biztos lehet majdnem biztos, hogy a probléma van a második való CSATLAKOZÁST. Töltse le a referencia-adatok a blobból, és tekintse meg:

![A SELECT INTO ref tábla](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Amint láthatja, a GUID, amely a referenciaadatok formátuma formátuma eltér az [a] oszlopában temp2. Ezért az adatok nem érkeznek output1 a várt módon.

Javítsa ki az adatok formátumát, töltse fel a blob hivatkoznak, és próbálkozzon újra:

![A SELECT INTO ideiglenes tábla](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Ebben az esetben az adatok a kimenetben formázott és kitölti a várt módon.

![A SELECT INTO végső táblázatba](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)


## <a name="get-help"></a>Segítségkérés

További segítségre van szüksége, próbálja meg [Azure Stream Analytics-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>További lépések

* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

