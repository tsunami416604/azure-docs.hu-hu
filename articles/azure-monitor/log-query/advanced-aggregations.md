---
title: Speciális összesítések az Azure Monitor naplólekérdezéseiben| Microsoft dokumentumok
description: Az Azure Monitor naplólekérdezései számára elérhető speciális abbrációs aggregálási lehetőségek ismertetése.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: e5dc290a40342e0797001dde6cab90e12dd5cf39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662178"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Speciális összesítések az Azure Monitor naplólekérdezéseiben

> [!NOTE]
> A lecke befejezése előtt el kell [végeznie az Aggregations szolgáltatást az Azure Monitor lekérdezésekben.](./aggregations.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ez a cikk az Azure Monitor-lekérdezések számára elérhető speciális abbrációs aggregálási lehetőségek et ismerteti.

## <a name="generating-lists-and-sets"></a>Listák és készletek létrehozása
Az `makelist` adatokat egy adott oszlop értékeinek sorrendjében is elforgathatja. Előfordulhat például, hogy szeretné felfedezni a leggyakoribb rendelési eseményeket a gépeken. Az adatokat lényegében az egyes gépeken lévő EventID-k sorrendjén elforgathatja. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| számítógép1 | [704,701,1501,1500,1085,704,704,701] |
| számítógép2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist`létrehoz egy listát abban a sorrendben, ahogy az adatokat átadták. Az események legrégebbitől a legújabbig történő rendezéséhez használja `asc` a rendelési utasításban a helyett. `desc` 

Az is hasznos, hogy hozzon létre egy listát csak különböző értékeket. Ezt _set-nek hívják,_ és `makeset`a következőkkel lehet létrehozni:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| számítógép1 | [704,701,1501,1500,1085] |
| számítógép2 | [326,105,302,301,300,102] |
| ... | ... |

A `makelist` `makeset` programhoz hasonlóan a rendezett adatokkal is működik, és a tömböket a hozzá átadott sorok sorrendje alapján hozza létre.

## <a name="expanding-lists"></a>Listák kibontása
A vagy az `makelist` `makeset` inverz `mvexpand`művelete, amely az értékek listáját külön sorokra bontja. Tetszőleges számú dinamikus oszlopra képes kiterjeszteni, mind a JSON, mind a tömb. Például ellenőrizheti a *Szívverés* táblában azokat a megoldásokat, amelyek adatokat küldenek olyan számítógépekről, amelyek az elmúlt órában szívverést küldtek:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer | Megoldások | 
|--------------|----------------------|
| számítógép1 | "biztonság", "frissítések", "changeTracking" |
| számítógép2 | "biztonság", "frissítések" |
| számítógép3 | "antiMalware", "changeTracking" |
| ... | ... |

Az `mvexpand` egyes értékeket külön sorban jelenítheti meg a vesszővel tagolt lista helyett:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computer | Megoldások | 
|--------------|----------------------|
| számítógép1 | "biztonság" |
| számítógép1 | "frissítések" |
| számítógép1 | "changeTracking" |
| számítógép2 | "biztonság" |
| számítógép2 | "frissítések" |
| számítógép3 | "antiMalware" |
| számítógép3 | "changeTracking" |
| ... | ... |


Ezután újra `makelist` csoportosíthatja az elemeket, és ezúttal láthatja a számítógépenkénti listát:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Megoldások | list_Computer |
|--------------|----------------------|
| "biztonság" | ["számítógép1", "számítógép2"] |
| "frissítések" | ["számítógép1", "számítógép2"] |
| "changeTracking" | ["számítógép1", "számítógép3"] |
| "antiMalware" | ["számítógép3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Hiányzó raktárhelyek kezelése
Hasznos alkalmazás, `mvexpand` hogy ki kell tölteni a hiányzó raktárhelyeket. Tegyük fel például, hogy egy adott gép készenléti idejét keresi a szívverésének feltárásával. Azt is szeretné látni a forrás a szívverés, amely a _kategória_ oszlopban. Normális esetben egy egyszerű összefoglalási nyilatkozatot használnánk az alábbiak szerint:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Kategória | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Közvetlen ügynök | 2017-06-06T17:00:00Z | 15 |
| Közvetlen ügynök | 2017-06-06T18:00:00Z | 60 |
| Közvetlen ügynök | 2017-06-06T20:00:00Z | 55 |
| Közvetlen ügynök | 2017-06-06T21:00:00Z | 57 |
| Közvetlen ügynök | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

Ezekben az eredményekben azonban a "2017-06-06T19:00:00Z" társított gyűjtő hiányzik, mert nincs enek szívverési adatok az adott órára vonatkozóan. A `make-series` függvénnyel alapértelmezett értéket rendelhet az üres gyűjtőkhöz. Ez minden kategóriához létrehoz egy sort, amely két extra tömboszlopot tartalmaz, egyet az értékekhez, egyet pedig az egyező időgyűjtőkhöz:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Kategória | count_ | TimeGenerated |
|---|---|---|
| Közvetlen ügynök | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:0 00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

A *count_* tömb harmadik eleme a vártnak megfelelő0, és a _TimeGenerated_ tömbben a "2017-06-06T19:00:00.0000000Z" megfelelő időbélyeg e megfelelő időbélyeg. Ez a tömb formátum nehéz olvasni mégis. A `mvexpand` tömbök kibontására és a következő formátumú kimenet létrehozására `summarize`használható:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Kategória | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Közvetlen ügynök | 2017-06-06T17:00:00Z | 15 |
| Közvetlen ügynök | 2017-06-06T18:00:00Z | 60 |
| Közvetlen ügynök | 2017-06-06T19:00:00Z | 0 |
| Közvetlen ügynök | 2017-06-06T20:00:00Z | 55 |
| Közvetlen ügynök | 2017-06-06T21:00:00Z | 57 |
| Közvetlen ügynök | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Az eredmények szűkítése `let`elemek `makeset` `toscalar`re: , , ,`in`
Gyakori forgatókönyv, hogy bizonyos entitások nevét egy feltételkészlet alapján választja ki, majd egy másik adatkészletet szűr az adott entitáskészletre. Előfordulhat például, hogy olyan számítógépeket talál, amelyekről ismert, hogy hiányoznak a frissítések, és azonosíthatja azokat az IP-ket, amelyeket ezek a számítógépek a következőknek hívtak ki:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>További lépések

Tekintse meg a [Kusto lekérdezési nyelv](/azure/kusto/query/) ének használatát az Azure Monitor naplóadataival:

- [Sztringműveletek](string-operations.md)
- [Dátum és idő típusú adatokkal végzett műveletek](datetime-operations.md)
- [Aggregátumfüggvények](aggregations.md)
- [Speciális aggregátumok](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Illesztések](joins.md)
- [Diagramok](charts.md)
