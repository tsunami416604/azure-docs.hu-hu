---
title: A Azure Time Series Insights-környezet skálázása | Microsoft Docs
description: Ez a cikk a Azure Time Series Insights környezetének méretezését ismerteti. Használja a Azure Portal a kapacitás hozzáadásához vagy kivonásához egy árképzési SKU-n belül.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: a899de22137decc1eb1578369a2751710c17abda
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332895"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>A Time Series Insights-környezet skálázása

Ez a cikk azt ismerteti, hogyan módosítható a Time Series Insights-környezet kapacitása a [Azure Portal](https://portal.azure.com)használatával. A kapacitás a beáramlási sebességre, a tárolási kapacitásra és a kiválasztott SKU-ra vonatkozó díjakra érvényes szorzó.

A Azure Portal használatával növelheti vagy csökkentheti a kapacitást egy adott árképzési SKU-n belül.

Azonban a díjszabási csomag módosítása nem engedélyezett. Egy S1 árképzési SKU-val rendelkező környezet például nem konvertálható S2-re, vagy fordítva.

## <a name="s1-sku-ingress-rates-and-capacities"></a>S1 SKU – bejövő forgalom aránya és kapacitása

| S1 SKU kapacitása | Bejövő forgalom aránya | Maximális tárolókapacitás
| --- | --- | --- |
| 1 | 1 GB (1 000 000 esemény) | 30 GB (30 000 000 esemény) havonta |
| 10 | 10 GB (10 000 000 esemény) | 300 GB (300 000 000 esemény)/hó |

## <a name="s2-sku-ingress-rates-and-capacities"></a>S2 SKU – bejövő forgalom aránya és kapacitása

| S2 SKU kapacitás | Bejövő forgalom aránya | Maximális tárolókapacitás
| --- | --- | --- |
| 1 | 10 GB (10 000 000 esemény) | 300 GB (300 000 000 esemény)/hó |
| 10 | 100 GB (100 000 000 esemény) | 3 TB (3 000 000 000 esemény) havonta |

A kapacitások lineárisan méretezhetők, ezért a 2. kapacitású S1 SKU 2 GB-ot (2 000 000), napi beléptetési arányt és 60 GB-ot (60 000 000 eseményt) támogat.

## <a name="change-the-capacity-of-your-environment"></a>A környezet kapacitásának módosítása

1. A Azure Portalban keresse meg és válassza ki Time Series Insights-környezetét.

1. A Time Series Insights-környezet menüjében válassza a **Konfigurálás**lehetőséget.

   [@no__t -1configure. png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Állítsa be a **kapacitás** csúszkát, és válassza ki azt a kapacitást, amely megfelel a bejövő és a tárolási kapacitás követelményeinek. Figyelje meg, hogy a **Bejövő forgalom sebessége**, a **tárolókapacitás**és a **becsült költségek** dinamikusan frissülnek a változás hatásának megjelenítéséhez.

   [@no__t – 1Slider](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Másik lehetőségként beírhatja a kapacitás szorzójának számát a csúszka jobb oldalán található szövegmezőbe.

1. A környezet méretezéséhez válassza a **Mentés** lehetőséget. A folyamatjelző addig jelenik meg, amíg a módosítás véglegesítése nem történik meg.

1. Győződjön meg arról, hogy az új kapacitás [elegendő a szabályozás megakadályozásához](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Következő lépések

- További információkért tekintse át [a Time Series Insights megőrzésének ismertetése](time-series-insights-concepts-retention.md)című témakört.

- Tudnivalók az [adatmegőrzés konfigurálásáról Azure Time Series Insightsban](time-series-insights-how-to-configure-retention.md).

- Ismerje meg [a környezet megtervezését](time-series-insights-environment-planning.md).