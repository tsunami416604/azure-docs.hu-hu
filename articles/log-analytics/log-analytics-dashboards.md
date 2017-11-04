---
title: "Egyéni irányítópult létrehozása az Azure Naplóelemzés |} Microsoft Docs"
description: "Az útmutató segítségével megismerheti, hogyan Naplóelemzési irányítópultok jelenítheti meg az összes a mentett napló keresések felkínálva egy egyetlen helyre gyűjti a környezet megtekintéséhez."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 22cc516c15353e39c73e762d2b8fa0d787a05ef4
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2017
---
# <a name="create-a-custom-dashboard-for-use-in-log-analytics"></a>A Log Analyticshez való használatra egyéni irányítópult létrehozása

Az útmutató segítségével megismerheti, hogyan Naplóelemzési irányítópultok jelenítheti meg az összes a mentett napló keresések felkínálva egy egyetlen helyre gyűjti a környezet megtekintéséhez.

>[!NOTE]
> Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), akkor már nem szerkesztheti a meglévő **saját irányítópult**. Ha a munkaterületet nem rendelkezett a **saját irányítópult** csempék hozzáadni, akkor nem jelenik meg **saját irányítópult** a frissített munkaterületén. 

![Példa irányítópult](./media/log-analytics-dashboards/oms-dashboards-example-dash.png)

Az OMS-portálon létrehozott egyéni irányítópultok is elérhetők az OMS mobilalkalmazás. Tekintse meg a következő oldalakon további információ az alkalmazások.

* [A Microsoft Store-ból OMS mobilalkalmazás](http://www.windowsphone.com/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865)
* [Az Apple iTunes OMS mobilalkalmazás](https://itunes.apple.com/app/microsoft-operations-management/id1042424859?mt=8)

![mobil irányítópult](./media/log-analytics-dashboards/oms-search-mobile.png)

## <a name="how-do-i-create-my-dashboard"></a>Hogyan hozható létre saját irányítópult?
Első lépésként nyissa meg az OMS – áttekintés oldalra. Látni fogja a **saját irányítópult** csempét a bal oldalon. Kattintson rá az irányítópult feltárásához.

![Áttekintés](./media/log-analytics-dashboards/oms-dashboards-overview.png)

## <a name="adding-a-tile"></a>Egy csempe hozzáadása
Az irányítópultokat csempék vannak kapcsolva, a mentett napló keresések által. Számos előre mentett napló keresések végzett, azonnali elkezdéséhez OMS tartalmaz. A következő lépésekkel, amelyek helyzeteit vázolják fel, hogy hogyan kezdheti meg.

Saját irányítópult-nézethez egyszerűen kattintson **Testreszabás** adja meg a testreszabási módban.

![Képi](./media/log-analytics-dashboards/oms-dashboards-pictorial01.png)

 A panel, amely megnyitja a lap jobb oldalán a munkaterület mentett napló keresések mutatja. Megjelenítheti a mentett napló keresési csempe, a mentett kereséseket mutat, és kattintson a **plus** szimbólum.

![Vegyen fel Csempéket 1](./media/log-analytics-dashboards/oms-dashboards-pictorial02.png)

Amikor rákattint az **plus** szimbólumot, egy új csempe jelenik meg a saját irányítópult nézetben.

![Vegyen fel Csempéket 2](./media/log-analytics-dashboards/oms-dashboards-pictorial03.png)

## <a name="edit-a-tile"></a>Egy csempe szerkesztése
Saját irányítópult-nézethez egyszerűen kattintson **Testreszabás** adja meg a testreszabási módban. Kattintson a szerkeszteni kívánt csempére. A jobb oldali panelen módosítások szerkesztéséhez és beállítások a kijelölt biztosítja:

![Mozaik szerkesztése](./media/log-analytics-dashboards/oms-dashboards-pictorial04.png)

![Mozaik szerkesztése](./media/log-analytics-dashboards/oms-dashboards-pictorial05.png)

### <a name="tile-visualizations"></a>Mozaik képi megjelenítések
Nincsenek háromféle csempe képi megjelenítéseket készíthet, lehetőségek közül választhat:

| diagram típusát | működés |
| --- | --- |
| ![Sáv diagrammá](./media/log-analytics-dashboards/oms-dashboards-bar-chart.png) |Megjeleníti a sávdiagram, vagy attól függően egy mezőt az eredmények listában ütemterv a mentett napló keresés eredményeit, ha a naplófájl-keresési eredményeket a mező szerint összesíti, vagy nem. |
| ![A metrika](./media/log-analytics-dashboards/oms-dashboards-metric.png) |Megjeleníti a teljes naplót keresési eredmény találatok egy számot a csempén. Metrika csempék lehetővé teszik a küszöbérték, amely a csempe jelölje ki, a küszöbérték elérésekor. |
| ![sor](./media/log-analytics-dashboards/oms-dashboards-line.png) |A mentett napló keresési eredmény találatok értékekkel ütemterv halmazaként jelenik meg. |

### <a name="threshold"></a>Küszöbérték
Létrehozhat egy metrika a képi megjelenítés használata csempe a küszöbértéket. Válassza ki a küszöbérték a csempére létrehozásához. Válasszon, hogy, hogy a mozaik elrendezés esetén az érték felett vagy alatt a hálókapacitás megadott küszöbértékét, akkor az alábbi értékre állítani.

## <a name="organizing-the-dashboard"></a>Az irányítópult rendezése
Az irányítópult rendezéséhez nyissa meg a saját irányítópult-nézetet, kattintson a **Testreszabás** adja meg a testreszabási módban. Kattintással és húzással szeretné áthelyezni, és helyezze át a csempe kell lennie, ahová a csempén.

![Az irányítópult rendezése](./media/log-analytics-dashboards/oms-dashboards-organize.png)

## <a name="remove-a-tile"></a>Egy csempe eltávolítása
Egy csempe eltávolításához nyissa meg a saját irányítópult-nézetet, és kattintson a **Testreszabás** adja meg a testreszabási módban. Válassza ki a csempe, távolítsa el, majd a jobb oldali panelen válassza ki a kívánt **csempe eltávolítása**.

![Egy csempe eltávolítása](./media/log-analytics-dashboards/oms-dashboards-remove-tile.png)

## <a name="next-steps"></a>Következő lépések
* Hozzon létre [riasztások](log-analytics-alerts.md) a Naplóelemzési értesítések létrehozásához, és javítani a jelentkező problémákat.
