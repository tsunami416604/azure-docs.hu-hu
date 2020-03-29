---
title: Idősorozat-adatok elemzése az Azure Data Explorer használatával
description: Ismerje meg, hogyan elemezheti az idősorozat-adatokat a felhőben az Azure Data Explorer használatával.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 3873b25394f91ce1c1601c348de2098198ba7fdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74765483"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Idősorozat-elemzés az Azure Data Explorerben

Az Azure Data Explorer (ADX) a felhőszolgáltatásokból vagy az IoT-eszközökről származó telemetriai adatok folyamatos gyűjtését hajtja végre. Ezek az adatok elemezhetők különböző elemzések, például a szolgáltatás állapotának figyelése, a fizikai termelési folyamatok és a használati trendek. Az elemzés a kiválasztott metrikák idősorozatán történik, hogy a mintában a tipikus kiindulási mintához képest eltérést találjunk.
Az ADX natív támogatást tartalmaz több idősorozat létrehozásához, manipulálásához és elemzéséhez. Ebben a témakörben megtudhatja, hogyan használható az ADX **több ezer idősorozat másodpercek alatt**történő létrehozásához és elemzéséhez, lehetővé téve a közel valós idejű figyelési megoldásokat és munkafolyamatokat.

## <a name="time-series-creation"></a>Idősorozatok létrehozása

Ebben a szakaszban a szokásos idősorok nagy készletét hozzuk `make-series` létre egyszerűen és intuitív módon a kezelő használatával, és szükség szerint töltse ki a hiányzó értékeket.
Az idősorozat-elemzés első lépése az eredeti telemetriai tábla idősorozat-készletté való particionálása és átalakítása. A tábla általában egy időbélyeg oszlopot, környezetfüggő dimenziókat és választható metrikákat tartalmaz. A dimenziók az adatok particionálására szolgálnak. A cél az, hogy partíciónként több ezer idősorozatot hozzon létre rendszeres időközönként.

A bemeneti tábla *demo_make_series1* 600K rekordokat tartalmaz tetszőleges webszolgáltatás-forgalom. Az alábbi paranccsal 10 rekordot mintaként használhat:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03MTo0vTi3KTC02VKhRKAFyFQwNADOyzKUbAAAA)**\]**

```kusto
demo_make_series1 | take 10 
```

Az eredményül kapott tábla egy időbélyeg oszlopot, három környezetfüggő dimenzióoszlopot tartalmaz, és nem tartalmaz metrikákat:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Időbélyeg | BrowserVer | OsVer között | Ország/régió |
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

Mivel nincsenek metrikák, csak a forgalomszámát képviselő idősorozatokat hozhatunk létre, amelyeket az operációs rendszer particionál a következő lekérdezéssel particionál:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPwQrCMBAE0Hu/Yo4NVLBn6Td4ULyWtV1tMJtIsoEq/XhbC4J48jgw+5h1rBDrW0UDDakjR7HsWUIrdOM2cbScakxIWYSiffJSL49W+KAkd2N2hVsMGv8yaPw2furFhCVu1gifpelC9loa9Hyh7LTZInh8FFiPSP7K5fufap1UoR4Mzg/s04njjEb2PUfofNYNFPUFtJiguAEBAAA=)**\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- Az [`make-series`](/azure/kusto/query/make-seriesoperator) operátor segítségével hozzon létre egy három idősorozatból álló készletet, ahol:
    - `num=count()`: a forgalom idősorozatai
    - `range(min_t, max_t, 1h)`: az idősorok 1 órás raktárhelyeken jönnek létre az időtartományban (a táblarekordok legrégebbi és legújabb időbélyegei)
    - `default=0`: adja meg a hiányzó raktárhelyek kitöltési módját a normál idősorok létrehozásához. Alternatív megoldásként [`series_fill_forward()`](/azure/kusto/query/series-fill-forwardfunction) [`series_fill_backward()`](/azure/kusto/query/series-fill-backwardfunction) használja [`series_fill_linear()`](/azure/kusto/query/series-fill-linearfunction) [`series_fill_const()`](/azure/kusto/query/series-fill-constfunction)a , és a változások
    - `byOsVer`: partíció operációs rendszer szerint
- A tényleges idősorozat-adatszerkezet az összesített érték numerikus tömbje minden egyes raktárhelyen. A `render timechart` vizualizációhoz használjuk.

A fenti táblázatban három partíció található. Létrehozhatunk egy külön idősort: Windows 10 (piros), 7 (kék) és 8.1 (zöld) minden operációs rendszer verzió, ahogy az a grafikonon látható:

![Idősorozat-partíció](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Idősorozat-elemzési függvények

Ebben a szakaszban a tipikus sorozatfeldolgozási funkciókat hajtjuk végre.
Miután egy sor idősorozat jön létre, ADX támogatja a növekvő függvények feldolgozni és elemezni őket, amely megtalálható az [idősorozat dokumentáció](/azure/kusto/query/machine-learning-and-tsa). Néhány reprezentatív függvényt fogunk leírni az idősorok feldolgozásához és elemzéséhez.

### <a name="filtering"></a>Szűrés

A szűrés gyakori gyakorlat a jelfeldolgozásban, és hasznos az idősorozat-feldolgozási feladatokhoz (például egy zajos jel simítása, változásészlelés).
- Két általános szűrési funkció létezik:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): FIR szűrő alkalmazása. A mozgóátlag egyszerű kiszámításához és az idősorok differenciálásához használható a változásérzékeléshez.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): IIR-szűrő alkalmazása. Exponenciális simításhoz és halmozott összeghez használatos.
- `Extend`az idősorok at egy új mozgó átlagsorozat 5-ös méretű *(ma_num)* tárolók hozzáadásával a lekérdezéshez:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPQavCMBCE7/6KOSYQ4fXgSfobPDx517C2q4bXpLLZQBV/vKkFQTx5WRh25tvZgRUxJK9ooWPuaCAxPcfRR/pnn1kC5wZ35BIjSbjxbDf7EPlXKV6s3a6GmUHTVwya3hkf9tUds1wvEqnEthtLUmPR85HKoO0PxoQXBSFBKJ3YPP9xSyWH5mxxuGKX/1gqlCfl1Neln5EL3R+DmCodhC9MahqHjXVQKbxMW5NScyzQerA7k+gDa1tswzsBAAA=)**\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Idősorozat-szűrés](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Regresszióanalízis

Az ADX támogatja a szegmentált lineáris regresszióanalízist az idősorok trendjének becsléséhez.
- Használja [series_fit_line()](/azure/kusto/query/series-fit-linefunction) segítségével, hogy illeszkedjen a legjobb sort egy idősorozat általános trend észlelése.
- A [series_fit_2lines()](/azure/kusto/query/series-fit-2linesfunction) segítségével észlelheti az alaptervhez viszonyított trendváltozásokat, amelyek hasznosak a figyelési forgatókönyvekben.

Példa `series_fit_line()` és `series_fit_2lines()` függvények egy idősorozat-lekérdezésre:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuKqUUitKEnNS1GACMSnZZbEG+Vk5qUWa1Rq6iCLggSBYkAdRUD1qUUKIIHkjMSiEoXyzJIMjYrk/JzS3DzbCk0AUIIJ02EAAAA=)**\]**

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Idősorozat-regresszió](media/time-series-analysis/time-series-regression.png)

- Kék: eredeti idősorozat
- Zöld: felszerelt vonal
- Piros: két felszerelt vonal

> [!NOTE]
> A funkció pontosan észlelte az ugrási (szintváltozás) pontot.

### <a name="seasonality-detection"></a>Szezonalitás észlelése

Számos metrika szezonális (periodikus) mintákat követ. A felhőszolgáltatások felhasználói forgalma általában napi és heti mintákat tartalmaz, amelyek a legmagasabbak az üzleti nap közepén, és a legalacsonyabbak éjszaka és hétvégén. Az IoT-érzékelők rendszeres időközönként mérnek. A fizikai mérések, például a hőmérséklet, a nyomás vagy a páratartalom szezonális viselkedést is mutathatnak.

A következő példa a webszolgáltatás egy hónapos forgalmára (2 órás raktárhelyekre) alkalmazza a szezonalitás észlelését:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuaqUShKzUtJLVIoycxNTc5ILCoBAHrjE80fAAAA)**\]**

```kusto
demo_series3
| render timechart 
```

![Idősorok szezonalitása](media/time-series-analysis/time-series-seasonality.png)

- A [series_periods_detect()](/azure/kusto/query/series-periods-detectfunction) segítségével automatikusan észlelheti az idősorok időszakait. 
- Használja [series_periods_validate()](/azure/kusto/query/series-periods-validatefunction) ha tudjuk, hogy egy metrikának meghatározott különböző időszakokkal kell rendelkeznie, és ellenőrizni szeretnénk, hogy léteznek-e.

> [!NOTE]
> Ez egy anomália, ha bizonyos időszakok nem léteznek.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12OwQ6CMBBE737FHKmpVtAr39IguwkYyzZ0IZj48TZSLx533szOEAfxieeR0/XwRpzlwb2iilkSShapl5mTQYvd5QvxxJqd1bQEi8vZor6RawaLxsA5FewcOjBKBOP0PXUMXL7lyrCeeIvdRPjrzIw35Qyoe6W2GY4qJMv9yb91xtX0AS7N323BAAAA)**\]**

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | Időszakok | Pontszámok | nap |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

A funkció észleli a napi és heti szezonalitást. A napi pontszámok kevesebb, mint a heti, mert hétvégi napok eltérnek hétköznap.

### <a name="element-wise-functions"></a>Elem-bölcs funkciók

A számtani és logikai műveletek et idősorozatokon lehet elvégezni. A [series_subtract()](/azure/kusto/query/series-subtractfunction) segítségével kiszámíthatjuk a maradék idősorokat, azaz az eredeti nyers metrika és a simított közötti különbséget, és a maradék jel anomáliáit kereshetjük:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WQQU/DMAyF7/sVT5waqWjrgRPqb+AAgmPltR6LSNLJcdhA+/G4izRAnLhEerbfl2cHVkSfBkUPnfNIgaSZOM5DpDceMovn3OGMXGIk8Z+8jDdPPvKjUjw4d78KC4NO/2LQ6Tfjz/jqjEXeVolUYj/OJWnjMPGOStB+gznhSoFPEEqv3Fz2aWukFt3eYfuBh/zMYlA+KafJmsOCrPRh56Ux2UL4wKRN1+LOtVApXF/37RTOfioUfvpz2arQqBVS2Q7rtc6wa4wlkPLVCLXIqE7DHvcsXOOh73Hz4tM0HzO6zQ1gDOx8UOvZrtayst0Y7z4babkkYQxMyQbGPYnCiGIxTS/fXGpfwk+n7uQBAAA=)**\]**

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

![Idősorozat-műveletek](media/time-series-analysis/time-series-operations.png)

- Kék: eredeti idősorozat
- Piros: simított idősorok
- Zöld: maradék idősorok

## <a name="time-series-workflow-at-scale"></a>Idősorozat-munkafolyamat nagy léptékben

Az alábbi példa bemutatja, hogyan futtathatók ezek a függvények nagy méretekben több ezer idősorozaton másodpercek alatt az anomáliadetektáláshoz. Ha négy napon keresztül szeretne néhány telemetriai rekordból mintát látni egy DB-szolgáltatás olvasási számláló metrikájából, futtassa a következő lekérdezést:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKEnMTlUwAQArfAiiGgAAAA==)**\]**

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | Időbélyeg | Loc | anonOp között | Db | DataRead (Olvasd el) |
|   | 2016-09-11 21:00:00.0000000 | 9. | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | 9. | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | 9. | -865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | 9. | 371921734563783410 | 255 | 0 |

És egyszerű statisztikák:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVcgrzbVNzi/NK9HQ1FHIzcyLL7EFkhohnr6uwSGOvgEg0cQKkGhiBZIoAEq2dK9VAAAA)**\]**

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | num | min\_t | max\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

Az olvasási metrika 1 órás raktáraiban (összesen négy nap * 24 óra = 96 pont) történő létrehozása normál mintaingadozást eredményez:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPMQvCMBSE9/6KGxOoYGfpIOjgUBDtXh7twwabFF6ittIfb2rBQSfHg+8+7joOsMZVATlC72vqSFTDtq8subHyLIZ9hgn+Zi2JefKMq/JQ7M/ltjhqvQGSbrbQ8JeFhm/LTyGZInbl1RIhTI3P6X5ROwp0ikmjd/hYYByE3IXV+1G6TEqRtTqahF3DgmAs1y1JwMOEVo0Rzdf6BbBH5FAHAQAA)**\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Idősorok nagy léptékben](media/time-series-analysis/time-series-at-scale.png)

A fenti viselkedés félrevezető, mivel az egyetlen normál idősorozat több ezer különböző példányból van összesítve, amelyek rendellenes mintákkal rendelkezhetnek. Ezért esetenként idősorozatot hozunk létre. Egy példányt a Loc (hely), az anonOp (művelet) és a DB (adott gép) határoz meg.

Hány idősort tudunk létrehozni?

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVUiqVPDJT9ZR8C/QUXBxAkol55fmlQAAWEsFxjQAAAA=)**\]**

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Darabszám |
|   | 18339 |

Most létrehozunk egy 18339-es idősorozatot az olvasási számláló mérőszámból. Hozzáadjuk `by` a záradékot a make-series utasításhoz, lineáris regressziót alkalmazunk, és kiválasztjuk az első két olyan idősort, amely a legjelentősebb csökkenő tendenciát mutat:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPsU7DQBBE+3zFdLmTTGHSgFAKUCiQiIKIe2u5rJ0T9l3YWwcH5eO5JBIFVJSzmnmz07Gi96FWzKExOepIzIb7WPcUDnVi8ZxKHJGGvifxX3yym+pp+biu7pcv1t4Bk+5EofFfFBp/U/4EJsdse+eri4QwbdKc9q1ZkNJrVhYx4IcCHyAUWjbnRcXlpQLl1uLtgOfoCqx2BRYPGcyjctjASPoYSLhA6uKObR5waasbr3XnA5tzrc0RjTtcn0hnKyg55KtkDAvU9+y2JIpPr1ujXjueT9cse+8YlVDTeIfVoNQymiiZ5ENSCi4vM3FQxAblzWx2a6f2G2UcBRyWAQAA)**\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Idősorozat első két](media/time-series-analysis/time-series-top-2.png)

A példányok megjelenítése:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPvW4CMRCEe55iSlsyBWkjChApIoESAb21udsQg38O26AD8fDx3SEUJVXKWc18s2M5wxmvM6bIIVVkKYqaXdCO/EUnjobTBDekk3MUzZU7u9i+rl4229nqXcpnYGQ7CrX/olD7m/InMLoV24HHg0RkqtOUzjuxoEzroiSCx4MC4xHJ71j0i9TwksLkS+LjgmWoFN4ahcW8gLnN7GuImI4niqyQbGhYlgFDm/40WVvjWfS1skRyaPDUkXorKFXl2MSw5yr/pN9Z31SyxuhbAQAA)**\]**

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
|   | Loc | Op | Db | Lejtőn |
|   | 15. | 37 | 1151 | -102743.910227889 |
|   | 13. hely | 37 | 1249 | -86303.2334644601 |

Kevesebb, mint két perc alatt az ADX közel 20 000 idősort elemzett, és két rendellenes idősort észlelt, amelyekben az olvasási szám hirtelen csökkent.

Ezek a fejlett képességek és az ADX gyors teljesítmény egyedülálló és hatékony megoldást kínálnak az idősorozat-elemzéshez.

## <a name="next-steps"></a>További lépések

* Ismerje meg [a Time series anomália észlelése és előrejelzése](/azure/data-explorer/anomaly-detection) az Azure Data Explorerben.
* Ismerje meg a [gépi tanulási képességeket](/azure/data-explorer/machine-learning-clustering) az Azure Data Explorerben.