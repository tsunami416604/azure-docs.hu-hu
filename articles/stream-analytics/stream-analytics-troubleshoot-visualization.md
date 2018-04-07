---
title: Megjelenítheti és Azure Stream Analytics-feladatok hibaelhárítása
description: Ez a cikk ismerteti a funkcióval a diagnosztika diagram elvégzendő önkiszolgáló hibaelhárítási Stream Analytics-feladat megjelenítése.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/28/2017
ms.openlocfilehash: 523802f1f9a1dda19c5b6a66da7bc26fee851bd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Megjelenítheti és a Stream Analytics-feladatok hibaelhárítása
A Stream Analytics más felhőalapú technológiákat a hibaelhárítás néha szükséges megismerhetők miért egy feladat nem készít a várt kimeneti (vagy adott függetlenül attól, hogy a kimenetet). Ilyen kialakításával szem előtt a Stream Analytics lehetővé teszi a folyamatos átviteli feladatnak megjelenítésére. Ez is modellezési eszközként lesz szüksége, és az ügyféloldali előnye a munkahelyi e megkövetelését dokumentációját.

A képi megjelenítés panelen a bemeneti adatok legyenek láthatók, és a lekérdezés végrehajtása és majd a mindegyikhez konfigurálva. Kapcsolat vagy konfigurációs probléma több nyilvánvaló válhat, és is hasznos lehet a konfigurációs vizuális ábrázolását megjelenítéséhez.

## <a name="using-the-diagnosis-diagram-tool"></a>A diagnosztikai diagram eszközzel
A vizualizálója eléréséhez egyszerűen kattintson a Stream Analytics-feladat "Beállítások" területén "Diagnosztika diagram" gombra.

![stream-analytics-troubleshoot-visualization-diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Minden bemeneti és kimeneti összetevő színkóddal jelzi az aktuális állapot látható módon.

![stream-analytics-troubleshoot-visualization-input-map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Ha a felhasználó szeretné tudni, hogy az adatok folyamata minták belül egy feladat közbenső lekérdezések lépéseit tekintse meg, a képi megjelenítés eszköz áttekintést nyújt a bontásban tartalmazza a lekérdezés az összetevő lépést, és a folyamat során. A lekérdezés-szerkesztő ablaktábla módon megfelelő szakaszához minden lekérdezési lépés parancsának jeleníti meg. 

![stream-analytics-troubleshoot-visualization-intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)

## <a name="next-steps"></a>További lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

