---
title: "Az Azure Time Series Insights egyéni nézeteinek megosztása paraméteres URL-címek használatával | Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan lehet paraméteres URL-címeket fejleszteni az Azure Time Series Insightsban az ügyfélnézetek egyszerű megosztásához."
services: time-series-insights
ms.service: time-series-insights
author: kfile
ms.author: kfile
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: rest-api
ms.topic: get-started-article
ms.workload: big-data
ms.date: 11/15/2017
ms.openlocfilehash: d7a4401ff87a94265c588aa5ef070bdc8544fe12
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Egyéni nézet megosztása paraméteres URL-cím használatával

Az adott egyéni nézetnek a Time Series Insights Explorerben történő megosztásához programozott módon hozhatja létre az egyéni nézet paraméteres URL-címét.

A Time Series Insights Explorer támogatja az URL-alapú lekérdezési paraméterek használatát a nézeteknek közvetlenül az URL-címből történő megadásához.  Például kizárólag az URL-cím használatával adhatja meg a célkörnyezetet, a keresési predikátumot és a kívánt időtartományt is. Ha a felhasználó a testreszabott URL-címre kattint, a felületen egy, közvetlenül a Time Series Insights portál adott adategységére mutató hivatkozás jelenik meg.  Adathozzáférési szabályzatok érvényesek. 

## <a name="environment-id"></a>Környezetazonosító

A célkörnyezet azonosítóját az `environmentId=<guid>` paraméter adja meg.  Ez a paraméter a teljes adathozzáférési tartománynév egy összetevője, amelyet az Azure Portal környezetáttekintési ablakának jobb felső sarkában talál meg.  Minden, ami az`env.timeseries.azure.com` előtt szerepel. Példa környezetazonosító paraméterre: `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Time

Paraméteres URL-cím esetében abszolút vagy relatív időértékeket is megadhat.

### <a name="absolute-time-values"></a>Abszolút időértékek

Abszolút időértékek megadásához használja a következő paramétereket: `from=<integer>` és `to=<integer>`. 

A `from=<integer>` egy olyan érték, amely JavaScript-ezredmásodpercben adja meg a keresés kezdési időpontját.

A `to=<integer>` egy olyan érték, amely JavaScript-ezredmásodpercben adja meg a keresés befejezési időpontját. 

Az adott dátumhoz tartozó JavaScript-ezredmásodpercek meghatározását lásd: [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relatív időértékek

Relatív időértékek esetében a `relativeMillis=<value>` paramétert használja, ahol a *value* (érték) a legutóbbi háttérbeli adatművelet óta eltelt idő JavaScript-ezredmásodpercben.

A `&relativeMillis=3600000` például a legutóbbi 60 perc adatait jeleníti meg.

Az elfogadott értékek megfelelnek a Time Series Insights Explorer **quick time** menüjének, és a következőket tartalmazzák:

- 1800000 (a legutóbbi 30 perc)
- 3600000 (a legutóbbi 60 perc)
- 10800000 (a legutóbbi 3 óra)
- 21600000 (a legutóbbi 6 óra)
- 43200000 (a legutóbbi 12 óra)
- 86400000 (a legutóbbi 24 óra)
- 604800000 (a legutóbbi 7 nap)
- 2592000000 (a legutóbbi 30 óra)

### <a name="optional-parameters"></a>Választható paraméterek

Az adott Time Series Insights-nézet kifejezéseit a `timeSeriesDefinitions=<collection of term objects>` paraméter határozza meg, amely a következőket tartalmazza:

- `name=<string>`
  - A *kifejezés* neve.
- `splitBy=<string>`
  - A *felosztás* alapjául szolgáló oszlop neve.
- `measureName=<string>`
  - A *mérték* oszlopneve.
- `predicate=<string>`
  - A kiszolgálóoldali szűrés *where* záradéka.

 
### <a name="examples"></a>Példák

Idősorozat-meghatározások URL-paraméterként történő hozzáadásához például használhatja a következőket:

```
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Használja ezeket a példa idősorozat-meghatározásokat a következőkhöz: 

- környezetazonosító
- a legutóbbi 60 perc adatai
- a választható paramétereket tartalmazó kifejezések (F1PressureID, F2TempStation és F3VibrationPL)
 
Ezáltal az alábbi paraméteres URL-címet állíthatja össze egy nézethez:

`https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]`

Ha az előbb említett URL-cím által leírt nézet létrehozásához a Time Series Insights Explorert használta, az a következőképpen fog kinézni:

![A Time Series Insights Explorer kifejezései](media/parameterized-url/url1.png)

A teljes nézet (a diagramot is beleértve) a következőképpen fog kinézni:

![Diagramnézet](media/parameterized-url/url2.png)

## <a name="next-steps"></a>Következő lépések
[Adatok lekérdezése a C# használatával](time-series-insights-query-data-csharp.md)
