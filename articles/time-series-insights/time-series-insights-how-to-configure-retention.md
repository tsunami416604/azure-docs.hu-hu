---
title: Megőrzési konfigurálása az Azure idő adatsorozat Insights környezetben |} Microsoft Docs
description: A cikkből megtudhatja, megőrzés konfigurálása Azure idő adatsorozat Insights környezetében.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: kfile
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/09/2018
ms.openlocfilehash: b1280549d43aac42c3ea3567a1411f42354c2b11
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293733"
---
# <a name="configuring-retention-in-time-series-insights"></a>Megőrzési idő adatsorozat insightsban konfigurálása
Ez a cikk ismerteti, hogyan konfigurálhatja **adatmegőrzési időtartam** és **tárolási korlátja túllépve viselkedés** Azure idő adatsorozat insightsban.

Minden alkalommal adatsorozat Insights (ÁME) környezetben van egy beállítás konfigurálása **adatmegőrzési időtartam**. Az érték 1 átterjed 400 nap. Az adatok törlése környezet tárolási kapacitás vagy az adatmegőrzési időtartam alapján (1-400), amelyik előbb következik be.

Minden egyes ÁME környezet rendelkezik egy további beállítás **tárolási korlátja túllépve viselkedés**. Ez a beállítás be- és végleges törlése viselkedését szabályozza, a maximális kapacitás, a környezet elérésekor. Nincsenek két viselkedésmódok közül választhat:
- **Régi adatok törlése** (alapértelmezett)  
- **Felfüggesztés érkező**

Jobb megértése érdekében ezek a beállítások részletes információkért tekintse át [ismertetése megőrzési idő adatsorozat insightsban](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Az adatmegőrzés konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Keresse meg a meglévő idő adatsorozat Insights környezetben. Válassza ki **összes erőforrás** a menüben, az Azure portál bal oldalán. Válassza ki az Azure Time Series Insights-környezetet.

3. Az a **beállítások** elemcsoportban válasszon **konfigurálása**.

4. Válassza ki a **adatmegőrzési időtartam** konfigurálása a megőrzési, a csúszka használatával, vagy írjon be egy számot a mezőbe.

5. Megjegyzés: a **kapacitás** beállítását, mert ez a konfiguráció hatással van az adatok események és a teljes tárolási kapacitás adatainak tárolásához maximális száma. 

6. Váltás a **tárolási korlátja túllépve viselkedés** beállítást. Válassza ki **régi adatok törlése** vagy **érkező szüneteltetése** viselkedését.

7. Válassza ki **mentése** konfigurálása a módosításokat.

## <a name="next-steps"></a>További lépések
További információkért tekintse át a [ismertetése megőrzési idő adatsorozat insightsban](time-series-insights-concepts-retention.md).
