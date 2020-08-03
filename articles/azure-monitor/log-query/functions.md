---
title: Függvények a Azure Monitor log lekérdezésekben | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan használhatók a függvények egy másik, Azure Monitorban található napló lekérdezésének meghívásához.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/31/2020
ms.openlocfilehash: 155f27b9f04db74955640aad8f582fdf05539368
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2020
ms.locfileid: "87511938"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Függvények használata Azure Monitor log-lekérdezésekben

Ha egy másik lekérdezéssel rendelkező naplózási lekérdezést szeretne használni, akkor azt függvényként mentheti. Ez lehetővé teszi, hogy leegyszerűsítse az összetett lekérdezéseket részekre bontással, és lehetővé teszi, hogy a közös kódokat több lekérdezéssel is felhasználhassa.

## <a name="create-a-function"></a>Függvény létrehozása

A **Save (Mentés** ) gombra kattintva hozzon létre egy Azure Portal log Analyticstel rendelkező függvényt, majd adja meg az adatokat a következő táblázatban.

| Beállítás | Leírás |
|:---|:---|
| Név           | A lekérdezés megjelenített neve a **query Explorerben**. |
| Mentés másként        | Függvény |
| Függvény aliasa | A függvény más lekérdezésekben való használatának rövid neve. A nem tartalmazhat szóközt, és egyedinek kell lennie. |
| Kategória       | A **lekérdezési Explorerben**mentett lekérdezések és függvények rendszerezésének kategóriája. |




## <a name="use-a-function"></a>Függvény használata
Használjon függvényt egy másik lekérdezésben szereplő aliassal együtt. Más táblákhoz hasonlóan is használható.

## <a name="function-parameters"></a>Függvény paraméterei 
Paramétereket adhat hozzá egy függvényhez, így bizonyos változók értékeit megadhatja a híváskor. Egy Resource Manager-sablon használatával az egyetlen lehetőség, hogy a függvényt a paraméterekkel együtt hozza létre. Példaként tekintse meg a [Resource Manager-sablonok mintáit a Azure monitor napló lekérdezéséhez](../samples/resource-manager-log-queries.md#parameterized-function) .

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

- [Sztringműveletek](string-operations.md)
- [Dátum és idő típusú adatokkal végzett műveletek](datetime-operations.md)
- [Aggregátumfüggvények](aggregations.md)
- [Speciális aggregátumok](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Illesztések](joins.md)
- [Diagramok](charts.md)
