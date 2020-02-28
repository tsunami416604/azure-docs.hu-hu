---
title: Diagramok és diagramok létrehozása Azure Monitor log-lekérdezésekből | Microsoft Docs
description: Az Azure Monitor különböző vizualizációit ismerteti a naplózási információk különböző módokon történő megjelenítéséhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8a515f01bfa9f8ec579c51b806c997d79b629250
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670321"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Diagramok és diagramok létrehozása Azure Monitor log-lekérdezésekből

> [!NOTE]
> A lecke elvégzése előtt hajtsa végre [a speciális összesítéseket Azure monitor-naplózási lekérdezésekben](advanced-aggregations.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ez a cikk a különböző vizualizációkat ismerteti Azure Monitor a naplózási adatai különböző módokon történő megjelenítéséhez.

## <a name="charting-the-results"></a>Az eredmények ábrázolása
Először tekintse át, hogy hány számítógépen van operációs rendszer, az elmúlt órában:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Alapértelmezés szerint az eredmények táblázatként jelennek meg:

![Tábla](media/charts/table-display.png)

Jobb nézet létrehozásához válassza a **diagram**lehetőséget, majd az eredmények megjelenítéséhez kattintson a **torta** lehetőségre:

![Tortadiagram](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Timecharts
A processzoridő átlagos, 50 és 95. százalékos értékének megjelenítése 1 órás raktárhelyeken. A lekérdezés több adatsorozatot hoz létre, és kiválaszthatja az idődiagramban megjelenítendő sorozatot:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Válassza ki **a diagram** megjelenítési beállítását:

![Vonaldiagram](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Hivatkozási vonal

A viszonyítási sorok segítségével egyszerűen azonosítható, hogy a metrika túllépte-e a megadott küszöbértéket. Egy vonal diagramhoz való hozzáadásához bővítse az adatkészletet egy állandó oszloppal:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Hivatkozási vonal](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Több dimenzió
A `summarize` `by` záradékában több kifejezés is több sort hoz létre az eredményekben, egyet pedig az értékek minden kombinációjára.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Amikor diagramként jeleníti meg az eredményeket, a `by` záradék első oszlopát fogja használni. Az alábbi példában egy halmozott oszlopdiagram látható a _Napszállta használatával._ A dimenziónak `string` típusúnak kell lennie, ezért ebben a példában a _Napszállta_ a karakterláncba kerül. 

![Oszlopdiagram Napszállta](media/charts/charts-and-diagrams-multiDimension1.png)

A közötti váltáshoz válassza ki a legördülő menüt az oszlop nevével. 

![Oszlopdiagram AccountType](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Következő lépések
Tekintse meg a [Kusto lekérdezési nyelv](/azure/kusto/query/) használatát ismertető további leckéket a Azure monitor naplózási adataival:

- [Karakterlánc-műveletek](string-operations.md)
- [Dátum-és időműveletek](datetime-operations.md)
- [Összesítési függvények](aggregations.md)
- [Speciális összesítések](advanced-aggregations.md)
- [JSON-és adatstruktúrák](json-data-structures.md)
- [Speciális lekérdezés írása](advanced-query-writing.md)
- [Csatlakozik](joins.md)
