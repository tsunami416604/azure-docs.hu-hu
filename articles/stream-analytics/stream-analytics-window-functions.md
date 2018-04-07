---
title: Bevezetés az Azure Stream Analytics Ablakozó funkciók
description: Ez a cikk ismerteti a három Ablakozó függvény (átfedésmentes, hopping, a késleltetett) az Azure Stream Analytics-feladatok használt.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: c6f5dbe49cb60e3c7b2bc6562acf2d7fd79096ec
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="introduction-to-stream-analytics-window-functions"></a>Bevezetés a Stream Analytics ablak Funkciók
Sok valós idejű adatfolyam-forgatókönyvek akkor kell végrehajtani a műveleteket csak időbeli windows szereplő adatokat. Ablakozó függvény natív támogatása az Azure Stream Analytics, amely helyezi át a tű fejlesztést tesz lehetővé az összetett adatfolyam jelentésfeldolgozó feladatok szerzői alapfunkciója. A Stream Analytics lehetővé teszi a fejlesztők használandó [ **Átfedésmentes**](https://msdn.microsoft.com/library/dn835055.aspx), [ **Hopping** ](https://msdn.microsoft.com/library/dn835041.aspx) és [ **csúszó** ](https://msdn.microsoft.com/library/dn835051.aspx) windows a streamelési adatok historikus műveletek végrehajtásához. Érdemes megjegyezni, hogy minden [ablak](https://msdn.microsoft.com/library/dn835019.aspx) műveletek kimeneti eredményt a **end** ablak. A kimenet az ablak lesz egyszeri esemény alapján az összesítő függvényt használja. Az esemény lesz az időszak végének időbélyegzőjét, és az összes ablak függvényeket rögzített hosszúságú. Végül fontos megjegyezni, hogy az összes ablak Funkciók használatos a [ **GROUP BY** ](https://msdn.microsoft.com/library/dn835023.aspx) záradékban.

![Stream Analytics ablak Funkciók fogalmak](media/stream-analytics-window-functions/stream-analytics-window-functions-conceptual.png)

## <a name="tumbling-window"></a>Átfedésmentes ablak
Átfedésmentes ablak Funkciók adatfolyam szegmentálni történő különböző idő és a művelet végrehajtása előzetesen, például az alábbi példában használt. A kulcs differentiators Átfedésmentes ablak, hogy azok ismételje meg a, nem lehetnek átfedésben és az esemény nem tartozhat egynél több átfedésmentes ablak.

![Stream Analytics ablak Funkciók átfedésmentes – bevezetés](media/stream-analytics-window-functions/stream-analytics-window-functions-tumbling-intro.png)

## <a name="hopping-window"></a>Ugróablak
Szórási ablak Funkciók Ugrás előre meghatározott időtartamon által. Elképzelhető, hogy könnyen gondolunk, hogy Átfedésmentes windows egymást átfedő is, mint, egynél több Hopping ablak eredménykészlet események is tartozhatnak. Ugyanaz, mint egy Átfedésmentes ablak Hopping annak az egy ablak egyszerűen kellene megadnia a ugrási méretének ablak méretének azonosnak kell lennie. 

![Stream Analytics ablak működik szórási – bevezetés](media/stream-analytics-window-functions/stream-analytics-window-functions-hopping-intro.png)

## <a name="sliding-window"></a>Csúszóablak
Mozgó ablak Funkciók Átfedésmentes és a windows, Hopping előállít egy kimeneti **csak** egy esemény bekövetkezésekor. Minden ablak lesz legalább egy eseményt, és az ablak folyamatosan mozgatja előre egy "(epszilon). Például a Hopping Windows események több késleltetett ablak is tartozhatnak.

![Bevezetés a késleltetett Stream Analytics ablak Funkciók](media/stream-analytics-window-functions/stream-analytics-window-functions-sliding-intro.png)

## <a name="getting-help-with-window-functions"></a>Az ablak functions kapcsolatos segítség kérése
További támogatásért keresse fel az [Azure Stream Analytics-fórumot](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

