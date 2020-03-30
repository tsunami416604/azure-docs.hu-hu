---
title: A Microsoft Azure FXT Edge Filer specifikációi | Microsoft dokumentumok
description: Az Azure FXT Edge Filer hardverének fizikai és környezeti specifikációi
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: c06b0c79e01257eebf566b9752269cb88c072d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264725"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Az Azure FXT Edge Filer specifikációi

Ez a cikk ismerteti az Azure FXT Edge Filer hardvercsomópontjainak hardverspecifikációit. A gyakorlatban három vagy több csomópont van együtt konfigurálva a fürtözött gyorsítótár-rendszer biztosításához.

## <a name="hardware-specifications"></a>Hardverspecifikációk

| Összetevő | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Processzormagok |  16 | 16 |
| Dram  | 1536 GB | 768 GB |
| Hálózati portok | 6 x 25/10 Gb + 2 x 1 Gb | 6 x 25/10 Gb + 2 x 1 Gb |
| NVMe SSD kapacitás | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>A meghajtó specifikációi

A rendszer tíz meghajtó rekeszek, megközelíthető elölről. Minden feltöltött meghajtó a jobb oldalon kapacitásadatokkal van ellátva. 

A meghajtószámok a meghajtók közötti helyre kerülnek. Az Azure FXT Edge Filer,0 meghajtó a bal felső meghajtó, és az 1-es meghajtó közvetlenül alatta.

![egy merevlemez-rekesz fényképe az FXT alvázban, a meghajtószámokkal és a kapacitáscímkékkel](media/fxt-drives-photo.png)

| Meghajtószámok    |  Használat   |  Specifikációk |
|------------------|--------|-----------------|
| 0, 1             | Operációs rendszer     | 480 GB SATA SSD |
| 2, 3, 4, 5, 6, 7, 8, 9 | Adatok   | FXT 6600: 3,2 TB NVMe SSD <br> FXT 6400: 1,6 TB NVMe SSD |


## <a name="dimensions-and-weight"></a>Méretek és súly

Az Azure FXT Edge Filer úgy lett kialakítva, hogy elférjen egy szabványos 19"-es berendezéstartóban, és egy állványegység magas (1U). 

<!-- 10x2.5 inches version -->

| Filer méretek           |                          |
|-----------------------------|--------------------------|
| Height (Magasság)                      | 42,8 mm (1,68 hüvelyk)    |
| Szélesség (a fülekkel együtt) | 482,0 mm (18,97 hüvelyk)  |
| Szélesség - fő burkolat      | 434,0 mm (17,08 hüvelyk) |
| Mélység - állványfülek a fő ház hátára                   | 733,82 mm (29,61 hüvelyk) |
| Mélység - állvány fülek a legtávolabbi hátsó kiemelkedéshez                 | 772,67 mm (30,42 hüvelyk) |
| Mélység - rack fülek a legtávolabbi első kiemelkedés, keret nélkül | 22,0 mm (0,87 col)  |
| Mélység - rack fülek a legtávolabbi elülső kiemelkedés, kerettel    | 35,84 mm (1,41 hüvelyk) |

| Tömeg | |
|-----------------|----------------------|
| Csomópont súlya (csomagolás nélkül, tartozékok nélkül) | 40 font (18,1 kg) |
| Nettó tömeg (csomagolás nélkül, beleértve a tartozékokat is) | 51 font (23,1 kg)|
| Bruttó tömeg (szállításkor, beleértve az összes csomagolást) |  64 font (29,0 kg) |

### <a name="shipping-dimensions"></a>Szállítási dimenziók

| Csomagdimenzió | Milliméter | Hüvelyk |
|-------------------|-------------|--------|
| Height (Magasság)            | 311.2       | 12.25" |
| Szélesség             | 642.8       | 25.31" |
| Hossz            | 1,051.1     | 41.38" |

## <a name="power-and-thermal-specifications"></a>Teljesítmény- és hőspecifikációk

Ez a szakasz az Azure FXT Edge Filer teljesítményminősítéseket és méréseket biztosít.

### <a name="nameplate-ratings"></a>Névtábla-minősítések

| Az FXT 6000 sorozatú modellek adattáblás besorolása |
|----------------|
| 100 - 240V~    |
| 10A - 5A (X2)  |
| 50/60Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Teljesítmény- és hőmérések 

Az Azure FXT Edge Filer csomópontjai változó sebességű ventilátorokat használnak, így a teljesítmény a hőmérséklettől és a terheléstől függ. A maximális ventilátorsebesség a nagy terhelés és a magas környezeti hőmérséklet bizonyos kombinációinál érhető el. 

Ezek a diagramok energiafogyasztást és hőkimeneti méréseket biztosítanak az általánosan használt feszültség-frekvencia kombinációkhoz. 

| FXT 6600 teljesítmény szobahőmérsékleten <br />(22° C, 71,6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Feszültség (V) | 100 | 120 | 208 | 230 | 240 | 
| Frekvencia (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuális (A) | 5.02 | 4.16 |2.40 | 2.20 | 2.16 |
| Látszólagos teljesítmény (VA) | 502 | 499 | 499 | 506 | 518|
| Teljesítménytényező | 0.99 | 0.99 |0.98 | 0.98 | 0.98 |
| Valódi teljesítmény (W) | 497 |494 | 489 | 496 | 508 |
| Termikus disszipáció (BTU/Hr) |1696 | 1686 | 1669 | 1692 | 1733 |

| FXT 6600 teljesítmény maximális ventilátorsebességmellett | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Feszültség (V) | 100 |120 | 208 | 230 | 240| 
| Frekvencia (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuális (A) | 5.98 | 5.01 | 2.81 | 2.55 | 2.48 |
| Látszólagos teljesítmény (VA) | 598 | 601 | 584 | 587 | 595 |
| Teljesítménytényező | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Valódi teljesítmény (W) | 592 | 595 | 573 | 575 | 583 |
| Termikus disszipáció (BTU/Hr) | 2020 |2031 | 1954 | 1961 | 1990 |

| FXT 6400 teljesítmény szobahőmérsékleten <br />(22° C, 71,6° F) | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz | 
|---------|---|---|---|---|---|
| Feszültség (V) | 100 | 120 | 208 | 230 | 240 |
| Frekvencia (Hz) |60 | 60 | 60 | 50 | 50 |
| Aktuális (A) | 4.63 | 3.86 | 2.24 | 2.04 | 1.94 |
| Látszólagos teljesítmény (VA) | 463 | 463 | 466 | 469 | 466 |
| Teljesítménytényező | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 | 
| Valódi teljesítmény (W) | 458 | 459 | 457 | 460 | 456 |
| Termikus disszipáció (BTU/Hr) | 1564 | 1565 | 1558 | 1569 | 1557 |

| FXT 6400 teljesítmény maximális ventilátorsebességmellett | 100 V, 60 Hz | 120 V, 60 Hz | 208 V, 60 Hz | 230 V, 50 Hz | 240 V, 50 Hz |
|---------|---|---|---|---|---|
| Feszültség (V) | 100 | 120 | 208 | 230 | 240 |
| Frekvencia (Hz) | 60 | 60 | 60 | 50 | 50 |
| Aktuális (A) | 5.15 | 4.28 | 2.48 | 2.28 | 2.13 |
| Látszólagos teljesítmény (VA) | 515 | 514 | 516 | 524 | 511 |
| Teljesítménytényező | 0.99 | 0.99 | 0.98 | 0.98 | 0.98 |
| Valódi teljesítmény (W) | 510 | 508 | 506 | 514 | 501 |
| Termikus disszipáció (BTU/Hr) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Környezetvédelmi követelmények

Ez a rész a hardver környezeti környezetének specifikációit adja meg.

### <a name="temperature-and-humidity"></a>Hőmérséklet és páratartalom

| Környezeti tulajdonság   | Működési tartomány                   | Nem üzemi tartomány         |
|---------------------------|-----------------------------------|-----------------------------|
| Környezeti hőmérséklet-tartomány | 10°C és 35°C között          | -40°C és 65°C között (-40 - 149°F) |
| Környezeti relatív páratartalom | 10% - 80% nem kondenzáló          | 5% - 95% nem kondenzáló     |
| Maximális harmatpont         | 29°C (84°F)                       | 33°C (91°F)                 |
| Magasság                  | akár 3048 méter (10 000 láb), az alábbi hőmérséklet-deminősítésfüggvénye | akár 12 000 méter (39 370 láb) |

> [!NOTE] 
> **A tengerszint feletti magasság irásának deminősítése:** A maximális hőmérséklet 1°C/300 m-rel (3117 láb) magasabb, mint 950 m.- vel.

### <a name="airflow-shock-and-vibration"></a>Légáramlás, ütés és rezgés 

| Attribútum         | Specifikáció |
|-------------------|---------------|
| Légáramlás                    | A rendszer légáramlása elölről hátrafelé halad. A rendszert alacsony nyomású hátsó kipufogógáz-beépítéssel kell működtetni. |
| Sokk, működés         | 6 G 11 milliszekundumra (6 irányban tesztelve) |
| Sokk, nem működő     | 71 G 2 milliszekundumra (6 irányban tesztelve) |
| Vibráció, működési     | 0,26 G<sub>RMS</sub> 5 Hz és 350 Hz között véletlenszerűen         |
| Nem működő rezgés | 1,88 G<sub>RMS</sub> 10 Hz és 500 Hz között 15 percig (mind a hat oldalon tesztelve)  |

## <a name="safety-regulation-compliance"></a>A biztonsági előírásoknak való megfelelés 

Az Azure FXT Edge Filer megfelel a felsorolt előírásoknak. 

| Kategória       | Szabályozási előírás | 
|----------------|--------------------------|
| Általános biztonság | EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 <br>En 62311:2008 | 
| Emc            | FCC A, ICES-003  <br>En 55032:2012/CISPR 32:2012  <br>En 55032:2015/CISPR 32:2015  <br>En 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (D osztály)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energia         | A Bizottság (EU) rendeletszáma 617/2013  |
| Rohs           |    En 50581:2012   |