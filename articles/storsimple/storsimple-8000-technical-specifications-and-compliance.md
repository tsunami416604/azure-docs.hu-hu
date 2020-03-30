---
title: StorSimple műszaki előírások | Microsoft dokumentumok
description: A StorSimple hardverösszetevők műszaki specifikációinak és szabályozási előírásainak megfelelőségi információit ismerteti.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 061194422a8c1bc449dbef0c4f04bb8e1db10dea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965288"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Műszaki előírások és megfelelőség a StorSimple eszközhöz

## <a name="overview"></a>Áttekintés

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

A Microsoft Azure StorSimple eszköz hardverösszetevői megfelelnek a cikkben ismertetett műszaki előírásoknak és szabályozási szabványoknak. A műszaki előírások a táp- és hűtőmodulokat (PCM- ek), a lemezmeghajtókat, a tárolókapacitást és a burkolatokat írják le. A megfelelőségi információk olyan dolgokra vonatkoznak, mint a nemzetközi szabványok, a biztonság és a kibocsátások, valamint a kábelezés.

## <a name="power-and-cooling-module-specifications"></a>Teljesítmény- és hűtőmodul specifikációi

A StorSimple eszköz két 100-240 V-os kétventilátoros, SBB-kompatibilis energiahűtési modullal (PCM) rendelkezik. Ez redundáns energiakonfigurációt biztosít. Ha egy PCM meghibásodik, az eszköz továbbra is megfelelően működik a másik PCM-en, amíg a meghibásodott modult ki nem cserélik.

Az EBOD ház 580 W-os PCM-et használ, az elsődleges ház pedig 764 W-os PCM-et. Az alábbi táblázatok a PCM-ekhez kapcsolódó műszaki előírásokat sorolják fel.

| Specifikáció | 580 W-os PCM (EBOD) | 764 W PCM (elsődleges) |
| --- | --- | --- |
| Maximális kimenő teljesítmény |580 W |764 |
| Frequency |50/60 Hz |50/60 Hz |
| Feszültségtartomány kiválasztása |Automatikus távolságmérés: 90 – 264 V AC, 47/63 Hz |Automatikus távolságmérés: 90- 264 V AC, 47/63 Hz |
| Maximális inrush áram |20 A |20 A |
| Teljesítménytényező korrekciója |>95%-os névleges bemeneti feszültség |>95%-os névleges bemeneti feszültség |
| Harmonikus |Megfelel az EN61000-3-2-nek |Megfelel az EN61000-3-2-nek |
| Kimenet |5V készenléti \@ feszültség 2,0 A |5V készenléti \@ feszültség 2,7 A |
| +5V \@ 42 A |+5V \@ 40 A | |
| +12V \@ 38 A |+12V \@ 38 A | |
| Forró dugaszolható |Igen |Igen |
| Kapcsolók és LED-ek |AC BE/OFF kapcsoló és négy állapotjelző LED |AC BE/OFF kapcsoló és hat állapotjelző LED |
| Ház hűtése |Axiális hűtőventilátorok változtatható ventilátorfordulatszám-szabályozással |Axiális hűtőventilátorok változtatható ventilátorfordulatszám-szabályozással |

## <a name="power-consumption-statistics"></a>Energiafogyasztásra vonatkozó statisztikák

Az alábbi táblázat a StorSimple-eszköz különböző modelljeinek tipikus energiafogyasztási adatait (a tényleges értékek eltérhetnek a közzétetttől).

| Feltételek | 240 v AC | 240 v AC | 240 v AC | 110 v AC | 110 v AC | 110 v AC |
| --- | --- | --- | --- | --- | --- | --- |
|  A ventilátorok lassúak, a meghajtók tétlenek |1.45 A. |0,31 kW |1057,76 BTU/óra |3.19 A. |0,34 kW |1160.13 BTU/óra |
|  A ventilátorok lassúak, a meghajtók |1.54 A |0,33 kW |1126.01 BTU/óra |3.27 A. |0,36 kW |1228.37 BTU/óra |
|  Ventilátorok gyors, meghajtók tétlen, két PSUs powered |2.14 A. |0,49 kW |1671.95 BTU/óra |4,99 A |0,54 kW |1842.56 BTU/óra |
|  Rajongók gyors, meghajtók tétlen, egy TÁPEGYSÉG hajtott egy tétlen |2.05 A |0,48 kW |1637.83 BTU/óra |4.58 A |0,50 kW |1706.07 BTU/óra |
|  Rajongók gyors, meghajtók elérése, két PSUs powered |2.26 A |0,51 kW |1740.19 BTU/óra |4.95 A. |0,54 kW |1842.56 BTU/óra |
|  Rajongók gyors, meghajtók elérése, egy PSU powered egy tétlen |2.14 A. |0,49 kW |1671.95 BTU/óra |4.81 A |0,53 kW |1808.44 BTU/óra |

## <a name="disk-drive-specifications"></a>Lemezmeghajtó specifikációi

A StorSimple eszköz legfeljebb 12 3,5 hüvelykes, soros csatlakoztatott SCSI (SAS) lemezmeghajtót támogat. A tényleges meghajtók lehetnek ssd-meghajtók (SSD-k) vagy merevlemez-meghajtók (HDD-k) keveréke, a termék konfigurációjától függően. A 12 lemezmeghajtó-foglalat 3-4-es konfigurációban található a ház előtt. Az EBOD ház további 12 lemezmeghajtó számára teszi lehetővé a további tárhelyet. Ezek mindig HDD-k.

## <a name="storage-specifications"></a>Tárolási előírások

A StorSimple eszközök a 8100 és a 8600-as és a 8600-as meghajtók vegyesen rendelkeznek merevlemez-meghajtókkal és SSD-meghajtókkal. A 8100 és 8600 teljes felhasználható kapacitása nagyjából 15 TB, illetve 38 TB. Az alábbi táblázat az SSD, a HDD és a felhőkapacitás részleteit tartalmazza a StorSimple-megoldás kapacitásának környezetében.

| Eszköz modell / kapacitás | 8100 | 8600 |
| --- | --- | --- |
| Merevlemez-meghajtók száma (HDD-k) |8 |19 |
| SSD-meghajtók száma |4 |5 |
| Egyetlen HDD kapacitás |4 TB |4 TB |
| Egyetlen SSD-kapacitás |400 GB |800 GB |
| Szabad kapacitás |4 TB |4 TB |
| Használható HDD-kapacitás |14 TB |36 TB |
| Használható SSD-kapacitás |800 GB |2 TB |
| Teljes felhasználható kapacitás* |~ 15 TB |~ 38 TB |
| Maximális megoldáskapacitás (felhővel együtt) |200 TB |500 TB |

<sup>* </sup>- *A teljes felhasználható kapacitás magában foglalja az adatok, metaadatok és pufferek rendelkezésre álló kapacitását. A 8100-as eszközön legfeljebb 8,5 TB-os, a nagyobb 8600-as eszközön akár 22,5 TB-os köteteket is kiépíthet. További információért látogasson el a [StorSimple helyileg rögzített kötetek](storsimple-8000-local-volume-faq.md).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>A tartási terület méretei és súlyspecifikációi

Az alábbi táblázatok a ház különböző méreteit és súlyát sorolják fel.

### <a name="enclosure-dimensions"></a>A ház méretei

Az alábbi táblázat a ház méreteit sorolja fel milliméterben és hüvelykben.

| Kamra | Milliméter | Hüvelyk |
| --- | --- | --- |
| Height (Magasság) |87.9 |3.46 |
| Szélesség a szerelőkarimán keresztül |483 |19.02 |
| A ház törzsének szélessége |443 |17.44 |
| Mélység az első szerelési karimától a tartási test végéig |577 |22.72 |
| Mélység a műveleti paneltől a kamra legtávolabbi kiirtása ig |630.5 |24.82 |
| Mélység a szerelési karimától a kamra legtávolabbi kiirtása ig |603 |23.74 |

### <a name="enclosure-weight"></a>A tartási terület tömege

A konfigurációtól függően a teljesen megrakott elsődleges burkolat súlya 21-33 kg lehet, és két személyre van szükség a kezeléséhez.

| Kamra | Tömeg |
| --- | --- |
| Maximális súly (a konfigurációtól függ) |30 kg – 33 kg |
| Üres (nincs felszerelve hajtás) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>A tartási környezetre vonatkozó előírások

Ez a szakasz a ház környezetével kapcsolatos specifikációkat sorolja fel. Ebbe a kategóriába tartozik a hőmérséklet, a páratartalom, a magasság, a rázkódás, a rezgés, a tájékozódás, a biztonság és az elektromágneses összeférhetőség (EMC).

### <a name="temperature-and-humidity"></a>Hőmérséklet és páratartalom

| Kamra | Környezeti hőmérséklet-tartomány | Környezeti relatív páratartalom | Maximális nedves izzó |
| --- | --- | --- | --- |
| Működési |5°C - 35°C(41°F - 95°F) |20% - 80% nem kondenzálódó |28°C (82°F) |
| Nem működő |-40°C - 70°C(40°F - 158°F) |5% - 100% nem kondenzáló |29°C (84°F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Légáramlás, magasság, ütés, rezgés, tájékozódás, biztonság és EMC

| Kamra | Működési előírások |
| --- | --- |
| Légáramlás |A rendszer légáramlása elölről hátrafelé halad. A rendszert alacsony nyomású, hátsó kipufogóberendezéssel kell működtetni. Az állványajtók és az akadályok által okozott ellennyomás nem haladhatja meg az 5 pascalt (0,5 mm-es vízmérő). |
| Magasság, üzemi |-30 méter és 3045 méter (-100 láb és 10 000 láb között), a maximális üzemi hőmérséklet 5 °C-kal 7000 láb felett. |
| Magasság, nem működő |-305 méter és 12 192 méter között (-1000 láb és 40 000 láb között) |
| Sokk, működés |5g 10 ms 1/2 szinusz |
| Sokk, nem működő |30g 10 ms 1/2 szinusz |
| Vibráció, működési |0,21 g RMS 5-500 Hz véletlenszerű |
| Nem működő rezgés |1.04g RMS 2-200 Hz véletlenszerű |
| Vibráció, áthelyezés |3g 2-200 Hz-es szinusz |
| Tájolás és rögzítés |19" rack tartó (2 EIA egység) |
| Rack sínek |Az IEC 297 szabványnak megfelelő, legalább 700 mm-es mélységtartók felszerelése |
| Biztonság és jóváhagyások |CE és UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| Emc |EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Nemzetközi szabványoknak való megfelelés

A Microsoft Azure StorSimple eszköz megfelel az alábbi nemzetközi szabványoknak:  

* CE - EN 60950 - 1
* CB-jelentés az IEC 60950- 1-nek
* UL és cUL az UL 60950 - 1

## <a name="safety-compliance"></a>Biztonsági megfelelőség

A Microsoft Azure StorSimple eszköz megfelel a következő biztonsági besorolásoknak:

* A rendszer típusának jóváhagyása: UL, cUL, CE
* Biztonsági megfelelőség: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>EMC-megfelelőség

A Microsoft Azure StorSimple eszköz megfelel a következő EMC-minősítések.

### <a name="emissions"></a>Kibocsátás

A készülék EMC-kompatibilis a vezetett és kisugárzott kibocsátási szintekhez.

* Vezetett kibocsátási határértékek: CFR 47 15B A. osztályú EN55022 A. osztályú CISPR A. osztály
* Kisugárzott kibocsátási határértékek: CFR 47 15B A. osztályú EN55022 A. osztályú CISPR A osztály

### <a name="harmonics-and-flicker"></a>Harmonikus és villódzó

A készülék megfelel az EN61000-3-2/3-nak.

### <a name="immunity-limit-levels"></a>Immunitási határértékszintek

A készülék megfelel az EN55024-nek.

## <a name="ac-power-cord-compliance"></a>A hálózati tápkábel megfelelősége

A csatlakozónak és a teljes tápkábel-egységnek meg kell felelnie a készülék használata országára/régiójára vonatkozó szabványoknak, és rendelkeznie kell az adott országban/régióban elfogadható biztonsági jóváhagyásokkal. Az alábbi táblázatok az USA és Európa szabványait sorolják fel.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Hálózati tápkábelek - USA (nrtl-értéken kell szerepelnie)

| Összetevő | Specifikáció |
| --- | --- |
| A kábel típusa |SV vagy SVT, 18 AWG minimum, 3 vezető, 2,0 méter maximális hossza |
| Plug |NEMA 5-15P földelés típusú csatlakozó dugó névleges 120 V, 10 A; vagy IEC 320 C14, 250 V, 10 A |
| Aljzat |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>Hálózati tápkábelek - Európa

| Összetevő | Specifikáció |
| --- | --- |
| A kábel típusa |Harmonizált, H05-VVF-3G1.0 |
| Aljzat |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Támogatott hálózati kábelek

A 10 GbE hálózati interfészek, a DATA 2 és a DATA 3 esetében a [támogatott hálózati kábelek és modulok listája](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)látható.

## <a name="next-steps"></a>További lépések

Most már készen áll egy StorSimple-eszköz üzembe helyezésére az adatközpontban. További információ: [Deploying your on-premises device](storsimple-8000-deployment-walkthrough-u2.md).

