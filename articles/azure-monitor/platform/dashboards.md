---
title: Egyéni irányítópult létrehozása az Azure Log Analyticsben |} A Microsoft Docs
description: Az útmutató segítségével megismerheti, hogyan Log Analytics-irányítópultok képesek megjeleníteni az összes mentett naplókeresését, így egy egyetlen fókuszban a környezet megtekintése.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: magoedte
ms.openlocfilehash: 46d113d06ee9d59cd3c3143b87c9166c9bd09fdc
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54101803"
---
# <a name="create-a-custom-dashboard-for-use-in-log-analytics"></a>A Log Analyticsben használható egyéni irányítópult létrehozása

Az útmutató segítségével megismerheti, hogyan Log Analytics-irányítópultok képesek megjeleníteni az összes mentett naplókeresését, így egy egyetlen fókuszban a környezet megtekintése.

>[!NOTE]
> Már nem szerkesztheti a meglévő **saját irányítópult**. Ez a funkció elavult folyamatban van.

![Példa irányítópult](./media/dashboards/oms-dashboards-example-dash.png)

Az OMS-portálon létrehozott összes egyéni irányítópultokat is az OMS Mobilalkalmazásról érhető el. További információ az alkalmazások a következő lapokon olvashat.

* [A Microsoft Store az OMS mobilalkalmazást](https://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
* [Apple iTunes OMS mobilalkalmazást](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![mobil irányítópult](./media/dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>Hogyan hozhatok létre saját irányítópult?
A kezdéshez nyissa meg az OMS-áttekintő lap. Látni fogja a **saját irányítópult** csempére a bal oldalon. Kattintson rá az irányítópulton feltárásához.

![Áttekintés](./media/dashboards/oms-dashboards-overview.png)

## <a name="adding-a-tile"></a>Csempe hozzáadása
Az irányítópultok csempék működteti a mentett naplóbeli keresésekre. OMS tartalmaz számos előre elkészített mentett naplókeresését, így azonnal megkezdheti. Használja az alábbi lépéseket, amelyek ismertetik a kezdéshez.

Saját irányítópult-nézet, egyszerűen kattintson **Testreszabás** adja meg a testreszabási módban.

![Képi](./media/dashboards/oms-dashboards-pictorial01.png)

 A az oldal jobb oldalon megnyíló panelen megjeleníti az összes mentett naplókeresését a munkaterülethez. Mentett Naplókeresés ábrázolása csempeként, egy mentett keresés fölé, majd kattintson a **plusz** szimbólum.

![1 Csempék hozzáadása](./media/dashboards/oms-dashboards-pictorial02.png)

Amikor rákattint a **plusz** szimbólum egy új csempe jelenik meg a saját irányítópult-nézet.

![2 Csempék hozzáadása](./media/dashboards/oms-dashboards-pictorial03.png)

## <a name="edit-a-tile"></a>Csempe szerkesztése
Saját irányítópult-nézet, egyszerűen kattintson **Testreszabás** adja meg a testreszabási módban. Kattintson a szerkeszteni kívánt csempére. A jobb oldali panel módosítások szerkesztéséhez és a egy kijelölt beállítások:

![Csempe szerkesztése](./media/dashboards/oms-dashboards-pictorial04.png)

![Csempe szerkesztése](./media/dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>Mozaikmegjelenítések
Mozaikmegjelenítések közül választhat, három fő típusba sorolhatók:

| diagram típusa | Művelet |
| --- | --- |
| ![Sávdiagram](./media/dashboards/oms-dashboards-bar-chart.png) |Jelenít meg a mentett naplóbeli keresési eredmények ütemterv sávdiagramot oszlopdiagramra cseréli, vagy egy listát az eredményekkel, attól függően, mező szerint, ha a naplóbeli keresés összesíti az eredményeket mező szerint, vagy nem. |
| ![metrika](./media/dashboards/oms-dashboards-metric.png) |Az összes napló keresési eredmény találatok csempében számmal jeleníti meg. Metrika csempék teszik lehetővé, amelyek ki vannak emelve a csempe a küszöbérték elérésekor küszöbértéket állíthat be. |
| ![Vonal](./media/dashboards/oms-dashboards-line.png) |A mentett naplóbeli keresési eredmény a találatok értékekkel ütemterv vonaldiagram jeleníti meg. |

### <a name="threshold"></a>Küszöbérték
Egy csempére a metrika képi megjelenítés használatával hozhat létre egy küszöbértéket. Válassza ki a küszöbérték létrehozásához a csempére. Döntse el, hogy az érték felett vagy a kiválasztott küszöbérték alatt van, majd állítsa be az alábbi küszöbérték, jelölje ki a csempét.

## <a name="organizing-the-dashboard"></a>Az irányítópult rendszerezéséhez
Az irányítópult rendszerezéséhez keresse meg a saját irányítópult-nézet, és kattintson **Testreszabás** adja meg a testreszabási módban. Kattintson és húzza át, és helyezze át a kívánt csempét, ahová a csempét.

![Az irányítópult rendszerezése](./media/dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>Egy csempe eltávolítása
El szeretné távolítani a csempét, keresse meg a saját irányítópult-nézet, és kattintson **Testreszabás** adja meg a testreszabási módban. Kattintson a csempére, távolítsa el, majd a jobb oldali panelen válassza ki a kívánt **csempe eltávolítása**.

![Egy csempe eltávolítása](./media/dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>További lépések
* Hozzon létre [riasztások](../../azure-monitor/platform/alerts-overview.md) a Log Analytics legyen orvosolni a problémákat és az értesítések generálását.
