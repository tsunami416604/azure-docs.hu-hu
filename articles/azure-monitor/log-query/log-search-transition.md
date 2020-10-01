---
title: Adatok megtekintése és elemzése az Azure Log Analyticsban | Microsoft Docs
description: Log Analytics naplóbeli keresés felhasználói számára való segítségnyújtás Azure Monitor naplózási lekérdezési élményhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: 8abd9d7f33a07141418ad67cc2128af40ad0bd51
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2020
ms.locfileid: "91607376"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Áttérés Log Analytics naplóbeli keresésről Azure Monitor naplókra
A naplóbeli keresés a Log Analytics nemrég váltotta fel a Azure Monitor naplók elemzésére szolgáló új felhasználói élményt. A naplóbeli keresés oldal jelenleg továbbra is elérhető a **naplók (klasszikus)** menüpontban a Azure Portal **log Analytics munkaterületek** lapján, de 2019. február 15-én el lesz távolítva. Ez a cikk a két funkció közötti különbségeket ismerteti, amelyek segítenek a naplók közötti keresésben való áttérésben. 

## <a name="filter-results-of-a-query"></a>Lekérdezés eredményeinek szűrése
A naplóbeli keresés során a rendszer a szűrők listáját jeleníti meg a keresési eredmények kézbesítése során. Válasszon ki egy szűrőt, és kattintson az **alkalmaz** gombra a lekérdezés a kiválasztott szűrővel való futtatásához.

![Napló keresési szűrője](media/log-search-transition/filter-log-search.png)

A szűrők megjelenítéséhez Azure Monitor naplókban válassza a *szűrő (előzetes verzió)* lehetőséget. Kattintson a szűrő ikonra a hozzáadási szűrők megjelenítéséhez. Jelöljön ki egy szűrőt, és kattintson az **alkalmazás & Futtatás** gombra a lekérdezés a kiválasztott szűrővel való futtatásához.

![Naplók szűrője](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Egyéni mezők kinyerése 
A naplóbeli keresés során kinyerheti az [egyéni mezőket](../platform/custom-fields.md) a listanézet alapján, ahol a mező menüje tartalmazza a művelet _kibontása mezőket a táblából_.

![Naplóbeli keresés kinyerésének mezői](media/log-search-transition/extract-fields-log-search.png)

Azure Monitor naplókból kinyerheti az egyéni mezőket a tábla nézetből. Egy rekord kibontásához kattintson a bal oldali nyílra, majd kattintson a három pontra a _mezők kinyerése_ művelet eléréséhez.

![Naplók kinyerési mezői](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Függvények és számítógépcsoportok
Ha a keresését szeretné menteni a naplóban, válassza a **mentett keresések** és **Hozzáadás** lehetőséget a név, kategória és lekérdezés szövegének megadásához. Hozzon létre egy [számítógépcsoportot](../platform/computer-groups.md) egy függvény alias hozzáadásával.

![Naplóbeli Keresés mentése](media/log-search-transition/save-search-log-search.png)

Ha az aktuális lekérdezést Azure Monitor naplókba szeretné menteni, válassza a **Mentés**lehetőséget. Módosítsa a **Mentés másként** _funkciót_ , és adjon meg egy **függvény aliast** a [függvény](functions.md)létrehozásához. Válassza a _lekérdezés mentése számítógépcsoportként_ lehetőséget a [számítógépcsoport](../platform/computer-groups.md)függvény aliasának használatához.

![Napló lekérdezésének mentése](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Mentett lekérdezések
A naplóbeli keresés során a mentett lekérdezések a művelet sáv **mentett keresések**menüpontján keresztül érhetők el. A Azure Monitor-naplókban a lekérdezés- [kezelőből](./get-started-portal.md#save-queries)férhet hozzá a mentett lekérdezésekhez.

![Lekérdezési tallózó](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Részletezés az összegzett sorokon
A naplók keresése elemre kattintva egy összegzett lekérdezés sorára kattintva elindíthat egy másik lekérdezést, amely felsorolja az adott sorban található részletes rekordokat.

![Naplóbeli keresés részletezése](media/log-search-transition/drilldown-search.png)

Azure Monitor naplókban módosítania kell a lekérdezést a rekordok visszaküldéséhez. Bontsa ki az eredmények egyik sorát, és kattintson a **+** mellette lévő értékre a lekérdezéshez való hozzáadáshoz. Ezután adja ki az **Összefoglalás** parancsot, és futtassa újra a lekérdezést.

![Naplók lebontása Azure Monitor](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Művelet elvégzése
A naplók keresése lehetőségre kattintva [elindíthat egy runbook](../platform/action-groups.md) egy keresési eredményből a **művelet elvégzése**lehetőség kiválasztásával.

![Művelet elvégzése](media/log-search-transition/take-action-log-search.png)

Azure Monitor naplókban [hozzon létre egy riasztást a napló lekérdezésből](../platform/alerts-log.md). Egy műveleti csoport konfigurálása egy vagy több olyan művelettel, amely a riasztásra adott válaszként fog futni.

![Műveletcsoport](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>További lépések

- További információ az új [Azure monitor naplókról](get-started-portal.md).

