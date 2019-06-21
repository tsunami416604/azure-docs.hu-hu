---
title: Functions az Azure Monitor log-lekérdezések |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure Monitor egy másik naplólekérdezés, hívja meg a lekérdezés a functions használatával.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 4b3116230a085bfbb9a6139fbada4179d802bf5e
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296064"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Az Azure Monitor log-lekérdezéseket a functions használatával

A naplólekérdezés használata egy másik lekérdezést is mentheti függvényében. Ez lehetővé teszi, hogy az összetett lekérdezések egyszerűbbé használhatatlanná tévő őket részre, és újra felhasználhatja a közös kód, amely több lekérdezést.

## <a name="create-a-function"></a>Függvény létrehozása

Függvény létrehozása a Log Analytics használatával az Azure Portalon kattintva **mentése** , és ezután az alábbi táblázatban szereplő adatok.

| Beállítás | Leírás |
|:---|:---|
| Name (Név)           | A lekérdezés a megjelenítendő név **lekérdezéskezelő**. |
| Mentés másként        | Függvény |
| Function Alias | A függvénnyel további lekérdezések rövid nevét. Nem tartalmazhat szóközt, és egyedinek kell lennie. |
| Category       | Mentett lekérdezések és a functions rendszerezéséhez kategória **lekérdezéskezelő**. |

> [!NOTE]
> Az Azure monitorban függvény nem tartalmazhat egy másik függvényt.

> [!NOTE]
> Egy függvény mentése az Azure Monitor naplólekérdezések, de jelenleg az Application Insights-lekérdezések esetében nem lehetséges.



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

Egy másik lekérdezést, és hivatkozás létrehozása a _security_updates_last_day_ függvény az SQL-kapcsolódó szükséges frissítések kereséséhez.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>További lépések
Tekintse meg az Azure Monitor log-lekérdezések írására szolgáló egyéb leckék:

- [Karakterlánc-műveletek](string-operations.md)
- [Dátum és idő műveletek](datetime-operations.md)
- [Összesítésfüggvények](aggregations.md)
- [Speciális összesítések](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Illesztés](joins.md)
- [Diagramok](charts.md)
