---
title: A környezet skálázása – Azure Time Series Insights | Microsoft Docs
description: Ismerje meg, hogyan méretezheti Azure Time Series Insights-környezetét a Azure Portal használatával.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/21/2020
ms.custom: seodec18
ms.openlocfilehash: 23efda2793ef5d323089ee5b72fb1ea873de6b20
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310985"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>A Time Series Insights-környezet skálázása

Ez a cikk azt ismerteti, hogyan módosítható a Time Series Insights-környezet kapacitása a [Azure Portal](https://portal.azure.com)használatával. A kapacitás a beáramlási sebességre, a tárolási kapacitásra és a kiválasztott SKU-ra vonatkozó díjakra érvényes szorzó.

A Azure Portal használatával növelheti vagy csökkentheti a kapacitást egy adott árképzési SKU-n belül.

Azonban a díjszabási csomag módosítása nem engedélyezett. Egy S1 árképzési SKU-val rendelkező környezet például nem konvertálható S2-re, vagy fordítva.

## <a name="ga-limits"></a>GA-korlátok

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>A környezet kapacitásának módosítása

1. A Azure Portalban keresse meg és válassza ki Time Series Insights-környezetét.

1. A Time Series Insights-környezet menüjében válassza a **tárolási konfiguráció**elemet.

   [a Time Series Insights kapacitásának ![konfigurálása](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Állítsa be a **kapacitás** csúszkát, és válassza ki azt a kapacitást, amely megfelel a bejövő és a tárolási kapacitás követelményeinek. Figyelje meg, hogy a **Bejövő forgalom sebessége**, a **tárolókapacitás**és a **becsült költségek** dinamikusan frissülnek a változás hatásának megjelenítéséhez.

   [a környezet konfigurálása a kapacitás csúszka használatával ![](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   Másik lehetőségként beírhatja a kapacitás szorzójának számát a csúszka jobb oldalán található szövegmezőbe.

1. A környezet méretezéséhez válassza a **Mentés** lehetőséget. A folyamatjelző addig jelenik meg, amíg a módosítás véglegesítése nem történik meg.

1. Győződjön meg arról, hogy az új kapacitás [elegendő a szabályozás megakadályozásához](time-series-insights-diagnose-and-solve-problems.md).

## <a name="next-steps"></a>Következő lépések

- További információkért tekintse át [a Time Series Insights megőrzésének ismertetése](time-series-insights-concepts-retention.md)című témakört.

- Tudnivalók az [adatmegőrzés konfigurálásáról Azure Time Series Insightsban](time-series-insights-how-to-configure-retention.md).

- Ismerje meg [a környezet megtervezését](time-series-insights-environment-planning.md).