---
title: Idősorozat-adatgyűjtés elemzése az Azure Adatkezelő
description: Megtudhatja, hogyan elemezheti a felhőben lévő idősorozat-információkat az Azure Adatkezelő használatával.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 3873b25394f91ce1c1601c348de2098198ba7fdd
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74765483"
---
# <a name="time-series-analysis-in-azure-data-explorer"></a>Idősorozat-elemzés az Azure-ban Adatkezelő

Az Azure Adatkezelő (ADX) telemetria-adatok folyamatos gyűjtését végzi a Cloud Services-ből vagy a IoT-eszközökről. Ezeket az adatokat elemezheti különböző elemzésekhez, például a szolgáltatás állapotának figyeléséhez, a fizikai üzemi folyamatokhoz és a használati trendekhez. Az elemzés a kiválasztott mérőszámok idősorozatán történik, hogy a mintázatot a jellemző alaptervhez képest megkeresse.
A ADX natív támogatást tartalmaz több idősorozat létrehozásához, kezeléséhez és elemzéséhez. Ebből a témakörből megtudhatja, hogyan lehet **másodpercek**alatt létrehozni és elemezni az ADX, így közel valós idejű figyelési megoldásokat és munkafolyamatokat hozhat létre.

## <a name="time-series-creation"></a>Idősorozat létrehozása

Ebben a szakaszban a rendszeres idősorozatok nagy készletét hozunk létre egyszerűen és intuitív módon a `make-series` operátor használatával, és szükség szerint kitöltheti a hiányzó értékeket.
Az idősorozat-elemzés első lépése az eredeti telemetria-tábla particionálása és átalakítása idősorozatra. A tábla általában időbélyeg-oszlopot, környezetfüggő dimenziókat és választható metrikákat tartalmaz. A dimenziók az adatparticionálásra szolgálnak. A cél az, hogy a partíciók több ezer időpontot hozzon létre a rendszeres időközönként.

A bemeneti tábla *demo_make_series1* a webszolgáltatások tetszőleges 600K tartozó rekordokat tartalmazza. Használja az alábbi parancsot a 10-es rekordok mintavételéhez:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03MTo0vTi3KTC02VKhRKAFyFQwNADOyzKUbAAAA) **\]**

```kusto
demo_make_series1 | take 10 
```

Az eredményül kapott tábla timestamp oszlopot, három környezetfüggő dimenzió oszlopot tartalmaz, és nincsenek metrikák:

|   |   |   |   |   |
| --- | --- | --- | --- | --- |
|   | Időbélyeg | BrowserVer | OsVer | Ország/régió |
|   | 2016-08-25 09:12:35.4020000 | Chrome 51,0 | Windows 7 rendszeren | Egyesült Királyság |
|   | 2016-08-25 09:12:41.1120000 | Chrome 52,0 | Windows 10 |   |
|   | 2016-08-25 09:12:46.2300000 | Chrome 52,0 | Windows 7 rendszeren | Egyesült Királyság |
|   | 2016-08-25 09:12:46.5100000 | Chrome 52,0 | Windows 10 | Egyesült Királyság |
|   | 2016-08-25 09:12:46.5570000 | Chrome 52,0 | Windows 10 | Litván Köztársaság |
|   | 2016-08-25 09:12:47.0470000 | Chrome 52,0 | Windows 8.1 | India |
|   | 2016-08-25 09:12:51.3600000 | Chrome 52,0 | Windows 10 | Egyesült Királyság |
|   | 2016-08-25 09:12:51.6930000 | Chrome 52,0 | Windows 7 rendszeren | Hollandia |
|   | 2016-08-25 09:12:56.4240000 | Chrome 52,0 | Windows 10 | Egyesült Királyság |
|   | 2016-08-25 09:13:08.7230000 | Chrome 52,0 | Windows 10 | India |

Mivel nincsenek mérőszámok, az operációs rendszer az alábbi lekérdezéssel particionálja az idősorozatot, amely a forgalmi számlálót jelöli:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPwQrCMBAE0Hu/Yo4NVLBn6Td4ULyWtV1tMJtIsoEq/XhbC4J48jgw+5h1rBDrW0UDDakjR7HsWUIrdOM2cbScakxIWYSiffJSL49W+KAkd2N2hVsMGv8yaPw2furFhCVu1gifpelC9loa9Hyh7LTZInh8FFiPSP7K5fufap1UoR4Mzg/s04njjEb2PUfofNYNFPUFtJiguAEBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| render timechart 
```

- A [`make-series`](/azure/kusto/query/make-seriesoperator) operátor használatával hozzon létre egy három idősorozatot, ahol:
    - `num=count()`: forgalmi idő sorozata
    - `range(min_t, max_t, 1h)`: az idősorozat 1 órás raktárhelyekben jön létre az időtartományban (a tábla rekordjainak legrégebbi és legmodernebb időbélyege)
    - `default=0`: adjon meg kitöltési metódust a hiányzó raktárhelyekhez a normál idősorozatok létrehozásához. Alternatív megoldásként [`series_fill_const()`](/azure/kusto/query/series-fill-constfunction), [`series_fill_forward()`](/azure/kusto/query/series-fill-forwardfunction), [`series_fill_backward()`](/azure/kusto/query/series-fill-backwardfunction) és [`series_fill_linear()`](/azure/kusto/query/series-fill-linearfunction) is használhatja a módosításokat
    - `byOsVer`: particionálás operációs rendszer szerint
- A tényleges idősorozat adatstruktúrája az összesített érték numerikus tömbje az egyes időraktárhelyek esetében. A vizualizációhoz `render timechart` használjuk.

A fenti táblázatban három partíció található. Létrehozhatunk egy külön idősorozatot: Windows 10 (vörös), 7 (kék) és 8,1 (zöld) minden egyes operációsrendszer-verzióhoz, ahogyan az a gráfban látható:

![Idősorozat-partíció](media/time-series-analysis/time-series-partition.png)

## <a name="time-series-analysis-functions"></a>Idősorozat-elemzési függvények

Ebben a szakaszban a tipikus adatsorozat-feldolgozási függvényeket fogjuk elvégezni.
Az idősorozatok létrehozása után a ADX támogatja a függvények egyre növekvő listáját, amelyekkel feldolgozhatja és elemezheti azokat, amelyek a [Time Series dokumentációjában](/azure/kusto/query/machine-learning-and-tsa)találhatók meg. Az idősorozatok feldolgozásához és elemzéséhez néhány reprezentatív funkciót ismertetünk.

### <a name="filtering"></a>Szűrés

A szűrés gyakori eljárás a jelfeldolgozásban, és hasznos az idősorozat-feldolgozási feladatokhoz (például Smooth a zajos jel, változások észlelése).
- Két általános szűrési funkció létezik:
    - [`series_fir()`](/azure/kusto/query/series-firfunction): a FIR-szűrő alkalmazása. A mozgóátlag és a változás észlelésének idősorozatának egyszerű kiszámítására használatos.
    - [`series_iir()`](/azure/kusto/query/series-iirfunction): IIR-szűrő alkalmazása. Exponenciális simításhoz és halmozott összeghez használatos.
- `Extend` az idősorozatot úgy, hogy az 5. méretű ( *ma_num*nevű) raktárhelyek új mozgóátlag-sorozatát adja hozzá a lekérdezéshez:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPQavCMBCE7/6KOSYQ4fXgSfobPDx517C2q4bXpLLZQBV/vKkFQTx5WRh25tvZgRUxJK9ooWPuaCAxPcfRR/pnn1kC5wZ35BIjSbjxbDf7EPlXKV6s3a6GmUHTVwya3hkf9tUds1wvEqnEthtLUmPR85HKoO0PxoQXBSFBKJ3YPP9xSyWH5mxxuGKX/1gqlCfl1Neln5EL3R+DmCodhC9MahqHjXVQKbxMW5NScyzQerA7k+gDa1tswzsBAAA=) **\]**

```kusto
let min_t = toscalar(demo_make_series1 | summarize min(TimeStamp));
let max_t = toscalar(demo_make_series1 | summarize max(TimeStamp));
demo_make_series1
| make-series num=count() default=0 on TimeStamp in range(min_t, max_t, 1h) by OsVer
| extend ma_num=series_fir(num, repeat(1, 5), true, true)
| render timechart
```

![Idősorozat-szűrés](media/time-series-analysis/time-series-filtering.png)

### <a name="regression-analysis"></a>Regressziós elemzés

A ADX támogatja a szegmentált lineáris regressziós elemzést az idősorozat trendének becslése érdekében.
- A [series_fit_line ()](/azure/kusto/query/series-fit-linefunction) használatával illessze be a legjobb sort egy idősorozatra az általános trendek észlelése érdekében.
- A [series_fit_2lines ()](/azure/kusto/query/series-fit-2linesfunction) használatával azonosíthatja az alaptervhez viszonyított trendek változásait, amelyek a figyelési helyzetekben hasznosak.

Példa `series_fit_line()` és `series_fit_2lines()` függvényekre egy idősorozat-lekérdezésben:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuKqUUitKEnNS1GACMSnZZbEG+Vk5qUWa1Rq6iCLggSBYkAdRUD1qUUKIIHkjMSiEoXyzJIMjYrk/JzS3DzbCk0AUIIJ02EAAAA=) **\]**

```kusto
demo_series2
| extend series_fit_2lines(y), series_fit_line(y)
| render linechart with(xcolumn=x)
```

![Idősorozat-regresszió](media/time-series-analysis/time-series-regression.png)

- Kék: eredeti idősorozat
- Zöld: beépített vonal
- Piros: két beépített vonal

> [!NOTE]
> A függvény pontosan észlelte a Jump (szint változás) pontot.

### <a name="seasonality-detection"></a>Szezonális észlelés

Számos mérőszám a szezonális (időszakos) mintákat követi. A Cloud Services felhasználói forgalma általában napi és heti mintákat tartalmaz, amelyek a legközelebb vannak az üzleti nap közepéhez és a legalacsonyabb éjszakán, illetve a hétvégén. IoT-érzékelők mérése rendszeres időközönként. A fizikai mérések, például a hőmérséklet, a nyomás vagy a páratartalom is megjeleníthetik a szezonális viselkedést.

Az alábbi példa egy webszolgáltatások egy hónapos forgalmára alkalmazza a szezonális észlelést (2 órás raktárhelyek):

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2PL04tykwtNuaqUShKzUtJLVIoycxNTc5ILCoBAHrjE80fAAAA) **\]**

```kusto
demo_series3
| render timechart 
```

![Idősorozatos szezonális](media/time-series-analysis/time-series-seasonality.png)

- A [series_periods_detect ()](/azure/kusto/query/series-periods-detectfunction) használatával automatikusan észlelhetők az idősorozatban lévő időszakok. 
- [Series_periods_validate ()](/azure/kusto/query/series-periods-validatefunction) használata, ha tudjuk, hogy egy metrika adott időtartam (oka) t tartalmazhat, és azt szeretnénk ellenőrizni, hogy létezik-e.

> [!NOTE]
> Ez egy anomália, ha nem létezik konkrét eltérő időszak

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12OwQ6CMBBE737FHKmpVtAr39IguwkYyzZ0IZj48TZSLx533szOEAfxieeR0/XwRpzlwb2iilkSShapl5mTQYvd5QvxxJqd1bQEi8vZor6RawaLxsA5FewcOjBKBOP0PXUMXL7lyrCeeIvdRPjrzIw35Qyoe6W2GY4qJMv9yb91xtX0AS7N323BAAAA) **\]**

```kusto
demo_series3
| project (periods, scores) = series_periods_detect(num, 0., 14d/2h, 2) //to detect the periods in the time series
| mv-expand periods, scores
| extend days=2h*todouble(periods)/1d
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | időszakok | Kotta | nap |
|   | 84 | 0.820622786055595 | 7 |
|   | 12 | 0.764601405803502 | 1 |

A függvény napi és heti szezonális feladatait észleli. A napi pontszám kevesebb, mint a hetente, mert a hétvégi napok eltérnek a hétköznaptól.

### <a name="element-wise-functions"></a>Elem – Wise függvények

Az aritmetikai és logikai műveletek egy idősorozaton is elvégezhető. A [series_subtract ()](/azure/kusto/query/series-subtractfunction) használatával kiszámíthatja a hátralévő idősorozatot, azaz az eredeti nyers metrika és a simított érték közötti különbséget, és megkeresheti a fennmaradó jel rendellenességeit:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WQQU/DMAyF7/sVT5waqWjrgRPqb+AAgmPltR6LSNLJcdhA+/G4izRAnLhEerbfl2cHVkSfBkUPnfNIgaSZOM5DpDceMovn3OGMXGIk8Z+8jDdPPvKjUjw4d78KC4NO/2LQ6Tfjz/jqjEXeVolUYj/OJWnjMPGOStB+gznhSoFPEEqv3Fz2aWukFt3eYfuBh/zMYlA+KafJmsOCrPRh56Ux2UL4wKRN1+LOtVApXF/37RTOfioUfvpz2arQqBVS2Q7rtc6wa4wlkPLVCLXIqE7DHvcsXOOh73Hz4tM0HzO6zQ1gDOx8UOvZrtayst0Y7z4babkkYQxMyQbGPYnCiGIxTS/fXGpfwk+n7uQBAAA=) **\]**

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
- Piros: simított idősorozat
- Zöld: hátralévő idő sorozat

## <a name="time-series-workflow-at-scale"></a>Idősorozat-munkafolyamatok méretezése

Az alábbi példa azt mutatja be, hogy ezek a függvények hogyan futhatnak több ezer időpontra, másodpercek alatt, a anomáliák észlelése érdekében. Ha egy adatbázis-szolgáltatás olvasási darabszám metrikájának néhány telemetria rekordját szeretné megtekinteni négy nap alatt, futtassa a következő lekérdezést:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKEnMTlUwAQArfAiiGgAAAA==) **\]**

```kusto
demo_many_series1
| take 4 
```

|   |   |   |   |   |   |
| --- | --- | --- | --- | --- | --- |
|   | IDŐBÉLYEG | Loc | anonOp | DB | DataRead |
|   | 2016-09-11 21:00:00.0000000 | Loc. 9 | 5117853934049630089 | 262 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc. 9 | 5117853934049630089 | 241 | 0 |
|   | 2016-09-11 21:00:00.0000000 | Loc. 9 | – 865998331941149874 | 262 | 279862 |
|   | 2016-09-11 21:00:00.0000000 | Loc. 9 | 371921734563783410 | 255 | 0 |

És egyszerű statisztikák:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVcgrzbVNzi/NK9HQ1FHIzcyLL7EFkhohnr6uwSGOvgEg0cQKkGhiBZIoAEq2dK9VAAAA) **\]**

```kusto
demo_many_series1
| summarize num=count(), min_t=min(TIMESTAMP), max_t=max(TIMESTAMP) 
```

|   |   |   |   |
| --- | --- | --- | --- |
|   | NUM | min\_t | maximális\_t |
|   | 2177472 | 2016-09-08 00:00:00.0000000 | 2016-09-11 23:00:00.0000000 |

Idősorozatok létrehozása az olvasási metrika 1 órás tárolójában (összesen négy nap * 24 óra = 96 pont), a normális mintázat ingadozását eredményezi:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPMQvCMBSE9/6KGxOoYGfpIOjgUBDtXh7twwabFF6ittIfb2rBQSfHg+8+7joOsMZVATlC72vqSFTDtq8subHyLIZ9hgn+Zi2JefKMq/JQ7M/ltjhqvQGSbrbQ8JeFhm/LTyGZInbl1RIhTI3P6X5ROwp0ikmjd/hYYByE3IXV+1G6TEqRtTqahF3DgmAs1y1JwMOEVo0Rzdf6BbBH5FAHAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h)
| render timechart with(ymin=0) 
```

![Idősorozat a skálán](media/time-series-analysis/time-series-at-scale.png)

A fenti viselkedés félrevezető, mivel az egyetlen normál idősorozat több ezer különböző példányból van összesítve, amelyek rendellenes mintákat tartalmazhatnak. Ezért hozzunk létre egy idősorozatot egy példányban. A példányokat a Loc (hely), a anonOp (művelet) és az adatbázis (adott gép) határozza meg.

Hány idősorozat hozható létre?

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA0tJzc2Pz03Mq4wvTi3KTC025KpRKC7NzU0syqxKVUiqVPDJT9ZR8C/QUXBxAkol55fmlQAAWEsFxjQAAAA=) **\]**

```kusto
demo_many_series1
| summarize by Loc, Op, DB
| count
```

|   |   |
| --- | --- |
|   | Mennyiség |
|   | 18339 |

Most hozzuk létre az olvasási szám metrikájának 18339-es idősorozatát. Hozzáadjuk a `by` záradékot a make-Series utasításhoz, lineáris regressziót alkalmazunk, és kiválasztjuk a legjelentősebb csökkenő trendtel rendelkező első két idősorozatot:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPsU7DQBBE+3zFdLmTTGHSgFAKUCiQiIKIe2u5rJ0T9l3YWwcH5eO5JBIFVJSzmnmz07Gi96FWzKExOepIzIb7WPcUDnVi8ZxKHJGGvifxX3yym+pp+biu7pcv1t4Bk+5EofFfFBp/U/4EJsdse+eri4QwbdKc9q1ZkNJrVhYx4IcCHyAUWjbnRcXlpQLl1uLtgOfoCqx2BRYPGcyjctjASPoYSLhA6uKObR5waasbr3XnA5tzrc0RjTtcn0hnKyg55KtkDAvU9+y2JIpPr1ujXjueT9cse+8YlVDTeIfVoNQymiiZ5ENSCi4vM3FQxAblzWx2a6f2G2UcBRyWAQAA) **\]**

```kusto
let min_t = toscalar(demo_many_series1 | summarize min(TIMESTAMP));  
let max_t = toscalar(demo_many_series1 | summarize max(TIMESTAMP));  
demo_many_series1
| make-series reads=avg(DataRead) on TIMESTAMP in range(min_t, max_t, 1h) by Loc, Op, DB
| extend (rsquare, slope) = series_fit_line(reads)
| top 2 by slope asc 
| render timechart with(title='Service Traffic Outage for 2 instances (out of 18339)')
```

![Első két idősorozat](media/time-series-analysis/time-series-top-2.png)

A példányok megjelenítése:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WPvW4CMRCEe55iSlsyBWkjChApIoESAb21udsQg38O26AD8fDx3SEUJVXKWc18s2M5wxmvM6bIIVVkKYqaXdCO/EUnjobTBDekk3MUzZU7u9i+rl4229nqXcpnYGQ7CrX/olD7m/InMLoV24HHg0RkqtOUzjuxoEzroiSCx4MC4xHJ71j0i9TwksLkS+LjgmWoFN4ahcW8gLnN7GuImI4niqyQbGhYlgFDm/40WVvjWfS1skRyaPDUkXorKFXl2MSw5yr/pN9Z31SyxuhbAQAA) **\]**

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
|   | Loc | Op | DB | lejtőn |
|   | Loc. 15 | 37 | 1151 | – 102743,910227889 |
|   | Loc 13 | 37 | 1249 | -86303.2334644601 |

A ADX kevesebb mint két perc alatt elemezte a 20 000-es idősorozatot, és két rendellenes idősorozatot észlelt, amelyben az olvasási szám hirtelen eldobásra kerül.

Ezek a fejlett funkciók a ADX gyors teljesítményével kombinálva egyedi és hatékony megoldást biztosítanak az idősorozat-elemzésekhez.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg a [Time Series anomália észlelését és előrejelzését](/azure/data-explorer/anomaly-detection) az Azure Adatkezelőban.
* Ismerje meg az Azure Adatkezelő [gépi tanulási funkcióit](/azure/data-explorer/machine-learning-clustering) .