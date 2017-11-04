---
title: "Megjelenítheti és a Stream Analytics-feladatok hibaelhárítása |} Microsoft Docs"
description: "Megtudhatja, hogyan jelenítheti meg a Stream Analytics feladat csővezeték önkiszolgáló hibaelhárítási a diagnosztika diagram szolgáltatással."
keywords: 
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: d87841cd-c59f-4a46-b46e-8b904fdc12e9
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.openlocfilehash: 820b73a5dbf9bb108e189313cf6ee2b924ab04c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="visualize-and-troubleshoot-stream-analytics-jobs"></a>Megjelenítheti és a Stream Analytics-feladatok hibaelhárítása
A Stream Analytics más felhőalapú technológiákat a hibaelhárítás néha szükséges megismerhetők miért egy feladat nem készít a várt kimeneti (vagy adott függetlenül attól, hogy a kimenetet). Ilyen kialakításával szem előtt a Stream Analytics lehetővé teszi a folyamatos átviteli feladatnak megjelenítésére. Ez is modellezési eszközként lesz szüksége, és az ügyféloldali előnye a munkahelyi e megkövetelését dokumentációját.

A képi megjelenítés panelen a bemeneti adatok legyenek láthatók, és a lekérdezés végrehajtása és majd a mindegyikhez konfigurálva. Kapcsolat vagy konfigurációs probléma több nyilvánvaló válhat, és is hasznos lehet a konfigurációs vizuális ábrázolását megjelenítéséhez.

## <a name="using-the-diagnosis-diagram-tool"></a>A diagnosztikai diagram eszközzel
A vizualizálója eléréséhez egyszerűen kattintson a Stream Analytics-feladat "Beállítások" területén "Diagnosztika diagram" gombra.

![Stream-Analytics-troubleshoot-Visualization-Diagnosis-diagram](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-diagnosis-diagram1.png)

Minden bemeneti és kimeneti összetevő színkóddal jelzi az aktuális állapot látható módon.

![Stream-Analytics-troubleshoot-Visualization-input-Map](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-input-map.png)

Ha a felhasználó szeretné tudni, hogy az adatok folyamata minták belül egy feladat közbenső lekérdezések lépéseit tekintse meg, a képi megjelenítés eszköz áttekintést nyújt a bontásban tartalmazza a lekérdezés az összetevő lépést, és a folyamat során. A lekérdezés-szerkesztő ablaktábla módon megfelelő szakaszához minden lekérdezési lépés parancsának jeleníti meg. 

![Stream-Analytics-troubleshoot-Visualization-Intermediate-steps](./media/stream-analytics-troubleshoot-visualization/stream-analytics-troubleshoot-visualization-intermediate-steps.png)

## <a name="next-steps"></a>Következő lépések
* [Az Azure Stream Analytics bemutatása](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md) (Azure Stream Analytics-feladatok méretezése)
* [Azure Stream Analytics Query Language Reference](https://msdn.microsoft.com/library/azure/dn834998.aspx) (Referencia az Azure Stream Analytics lekérdezési nyelvhez)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](https://msdn.microsoft.com/library/azure/dn835031.aspx)

