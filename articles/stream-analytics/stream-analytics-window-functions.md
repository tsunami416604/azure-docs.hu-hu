---
title: Bevezetés az Azure Stream Analytics Ablakozó funkciók
description: Ez a cikk ismerteti a négy Ablakozó függvény (átfedésmentes, hopping, a késleltetett, munkamenet) az Azure Stream Analytics-feladatok használt.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/07/2018
ms.openlocfilehash: 2650058e277bc0338c779655ce381be046fb120a
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
---
# <a name="introduction-to-stream-analytics-windowing-functions"></a>A Stream Analytics Ablakozó függvény bemutatása
Forgatókönyvek idejű adatfolyamos műveletet hajt végre a historikus windows szereplő adatokat a megszokott mintát követi. A Stream Analytics Ablakozó függvény, natív támogatása rendelkezik, így a fejlesztők csatlakozhatnak Szerző összetett adatfolyam jelentésfeldolgozó feladatok.

Választható historikus windows négy fő típusba sorolhatók: [ **Átfedésmentes**](https://msdn.microsoft.com/azure/stream-analytics/reference/tumbling-window-azure-stream-analytics), [ **Hopping**](https://msdn.microsoft.com/azure/stream-analytics/reference/hopping-window-azure-stream-analytics), [  **A késleltetett**](https://msdn.microsoft.com/azure/stream-analytics/reference/sliding-window-azure-stream-analytics), és [ **munkamenet** ](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics) windows.  Az ablak funkciók használata a [ **GROUP BY** ](https://msdn.microsoft.com/azure/stream-analytics/reference/group-by-azure-stream-analytics) a lekérdezés szintaxisa a Stream Analytics-feladatok záradékában.

Minden a [Ablakozó](https://msdn.microsoft.com/azure/stream-analytics/reference/windowing-azure-stream-analytics) műveletek kimeneti eredményt a **end** ablak. A kimenet az ablak lesz egyszeri esemény alapján az összesítő függvényt használja. A kimeneti esemény lesz az időszak végének időbélyegzőjét, és az összes ablak függvényeket rögzített hosszúságú. 

![Stream Analytics ablak Funkciók fogalmak](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Átfedésmentes ablak
Átfedésmentes ablak Funkciók adatfolyam szegmentálni történő különböző idő és a művelet végrehajtása előzetesen, például az alábbi példában használt. A kulcs differentiators Átfedésmentes ablak, akkor ismételje meg a, tegye nem hozzon létre, és az esemény nem tartozhat egynél több átfedésmentes ablak.

![Stream Analytics átfedésmentes ablak](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Ugróablak
Szórási ablak Funkciók Ugrás előre meghatározott időtartamon által. Elképzelhető, hogy könnyen gondolunk, hogy Átfedésmentes windows egymást átfedő is, mint, egynél több Hopping ablak eredménykészlet események is tartozhatnak. Hopping ablak ugyanaz, mint egy Átfedésmentes ablak adja meg a ugrási méretének ablak méretének azonosnak kell lennie. 

![Stream Analytics ugróablak](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Csúszóablak
Mozgó ablak Funkciók Átfedésmentes és a windows, Hopping előállít egy kimeneti **csak** egy esemény bekövetkezésekor. Minden ablak lesz legalább egy eseményt, és az ablak folyamatosan mozgatja előre egy "(epszilon). Például a hopping windows, események egynél több csúszóablak is tartozhatnak.

![Stream Analytics csúszóablak](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="session-window-preview"></a>Munkamenet ablakban (előzetes verzió)
Munkamenet ablak Funkciók csoport azonos időpontban érkező események kiszűrése időszakokra, ha nincsenek adatok. Három fő paraméterekkel rendelkezik:, időtúllépés maximális időtartam, és a particionálási kulcsot (nem kötelező).

![Stream Analytics munkamenet ablak](media/stream-analytics-window-functions/stream-analytics-window-functions-session-intro.png)

A munkamenet ablak akkor kezdődik, amikor az első esemény akkor következik be. Ha a megadott időkorláton belül egy másik eseményt az utolsó feldolgozott esemény következik be, majd az ablak kiterjeszti az új esemény. Ellenkező esetben az időkorláton belül események nem fordulhat elő, ha majd a időszak vége, az időkorlát.

Események a megadott időkorláton belül tartsa elő, ha a munkamenet ablak fog tartani maximális időtartam eléréséig kiterjesztése. A maximális időtartam ellenőrzése intervallumok akkora, mint a megadott maximális időtartama vannak beállítva. Például ha maximális időtartama 10, majd az ellenőrzést, ha az az időszak haladhatja meg a maximális időtartam fog hiba akkor fordulhat elő a t = 0, 10, 20, 30, stb.

A partíciós kulcs valósul meg, ha az események együtt a kulcs szerint vannak csoportosítva, és munkamenet ablak vonatkozik az egyes csoportok egymástól függetlenül. A particionálás akkor hasznos olyan esetekben, ahol különböző munkamenet windows kell a különböző felhasználók vagy eszközök.


## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

