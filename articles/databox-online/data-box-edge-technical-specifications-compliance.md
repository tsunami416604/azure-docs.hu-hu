---
title: A Microsoft Azure Data Box Edge műszaki specifikációkról és megfelelőség |} A Microsoft Docs
description: A műszaki specifikációk és az Azure Data Box Edge megfelelőségi ismertetése
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 52fb32a8b34c62fe94ab35e2c051d996ab8bef10
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449203"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Az Azure Data Box Edge műszaki specifikációk

A hardverösszetevők, a Microsoft Azure Data Box Edge-eszköz betartja a műszaki specifikációkról és a következő cikkben ismertetett szabályozási normák terén. A műszaki specifikációk az ellátási egységek (PSUs), a tárolási kapacitás, a ház és a környezeti szabványok ismertetik. 

## <a name="power-supply-unit-specifications"></a>Energiagazdálkodási ellátási egység specifikációk

A Data Box peremhálózati eszköz van két 100-240 V ellátási egységek (PSUs) a nagy teljesítményű ventilátor. A két PSUs adjon meg egy redundáns áramforrások konfigurációt. Ha egy PSU meghiúsul, az eszköz továbbra is a szokott a többi PSU a mindaddig, amíg a hibás modult váltja fel. A következő táblázat felsorolja a PSUs műszaki leírásában.

| Specifikáció           | 750 W PSU                  |
|-------------------------|----------------------------|
| Maximális kimeneti összteljesítmény    | 750 W                     |
| Gyakoriság               | 50/60 Hz                   |
| Feszültség-tartomány kiválasztása | Automatikus terjedő: 100-240 V AC |
| A gyakran moduláris           | Igen                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="storage-specifications"></a>Storage-leírások

A Data Box peremeszközökre 10 X 2,5" NVMe SSD-k, 1,6 TB kapacitású mindegyik rendelkezik. Ezek SSD 2 operációsrendszer-lemezek, és az egyéb 8 adatlemezeket. Az eszköz teljes felhasználható kapacitás esetében körülbelül 12,5 TB. Az alábbi táblázat a tárolási kapacitás, az eszköz részleteinek rendelkezik.

|     Specifikáció                          |     Érték             |
|--------------------------------------------|-----------------------|
|    Tartós állapotú meghajtókhoz (SSD-k) száma     |    8                  |
|    Egyetlen SSD kapacitása                     |    1,6 TB             |
|    Teljes kapacitás                          |    12.8 TB            |
|    Teljes felhasználható kapacitást *                  |    ~ 12,5 TB            |

**Több helyet a belső használatra van fenntartva.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Rendszerház dimenziókat és súlyozás specifikációk

Az alábbi táblázatok sorolják fel a ház különböző dimenziók és a súlyozást előírásainak.

### <a name="enclosure-dimensions"></a>A lemezház dimenziók

Az alábbi táblázat a ház milliméterben és hüvelyk méretét.

|     Rendszerház     |     Milliméterben     |     Hüvelyk     |
|-------------------|---------------------|----------------|
|    Magasság         |    44.45            |    1.75"          |
|    Szélesség          |    434.1           |    17.09"          |
|    Hossz          |    740.4           |    29.15"          |

Az alábbi táblázat a szállítási címhez tartozó csomag milliméterben és hüvelyk méretét.

|     Csomag     |     Milliméterben     |     Hüvelyk     |
|-------------------|---------------------|----------------|
|    Magasság         |    311.2            |    12.25"          |
|    Szélesség          |    642.8          |    25.31"          |
|    Hossz          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>A lemezház súlyozása

Az eszköz csomag tömege 66 lbs. és Felkészült rá két személyek igényli. Az eszköz függ a ház konfigurációját.

|     Rendszerház                                 |     Tömeg          |
|-----------------------------------------------|---------------------|
|    Többek között a csomagolás teljes súlya       |    61 lbs.          |
|    Az eszköz súlyozása                       |    35 lbs.          |

## <a name="enclosure-environment-specifications"></a>A lemezház környezet specifikációk

Ez a szakasz felsorolja a ház környezetekben, például a hőmérséklet, a páratartalmat és a magasság kapcsolatos előírásoknak.

### <a name="temperature-and-humidity"></a>Hőmérséklettel és páratartalommal kapcsolatos

|     Rendszerház         |     Környezeti hőmérséklet-tartomány     |     Környezeti relatív páratartalom     |     Maximális harmatpontja     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Működik        |    10 C – 35 C (50° F - 86° F)         |    80 %-os 10 %-os nem kicsapódó.         |    29°C (84°F)            |
|    Nem működő    |    Tartsuk ott-40 C-65 C (tartsuk ott-40 ° F - 149 ° F)     |    95 %-os 5 %-os nem kicsapódó.          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Légmozgás, tengerszint, megváltani, rezgés, tájolást, biztonsági és EMC

|     Rendszerház                           |     Működési specifikációk                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Légmozgás                              |    Rendszer légmozgás hátsó hosszirányú. Rendszer kell egy alacsony nyomású, hátsó-kipufogógáz telepítési az működtetni. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maximális magasság, a üzemeltetési        |    határozza meg 3048 mérőszámok (10 000 feet) a maximális operációsrendszer-megszüntetéséhez minősített hőmérséklet [üzemi megszüntetéséhez minősítés specifikációk hőmérséklet](#operating-temperature-de-rating-specifications).                                                                                |
|    Nem működő maximális magasság    |    12 000 mérőszámok (39,370 feet)                                                                                                                                                                                         |
|    Megváltani, a üzemeltetési                   |    A 6 tájolások 11 ezredmásodpercig 6 G                                                                                                                                                                         |
|    Nem működő megváltani               |    A tájolások 6 2 ezredmásodperc 71 G                                                                                                                                                                           |
|    Rezgés, a üzemeltetési               |    0.26 G<sub>RMS</sub> 5 Hz és 350 Hz véletlenszerű                                                                                                                                                                                     |
|    Nem működő rezgés           |    1,88 G<sub>RMS</sub> 10 Hz és 500 Hz 15 percig (minden hat oldalról tesztelését.)                                                                                                                                                  |
|    Tájolás és csatlakoztatása             |    19" rack csatlakoztatási                                                                                                                                                                                        |
|    Biztonság és jóváhagyások                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012 / CISPR 32:2012  <br>EN 55032:2015 / CISPR 32:2015  <br>EN 55024:2010 + A1:2015 / CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014 / IEC 61000-3-2:2014 (D osztály)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energia             |    A Bizottság (EU) mezőben. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Megszünteti a specifikációk minősítés működési hőmérséklet

|     Működési hőmérséklet megszüntetéséhez minősítés     |     Környezeti hőmérséklet-tartomány                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Visszamenőleges tárolása akár 35 C (95° F)                       |    Maximális hőmérséklet csökken által 1 C/300 m (1° F/547 szerint) 950 m (3,117 szerint).    |
|    35 C-40 C (95° F 104° f)            |    Maximális hőmérséklet csökken szerint 1 C/175 m (1° F/319 szerint) feletti 950 m (3,117 szerint).    |
|    40 C-45 C (104° F 113° f)           |    Maximális hőmérséklet csökken szerint 1 C/125 m (1° F/228 szerint) feletti 950 m (3,117 szerint).    |


## <a name="next-steps"></a>További lépések

- [Az Azure Data Box Edge üzembe helyezése](data-box-edge-deploy-prep.md)
