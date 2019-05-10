---
title: Machine learning-funkció az Azure Data Explorer
description: Machine learning a kiváltó okok elemzését az Azure Adatkezelőben fürtszolgáltatás használatát.
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: bc72cc21ab525ec82d9ce4b24e80ce82d92a5d21
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233493"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Machine learning-funkció az Azure Data Explorer

Az Azure az adatkezelőt, a Big Data-elemzési platform, a service health, QoS vagy nem megfelelően működő eszközök beépített rendellenes viselkedések figyelésére használható [rendellenességek észlelése és előrejelzését](/azure/data-explorer/anomaly-detection) funkciók. Egy rendellenes minta észlelésekor, miután a legfelső szintű okok elemzése (RCA) csökkentése vagy oldja meg az anomáliadetektálási történik meg.

Az elemzés céljából való összetett és hosszú és tartomány-szakértők által végzett. A folyamat magában foglalja a beolvasása, illetve további adatokat az időkeretből, több dimenzióban, diagramkészítési további változók értékeinek eloszlását változásai keres a különböző forrásokból származó csatlakoztatásához és egyéb technikák alapján adatait és intuition. Mivel ezek a diagnosztikai forgatókönyvek az Azure Data Explorer közös, machine learning beépülő modulok érhetők el a diagnosztika szakasz egyszerűbbé és az RCA rövidíteni.

Az Azure adatkezelő rendelkezik három Machine Learning beépülő modulok: [ `autocluster` ](/azure/kusto/query/autoclusterplugin), [ `basket` ](/azure/kusto/query/basketplugin), és [ `diffpatterns` ](/azure/kusto/query/diffpatternsplugin). Az összes beépülő modulok fürtözési algoritmust megvalósításához. A `autocluster` és `basket` beépülő modulokat a fürt egyetlen rekordhalmaz és a `diffpatterns` beépülő fürtök két rekordhalmazok közötti különbségeket.

## <a name="clustering-a-single-record-set"></a>Fürtszolgáltatás egyetlen rekordhalmazok

Egy gyakori forgatókönyv például ez alatt az időszak, amely a rendellenes viselkedést, magas hőmérséklet eszköz olvasmányok, hosszú időtartam parancsokat és kiadások felhasználók felső szolgáltatásnak egy adott feltételnek által kiválasztott adatkészlet tartalmaz. Gyakori minták (szegmensek) megkeresendő az adatok gyorsan és egyszerűen úgy szeretnénk. Minták az adatkészlet, amelynek rekordok ugyanazokat az értékeket osszanak meg több dimenziót (kategorikus oszlopok) a részhalmazát képezik. A következő lekérdezés hozza létre, és a egy idősorozat szolgáltatás kivételek megjeleníti egy adott héten 10 perc bins:

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Szolgáltatás kivételek idődiagramját](media/machine-learning-clustering/service-exceptions-timechart.png)

A szolgáltatás kivételek száma utal. a teljes forgalom szolgáltatás. Hétfőtől péntekig üzleti nap a napi minta egyértelműen láthatja, nőtt a szolgáltatás a kivétel közepes nap száma, valamint az éjszaka száma csökken. Kis száma láthatók a hétvégén. Kivétel ugrásszerűen használatával lehet észlelt [time series anomáliadetektálás](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) az Azure Data Explorer.

Az adatok a második megnövekedett keddi délután történik. A következő lekérdezéssel további diagnosztizálása érdekében a megnövekedett. A lekérdezés segítségével újrarajzolja a diagram a nagyobb felbontást (nyolc órát perces bins) megnövekedett körül, ellenőrizze, hogy azt nem éles ugrásszerű, és a határai megtekintéséhez.

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Megnövekedett idődiagramját összpontosíthat](media/machine-learning-clustering/focus-spike-timechart.png)

Keskeny kétperces ugrásszerű a 15:00, 15:02 látható. A következő lekérdezést Ez két perces ablakban a kivételek száma:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Count |
|---------|
|972    |

A következő lekérdezést a minta 20 kivételek 972 kívül:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Régió | ScaleUnit | DeploymentId                     | Tracepoint | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Fürtszolgáltatás egyetlen rekordot autocluster() használata

Annak ellenére, hogy kevesebb mint Kaliforniában kivételek vannak, ez megegyezik továbbra is nehéz megtalálni a közös szegmensek több értékek vannak az egyes oszlopokban. Használhat [ `autocluster()` ](/azure/kusto/query/autoclusterplugin) azonnal kibontása egy kis közös szegmensek listájához, és keresse meg az érdekes beépülő modul a megnövekedett két percen belül fürtök, ahogyan az a következő lekérdezést:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | Count | Százalék | Régió | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1. | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

Az eredményekből látható, hogy a legtöbb domináns szegmens 65.74 % a teljes kivétel rekordokat tartalmaz, és négy dimenziók megosztja. A következő szegmens sokkal kevésbé gyakori, csak a rekordok 9.67 % tartalmaz, és megosztja háromdimenziós. Akár kevésbé gyakoriak a többi szegmens szerepel. 

Autocluster adatbányászati több dimenzióban, és érdekes szegmensek kinyeréséhez szellemi tulajdont képező algoritmust használ. "Érdekes" azt jelenti, hogy rendelkezik-e mind a rekordokat és a szolgáltatásokat jelentős lefedettsége minden szegmensben. A szegmensek is fürtre, ami azt jelenti, hogy mindegyikhez jelentősen eltér a többi. Egy vagy több ezekben a szegmensekben az RCA-folyamat szempontjából releváns lehet. Szegmens tekintse át és felmérési minimalizálása érdekében autocluster csak kisebb szegmensek listáját adja eredményül.

### <a name="use-basket-for-single-record-set-clustering"></a>Fürtszolgáltatás egyetlen rekordot basket() használata

Is használhatja a [ `basket()` ](/azure/kusto/query/basketplugin) beépülő modult, ahogyan az a következő lekérdezést:

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | Count | Százalék | Régió | ScaleUnit | DeploymentId | Tracepoint | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1. | 642 | 66.0493827160494 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

Kosár a Apriori algoritmust valósít meg, elemet beállítva az adatbányászati, és kinyeri az összes szegmens, amelynek lefedettség beállított rekord egy küszöbértéket (az alapértelmezett érték 5 %) felett van. Láthatja, hogy további szegmensek hasonlóság alapján (a példában, 0, 1 vagy 2,3 szegmensekre) a könyvtárban találhatók.

Mindkét beépülő modulok hatékony és könnyen használható, de azok jelentős korlátozás oka, hogy az azokat a fürt egy egyetlen rekordot felügyeletlen módon (a címkéket). Nem így egyértelmű, hogy a kinyert minták írhatók le a kiválasztott rekordhalmaz (a rendellenes rekordokat) vagy a globális rekordhalmaz.

## <a name="clustering-the-difference-between-two-records-sets"></a>A különbség a két rekordhalmazok fürtszolgáltatás

A [ `diffpatterns()` ](/azure/kusto/query/diffpatternsplugin) beépülő modul a szolgáltatásoknak a overcomes `autocluster` és `basket`. `Diffpatterns` két rekordhalmazok vesz igénybe, és kinyeri a fő szegmensek, amelyek különböző közöttük. Általában a tartalmazza a rendellenes rekordhalmaz vizsgálata folyamatban van egy (az egyik által elemzett `autocluster` és `basket`). A többi tartalmazza a referencia-rekordhalmaz (alapkonfiguráció). 

Az alábbi lekérdezést, a használunk `diffpatterns` érdekes fürtök keresése a megnövekedett két percen belül, amely eltér az alaptervnek fürtök, mint a. Az alapkonfiguráció ablak meghatározzuk, 15:00 nyolc percet (Ha a megnövekedett kezdődött). Is kell-e egy adott rekord az alaptervhez, vagy a rendellenes csoporthoz tartozik-e megadása bináris oszlop (AB) által bővítése. `Diffpatterns` felügyelt tanulási algoritmus, ahol a két osztály címkéket a referenciakonfiguráció jelző (AB) és a rendellenes által generált valósítja meg.

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| SegmentId | CountA | CountB | PercentA | PercentB | PercentDiffAB | Régió | ScaleUnit | DeploymentId | Tracepoint |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1. | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

A legtöbb domináns szegmens pedig az ugyanazon a szegmensen által kinyert `autocluster`, kiterjesztette lefedettségét a kétperces rendellenes ablakban egyben 65.74 %. De kiterjesztette lefedettségét a nyolc perces alapkonfiguráció ablakban csak az 1.7-es %. Az különbség a 64.04 %. Ez a különbség úgy tűnik, hogy a rendellenes kiugrás kapcsolódnak. Ezt a feltételezést azokat a rekordokat, és a többi szegmens, ahogy az alábbi lekérdezést a problémás szegmenshez tartozó az eredeti diagram halmazra ellenőrizheti:

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

!["Diffpattern" szegmens idődiagramját ellenőrzése](media/machine-learning-clustering/validating-diffpattern-timechart.png)

A diagram lehetővé teszi számunkra, hogy a megnövekedett keddi délután az volt az adott szegmens, módszerével észlelt alóli kivételek miatt a `diffpatterns` beépülő modult.

## <a name="summary"></a>Összegzés

Az Azure Data Explorer Machine Learning beépülő modulok hasznosak számos forgatókönyv esetében. A `autocluster` és `basket` felügyeletlen tanulási algoritmus és a könnyen használható megvalósításához. `Diffpatterns` megvalósítja a tanulási algoritmus felügyelt, és bár összetettebb, legyen az RCA differenciálás szegmensek kibontása a hatékonyabb.

Ad hoc forgatókönyvek és a közel valós idejű figyelési szolgáltatásokat automatikus ezek beépülő modulok interaktív módon használhatók. Az Adatkezelőben az Azure time series anomáliadetektálás követ egy diagnosztikai folyamat, amely a szükséges teljesítményt szabványainak való optimalizálhatja.
