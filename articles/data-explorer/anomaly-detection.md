---
title: Idősorozat-anomáliaészlelési & előrejelzés az Azure Data Explorerben
description: Ismerje meg, hogyan elemezheti az idősorozat-adatokat az anomáliaészlelés és -előrejelzés érdekében az Azure Data Explorer használatával.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: a482fef93d43f92257608b65c9c0e2ade535bcca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78194157"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Anomáliaészlelés és előrejelzés az Azure Data Explorerben

Az Azure Data Explorer a felhőszolgáltatásokból vagy az IoT-eszközökről származó telemetriai adatok folyamatos gyűjtését hajtja végre. Ezeket az adatokat a rendszer elemzi a különböző elemzési adatok, például a szolgáltatás állapotának figyelése, fizikai termelési folyamatok, használati trendek és a terhelési előrejelzés. Az elemzés a kiválasztott metrikák idősorozatán történik, hogy a metrika eltérési mintázatát a tipikus normál kiindulási mintához viszonyítva keresse meg. Az Azure Data Explorer natív támogatást nyújt több idősorozat létrehozásához, manipulálásához és elemzéséhez. Másodpercek alatt több ezer idősorozatot hozhat létre és elemezhet, lehetővé téve a közel valós idejű figyelési megoldásokat és munkafolyamatokat.

Ez a cikk részletezi az Azure Data Explorer idősorozat anomáliaészlelési és-előrejelzési képességeit. Az alkalmazandó idősorozat-funkciók egy robusztus, jól ismert bomlási modellen alapulnak, ahol minden eredeti idősorozat ot szezonális, trendi és maradék alkatrészekre bont. Az anomáliákat a maradék komponens kiugró értékei észlelik, míg az előrejelzést a szezonális és trendösszetevők extrapolálásával végzik. Az Azure Data Explorer megvalósítása jelentősen javítja az alapvető bomlási modellt az automatikus szezonalitás észlelésével, a robusztus kiugró elemzéssel és a vektorizált megvalósítással, hogy másodpercek alatt több ezer idősorozatot dolgozzon fel.

## <a name="prerequisites"></a>Előfeltételek

Olvassa el [az Azure Data Explorer idősorozat-elemzését](/azure/data-explorer/time-series-analysis) az idősorozat-képességek áttekintéséhez.

## <a name="time-series-decomposition-model"></a>Idősorozat-bomlási modell

Az Azure Data Explorer natív implementációja az idősorozat-előrejelzéshez és az anomáliadetektáláshoz jól ismert bomlási modellt használ. Ez a modell olyan metrikák idősorozatára vonatkozik, amelyek várhatóan időszakos és trendi viselkedést mutatnak, például a szolgáltatásforgalmat, az összetevő-szívveréseket és az IoT-időszakos méréseket a jövőbeli metrikaértékek előrejelzéséhez és a rendellenesek észleléséhez. A regressziós folyamat feltételezése az, hogy a korábban ismert szezonális és trendviselkedéstől eltérő idősorok véletlenszerűen oszlanak el. Ezután előre jelezheti a jövőbeli metrikaértékeket a szezonális és trend összetevők, együttesen elnevezett alapterv, és figyelmen kívül hagyja a maradék rész. A rendellenes értékeket is észlelheti kiugró elemzés alapján, csak a maradék rész használatával.
Bomlási modell létrehozásához használja [`series_decompose()`](/azure/kusto/query/series-decomposefunction)a függvényt. A `series_decompose()` függvény idősorozatokat vesz fel, és automatikusan lebomlik az egyes idősorok szezonális, trendi, maradék és alapkomponenseire. 

Például a következő lekérdezéssel leválaszthatja egy belső webszolgáltatás forgalmát:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=)**\]**

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

* Az eredeti idősorozat címkéje **szám** (piros). 
* A folyamat a szezonalitás automatikus felismerésével [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) kezdődik a funkció használatával, és kinyeri a **szezonális** mintát (lila színben).
* A szezonális mintát kivonjuk az eredeti idősorozatból, és lineáris [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) regressziót futtatunk a függvény segítségével a **trendösszetevő** megkereséséhez (világoskék színben).
* A függvény kivonja a trendet, a maradék pedig a **maradék** komponens (zöldszínnel).
* Végül a függvény hozzáadja a szezonális és trend összetevőket az **alapvonal** létrehozásához (kék színnel).

## <a name="time-series-anomaly-detection"></a>Idősorozat-anomáliadetektálás

A [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) függvény rendellenes pontokat talál egy idősorozatból. Ez a `series_decompose()` függvény a bomlási modell [`series_outliers()`](/azure/kusto/query/series-outliersfunction) létrehozásához, majd a maradék összetevőn való futtatáshoz hív. `series_outliers()`Tukey kerítéstesztjével kiszámítja az anomáliapontszámokat a maradék komponens minden egyes pontjára. Az 1,5 vagy 1,5 feletti anomáliapontszámok enyhe anomáliaemelkedést vagy csökkenést jeleznek. A 3,0 vagy -3,0 feletti anomáliapontszámok erős anomáliát jeleznek. 

A következő lekérdezés lehetővé teszi a belső webszolgáltatás-forgalom anomáliáinak észlelését:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==)**\]**

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

![Idősorozat-anomáliadetektálás](media/anomaly-detection/series-anomaly-detection.png)

* Az eredeti idősorozat (piros színben). 
* A kiindulási (szezonális + trend) komponens (kék).
* A rendellenes pontok (lila) tetején az eredeti idősorozat. A rendellenes pontok jelentősen eltérnek a várt kiindulási értékektől.

## <a name="time-series-forecasting"></a>Idősorozat-előrejelzés

A [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) függvény előre jelzi az idősorozatok jövőbeli értékeit. Ez a `series_decompose()` függvény a bomlási modell létrehozásához, majd minden egyes idősorozathoz extrapolálja az alapkomponenst a jövőbe.

A következő lekérdezés lehetővé teszi a jövő heti webszolgáltatás-forgalom előrejelzését:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==)**\]**

```kusto
let min_t = datetime(2017-01-05);
let max_t = datetime(2017-02-03 22:00);
let dt = 2h;
let horizon=7d;
demo_make_series2
| make-series num=avg(num) on TimeStamp from min_t to max_t+horizon step dt by sid 
| where sid == 'TS1'   //  select a single time series for a cleaner visualization
| extend forecast = series_decompose_forecast(num, toint(horizon/dt))
| render timechart with(title='Web app. traffic of a month, forecasting the next week by Time Series Decomposition')
```

![Idősorozat-előrejelzés](media/anomaly-detection/series-forecasting.png)

* Eredeti metrika (piros színben). A jövőbeli értékek alapértelmezés szerint hiányoznak, és 0-ra vannak állítva.
* Extrapolálja a kiindulási komponenst (kék színnel) a jövő heti értékek előrejelzéséhez.

## <a name="scalability"></a>Méretezhetőség

Az Azure Data Explorer lekérdezési nyelvének szintaxisa lehetővé teszi, hogy egyetlen hívás több idősorozat feldolgozását. Egyedülálló, optimalizált megvalósítása lehetővé teszi a gyors teljesítményt, ami kritikus fontosságú a hatékony anomáliaészlelés és előrejelzés szempontjából, amikor közel valós idejű forgatókönyvekben több ezer számlálót figyel.

A következő lekérdezés három idősorozat egyidejű feldolgozását mutatja be:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA)**\]**

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

![Idősorozatok méretezhetősége](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Összefoglalás

Ez a dokumentum részletezi a natív Azure Data Explorer-függvényeket az idősorozat-anomáliaészleléshez és -előrejelzéshez. Minden eredeti idősorozat ot szezonális, trendi és maradék komponensekké bontja az anomáliák és/vagy előrejelzés kimutatására. Ezek a funkciók közel valós idejű figyelési forgatókönyvekhez használhatók, például hibaészleléshez, prediktív karbantartáshoz, valamint igény- és terhelés-előrejelzéshez.

## <a name="next-steps"></a>További lépések

Ismerje meg a [gépi tanulási képességeket](/azure/data-explorer/machine-learning-clustering) az Azure Data Explorerben.
