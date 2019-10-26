---
title: Összesítések a Azure Monitor log lekérdezésekben | Microsoft Docs
description: Leírja az aggregációs függvényeket Azure Monitor napló lekérdezésekben, amelyek hasznos módszereket kínálnak az adatok elemzéséhez.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 86b84e76b4716c1fddda23a6d52c65c0700c5663
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900412"
---
# <a name="aggregations-in-azure-monitor-log-queries"></a>Összesítések Azure Monitor naplóbeli lekérdezésekben

> [!NOTE]
> A lecke elvégzése előtt fejezze be [az Analytics-portál és a](get-started-portal.md) [lekérdezések első](get-started-queries.md) lépéseit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ez a cikk az összesítő függvényeket ismerteti Azure Monitor napló lekérdezésekben, amelyek hasznos módszereket kínálnak az adatok elemzéséhez. Ezek a függvények a `summarize` operátorral működnek, amely a bemeneti tábla összesített eredményeivel rendelkező táblázatot állít elő.

## <a name="counts"></a>Száma

### <a name="count"></a>count
Az eredményhalmazban szereplő sorok számának megszámlálása a szűrők alkalmazása után. A következő példa a _perf_ táblában lévő sorok teljes számát adja vissza az elmúlt 30 percben. Az eredmény egy *count_* nevű oszlopban lesz visszaadva, kivéve, ha egy adott nevet rendel hozzá:


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

A idődiagramját vizualizációk hasznosak lehetnek az időbeli tendenciák megjelenítéséhez:

```Kusto
Perf 
| where TimeGenerated > ago(30m) 
| summarize count() by bin(TimeGenerated, 5m)
| render timechart
```

A példa kimenete 5 perces időközökben mutatja be a Perf Records Count trendvonalat:

![Trend száma](media/aggregations/count-trend.png)


### <a name="dcount-dcountif"></a>DCount, dcountif
`dcount` és `dcountif` használatával megszámolhatja a különböző értékeket egy adott oszlopban. A következő lekérdezés kiértékeli, hogy hány különböző számítógép fogadta el a szívveréseket az elmúlt órában:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcount(Computer)
```

Ha csak a szívverést küldő Linux rendszerű számítógépeket szeretné megszámolni, használja a `dcountif`:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize dcountif(Computer, OSType=="Linux")
```

### <a name="evaluating-subgroups"></a>Alcsoportok kiértékelése
Az adatokban található alcsoportok számának vagy egyéb összesítésének végrehajtásához használja a `by` kulcsszót. Például az egyes országokban/régiókban szívverést küldő különböző Linux-számítógépek számának megszámlálásához:

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


Ha még kisebb alcsoportokat szeretne elemezni az adataiban, adjon hozzá további oszlopokat a `by` szakaszhoz. Előfordulhat például, hogy az egyes országokból/régiókból származó különálló számítógépeket szeretné megszámlálni OSType:

```Kusto
Heartbeat 
| where TimeGenerated > ago(1h) 
| summarize distinct_computers=dcountif(Computer, OSType=="Linux") by RemoteIPCountry, OSType
```

## <a name="percentiles-and-variance"></a>Percentilis és variancia
A numerikus értékek kiértékelése során az általános gyakorlat az, hogy `summarize avg(expression)`használatával átlagot kell használni. Az átlagokat csak néhány esetben jellemző szélsőséges értékek érintik. A probléma megoldásához használhat kevésbé kényes funkciókat, például `median` vagy `variance`.

### <a name="percentile"></a>Percentilis
A medián érték megkereséséhez használja a `percentile` függvényt egy értékkel a percentilis megadásához:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 50) by Computer
```

Különböző percentilis-ket is megadhat, hogy összesíthető eredményt kapjon az egyes:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize percentiles(CounterValue, 25, 50, 75, 90) by Computer
```

Ez azt jelezheti, hogy egyes számítógép-processzorok hasonló medián értékekkel rendelkeznek, de míg egyesek a medián körül vannak, a többi számítógép sokkal alacsonyabb és magasabb CPU-értékeket jelentett, ami azt jelenti, hogy a tüskéket észleltek.

### <a name="variance"></a>Variancia
Egy érték eltérésének közvetlen kiértékeléséhez használja a szórás és a variancia módszert:

```Kusto
Perf
| where TimeGenerated > ago(30m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), variance(CounterValue) by Computer
```

A CPU-használat stabilitásának elemzése jó módszer a szórás a középértékes számítással való kombinálása:

```Kusto
Perf
| where TimeGenerated > ago(130m) 
| where CounterName == "% Processor Time" and InstanceName == "_Total" 
| summarize stdev(CounterValue), percentiles(CounterValue, 50) by Computer
```

Tekintse meg a [Kusto lekérdezési nyelv](/azure/kusto/query/) használatát ismertető további leckéket a Azure monitor naplózási adataival:

- [Karakterlánc-műveletek](string-operations.md)
- [Dátum-és időműveletek](datetime-operations.md)
- [Speciális összesítések](advanced-aggregations.md)
- [JSON-és adatstruktúrák](json-data-structures.md)
- [Speciális lekérdezés írása](advanced-query-writing.md)
- [Csatlakozik](joins.md)
- [Diagramok](charts.md)
