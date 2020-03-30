---
title: Adatok megtekintése és elemzése az Azure Log Analytics szolgáltatásban | Microsoft dokumentumok
description: Segítség a Log Analytics-napló keresés felhasználóinak az Azure Monitor naplólekérdezési élményéhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: 6cc7fc94162ed101d06fbbbc4722867be46987b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670134"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Áttérés a Log Analytics-naplókeresésről az Azure Monitor-naplókra
A Log Analytics naplókeresését a közelmúltban egy új felület váltotta fel az Azure Monitor-naplók elemzéséhez. A naplókeresési lap jelenleg továbbra is elérhető az Azure Portal **Log Analytics munkaterületeinek Naplók** **(klasszikus)** menüelemén keresztül, de 2019. Ez a cikk ismerteti a két élmény közötti különbségeket, amelyek segítenek a naplókeresésről való áttérésben. 

## <a name="filter-results-of-a-query"></a>Lekérdezés eredményeinek szűrése
A Naplókeresés ben a szűrők listája jelenik meg a keresési eredmények kézbesítése során. Jelöljön ki egy szűrőt, és kattintson az **Alkalmaz** gombra a lekérdezés kijelölt szűrővel való futtatásához.

![Naplókeresési szűrő](media/log-search-transition/filter-log-search.png)

Az Azure Monitor naplóiban válassza *a Szűrő (előzetes verzió)* lehetőséget a szűrők megjelenítéséhez. Kattintson a szűrő ikonra a hozzáadásszűrők megjelenítéséhez. Jelöljön ki egy szűrőt, és kattintson **& Futtatás gombra** a lekérdezés kijelölt szűrővel való futtatásához.

![Naplók szűrő](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Egyéni mezők kinyerése 
A Naplókeresés párbeszédpanelen [egyéni mezőket](../platform/custom-fields.md) nyerhet ki a Lista nézetből, ahol a mező menüje tartalmazza a _művelet kinyerése mezőket_a táblából .

![Naplókeresési kibontó mezők](media/log-search-transition/extract-fields-log-search.png)

Az Azure Monitor naplóiban kibonthatja az egyéni mezőket a táblanézetből. Bontsa ki a rekordot a bal oldali nyílra kattintva, majd kattintson a három pontra a _Mezők kibontása_ művelet eléréséhez.

![Naplók kibontási mezői](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Funkciók és számítógépcsoportok
Ha menteni szeretne egy keresést a Naplókeresés ben, válassza a **Mentett keresések** és **a Hozzáadás** lehetőséget a név, kategória és lekérdezés szövegének megadásához. [Számítógépcsoport](../platform/computer-groups.md) létrehozása függvényalias hozzáadásával.

![Naplókeresés mentése](media/log-search-transition/save-search-log-search.png)

Ha menteni szeretné az aktuális lekérdezést az Azure Monitor naplóiba, válassza a **Mentés lehetőséget.** Módosítsa a **Mentés függvényként** _lehetőséget,_ és adjon **meg függvényaliast** [a függvény](functions.md)létrehozásához. Jelölje _be a Lekérdezés mentése számítógépcsoportként_ jelölőnégyzetet a [számítógépcsoport](../platform/computer-groups.md)függvényaliasának használatához.

![Naplólekérdezés mentése](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Mentett lekérdezések
A Naplókeresés ben a mentett lekérdezések a **műveletsáv Mentett keresések**elemén keresztül érhetők el. Az Azure Monitor naplóiban a [Query Explorer](../log-query/get-started-portal.md#save-queries)mentett lekérdezéseit érheti el.

![Lekérdezéskezelő](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Részletezés az összesített sorokon
A Naplókeresés ben az összesített lekérdezés egy sorára kattintva elindíthat egy másik lekérdezést, amely részletes rekordokat sorol fel a sorban.

![Naplókeresés részletezése](media/log-search-transition/drilldown-search.png)

Az Azure Monitor naplóiban módosítania kell a lekérdezést, hogy adja vissza ezeket a rekordokat. Bontsa ki az eredmények egyik **+** sorát, és kattintson a következő értékre a lekérdezéshez való hozzáadásához. Ezután fűzze hozzá az **összegző parancsot,** és futtassa újra a lekérdezést.

![Az Azure Monitor naplózza a részletezést](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Művelet megtemitetése
A Naplókeresés ben a Művelet **művelet**lehetőségkiválasztásával indíthatja el a [runbookot](take-action.md) egy keresési eredményből.

![Művelet megtemitetése](media/log-search-transition/take-action-log-search.png)

Az Azure Monitor naplóiban [hozzon létre egy riasztást a naplólekérdezésből.](../platform/alerts-log.md) Konfiguráljon egy műveletcsoportot egy vagy több művelettel, amely a riasztásra válaszul fog futni.

![Műveletcsoport](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>További lépések

- További információ az [Azure Monitor új naplóinak élményéről.](get-started-portal.md)
