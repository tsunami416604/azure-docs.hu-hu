---
title: Speciális összesítések Azure Monitor log-lekérdezésekben | Microsoft Docs
description: Ismerteti a több speciális aggregációs lehetőséget, amelyek elérhetők Azure Monitor a naplók lekérdezéséhez.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: f34e71c4e15e3bb09676e366313e90a7261439e5
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900444"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Speciális összesítések Azure Monitor log lekérdezésekben

> [!NOTE]
> A lecke elvégzése előtt [Azure monitor lekérdezések összesítéseit](./aggregations.md) kell végrehajtania.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ez a cikk ismerteti a Azure Monitor lekérdezések számára elérhető fejlettebb összesítési lehetőségeket.

## <a name="generating-lists-and-sets"></a>Listák és készletek létrehozása
A `makelist` segítségével az adatokat egy adott oszlop értékeinek sorrendje alapján is elvégezheti. Előfordulhat például, hogy meg szeretné vizsgálni a leggyakrabban használt rendezési eseményeket a gépeken. Az adatmennyiséget lényegében az egyes gépek EventIDs sorrendjével lehet kimutatni. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| Számítógép1 | [704, 701, 1501, 1500, 1085, 704, 704, 701] |
| számítógép2 | [326 105 302 301 300 102] |
| ... | ... |

`makelist` létrehoz egy listát abban a sorrendben, ahogy a rendszer átadta az adatbevitelt. Ha az eseményeket a legrégebbitől a legújabbig szeretné rendezni, a `desc`helyett a Order utasításban használja a `asc`. 

Az is hasznos, ha csak a különböző értékek listáját szeretné létrehozni. Ezt egy _készletnek_ nevezzük, és a `makeset`használatával hozható létre:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| Számítógép1 | [704, 701, 1501, 1500, 1085] |
| számítógép2 | [326 105 302 301 300 102] |
| ... | ... |

A `makelist`hoz hasonlóan a `makeset` is működik a rendezett adatmennyiséggel, és a tömböket az átadott sorok sorrendje alapján hozza majd ki.

## <a name="expanding-lists"></a>Kiterjesztések listája
`makelist` vagy `makeset` inverz művelete `mvexpand`, amely kibővíti az értékek listáját a sorok elkülönítéséhez. A JSON és a tömb tetszőleges számú dinamikus oszlopán kiterjeszthető. Például megtekintheti a *szívverési* táblázatot olyan megoldások esetében, amelyek az elmúlt órában szívverést küldő számítógépekről küldenek adatokat:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer | Megoldások | 
|--------------|----------------------|
| Számítógép1 | "biztonság", "frissítések", "változáskövetési" |
| számítógép2 | "biztonság", "frissítések" |
| számítógép3 | "antimalware", "változáskövetési" |
| ... | ... |

A `mvexpand` használatával a vesszővel tagolt lista helyett külön sorban jelenítheti meg az egyes értékeket:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computer | Megoldások | 
|--------------|----------------------|
| Számítógép1 | biztonsági |
| Számítógép1 | frissítések |
| Számítógép1 | Változáskövetési |
| számítógép2 | biztonsági |
| számítógép2 | frissítések |
| számítógép3 | Kártevőirtó |
| számítógép3 | Változáskövetési |
| ... | ... |


Ezután a `makelist` újra felhasználhatja az elemek csoportosításához, és ez alkalommal a számítógépek listáját tekintheti meg megoldásként:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Megoldások | list_Computer |
|--------------|----------------------|
| biztonsági | ["Számítógép1", "számítógép2"] |
| frissítések | ["Számítógép1", "számítógép2"] |
| Változáskövetési | ["Számítógép1", "számítógép3"] |
| Kártevőirtó | ["számítógép3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Hiányzó raktárhelyek feldolgozása
`mvexpand` hasznos alkalmazása a hiányzó raktárhelyekhez tartozó alapértelmezett értékek kitöltésének szükségessége. Tegyük fel például, hogy egy adott gép üzemidőét keresi a szívverésének vizsgálatával. Érdemes megtekinteni a szívverés forrását is, amely a category ( _Kategória_ ) oszlopban található. Általában egy egyszerű összefoglaló utasítást fogunk használni a következőképpen:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Kategória | TimeGenerated | száma |
|--------------|----------------------|--------|
| Közvetlen ügynök | 2017-06-06T17:00:00Z | 15 |
| Közvetlen ügynök | 2017-06-06T18:00:00Z | 60 |
| Közvetlen ügynök | 2017-06-06T20:00:00Z | 55 |
| Közvetlen ügynök | 2017-06-06T21:00:00Z | 57 |
| Közvetlen ügynök | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

Ezekben az eredményekben azonban a "2017-06-06T19:00:00Z" elemhez társított gyűjtő hiányzik, mert az adott órában nem található szívverési érték. Az `make-series` függvény használatával rendeljen hozzá egy alapértelmezett értéket az üres gyűjtőhöz. Ez egy sort hoz majd az egyes kategóriákhoz két extra tömb oszloppal, egyet az értékekhez, egyet pedig a megfelelő időgyűjtőhöz:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Kategória | száma | TimeGenerated |
|---|---|---|
| Közvetlen ügynök | [15, 60, 0, 55, 60, 57, 60,...] | ["2017-06-06T17:00:00.0000000 Z", "2017-06-06T18:00:00.0000000 Z", "2017-06-06T19:00:00.0000000 Z", "2017-06-06T20:00:00.0000000 Z", "2017-06-06T21:00:00.0000000 Z",...] |
| ... | ... | ... |

A *count_* tömb harmadik eleme a várt érték, és a _TimeGenerated_ tömbben a "2017-06-06T19:00:00.0000000 z" egyező időbélyeg szerepel. Ez a tömb formátuma nehezen olvasható. A `mvexpand` használatával bontsa ki a tömböket, és hozza létre a `summarize`által generált formátum kimenetét:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Kategória | TimeGenerated | száma |
|--------------|----------------------|--------|
| Közvetlen ügynök | 2017-06-06T17:00:00Z | 15 |
| Közvetlen ügynök | 2017-06-06T18:00:00Z | 60 |
| Közvetlen ügynök | 2017-06-06T19:00:00Z | 0 |
| Közvetlen ügynök | 2017-06-06T20:00:00Z | 55 |
| Közvetlen ügynök | 2017-06-06T21:00:00Z | 57 |
| Közvetlen ügynök | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Eredmények szűkítése az elemek egy halmazára: `let`, `makeset`, `toscalar`, `in`
Gyakori forgatókönyv, hogy bizonyos entitások nevét kijelöli a feltételek alapján, majd egy másik, az entitásokra vonatkozó adathalmazt szűr. Előfordulhat például, hogy olyan számítógépeket talál, amelyekről ismert, hogy hiányoznak a frissítések, és azonosítsa azokat az IP-címeket, amelyeket a számítógépek meghívtak:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>Következő lépések

Tekintse meg a [Kusto lekérdezési nyelv](/azure/kusto/query/) használatát ismertető további leckéket a Azure monitor naplózási adataival:

- [Karakterlánc-műveletek](string-operations.md)
- [Dátum-és időműveletek](datetime-operations.md)
- [Összesítési függvények](aggregations.md)
- [Speciális összesítések](advanced-aggregations.md)
- [JSON-és adatstruktúrák](json-data-structures.md)
- [Csatlakozik](joins.md)
- [Diagramok](charts.md)