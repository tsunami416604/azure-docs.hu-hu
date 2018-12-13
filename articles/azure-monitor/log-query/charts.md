---
title: Diagramok és ábrák létrehozása az Azure Log Analytics-lekérdezések |} A Microsoft Docs
description: Az Azure Log Analytics az adatokat különböző módon megjeleníthető különböző Vizualizációk ismerteti.
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
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 0f2a19c4e74db4fa0329587023995aa43b7b6a2a
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52882620"
---
# <a name="creating-charts-and-diagrams-from-log-analytics-queries"></a>Diagramok és ábrák hoz létre a Log Analytics-lekérdezések

> [!NOTE]
> Hajtsa végre [speciális összesítések a Log Analytics-lekérdezések](advanced-aggregations.md) ebben a leckében befejezése előtt.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ez a cikk ismerteti az Azure Log Analytics az adatokat különböző módon megjeleníthető különféle vizualizációkat.

## <a name="charting-the-results"></a>Az eredmények diagramkészítési
Indítsa el vannak egy operációs rendszer, az elmúlt órában hány számítógép áttekintésével:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Alapértelmezés szerint eredmények táblázatos megjelenítéséhez:

![Tábla](media/charts/table-display.png)

Jobb képet kaphat, jelölje be **diagram**, és válassza ki a **torta** beállítással megjelenítheti az eredményeket:

![Tortadiagram](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Timecharts
Az átlag, a processzor kihasználtsága az 50, és a 95. percentilisei megjelenítése bins 1 óra. A lekérdezés több sorozat állít elő, és ezután kiválaszthatja, melyik sorozat a diagram megjelenítése:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Válassza ki a **sor** diagram megjelenítési beállítás:

![Vonaldiagram](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Referenciavonal

Referenciavonalak segítségével egyszerűbb azonosításához, ha a metrika túllépte az egy adott küszöbértéket. Adjon hozzá egy sort egy diagramra, hogy az adatkészlet egy állandó oszloppal kiterjesztése:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Referenciavonal](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Több dimenzióban
Több kifejezésének a `by` záradékában `summarize` hozzon létre több sort az eredmények között, egy az egyes kombinaci hodnot.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Megtekintheti az eredményeket egy diagramon, az első oszlop, használja a `by` záradékban. Az alábbi példa bemutatja egy halmozott diagram használata a _eseményazonosító._ Kell lennie a `string` írja be, így ebben a példában a _eseményazonosító_ van folyamatban alakítható karakterlánccá. 

![Sávdiagram eseményazonosító](media/charts/charts-and-diagrams-multiDimension1.png)

Az az oszlop nevét a legördülő listára kattintva válthat között. 

![Sávdiagram AccountType](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>További lépések
Tekintse meg a Log Analytics lekérdezési nyelv segítségével a többi leckék:

- [Karakterlánc-műveletek](string-operations.md)
- [Dátum és idő műveletek](datetime-operations.md)
- [Összesítésfüggvények](aggregations.md)
- [Speciális összesítések](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Speciális lekérdezés írása](advanced-query-writing.md)
- [Illesztés](joins.md)