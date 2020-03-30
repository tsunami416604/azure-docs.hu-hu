---
title: Microsoft Azure Data Box Edge műszaki specifikációk és megfelelőség| Microsoft dokumentumok
description: Ismerje meg az Azure Data Box Edge műszaki specifikációit és megfelelőségét
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/02/2020
ms.author: alkohli
ms.openlocfilehash: b646ee9b727d5adf4ec1c8b5c769b3d8f5c0fc1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252035"
---
# <a name="azure-data-box-edge-technical-specifications"></a>Az Azure Data Box Edge műszaki specifikációi

A Microsoft Azure Data Box Edge-eszköz hardverösszetevői megfelelnek a cikkben ismertetett műszaki előírásoknak és szabályozási szabványoknak. A műszaki előírások leírják a tápegységeket (PSUs), a tárolókapacitást, a burkolatokat és a környezetvédelmi szabványokat. 

## <a name="compute-memory-specifications"></a>Számítás, memória specifikációk

A Data Box Edge eszköz a következő számítási és memóriaspecifikációjú:

| Specifikáció           | Érték                  |
|-------------------------|----------------------------|
| CPU    | 2 x 10 magos CPU                     |
| Memory (Memória)              | 128 GB RAM                  |


## <a name="fpga-specifications"></a>FPGA specifikációk

A Field Programmable Gate Array (FPGA) minden Data Box Edge eszköz, amely lehetővé teszi a Machine Learning (ML) forgatókönyvek. 

| Specifikáció           | Érték                  |
|-------------------------|----------------------------|
| Fpga   | Intel Arria 10 <br> A rendelkezésre álló Deep Neural Network (DNN) modellek megegyeznek a [felhőalapú FPGA-példányok által támogatott modellekkel.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-fpga-web-service#whats-supported-on-azure)| 


## <a name="power-supply-unit-specifications"></a>A tápegység specifikációi

A Data Box Edge eszköz két 100-240 V-os tápegységgel (PSUs) rendelkezik nagy teljesítményű ventilátorokkal. A két PSUs redundáns energiakonfigurációt biztosít. Ha egy tápegység meghibásodik, az eszköz továbbra is megfelelően működik a másik tápegységen, amíg a meghibásodott modult ki nem cserélik. Az alábbi táblázat a PSZ műszaki előírásait sorolja fel.

| Specifikáció           | 750 W tápegység                  |
|-------------------------|----------------------------|
| Maximális kimenő teljesítmény    | 750 W                     |
| Frequency               | 50/60 Hz                   |
| Feszültségtartomány kiválasztása | Automatikus távolság: 100-240 V AC |
| Forró dugaszolható           | Igen                        |

<!--## Power consumption statistics

The following table lists the typical power consumption data (actual values may vary from the published) for the Data Box Edge device.-->

## <a name="network-interface-specifications"></a>Hálózati adapter specifikációi

A You Data Box Edge eszköz 6 hálózati csatolóval rendelkezik, PORT1- PORT6.

| Specifikáció           | Leírás                 |
|-------------------------|----------------------------|
|  Hálózati illesztők    | 2 db 1 GbE sávszélességű illesztő – 1 a kezeléshez, a felhasználó által nem konfigurálható, a kezdeti beállításhoz való. A másik felület felhasználó által konfigurálható, adatátvitelre használható, és alapértelmezés szerint DHCP. <br>2 db 25 GbE sávszélességű illesztő – 10 GbE sávszélességű illesztőként is üzemelnek. Az adatillesztők a felhasználó által megadott konfigurációja DHCP (alapértelmezett) vagy statikus lehet. <br> 2 db 25 GbE sávszélességű illesztő – Az adatillesztők a felhasználó által megadott konfigurációja DHCP (alapértelmezett) vagy statikus lehet.                  |

## <a name="storage-specifications"></a>Tárolási előírások

A Data Box Edge eszközök 9 X 2,5"-os NVMe SSD-vel rendelkeznek, amelyek mindegyike 1,6 TB kapacitással rendelkezik. Ezen SSD-k közül az 1 az operációs rendszer lemeze, a másik 8 pedig adatlemez. A készülék teljes felhasználható kapacitása nagyjából 12,5 TB. Az alábbi táblázat az eszköz tárolókapacitásának részleteit tartalmazza.

|     Specifikáció                          |     Érték             |
|--------------------------------------------|-----------------------|
|    SSD-meghajtók száma     |    8                  |
|    Egyetlen SSD-kapacitás                     |    1,6 TB             |
|    Teljes kapacitás                          |    12,8 TB            |
|    Teljes felhasználható kapacitás*                  |    ~ 12,5 TB            |

**Néhány hely belső használatra van fenntartva.*

## <a name="enclosure-dimensions-and-weight-specifications"></a>A tartási terület méretei és súlyspecifikációi

Az alábbi táblázatok a ház különböző méreteit és súlyát sorolják fel.

### <a name="enclosure-dimensions"></a>A ház méretei

Az alábbi táblázat a ház méreteit sorolja fel milliméterben és hüvelykben.

|     Kamra     |     Milliméter     |     Hüvelyk     |
|-------------------|---------------------|----------------|
|    Height (Magasság)         |    44.45            |    1.75"          |
|    Szélesség          |    434.1           |    17.09"          |
|    Hossz          |    740.4           |    29.15"          |

Az alábbi táblázat a szállítási csomag méreteit sorolja fel milliméterben és hüvelykben.

|     Csomag     |     Milliméter     |     Hüvelyk     |
|-------------------|---------------------|----------------|
|    Height (Magasság)         |    311.2            |    12.25"          |
|    Szélesség          |    642.8          |    25.31"          |
|    Hossz          |   1,051.1          |    41.38"          |

### <a name="enclosure-weight"></a>A tartási terület tömege

A készülék csomag súlya 66 lbs. és két személyre van szükség a kezeléséhez. A készülék súlya a burkolat konfigurációjától függ.

|     Kamra                                 |     Tömeg          |
|-----------------------------------------------|---------------------|
|    Össztömeg a csomagolással együtt       |    61 font.          |
|    A készülék súlya                       |    35 font.          |

## <a name="enclosure-environment-specifications"></a>A tartási környezetre vonatkozó előírások

Ez a szakasz a burkolati környezetre vonatkozó specifikációkat sorolja fel, például a hőmérsékletet, a páratartalmat és a magasságot.

### <a name="temperature-and-humidity"></a>Hőmérséklet és páratartalom

|     Kamra         |     Környezeti hőmérséklet-tartomány     |     Környezeti relatív páratartalom     |     Maximális harmatpont     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Működési        |    10°C - 35°C (50°F - 86°F)         |    10% - 80% nem kondenzálódás.         |    29°C (84°F)            |
|    Nem működő    |    -40°C és 65°C között (-40°F - 149°F)     |    5% - 95% nem kondenzálódás.          |    33°C (91°F)            |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Légáramlás, magasság, ütés, rezgés, tájékozódás, biztonság és EMC

|     Kamra                           |     Működési előírások                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Légáramlás                              |    A rendszer légáramlása elölről hátrafelé halad. A rendszert alacsony nyomású, hátsó kipufogóberendezéssel kell működtetni. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).-->    |
|    Maximális magasság, üzemben        |    3048 méter (10 000 láb) a maximális üzemi hőmérséklet de-rated határozza meg [működési hőmérséklet de-minősítés előírások](#operating-temperature-de-rating-specifications).                                                                                |
|    Maximális magasság, nem működő    |    12 000 méter                                                                                                                                                                                         |
|    Sokk, működés                   |    6 G 11 milliszekundumért 6 irányban                                                                                                                                                                         |
|    Sokk, nem működő               |    71 G 2 milliszekundumig 6 irányban                                                                                                                                                                           |
|    Vibráció, működési               |    0,26 G<sub>RMS</sub> 5 Hz és 350 Hz között véletlenszerűen                                                                                                                                                                                     |
|    Nem működő rezgés           |    1,88 G<sub>RMS</sub> 10 Hz és 500 Hz között 15 percig (mind a hat oldalon tesztelve.)                                                                                                                                                  |
|    Tájolás és rögzítés             |    19" rack tartó                                                                                                                                                                                        |
|    Biztonság és jóváhagyások                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    Emc                                  |    FCC A, ICES-003 <br>En 55032:2012/CISPR 32:2012  <br>En 55032:2015/CISPR 32:2015  <br>En 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (D osztály)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energia             |    A Bizottság (EU) rendeletszáma 617/2013                                                                                                                                                                                        |
|    Rohs           |    En 50581:2012                                                                                                                                                                                        |


### <a name="operating-temperature-de-rating-specifications"></a>Az üzemi hőmérséklet minősítésének deminősítési specifikációi

|     Üzemi hőmérséklet-de-minősítés     |     Környezeti hőmérséklet-tartomány                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Akár 35°C (95°F)                       |    A maximális hőmérséklet 1°C/300 m-rel (3117 láb) magasabb, mint 950 m.- vel.    |
|    35°C és 40°C között            |    A maximális hőmérséklet 1°C/175 m-rel (3117 láb) nagyobb, mint 950 m.-rel.    |
|    40°C és 45°C között           |    A maximális hőmérséklet 1°C/125 m-rel (1°F/228 láb) 950 m (3117 láb) fölé csökken.    |


## <a name="next-steps"></a>További lépések

- [Az Azure Data Box Edge üzembe helyezése](data-box-edge-deploy-prep.md)
