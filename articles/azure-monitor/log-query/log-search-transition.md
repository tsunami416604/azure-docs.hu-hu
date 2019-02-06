---
title: Az Azure Log Analyticsben adatok megtekintésére és elemzésére |} A Microsoft Docs
description: Segítség a Log Analytics naplóbeli keresés az Azure Monitor log-lekérdezés élmény a felhasználók számára.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2019
ms.author: bwren
ms.openlocfilehash: dc3a754bd1a9f689f4b24b9ee2c1e4cbc0611684
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55751361"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Áttérés a Log Analytics naplóbeli keresés a Azure Monitor naplóira
Közelmúltban váltotta fel új felülettel jelentkezik a Azure Monitor-naplók elemzése a Log Analytics naplóbeli keresés. A keresési oldalon keresztül érhető el jelenleg továbbra is a **naplók (klasszikus)** menüpontja a **Log Analytics-munkaterületek** oldal az Azure portálon, de a rendszer eltávolítja 2019. február 15. Ez a cikk leírja a két megközelítés segítségével közötti különbségeket, áttérés a naplóbeli keresés. 

## <a name="filter-results-of-a-query"></a>A lekérdezés eredményeinek szűrése
Naplókeresés, a szűrők listájának vannak feltüntetve, a keresési eredmények érkezzenek. Válasszon ki egy szűrőt, és kattintson a **alkalmaz** a lekérdezés futtatásához a kiválasztott szűrővel.

![Keresési szűrő](media/log-search-transition/filter-log-search.png)

Válassza ki az Azure Monitor naplóira, *szűrőt (előzetes verzió)* szűrők megjelenítéséhez. Kattintson a szűrő ikonjára kattintva emellett szűrők megjelenítése. Válasszon ki egy szűrőt, és kattintson a **Futtatás & alkalmazása** a lekérdezés futtatásához a kiválasztott szűrővel.

![Naplók szűrése](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Egyéni mezők kinyerése 
A Naplókeresés, csomagolja ki [egyéni mezők](../platform/custom-fields.md) egy mező menü kiterjed a művelet a lista nézetben _mezők kinyerése táblából_.

![Log Search kinyerési mezők](media/log-search-transition/extract-fields-log-search.png)

Az Azure Monitor naplóira akkor kinyerése egyéni mezőket a táblázatos nézetre. A bal oldalán található nyílra kattintva bontsa ki a rekord, majd kattintson a három pont eléréséhez a _mezők kinyerése_ művelet.

![Naplók mezők kinyerése](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Függvények és számítógépcsoportok
Keresés mentése a Naplókeresésben, válassza ki a **mentett keresések** és **Hozzáadás** biztosít egy név, kategória és a lekérdezés szövege. Hozzon létre egy [számítógépcsoport](../platform/computer-groups.md) egy függvényaliast hozzáadásával.

![Naplóbeli Keresés mentése](media/log-search-transition/save-search-log-search.png)

Az Azure Monitor naplóira az aktuális lekérdezés mentéséhez válassza **mentése**. Változás **Mentés másként** való _függvény_ , és adja meg egy **függvény aliasa** hozhat létre egy [függvény](functions.md). Válassza ki _lekérdezés mentése számítógépcsoportként_ függvény alias használata egy [számítógépcsoport](../platform/computer-groups.md).

![Napló lekérdezés mentése](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Mentett lekérdezések
A Naplókeresés, a mentett lekérdezések érhetők el a művelet sáv cikket **mentett keresések**. Az Azure Monitor naplóira, a mentett lekérdezések eléréséhez [Query Explorer](../log-query/get-started-portal.md#save-queries).

![Lekérdezéskezelő](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>A Lehatolás sorok összesítése
Naplókeresés kattinthat egy sorra a összesített lekérdezés elindítása egy másik lekérdezést, amely felsorolja a részletes rekordok abban a sorban.

![Log Search drilldown](media/log-search-transition/drilldown-search.png)

Az Azure Monitor naplóira módosítania kell a lekérdezés visszaadna ezeket a rekordokat. Bontsa ki az egyik az eredményeket a sorokat, majd kattintson a **+** azt a lekérdezésbe felvenni kívánt érték mellett. Ezután tegye megjegyzésbe a **összefoglalója** parancsot, és futtassa újra a lekérdezést.

![Az Azure Monitor drilldown naplózza](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Művelet végrehajtása
A Naplókeresés, [elindít egy runbookot](take-action.md) egy keresési eredmény kiválasztásával **művelet végrehajtása**.

![Művelet végrehajtása](media/log-search-transition/take-action-log-search.png)

Az Azure Monitor naplóira [hozzon létre egy riasztást a napló lekérdezésből](../platform/alerts-log.md). Műveletcsoport konfigurálhatja egy vagy több műveletet, amely a riasztásra válaszként futtatni fog.

![Műveletcsoport](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>További lépések

- További információ az új [Azure Monitor naplózza a felhasználói élményt](get-started-portal.md).