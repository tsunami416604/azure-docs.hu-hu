---
title: Speciális összesítések az Azure Monitor log-lekérdezések |} A Microsoft Docs
description: Az Azure Monitor log-lekérdezések számára elérhető speciális összesítési opciók egy része ismerteti.
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
ms.openlocfilehash: 37eb8ca3c25268dd7923087439a8fbf0fd1f168b
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269909"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Speciális összesítések az Azure Monitor log-lekérdezések

> [!NOTE]
> Hajtsa végre [összesítések az Azure Monitor lekérdezések](./aggregations.md) ebben a leckében befejezése előtt.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ez a cikk ismerteti az Azure Monitor lekérdezések számára elérhető fejlett összesítési opciók egy része.

## <a name="generating-lists-and-sets"></a>Listák és a csoportok létrehozása
Használhat `makelist` pivot adatok egy adott oszlopban lévő értékek sorrendje. Például előfordulhat, hogy szeretné a leggyakrabban használt sorrend események kerül sor a gépeken. Az adatok sorrendje EventIDs az összes olyan számítógépen forgáspont lényegében azt is. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```
|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist` adatok bele lett átadva a sorrendben álló listát hoz létre. Rendezze az eseményeket a legrégebbitől a legújabbig, használja a `asc` helyett a rendelés utasításban `desc`. 

Emellett hasznos csak egyedi értékek listájának létrehozásához. Ezt nevezzük a _beállítása_ és készíthet `makeset`:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```
|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

Például `makelist`, `makeset` is együttműködik rendezett adatok, és a diagrambeli a sorok bele átadott tömbök hoz létre.

## <a name="expanding-lists"></a>Listák kibontása
Más néven inverz működésének `makelist` vagy `makeset` van `mvexpand`, amely kibővíti a sorok értékek listáját. Kibővítheti, hogy a dinamikus oszlopok, mind a JSON-t, és a tömb bármennyi. Például, hogy sikerült ellenőrizni a *szívverés* tábla adatokat küldő számítógépek szívverés az elmúlt egy órában küldött megoldásokhoz:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer | Megoldások | 
|--------------|----------------------|
| computer1 | "security", "updates", "changeTracking" |
| computer2 | "security", "updates" |
| számítógép3 | "antiMalware", "changeTracking" |
| ... | ... | ... |

Használat `mvexpand` egy külön sorban helyett egy vesszővel tagolt lista minden egyes érték megjelenítése:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computer | Megoldások | 
|--------------|----------------------|
| computer1 | "security" |
| computer1 | "updates" |
| computer1 | "Change tracking" |
| computer2 | "security" |
| computer2 | "updates" |
| számítógép3 | "kártevőirtó" |
| számítógép3 | "Change tracking" |
| ... | ... | ... |


Ezt követően az `makelist` újra elemek csoportosítása együtt, és ezúttal megoldásonként számítógépek listájának megtekintéséhez:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```
|Megoldások | list_Computer |
|--------------|----------------------|
| "security" | ["számítógép1", "számítógép2"] |
| "updates" | ["számítógép1", "számítógép2"] |
| "Change tracking" | ["számítógép1", "számítógép3"] |
| "kártevőirtó" | ["számítógép3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Hiányzik a bins kezelése
Egy hasznos alkalmazásának `mvexpand` van szükség a hiányzó bins töltse ki alapértelmezett értékeket. Tegyük fel, hogy egy adott gép üzemideje szívverésével áttekintésével keres. Meg is át szeretné tekinteni a forrás, amely szívverés a _kategória_ oszlop. Normális esetben használjuk lenne egy egyszerű foglalják össze a következő utasítást:

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

Ezeket az eredményeket, ha a gyűjtő társított "2017-06-06T19:00:00Z" hiányzik, mert nincs minden Szívveréses adatokat az adott órában. Használja a `make-series` függvény üres gyűjtők egy alapértelmezett értéket rendelni. A művelet létrehoz egy extra tömb két oszlopot tartalmazó kategóriákhoz, egy az értékek és a egy megfelelő idő gyűjtők a sort:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Kategória | count_ | TimeGenerated |
|---|---|---|
| Közvetlen ügynök | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00.0000000Z","2017-06-06T19:00:00.0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:00.0000000Z",...] |
| ... | ... | ... |

A harmadik eleme a *count_* tömbben 0 várt módon, és a megfelelő időbélyeg "2017-06-06T19:00:00.0000000Z" az a _TimeGenerated_ tömb. A tömb formátuma nehezen olvasható, ha. Használat `mvexpand` bontsa ki a tömbök és állítja elő a kimeneti által generált, ugyanazt a formátumot `summarize`:

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



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>Szűkítheti az eredmények elemek körét: `let`, `makeset`, `toscalar`, `in`
Gyakran előfordul, hogy válassza ki a különböző kritériumok alapján néhány egyedi entitások neve, és szűrjön rá, hogy entitáshalmazt le másik adatkészletet. Például előfordulhat, hogy ismert, hogy a hiányzó frissítések számítógépek keresése és azonosítása, amelyek ezeket a számítógépeket emelte ki az IP-címek:


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

Tekintse meg a többi leckéket a [Kusto-lekérdezés nyelvi](/azure/kusto/query/) adatok naplózása az Azure Monitor szolgáltatással:

- [Karakterlánc-műveletek](string-operations.md)
- [Dátum és idő műveletek](datetime-operations.md)
- [Összesítésfüggvények](aggregations.md)
- [Speciális összesítések](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Illesztés](joins.md)
- [Diagramok](charts.md)