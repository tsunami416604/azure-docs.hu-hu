---
title: A környezet méretezése – Azure Time Series Insights| Microsoft dokumentumok
description: Ismerje meg, hogyan skálázhatja az Azure Time Series Insights-környezetet az Azure Portalon.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76310985"
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>A Time Series Insights-környezet méretezése

Ez a cikk ismerteti, hogyan módosíthatja a Time Series Insights-környezet kapacitását az [Azure Portal](https://portal.azure.com)használatával. A kapacitás a kiválasztott termékváltozathoz kapcsolódó be- és tárolási kapacitásra alkalmazott szorzó.

Az Azure Portal használatával növelheti vagy csökkentheti a kapacitást egy adott díjszabási termékváltozaton belül.

Azonban a tarifacsomag termékváltozat módosítása nem engedélyezett. Például egy s1-es árképzési termékváltozattal rendelkező környezet nem konvertálható S2-vé, vagy fordítva.

## <a name="ga-limits"></a>GA határértékek

[!INCLUDE [Azure Time Series Insights GA limits](../../includes/time-series-insights-ga-limits.md)]

## <a name="change-the-capacity-of-your-environment"></a>A környezet kapacitásának módosítása

1. Az Azure Portalon keresse meg és válassza ki a Time Series Insights-környezetet.

1. A Time Series Insights környezet menüjében válassza a **Tároló konfigurációja lehetőséget.**

   [![A Time Series Insights-kapacitás konfigurálása](media/scale-your-environment/scale-your-environment-configure.png)](media/scale-your-environment/scale-your-environment-configure.png#lightbox)

1. Állítsa be a **Kapacitás** csúszkát, hogy kiválassza a be- és a be- és tárolási sebesség követelményeinek megfelelő kapacitást. Figyelje meg a **be- éstárolási arányt**, **a tárolási kapacitást**és a **becsült költség** frissítést dinamikusan a változás hatásának megjelenítéséhez.

   [![A környezet konfigurálása a kapacitáscsúszkával](media/scale-your-environment/scale-your-environment-slider.png)](media/scale-your-environment/scale-your-environment-slider.png#lightbox)

   Másik lehetőségként beírhatja a kapacitásszorzó számát a csúszka jobb oldalán lévő szövegmezőbe.

1. A környezet méretezéséhez válassza a **Mentés** lehetőséget. A folyamatjelző a módosítás véglegesítéséig jelenik meg.

1. Ellenőrizze, hogy az új kapacitás [elegendő-e a szabályozás megakadályozásához.](time-series-insights-diagnose-and-solve-problems.md)

## <a name="next-steps"></a>További lépések

- További információért tekintse át a Megértés megőrzése a Time Series Insights alkalmazásban című [témakört.](time-series-insights-concepts-retention.md)

- Ismerje meg [az adatmegőrzés konfigurálását az Azure Time Series Insightsban.](time-series-insights-how-to-configure-retention.md)

- További információ [a környezet megtervezéséről.](time-series-insights-environment-planning.md)