---
title: Gépi tanulási képesség az Azure Adatkezelő
description: A gépi tanulási fürtszolgáltatás használata az Azure Adatkezelő a kiváltó okok elemzéséhez.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: fe72031ef9ade7473dc4d5de7e090e92ef2a6843
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769931"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Gépi tanulási képesség az Azure Adatkezelő

Az Azure Adatkezelő egy Big adatelemzési platform, amely a szolgáltatás állapotának, a QoS-nek vagy a rendellenes viselkedésű eszközöknek a beépített [anomália-észlelési és előrejelzési](/azure/data-explorer/anomaly-detection) függvények használatával történő figyelésére szolgál. A rendellenes minta észlelése után a rendszer kiváltó okok elemzését (RCA) hajtja végre az anomália enyhítése vagy feloldása érdekében.

A diagnosztikai folyamat összetett és hosszadalmas, és tartományi szakértők által végezhető el. A folyamat magában foglalja a különböző forrásokból származó további adatok egyidejű beolvasását és összekapcsolását, a több dimenzióban lévő értékek eloszlásának változásait, a további változók ábrázolását, valamint a tartományi tudásbázison alapuló egyéb technikákat. intuíció. Mivel ezek a diagnosztikai forgatókönyvek az Azure-Adatkezelő gyakoriak, a gépi tanulási beépülő modulok megkönnyítik a diagnosztikai fázist, és lerövidítik az RCA időtartamát.

Az Azure Adatkezelő három Machine Learning beépülő modult tartalmaz: [`autocluster`](/azure/kusto/query/autoclusterplugin), [`basket`](/azure/kusto/query/basketplugin)és [`diffpatterns`](/azure/kusto/query/diffpatternsplugin). Minden beépülő modul implementálja a fürtszolgáltatási algoritmusokat. A `autocluster` és `basket` beépülő modul egyetlen rekordhalmazsal és a `diffpatterns` beépülő modullal csoportosítja a két rekordhalmaz közötti különbségeket.

## <a name="clustering-a-single-record-set"></a>Egyetlen rekordazonosító fürtözése

Egy gyakori forgatókönyv olyan adathalmazt tartalmaz, amelyet egy adott feltétel választ ki, például az időablakot, amely rendellenes viselkedést, magas hőmérsékletű eszközök olvasását, hosszú időtartamú parancsokat és legjelentősebb kiadási felhasználókat mutat be. Szeretnénk egyszerűen és gyorsan megkeresni az adathalmazok közös mintáit (szegmenseit). A minták az adathalmazok egy részhalmaza, amelynek rekordjai ugyanazokat az értékeket használják, mint több dimenzió (kategorikus oszlop). A következő lekérdezés egy idősorozatot hoz létre a szolgáltatási kivételek közül, és megjeleníti a tíz perces raktárhelyeken:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPsaoCQQyF4d6nCFa7oHCtZd9B0F6G8ajByWTJZHS5+PDOgpVgYRn485EkOAnno9NAriWGFKw7QfQYUy0O43zZ0JNKFQnG/5jrbmeIXHBgwd6DjH2/JVqk2QrTL1aYvlifa4tni29YlzaiUK4yRK3Zu54006dBZ1N5/+X6PqpRI23+pFGGfIKRtz5egzk92K+dsycMyz3szhGEKWJ01lxI760O9ABuq0bMcvV2hqFoqnOz7F9BdSHlSgEAAA==) **\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Szolgáltatási kivételek idődiagramját](media/machine-learning-clustering/service-exceptions-timechart.png)

A szolgáltatási kivételek száma összefügg a szolgáltatás általános forgalmával. A napi mintát a hétfőtől péntekig megjelenő munkanapokon láthatja, és a szolgáltatási kivételek a középső nap folyamán növekednek, és az éjszaka folyamán csökken. Az alacsony számok a hétvégén láthatók. Az Azure Adatkezelőban a [Time Series anomália észlelésével](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) észlelhetők a kivételek.

Az adatsorok második csúcsa kedd délután történik. A következő lekérdezés a tüske további diagnosztizálására szolgál. A lekérdezés segítségével állítsa át a diagramot a csúcs körül a magasabb felbontásban (egyperces raktárhelyeken nyolc órán belül) annak ellenőrzéséhez, hogy az éles szeg-e, és megtekintse a szegélyeit.

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAyXNwQrCMBAE0Hu/YvHUooWkghSl/yDoyUsJyWpCk2xJNnjx403pbeYwbzwyBBdnnoxiZBewHYS89GLshzNIeRWiuzUGA83al8yYXPzI5gdBLdjnWjFDLGHSVCK3HVCEe0LtMj4r9mAVVngnCvsLMO3hOFqo2goyVCxhNJhgu9dWJYavY9uyY4/T4UV1XVm2CEM0kFe34AnkBhXGOs7kCzuKh+4P3/XM5M8AAAA=) **\]**

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Hangsúly a Spike idődiagramját](media/machine-learning-clustering/focus-spike-timechart.png)

A 15:00 és 15:02 közötti keskeny kétpercenkénti tüske látható. A következő lekérdezésben az ebben a két perces ablakban szereplő kivételek száma:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVHIzcyLL0hNzI4vsU1JLEktycxN1TAyMDTTNbDQNTJWMDS1MjDQtObKASlNrCCk1AioNCU1Nz8+Oae0uCS1KDMv3ZCrRqE8I7UoVSGgKDU5szg1BKgvuCQxt0AhKbWkPDU1TwPhBj09hCWaQI3J+aV5JQACnQoRpwAAAA==) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Mennyiség |
|---------|
|972    |

A következő lekérdezésben, a 972-as számú kivétel:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4XOsQrCMBSF4b1Pccd2aLmJKKL4DoLu4doeNDSJJb1SBx/eOHV0/37OCVCKPrkJMjo9DaJQH1FbNruW963dkNkemJtjFX5U3v+oLXRAfLo+vGZF9uluqg8tD2TQOaP3M66lu6jEiW7QBUj1+qHr1pGmhCojyPIX7QHvzakAAAA=) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | Region (Régió) | ScaleUnit | DeploymentId                     | Tracepoint | ServiceHost                          |
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

### <a name="use-autocluster-for-single-record-set-clustering"></a>Az autocluster () használata egyetlen erőforrásrekord-fürtözéshez

Bár kevesebb mint ezer kivétel van, még mindig nehéz megkeresni a közös szegmenseket, mivel az egyes oszlopokban több érték is van. A [`autocluster()`](/azure/kusto/query/autoclusterplugin) beépülő modullal azonnal kinyerheti a közös szegmensek kisméretű listáját, és megkeresheti a Spike két percén belüli érdekes fürtöket az alábbi lekérdezésben látható módon:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQrCMBRF937FG5OhJYkoovQfBN1DbC8aTNqSvlgHP94IQkf3c+65AUzRD3aCe1hue8dgHyGM0rta7WuzIb09KCWPVfii7vUPNQXtEUfbhTwzkh9uunrTckcCnRI6P+NSvDO7ONEVvACDWD80zRqRRcTThVxa5DKPv00hP81KL1+4AAAA) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | Mennyiség | Százalék | Region (Régió) | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | Nyugat | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

A fenti eredményekből megtudhatja, hogy a legjelentősebb szegmens a teljes kivételek 65,74%-át és négy dimenziót oszt meg. A következő szegmens sokkal kevésbé gyakori, csak a rekordok 9,67%-át és három dimenziót oszt meg. A többi szegmens még kevésbé gyakori. 

Az autocluster egy szabadalmaztatott algoritmust használ több dimenzió kinyeréséhez és érdekes szegmensek kinyeréséhez. Az "érdekes" érték azt jelenti, hogy minden szegmens jelentős lefedettséggel rendelkezik a rekordhalmaz és a beállított szolgáltatások esetében is. A szegmensek is eltérnek, ami azt jelenti, hogy mindegyik jelentősen eltér a többitől. Az RCA-folyamathoz tartozó egy vagy több szegmens is releváns lehet. A szegmensek áttekintésének és értékelésének minimalizálásához az autocluster csak egy kis szegmens listát bont ki.

### <a name="use-basket-for-single-record-set-clustering"></a>A kosár () használata egyetlen rekordazonosító-fürtözéshez

Használhatja a [`basket()`](/azure/kusto/query/basketplugin) beépülő modult is, ahogy az a következő lekérdezésben látható:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQ6CMBgGd57iH9sB0tZojMZ3MNG9KfBFG1og7Y84+PDWidH9LncBTNGPdoYbLF96x2AfIYzSh1oda7MjvT8pJc9V+KHu/Q81Be0RJ9uFJTOSHx+6+tD6RAJdEzqfcS/ejV2cqQWvwCi2h6bZIrKIeLmwlBa1Lg9gIb9KJv2TswAAAA==) **\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | Mennyiség | Százalék | Region (Régió) | ScaleUnit | DeploymentId | Tracepoint | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | Nyugat | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

A kosár megvalósítja a Apriori algoritmust az elemek kinyeréséhez, és kinyeri az összes olyan szegmenst, amelynek lefedettsége meghaladja a küszöbértéket (alapértelmezés szerint 5%). Láthatja, hogy több szegmens lett kinyerve hasonló módon (például 0, 1 vagy 2, 3. szegmens).

Mindkét beépülő modul hatékony és könnyen használható, de jelentős korlátozás szerint egyetlen, nem felügyelt módon üzemelő rekordot (címkék nélkül) is felhasználhat. Ezért nem egyértelmű, hogy a kinyert minták a kiválasztott rekordhalmazt (a rendellenes rekordokat) vagy a globális rekordhalmazt jellemzik-e.

## <a name="clustering-the-difference-between-two-records-sets"></a>Két rekordhalmaz közötti különbség csoportosítása

A [`diffpatterns()`](/azure/kusto/query/diffpatternsplugin) beépülő modul a `autocluster` és a `basket`korlátozását is feldolgozza. `Diffpatterns` két rekordhalmazt vesz fel, és kinyeri az egymástól eltérő fő szegmenseket. Egy készlet általában tartalmazza a vizsgált rendellenes rekordokat (az egyiket `autocluster` és `basket`elemzi). A másik készlet tartalmazza a hivatkozási rekordot (alapterv). 

Az alábbi lekérdezésben a `diffpatterns` segítségével érdekes fürtöket talál a Spike két percén belül, amelyek eltérnek az alapkonfigurációban lévő fürtöktől. Az alapablakot nyolc perccel a 15:00 előtt definiáljuk (amikor a nyárs elindult). Azt is meg kell hosszabbítani egy bináris oszlop (AB) alapján, hogy egy adott rekord az alaptervhez vagy a rendellenes készlethez tartozik-e. `Diffpatterns` megvalósít egy felügyelt tanulási algoritmust, ahol a két osztály címkéit a rendellenesség és az alapkonfiguráció jelzője (AB) hozta létre.

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA42QzU+DQBDF7/wVcwOi5UtrmhJM4OzBRO9kWqbtpssuYacfGv94t0CrxFTd02by5jfvPUkMtVBlQ7gtOauQiUVNXhLFD5NoNknuIJ7Oo8hPHXmS4vEvaXKWWuoCDUmh6Jr8fj79Tv6HfOanEIbwRLgnQFhjAwviA5EC3hCcCYCq6gamEVsC1oB7LfoRt6iMYKEVvGtFQXfeNFKc7mXe2MjNVzl+mARR6lRU63Ipd4apFWodOx9w2FBL4D23tBSGXi3mhbG+OPPGVQTB+ITvg24dGN7vlN5JTxhc+dYAHZls4LzIxGr1k/B4iXcLbq50jfLNtd9i8OB2jD3KnW0dKstokG08Zby8uLbyCfX/tG46AgAA) **\]**

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

| SegmentId | CountA | CountB | Százalék | PercentB | PercentDiffAB | Region (Régió) | ScaleUnit | DeploymentId | Tracepoint |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65,74 | 1,7 | 64,04 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17,18 | 44,16 | 26,97 | scus |  |  |  |
| 2 | 92 | 356 | 9,47 | 28,9 | 19,43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9,26 | 27,27 | 18,01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8,44 | 25,81 | 17,38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5,66 | 20,45 | 14,8 | Nyugat | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5,86 | 16,56 | 10,69 |  |  |  |  |

A legjelentősebb szegmens ugyanaz a szegmens, amelyet `autocluster`kinyert, a két perces rendellenes időszakra vonatkozó lefedettség is 65,74%. A 8 perces alapkonfiguráció azonban csak 1,7%-os lefedettséggel rendelkezik. A különbség 64,04%. Úgy tűnik, ez a különbség a rendellenes csúcshoz kapcsolódik. Ezt a feltételezést úgy ellenőrizheti, hogy az eredeti diagramot a problémás szegmensbe tartozó rekordokra bontja, az alábbi lekérdezésben látható módon:

**\[** [**kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WRsWrDMBCG9zzF4cmGGuJUjh2Ktw7tUkLTzuEsnRNRnRQkuSQlD185yRTo0EWIO913/J8MRWBttxE6iC5INOhzRey20owhktd2V8EZwsiMXv/Q9Dpfe5I60Idm2kTkQ1E8AczMxMLjf1h4/IN1PzY7Ax0jWQWBdomvhyF/p512FroOMsIxA0zdTdpKn1bHSzmMzbX8TAfjTkw2vqpLp69VpYQaatEogXOBsqrbtl5WDake6yabXWjkv7WkFxeuPGqG5VzWqhQrIUqx6B/L1WKB6aBViy01imT2ANnau94QT9c35xlNVqQAjF9UhpSHAtiRO+lGG/MCUoZ7CTB4x7ePie5mNbk4QDVn6E+ThUT0SQh5iGlM7tHHX4WFgLHOAQAA) **\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![A "diffpattern" szegmens idődiagramját ellenőrzése](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Ez a diagram lehetővé teszi számunkra, hogy kedd délután megtekintse a nyársat az adott szegmens kivételei miatt, amely a `diffpatterns` beépülő modullal lett felderítve.

## <a name="summary"></a>Összefoglalás

Az Azure Adatkezelő Machine Learning beépülő modulok számos forgatókönyv esetén hasznosak lehetnek. A `autocluster` és `basket` nem felügyelt tanulási algoritmust implementál, és könnyen használható. `Diffpatterns` megvalósítja a felügyelt tanulási algoritmust, és bár összetettebb, a differenciált elemek az RCA-ben való kinyerése erősebb.

Ezek a beépülő modulok interaktív módon, alkalmi forgatókönyvekben és automatikus, közel valós idejű figyelési szolgáltatásokban használatosak. Az Azure Adatkezelő-ban a Time Series anomália észlelését egy olyan diagnosztikai folyamat követi, amely a szükséges teljesítményi követelmények kielégítése érdekében nagyon optimalizált.
