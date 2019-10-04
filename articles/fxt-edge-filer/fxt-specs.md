---
title: A Microsoft Azure FXT Edge Filer specifikációk |} A Microsoft Docs
description: Fizikai és környezeti specifikációk Azure FXT Edge Filer hardver
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 0679bce8eae515aa6b90e34fcfd15ee9b4e56b31
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542889"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Az Azure FXT Edge Filer specifikációk

Ez a cikk ismerteti a hardverkövetelményeket, az Azure FXT Edge Filer hardver csomópont. A gyakorlatban három vagy több csomópont együtt vannak konfigurálva a fürtözött gyorsítótár rendszer biztosít.

## <a name="hardware-specifications"></a>Hardverspecifikációk

| Összetevő | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Processzormagok |  16 | 16 |
| DRAM  | 1536 GB | 768 GB |
| Hálózati portok | 6 x 25/10 Gb + 2 darab 1 Gb | 6 x 25/10 Gb + 2 darab 1 Gb |
| NVMe SSD kapacitása | 25.6 TB | 12.8 TB |

## <a name="drive-specifications"></a>Meghajtó specifikációk

A rendszer tíz meghajtó üzemanyagtartály, az első elérhető rendelkezik. Minden egyes feltöltött meghajtó a kapacitási adatainak a jobb oldali címkéje. 

Meghajtó számok nyomtatott meghajtók közötti távolság. Az Azure FXT Edge szűrőlista 0 meghajtó meghajtó bal felső, és közvetlenül alatta a meghajtó 1 az.

![fénykép rekeszes a FXT váz, számokat és a kapacitás felirat megjelenítése a meghajtó a egy merevlemez](media/fxt-drives-photo.png)

| Meghajtó számok    |  Használat   |  Specifikációk |
|------------------|--------|-----------------|
| 0, 1             | OS     | 480 GB SATA SSD |
| 2, 3, 4, 5, 6, 7, 8, 9 | Adatok   | FXT 6600: 3.2-es TB NVMe SSD <br> FXT 6400: 1.6-os TB NVMe SSD |


## <a name="dimensions-and-weight"></a>Dimenziók és a súlyozást

Az Azure FXT Edge Filer célja, hogy elférjen standard 19" berendezések állvány és a egy állvány egység magas (1-u). 

<!-- 10x2.5 inches version -->

| Filer dimenziók           |                          |
|-----------------------------|--------------------------|
| Magasság                      | 42.8 mm (1.68 hüvelyk)    |
| Szélesség (beleértve az állvány füleken) | 482.0 mm (18.97 hüvelyk)  |
| Szélesség - fő lemezház      | 434.0 mm (17.08 hüvelyk) |
| Várólistamélység - állvány füleken a fő lemezház oldalán                   | 733.82 mm (29.61 hüvelyk) |
| Várólistamélység - állvány füleken a legtávolabbi hátsó protrusion                 | 772.67 mm (30.42 hüvelyk) |
| Várólistamélység - állvány füleken a legtávolabbi első protrusion bezel nélkül | 22.0 mm (0.87 hüvelyk)  |
| Mélysége – a legtávolabbi első protrusion bezel az állvány füleken    | 35.84 mm (1.41 hüvelyk) |

| Tömeg | |
|-----------------|----------------------|
| Csomópont súly (nélkül csomagolás, a Kellékek nélkül) | 40 lbs (18.1 kg) |
| Nettó súly (nélkül csomagolást, beleértve a) | 51 lbs (23.1 kg)|
| Bruttó súly (mint tartalmazza a szükséges, az összes csomagolással) |  64 lbs (29.0 kg) |

### <a name="shipping-dimensions"></a>A szállítási címhez tartozó dimenziók

| Csomag dimenzió | Milliméterben | Hüvelyk |
|-------------------|-------------|--------|
| Magasság            | 311.2       | 12.25" |
| Szélesség             | 642.8       | 25.31" |
| Hossz            | 1,051.1     | 41.38" |

## <a name="power-and-thermal-specifications"></a>A Power és hőmérsékleti specifikációk

Ez a szakasz power minősítések és az Azure FXT Edge Filer mértékek biztosít.

### <a name="nameplate-ratings"></a>Adattábla minősítések

| Adattábla minősítések FXT 6000 sorozat modellekhez |
|----------------|
| 100 - 240V~    |
| 10A - 5A (X2)  |
| 50 / 60Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Teljesítmény és a hőmérséklet mértékegysége 

Azure FXT Edge Filer csomópontok változó sebesség ventilátorok, használ, így a power hőmérséklet és a terhelés függ. Maximális Ventilátor sebessége nagy terhelés és emelt szintű környezeti hőmérséklet egyes kombinációi címen érhető el. 

Ezekbe a diagramokba lehetővé teszik energiafogyasztását és hőmérsékleti kimeneti mérések kombinációk gyakran használt feszültség-gyakorisága. 

| FXT 6600 power történik <br />(22° C, 71.6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Feszültségérzékelő (V) | 100 | 120 | 208 | 230 | 240 | 
| Gyakoriság (Hz) | 60 | 60 | 60 | 50 | 50 |
| Jelenlegi (A) | 5.02 | 4.16 |2.40 | 2.20 | 2.16 |
| Apparent Power (VA) | 502 | 499 | 499 | 506 | 518|
| Teljesítménytényező | 0.99 | 0.99 |0.98 | 0.98 | 0.98 |
| Valódi Power (W) | 497 |494 | 489 | 496 | 508 |
| Hőmérsékleti megoszlás (BTU/óra) |1696 | 1686 | 1669 | 1692 | 1733 |

| FXT 6600 power maximális ventilátor sebességek esetében | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Feszültségérzékelő (V) | 100 |120 | 208 | 230 | 240| 
| Gyakoriság (Hz) | 60 | 60 | 60 | 50 | 50 |
| Jelenlegi (A) | 5.98 | 5.01 | 2.81 | 2.55 | 2.48 |
| Apparent Power (VA) | 598 | 601 | 584 | 587 | 595 |
| Teljesítménytényező | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Valódi Power (W) | 592 | 595 | 573 | 575 | 583 |
| Hőmérsékleti megoszlás (BTU/óra) | 2020 |2031 | 1954 | 1961 | 1990 |

| FXT 6400 power történik <br />(22° C, 71.6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Feszültségérzékelő (V) | 100 | 120 | 208 | 230 | 240 |
| Gyakoriság (Hz) |60 | 60 | 60 | 50 | 50 |
| Jelenlegi (A) | 4.63 | 3.86 | 2.24 | 2.04 | 1.94 |
| Apparent Power (VA) | 463 | 463 | 466 | 469 | 466 |
| Teljesítménytényező | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 | 
| Valódi Power (W) | 458 | 459 | 457 | 460 | 456 |
| Hőmérsékleti megoszlás (BTU/óra) | 1564 | 1565 | 1558 | 1569 | 1557 |

| FXT 6400 power maximális ventilátor sebességek esetében | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Feszültségérzékelő (V) | 100 | 120 | 208 | 230 | 240 |
| Gyakoriság (Hz) | 60 | 60 | 60 | 50 | 50 |
| Jelenlegi (A) | 5.15 | 4.28 | 2.48 | 2.28 | 2.13 |
| Apparent Power (VA) | 515 | 514 | 516 | 524 | 511 |
| Teljesítménytényező | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Valódi Power (W) | 510 | 508 | 506 | 514 | 501 |
| Hőmérsékleti megoszlás (BTU/óra) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Környezeti követelmények

Ez a szakasz környezeti Hardverkörnyezet specifikációk biztosít.

### <a name="temperature-and-humidity"></a>Hőmérséklettel és páratartalommal kapcsolatos

| Környezeti attribútum   | Üzemi tartomány                   | A nem működő tartomány         |
|---------------------------|-----------------------------------|-----------------------------|
| Környezeti hőmérséklet-tartomány | 10 C-35 C (50-86° F)          | Tartsuk ott-40 C-65 C (tartsuk ott-40-149 ° F) |
| Környezeti relatív páratartalom | 80 %-os 10 %-os nem kicsapódó          | 5% - 95% non-condensing     |
| Maximális harmatpontja         | 29°C (84°F)                       | 33°C (91°F)                 |
| Altitude                  | vonatkoznak hőmérséklet megszüntetéséhez minősítés 3048 mérőszámok (10 000 feet), akár az alábbi esetekben | az érték legfeljebb 12 000 (39,370 feet) |

> [!NOTE] 
> **Magasság hőmérséklet megszüntetéséhez minősítés:** Maximális hőmérséklet csökken által 1 C/300 m (1° F/547 szerint) 950 m (3,117 szerint).

### <a name="airflow-shock-and-vibration"></a>Légmozgás megváltani és rezgés 

| Attribútum         | Specifikáció |
|-------------------|---------------|
| Légmozgás                    | Rendszer légmozgás hátsó hosszirányú. Rendszer alacsony nyomású hátsó kipufogógáz telepítést kell működtetni. |
| Megváltani, a üzemeltetési         | 6 G-11 ezredmásodperc (6 tájolások tesztelése) |
| Nem működő megváltani     | 71 G-2 ezredmásodperc (6 tájolások tesztelése) |
| Rezgés, a üzemeltetési     | 0.26 G<sub>RMS</sub> 5 Hz és 350 Hz véletlenszerű         |
| Nem működő rezgés | 1,88 G<sub>RMS</sub> 10 Hz és 500 Hz 15 percig (minden hat oldalról, tesztelt)  |

## <a name="safety-regulation-compliance"></a>Biztonsági rendelet megfelelőség 

Az Azure FXT Edge Filer megfelel-e a listán szereplő szabályzat az. 

| Category       | Szabályozási specification | 
|----------------|--------------------------|
| Általános biztonsági | EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 <br>EN 62311:2008 | 
| EMC            | FCC A, ICES-003  <br>EN 55032:2012 / CISPR 32:2012  <br>EN 55032:2015 / CISPR 32:2015  <br>EN 55024:2010 + A1:2015 / CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014 / IEC 61000-3-2:2014 (D osztály)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energia         | A Bizottság (EU) mezőben. 617/2013  |
| RoHS           |    EN 50581:2012   |