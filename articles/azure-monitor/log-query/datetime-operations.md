---
title: Dátum idő értékek az Azure Monitor log-lekérdezések használata |} A Microsoft Docs
description: Dátum és idő adatok használata az Azure Monitor log-lekérdezések módját ismerteti.
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
ms.openlocfilehash: 8350524e51d8ced45586d085fe1b49274aa6db9d
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56269978"
---
# <a name="working-with-date-time-values-in-azure-monitor-log-queries"></a>Dátum idő értékek az Azure Monitor log-lekérdezések használata

> [!NOTE]
> Hajtsa végre [az Analytics-portál – első lépések](get-started-portal.md) és [Ismerkedés a lekérdezések](get-started-queries.md) ebben a leckében befejezése előtt.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Ez a cikk ismerteti a dátum és idő adatok használata az Azure Monitor log-lekérdezéseket.


## <a name="date-time-basics"></a>Dátum-idő alapjai
A Kusto-lekérdezés nyelvi rendelkezik hozzárendelt dátumok és időpontok két fő adattípusok: datetime és időtartamhoz. Az összes dátum (UTC) kell megadni. Több datetime formátum támogatott, amíg az ISO8601 formátumot részesíti előnyben. 

Mérföldkövei időegységet követ tizedes fejezik ki:

|gyorsírás   | időegység    |
|:---|:---|
|n           | nap          |
|ó           | óra         |
|p           | perc       |
|s           | másodperc       |
|ms          | ezredmásodpercnél  |
|mikroszekundumos | mikroszekundumos  |
|osztásjelek        | nanoszekundumos   |

Időpontok leadó egy karakterlánc használatával hozható létre a `todatetime` operátor. Például, tekintse át a virtuális gép szívveréseket küld egy adott időkereten, használja a `between` operátor számára adjon meg egy időtartományt.

```Kusto
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Egy másik gyakori forgatókönyv hasonlít össze, az aktuális dátumból/időből. Ha például az elmúlt két perc minden szívverések megtekintéséhez használhatja a `now` operátor két perc képviselő timespan együtt:

```Kusto
Heartbeat
| where TimeGenerated > now() - 2m
```

Ez a függvény egy helyi is érhető el:
```Kusto
Heartbeat
| where TimeGenerated > now(-2m)
```

A legrövidebb, és a legtöbb olvasható módszer használata, ha a `ago` operátor:
```Kusto
Heartbeat
| where TimeGenerated > ago(2m)
```

Tegyük fel, hogy ahelyett, hogy a kezdő és záró idő, tudja, a kezdési idő és az időtartamot. Akkor is írja újra a lekérdezést a következő:

```Kusto
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Időegységek alakítása
Érdemes egy dátum és idő vagy a nem az alapértelmezett időegységet timespan express. Például, ha az elmúlt 30 percben hibaesemények lehetőséggel megtekintheti, és egy számított oszlopot megjelenítő hogyan régen az esemény történt:

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

A `timeAgo` oszlop értékei például: "00:09:31.5118992", ami azt jelenti, hh:mm:ss.fffffff formázás. Ha szeretné formázni ezekkel az értékekkel a `numver` perc alatt a kezdési időpont óta, el kell osztani ezt az értéket "1 perces":

```Kusto
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Összesítések és az időszakonkénti bucketing
Egy másik gyakori forgatókönyv esetén a kell lekérni a statisztikát bizonyos idő alatt az egy adott időfelbontási szint. Ebben a forgatókönyvben egy `bin` operátor egy summarize záradék részeként is használható.

A számot 5 percenként az elmúlt fél óra során bekövetkezett események beolvasásához használja a következő lekérdezést:

```Kusto
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Ez a lekérdezés a következő táblázat hoz létre:  
|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Egy másik gyűjtők eredmények létrehozása módja a Funkciók, például a használandó `startofday`:

```Kusto
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Ez a lekérdezés eredménye a következő:

|időbélyeg|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416|


## <a name="time-zones"></a>Időzónák
Mivel minden dátum/idő érték (UTC) van kifejezve, hasznos gyakran alakítható át ezeket az értékeket a helyi időzónában. Például a számítás használatával konvertálja PST idő (UTC):

```Kusto
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Kapcsolódó függvények

| Kategória | Függvény |
|:---|:---|
| Az adattípusok konvertálása | [ToDateTime](/azure/kusto/query/todatetimefunction)[totimespan](/azure/kusto/query/totimespanfunction)  |
| A dobozméretet kerek érték | [bin](/azure/kusto/query/binfunction) |
| Adott dátum és idő lekérése | [ezelőtt](/azure/kusto/query/agofunction) [most](/azure/kusto/query/nowfunction)   |
| Érték részének lekérése | [datetime_part](/azure/kusto/query/datetime-partfunction) [getmonth](/azure/kusto/query/getmonthfunction) [év hónapja](/azure/kusto/query/monthofyearfunction) [getyear](/azure/kusto/query/getyearfunction) [dayofmonth](/azure/kusto/query/dayofmonthfunction) [dayofweek](/azure/kusto/query/dayofweekfunction) [dayofyear](/azure/kusto/query/dayofyearfunction) [weekofyear](/azure/kusto/query/weekofyearfunction) |
| A relatív értéket  | [endofday](/azure/kusto/query/endofdayfunction) [endofweek](/azure/kusto/query/endofweekfunction) [endofmonth](/azure/kusto/query/endofmonthfunction) [endofyear](/azure/kusto/query/endofyearfunction) [startofday](/azure/kusto/query/startofdayfunction) [startofweek](/azure/kusto/query/startofweekfunction) [startofmonth](/azure/kusto/query/startofmonthfunction) [startofyear](/azure/kusto/query/startofyearfunction) |

## <a name="next-steps"></a>További lépések
Tekintse meg a többi leckéket a [Kusto-lekérdezés nyelvi](/azure/kusto/query/) adatok naplózása az Azure Monitor szolgáltatással:

- [Karakterlánc-műveletek](string-operations.md)
- [Összesítésfüggvények](aggregations.md)
- [Speciális összesítések](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Speciális lekérdezés írása](advanced-query-writing.md)
- [Illesztés](joins.md)
- [Diagramok](charts.md)