---
title: Az Azure az adatkezelő segítségével idősorozat-adatok elemzése
description: Ismerje meg, hogyan elemezheti a felhőben az Azure Data Explorer használatával.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 496c033e3df096cdada2b3facba3e73092ffd755
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59051495"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Az Azure Data Explorer idősoros elemzés

Az Azure Data Explorer (ADX) telemetriai adatok gyűjtésének folyamatos cloud services vagy az IoT-eszközök hajt végre. Ezek az adatok elemezhetők a különféle elemzéseket, például a figyelési szolgáltatás állapotát, fizikai gyártási folyamatok és használati trendeket. Elemzés az eltérés a tipikus alapkonfiguráció minta képest minta található kiválasztott metrikák időbeli adatsoroknál történik.
ADX létrehozását, kezelését és több idősorozat-elemzési natív támogatást tartalmaz. Ebben a témakörben megtudhatja, hogyan ADX használható létrehozása és elemzése **idősorozat másodpercek alatt több ezer**, lehetővé téve, közel valós idejű megfigyelési megoldásokat, és munkafolyamatokat.

## <a name="time-series-creation"></a>Time series létrehozása

Ebben a szakaszban létrehozunk rengeteg rendszeres időbeli adatsorok használatával egyszerűen és intuitív módon a `make-series` operátor és a kitöltés a hiányzó értékeket a szükséges.
Az első lépés az idősoros elemzés particionálja, és a time series készletét használja az eredeti telemetriai tábla átalakítása. A tábla az időbélyegző-oszlopa, környezetfüggő dimenziók és választható metrikák általában tartalmazza. A dimenziók segítségével az adatok particionálásához. A cél, hogy rendszeres időközönként idősorozat partíciónként több ezer létrehozása.

A bemeneti tábla *demo_make_series1* tetszőleges webes szolgáltatás forgalom 600 ezer rekordot tartalmaz. Használja az alábbi parancsot a minta a 10 rekordot:

```kusto
demo_make_series1 | take 10 
```

Az eredményül kapott tábla időbélyegző-oszlopa, három környezetfüggő dimenziók, és a metrika sem tartalmazza:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Időbélyeg | BrowserVer | OsVer | Ország |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51.0 | Windows 7 | Egyesült Királyság |
|   | 2016-08-25 09:12:41.1120000 | Chrome 52.0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrome 52.0 | Windows 7 | Egyesült Királyság |
|   | 2016-08-25 09:12:46.5100000 | Chrome 52.0 | Windows 10 | Egyesült Királyság |
|   | 2016-08-25 09:12:46.5570000 | Chrome 52.0 | Windows 10 | Litván Köztársaság |
|   | 2016-08-25 09:12:47.0470000 | Chrome 52.0 | Windows 8.1 | India |
|   | 2016-08-25 09:12:51.3600000 | Chrome 52.0 | Windows 10 | Egyesült Királyság |
|   | 2016-08-25 09:12:51.6930000 | Chrome 52.0 | Windows 7 | Hollandia |
|   | 2016-08-25 09:12:56.4240000 | Chrome 52.0 | Windows 10 | Egyesült Királyság |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52.0 | Windows 10 | India |

Mivel ebben az esetben nincs metrikák, a forgalom száma, az operációs rendszer a következő lekérdezéssel dokumentumtárolási jelölő a time series készletét csak olyan:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Használja a [ `make-series` ](/azure/kusto/query/make-seriesoperator) operátor létre egy három idősorozat, ahol:
    - `num=count()`: time series forgalom
    - `range(min_t, max_t, 1h)`: a time series jön létre a következő időtartományban: (a tábla rekordjait legrégebbi és a legújabb időbélyegeket) 1 óra bins
    - `default=0`: Adja meg a hiányzó bins hozhat létre a rendszeres time series kitöltés módszert. Alternatív megoldásként [ `series_fill_const()` ](/azure/kusto/query/series-fill-constfunction), [ `series_fill_forward()` ](/azure/kusto/query/series-fill-forwardfunction), [ `series_fill_backward()` ](/azure/kusto/query/series-fill-backwardfunction) és [ `series_fill_linear()` ](/azure/kusto/query/series-fill-linearfunction) módosítások
    - `byOsVer`: az operációs rendszer által partíció
- A tényleges idősorozat-adatok struktúrát az összesített érték minden egyes alkalommal bin / numerikus tömbje. Használjuk a `render timechart` Vizualizáció céljából.

A fenti táblázatban három partíció van. Létrehozhatunk egy külön idősorozat: Windows 10 (vörös), 7 (kék) és 8.1-es (zöld) minden egyes operációs rendszer verzió a grafikonon látható módon:

![Time series partíció](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Time series elemzési funkciók

Ebben a szakaszban azt feldolgozási funkciók jellemző sorozat kell végrehajtani.
A time series készletét létrehozása után ADX támogatja az egyre nagyobb számban elérhető függvények feldolgozására és elemzésére azokat, amelyek megtalálhatók a [sorozat dokumentációja idő](/azure/kusto/query/machine-learning-and-tsa). Néhány reprezentatív funkciók feldolgozásához és elemzéséhez a time series bemutatunk néhányat.

### <a name="filtering"></a>Szűrés

Szűrés egy gyakori eljárás a jel feldolgozási és hasznos idősorozat-feldolgozási feladatokat (például zajos jel smooth, észlelés módosításához).
- Nincsenek két általános szűrési funkciók:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): RÉSZÉHEZ szűrő alkalmazásával. A mozgó átlag és az üzleti differenciálás a idősorozat címváltozásának felderítését a egyszerű kiszámításához használt.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): IIR szűrő alkalmazásával. Exponenciális simítás és sum összesítő használható.
- `Extend` az idősorozat, adja hozzá az új mozgó átlag sorozatát állítsa 5 dobozok mérete (nevű *ma_num*) a lekérdezés:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Idősorozat-szűrés](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Regressziós elemzést

Támogatja a ADX szegmentált becsült alakulását az idősor lineáris regressziós elemzést.
- Használat [series_fit_line()](/azure/kusto/query/series-fit-linefunction) megfelelően a legjobb sor a time Series általános trendjét észleléséhez.
- Használat [series_fit_2lines()](/azure/kusto/query/series-fit-2linesfunction) trend változások, az alaptervhez képest, amelyek hasznosak a figyelési forgatókönyvek az észleléséhez.

Példa `series_fit_line()` és `series_fit_2lines()` függvények a time series lekérdezés:

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Time series regresszió](media/time-series-analysis/time-series-regression.png)

- Kék: eredeti idősorozat
- Zöld: felszerelt sor
- Red: két illesztett sorok

> [!NOTE]
> A függvény ismerte fel a jump (a szint módosítása) pontra.

### <a name="seasonality-detection"></a>Szezonalitás észlelése

Számos metrikák szezonális (rendszeres) minták kövesse. A cloud services, a felhasználói adatforgalmat általában tartalmazza a napi és heti mintákat, amelyek a középső üzleti napi és a legalacsonyabb legmagasabb éjszaka és a hétvégén. IoT-érzékelőkről mérték rendszeres időközönként. Például a hőmérsékletet, nyomás vagy páratartalom fizikai mértékek is megjelenhetnek szezonális viselkedését.

Az alábbi példa egy hónap (2 óra bins) webszolgáltatás forgalmát a szezonalitás észlelési vonatkozik:

```kusto
demo_series3
| render timechart 
```

![Time series szezonalitás](media/time-series-analysis/time-series-seasonality.png)

- Használat [series_periods_detect()](/azure/kusto/query/series-periods-detectfunction) automatikusan észlelni az időszakok idősorozatban található. 
- Használat [series_periods_validate()](/azure/kusto/query/series-periods-validatefunction) Ha tudjuk, hogy egy metrika kell rendelkeznie az adott különböző megismétlésével, és győződjön meg arról, hogy azok léteznek szeretnénk.

> [!NOTE]
> Ha még nem léteznek a megadott különböző időszakok anomáliát

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mvexpand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | időszakok | Pontszámok | nap |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

A függvény a napi és heti szezonalitás észleli. A napi kisebb, mint a heti pontszámmodell, mivel hétvégi napok különböznek a weekdays is.

### <a name="element-wise-functions"></a>Element-Wise funkciók

Egy time Series aritmetikai és logikai műveleteket végezheti el. Használatával [series_subtract()](/azure/kusto/query/series-subtractfunction) lehet egy maradék idősorozat, amely, az eredeti nyers metrika- és a egy visszametszéses közötti különbség kiszámítása, és keresse meg a fennmaradó jel protokollmegvalósításokat:

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| extend residual_num=series_subtract(num, ma_num) //to calculate residual time series
| where OsVer == "Windows 10"   // filter on Win 10 to visualize a cleaner chart 
| render timechart
```

![Time series műveletek](media/time-series-analysis/time-series-operations.png)

- Kék: eredeti idősorozat
- Piros: a time series Görbített
- Zöld: maradék idősorozat

## <a name="time-series-workflow-at-scale"></a>Time series munkafolyamat ipari méretekben

Az alábbi példa bemutatja, hogyan ezek a függvények futhatnak ipari méretekben a time series ezer anomáliadetektálás másodpercben. Egy adatbázis-szolgáltatás olvasási száma mérőszám néhány minta telemetriai rekordok megtekintéséhez a négy nap a következő lekérdezés futtatásával:

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | IDŐBÉLYEG | hely | anonOp | DB | DataRead |
|   | 2016-09-11 21:00:00.0000000 | Hely 9 | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Hely 9 | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Hely 9 | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Hely 9 | 371921734563783410 | 255 | 0 |

És egyszerű statisztikai:

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | num | Min\_t | max\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

A mérőszám olvasási 1 óra dobozok az idősor lépésközi létrehozásához (összesen négy nap * 24 óra = 96 pontok), normál minta ingadozása eredményezi:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Időbeli adatsorok ipari méretekben](media/time-series-analysis/time-series-at-scale.png)

A fenti viselkedése azért félrevezető, mert az egy normál idősorozat összesített értéket jelenít meg a különböző példányok, amelyek esetlegesen rendellenes minták több ezer eszközről. Ezért hozunk létre egy idősorozat példányonként. Egy példány Csatornatérképeket (hely), a anonOp (művelet) és a DB (az adott gép) határozzák meg.

Hány idősorozat is létrehozunk?

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Darabszám |
|   | 18339 |

Most fogunk olvasási száma mérőszám 18339 idősorozat csoportját hozhatja létre. Adjuk hozzá a `by` záradékot a márka sorozat utasítás lineáris regressziós érvényesek, és válassza a felső két idősorozat, akinek a legjelentősebb csökkenő trend:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![A Time series az első két](media/time-series-analysis/time-series-top-2.png)

Megjeleníti a példányok:

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc
| project Loc, Op, DB, slope 
```

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | hely | Op | DB | görbét |
|   | Hely 15 | 37 | 1151 | -102743.910227889 |
|   | Hely 13 | 37 | 1249 | -86303.2334644601 |

Kevesebb mint két perc alatt ADX közel 20 000 idősorozat elemzett, és azt észlelte, amelyben az olvasási száma hirtelen eldobott két rendellenes idősorozat.

Speciális képességek ADX gyors teljesítményt kombinálva adja meg a idősoros elemzés egyedi és hatékony megoldást.
