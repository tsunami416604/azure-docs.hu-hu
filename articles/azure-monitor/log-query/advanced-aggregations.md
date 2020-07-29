---
title: Speciális összesítések Azure Monitor log-lekérdezésekben | Microsoft Docs
description: Ismerteti a több speciális aggregációs lehetőséget, amelyek elérhetők Azure Monitor a naplók lekérdezéséhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: dba058dce09e958a2ae769d927a5569fb3e42113
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324556"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Speciális összesítések Azure Monitor log lekérdezésekben

> [!NOTE]
> A lecke elvégzése előtt [Azure monitor lekérdezések összesítéseit](./aggregations.md) kell végrehajtania.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ez a cikk ismerteti a Azure Monitor lekérdezések számára elérhető fejlettebb összesítési lehetőségeket.

## <a name="generating-lists-and-sets"></a>Listák és készletek létrehozása
`makelist`Egy adott oszlop értékeinek sorrendje alapján is elvégezheti az adatok kimutatását. Előfordulhat például, hogy meg szeretné vizsgálni a leggyakrabban használt rendezési eseményeket a gépeken. Az adatmennyiséget lényegében az egyes gépek EventIDs sorrendjével lehet kimutatni. 

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

`makelist`létrehoz egy listát abban a sorrendben, ahogy az adatgyűjtés megtörtént. Ha az eseményeket a legrégebbtől a legújabbig szeretné rendezni, használja `asc` a Order utasítást a következő helyett: `desc` . 

Az is hasznos, ha csak a különböző értékek listáját szeretné létrehozni. Ezt egy _készletnek_ nevezzük, amely a következővel hozható létre `makeset` :

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

A hasonlóan a `makelist` `makeset` megrendelt adatmennyiséggel is működik, és az átadott sorok sorrendje alapján hozza a tömböket.

## <a name="expanding-lists"></a>Kiterjesztések listája
A vagy a inverz művelete `makelist` `makeset` `mvexpand` , amely kibővíti az értékek listáját a sorok elkülönítéséhez. A JSON és a tömb tetszőleges számú dinamikus oszlopán kiterjeszthető. Például megtekintheti a *szívverési* táblázatot olyan megoldások esetében, amelyek az elmúlt órában szívverést küldő számítógépekről küldenek adatokat:

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

A használatával `mvexpand` külön sorban jelenítheti meg az egyes értékeket vesszővel tagolt lista helyett:

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


Ezután ismét felhasználhatja az `makelist` elemek csoportosítását, és ez alkalommal a számítógépek listáját tekintheti meg megoldásként:

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
A hasznos alkalmazása `mvexpand` a hiányzó raktárhelyekhez tartozó alapértelmezett értékek kitöltésének szükségessége. Tegyük fel például, hogy egy adott gép üzemidőét keresi a szívverésének vizsgálatával. Érdemes megtekinteni a szívverés forrását is, amely a category ( _Kategória_ ) oszlopban található. Általában egy egyszerű összefoglaló utasítást fogunk használni a következőképpen:

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

Ezekben az eredményekben azonban a "2017-06-06T19:00:00Z" elemhez társított gyűjtő hiányzik, mert az adott órában nem található szívverési érték. A `make-series` függvény használatával rendeljen hozzá egy alapértelmezett értéket az üres gyűjtőhöz. Ez egy sort hoz majd az egyes kategóriákhoz két extra tömb oszloppal, egyet az értékekhez, egyet pedig a megfelelő időgyűjtőhöz:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Kategória | count_ | TimeGenerated |
|---|---|---|
| Közvetlen ügynök | [15, 60, 0, 55, 60, 57, 60,...] | ["2017-06-06T17:00:00.0000000 Z", "2017-06-06T18:00:00.0000000 Z", "2017-06-06T19:00:00.0000000 Z", "2017-06-06T20:00:00.0000000 Z", "2017-06-06T21:00:00.0000000 Z",...] |
| ... | ... | ... |

A *count_* tömb harmadik eleme a várt érték, és a _TimeGenerated_ tömbben a "2017-06-06T19:00:00.0000000 z" egyező időbélyeg szerepel. Ez a tömb formátuma nehezen olvasható. A (z) használatával `mvexpand` bontsa ki a tömböket, és a következő módon hozza létre ugyanazt a formátumot `summarize` :

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



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Eredmények szűkítése a következő elemek egy halmazára:,, `let` `makeset` `toscalar` ,`in`
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

## <a name="next-steps"></a>További lépések

Tekintse meg a [Kusto lekérdezési nyelv](/azure/kusto/query/) használatát ismertető további leckéket a Azure monitor naplózási adataival:

- [Sztringműveletek](string-operations.md)
- [Dátum és idő típusú adatokkal végzett műveletek](datetime-operations.md)
- [Aggregátumfüggvények](aggregations.md)
- [Speciális aggregátumok]()
- [JSON és adatstruktúrák](json-data-structures.md)
- [Illesztések](joins.md)
- [Diagramok](charts.md)

