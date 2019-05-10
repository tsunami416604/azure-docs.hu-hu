---
title: Az Azure Time Series Insights egyéni nézeteinek megosztása paraméteres URL-címek használatával | Microsoft Docs
description: Ez a cikk bemutatja, hogyan lehet paraméteres URL-címeket fejleszteni az Azure Time Series Insightsban az ügyfélnézetek egyszerű megosztásához.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.topic: conceptual
ms.workload: big-data
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: e70eb7ae73e88b37e649d519d0d0428554dd4ab3
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467508"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Egyéni nézet megosztása paraméteres URL-cím használatával

Adott egyéni nézetnek a Time Series Insights Explorerben megosztásához programozott módon létrehozhat az egyéni nézet paraméteres URL-cím.

A Time Series Insights Explorer támogatja az URL-cím lekérdezési paraméterek használatával adja meg a nézetek a felületen közvetlenül az URL-CÍMÉT. Például kizárólag az URL-cím használatával adhatja meg a célkörnyezetet, a keresési predikátumot és a kívánt időtartományt is. Ha a felhasználó a testreszabott URL-címre kattint, a felületen egy, közvetlenül a Time Series Insights portál adott adategységére mutató hivatkozás jelenik meg. Adathozzáférési szabályzatok érvényesek.

> [!TIP]
> * Megtekintheti az ingyenes [Time Series Insights bemutató](https://insights.timeseries.azure.com/samples).
> * Olvassa el a kísérő [Time Series Insights Explorerben](./time-series-insights-explorer.md) dokumentációját.

## <a name="environment-id"></a>Környezetazonosító

A célkörnyezet azonosítóját az `environmentId=<guid>` paraméter adja meg. A teljes adathozzáférési tartománynév egy összetevője, és a környezet áttekintése az Azure Portal jobb felső sarkában található. Minden, ami az`env.timeseries.azure.com` előtt szerepel.

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

Elfogadott értékek megfelelnek a Time Series Insights explorer **gyors piacra** menüben, és tartalmazzák:

* `1800000` (A legutóbbi 30 perc)
* `3600000` (A legutóbbi 60 perc)
* `10800000` (Az elmúlt 3 óra)
* `21600000` (Az elmúlt 6 óra)
* `43200000` (Az elmúlt 12 óra)
* `86400000` (Az elmúlt 24 óra)
* `604800000` (Legutóbbi 7 nap)
* `2592000000` (A legutóbbi 30 óra)

### <a name="optional-parameters"></a>Választható paraméterek

A `timeSeriesDefinitions=<collection of term objects>` paraméter adja meg a feltételeket a Time Series Insights nézetek:

| Paraméter | Elem URL-címe | Leírás |
| --- | --- | --- |
| **name** | `\<string>` | A *kifejezés* neve. |
| **splitBy** | `\<string>` | A *felosztás* alapjául szolgáló oszlop neve. |
| **measureName** | `\<string>` | A *mérték* oszlopneve. |
| **Predikátum** | `\<string>` | A kiszolgálóoldali szűrés *where* záradéka. |
| **useSum** | `true` | Egy nem kötelező paraméter, amely meghatározza az összeg használatát az mértékcsoport. </br>  Vegye figyelembe, ha `Events` a kiválasztott mérték száma alapértelmezés szerint ki van választva.  </br>  Ha `Events` van nincs bejelölve, átlag van alapértelmezettként kiválasztva. |

* A `multiChartStack=<true/false>` kulcs-érték pár lehetővé teszi, hogy a diagram rétegezést.
* A `multiChartSameScale=<true/false>` kulcs-érték pár lehetővé teszi, hogy a azonos y tengelylépték belül egy nem kötelező paraméter.  
* A `timeBucketUnit=<Unit>&timeBucketSize=<integer>` lehetővé teszi, hogy az időköz csúszkáját egy részletesebb vagy simább, módosítsa a diagram nézet több összesítve.  
* A `timezoneOffset=<integer>` a paraméter lehetővé teszi, hogy az UTC Időzóna időeltolódással lehet megtekinteni kívánt diagram időzónáját.

| Pair(s) | Leírás |
| --- | --- |
| `multiChartStack=false` | `true` Alapértelmezés szerint engedélyezve van így át `false` jegyzettömbhöz. |
| `multiChartStack=false&multiChartSameScale=true` | A rétegezést engedélyeznie kell az azonos Y tengely irányú skálázás több feltételben való használatához.  Rendelkezik `false` alapértelmezés szerint tehát a "true" passing lehetővé teszi a funkció. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Egységek = nap, óra, perc, másodperc, ezredmásodperc.  Az egység nevét mindig nagybetűvel írja. </br> Az egységek számának megadásához adja át a kívánt egész számot a timeBucketSize paraméterhez.  Vegye figyelembe, hogy a részletesség legfeljebb 7 napos lehet.  |
| `timezoneOffset=-<integer>` | Az egész számot mindig ezredmásodpercben kell megadnia. </br> Vegye figyelembe, hogy ez a funkció kissé eltér a TSI Explorerben engedélyezett funkciótól, a helyi (böngésző szerinti) időt vagy az egyezményes világidőt (UTC) is kiválaszthatja. |

### <a name="examples"></a>Példák

Az append idősorozat-meghatározások a TSI-környezet URL-paraméterként történő hozzáadásához:

```plaintext
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

A példa idősorozat-meghatározások a használja:

* A környezet azonosítója
* A legutóbbi 60 perc adatai
* A választható paraméterek: alkotó kifejezések (F1PressureID, F2TempStation és F3VibrationPL)

az alábbi paraméteres URL-cím megjelenítéséhez állíthat össze:

```plaintext
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

> [!TIP]
> A live Explorert [az URL-cím használatával](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]).

A fenti URL-CÍMÉT írja le, és a TSI Explorerben engedélyezett képet alkot:

[![Time Series Insights explorer Terms](media/parameterized-url/url1.png)](media/parameterized-url/url1.png#lightbox)

A teljes nézet (a diagramot is beleértve):

[![Diagram nézet](media/parameterized-url/url2.png)](media/parameterized-url/url2.png#lightbox)

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [lekérdezés adatait az C# ](time-series-insights-query-data-csharp.md).

* További információ a [Time Series Insights Explorer](./time-series-insights-explorer.md).
