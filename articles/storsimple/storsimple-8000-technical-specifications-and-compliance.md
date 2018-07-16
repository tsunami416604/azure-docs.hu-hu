---
title: A StorSimple műszaki specifikációk |} A Microsoft Docs
description: Ismerteti a műszaki specifikációk és a StorSimple hardverösszetevők jogszabályi standards megfelelőségi információit.
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
ms.openlocfilehash: 855ea6c34082b859bb5b5b6e69b3e3f2fa54eb4a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056463"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Műszaki specifikációkról és a StorSimple-eszköz megfelelőségének

## <a name="overview"></a>Áttekintés

A hardverösszetevők, a Microsoft Azure StorSimple eszköz betartja a műszaki specifikációkról és a következő cikkben ismertetett szabályozási normák terén. A műszaki specifikációk írják le, a Power és hűtéssel modulok (PCMs), meghajtók, tárolási kapacitás és házakat. A megfelelőségi adatok nemzetközi előírásoknak, biztonsági és mennyiségét és a kábelezést is ismerteti.

## <a name="power-and-cooling-module-specifications"></a>A Power és hűtéssel modul specifikációk

A StorSimple-eszköz két 100-240 V kettős ventilátor, SBB megfelelő Power hűtéssel modulok (PCMs) rendelkezik. Ez egy redundáns áramforrások konfigurációt biztosít. A PCM meghiúsul, ha az eszköz továbbra is a szokott meg a többi PCM mindaddig, amíg a hibás modult váltja fel.

Az EBOD ház egy 580 W PCM, és elsődleges ház egy 764 W PCM használja. Az alábbi táblázatok sorolják fel a műszaki specifikációk a PCMs társított.

| Specifikáció | 580 W PCM (EBOD) | 764 W PCM (elsődleges) |
| --- | --- | --- |
| Maximális kimeneti összteljesítmény |580 W |764 |
| Gyakoriság |50/60 Hz |50/60 Hz |
| Feszültség-tartomány kiválasztása |Automatikus terjedő: 90 – 264 V AC, 47/63 Hz |Automatikus terjedő: 90-264 V AC, 47/63 Hz |
| Jelenlegi maximális behatolása |20-A |20-A |
| Energiagazdálkodási tényező javítása |> 95 %-os névleges bemeneti feszültség |> 95 %-os névleges bemeneti feszültség |
| Harmonikus |Megfelel-e EN61000-3-2 |Megfelel-e EN61000-3-2 |
| Kimenet |5 voltos készenléti feszültség \@ 2.0 A |5 voltos készenléti feszültség \@ 2.7 A |
| + 5 VOLTOS \@ 42 A |+ 5 VOLTOS \@ 40 A | |
| + 12 \@ 38 A |+ 12 \@ 38 A | |
| A gyakran moduláris |Igen |Igen |
| Kapcsolók és LED-EK |AC be-/ kikapcsolási kapcsoló és a négy állapotát jelző LED-EK |AC be-/ kikapcsolási kapcsoló és a hat állapotát jelző LED-EK |
| Hűtés ház |Tengelyirányú hűtés a változó ventilátorvezérlésre sebesség ventilátor |Tengelyirányú hűtés a változó ventilátorvezérlésre sebesség ventilátor |

## <a name="power-consumption-statistics"></a>Energiagazdálkodási használati statisztikák

A következő táblázat felsorolja a különböző modellek StorSimple-eszköz a tipikus energiafogyasztási adatokkal (a tényleges értékek eltérőek lehetnek a közzétett).

| Feltételek | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Inaktív lassú, meghajtók ventilátor |1.45 A |0.31 kW |1057.76 BTU/óra |3.19. A |0.34 kW |1160.13 BTU/óra |
|  Ventilátorok lassú, meghajtók elérése |1.54 A |0.33 kW |1126.01 BTU/óra |3.27 A |0.36 kW |1228.37 BTU/óra |
|  Ventilátorok gyorsan és meghajtók tétlen, két PSUs működtetett |2.14 A |0.49 kW |1671.95 BTU/óra |4.99 A |0.54 kW |1842.56 BTU/óra |
|  Gyors, rajongókat meghajtók tétlen, egy PSU működtetett egy inaktív |2.05 A |0.48 kW |1637.83 BTU/óra |4.58 A |0,50 kW |1706.07 BTU/óra |
|  Ventilátorok fér hozzá, két PSUs működtetett gyorsan és meghajtók |2.26 PONTBAN A |0,51 kW |1740.19 BTU/óra |4.95 A |0.54 kW |1842.56 BTU/óra |
|  A rajongókat gyors meghajtók fér hozzá, egy PSU működtetett egy inaktív |2.14 A |0.49 kW |1671.95 BTU/óra |4.81 A |0.53 kW |1808.44 BTU/óra |

## <a name="disk-drive-specifications"></a>Lemezmeghajtó specifikációk

A StorSimple-eszköz támogatja a legfeljebb 12 3,5 hüvelykes űrlap tényező soros csatlakozású SCSI (SAS) lemezmeghajtókat. A tényleges meghajtók kettőn tartós állapotú meghajtókhoz (SSD-kkel) és a merevlemezes (HDD) meghajtók, a termék konfigurációjától függően. 12 lemezmeghajtó tárolóhelyben elé a ház 3 x 4 konfigurációban található. Az EBOD ház egy másik 12-meghajtók számára további tárhely lehetővé teszi. Ezek mindig a HDD-k.

## <a name="storage-specifications"></a>Storage-leírások

A StorSimple-eszközök rendelkező merevlemez-meghajtók és SSD-meghajtókat a 8100-as és 8600-as. A 8100-as és 8600-as teljes felhasználható kapacitás pedig körülbelül 15 TB, illetve 38 TB jelölik. Az alábbi táblázat a dokumentumok felhő kapacitása a StorSimple megoldás kapacitás környezetében, SSD és HDD részleteit.

| Eszköz modellje / kapacitás | 8100 | 8600 |
| --- | --- | --- |
| Merevlemezes (HDD) meghajtók száma |8 |19 |
| Tartós állapotú meghajtókhoz (SSD-k) száma |4 |5 |
| Egyetlen HDD-kapacitás |4 TB |4 TB |
| Egyetlen SSD kapacitása |400 GB |800 GB |
| Tartalék kapacitással |4 TB |4 TB |
| HDD felhasználható kapacitás |14 TB |36 TB |
| Felhasználható SSD-kapacitás |800 GB |2 TB |
| Teljes felhasználható kapacitást * |~ 15 TB |~ 38 TB |
| Megoldás maximális kapacitás (beleértve a felhő) |200 TB |500 TB |

<sup>* </sup>- *A teljes felhasználható kapacitás rendelkezésre álló kapacitás az adatok, metaadatok és pufferek tartalmazza. A gyors helyi kötetekhez legfeljebb 8,5 TB a 8100-as eszközön vagy a nagyobb 8600-as eszközön legfeljebb 22,5 TB. További információért ugorjon [StorSimple helyileg rögzített kötetekről](storsimple-8000-local-volume-faq.md).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Rendszerház dimenziókat és súlyozás specifikációk

Az alábbi táblázatok sorolják fel a ház különböző dimenziók és a súlyozást előírásainak.

### <a name="enclosure-dimensions"></a>A lemezház dimenziók

Az alábbi táblázat a ház milliméterben és hüvelyk méretét.

| Rendszerház | Milliméterben | Hüvelyk |
| --- | --- | --- |
| Magasság |87.9 |3.46 |
| Szélesség bordában között |483 |19.02 |
| Szélesség törzse ház között |443 |17.44 |
| A ház törzs végén az első bordában mélysége |577 |22.72 |
| A ház legtávolabbi végén a Műveletek panel mélysége |630.5 |24.82 |
| A ház legtávolabbi végén bordában a mélysége |603 |23.74 |

### <a name="enclosure-weight"></a>A lemezház súlyozása

A konfigurációtól függően egy teljesen betöltött elsődleges ház is 33 kgs a 21 mérjük, és Felkészült rá két személyek igényli.

| Rendszerház | Súlyozás |
| --- | --- |
| Maximális súly (konfigurációjától függ) |30 kg – 33 kg |
| Üres (nincs felszerelt meghajtók) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>A lemezház környezet specifikációk

Ez a szakasz felsorolja a ház környezettel kapcsolatos előírásoknak. A hőmérséklet, páratartalom, tengerszint, megváltani, rezgés, tájolást, biztonsági és elektromágneses kompatibilitási (EMC) ebbe a kategóriába szerepelnek.

### <a name="temperature-and-humidity"></a>Hőmérséklettel és páratartalommal kapcsolatos

| Rendszerház | Környezeti hőmérséklet-tartomány | Környezeti relatív páratartalom | Maximális nedves tartályának |
| --- | --- | --- | --- |
| Működik |5 C – 35 C (41° F - 95° F) |80 %-os 20 %-os nem-kondenzációs- |28 C (82° F) |
| Nem működő |-40 C - 70° C (40° F - 158° F) |100 %-os 5 %-os nem kicsapódó |29 C (84° F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Légmozgás, tengerszint, megváltani, rezgés, tájolást, biztonsági és EMC

| Rendszerház | Működési specifikációk |
| --- | --- |
| Légmozgás |Rendszer légmozgás hátsó hosszirányú. Rendszer kell egy alacsony nyomású, hátsó-kipufogógáz telepítési az működtetni. Állvány ajtók és az akadályok által létrehozott nyomása legfeljebb 5 pascalban (0,5 mm víz mérőműszer). |
| Magasság, a üzemeltetési |-30 mérőszámok 3045 mérőszámok (10 000 feet-100 láb) a fent 7000 feet 5 C megszüntetéséhez minősíthetik maximális működési hőmérséklet. |
| Nem működő magasság |-305 mérőszámok 12,192 mérőszámok (40 000 feet-1,000 láb) |
| Megváltani, a üzemeltetési |5 g. 10 ms ½ szinusza |
| Nem működő megváltani |30g 10 ms ½ szinusza |
| Rezgés, a üzemeltetési |0.21g RMS 5-500 Hz véletlenszerű |
| Nem működő rezgés |1.04-es g RMS 2 – 200 Hz véletlenszerű |
| Rezgés, az adatáthelyezési |3g 2 – 200 Hz szinusza |
| Tájolás és csatlakoztatása |19" rack csatlakoztatási (2 EIA egység) |
| Állvány rails |Minimális 700 mm (31.50 hüvelyk) mélysége igazítás állványt IEC 297 megfelelő |
| Biztonság és jóváhagyások |CE és UL EN 61000-3, IEC 61000-3, UL 61000 – 3 |
| EMC |EN55022 (CISPR - A), FCC A |

## <a name="international-standards-compliance"></a>Nemzetközi szabványoknak való megfelelés

A következő nemzetközi előírásoknak megfelel a Microsoft Azure StorSimple-eszköz:  

* CE - EN 60950-1
* CB jelentés IEC 60950-1
* UL és cUL UL 60950-1

## <a name="safety-compliance"></a>Biztonsági megfelelőségi

A Microsoft Azure StorSimple-eszköz megfelel-e a következő biztonsági besorolások:

* Rendszer termék típus jóváhagyási: UL, cUL, CE
* Biztonsági megfelelőségi: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>EMC megfelelőség

A Microsoft Azure StorSimple-eszköz megfelel-e a következő EMC minősítések.

### <a name="emissions"></a>Kibocsátás

Az eszköz EMC megfelelő vezetett és kisugárzott kibocsátási.

* Vezetett mennyiségét korlátozza szintek: CFR 47 15B osztály egy EN55022 osztály egy CISPR osztály a.
* Kisugárzott mennyiségét korlátozza szintek: CFR 47 15B osztály egy EN55022 osztály egy CISPR osztály a.

### <a name="harmonics-and-flicker"></a>Harmonikus és Villódzás

Az eszköz megfelel-e az EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Mentesség korlát szintek

Az eszköz megfelel-e az EN55024.

## <a name="ac-power-cord-compliance"></a>AC teljesítmény kapcsolatai megfelelőség

A Plug and és a teljes körű power kábellel szerelvény meg kell felelnie az előírásoknak megfelelő, az ország, ahol az eszközt használja, és biztonsági jóváhagyások, amelyek elfogadható ebben az országban kell rendelkezniük. Az alábbi táblázatok sorolják fel az USA és Európa szabványainak.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>AC tápkábelek – Amerikai Egyesült Államok (a felsorolt NRTL kell lennie)

| Összetevő | Specifikáció |
| --- | --- |
| Típus kapcsolatai |SV vagy SVT, minimális. AWG 18., 3 conductor 2.0 mérőszámok maximális hossza |
| Plug |NEMA 5 – 15P résidőalapban-mellékletet beépülő 120 V, A; 10 besorolása vagy IEC a 320 14, 250 V, A 10 |
| A szoftvercsatorna |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>Tápkábelek AC - Európa

| Összetevő | Specifikáció |
| --- | --- |
| Típus kapcsolatai |Összehangolt, H05-VVF-3G1.0 |
| A szoftvercsatorna |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>A támogatott hálózati kábeleket

A 10 GbE hálózati adapterek DATA 2 és DATA 3, tekintse meg a [támogatott hálózati kábelek és a modulok listája](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>További lépések

Most már készen áll az adatközpontban StorSimple eszköz üzembe helyezéséhez. További információkért lásd: [a helyszíni eszköz üzembe helyezésének](storsimple-8000-deployment-walkthrough-u2.md).

