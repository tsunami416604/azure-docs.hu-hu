---
title: Idősorozat-rendellenességek észlelése & az előrejelzés az Azure-ban Adatkezelő
description: Ismerje meg, hogyan elemezheti az idősorozat-információkat a anomáliák észleléséhez és az előrejelzéshez az Azure Adatkezelő használatával.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: a482fef93d43f92257608b65c9c0e2ade535bcca
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194157"
---
# <a name="anomaly-detection-and-forecasting-in-azure-data-explorer"></a>Anomáliák észlelése és előrejelzése az Azure Adatkezelő

Az Azure Adatkezelő a Cloud Services-ből vagy IoT-eszközökről származó telemetria-adatok folyamatos gyűjtését végzi. Ezeket az adatokat elemezni kell különböző elemzésekhez, például a szolgáltatás állapotának figyeléséhez, a fizikai üzemi folyamatokhoz, a használati trendekhez és a betöltési előrejelzésekhez. Az elemzés a kiválasztott mérőszámok idősorozatán történik, hogy a metrika eltérési mintáját keresse meg a szokásos normál alaptervhez képest. Az Azure Adatkezelő natív támogatást nyújt több idősorozat létrehozásához, kezeléséhez és elemzéséhez. Több ezer idősorozatot hozhat létre és elemezheti másodpercek alatt, így közel valós idejű figyelési megoldásokat és munkafolyamatokat.

Ez a cikk az Azure Adatkezelő Time Series anomália észlelési és előrejelzési képességeit ismerteti. A megfelelő idősorozat-függvények egy robusztus, jól ismert dekompozíciós modellen alapulnak, ahol minden egyes eredeti idősorozat a szezonális, a trend és a fennmaradó összetevőkből áll. A rendszer rendellenességeket észlel a fennmaradó összetevő kiugró elemei alapján, az előrejelzést pedig a szezonális és a trend-összetevők kivetítésével végezheti el. Az Azure Adatkezelő implementálása jelentős mértékben javítja az alapszintű lebomlási modellt az automatikus szezonális észlelés, a robusztus kiugró analízis és a vektoros implementáció révén, amely több ezer idősorozatot dolgoz fel másodpercek alatt.

## <a name="prerequisites"></a>Előfeltételek

Olvassa el az idősorozat [-elemzést az Azure adatkezelő](/azure/data-explorer/time-series-analysis) az idősorozat-képességek áttekintéséhez.

## <a name="time-series-decomposition-model"></a>Idősorozat-lebomlási modell

Az Azure Adatkezelő natív implementációja az idősorozat-előrejelzéshez és a anomáliák észleléséhez egy jól ismert dekompozíciós modellt használ. Ez a modell az időszakos és a trendi viselkedést (például a szolgáltatás forgalmát, az összetevők szívverését) és a IoT rendszeres méréseket, a jövőbeli metrikai értékek előrejelzését és a rendellenesek észlelését elvárt mérőszámok idősorozatára alkalmazza. Ennek a regressziós folyamatnak az a feltételezése, hogy a korábban ismert szezonális és trendi viselkedéstől eltérve az idősorozat véletlenszerűen oszlik meg. Ezt követően előre jelezheti a jövőbeli mérőszám-értékeket a szezonális és trend összetevőkből, együttesen elnevezett alapkonfigurációból, és figyelmen kívül hagyhatja a fennmaradó részt. A rendellenes értékeket a kiugró analízis alapján is azonosíthatja, csak a fennmaradó rész használatával.
A dekompozíciós modell létrehozásához használja a [`series_decompose()`](/azure/kusto/query/series-decomposefunction)függvényt. A `series_decompose()` függvény idősorozatot vesz igénybe, és automatikusan kibontja az egyes idősorokat a szezonális, a trend, a maradék és az alapkonfiguráció összetevőire. 

A belső webszolgáltatás forgalmát például a következő lekérdezéssel lehet kibontani:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WQ3WrDMAyF7/sUukvCnDXJGIOVPEULuwxqoixm/gm2+jf28JObFjbYrmyho3M+yRCD1a5jaGFAJtaW8qaqX8qqLqvnYrMySYHnvxRNWT1B07xW1U03JFEzbVYDWd9Z/KAuUtAUm9UXpLJcSnAH2+LxPZe3AO9gJ6ZbRjvDGLy9EbG/BUemOXnvLxD1AOJ1mijQtWhbyHbbOgOA9RogkqGeAaXn3g1BooVb6OiDNHpD6CjAUccDGv2JrL0TSzozuQHyPYqHdqRkDKN3aBRwkJaCQJIoQ4VsuXh2A/Xezj5SWkVBWSvI0vSoOSsWpLtEpyDwY4KTW8nnJ5ws+2+eAhSyOxjkd+HDVVcIfHplp2TYTxgYTpqnnDUbarM32gPO86PY4jjqfmGw3vGkftNlCi5xNprbWW5kYvENQQnqDh8CAAA=) **\]**

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

![Idősorozat-Kibontás](media/anomaly-detection/series-decompose-timechart.png)

* Az eredeti idősorozat címkéje **szám** (piros). 
* A folyamat elindul a szezonális környezet automatikus észlelésével a [`series_periods_detect()`](/azure/kusto/query/series-periods-detectfunction) függvénnyel, és Kinyeri a **szezonális** mintázatot (lila).
* Az időszakos minta kivonása az eredeti idősorozatból történik, és egy lineáris regresszió fut a [`series_fit_line()`](/azure/kusto/query/series-fit-linefunction) függvény használatával a **trend** összetevő (világoskék) megtalálásához.
* A függvény kivonja a trendet, a maradék pedig a **maradék** összetevő (zöld színnel).
* Végül a függvény hozzáadja a szezonális és a trend összetevőt az **Alapterv** létrehozásához (kék színnel).

## <a name="time-series-anomaly-detection"></a>Idősorozat-anomáliák észlelése

Az [`series_decompose_anomalies()`](/azure/kusto/query/series-decompose-anomaliesfunction) függvény rendellenes pontokat keres az idősorozatok egy adott halmazán. Ez a függvény meghívja a `series_decompose()` a dekompozíciós modell felépítéséhez, majd [`series_outliers()`](/azure/kusto/query/series-outliersfunction) futtatására a fennmaradó összetevőn. `series_outliers()` a Tukey kerítési tesztjét használva kiszámítja a anomália pontszámait a fennmaradó összetevő minden pontjára vonatkozóan. Az anomália 1,5-es vagy alacsonyabb-1,5-os pontszáma enyhe anomália-emelkedést vagy csökkenést jelez. Az anomália 3,0-es vagy alacsonyabb-3,0-as pontszáma erős anomáliát jelez. 

A következő lekérdezés lehetővé teszi a belső webszolgáltatás-forgalomban észlelt rendellenességek észlelését:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA3WR3W7CMAyF73mKI25KpRbaTmjSUJ8CpF1WoXVptPxUifmb9vBLoGO7GFeR7ePv2I4ihpamYdToBBNLTYuqKF/zosyLdbqZqagQl/8UVV68oKreimLSdVFUDZtZR9o2WnxQ48lJ8tXsCzHM7yHMUdfidFiEN4U12AXoloUe0Turp4nYTsaeaYzs/RVedgis80CObkFdI9ltywTAagV4UtQyRKiZgyLEaTGZ9taFQqtIGHI4SX8USn4KltYEJF2YTIeFMFaHPPkMvrWOMuxFoEpDaVjujmo6aq0erafmIY+7ZCiX6wx5mSGJHb3kJA1sF8jB8q69toNwjLPkYfGTseqoja//eLNkRXXyTnuIcVyCneh72cL2YQdtDQ8ZHvIkDcsfPWH+3AvPvObx0FMXD/RLhfDYW9VhtNKwj/8U69M1b2S//AbRUQMWQQIAAA==) **\]**

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

![Idősorozat-anomáliák észlelése](media/anomaly-detection/series-anomaly-detection.png)

* Az eredeti idősorozat (piros színnel). 
* Az alapkonfiguráció (szezonális + trend) összetevő (kék színnel).
* Az eredeti idősorozathoz tartozó rendellenes pontok (lila). A rendellenes pontok jelentősen eltérnek a várt alapértéktől.

## <a name="time-series-forecasting"></a>Idősorozat-előrejelzés

A függvény [`series_decompose_forecast()`](/azure/kusto/query/series-decompose-forecastfunction) idősorozat jövőbeli értékeit Jósolja meg. Ez a függvény meghívja a `series_decompose()` a dekompozíciós modell összeállításához, majd minden egyes idősorozat esetében kikövetkezteti az alapösszetevőt a jövőben.

A következő lekérdezés lehetővé teszi a következő heti webszolgáltatási forgalom előrejelzését:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA22QzW6DMBCE73mKuQFqKISqitSIW98gkXpEDl5iK9hG9uanUR++dqE99YRGO8x845EYRtuO0UIKJtaG8qbebMt6U9avxW41Joe4/+doyvoFTfNW14tPJlOjZqGc1w9n263crSQZ1xlxpi6Q1xSa1ReSLGcJezGtuJ7y+C3gLA6xZM/CTBi8MwshuxnkaUlGYJpS5/ETQUvEzJsiTz+ibZEd9psMQFUBgUbqGSLe7GkkpBVYygfn46EfSVjyuOpwEaN+CNbOxki6M1mZTNSLkAbOv3WSemcmF6j7vSX8dcTUlvOFsZJcFDHFx4wYnmp7JTzjplnlrHmkNvugI8Q0PYO9GAbdww0RyDjLav1XHLnBimAjEG5E5zQ7vRP284x36hOOTtxZ8Q3The8P2QEAAA==) **\]**

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

* Eredeti metrika (piros színnel). A jövőbeli értékek hiányoznak, és alapértelmezés szerint 0 értékre vannak állítva.
* Kikövetkezteti az alapösszetevőt (kék színnel) a következő heti értékek előrejelzéséhez.

## <a name="scalability"></a>Méretezhetőség

Az Azure Adatkezelő lekérdezési nyelv szintaxisa lehetővé teszi, hogy egyetlen hívás dolgozza fel több idősorozatot. Az egyedi optimalizált implementációja lehetővé teszi a gyors teljesítményt, ami kritikus fontosságú a anomáliák hatékony észleléséhez és előrejelzéséhez, ha a közel valós idejű forgatókönyvek több ezer számlálóját figyelik.

A következő lekérdezés három idősorozat egyidejű feldolgozását mutatja be egyszerre:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA21Qy26DMBC85yvmFlChcUirSI34ikTqETl4KVawjfDmqX587UCaHuqLtePxPLYjhtG2YpRQkom1oaQQy3Uulrl4TzezLjLk5T9GkYsViuJDiImnIqlox6F1g745W67VZqbIuMrIA1WeBk2+mH0jjvk4wh5NKU9fSbhTOItdMNmyND2awZkpIbsxyMukDM/UR8/9FV6rIEkXJqvgmsYTl7X0lISHspzvtqt5hjdxPxkeYBHA4gGKFMBiAUilIAfWja617CY1NG4ASX/FSfuj7PRNsg4ZXANz7Fj3HSGuBmOjZ5hYbcSqIBwbZpNk+iQFcQpx4/omrqLamd55qh5v41d22nIybWChOI0qQ9Cg4e5ftyE6zprbhDV3VM4/aQ/Z96/gQTahU4wsYZzlNvs11vYL3BJsCIQz0eHed/W30jz9AUEBI0ktAgAA) **\]**

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

![Idősorozat-méretezhetőség](media/anomaly-detection/series-scalability.png)

## <a name="summary"></a>Összegzés

Ez a dokumentum részletesen ismerteti a natív Azure Adatkezelő functions for Time Series anomália észlelését és előrejelzését. Minden egyes eredeti idősorozat a rendellenességek és/vagy előrejelzések észlelésére szolgáló szezonális, trend és fennmaradó összetevőkből áll. Ezek a funkciók olyan közel valós idejű figyelési forgatókönyvekhez használhatók, mint például a hibák észlelése, a prediktív karbantartás, valamint az igények és a terhelések előrejelzése.

## <a name="next-steps"></a>További lépések

Ismerje meg az Azure Adatkezelő [gépi tanulási funkcióit](/azure/data-explorer/machine-learning-clustering) .
