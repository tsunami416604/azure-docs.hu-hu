---
title: "Az Azure idő adatsorozat Insights környezet méretezése |} Microsoft Docs"
description: "Ez a cikk ismerteti az Azure idő adatsorozat Insights környezet méretezése. Az Azure portál segítségével növekménye árképzési SKU belül."
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: d3c8b2b1ba220bf07a2bcdbd7fb26b94f897981f
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>A idő adatsorozat Insights környezet méretezése

Ez a cikk ismerteti a kapacitás, a környezet módosításáról a idő adatsorozat Insights környezet az Azure portál használatával. A többszöröző alkalmazza a érkező sebessége, tárolási kapacitás és a kiválasztott termékváltozat költsége. 

Az Azure portál segítségével növelheti vagy csökkentheti a belül egy adott árképzési termékváltozat. 

Azonban ez a tarifacsomag Termékváltozat nem módosítható. Például a Termékváltozat árképzési egy S1 tartalmazó környezet nem konvertálható egy S2 vagy fordítva. 


## <a name="s1-sku-ingress-rates-and-capacities"></a>S1 SKU érkező sebességét és a kapacitások

| S1 Termékváltozat-kapacitás | Érkező arány | Tárolókészlet teljes tárhelykapacitását
| --- | --- | --- |
| 1 | 1 GB (1 millió esemény) | Havonta 30 GB (30 millió esemény) |
| 10 | 10 GB-os (10 millió esemény) | Havonta 300 GB (300 millió esemény) |

## <a name="s2-sku-ingress-rates-and-capacities"></a>S2 SKU érkező sebességét és a kapacitások

| S2 Termékváltozat-kapacitás | Érkező arány | Tárolókészlet teljes tárhelykapacitását
| --- | --- | --- |
| 1 | 10 GB-os (10 millió esemény) | Havonta 300 GB (300 millió esemény) |
| 10 | 100 GB (100 millió esemény) | Havonta 3 TB (3 milliárd esemény) |

Kapacitások lineárisan lehessen méretezni, így egy S1 SKU 2 kapacitással rendelkező havonta támogatja a 2 GB (2 millió) események / nap érkező sebessége és 60 GB (60 millió esemény).

## <a name="change-the-capacity-of-your-environment"></a>A kapacitás, a környezet módosítása
1. Az Azure-portálon keresse meg és válassza ki azt az időt adatsorozat Insights környezetet. 

2. Az a **beállítások** elemcsoportban válasszon **konfigurálása**.

   ![Configure.png](media/scale-your-environment/configure.png)

3. Módosítsa a **kapacitás** csúszkát a, amely megfelel az érkező sebesség és a tárolási kapacitás. Figyelje meg a **érkező arány**, **tárolási kapacitás**, és **becsült költség** frissítés dinamikusan milyen hatással bír a változtatás megjelenítéséhez. 

   ![A csúszka](media/scale-your-environment/slider.png)

   Másik lehetőségként adhatja meg a kapacitás többszöröző száma a szövegmezőbe, a csúszka jobbra. 

4. Válassza ki **mentése** méretezése a környezetben. Folyamatjelző jelenik meg, amíg a módosítás véglegesítve lesz, rövid ideig. 

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Ellenőrizze, hogy az új kapacitása elegendő, hogy megakadályozza a sávszélesség-szabályozás](time-series-insights-diagnose-and-solve-problems.md).
