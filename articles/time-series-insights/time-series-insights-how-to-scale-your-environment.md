---
title: A Azure Time Series Insights-környezet skálázása | Microsoft Docs
description: Ez a cikk a Azure Time Series Insights környezetének méretezését ismerteti. Használja a Azure Portal a kapacitás hozzáadásához vagy kivonásához egy árképzési SKU-n belül.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 3f03f5ed75c720c9b0daf30d721ef4d2aee9749c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991162"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>A Time Series Insights-környezet skálázása

Ez a cikk azt ismerteti, hogyan módosítható a Time Series Insights-környezet kapacitása a [Azure Portal](https://portal.azure.com)használatával. A kapacitás a beáramlási sebességre, a tárolási kapacitásra és a kiválasztott SKU-ra vonatkozó díjakra érvényes szorzó.

A Azure Portal használatával növelheti vagy csökkentheti a kapacitást egy adott árképzési SKU-n belül.

Azonban a díjszabási csomag módosítása nem engedélyezett. Egy S1 árképzési SKU-val rendelkező környezet például nem konvertálható S2-re, vagy fordítva.

## <a name="ga-limits"></a>GA-korlátok

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>A környezet kapacitásának módosítása

1. A Azure Portalban keresse meg és válassza ki Time Series Insights-környezetét.

1. A Time Series Insights-környezet menüjében válassza a **Konfigurálás**lehetőséget.

   [![configure. png](media/scale-your-environment/configure.png)](media/scale-your-environment/configure.png#lightbox)

1. Állítsa be a **kapacitás** csúszkát, és válassza ki azt a kapacitást, amely megfelel a bejövő és a tárolási kapacitás követelményeinek. Figyelje meg, hogy a **Bejövő forgalom sebessége**, a **tárolókapacitás**és a **becsült költségek** dinamikusan frissülnek a változás hatásának megjelenítéséhez.

   [![csúszka](media/scale-your-environment/slider.png)](media/scale-your-environment/slider.png#lightbox)

   Másik lehetőségként beírhatja a kapacitás szorzójának számát a csúszka jobb oldalán található szövegmezőbe.

1. A környezet méretezéséhez válassza a **Mentés** lehetőséget. A folyamatjelző addig jelenik meg, amíg a módosítás véglegesítése nem történik meg.

1. Győződjön meg arról, hogy az új kapacitás [elegendő a szabályozás megakadályozásához](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Következő lépések

- További információkért tekintse át [a Time Series Insights megőrzésének ismertetése](time-series-insights-concepts-retention.md)című témakört.

- Tudnivalók az [adatmegőrzés konfigurálásáról Azure Time Series Insightsban](time-series-insights-how-to-configure-retention.md).

- Ismerje meg [a környezet megtervezését](time-series-insights-environment-planning.md).