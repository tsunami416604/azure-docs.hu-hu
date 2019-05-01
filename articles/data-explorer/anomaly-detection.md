---
title: Time series rendellenességek észlelése, és az Adatkezelőben az Azure-előrejelzés
description: Ismerje meg, hogyan elemezheti a rendellenességek észlelése és előrejelzés az Azure Data Explorer használatával.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: 934dfd3334b6f433c7acdf9816a3fb5e24f0430f
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872008"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Rendellenességek észlelése, és az Adatkezelőben az Azure-előrejelzés

Az Azure Data Explorer telemetriai adat folyamatos gyűjtése a cloud services vagy az IoT-eszközök hajt végre. Ezek az adatok különböző insights – például a figyelés, a szolgáltatás állapotát, fizikai gyártási folyamatok, használati trendeket és terhelés előrejelzés elemzett. Keresse meg a mérőszám viszonyított tipikus normál alapkonfiguráció minta eltérés mintát kiválasztott metrikák időbeli adatsoroknál történik, az elemzés. Az Azure Data Explorer létrehozását, kezelését és több idősorozat-elemzési natív támogatást tartalmaz. Hozhat létre és elemezheti a time series ezer (másodpercben), a közel valós idejű figyelési megoldások és -munkafolyamatok engedélyezése.

Ez a cikk ismerteti az Azure Data Explorer idő sorozat anomáliadetektálási észleléséhez és az előrejelzési képességeket. Az adott időtartományon sorozat funkciók alapuló egy robusztus jól ismert idősorfelbontási modell, ahol minden egyes eredeti idősorozat a szezonális, trend van bontva, és a maradék összetevőit. Rendellenességek előrejelzés végzi el a szezonális kivetítése által kiugró értékek a maradék összetevő, a rendszer észlelt, és a trend összetevőket. Az Azure Data Explorer megvalósítási jelentősen javítja az alapszintű idősorfelbontási modell automatikus szezonalitás észlelése, a kiugró hatékony elemzési és vectorized végrehajtásának folyamata több ezer, a time series másodpercek alatt.

## <a name="prerequisites"></a>Előfeltételek

Olvasási [idősoros elemzés az Azure Data Explorer idő](/azure/data-explorer/time-series-analysis) time series képességek áttekintését.

## <a name="time-series-decomposition-model"></a>Idősorozat-modell felbontása

Az Azure Data Explorer natív végrehajtására az idősorozat-előrejelzés és a rendellenességek észlelése egy jól ismert idősorfelbontási modellt használ. Ez a modell rendszeres és a trend viselkedését, például a szolgáltatás forgalmát, összetevő szívverések és rendszeres mérések IoT-előrejelzési jövőbeli metrikaértékek és észleli a rendellenes eszközök várható metrikák időbeli adatsoroknál alkalmazza a rendszer. A regresszió folyamat során feltételezzük, amely eltérő a korábban már ismert szezonális és a trend viselkedés, a sorozat véletlenszerűen elosztott idő. Ezután a szezonális jövőbeli metrikus értékeit és a trend-összetevők együttesen nevű alapterv, és a fennmaradó rész figyelmen kívül. Emellett észlelheti a rendellenes értékek csak a fennmaradó rész használatával rendkívüli elemzése alapján.
Hozzon létre egy felbontási minta, a függvény használható [ `series_decompose()` ](/azure/kusto/query/series-decomposefunction). A `series_decompose()` függvény vesz igénybe a time series készletét, és automatikusan bontja a minden egyes idősorozat, az évszaknak megfelelő, a trend, a maradék és az alaprendszerének. 

Például egy belső webszolgáltatás forgalom is bont használatával a következő lekérdezést:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (baseline, seasonal, trend, residual) = series_decompose(num, -1, 'linefit')  //  decomposition of a set of time series to seasonal, trend, residual, and baseline (seasonal+trend)
| render timechart with(title='Web app. traffic of a month, decomposition', ysplit=panels)
```

![Idősorfelbontás](media/anomaly-detection/series-decompose-timechart.png)

* Az eredeti idősorozat feliratú **num** (pirossal jelölt). 
* A folyamat elindul az automatikus észlelés a szezonalitás időtartama szerint a függvény használatával [ `series_periods_detect()` ](/azure/kusto/query/series-periods-detectfunction) , és kinyeri a **szezonális** minta (a lila).
* Az időszakos minta ki kell vonni az eredeti idősor és a egy lineáris regressziós fut, a függvény használatával [ `series_fit_line()` ](/azure/kusto/query/series-fit-linefunction) keresése a **trend** (világoskék) összetevőre.
* A függvény kivonja a tendencia, és a fennmaradó a **maradék** (zöld) összetevőre.
* Végül, a függvény hozzáadja az időszakos és összetevőinek trend a **alapkonfiguráció** (a kék).

## <a name="time-series-anomaly-detection"></a>Time series anomáliadetektálás

A függvény [ `series_decompose_anomalies()` ](/azure/kusto/query/series-decompose-anomaliesfunction) rendellenes pontok megkeresi a time series egy csoportján. Ez a függvény meghívja `series_decompose()` hozhat létre a felbontási minta, és a futtatások [ `series_outliers()` ](/azure/kusto/query/series-outliersfunction) a maradék összetevőnél. `series_outliers()` anomáliadetektálási pontszámok egyes pontok, a maradék összetevő Tukey a időkorlát teszt használatával számítja ki. Anomáliadetektálási pontszámok 1.5-ös felett vagy alatt-1.5 jelzésére egy anomáliadetektálási enyhe növekedése vagy elutasíthatja törléseknek megfelelő. Anomáliadetektálási-3.0 alatti vagy feletti 3.0-hoz közeliek erős anomáliadetektálási. 

A következő lekérdezés lehetővé teszi, hogy észlelje a rendellenességeket a belső webes forgalmát:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend (anomalies, score, baseline) = series_decompose_anomalies(num, 1.5, -1, 'linefit')
| render anomalychart with(anomalycolumns=anomalies, title='Web app. traffic of a month, anomalies') //use "| render anomalychart with anomalycolumns=anomalies" to render the anomalies as bold points on the series charts.
```

![Time series anomáliadetektálás](media/anomaly-detection/series-anomaly-detection.png)

* Az eredeti idősorozat (pirossal jelölt). 
* Az alapkonfiguráció (Időszakos + trend) (kék) összetevőre.
* A rendellenes pontokat (a lila) felül az eredeti idősor. A rendellenes pontok várt alapértékek jelentősen eltérnek.

## <a name="time-series-forecasting"></a>Idősor-előrejelzési

A függvény [ `series_decompose_forecast()` ](/azure/kusto/query/series-decompose-forecastfunction) előrejelzi a time series készletét jövőbeli értékét. Ez a függvény meghívja `series_decompose()` hozhat létre a felbontási minta, majd az egyes idősorok extrapolates az alapkonfiguráció összetevő a jövőben.

A következő lekérdezés előre jelezni a jövő héten webes szolgáltatás forgalom teszi lehetővé:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decmposition')
```

![Idősor-előrejelzési](media/anomaly-detection/series-forecasting.png)

* Ez a metrika eredeti (pirossal jelölt). Jövőbeli értékek hiányoznak, és alapértelmezés szerint a 0-ra állítva.
* Az alapkonfiguráció összetevő (a kék) extrapolálja jövő héten értékeinek.

## <a name="scalability"></a>Méretezhetőség

Az Azure Data Explorer lekérdezési nyelvi szintaxis lehetővé teszi, hogy az idősorozatban fel egyetlen meghívása. Gyors teljesítményt, ami kritikus fontosságú hatékony rendellenességek észlelése és a figyelő számlálókat a közel valós idejű felhasználói helyzetek ezer előrejelzés annak egyedi optimalizált végrehajtását teszi lehetővé.

A következő lekérdezés feldolgozása három idősorozat egyszerre jelenít meg:

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid
| extend offset=case(sid=='TS3', 4000000, sid=='TS2', 2000000, 0)   //  add artificial offset for easy visualization of multiple time series
| extend num=series_add(num, offset)
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week for 3 time series')
```

![Time series méretezhetőség](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Összegzés

Ez a dokumentum részletesen natív az adatkezelőt az Azure functions a time series rendellenességek észlelése és előrejelzés, ami jelentősen javítja a alapszintű idősorfelbontási modell, amelyen alapul. Előrejelzés leállításra és minden eredeti idősorozat bontjuk, rendellenességek észlelése esetén. Az idő sorozat anomáliadetektálási észleléséhez és az előrejelzési funkciói előrejelzés betölteni, és közel valós idejű figyelési helyzetek, például a hibák észlelése, a prediktív karbantartási és igény szerint is használják.

## <a name="next-steps"></a>További lépések

Ismerje meg [gépi tanulási funkciókat](/azure/data-explorer/machine-learning-clustering) az Azure Data Explorer.