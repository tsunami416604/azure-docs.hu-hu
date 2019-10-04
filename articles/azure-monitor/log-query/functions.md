---
title: Függvények a Azure Monitor log lekérdezésekben | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a függvények egy másik, Azure Monitorban található napló lekérdezésének meghívásához.
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
ms.openlocfilehash: 75beb7b66863efd2fb3679f034a3663dca4a6d2f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076697"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Függvények használata Azure Monitor log-lekérdezésekben

Ha egy másik lekérdezéssel rendelkező naplózási lekérdezést szeretne használni, akkor azt függvényként mentheti. Ez lehetővé teszi, hogy leegyszerűsítse az összetett lekérdezéseket részekre bontással, és lehetővé teszi, hogy a közös kódokat több lekérdezéssel is felhasználhassa.

## <a name="create-a-function"></a>Függvény létrehozása

A **Save (Mentés** ) gombra kattintva hozzon létre egy Azure Portal log Analyticstel rendelkező függvényt, majd adja meg az adatokat a következő táblázatban.

| Beállítás | Leírás |
|:---|:---|
| Name (Név)           | A lekérdezés megjelenített neve a **query Explorerben**. |
| Mentés másként        | Függvény |
| Függvény aliasa | A függvény más lekérdezésekben való használatának rövid neve. A nem tartalmazhat szóközt, és egyedinek kell lennie. |
| Category       | A **lekérdezési Explorerben**mentett lekérdezések és függvények rendszerezésének kategóriája. |

> [!NOTE]
> Azure Monitor függvény nem tartalmazhat másik függvényt.




## <a name="use-a-function"></a>Függvény használata
Használjon függvényt egy másik lekérdezésben szereplő aliassal együtt. Más táblákhoz hasonlóan is használható.

## <a name="example"></a>Példa
A következő minta lekérdezés az elmúlt nap során jelentett összes hiányzó biztonsági frissítést visszaadja. Mentse ezt a lekérdezést függvényként az alias _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Hozzon létre egy másik lekérdezést, és hivatkozzon a _security_updates_last_day_ függvényre az SQL-hez kapcsolódó szükséges biztonsági frissítések kereséséhez.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>További lépések
A Azure Monitor log-lekérdezések írásához további leckéket talál:

- [Karakterlánc-műveletek](string-operations.md)
- [Dátum-és időműveletek](datetime-operations.md)
- [Összesítési függvények](aggregations.md)
- [Speciális összesítések](advanced-aggregations.md)
- [JSON-és adatstruktúrák](json-data-structures.md)
- [Csatlakozik](joins.md)
- [Diagramok](charts.md)
