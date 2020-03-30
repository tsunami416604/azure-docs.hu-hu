---
title: Összesítések az Azure Monitor naplólekérdezésében| Microsoft dokumentumok
description: Az Azure Monitor naplólekérdezéseinek aggregációs függvényeit ismerteti, amelyek hasznos módszereket kínálnak az adatok elemzésére.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: d164c53e7e2be55f3cede389901a256ba388808d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670304"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Összesítések az Azure Monitor naplólekérdezéseiben

> [!NOTE]
> A lecke befejezése előtt el kell [kezdenie az Első lépések szolgáltatást az Analytics-portállal,](get-started-portal.md) és [el kell kezdenie a lekérdezéseket.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ez a cikk ismerteti az aggregációs függvények az Azure Monitor naplólekérdezések, amelyek hasznos módszereket az adatok elemzésére. Ezek a függvények `summarize` mind azzal az operátorral működnek, amely a bemeneti tábla összesített eredményeit tartalmazó táblát hoz létre.

## <a name="counts"></a>Számít

### <a name="count"></a>count
Számolja meg az eredményhalmaz sorainak számát a szűrők alkalmazása után. A következő példa az utolsó 30 perc _perf-táblában_ szereplő sorok teljes számát adja vissza. Az eredmény egy *count_* nevű oszlopban kerül visszaadásra, kivéve, ha konkrét nevet rendel hozzá:


```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize count()
```

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| summarize num_of_records=count() 
```

Az idődiagram-vizualizáció hasznos lehet egy trend idővel való megtekintéséhez:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

A példa kimenete 5 perces időközönként mutatja a perf rekordszám trendvonalát:

![Számlálási trend](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>dcount, dcountif
Különböző `dcount` `dcountif` értékek et használhat egy adott oszlopban. A következő lekérdezés kiértékeli, hogy hány különböző számítógép küldött szívverést az elmúlt órában:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Ha csak a szívveréseket küldő `dcountif`Linux-számítógépeket szeretné megszámolni, használja a következőket:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Alcsoportok értékelése
Ha az adatok alcsoportjain számlálást vagy más összesítéseket szeretne végrehajtani, használja a `by` kulcsszót. Például meg kell számolni a különböző Linux-számítógépek számát, amelyek szívveréseket küldtek az egyes országokban/régiókban:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry
```

|RemoteIPCountry  | distinct_computers  |
------------------|---------------------|
|Egyesült Államok    | 19                  |
|Kanada           | 3                   |
|Írország          | 0                   |
|Egyesült Királyság   | 0                   |
|Hollandia      | 2                   |


Az adatok még kisebb alcsoportjainak elemzéséhez adjon `by` hozzá további oszlopneveket a szakaszhoz. Előfordulhat például, hogy az egyes országok/régiók különböző számítógépeit szeretné megszámolni OSType-onként:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentiles és variancia
A numerikus értékek értékelésekor általános gyakorlat, `summarize avg(expression)`hogy átlagolni kell őket a használatával. Az átlagokat olyan szélsőséges értékek befolyásolják, amelyek csak néhány esetet jellemeznek. A probléma megoldásához kevésbé érzékeny függvényeket használhat, például `median` a vagy `variance`a.

### <a name="percentile"></a>Percentilis
A medián érték megkereséséhez `percentile` használja az értékkel rendelkező függvényt a percentilis megadásához:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Különböző százalékokat is megadhat, hogy összesített eredményt kapjon az egyes adatokhoz:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Ez azt mutathatja, hogy egyes számítógép-CPU-k hasonló medián értékekkel rendelkeznek, míg néhány a medián körül állandó, más számítógépek sokkal alacsonyabb és magasabb CPU-értékekről számoltak be, ami azt jelenti, hogy csúcsokat tapasztaltak.

### <a name="variance"></a>Eltérés
Egy érték varianciájának közvetlen kiértékeléséhez használja a szórásés varianciamódszereket:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

A CPU-használat stabilitásának elemzésének jó módja a stdev és a medián számítás kombinálása:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Tekintse meg a [Kusto lekérdezési nyelv](/azure/kusto/query/) ének használatát az Azure Monitor naplóadataival:

- [Sztringműveletek](string-operations.md)
- [Dátum és idő típusú adatokkal végzett műveletek](datetime-operations.md)
- [Speciális aggregátumok](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Továbbfejlesztett lekérdezésírás](advanced-query-writing.md)
- [Illesztések](joins.md)
- [Diagramok](charts.md)
