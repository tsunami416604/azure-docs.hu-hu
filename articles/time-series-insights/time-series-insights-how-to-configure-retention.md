---
title: Az adatmegőrzés konfigurálása a környezetben – Azure Time Series Insights | Microsoft dokumentumok
description: Ismerje meg, hogyan konfigurálhatja az adatmegőrzést az Azure Time Series Insights környezetben.
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
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605053"
---
# <a name="configuring-retention-in-time-series-insights"></a>Adatmegőrzés konfigurálása a Time Series Insights ban

Ez a cikk ismerteti, hogyan konfigurálhatja **az adatmegőrzési idő** és **a tárolási korlát túllépte a viselkedést** az Azure Time Series Insights.

## <a name="summary"></a>Összefoglalás

Minden Azure Time Series Insights-környezet rendelkezik egy beállítással **az adatmegőrzési idő**konfigurálásához. Az érték 1 és 400 nap között tart. Az adatok törlődnek a környezeti tárolókapacitás vagy megőrzési időtartam (1-400) alapján, amelyik előbb következik be.

Minden Time Series Insights-környezetben egy további **beállítás túllépte a működést.** Ez a beállítás szabályozza a be- és törlési viselkedést, amikor egy környezet maximális kapacitása eléri. Két viselkedés közül választhat:

- **Régi adatok törlése** (alapértelmezett)
- **Be- és be- és betolatás szüneteltetése**

A beállítások jobb megértéséhez tekintse át [a Tudásmegőrzés a Munkaidő-sorozat elemzési adatai című témakört.](time-series-insights-concepts-retention.md)  

## <a name="configure-data-retention"></a>Az adatmegőrzés konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keresse meg a meglévő Time Series Insights-környezetet. Válassza az **Összes erőforrás az** Azure Portal bal oldalán található menüben. Válassza ki az Azure Time Series Insights-környezetet.

1. A **Beállítások** cím alatt válassza a **Tároló konfigurációja lehetőséget.**

    [![A Beállítások csoportban válassza a Tároló konfigurációja lehetőséget.](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. Válassza ki az **adatmegőrzési időt (napokban)** a megőrzést a csúszka segítségével, vagy írjon be egy számot a szövegmezőbe.

1. Vegye figyelembe a **Kapacitás** beállítást, mivel ez a konfiguráció hatással van az adatok maximális mennyiségére és az adatok tárolására szolgáló teljes tárolókapacitásra.

1. A tárolási korlát lépte túl a **viselkedési** beállítást. Válassza **a Régi adatok kiürítése** vagy a **Be-műveletek viselkedésének szüneteltetése lehetőséget.**

    [![Be- fogadja el és mentse.](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. Tekintse át a dokumentációt, hogy tisztában lévén megismerheti az adatvesztés lehetséges kockázatait. A módosítások konfigurálásához válassza a **Mentés** gombot.

## <a name="next-steps"></a>További lépések

- További információért tekintse át a Megértés megőrzése a Time Series Insights alkalmazásban című [témakört.](time-series-insights-concepts-retention.md)

- [Ismerje meg, hogyan méretezheti a Time Series Insights-környezetet.](time-series-insights-how-to-scale-your-environment.md)

- További információ [a környezet megtervezéséről.](time-series-insights-environment-planning.md)
