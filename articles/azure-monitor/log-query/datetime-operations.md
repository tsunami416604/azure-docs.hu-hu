---
title: A dátum-és időértékek használata Azure Monitor log-lekérdezésekben | Microsoft Docs
description: Ismerteti, hogyan használhatók a dátum-és időadatok Azure Monitor naplók lekérdezésében.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: ea7c98a1b5b4059c5fea0cf1e8ea2ff5ef08d9d1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77655378"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>A dátum-és időértékek használata Azure Monitor log-lekérdezésekben

> [!NOTE]
> A lecke elvégzése előtt fejezze be [az Analytics-portál és a](get-started-portal.md) [lekérdezések első](get-started-queries.md) lépéseit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ez a cikk azt ismerteti, hogyan használhatók a dátum-és időadatok Azure Monitor napló lekérdezésekben.


## <a name="date-time-basics"></a>Időpontok alapjai
A Kusto lekérdezési nyelvnek két fő adattípusa van, amelyek dátum és idő értékkel vannak társítva: datetime és TimeSpan. Az összes dátum UTC szerint van megadva. Míg több datetime formátum is támogatott, a ISO8601 formátuma javasolt. 

A időtávok tizedes törtként van megadva, majd egy időegység után:

|rövid függvényneve   | időegység    |
|:---|:---|
|n           | nap          |
|ó           | hour         |
|m           | minute       |
|s           | second       |
|MS          | ezredmásodperc  |
|mikromásodperces | mikromásodperces  |
|osztásjel        | NS   |

A dátum-és időértékek létrehozásához egy karakterláncot kell bemutatnia a `todatetime` kezelő használatával. Ha például át szeretné tekinteni az adott időkereten belül küldött virtuális gépek szívverését, `between` az operátor használatával adhatja meg az időtartományt.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Egy másik gyakori forgatókönyv a DateTime és a jelen érték összevetése. Ha például az utolsó két percben szeretné látni az összes szívverést, használhatja a `now` kezelőt egy két percet jelölő TimeSpan:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Ehhez a függvényhez egy parancsikon is elérhető:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

A legrövidebb és a legkönnyebben olvasható módszer `ago` azonban az operátort használja:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Tegyük fel, hogy a kezdő és a záró időpont ismerete helyett a kezdési időt és az időtartamot ismeri. A lekérdezést a következőképpen írhatja át:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Időegységek konvertálása
Előfordulhat, hogy az alapértelmezett értéktől eltérő időegységben szeretné kifejezni a DateTime vagy a TimeSpan értéket. Például, ha az elmúlt 30 percben megtekinti a hibák eseményeit, és egy számított oszlopra van szüksége, amely azt mutatja, hogy az esemény mennyi ideig történt:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Az `timeAgo` oszlop a következő értékeket tartalmazza: "00:09:31.5118992", ami azt jelenti, hogy óó: PP: SS. fffffff formátumban vannak formázva. Ha ezeket az értékeket `numver` a kezdési időpont óta percre szeretné formázni, az értéket az "1 perc" értékkel kell elosztani:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Összesítések és gyűjtők időszakonként
Egy másik gyakori forgatókönyv, hogy egy adott időszakra vonatkozó statisztikai adatokat kell megszereznie egy adott időtartamon belül. Ebben a forgatókönyvben egy `bin` operátor használható egy összefoglaló záradék részeként.

A következő lekérdezéssel lekérdezheti az elmúlt fél órában 5 percenként bekövetkezett események számát:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Ez a lekérdezés a következő táblázatot állítja elő:  

|TimeGenerated (UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

A gyűjtők létrehozásának másik módja a függvények használata, például `startofday`:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Ez a lekérdezés a következő eredményeket eredményezi:

|időbélyeg|count_|
|--|--|
|2018-07-28T00:00:00.000|7 136|
|2018-07-29T00:00:00.000|12 315|
|2018-07-30T00:00:00.000|16 847|
|2018-07-31T00:00:00.000|12 616|
|2018-08-01T00:00:00.000|5 416|


## <a name="time-zones"></a>Időzónák
Mivel az összes datetime érték UTC szerint van kifejezve, gyakran hasznos az értékek helyi időzónába való konvertálása. Ez a számítás például az UTC és a PST-időpontok átalakítására használható:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Kapcsolódó függvények

| Kategória | Függvény |
|:---|:---|
| Adattípusok konvertálása | [ToDateTime](/azure/kusto/query/todatetimefunction)  [ToTimeSpan](/azure/kusto/query/totimespanfunction)  |
| Kerekített érték a raktárhely méretéhez | [bin](/azure/kusto/query/binfunction) |
| Megadott dátum vagy idő beolvasása | [ezelőtt](/azure/kusto/query/agofunction) [most](/azure/kusto/query/nowfunction)   |
| Az érték egy részének beolvasása | [datetime_part](/azure/kusto/query/datetime-partfunction) [getmonth](/azure/kusto/query/getmonthfunction) [monthofyear](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) [DayOfWeek](/azure/kusto/query/dayofweekfunction) [DAYOFYEAR](/azure/kusto/query/dayofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) |
| Relatív dátum értékének beolvasása  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [ENDOFMONTH](/azure/kusto/query/endofmonthfunction) [ENDOFYEAR](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [STARTOFMONTH](/azure/kusto/query/startofmonthfunction) [STARTOFYEAR](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>További lépések
Tekintse meg a [Kusto lekérdezési nyelv](/azure/kusto/query/) használatát ismertető további leckéket a Azure monitor naplózási adataival:

- [Sztringműveletek](string-operations.md)
- [Aggregátumfüggvények](aggregations.md)
- [Speciális aggregátumok](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Továbbfejlesztett lekérdezésírás](advanced-query-writing.md)
- [Illesztések](joins.md)
- [Diagramok](charts.md)
