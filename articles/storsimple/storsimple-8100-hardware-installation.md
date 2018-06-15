---
title: Telepítse a Microsoft Azure StorSimple 8100-as eszközhöz |} Microsoft Docs
description: Kicsomagolása, állványra szerelése és központi telepítése és a szoftver konfigurálása előtt a StorSimple 8100 bekábelezésére ismerteti.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 5fbc407a9792d033037fdaa2b14f4055d94c15ab
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2018
ms.locfileid: "27743466"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Kicsomagolása, állványra-csatlakoztatási, és a StorSimple 8100 bekábelezése
## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple 8100-e egy egyetlen ház, állványra szerelt eszköz. Ez az oktatóanyag azt ismerteti, hogyan kicsomagolása, állványra-csatlakoztatási, és a StorSimple 8100 kábel hardver beállítása és a StorSimple eszköz üzembe helyezése előtt.

## <a name="unpack-your-storsimple-8100-device"></a>Bontsa ki a StorSimple 8100-as eszközhöz
Az alábbi lépések nyújtanak a StorSimple 8100 tárolóeszköz kicsomagolása törölje a jelet, részletes útmutatást. Ez az eszköz egyetlen mezőben van kiadva.

### <a name="prepare-to-unpack-your-device"></a>Bontsa ki az eszköz előkészítése
Mielőtt az eszköz csomagolja ki, áttekintheti a következőket.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png)![nagy súlyozást ikon](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **figyelmeztetés!**

1. Győződjön meg arról, hogy rendelkezik-e a ház súlya kezelhető, ha manuálisan vannak kezelése két személy. A teljesen konfigurált ház is mérjük legfeljebb 32 kg (70 lbs.).
2. Helyezze a mezőben egy egyszerű, szintű felületen.

Ezután kövesse az alábbi lépéseket az eszköz kicsomagolásához.

#### <a name="to-unpack-your-device"></a>Az eszköz kicsomagolása
1. Vizsgálja meg a és a crushes, darabok, vízjel sérülés vagy más egyértelmű károkért csomagolás használatos. Ha a lista vagy a csomag súlyosan sérült, nem a megnyitása. Adjon [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) segítséget ellenőrzéséhez, hogy az eszköz működőképes állapotban van.
2. Bontsa ki a jelölőnégyzetet. A következő kép bemutatja a StorSimple eszköz kibontott nézete.
   
     ![Bontsa ki a tárolóeszköz](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **A tárolóeszköz kibontott nézete**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Csomagolási mezőbe |
   |   2 |Alsó hab |
   |   3 |Eszköz |
   |   4 |Felső hab |
   |   5 |Kiegészítő mezőben |
3. Után kicsomagolása a jelölőnégyzetet, győződjön meg arról, hogy rendelkezik-e:
   
   * 1 egyetlen ház eszköz
   * 2 tápkábelek
   * 1 fordított kábel
   * 2 soros konzol kábelek
   * a soros hozzáféréshez 1 soros-USB-konverter
   * 1 hamisíthatatlan T10 csavarhúzóra
   * 4 QSFP-az-adapterek SFP + 10 GbE hálózati adapterrel való használatra
   * 1. állvány-csatlakoztatási kit (2 ügyféloldali sínek csatlakoztatni hardverkonfiguráción)
   * Getting Started dokumentáció
     
     Ha Ön nem kapta meg az elemek, a fent felsorolt [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md).

A következő lépés, hogy az eszköz állvány csatlakoztatást.

## <a name="rack-mount-your-storsimple-8100-device"></a>Állvány csatlakoztatást a StorSimple 8100-as eszközhöz
A következő lépések segítségével a StorSimple 8100 tárolóeszköz telepítése az első és hátsó bejegyzéseket szabványos 19 hüvelykes szekrényben. A StorSimple 8100 eszközhöz tartozik egy önálló elsődleges ház.

A telepítés több lépést ismertet, amelyek a következő eljárások áll.

> [!IMPORTANT]
> StorSimple eszközökhöz kell lennie az állványra szerelt megfelelő működéséhez.
> 
> 

### <a name="prepare-the-site"></a>A hely előkészítése
Az eszköz, amely az első és hátsó bejegyzéseket szabványos 19 hüvelykes állvány kell telepíteni. A következő eljárással állvány telepítés előkészítéséhez.

#### <a name="to-prepare-the-site-for-rack-installation"></a>A hely állvány telepítés előkészítéséhez
1. Győződjön meg arról, hogy az eszköz biztonságos alapja a strukturálatlan stabil és szintű munkahelyi felület (vagy hasonlót).
2. Ellenőrizze, hogy a hely, ahol kíván beállítani egy független forrás- vagy egy állványra terjesztési teljesítménye (PDU) rendelkező szünetmentes tápegység (UPS) szabványos AC tápellátás.
3. Győződjön meg arról, hogy egy 2U tárolóhely érhető el az állványra profilokhoz csatlakoztassa az eszközt.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png)![nagy súlyozást ikon](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **figyelmeztetés!**

Győződjön meg arról, hogy rendelkezik-e két személynek kezelése a súly, ha az eszköz beállítását manuálisan vannak kezelése érhető el. A teljesen konfigurált ház is mérjük legfeljebb 32 kg (70 lbs.).

### <a name="rack-prerequisites"></a>Állvány Előfeltételek
A 8100-ház kabinetfájl a szabványos 19 hüvelykes szekrényben telepítési készült:

* A post a feladás egy vagy több állványra 27.84 hüvelyk minimális mélységét.
* Az eszköz 32 kg legnagyobb súlyú
* 5 Pascal (0,5 mm vízjel mérőműszer) maximális hátsó terhelés.

### <a name="rack-mounting-rail-kit"></a>Állvány csatlakoztatása oldalon csomag
A 19 hüvelykes állvány kabinet való használatra sínek csatlakoztatása készlete valósul meg. A maximális ház súly kezelni a sínek lettek tesztelve. Ezek sínek lehetővé teszi több házakat az állványra lemezterülete adatvesztés nélkül telepítési.

#### <a name="to-install-the-device-on-the-rails"></a>Az eszköz telepítése a sínen
1. Hajtsa végre ezt a lépést, csak akkor, ha a belső sínek nincsenek telepítve az eszközön. A belső sínek általában gyári vannak telepítve. Ha sínek nincsenek telepítve, majd telepítse a bal oldali-oldalon, és jobb-oldalon diák a ház váz oldalán. Hat metrika csavart segítségével mindkét oldalon csatolja azokat. Tájolás érdekében az oldalon diák megjelölve **LH – első** és **RH – első**, és a célból, hogy a ház hátulról dobozának kúpos.<br/>
   
    ![Ház váz oldalon diák csatolása](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Belső oldalon diák csatolása a ház oldala**
   
    Címke | Leírás
    ----- | -----------
    1     | M 3 x 4 gomb-head csavart
    2     | Váz diák

2. A külső bal oldalon és a jobb oldalon külső szerelvények csatolása a állvány kabinet függőleges tagokat. A szögletes megjelölve **LH**, **RH**, és **Ez az oldal fel** végigvezeti a megfelelő tájolását.
3. Keresse meg az oldalon PIN-kód az első és hátsó oldalon szerelvény. Az oldalon való elhelyezéséhez állvány helyek között, és a PIN-kód beillesztése az első és hátsó állvány post függőleges tag lyuk kiterjesztése. Győződjön meg arról, hogy oldalon szerelvény be-e szintjét.
4. Használjon a a megadott metrika csavart az állványra oldalon szerelvény függőleges tagok védelmét. Az első és hátsó a egy csavart használja.
5. Ismételje meg ezeket a lépéseket a másik oldalon szerelvény.<br/>
   
     ![Állvány kabinet oldalon diák csatolása](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Az állványra külső oldalon szerelvények csatolása**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Rögzítéssel csavart |
   |   2 |Négyzetes-lyuk első állvány post csavart |
   |   3 |Első hely PIN-kód bal oldalon |
   |   4 |Rögzítéssel csavart |
   |   5 |Hátsó hely PIN-kód bal oldalon |

### <a name="mounting-the-device-in-the-rack"></a>Az eszközt, az állvány csatlakoztatása
Az imént telepített állvány sínek, végezze el az alábbi lépések végrehajtásával csatlakoztassa az eszközt, az állvány.

#### <a name="to-mount-the-device"></a>Az eszköz csatlakoztatása
1. A segítségnyújtó növekedési a ház és igazíthatja a állvány sínek.
2. Gondosan a sínek helyezze az eszközt, és majd leküldéses az eszköz teljes mértékben be az állványra kabinetfájl.<br/>
   
    ![Az állványra eszköz beszúrása](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Az eszközt, az állvány csatlakoztatása**
3. Távolítsa el a bal és jobb első nyomkarima caps modulba húzza a caps szabad. A nyomkarima caps egyszerűen illesztés fekszenek helyezik.
4. A ház, az állvány biztonságos egy megadott Phillips-head csavart keresztül minden nyomkarima, bal és jobb telepítésével.
5. Telepítse a nyomkarima caps lenyomásával őket egy helyen, és helyben illesztésének őket.<br/>
   
     ![Nyomkarima caps telepítése](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **A nyomkarima caps telepítése**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Ház rögzítési csavart |

A következő lépés, hogy a tápellátáshoz, a hálózati és a soros hozzáféréshez bekábelezésére.

## <a name="cable-your-storsimple-8100-device"></a>A StorSimple 8100 bekábelezése
Az alábbi eljárások ismertetik a StorSimple 8100 bekábelezése a tápellátáshoz, a hálózati és a soros kapcsolatok.

### <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené, az eszköz kábelek, szüksége lesz:

* A tárolóeszköz teljesen kicsomagolása és az állványra szerelt.
* az eszköz használati utasításának megfelelően 2 kábelek
* (Ajánlott) 2 Áramelosztó egységekből való hozzáférést.
* Hálózati kábel
* A megadott soros kábelek
* Soros USB konverter a megfelelő illesztőprogram telepítve a számítógépen (ha szükséges)
* A megadott 4 QSFP-a-adapterek SFP + 10 GbE hálózati adapterrel való használatra
* [Támogatott hardveres a 10 GbE hálózati adapterek a StorSimple eszköz](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Energiagazdálkodási kábelek
Az eszköz redundáns energia- és hűtési modulok (PCMs) tartalmazza. Mindkét PCMs telepítve legyen, és csatlakozik a különböző áramforrásokból magas rendelkezésre állásának biztosításához.

A következő lépésekkel bekapcsolási bekábelezésére.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Hálózati kábel
Az eszköz egy aktív-készenléti állapotban lévő konfigurációs: egy adott időpontban egy tartományvezérlő modul aktív és feldolgozási összes lemez és hálózat műveletek során a tartományvezérlő modul készenléti állapotba. Ha egy tartományvezérlő sikertelen, a készenléti vezérlő azonnal aktiválni, és továbbra is fennáll, a lemez és a hálózati műveletek.

A redundáns vezérlő feladatátvételi támogatja, az eszköz hálózati bekábelezése, a következő lépésekben ismertetett kell.

#### <a name="to-cable-for-network-connection"></a>A hálózati kapcsolat bekábelezése
1. Az eszköz minden tartományvezérlőn hat hálózati adapterrel rendelkezik: négy 1 GB/s, és két 10 gbps-os Ethernet-portokat. A különböző adatok portokat a csatlakozópanel az eszköz azonosítására.
   
    ![A 8100-as eszközön Csatlakozópanel](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Vissza az eszközt az adatok portok megjelenítése**
   
   | Címke | Leírás |
   | --- | --- |
   |   0,1,4,5 |1 GbE hálózati adapterek |
   |   2,3 |10 GbE hálózati adapterek |
   |   6 |Soros portokhoz |
2. Tekintse meg a következő ábra a hálózati kábeleket. (A minimális hálózati konfiguráció folytonos kék vonal is látható. A magas rendelkezésre állás és teljesítmény szükséges további konfigurációs látható szaggatott vonal.)

    ![A hálózati 2U bekábelezésére](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Az eszköz kábelek hálózati**

   |Címke | Leírás |
   |----- | ----------- |
   | A    | Internet-hozzáféréssel rendelkező LAN |
   | B    | A vezérlő 0 |
   | C#    | PCM 0 |
   | D    | 1. vezérlő |
   | E    | PCM 1 |
   | F, G | Hosts |
   | 0-5  | Hálózati illesztők |



Az eszköz kábelek, a minimális konfigurációs kell rendelkeznie:

* Legalább két hálózati adapterrel minden tartományvezérlőn, a felhő hozzáféréshez és az egyikben iSCSI csatlakoztatva. A DATA 0 port automatikusan engedélyezve és konfigurálva, az eszköz soros konzolon keresztül. DATA 0, leszámítva a adatokat egy másik portra is kell megadni a klasszikus Azure portálon keresztül. Ebben az esetben a DATA 0 csatlakozzon az elsődleges helyi hálózathoz (Internet-hozzáféréssel rendelkező hálózati) porton. Az egyéb adatok portok SAN/iSCSI LAN (VLAN) szegmens a hálózat, attól függően, hogy a megfelelő szerepkör lehet csatlakoztatni.
* Minden egyes tartományvezérlő azonos csatolóinak kapcsolódik ugyanazon a hálózaton rendelkezésre állásának biztosításához, ha egy tartományvezérlő feladatátvételt hajt végre. Például ha kíván csatlakozni a DATA 0 és a vezérlőket a DATA 3, kell a megfelelő adatok 0 és a DATA 3 csatlakozzon a többi tartományvezérlőn.

Vegye figyelembe a magas rendelkezésre állás és teljesítmény:

* Ha lehetséges, állítsa be két hálózati csatoló a felhőelérést (1 gbe-s) és az iSCSI (ajánlott 10 GbE) egy másik pár minden tartományvezérlőn.
* Ha lehetséges, kapcsolódó hálózati adapterek minden vezérlő két különböző kapcsolókhoz egy kapcsoló meghibásodása elleni rendelkezésre állásának biztosításához. Az ábra azt mutatja be, a két 10 GbE hálózati adapterek, DATA 2 és a DATA 3 két különböző kapcsolókhoz csatlakozik, minden tartományvezérlőről.

További információkért tekintse meg a **hálózati illesztőt** alatt a [a StorSimple eszköz követelményei magas rendelkezésre állású](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Ha a 10 GbE hálózati adapterek SFP + adó használ, használja a megadott QSFP-SFP + adapterek. További információkért látogasson el [támogatott hardvert a 10 GbE hálózati adapterek a StorSimple eszköz](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Soros port kábelek
A következő lépésekkel bekábelezése a soros port.

#### <a name="to-cable-for-serial-connection"></a>A soros kapcsolat bekábelezése
1. Az eszköz soros minden vezérlő egy csavarkulcsot ábrázoló ikon által azonosított-porttal rendelkezik. Az ábrán a tekintse meg a [hálózati kábeleket](#network-cabling) szakaszban keresse meg az eszköz csatlakozópanel a soros port.
2. Azonosítsa az eszköz csatlakozópanel aktív vezérlőhöz. Egy villogó kék LED azt jelzi, hogy a vezérlő aktív.
3. A megadott soros kábelek (ha szükséges, az USB – soros átalakító hordozható számítógépre), és a konzol vagy a számítógép (a terminálemuláció az eszközhöz) kapcsolódjanak a soros port az aktív vezérlő.
4. A sorozat-USB-illesztőprogramot (az eszköz) telepítése a számítógépre.
5. A soros kapcsolat beállítása az alábbiak szerint: 115 200 átviteli, 8 adatbitek, 1 stop bit, nem a paritásos és adatfolyam vezérlés beállítása None.
6. Győződjön meg arról, hogy működik-e a kapcsolat a konzolon Enter billentyűkombináció lenyomásával. A soros konzol menü megjelenjen-e.

> [!NOTE]
> **Lights-Out felügyeleti**: Ha az eszköz telepítve van távoli adatközpontban vagy korlátozott hozzáférésű számítógép helyiségben, győződjön meg arról, a soros mindkét tartományvezérlői kapcsolatok mindig csatlakoztatva a soros konzol kapcsoló vagy hasonló berendezések. Ez lehetővé teszi sávon kívüli a távvezérlés és a támogatási műveleteket, ha hálózati hibákat vagy váratlan meghibásodások esetén.
> 
> 

Az eszköz most már bekábelezte a tápellátáshoz, a hálózati hozzáférés és a soros kapcsolat. A következő lépés, hogy a szoftver konfigurálása és az eszköz üzembe helyezése.

## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan [központi telepítése és konfigurálása a helyszíni StorSimple eszköz](storsimple-8000-deployment-walkthrough-u2.md).

