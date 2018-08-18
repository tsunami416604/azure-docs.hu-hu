---
title: Dátum-idő értékek az Azure Log Analytics-lekérdezések használata |} A Microsoft Docs
description: Dátum és idő adatok Log Analytics-lekérdezések használata módját ismerteti.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 833548a4bfca83a8ee6971f05a4f308cc54d5b5d
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190284"
---
# <a name="working-with-date-time-values-in-log-analytics-queries"></a>Dátum-idő értékek a Log Analytics-lekérdezések használata

> [!NOTE]
> Hajtsa végre [az Analytics-portál – első lépések](get-started-analytics-portal.md) és [Ismerkedés a lekérdezések](get-started-queries.md) ebben a leckében befejezése előtt.

Ez a cikk ismerteti a dátum és idő adatok használata a Log Analytics-lekérdezéseket.


## <a name="date-time-basics"></a>Dátum-idő alapjai
A Log Analytics lekérdezési nyelv rendelkezik hozzárendelt dátumok és időpontok két fő adattípusok: datetime és időtartamhoz. Az összes dátum (UTC) kell megadni. Több datetime formátum támogatott, amíg az ISO8601 formátumot részesíti előnyben. 

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

Időpontok leadó egy karakterlánc használatával hozható létre a `todatetime` operátor. Például, tekintse át a virtuális gép szívveréseket küld egy adott időkereten, akkor is győződjön meg arról, használja a [közötti operátor](https://docs.loganalytics.io/docs/Language-Reference/Scalar-operators/between-operator) vagyis a adjon meg egy időtartományt...

```OQL
Heartbeat
| where TimeGenerated between(datetime("2018-06-30 22:46:42") .. datetime("2018-07-01 00:57:27"))
```

Egy másik gyakori forgatókönyv hasonlít össze, az aktuális dátumból/időből. Ha például az elmúlt két perc minden szívverések megtekintéséhez használhatja a `now` operátor két perc képviselő timespan együtt:

```OQL
Heartbeat
| where TimeGenerated > now() - 2m
```

Ez a függvény egy helyi is érhető el:
```OQL
Heartbeat
| where TimeGenerated > now(-2m)
```

A legrövidebb, és a legtöbb olvasható módszer használata, ha a `ago` operátor:
```OQL
Heartbeat
| where TimeGenerated > ago(2m)
```

Tegyük fel, hogy ahelyett, hogy a kezdő és záró idő, tudja, a kezdési idő és az időtartamot. Akkor is írja újra a lekérdezést a következő:

```OQL
let startDatetime = todatetime("2018-06-30 20:12:42.9");
let duration = totimespan(25m);
Heartbeat
| where TimeGenerated between(startDatetime .. (startDatetime+duration) )
| extend timeFromStart = TimeGenerated - startDatetime
```

## <a name="converting-time-units"></a>Időegységek alakítása
Egy dátum és idő vagy az alapértelmezett értéktől eltérő idő egységben timespan Express hasznos lehet. Például tegyük fel, hogy az elmúlt 30 percben hibaesemények lehetőséggel megtekintheti, és a egy számított oszlopot, amely bemutatja, hogyan olyan régen az esemény történt kell:

```OQL
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated 
```

Megtekintheti a _timeAgo_ oszlop értékei például: "00:09:31.5118992", ami azt jelenti, azok hh:mm:ss.fffffff formázott. Ha ezekre az értékekre, formázhatja a _numver_ perc alatt a kezdési időpont óta, egyszerűen el kell osztani ezt az értéket "1 perces":

```OQL
Event
| where TimeGenerated > ago(30m)
| where EventLevelName == "Error"
| extend timeAgo = now() - TimeGenerated
| extend timeAgoMinutes = timeAgo/1m 
```


## <a name="aggregations-and-bucketing-by-time-intervals"></a>Összesítések és az időszakonkénti bucketing
Egy másik nagyon gyakori forgatókönyv esetén a kell lekérni a statisztikát bizonyos idő alatt az egy adott időfelbontási szint. Ehhez egy `bin` operátor egy summarize záradék részeként is használható.

A számot 5 percenként az elmúlt fél óra során bekövetkezett események beolvasásához használja a következő lekérdezést:

```OQL
Event
| where TimeGenerated > ago(30m)
| summarize events_count=count() by bin(TimeGenerated, 5m) 
```

Ez létrehozza a következő táblázat:  
|TimeGenerated(UTC)|events_count|
|--|--|
|2018-08-01T09:30:00.000|54|
|2018-08-01T09:35:00.000|41|
|2018-08-01T09:40:00.000|42|
|2018-08-01T09:45:00.000|41|
|2018-08-01T09:50:00.000|41|
|2018-08-01T09:55:00.000|16|

Egy másik gyűjtők eredmények létrehozása módja a Funkciók, például a használandó `startofday`:

```OQL
Event
| where TimeGenerated > ago(4d)
| summarize events_count=count() by startofday(TimeGenerated) 
```

Ez a következő eredményt adja:

|időbélyeg|count_|
|--|--|
|2018-07-28T00:00:00.000|7,136|
|2018-07-29T00:00:00.000|12,315|
|2018-07-30T00:00:00.000|16,847|
|2018-07-31T00:00:00.000|12,616|
|2018-08-01T00:00:00.000|5,416  |


## <a name="time-zones"></a>Időzónák
Mivel minden dátum/idő érték (UTC) van kifejezve, legtöbbször hasznos, ha szeretné átalakítani ezek a helyi időzónában. Például a számítás használatával konvertálja PST idő (UTC):

```OQL
Event
| extend localTimestamp = TimeGenerated - 8h
```

## <a name="related-functions"></a>Kapcsolódó függvények

| Kategória | Függvény |
|:---|:---|
| Az adattípusok konvertálása | [ToDateTime](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/todatetime())[totimespan  ](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/totimespan())  |
| A dobozméretet kerek érték | [doboz](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/bin()) |
| Adott dátum és idő lekérése | [ezelőtt](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/ago()) [most](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/now())   |
| Érték részének lekérése | [datetime_part](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/datetime_part()) [getmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/getmonth()) [év hónapja](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/monthofyear()) [getyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/getyear()) [dayofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofmonth()) [dayofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofweek()) [dayofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/dayofyear()) [weekofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/weekofyear()) |
| Első érték dátum  | [endofday](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofday()) [endofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofweek()) [endofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofmonth()) [endofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/endofyear()) [startofday](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofday()) [startofweek](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofweek()) [startofmonth](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofmonth()) [startofyear](https://docs.loganalytics.io/docs/Language-Reference/Scalar-functions/startofyear()) |

## <a name="next-steps"></a>További lépések
Tekintse meg a Log Analytics lekérdezési nyelv segítségével a többi leckék:

- [Karakterlánc-műveletek](string-operations.md)
- [Összesítésfüggvények](aggregations.md)
- [Speciális összesítések](advanced-aggregations.md)
- [JSON és adatstruktúrák](json-data-structures.md)
- [Speciális lekérdezés írása](advanced-query-writing.md)
- [Illesztés](joins.md)
- [Diagramok](charts.md)