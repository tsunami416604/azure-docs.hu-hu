---
title: Adatmegőrzés konfigurálása az Azure Time Series Insights-környezet |} A Microsoft Docs
description: Ez a cikk ismerteti a megőrzésének konfigurálása az Azure Time Series Insights-környezetet.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: a87a12e08e78cf8a7f0b4d2af3e8029b233cf02b
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629888"
---
# <a name="configuring-retention-in-time-series-insights"></a>A Time Series Insightsban megőrzés konfigurálása
Ez a cikk bemutatja, hogyan konfigurálhatja **adatmegőrzési idő** és **tárolási kapacitása túllépve viselkedés** az Azure Time Series Insightsban.

Minden egyes Time Series Insights (TSI) környezet rendelkezik egy beállítás konfigurálása **adatmegőrzési idő**. Az érték 1, 400 napos általi. Az adatok törlődnek a környezet tárolási kapacitás vagy a megőrzési időtartamát (1 – 400) alapján, amelyiket hamarabb.

A TSI-környezeteket egy további beállítása megfelelő-e **tárolási kapacitása túllépve viselkedés**. Ezzel a beállítással a bejövő és a végleges törlés viselkedését szabályozza, ha eléri a maximális kapacitás, a környezetnek. Nincsenek két viselkedésmódok közül választhat:
- **Régi adatok törlése** (alapértelmezett)  
- **Felfüggesztés bejövő forgalom**

Jobb megértése érdekében ezek a beállítások részletes információkért tekintse át a [a Time Series Insights ismertetése megőrzési](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Az adatmegőrzés konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a meglévő Time Series Insights-környezetbe. Válassza ki **összes erőforrás** az Azure portal bal oldali menüben. Válassza ki az Azure Time Series Insights-környezetet.

3. Alatt a **beállítások** szakaszban kattintson **konfigurálása**.

4. Válassza ki a **adatmegőrzési idő** konfigurálásához a megőrzési ideje a csúszka használatával, vagy adjon meg egy számot a mezőbe.

5. Megjegyzés: a **kapacitás** beállítást, mivel ez a konfiguráció hatással van a maximális mennyisége adatok események és a teljes tárolási kapacitást az adatok tárolására. 

6. Váltás a **tárolási kapacitása túllépve viselkedés** beállítás. Válassza ki **régi adatok törlése** vagy **bejövő szüneteltetése** viselkedését.

7. Válassza ki **mentése** konfigurálása a módosításokat.

## <a name="next-steps"></a>További lépések
További információkért tekintse át a [a Time Series Insights ismertetése megőrzési](time-series-insights-concepts-retention.md).
