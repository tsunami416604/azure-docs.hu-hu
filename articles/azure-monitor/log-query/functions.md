---
title: Függvények az Azure Monitor naplólekérdezésében | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan használhatja a függvények egy lekérdezés hívása egy másik naplólekérdezés az Azure Monitorban.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 7d94e53abbe8f4d2953729aa2363c3906ce94f74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670219"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Függvények használata az Azure Monitor naplólekérdezéseiben

Ha egy naplólekérdezést egy másik lekérdezéssel szeretne használni, mentheti függvényként. Ez lehetővé teszi az összetett lekérdezések egyszerűsítését azáltal, hogy részekre bontja őket, és lehetővé teszi a gyakori kód több lekérdezéssel történő újrafelhasználását.

## <a name="create-a-function"></a>Függvény létrehozása

Hozzon létre egy függvényt a Log Analytics az Azure Portalon kattintson a **Mentés,** majd adja meg az alábbi táblázatban szereplő információkat.

| Beállítás | Leírás |
|:---|:---|
| Név           | A lekérdezés nevének megjelenítése a **Lekérdezéskezelőben**. |
| Mentés másként        | Függvény |
| Függvény aliasa | A függvény más lekérdezésekben való használatához használt rövid név. Nem tartalmazhat szóközt, és egyedinek kell lennie. |
| Kategória       | A Mentett lekérdezések és függvények rendszerezésére alkalmas kategória a **Lekérdezéskezelőben.** |

> [!NOTE]
> Az Azure Monitor egy függvénynem tartalmazhat másik függvényt.




## <a name="use-a-function"></a>Függvény használata
Függvény használata az alias másik lekérdezésbe való befoglalásával. Ezt fel lehet használni, mint bármely más asztal.

## <a name="example"></a>Példa
A következő mintalekérdezés az utolsó napon jelentett összes hiányzó biztonsági frissítést visszaadja. Mentse a lekérdezést függvényként az _security_updates_last_day_aliasszal. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Hozzon létre egy másik _lekérdezést,_ és hivatkozzon a security_updates_last_day függvényre az SQL-hez kapcsolódó szükséges biztonsági frissítések kereséséhez.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>További lépések
Tekintse meg az Azure Monitor naplólekérdezéseinek írásával kapcsolatos egyéb leckéket:

- [Sztringműveletek](string-operations.md)
- [Dátum és idő típusú adatokkal végzett műveletek](datetime-operations.md)
- [Aggregátumfüggvények](aggregations.md)
- [Speciális aggregátumok](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Illesztések](joins.md)
- [Diagramok](charts.md)
