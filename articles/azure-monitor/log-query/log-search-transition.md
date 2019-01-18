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
ms.openlocfilehash: 89811e95ec24eb354020dd6384f6fdab6cee8c8f
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54392293"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Áttérés a Log Analytics naplóbeli keresés a Azure Monitor naplóira
Közelmúltban váltotta fel új felülettel jelentkezik a Azure Monitor-naplók elemzése a Log Analytics naplóbeli keresés. A keresési oldalon keresztül érhető el jelenleg továbbra is a **naplók (klasszikus)** menüpontja a **Log Analytics-munkaterületek** oldal az Azure portálon, de a rendszer eltávolítja 2019. február 15. Ez a cikk leírja a két megközelítés segítségével közötti különbségeket, áttérés a naplóbeli keresés. 

## <a name="extract-custom-fields"></a>Egyéni mezők kinyerése 
A Naplókeresés, csomagolja ki [egyéni mezők](../platform/custom-fields.md) egy mező menü kiterjed a művelet a lista nézetben _mezők kinyerése táblából_.

![Log Search kinyerési mezők](media/log-search-transition/extract-fields-log-search.png)

Az Azure Monitor naplóira akkor kinyerése egyéni mezőket a táblázatos nézetre. A bal oldalán található nyílra kattintva bontsa ki a rekord, majd kattintson a három pont eléréséhez a _mezők kinyerése_ művelet.

![Naplók mezők kinyerése](media/log-search-transition/extract-fields-logs.png)

## <a name="filter-results-of-a-query"></a>A lekérdezés eredményeinek szűrése
Naplókeresés, a szűrők listájának vannak feltüntetve, a keresési eredmények érkezzenek. Válasszon ki egy szűrőt, és kattintson a **alkalmaz** a lekérdezés futtatásához a kiválasztott szűrővel.

![Keresési szűrő](media/log-search-transition/filter-log-search.png)

Válassza ki az Azure Monitor naplóira, *szűrőt (előzetes verzió)* szűrők megjelenítéséhez. Kattintson a szűrő ikonjára kattintva emellett szűrők megjelenítése. Válasszon ki egy szűrőt, és kattintson a **Futtatás & alkalmazása** a lekérdezés futtatásához a kiválasztott szűrővel.

![Naplók szűrése](media/log-search-transition/filter-logs.png)

## <a name="functions-and-computer-groups"></a>Függvények és számítógépcsoportok
Keresés mentése a Naplókeresésben, válassza ki a **mentett keresések** és **Hozzáadás** biztosít egy név, kategória és a lekérdezés szövege. Hozzon létre egy [számítógépcsoport](../platform/computer-groups.md) egy függvényaliast hozzáadásával.

![Naplóbeli Keresés mentése](media/log-search-transition/save-search-log-search.png)

Az Azure Monitor naplóira az aktuális lekérdezés mentéséhez válassza **mentése**. Változás **Mentés másként** való _függvény_ , és adja meg egy **függvény aliasa** hozhat létre egy [függvény](functions.md).

![Napló lekérdezés mentése](media/log-search-transition/save-query-logs.png)

## <a name="saved-searches"></a>Mentett keresések
A Naplókeresés, a mentett keresések érhetők el a művelet sáv cikket **mentett keresések**. Az Azure Monitor naplóira, a mentett lekérdezések eléréséhez **Query Explorer**.

![Lekérdezéskezelő](media/log-search-transition/query-explorer.png)

## <a name="take-action"></a>Művelet végrehajtása
A Naplókeresés, [elindít egy runbookot](take-action.md) egy keresési eredmény szerint selectionselecting **művelet végrehajtása**.

![Művelet végrehajtása](media/log-search-transition/take-action-log-search.png)

Az Azure Monitor naplóira [hozzon létre egy riasztást a napló lekérdezésből](../platform/alerts-log.md). Műveletcsoport konfigurálhatja egy vagy több műveletet, amely a riasztásra válaszként futtatni fog.

![Műveletcsoport](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>További lépések

- További információ az új [Azure Monitor naplózza a felhasználói élményt](get-started-portal.md).