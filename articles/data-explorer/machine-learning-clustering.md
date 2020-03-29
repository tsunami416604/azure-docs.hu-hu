---
title: Gépi tanulási képesség az Azure Data Explorerben
description: Használja a gépi tanulási fürtözés tananyagelemzés az Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: fe72031ef9ade7473dc4d5de7e090e92ef2a6843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769931"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Gépi tanulási képesség az Azure Data Explorerben

Az Azure Data Explorer, egy Big Data-elemzési platform, a szolgáltatás állapotának, a QoS-nak vagy a hibás anamális viselkedésnek a figyelésére szolgál a beépített [anomáliaészlelési és-előrejelzési](/azure/data-explorer/anomaly-detection) funkciók használatával. A rendellenes minta észlelése után a rendszer gyökérok elemzését (RCA) hajtja végre az anomália csökkentése vagy feloldása érdekében.

A diagnosztikai folyamat összetett és hosszadalmas, és a domain szakértők végzik. A folyamat magában foglalja a különböző forrásokból származó további adatok beolvasását és összekapcsolását ugyanarra az időkeretre vonatkozóan, az értékek több dimenzióra történő eloszlásában bekövetkező változások keresését, további változók ábrázolását és a domain tudáson alapuló egyéb technikákat, valamint Intuíció. Mivel ezek a diagnosztikai forgatókönyvek gyakoriak az Azure Data Explorerben, a gépi tanulási bővítmények elérhetők a diagnosztikai fázis megkönnyítése és az RCA időtartamának lerövidítése érdekében.

Az Azure Data Explorer három [`autocluster`](/azure/kusto/query/autoclusterplugin)Machine [`basket`](/azure/kusto/query/basketplugin)Learning-beépülő modult is beszerzett: , , és [`diffpatterns`](/azure/kusto/query/diffpatternsplugin)a. Minden bővítmény fürtözési algoritmusokat valósít meg. A `autocluster` `basket` és a dugó fürt egyetlen `diffpatterns` rekordkészlet, és a plugin klaszterek közötti különbségek két rekordhalmazok.

## <a name="clustering-a-single-record-set"></a>Egyetlen rekordkészlet fürtözése

A gyakori forgatókönyv egy adott feltétel által kiválasztott adatkészletet tartalmaz, például az időablakot, amely rendellenes viselkedést mutat, magas hőmérsékletű eszközértékeket, hosszú időtartamú parancsokat és a legnépszerűbb költési felhasználókat. Szeretnénk egy egyszerű és gyors módja annak, hogy megtalálja a közös minták (szegmensek) az adatokban. A minták az adatkészlet egy olyan részhalmaza, amelynek rekordjai több dimenzióban (kategorikus oszlopok) azonos értékekkel rendelkeznek. A következő lekérdezés egy héten keresztül a szolgáltatáskivételek idősorozatát építi fel és jeleníti meg tízperces raktárhelyeken:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPsaoCQQyF4d6nCFa7oHCtZd9B0F6G8ajByWTJZHS5+PDOgpVgYRn485EkOAnno9NAriWGFKw7QfQYUy0O43zZ0JNKFQnG/5jrbmeIXHBgwd6DjH2/JVqk2QrTL1aYvlifa4tni29YlzaiUK4yRK3Zu54006dBZ1N5/+X6PqpRI23+pFGGfIKRtz5egzk92K+dsycMyz3szhGEKWJ01lxI760O9ABuq0bMcvV2hqFoqnOz7F9BdSHlSgEAAA==)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![Szolgáltatáskivételek idődiagramja](media/machine-learning-clustering/service-exceptions-timechart.png)

A szolgáltatás kivételszáma korrelál a teljes szolgáltatásforgalom. A hétfőtől péntekig tartó munkanapokon jól látható a napi minta, a szolgáltatási kivételek növekedése a nap közepén számít, és az éjszakai számlálások száma csökken. Lapos alacsony számok láthatók a hétvégén. Kivétel kiugrások az Azure Data Explorer [idősorozat-anomáliadetektálás](/azure/data-explorer/anomaly-detection?#time-series-anomaly-detection) használatával észlelhető.

Az adatok második csúcsa kedden délután következik be. A következő lekérdezés a csúcs további diagnosztizálására szolgál. A lekérdezés segítségével újrarajzolhatja a diagramot a nagyobb felbontású (nyolc órás egyperces raktárhelyeken) körüli diagramkörül, hogy ellenőrizze, éles-e, és tekintse meg a szegélyeket.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAyXNwQrCMBAE0Hu/YvHUooWkghSl/yDoyUsJyWpCk2xJNnjx403pbeYwbzwyBBdnnoxiZBewHYS89GLshzNIeRWiuzUGA83al8yYXPzI5gdBLdjnWjFDLGHSVCK3HVCEe0LtMj4r9mAVVngnCvsLMO3hOFqo2goyVCxhNJhgu9dWJYavY9uyY4/T4UV1XVm2CEM0kFe34AnkBhXGOs7kCzuKh+4P3/XM5M8AAAA=)**\]**

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![Fókuszban a tüske idődiagramja](media/machine-learning-clustering/focus-spike-timechart.png)

Látunk egy szűk kétperces csúcsot 15:00 és 15:02 között. A következő lekérdezésben számolja meg a kivételeket ebben a kétperces ablakban:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVHIzcyLL0hNzI4vsU1JLEktycxN1TAyMDTTNbDQNTJWMDS1MjDQtObKASlNrCCk1AioNCU1Nz8+Oae0uCS1KDMv3ZCrRqE8I7UoVSGgKDU5szg1BKgvuCQxt0AhKbWkPDU1TwPhBj09hCWaQI3J+aV5JQACnQoRpwAAAA==)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|Darabszám |
|---------|
|972    |

A következő lekérdezésben a 972-ből 20 kivételt kell kivenni:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4XOsQrCMBSF4b1Pccd2aLmJKKL4DoLu4doeNDSJJb1SBx/eOHV0/37OCVCKPrkJMjo9DaJQH1FbNruW963dkNkemJtjFX5U3v+oLXRAfLo+vGZF9uluqg8tD2TQOaP3M66lu6jEiW7QBUj1+qHr1pGmhCojyPIX7QHvzakAAAA=)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp (Pontos Időbélyeg)            | Régió | Léptékegység | Telepítési azonosító                     | Tracepoint (Nyompont) | Servicehost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scu   | su5 (magyarul: su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scu   | su5 (magyarul: su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scu   | su5 (magyarul: su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scu   | su5 (magyarul: su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scu   | su5 (magyarul: su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scu   | su5 (magyarul: su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2 között   | su2 (magyarul: su2       | 89e2f62a73bb4efd8f545ae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncu   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scu   | su3 (magyarul: su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scu   | su3 (magyarul: su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scu   | su5 (magyarul: su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scu   | su3 (magyarul: su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scu   | su3 (magyarul: su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncu   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scu   | su5 (magyarul: su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scu   | su5 (magyarul: su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scu   | su5 (magyarul: su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2 között   | su2 (magyarul: su2       | 89e2f62a73bb4efd8f545ae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scu   | su5 (magyarul: su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scu   | su3 (magyarul: su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>Autocluster() használata egyrekordkészletes fürtözéshez

Annak ellenére, hogy kevesebb, mint ezer kivétel van, még mindig nehéz közös szegmenseket találni, mivel minden oszlopban több érték van. Használhatja [`autocluster()`](/azure/kusto/query/autoclusterplugin) plugin azonnal kivonat egy kis listát a közös szegmensek, és keresse meg az érdekes klaszterek a tüske két perc alatt látható a következő lekérdezés:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQrCMBRF937FG5OhJYkoovQfBN1DbC8aTNqSvlgHP94IQkf3c+65AUzRD3aCe1hue8dgHyGM0rta7WuzIb09KCWPVfii7vUPNQXtEUfbhTwzkh9uunrTckcCnRI6P+NSvDO7ONEVvACDWD80zRqRRcTThVxa5DKPv00hP81KL1+4AAAA)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| Szegmentált azonosító | Darabszám | Százalék | Régió | Léptékegység | Telepítési azonosító | Servicehost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scu | su5 (magyarul: su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncu | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scu | su3 (magyarul: su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | Nyeu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

A fenti eredményekből látható, hogy a legdominánsabb szegmens a teljes kivételrekord 65,74%-át tartalmazza, és négy dimenziót oszt meg. A következő szegmens sokkal kevésbé gyakori, csak a rekordok 9,67%-át tartalmazza, és három dimenzióval rendelkezik. A többi szegmens még kevésbé gyakori. 

Az Autocluster egy szabadalmaztatott algoritmust használ több dimenzió bányászatához és érdekes szegmensek kitermeléséhez. Az "érdekes" azt jelenti, hogy minden szegmens jelentős lefedettséggel rendelkezik mind a rekordok, mind a funkciók számára. A szegmensek is eltérnek egymástól, ami azt jelenti, hogy mindegyik jelentősen különbözik a többitől. Egy vagy több ilyen szegmens releváns lehet az RCA-folyamat szempontjából. A szegmensek áttekintésének és értékelésének minimalizálása érdekében az autocluster csak egy kis szegmenslistát von ki.

### <a name="use-basket-for-single-record-set-clustering"></a>Kosár() használata egyrekordkészletes fürtözéshez

A bővítményt [`basket()`](/azure/kusto/query/basketplugin) a következő lekérdezésben látható módon is használhatja:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQ6CMBgGd57iH9sB0tZojMZ3MNG9KfBFG1og7Y84+PDWidH9LncBTNGPdoYbLF96x2AfIYzSh1oda7MjvT8pJc9V+KHu/Q81Be0RJ9uFJTOSHx+6+tD6RAJdEzqfcS/ejV2cqQWvwCi2h6bZIrKIeLmwlBa1Lg9gIb9KJv2TswAAAA==)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| Szegmentált azonosító | Darabszám | Százalék | Régió | Léptékegység | Telepítési azonosító | Tracepoint (Nyompont) | Servicehost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scu | su5 (magyarul: su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncu | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scu | su3 (magyarul: su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scu |  |  |  |  |
| 9 | 55 | 5.65843621399177 | Nyeu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

A kosár megvalósítja az Apriori algoritmust a cikkkészlet bányászatához, és kinyeri az összes olyan szegmenst, amelynek a rekordkészlet lefedettsége meghaladja a küszöbértéket (alapértelmezett 5%). Láthatja, hogy több szegmenst vontak ki hasonlókkal (például 0,1 vagy 2,3 szegmens).

Mindkét plugin erős és könnyen használható, de jelentős korlátozás, hogy a klaszter egyetlen rekordot meg felügyelet nélkül (címkék nélkül). Ezért nem világos, hogy a kinyert minták jellemzik a kiválasztott rekordkészletet (a rendellenes rekordokat) vagy a globális rekordkészletet.

## <a name="clustering-the-difference-between-two-records-sets"></a>Két rekordhalmaz közötti különbség fürtözése

A [`diffpatterns()`](/azure/kusto/query/diffpatternsplugin) plugin legyőzi `autocluster` a `basket`korlátozás, és . `Diffpatterns`két rekordhalmazt vesz fel, és kinyeri a közöttük eltérő fő szegmenseket. Az egyik készlet általában tartalmazza a vizsgált rendellenes rekordkészletet (az egyiket elemezte `autocluster` és `basket`a). A másik készlet a referenciarekord-készletet (alapterv) tartalmazza. 

Az alábbi lekérdezésben `diffpatterns` érdekes fürtöket keresünk a tüske két percen belül, amelyek eltérnek az alapkonfiguráción belüli fürtöktől. Az alapvonalablakot a 15:00 előtti nyolc percként határoztuk meg (amikor a tüske elkezdődött). Ki kell terjesztenünk egy bináris oszlopot (AB), amely meghatározza, hogy egy adott rekord az alaptervhez vagy a rendellenes halmazhoz tartozik-e. `Diffpatterns`felügyelt tanulási algoritmust alkalmaz, ahol a két osztálycímkét a rendellenes és az alapvonaljelző (AB) hozta létre.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA42QzU+DQBDF7/wVcwOi5UtrmhJM4OzBRO9kWqbtpssuYacfGv94t0CrxFTd02by5jfvPUkMtVBlQ7gtOauQiUVNXhLFD5NoNknuIJ7Oo8hPHXmS4vEvaXKWWuoCDUmh6Jr8fj79Tv6HfOanEIbwRLgnQFhjAwviA5EC3hCcCYCq6gamEVsC1oB7LfoRt6iMYKEVvGtFQXfeNFKc7mXe2MjNVzl+mARR6lRU63Ipd4apFWodOx9w2FBL4D23tBSGXi3mhbG+OPPGVQTB+ITvg24dGN7vlN5JTxhc+dYAHZls4LzIxGr1k/B4iXcLbq50jfLNtd9i8OB2jD3KnW0dKstokG08Zby8uLbyCfX/tG46AgAA)**\]**

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

| Szegmentált azonosító | CountA | B. gróf | SzázalékA | B százalék | Percentdiffab között | Régió | Léptékegység | Telepítési azonosító | Tracepoint (Nyompont) |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | Eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scu |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncu | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | Nyeu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

A legdominánsabb szegmens ugyanaz a szegmens, amelyet `autocluster`kitermelt , a lefedettség a kétperces rendellenes ablakis 65,74%. De a lefedettség a nyolc perces alapvonal ablak csak 1,7%. A különbség 64,04%. Ez a különbség úgy tűnik, hogy kapcsolódik a rendellenes tüske. Ezt a feltételezést úgy ellenőrizheti, hogy az eredeti diagramot az e problémás szegmenshez tartozó rekordokra osztja fel, szemben az alábbi lekérdezésben látható többi szegmenssel:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WRsWrDMBCG9zzF4cmGGuJUjh2Ktw7tUkLTzuEsnRNRnRQkuSQlD185yRTo0EWIO913/J8MRWBttxE6iC5INOhzRey20owhktd2V8EZwsiMXv/Q9Dpfe5I60Idm2kTkQ1E8AczMxMLjf1h4/IN1PzY7Ax0jWQWBdomvhyF/p512FroOMsIxA0zdTdpKn1bHSzmMzbX8TAfjTkw2vqpLp69VpYQaatEogXOBsqrbtl5WDake6yabXWjkv7WkFxeuPGqG5VzWqhQrIUqx6B/L1WKB6aBViy01imT2ANnau94QT9c35xlNVqQAjF9UhpSHAtiRO+lGG/MCUoZ7CTB4x7ePie5mNbk4QDVn6E+ThUT0SQh5iGlM7tHHX4WFgLHOAQAA)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![A "diffpattern" szegmens időalapjának ellenőrzése](media/machine-learning-clustering/validating-diffpattern-timechart.png)

Ez a diagram lehetővé teszi számunkra, hogy a tüske kedden délután volt, mert a kivételek ebből a szegmensből, felfedezett segítségével a `diffpatterns` plugin.

## <a name="summary"></a>Összefoglalás

Az Azure Data Explorer Machine Learning beépülő modulok számos forgatókönyv esetén hasznosak. A `autocluster` `basket` felügyelet nélküli tanulási algoritmus és könnyen használható. `Diffpatterns`felügyelt tanulási algoritmust valósít meg, és bár összetettebb, de hatékonyabb az RCA differenciálószegmenseinek kinyerésében.

Ezeket a bővítményeket interaktívan használják ad-hoc forgatókönyvekben és az automatikus, közel valós idejű felügyeleti szolgáltatásokban. Az Azure Data Explorerben az idősorozat-anomáliadetektálást egy diagnosztikai folyamat követi, amely nagymértékben optimalizált a szükséges teljesítményszabványoknak való megfeleléshez.
