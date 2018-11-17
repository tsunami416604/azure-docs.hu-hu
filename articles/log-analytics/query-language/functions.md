---
title: Az Azure Log Analytics-függvények |} A Microsoft Docs
description: Ez a cikk ismerteti a Log Analytics egy másik lekérdezést, hívja meg a lekérdezés a functions használatával.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 23079c265cc0c7bd8aa11270ecd38003d87eb30f
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2018
ms.locfileid: "51855160"
---
# <a name="using-functions-in-azure-monitor-log-analytics"></a>Functions az Azure Monitor Log Analytics használatával

> [!NOTE]
> Hajtsa végre [az Analytics-portál – első lépések](get-started-analytics-portal.md) és [Ismerkedés a lekérdezések](get-started-queries.md) ebben a leckében befejezése előtt.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]


Log Analytics-lekérdezés használata egy másik lekérdezést is mentheti függvényében. Ez lehetővé teszi, hogy az összetett lekérdezések egyszerűbbé használhatatlanná tévő őket részre, és újra felhasználhatja a közös kód, amely több lekérdezést.

## <a name="create-a-function"></a>Függvény létrehozása

Függvény létrehozása az Azure Portalon kattintva **mentése** , és ezután az alábbi táblázatban szereplő adatok.

| Beállítás | Leírás |
|:---|:---|
| Name (Név)           | A lekérdezés a megjelenítendő név **lekérdezéskezelő**. |
| Mentés másként        | Függvény |
| Függvény aliasa | A függvénnyel további lekérdezések rövid nevét. Nem tartalmazhat szóközt, és egyedinek kell lennie. |
| Kategória       | Mentett lekérdezések és a functions rendszerezéséhez kategória **lekérdezéskezelő**. |

> [!NOTE]
> A Log Analytics függvény nem tartalmazhat egy másik függvényt.

> [!NOTE]
> Függvény mentése a Log Analytics-lekérdezéseket, de jelenleg az Application Insights-lekérdezések esetében nem lehetséges.



## <a name="use-a-function"></a>Egy függvény használata
A funkció használatához többek között az alias egy másik lekérdezésbe. Mint bármely más táblázat használható.

## <a name="example"></a>Példa
Az alábbi mintalekérdezés az elmúlt egy napon belül jelentett összes hiányzó biztonsági frissítések adja vissza. Lekérdezés mentése az aliasszal függvényében _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Hozzon létre egy másik SQL-kapcsolódó szükséges frissítések kereséséhez.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>További lépések
Tekintse meg a Log Analytics lekérdezési nyelv segítségével a többi leckék:

- [Karakterlánc-műveletek](string-operations.md)
- [Dátum és idő műveletek](datetime-operations.md)
- [Összesítésfüggvények](aggregations.md)
- [Speciális összesítések](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Illesztés](joins.md)
- [Diagramok](charts.md)