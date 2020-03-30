---
title: Diagramok és diagramok létrehozása az Azure Monitor naplólekérdezéseiből | Microsoft dokumentumok
description: Az Azure Monitor különböző vizualizációit ismerteti a naplóadatok különböző módokon való megjelenítéséhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8a515f01bfa9f8ec579c51b806c997d79b629250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670321"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Diagramok és diagramok létrehozása az Azure Monitor naplólekérdezéseiből

> [!NOTE]
> A lecke befejezése előtt el kell [végeznie a speciális összesítéseket az Azure Monitor naplólekérdezéseiben.](advanced-aggregations.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ez a cikk ismerteti a különböző vizualizációk az Azure Monitor különböző módon jeleníti meg a naplóadatokat.

## <a name="charting-the-results"></a>Az eredmények ábrázolása
Először tekintse át, hogy hány számítógép van operációs rendszerenként az elmúlt órában:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Alapértelmezés szerint az eredmények táblázatként jelennek meg:

![Tábla](media/charts/table-display.png)

Jobb nézet megjelenítéséhez válassza a **Diagram**lehetőséget, és válassza a **Kör** lehetőséget az eredmények megjelenítéséhez:

![Tortadiagram](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Idődiagramok
A processzoridő átlagos, 50. A lekérdezés több adatsort hoz létre, és kiválaszthatja, hogy melyik adatsort szeretné megjeleníteni az idődiagramban:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Válassza a **Vonaldiagram** megjelenítési beállítását:

![Vonaldiagram](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Referenciasor

A referenciasor segítségével könnyen azonosíthatja, hogy a metrika túllépett-e egy adott küszöbértéket. Ha vonalat szeretne hozzáadni egy diagramhoz, bővítse ki az adatkészletet állandó oszloppal:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Referenciasor](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Több dimenzió
Több kifejezés a `by` záradékban, `summarize` hogy több sort hozzon létre az eredményekben, egyet-egyet az értékek minden kombinációjához.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Ha az eredményeket diagramként tekinti meg, az `by` a záradék első oszlopát használja. A következő példa egy halmozott oszlopdiagramot mutat be az _EventID használatával._ A dimenzióknak `string` típusúaknak kell lenniük, ezért ebben a példában az _EventID_ karakterláncra kerül. 

![Sávdiagram eseményazonosítója](media/charts/charts-and-diagrams-multiDimension1.png)

A váltáshoz jelölje ki az oszlop nevét tartalmazó legördülő menüt. 

![Sávdiagram AccountType](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>További lépések
Tekintse meg a [Kusto lekérdezési nyelv](/azure/kusto/query/) ének használatát az Azure Monitor naplóadataival:

- [Sztringműveletek](string-operations.md)
- [Dátum és idő típusú adatokkal végzett műveletek](datetime-operations.md)
- [Aggregátumfüggvények](aggregations.md)
- [Speciális aggregátumok](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Továbbfejlesztett lekérdezésírás](advanced-query-writing.md)
- [Illesztések](joins.md)
