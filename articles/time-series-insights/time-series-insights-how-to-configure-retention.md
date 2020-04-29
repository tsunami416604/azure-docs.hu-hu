---
title: Az adatmegőrzés konfigurálása a környezetben – Azure Time Series Insights | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhatja az adatmegőrzést a Azure Time Series Insights-környezetben.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: seodec18
ms.openlocfilehash: 524e47e5be142b720687ad48ee9407da23284bd4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81605053"
---
# <a name="configuring-retention-in-time-series-insights"></a>Adatmegőrzés konfigurálása Time Series Insights

Ez a cikk azt ismerteti, hogyan konfigurálható az **adatmegőrzési idő** és a **tárolási korlát túllépte a Azure Time Series Insights viselkedését** .

## <a name="summary"></a>Összefoglalás

Az egyes Azure Time Series Insights környezetek az **adatmegőrzési idő**konfigurálására szolgáló beállítással rendelkeznek. Az érték 1 – 400 nap. Az adatok törlődnek a környezet tárolókapacitása vagy a megőrzési időtartam (1-400) alapján, attól függően, hogy melyik következik be először.

Minden Time Series Insights környezetnek van egy további **tárolási korlátja, amely túllépte a működést**. Ezzel a beállítással szabályozható a bejövő és a kiürítési viselkedés, ha elérik a környezetek maximális kapacitását. Két viselkedés közül választhat:

- **Régi adattörlés** (alapértelmezett)
- **Bejövő forgalom szüneteltetése**

A beállítások jobb megismeréséhez tekintse át a [Time Series Insights megőrzésének ismertetése](time-series-insights-concepts-retention.md)című témakört.  

## <a name="configure-data-retention"></a>Az adatmegőrzés konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keresse meg a meglévő Time Series Insights-környezetét. A Azure Portal bal oldalán található menüben válassza az **összes erőforrás** lehetőséget. Válassza ki az Azure Time Series Insights-környezetet.

1. A **Beállítások** fejléc alatt válassza a **tárolási konfiguráció**elemet.

    [![A beállítások területen válassza a tárolási konfiguráció elemet.](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. Válassza ki az **adatmegőrzési időt (napokban)** az adatmegőrzés konfigurálásához a csúszka sáv használatával, vagy írjon be egy számot a szövegmezőbe.

1. Figyelje meg a **kapacitás** beállítását, mivel ez a konfiguráció az adattároláshoz szükséges maximális mennyiségű adateseményre és teljes tárolókapacitásra is hatással van.

1. A **tárolási korlát túllépte a viselkedési** beállítást. Válassza a **régi adattörlés** vagy a **Bejövő forgalom szüneteltetése** lehetőséget.

    [![Bejövő forgalom szüneteltetése – elfogadás és mentés.](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. Tekintse át a dokumentációt, és Ismerje meg az adatvesztés lehetséges kockázatait. A módosítások konfigurálásához válassza a **Mentés** lehetőséget.

## <a name="next-steps"></a>További lépések

- További információkért tekintse át [a Time Series Insights megőrzésének ismertetése](time-series-insights-concepts-retention.md)című témakört.

- Ismerje meg [, hogyan méretezheti Time Series Insights-környezetét](time-series-insights-how-to-scale-your-environment.md).

- Ismerje meg [a környezet megtervezését](time-series-insights-environment-planning.md).
