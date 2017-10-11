---
title: "Telepítse a Microsoft Azure StorSimple 8600-as eszközön |} Microsoft Docs"
description: "Kicsomagolása, állványra szerelése és központi telepítése és a szoftver konfigurálása előtt a StorSimple 8600 bekábelezésére ismerteti."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 10/24/2016
ms.author: alkohli
ms.openlocfilehash: 309ceba2d65c0745ba1acac698acb62526ab8078
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Kicsomagolása, állványra-csatlakoztatási, és a StorSimple 8600 bekábelezése
## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple 8600 kettős ház eszköz, és egy elsődleges és egy EBOD ház áll. Ez az oktatóanyag azt ismerteti, hogyan kicsomagolása, állványra-csatlakoztatási, és a StorSimple 8600 kábel eszközhardver előtt konfigurálása a StorSimple szoftver.

## <a name="unpack-your-storsimple-8600-device"></a>Bontsa ki a StorSimple 8600 eszköz
Az alábbi lépéseket a StorSimple 8600 tárolóeszköz kicsomagolása törölje a jelet, részletes utasításokat biztosítanak. Ez az eszköz két mezőbe, egyet a elsődleges ház és egy másikat a EBOD ház a nyújtják. Ezek a mezők két majd kerülnek mezőt.

### <a name="prepare-to-unpack-your-device"></a>Bontsa ki az eszköz előkészítése
Mielőtt az eszköz csomagolja ki, áttekintheti a következőket.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png)![nagy súlyozást ikon](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **figyelmeztetés!**

1. Győződjön meg arról, hogy rendelkezik-e két személynek az eszköz a súlyozási kezelhető, ha manuálisan is kezel. A teljesen konfigurált ház is mérjük legfeljebb 32 kg (70 lbs.).
2. Helyezze a mezőben egy egyszerű, szintű felületen.

Ezután kövesse az alábbi lépéseket az eszköz kicsomagolásához.

#### <a name="to-unpack-your-device"></a>Az eszköz kicsomagolása
1. Vizsgálja meg a és a crushes, darabok, vízjel sérülés vagy más egyértelmű károkért csomagolás használatos. Ha a lista vagy a csomag súlyosan sérült, nem a megnyitása. Adjon [forduljon a Microsoft Support](storsimple-contact-microsoft-support.md) segítséget ellenőrzéséhez, hogy az eszköz működőképes állapotban van.
2. A külső megnyitásához, és hajtsa végre a megfelelő elsődleges és a EBOD házakat két mező. Az elsődleges és a EBOD ház most csomagolja ki. Az alábbi ábrán a ház egyik kicsomagolt nézetét.
   
    ![Bontsa ki a tárolóeszköz](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **A tárolóeszköz kibontott nézete**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Csomagolási mezőbe |
   |   2 |SAS-kábel (a Kellékek és kábelek tálca) |
   |   3 |Alsó hab |
   |   4 |Eszköz |
   |   5 |Felső hab |
   |   6 |Kiegészítő mezőben |
3. Kicsomagolása után a két mező, győződjön meg arról, hogy rendelkezik:
   
   * (az elsődleges ház és EBOD ház is két külön mezőkbe) 1 elsődleges ház
   * 1 EBOD ház
   * 4 tápkábelek, minden mezőbe 2
   * 2 SAS-kábel (csatlakozni az elsődleges ház EBOD ház)
   * 1 fordított kábel
   * 2 soros konzol kábelek
   * a soros hozzáféréshez 1 soros-USB-konverter
   * 4 QSFP-az-adapterek SFP + 10 GbE hálózati adapterrel való használatra
   * 2 állványra csatlakoztatási kits (4 ügyféloldali sínek hardver, 2 minden egyes elsődleges ház és EBOD ház csatlakoztatni), minden mezőbe 1
   * Első lépések dokumentáció
     
     Ha Ön nem kapta meg az elemek, a fent felsorolt [forduljon a Microsoft Support](storsimple-contact-microsoft-support.md).  

A következő lépés, hogy az eszköz állvány csatlakoztatást.

## <a name="rack-mount-your-storsimple-8600-device"></a>Állvány csatlakoztatást a StorSimple 8600 eszköz
A következő lépések segítségével a StorSimple 8600 tárolóeszköz telepítése az első és hátsó bejegyzéseket szabványos 19 hüvelykes szekrényben. Az eszköz két ház rendelkezik: egy elsődleges ház és egy EBOD ház. Mindkét esetben kell lennie állványra szerelt.

A telepítés több lépést ismertet, amelyek a következő eljárások áll.

> [!IMPORTANT]
> StorSimple eszközökhöz kell lennie az állványra szerelt megfelelő működéséhez.
> 
> 

### <a name="site-preparation"></a>Hely előkészítése
A ház szabványos 19 hüvelykes szekrényben, amely rendelkezik az első és hátsó bejegyzéseket egyaránt telepíteni kell. A következő eljárással állvány telepítés előkészítéséhez.

#### <a name="to-prepare-the-site-for-rack-installation"></a>A hely állvány telepítés előkészítéséhez
1. Ellenőrizze, hogy az elsődleges és a EBOD ház nyugalmi biztonságosan egy egyszerű, stabil és szintű munkahelyi felületén (vagy hasonlót).
2. Ellenőrizze, hogy a hely, ahol kíván beállítani egy független forrás vagy a szünetmentes tápegység (UPS) állványba Power terjesztési egységek (PDU) szabványos AC tápellátás.
3. Győződjön meg arról, hogy egy 4U (2 X 2U) bővítőhely, amelyben a ház csatlakoztatni kívánja az állvány érhető el.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png)![nagy súlyozást ikon](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **figyelmeztetés!**

 Győződjön meg arról, hogy rendelkezik-e két személynek kezelése a súly, ha az eszköz beállítását manuálisan vannak kezelése érhető el. A teljesen konfigurált ház is mérjük legfeljebb 32 kg (70 lbs.).

### <a name="rack-prerequisites"></a>Állvány Előfeltételek
A kabinet szabványos 19 hüvelykes szekrényben telepítési készültek, a ház:

* A post a feladás egy vagy több állványra 27.84 hüvelyk minimális mélysége
* Az eszköz 32 kg legnagyobb súlyú
* Maximális hátsó nyomás 5 Pascal (0,5 mm vízjel mérőműszer)

### <a name="rack-mounting-rail-kit"></a>Állvány csatlakoztatása oldalon csomag
Sínek csatlakoztatása készlete fognak adni a 19 hüvelykes állvány kabinet való használatra. A maximális ház súly kezelni a sínek lettek tesztelve. Ezek sínek lehetővé teszi több házakat belül az állványra terület adatvesztés nélkül telepítési. Először telepítse a EBOD ház.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>A sínen a EBOD ház telepítése
1. Hajtsa végre ezt a lépést, csak akkor, ha a belső sínek nincsenek telepítve az eszközön. A belső sínek általában gyári vannak telepítve. Ha sínek nincsenek telepítve, majd telepítse a bal oldali-oldalon, és jobb-oldalon diák a ház váz oldalán. Hat metrika csavart segítségével mindkét oldalon csatolja azokat. Tájolás érdekében az oldalon diák megjelölve **LH – első** és **RH – első**, és a célból, hogy a ház hátulról dobozának kúpos.
   
    ![Ház váz oldalon diák csatolása](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **A partnerek a ház oldalon diák csatolása**
   
   | Címke | Leírás |
   | --- | --- |
   |  1 |M 3 x 4 gomb-head csavart |
   |  2 |Váz diák |
2. A bal oldalon és a jobb oldalon szerelvények csatolása a állvány kabinet függőleges tagokat. A szögletes megjelölve **LH**, **RH**, és **Ez az oldal fel** végigvezeti a felhasználót a megfelelő tájolását.
3. Keresse meg az oldalon PIN-kód az első és hátsó oldalon szerelvény. Az oldalon való elhelyezéséhez állvány helyek között, és a PIN-kód beillesztése az első és hátsó-állvány post függőleges tag lyuk kiterjesztése. Győződjön meg arról, hogy oldalon szerelvény be-e szintjét.
4. Biztonságos az állványra oldalon szerelvény függőleges tagok két a megadott metrika csavart használatával. Az első és hátsó a egy csavart használja.
5. Ismételje meg ezeket a lépéseket a másik oldalon szerelvény.
   
     ![Állvány kabinet oldalon diák csatolása](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Az állványra oldalon szerelvények csatolása**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Rögzítéssel csavart |
   |   2 |Négyzetes-lyuk első állvány post csavart |
   |   3 |PIN-kód helye első oldalon balra |
   |   4 |Rögzítéssel csavart |
   |   5 |Bal oldali hátsó oldalon hely PIN-kód |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>A EBOD ház, az állvány csatlakoztatása
Az imént telepített állvány sínek, végezze el az alábbi lépések végrehajtásával csatlakoztassa a EBOD ház, az állvány.

#### <a name="to-mount-the-ebod-enclosure"></a>A EBOD ház csatlakoztatása
1. A segítségnyújtó növekedési a ház és igazíthatja a állvány sínek.
2. A ház gondosan beilleszteni a sínek, és majd leküldeni teljesen az állványra a kabinetfájl.
   
    ![Az állványra eszköz beszúrása](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **A ház, az állvány csatlakoztatása**
3. Távolítsa el a bal és jobb első nyomkarima caps modulba húzza a caps szabad. A nyomkarima caps egyszerűen illesztés fekszenek helyezik.
4. A ház biztonságos az állványra be egy megadott Phillips-head csavart keresztül minden nyomkarima, bal és jobb telepítésével.
5. Telepítse a nyomkarima caps lenyomásával őket egy helyen, és azokat a hely illesztésének.
   
     ![Nyomkarima caps telepítése](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **A nyomkarima caps telepítése**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Ház rögzítési csavart |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Az elsődleges ház, az állvány csatlakoztatása
Miután befejezte a EBOD ház csatlakoztatni, szüksége lesz a lépéseket az elsődleges ház csatlakoztatni.

> [!NOTE]
> * Lehetséges, hogy néhány üres tárhelyek, az állvány az elsődleges ház és a EBOD ház között.
> * A megadott 2m SAS-kábel segítségével csatlakozzon az elsődleges ház a EBOD ház.
> * A head egység EBOD egységhez relatív kevésbé megkötés van. Ezért az elsődleges ház helyezhető a felső tárhely és az alábbi EBOD ház – vagy fordítva.
> 
> 

A következő lépés, hogy a tápellátáshoz, a hálózati és a soros hozzáféréshez bekábelezésére.

## <a name="cable-your-storsimple-8600-device"></a>A StorSimple 8600 bekábelezése
Az alábbi eljárások ismertetik a StorSimple 8600 bekábelezése a tápellátáshoz, a hálózati és a soros kapcsolatok.

### <a name="prerequisites"></a>Előfeltételek
Az eszköz bekábelezése megkezdése előtt szüksége lesz:

* Az elsődleges ház és a EBOD ház teljesen kicsomagolása
* 4 tápkábelek (2 minden elsődleges és a EBOD ház) az eszköz használati utasításának megfelelően
* Az eszközt, amellyel a EBOD ház az elsődleges házhoz pedig 2 SAS-kábel
* Hozzáférés 2 Áramelosztó egységekből (PDU-k) (ajánlott)
* Hálózati kábel
* A megadott soros kábelek
* Soros-USB-konverter a megfelelő illesztőprogram telepítve a számítógépen (ha szükséges)
* A megadott 4 QSFP-a-adapterek SFP + 10 GbE hálózati adapterrel való használatra
* [Támogatott hardveres a 10 GbE hálózati adapterek a StorSimple eszköz](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS- és Power kábelek
Az eszköz elsődleges ház és egy EBOD ház is rendelkezik. Ehhez a soros csatlakozású SCSI (SAS) kapcsolódási és power együtt kábel egységeket.

Ez az eszköz beállításához először, SAS kábelek először hajtsa végre a lépést, és fejezze be az energiagazdálkodási kábelek lépéseit.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Hálózati kábel
Az eszköz aktív-készenléti állapotban lévő konfigurációban van: az adott időpontban egy tartományvezérlő modul aktív és feldolgozási összes lemez és hálózat műveletek során a tartományvezérlő modul készenléti állapotba. Tartományvezérlő hiba esetén a készenléti vezérlő azonnal aktiválódik, és továbbra is a lemez- és hálózati műveletek.

A redundáns vezérlő feladatátvételi támogatja, az eszköz hálózati bekábelezése, ahogy az az alábbi lépéseket kell.

#### <a name="to-cable-for-network-connection"></a>A hálózati kapcsolat bekábelezése
1. Az eszköz minden tartományvezérlőn hat hálózati adapterrel rendelkezik: négy 1 GB/s és a két 10 GB/s Ethernet portokat. Tekintse át az alábbi ábrán látható, az adatok portokat a csatlakozópanel az eszköz azonosítására.
   
     ![Csatlakozópanel 8600 eszköz](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Az eszköz visszaállítása a megjelenítő az adatok portok**
   
   | Címke | Leírás |
   | --- | --- |
   |   0,1,4,5 |1 GbE hálózati adapterek |
   |   2,3 |10 GbE hálózati adapterek |
   |   6 |Soros portokhoz |
2. Tekintse meg a következő ábra a hálózati kábeleket. (A minimális hálózati konfiguráció folytonos kék vonal is látható. A magas rendelkezésre állás és teljesítmény szükséges további konfigurációs látható szaggatott vonal.)

![A hálózati 4U bekábelezésére](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Az eszköz kábelek hálózati**

| Címke | Leírás |
| --- | --- |
| A |Internet-hozzáféréssel rendelkező LAN |
| B |A vezérlő 0 |
| C# |PCM 0 |
| D |1. vezérlő |
| E |PCM 1 |
| F |EBOD vezérlő 0 |
| G |1. EBOD vezérlő |
| H, I |Gazdagépek (például a fájlkiszolgálók) |
| 0-5 |Hálózati illesztők |
| 6 |Elsődleges ház |
| 7 |EBOD ház |

Az eszköz kábelek, a minimális konfigurációs kell rendelkeznie:

* Legalább két hálózati adapterrel minden tartományvezérlőn, a felhő hozzáféréshez és az egyikben iSCSI csatlakoztatva. A DATA 0 port automatikusan engedélyezve és konfigurálva, az eszköz soros konzolon keresztül. DATA 0, leszámítva a adatokat egy másik portra is kell megadni a klasszikus Azure portálon keresztül. Ebben az esetben a DATA 0 csatlakozzon az elsődleges helyi hálózathoz (Internet-hozzáféréssel rendelkező hálózati) porton. Az egyéb adatok portok SAN/iSCSI LAN (VLAN) szegmens a hálózat, attól függően, hogy a megfelelő szerepkör lehet csatlakoztatni.
* Minden egyes tartományvezérlő azonos csatolóinak kapcsolódik ugyanazon a hálózaton rendelkezésre állásának biztosításához, ha egy tartományvezérlő feladatátvételt hajt végre. Például ha kíván csatlakozni a DATA 0 és a vezérlőket a DATA 3, kell a megfelelő adatok 0 és a DATA 3 csatlakozzon a többi tartományvezérlőn.

Vegye figyelembe a magas rendelkezésre állás és teljesítmény:

* Ha lehetséges, állítsa be két hálózati csatoló a felhőelérést (1 gbe-s) és az iSCSI (ajánlott 10 GbE) egy másik pár minden tartományvezérlőn.
* Ha lehetséges, kapcsolódó hálózati adapterek minden vezérlő két különböző kapcsolókhoz egy kapcsoló meghibásodása elleni rendelkezésre állásának biztosításához. Az ábra azt mutatja be, a két 10 GbE hálózati adapterek, DATA 2 és a DATA 3 két különböző kapcsolókhoz csatlakozik, minden tartományvezérlőről. További információkért tekintse meg a **hálózati illesztőt** alatt a [a StorSimple eszköz követelményei magas rendelkezésre állású](storsimple-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Ha a 10 GbE hálózati adapterek SFP + adó használ, használja a megadott QSFP-SFP + adapterek. További információkért látogasson el [támogatott hardvert a 10 GbE hálózati adapterek a StorSimple eszköz](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Soros port kábelek
A következő lépésekkel bekábelezése a soros port.

#### <a name="to-cable-for-serial-connection"></a>A soros kapcsolat bekábelezése
1. Az eszköz soros minden vezérlő egy csavarkulcsot ábrázoló ikon által azonosított-porttal rendelkezik. A soros portokhoz megkereséséhez tekintse meg a bemutató ábrát, az adatok portok az eszköz hátulján olvasható.
2. Azonosítsa az eszköz csatlakozópanel aktív vezérlőhöz. Egy villogó kék LED azt jelzi, hogy a vezérlő aktív.
3. A megadott soros kábelt (ha szükséges, az USB – soros átalakító hordozható számítógépre), és a konzol vagy a számítógép (a terminálemuláció az eszközhöz) kapcsolódjanak a soros port az aktív vezérlő.
4. A sorozat-USB-illesztőprogramot (az eszköz) telepítése a számítógépre.
5. Az alábbiak szerint állíthatja be a soros kapcsolatot:
   
   * 115 200 átviteli
   * 8 adatbitek
   * 1 stop bit
   * Nincs paritás
   * Folyamatvezérlés beállítása **nincs**
6. Győződjön meg arról, hogy működik-e a kapcsolat a konzolon Enter billentyűkombináció lenyomásával. A soros konzol menü megjelenjen-e.

> [!NOTE]
> **Lights-Out felügyeleti:** telepítésekor az eszköz van távoli adatközpontban vagy korlátozott hozzáférésű számítógép helyiségben, győződjön meg arról, a soros kapcsolatok mindkét tartományvezérlők mindig csatlakoztatva a soros konzol kapcsoló vagy hasonló berendezések. Ez lehetővé teszi a sávon kívüli a távvezérlés és a támogatási műveletek esetén a hálózati megszakítása vagy váratlan meghibásodások esetén.
> 
> 

Az eszköz a tápellátáshoz, a hálózati hozzáférés és a soros kapcsolat kábelek befejeződött. A következő lépés, hogy az eszköz szoftverének konfigurálásához.

## <a name="next-steps"></a>Következő lépések
Most már készen áll a [központi telepítése és konfigurálása a helyszíni StorSimple eszköz](storsimple-deployment-walkthrough-u2.md).

