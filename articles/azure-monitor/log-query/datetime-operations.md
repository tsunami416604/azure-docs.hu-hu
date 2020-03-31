---
title: A dátumidő-értékek használata az Azure Monitor naplólekérdezéseiben| Microsoft dokumentumok
description: Bemutatja, hogyan dolgozhat a dátum- és időadatokkal az Azure Monitor naplólekérdezéseiben.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: ea7c98a1b5b4059c5fea0cf1e8ea2ff5ef08d9d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655378"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>A dátumidő-értékek használata az Azure Monitor naplólekérdezéseiben

> [!NOTE]
> A lecke befejezése előtt el kell [kezdenie az Első lépések szolgáltatást az Analytics-portállal,](get-started-portal.md) és [el kell kezdenie a lekérdezéseket.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ez a cikk ismerteti, hogyan dolgozhat a dátum- és időadatokkal az Azure Monitor naplólekérdezéseiben.


## <a name="date-time-basics"></a>A dátum időpontjának alapjai
A Kusto lekérdezési nyelv két fő adattípussal rendelkezik a dátumokhoz és időpontokhoz társítva: datetime és timespan. Minden dátum UTC-ben van kifejezve. Bár több datetime formátum támogatott, az ISO8601 formátum ajánlott. 

Az időtávokat decimálisan fejezik ki, amelyet egy időegység követ:

|Gyorsírás   | időegység    |
|:---|:---|
|n           | nap          |
|ó           | hour         |
|m           | minute       |
|s           | second       |
|Ms          | ezredmásodperc  |
|mikroszekundum | mikroszekundum  |
|Ketyeg        | nanoszekundum   |

A dátumidők et úgy lehet `todatetime` létrehozni, hogy az operátort használó karakterláncot ad. Például egy adott időkereten belül küldött virtuális gép szívverések áttekintéséhez használja az `between` operátort egy időtartomány megadásához.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Egy másik gyakori forgatókönyv a datetime összehasonlítása a jelenvel. Ha például az elmúlt két perc összes szívverését `now` szeretné látni, használhatja az operátort egy két perces idővel együtt:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Ehhez a funkcióhoz egy parancsikon is elérhető:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

A legrövidebb és legolvashatóbb módszer `ago` azonban az operátort használja:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Tegyük fel, hogy a kezdési és befejezési időpont ismerete helyett ismeri a kezdési időpontot és az időtartamot. A lekérdezést a következőképpen írhatja át:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Időegységek konvertálása
Előfordulhat, hogy az alapértelmezetttől eltérő időegységben szeretné kifejezni a dátumidőt vagy az időtartományt. Ha például az elmúlt 30 perc hibaeseményeit tekinti át, és szüksége van egy számított oszlopra, amely megmutatja, hogy milyen régen történt az esemény:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Az `timeAgo` oszlop a következő értékeket tartalmazza: "00:09:31.5118992", ami azt jelenti, hogy hh:mm:ss.fffffff. Ha ezeket az értékeket `numver` a kezdési időpont óta eltelt percekre szeretné formázni, ossza el ezt az értéket "1 perccel":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Összesítések és időintervallumok szerinti korösz
Egy másik gyakori forgatókönyv az, hogy egy adott időszakban egy adott időszakban statisztikákat kell szerezni egy adott időalatt. Ebben a forgatókönyvben az `bin` operátor összegző záradék részeként használható.

A következő lekérdezés segítségével lekérdezi az elmúlt fél órában 5 percenként történt események számát:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Ez a lekérdezés a következő táblát hozza létre:  

|Időgenerált(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Az eredmények gyűjtőinek létrehozásának másik módja `startofday`a függvények használata, mint például:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Ez a lekérdezés a következő eredményeket hozza:

|időbélyeg|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


## <a name="time-zones"></a>Időzónák
Mivel az összes datetime érték UTC-ben van kifejezve, gyakran hasznos ezeket az értékeket a helyi időzónába konvertálni. Ezzel a számítással például az UTC-t PST-időkké alakíthatja át:

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Kapcsolódó függvények

| Kategória | Függvény |
|:---|:---|
| Adattípusok konvertálása | [todatetime](/azure/kusto/query/todatetimefunction)  [totimespan](/azure/kusto/query/totimespanfunction)  |
| Kerekített érték raktárhely méretre | [Bin](/azure/kusto/query/binfunction) |
| Adott dátum vagy idő betöltése | [ezelőtt](/azure/kusto/query/agofunction) [most](/azure/kusto/query/nowfunction)   |
| Az érték egy része | [datetime_part](/azure/kusto/query/datetime-partfunction) [getmonth](/azure/kusto/query/getmonthfunction) [month ofyear](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) [dayofweek](/azure/kusto/query/dayofweekfunction) [dayofweek ofyear](/azure/kusto/query/dayofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) |
| Relatív dátumérték bekésezése  | [endofday](/azure/kusto/query/endofdayfunction) endofweek endofmonth endofyear startofday [startofweek startofmonth](/azure/kusto/query/startofmonthfunction) [startofyear endofweek](/azure/kusto/query/startofyearfunction) [endofmonth](/azure/kusto/query/endofmonthfunction) [endofmonth endofyear](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofmonth](/azure/kusto/query/startofweekfunction) [endofweek](/azure/kusto/query/endofweekfunction) |

## <a name="next-steps"></a>További lépések
Tekintse meg a [Kusto lekérdezési nyelv](/azure/kusto/query/) ének használatát az Azure Monitor naplóadataival:

- [Sztringműveletek](string-operations.md)
- [Aggregátumfüggvények](aggregations.md)
- [Speciális aggregátumok](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Továbbfejlesztett lekérdezésírás](advanced-query-writing.md)
- [Illesztések](joins.md)
- [Diagramok](charts.md)
