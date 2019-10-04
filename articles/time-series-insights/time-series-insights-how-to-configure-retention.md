---
title: Az adatmegőrzés konfigurálása a Azure Time Series Insights-környezetben | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható a megőrzés a Azure Time Series Insights környezetben.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: e5cc1489af1dce3a9a57b96a3240c63e0395c33a
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947227"
---
# <a name="configuring-retention-in-time-series-insights"></a>Adatmegőrzés konfigurálása Time Series Insights

Ez a cikk azt ismerteti, hogyan konfigurálható az adatmegőrzési **idő** és a **tárolási korlát túllépte** a Azure Time Series Insights viselkedését.

## <a name="summary"></a>Összegzés

Az egyes Time Series Insights (ÁME-) környezetek az adatmegőrzési **idő**konfigurálására vonatkozó beállítással rendelkeznek. Az érték 1 – 400 nap. Az adatok törlődnek a környezet tárolókapacitása vagy a megőrzési időtartam (1-400) alapján, attól függően, hogy melyik következik be először.

Az egyes ÁME-környezetek további **tárolási korlátja túllépte**a működést. Ezzel a beállítással szabályozható a bejövő és a kiürítési viselkedés, ha elérik a környezetek maximális kapacitását. Két viselkedés közül választhat:

- **Régi adattörlés** alapértelmezett
- **Bejövő forgalom szüneteltetése**

A beállítások jobb megismeréséhez tekintse át a [Time Series Insights megőrzésének ismertetése](time-series-insights-concepts-retention.md)című témakört.  

## <a name="configure-data-retention"></a>Az adatmegőrzés konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Keresse meg a meglévő Time Series Insights-környezetbe. A Azure Portal bal oldalán található menüben válassza az **összes erőforrás** lehetőséget. Válassza ki az Azure Time Series Insights-környezetet.

1. A **Beállítások** fejléc alatt válassza a **Konfigurálás**lehetőséget.

1. Válassza ki az adatmegőrzési **időt** az adatmegőrzés konfigurálásához a csúszka sáv használatával, vagy írjon be egy számot a szövegmezőbe.

1. Figyelje meg a **kapacitás** beállítását, mivel ez a konfiguráció az adattároláshoz szükséges maximális mennyiségű adateseményre és teljes tárolókapacitásra is hatással van.

1. A **tárolási korlát túllépte** a viselkedési beállítást. Válassza a **régi adattörlés** vagy a bejövő forgalom **szüneteltetése** lehetőséget.

1. A módosítások konfigurálásához válassza a **Mentés** lehetőséget.

## <a name="next-steps"></a>További lépések

- További információkért tekintse át [a Time Series Insights megőrzésének ismertetése](time-series-insights-concepts-retention.md)című témakört.