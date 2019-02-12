---
title: Diagramok és ábrák létrehozása az Azure Monitor log-lekérdezések |} A Microsoft Docs
description: Az Azure monitort a naplózási adatokat különböző módon különböző Vizualizációk ismerteti.
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
ms.date: 08/16/2018
ms.author: bwren
ms.openlocfilehash: 381e8361d31660d90eaee3f7a500139af1ef2a82
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55992984"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Diagramok és ábrák létrehozása az Azure Monitor log-lekérdezések

> [!NOTE]
> Hajtsa végre [speciális összesítések az Azure Monitor log-lekérdezések](advanced-aggregations.md) ebben a leckében befejezése előtt.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ez a cikk ismerteti az Azure monitort a naplózási adatokat különböző módon különféle vizualizációkat.

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
Tekintse meg a többi leckéket a [adatkezelő lekérdezési nyelv](/azure/kusto/query/) adatok naplózása az Azure Monitor szolgáltatással:

- [Karakterlánc-műveletek](string-operations.md)
- [Dátum és idő műveletek](datetime-operations.md)
- [Összesítésfüggvények](aggregations.md)
- [Speciális összesítések](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Speciális lekérdezés írása](advanced-query-writing.md)
- [Illesztés](joins.md)