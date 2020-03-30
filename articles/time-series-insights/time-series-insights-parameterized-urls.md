---
title: Egyéni nézetek megosztása paraméterezett URL-ekkel – Azure Time Series Insights | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre paraméterezett URL-címeket, hogy egyszerűen megoszthassa a testreszabott felfedezőnézeteket az Azure Time Series Insightsban.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 9dfe499a7d6084a23fd71ab98db472befe71fc04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024364"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Egyéni nézet megosztása paraméteres URL-cím használatával

Ha egyéni nézetet szeretne megosztani a Time Series Insights Explorerben, programozott módon létrehozhatja az egyéni nézet paraméterezett URL-címét.

A Time Series Insights Explorer támogatja az URL-lekérdezési paramétereket, hogy közvetlenül az URL-címből adja meg a nézeteket a felhasználói élményben. Például kizárólag az URL-cím használatával adhatja meg a célkörnyezetet, a keresési predikátumot és a kívánt időtartományt is. Amikor egy felhasználó kiválasztja a testreszabott URL-címet, a felület közvetlenül az adott eszközre mutató hivatkozást biztosít a Time Series Insights portálon. Adathozzáférési szabályzatok érvényesek.

> [!TIP]
> * Tekintse meg az ingyenes [Time Series Insights demót.](https://insights.timeseries.azure.com/samples)
> * Olvassa el a [Time Series Insights Explorer](./time-series-insights-explorer.md) dokumentációját.

## <a name="environment-id"></a>Környezetazonosító

A célkörnyezet azonosítóját az `environmentId=<guid>` paraméter adja meg. Ez egy összetevője az adatelérési teljes tartományn, és megtalálja azt a környezet áttekintésének jobb felső sarkában az Azure Portalon. Minden, ami az`env.timeseries.azure.com` előtt szerepel.

Példa környezetazonosító paraméterre: `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Time

Paraméteres URL-cím esetében abszolút vagy relatív időértékeket is megadhat.

### <a name="absolute-time-values"></a>Abszolút időértékek

Abszolút időértékek megadásához használja a következő paramétereket: `from=<integer>` és `to=<integer>`.

* A `from=<integer>` egy olyan érték, amely JavaScript-ezredmásodpercben adja meg a keresés kezdési időpontját.
* A `to=<integer>` egy olyan érték, amely JavaScript-ezredmásodpercben adja meg a keresés befejezési időpontját.

> [!TIP]
> A dátumok JavaScript ezredmásodpercre történő egyszerű lefordításához próbálja ki az [Epoch & Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relatív időértékek

Relatív időérték esetén `relativeMillis=<value>`használja a , ahol az *érték* JavaScript ezredmásodpercben van az API-ból kapott legutóbbi időbélyegtől.

A `&relativeMillis=3600000` például a legutóbbi 60 perc adatait jeleníti meg.

Az elfogadott értékek megfelelnek a Time Series Insights explorer **gyorsidő** menüjének, és a következőket tartalmazzák:

* `1800000`(Utolsó 30. év)
* `3600000`(Utolsó 60. év)
* `10800000`(Utolsó 3 óra)
* `21600000`(Elmúlt 6 óra)
* `43200000`(Utolsó 12 óra)
* `86400000`(Utolsó 24 óra)
* `604800000`(Elmúlt 7 nap)
* `2592000000`(Utolsó 30 óra)

### <a name="optional-parameters"></a>Választható paraméterek

A `timeSeriesDefinitions=<collection of term objects>` paraméter olyan predikátumkifejezéseket ad meg, amelyek a Time Series Insights nézetben jelennek meg:

| Paraméter | URL-elem | Leírás |
| --- | --- | --- |
| **név** | `\<string>` | A *kifejezés* neve. |
| **splitBy** | `\<string>` | A *felosztás* alapjául szolgáló oszlop neve. |
| **measureName (measureName)** | `\<string>` | A *mérték* oszlopneve. |
| **Predikátum** | `\<string>` | A kiszolgálóoldali szűrés *where* záradéka. |
| **useSum** | `true` | Nem kötelező paraméter, amely a mérték összegének használatával határozza meg. |

> [!NOTE]
> Ha `Events` a **kiválasztott useSum** mérték, akkor a darabszám alapértelmezés szerint be van jelölve.  
> Ha `Events` nincs bejelölve, az átlag beállítás alapértelmezés szerint be van jelölve. |

* A `multiChartStack=<true/false>` kulcs-érték pár lehetővé teszi a halmozást a diagramon.
* A `multiChartSameScale=<true/false>` kulcs-érték pár lehetővé teszi ugyanazt az Y-tengely skálázás kifejezések között egy választható paraméter.  
* A `timeBucketUnit=<Unit>&timeBucketSize=<integer>` lehetővé teszi az intervallum csúszka beállítását, hogy a diagram részletesebb vagy simább, összesítettebb nézetét biztosítsa.  
* A `timezoneOffset=<integer>` paraméter lehetővé teszi, hogy a diagram időzónáját UTC-eltolásként állítsa be.

| Pár(ok) | Leírás |
| --- | --- |
| `multiChartStack=false` | `true`engedélyezve van alapértelmezés `false` szerint így át verem. |
| `multiChartStack=false&multiChartSameScale=true` | A rétegezést engedélyeznie kell az azonos Y tengely irányú skálázás több feltételben való használatához.  Ez `false` alapértelmezés szerint, így `true` a tompított lehetővé teszi ezt a funkciót. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Egységek `days`= `hours` `minutes`, `seconds` `milliseconds`, , , .  Az egység nevét mindig nagybetűvel írja. </br> Adja meg az egységek számát a timeBucketSize kívánt egész számának **átadásával.**  |
| `timezoneOffset=-<integer>` | Az egész számot mindig ezredmásodpercben kell megadnia. |

> [!NOTE]
> **timeBucketUnit** értékek legfeljebb 7 napig simíthatók.
> **a timezoneOffset** értékek nem UTC és helyi idő.

### <a name="examples"></a>Példák

Ha idősorozat-definíciókat szeretne hozzáadni egy Time Series Insights-környezethez URL-paraméterként, fűzz hozzá:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Használja a példa idősorozat-definíciókat a következőkre:

* A környezeti azonosító
* Az utolsó 60 perc adat
* A választható paramétereket tartalmazó kifejezések (**F1PressureID**, **F2TempStation**és **F3VibrationPL**)

A következő paraméterezett URL-címet hozhatja létre egy nézethez:

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![A Time Series Insights-kezelő paraméteres URL-címe](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Tekintse meg az Explorer élő ben a fenti [URL-példát.](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}])

A fenti URL ismerteti és megjeleníti a paraméteres Time Series Insights explorer nézetet. 

* A paraméterezett predikátumok.

  [![A Time Series Insights felfedezőparaméteresített predikátumai.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* A megosztott teljes diagramnézet.

  [![A megosztott teljes diagramnézet.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>További lépések

* További információ az [adatok c# használatával történő lekérdezéséről.](time-series-insights-query-data-csharp.md)

* További információ a [Time Series Insights Explorer ről.](./time-series-insights-explorer.md)
