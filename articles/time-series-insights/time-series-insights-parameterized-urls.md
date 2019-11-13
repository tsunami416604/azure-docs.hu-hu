---
title: Egyéni nézetek megosztása paraméteres URL-címekkel – Azure Time Series Insights | Microsoft Docs
description: Megtudhatja, hogyan fejleszthet a Azure Time Series Insights paraméteres URL-címeket a testreszabott nézetek egyszerű megosztásához.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 10/18/2019
ms.custom: seodec18
ms.openlocfilehash: 145af35f8c36d7f4659c3937209cb0d4d5b221a3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006375"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Egyéni nézet megosztása paraméteres URL-cím használatával

Az egyéni nézetek Time Series Insights Explorerben való megosztásához programozott módon hozhatja létre az egyéni nézet paraméteres URL-címét.

A Time Series Insights Explorer támogatja az URL-lekérdezési paramétereket, amelyek közvetlenül az URL-címről határozzák meg a nézeteket. Például kizárólag az URL-cím használatával adhatja meg a célkörnyezetet, a keresési predikátumot és a kívánt időtartományt is. Amikor a felhasználó kiválasztja a testreszabott URL-címet, az illesztőfelület közvetlenül az adott eszközre mutató hivatkozást biztosít a Time Series Insights portálon. Adathozzáférési szabályzatok érvényesek.

> [!TIP]
> * Tekintse meg az ingyenes [Time Series Insights bemutatót](https://insights.timeseries.azure.com/samples).
> * Olvassa el a mellékelt [Time Series Insights Explorer](./time-series-insights-explorer.md) dokumentációját.

## <a name="environment-id"></a>Környezetazonosító

A célkörnyezet azonosítóját az `environmentId=<guid>` paraméter adja meg. Ez az adatelérési teljes tartománynév összetevője, és a Azure Portal környezetének jobb felső sarkában található. Minden, ami az`env.timeseries.azure.com` előtt szerepel.

Példa környezetazonosító paraméterre: `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Time

Paraméteres URL-cím esetében abszolút vagy relatív időértékeket is megadhat.

### <a name="absolute-time-values"></a>Abszolút időértékek

Abszolút időértékek megadásához használja a következő paramétereket: `from=<integer>` és `to=<integer>`.

* A `from=<integer>` egy olyan érték, amely JavaScript-ezredmásodpercben adja meg a keresés kezdési időpontját.
* A `to=<integer>` egy olyan érték, amely JavaScript-ezredmásodpercben adja meg a keresés befejezési időpontját.

Az adott dátumhoz tartozó JavaScript-ezredmásodpercek meghatározását lásd: [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relatív időértékek

Relatív időértékek esetében a `relativeMillis=<value>` paramétert használja, ahol a *value* (érték) a legutóbbi háttérbeli adatművelet óta eltelt idő JavaScript-ezredmásodpercben.

A `&relativeMillis=3600000` például a legutóbbi 60 perc adatait jeleníti meg.

Az elfogadott értékek a Time Series Insights Explorer **gyors idő** menüjének felelnek meg, és a következők:

* `1800000` (az elmúlt 30 perc)
* `3600000` (utolsó 60 perc)
* `10800000` (elmúlt 3 óra)
* `21600000` (elmúlt 6 óra)
* `43200000` (utolsó 12 óra)
* `86400000` (az elmúlt 24 óra)
* `604800000` (az elmúlt 7 nap)
* `2592000000` (az elmúlt 30 óra)

### <a name="optional-parameters"></a>Választható paraméterek

A `timeSeriesDefinitions=<collection of term objects>` paraméter a Time Series Insights nézet feltételeit adja meg:

| Paraméter | URL-tétel | Leírás |
| --- | --- | --- |
| **name** | `\<string>` | A *kifejezés* neve. |
| **splitBy** | `\<string>` | A *felosztás* alapjául szolgáló oszlop neve. |
| **measureName** | `\<string>` | A *mérték* oszlopneve. |
| **predikátum** | `\<string>` | A kiszolgálóoldali szűrés *where* záradéka. |
| **useSum** | `true` | Egy opcionális paraméter, amely a mérték összegének használatát határozza meg. </br>  Vegye figyelembe, hogy ha `Events` a kiválasztott mérték, a darabszám alapértelmezés szerint ki van választva.  </br>  Ha a `Events` nincs bejelölve, az átlag alapértelmezés szerint ki van választva. |

* A `multiChartStack=<true/false>` kulcs-érték párok a diagramon való halmozást teszik lehetővé.
* Az `multiChartSameScale=<true/false>` kulcs-érték pár lehetővé teszi, hogy a választható paraméteren belül ugyanazt az Y tengelyt méretezheti a feltételek között.  
* A `timeBucketUnit=<Unit>&timeBucketSize=<integer>` lehetővé teszi az intervallum csúszkájának módosítását, hogy a diagram részletesebb vagy simább legyen.  
* A `timezoneOffset=<integer>` paraméterrel beállíthatja, hogy a diagram időzónája az UTC szerint legyen megtekinthető.

| Pár (ok) | Leírás |
| --- | --- |
| `multiChartStack=false` | az `true` alapértelmezés szerint engedélyezve van, ezért `false` a verembe. |
| `multiChartStack=false&multiChartSameScale=true` | A rétegezést engedélyeznie kell az azonos Y tengely irányú skálázás több feltételben való használatához.  Alapértelmezés szerint `false`, ezért a "true" érték beírásával engedélyezi ezt a funkciót. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Egységek = nap, óra, perc, másodperc, ezredmásodperc.  Az egység nevét mindig nagybetűvel írja. </br> Az egységek számának megadásához adja át a kívánt egész számot a timeBucketSize paraméterhez.  Vegye figyelembe, hogy a részletesség legfeljebb 7 napos lehet.  |
| `timezoneOffset=-<integer>` | Az egész számot mindig ezredmásodpercben kell megadnia. </br> Vegye figyelembe, hogy ez a funkció némileg eltér a Time Series Insights Explorerben, ahol engedélyezheti a helyi (böngésző idő) vagy az UTC használatát. |

### <a name="examples"></a>Példák

Ha idősorozat-definíciókat szeretne hozzáadni egy Time Series Insights-környezethez URL-paraméterként, fűzze hozzá a következőt:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Az idősorozat-definíciók használata a következőhöz:

* A környezeti azonosító
* Az utolsó 60 perc
* A választható paramétereket tartalmazó feltételek (F1PressureID, F2TempStation és F3VibrationPL)

A nézethez a következő paraméteres URL-címet hozhatja létre:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> Tekintse meg az Explorer Live [-t az URL-cím használatával](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

A fenti URL-cím leírja és létrehozza a Time Series Insights Explorer nézetet:

[![Time Series Insights Explorer használati feltételei](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

A teljes nézet (beleértve a diagramot is):

[![diagram nézet](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>Következő lépések

* Útmutató az [adatlekérdezéshez a C#használatával ](time-series-insights-query-data-csharp.md).

* Ismerkedjen meg a [Time Series Insights Explorerrel](./time-series-insights-explorer.md).
