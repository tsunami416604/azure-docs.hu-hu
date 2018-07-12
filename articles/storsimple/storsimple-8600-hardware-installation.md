---
title: Telepítse a Microsoft Azure StorSimple 8600 sorozatú eszköz |} A Microsoft Docs
description: Ismerteti, hogyan lehet kicsomagolása, állványra és a StorSimple 8600 sorozatú eszköz kábelezése üzembe helyezése és a szoftver konfigurálása előtt.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 5a8b460441323cb668a3d9939cce434636afc44d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38719005"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>Kicsomagolása, állványra csatlakoztatást és a StorSimple 8600 sorozatú eszköz kábelezése
## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple 8600-as kettős ház eszköz, és a egy elsődleges és a egy EBOD ház áll. Ez az oktatóanyag azt ismerteti, hogyan kicsomagolása, állványra-szerelheti és kábellel StorSimple 8600-as eszköz hardveres előtt konfigurálása a StorSimple szoftver.

## <a name="unpack-your-storsimple-8600-device"></a>A StorSimple 8600 sorozatú eszköz kicsomagolása
Az alábbi lépéseket a StorSimple 8600-as tárolóeszköz kicsomagolása a világos, részletes utasításokat biztosítanak. Ez az eszköz két mezőbe, egyet az elsődleges ház és a egy másikat a EBOD ház tartalmazza a szükséges. E két mezőt egyetlen mező majd kerülnek.

### <a name="prepare-to-unpack-your-device"></a>Csomagolja ki az eszköz előkészítése
Mielőtt, az eszköz kicsomagolása, tekintse át a következő információkat.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png)![nagy súly ikon](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **figyelmeztetés!**

1. Győződjön meg arról, hogy a két személynek a súlyt az eszköz kezelése, ha manuálisan is kezelése érhető el. Egy teljes körűen konfigurált ház legfeljebb 32 kg (70 lbs.) is mérjük.
2. Helyezze el a box egy egybesimított, szolgáltatásiszint-felületen.

Ezután kövesse az alábbi lépéseket az eszköz kicsomagolása.

#### <a name="to-unpack-your-device"></a>Az eszköz kicsomagolása
1. Vizsgálja meg a box és a csomagolási habgumiból készült crushes, darabok, víz sérülés vagy bármely más nyilvánvaló sérülések. Ha a lista vagy a csomagolási súlyosan sérült, nem a megnyitása. Adjon [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) segítséget ellenőrzéséhez, hogy az eszköz működőképes állapotban van.
2. Nyissa meg a külső mezőbe, és ezután vegye ki a megfelelő elsődleges és a EBOD házakat két mező. Az elsődleges és a EBOD ház most kicsomagolása. A következő ábrán látható, a házak egyik kicsomagolt nézetét.
   
    ![Csomagolja ki a tárolóeszköz](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **A tárolóeszköz kicsomagolt nézete**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Balí se mezőbe |
   |   2 |SAS-kábel (a Kellékek és kábelek tálca) |
   |   3 |Alsó habgumiból készült |
   |   4 |Eszköz |
   |   5 |Felső habgumiból készült |
   |   6 |Kiegészítő mezőben |
3. Kicsomagolása után a két mező, győződjön meg arról, hogy:
   
   * (az elsődleges ház és EBOD ház is két külön mezőkben) 1 elsődleges ház
   * EBOD 1 ház
   * 4 tápkábelek, minden mezőben 2
   * 2 SAS-kábel (csatlakozni az elsődleges ház EBOD ház)
   * 1 fordított Ethernet-kábelek
   * 2 soros kábelt
   * a soros hozzáféréshez 1 sorozat – USB-átalakító
   * 4 QSFP-az-SFP + adapter 10 GbE hálózati adapterek való használatra
   * 2 állványra csatlakoztatási kits (4 ügyféloldali rails rendelkező csatlakoztatásával hardver, 2 minden egyes elsődleges ház és EBOD ház), minden mezőben 1
   * Első lépések dokumentációja
     
     Ha Ön nem kapott az elemek a fent felsorolt [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md).  

A következő lépés, hogy az eszköz állvány-csatlakoztatási.

## <a name="rack-mount-your-storsimple-8600-device"></a>A StorSimple 8600 sorozatú eszköz állvány-csatlakoztatás
A következő lépésekkel telepítse a StorSimple 8600-as tárolóeszköz, az első és hátsó bejegyzések standard 19 hüvelyk állvány. Ez az eszköz két ház tartalmaz: egy elsődleges ház és a egy EBOD ház. Mindkét kell lennie állványra szerelt.

A telepítés több lépést tárgyalt, amelyek a következő eljárások áll.

> [!IMPORTANT]
> A StorSimple-eszközök kell lennie az állványra szerelt megfelelő működéséhez.
> 
> 

### <a name="site-preparation"></a>Hely előkészítése
A ház egy standard szintű 19 hüvelyk állványra, amely rendelkezik az első és hátsó bejegyzések is telepíteni kell. A következő eljárással rack telepítés előkészítéséhez.

#### <a name="to-prepare-the-site-for-rack-installation"></a>A hely állvány telepítés előkészítéséhez
1. Győződjön meg arról, hogy az elsődleges és a EBOD ház pihenő biztonságosan egy egybesimított, stabil és szintű munkahelyi felületén (vagy hasonlót).
2. Ellenőrizze, hogy a hely, ahol szeretne beállítani rendelkezik-e a szabványos hálózati áramellátás Visszaállt a egy független forrás vagy a szünetmentes tápegység (UPS) állványba szerelt Power terjesztési Unit (PDU).
3. Győződjön meg arról, hogy egy 4U (2 X 2U) a tárolóhely érhető el az állványra szerelt, amelyben a ház csatlakoztatni kíván.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png)![nagy súly ikon](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) **figyelmeztetés!**

 Győződjön meg arról, hogy a két személynek a súly kezelése, ha azt manuálisan kezelik az eszköz beállítása rendelkezésre. Egy teljes körűen konfigurált ház legfeljebb 32 kg (70 lbs.) is mérjük.

### <a name="rack-prerequisites"></a>Állványra előfeltételei
A ház tervezett egy standard szintű 19 hüvelyk állványra a kabinet telepítését:

* A bejegyzés a post rack 27.84 hüvelyk minimális mélysége
* Az eszköz 32 kg legnagyobb súlyú
* Maximális vissza nyomással 5 Pascal (0,5 mm víz mérőműszer)

### <a name="rack-mounting-rail-kit"></a>Állvány csatlakoztatást sín csomag
Csatlakoztatási rails készletét biztosítjuk a 19 hüvelyk rack kabinet való használatra. A rails teszteltük maximális ház súlyát kezelni. Ezek a rails lehetővé teszi az állványra szerelt lemezterülete megszakadása nélkül több ház telepítését. Először telepítse a EBOD ház.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Az EBOD ház telepíthető a rails
1. Hajtsa végre ezt a lépést, csak akkor, ha a belső rails nincsenek telepítve az eszközön. A belső rails általában gyári vannak telepítve. Rails nem települnek, ha ezután telepítse a bal oldali-sín és jobb-sín diák a ház váz oldalát. Ezek csatolás a hat metrika csavart használatával minden oldalán. Annak érdekében, a tájolás, a sín diák vannak megjelölve **LH – első** és **RH – első**, és érdekében, hogy a ház hátulról dobozának kúpos rendelkezik.
   
    ![Sín diák ház váz csatolása](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **A partnerek a ház sín diák csatolása**
   
   | Címke | Leírás |
   | --- | --- |
   |  1 |3 x 4 millió csavart gomb-fő |
   |  2 |Váz diák |
2. A bal oldali sín és a megfelelő sín szerelvények csatolása az állvány kabinet függőleges tagok. A szögletes zárójelek lesznek megjelölve **LH**, **RH**, és **ezt oldal fel** végigvezeti a felhasználót a megfelelő tájolását.
3. Keresse meg az sín PIN-kód, az első és hátsó sín sestavení. A sín illeszkedjen az állvány-bejegyzések között, és a PIN-kód beillesztése az első és hátsó-rack post függőleges tag lyuk kiterjesztése. Győződjön meg arról, hogy a sín szerelvény szintjét.
4. Gondoskodhat az állványra sín szerelvény függőleges tagok két a metrika csavart megadott. Az előtér és a egy hátsó egy csavart használja.
5. Ismételje meg ezeket a lépéseket más sín sestavení.
   
     ![Sín diák kabinet rack csatolása](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Az állványra szerelt sín szerelvények csatolása**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Csavart korlátozását. |
   |   2 |Szögletes lyuk első rack post csavart |
   |   3 |Az előtérbeli sín hely PIN-kódok balra |
   |   4 |Csavart korlátozását. |
   |   5 |Bal oldali hátsó sín hely PIN-kódok |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Az EBOD-lemezház, az állvány egyik kiszolgálója csatlakoztatása
Az imént telepített rack rails használja, hajtsa végre az alábbi lépések végrehajtásával csatlakoztassa a EBOD ház, az állvány egyik kiszolgálója.

#### <a name="to-mount-the-ebod-enclosure"></a>Az EBOD ház csatlakoztatása
1. Az asszisztens a ház lift- and, az állvány rails igazodva.
2. A ház gondosan beszúrása a rails, és ezután leküldése, teljesen az állványra szerelt kabinetfájl.
   
    ![Eszköz Beszúrás, az állvány egyik kiszolgálója](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **A ház, az állvány egyik kiszolgálója csatlakoztatása**
3. Távolítsa el a bal és jobb első nyomkarima caps ingyenes caps kiindulásként. A nyomkarima caps egyszerűen alakzatot fekszenek beépülő modul.
4. A ház biztonságos, az állvány egyik kiszolgálója egy megadott Phillips – fő csavart keresztül minden egyes nyomkarima bal és jobb telepítésével.
5. Telepítse a nyomkarima caps lenyomásával őket egy helyen, és illeszkedik őket a helyen.
   
     ![Nyomkarima caps telepítése](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **A nyomkarima caps telepítése**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |A lemezház rögzítési csavart |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Az elsődleges ház, az állvány egyik kiszolgálója csatlakoztatása
Miután befejezte a EBOD ház csatlakoztatni, szüksége lesz az elsődleges ház ugyanazokat a lépéseket követve csatlakoztassa.

> [!NOTE]
> * Lehetséges, hogy néhány üres tárolóhelyei, az állvány egyik kiszolgálója az elsődleges ház és a EBOD ház között.
> * A megadott 2m SAS-kábel használatával az elsődleges ház kapcsolódhat a EBOD ház.
> * Nincsenek a fő egység EBOD egységhez relatív kevésbé korlátozások nélkül. Ezért az elsődleges ház helyezhető a felső tárolóhely, és a lenti EBOD ház – és fordítva.
> 
> 

A következő lépés, hogy bekábelezése, hálózati és a soros hozzáféréshez.

## <a name="cable-your-storsimple-8600-device"></a>A StorSimple 8600 sorozatú eszköz kábelezése
A következő eljárások azt ismertetik, hogyan, hálózati és a soros kapcsolat a StorSimple 8600 sorozatú eszköz kábelezése.

### <a name="prerequisites"></a>Előfeltételek
Mielőtt megkezdené az eszköz bekábelezése, lesz szüksége:

* Az elsődleges ház és a EBOD ház teljesen kicsomagolása
* 4 tápkábelek (2. minden egyes az elsődleges és a EBOD ház), amely az eszköz használati utasításának
* Az EBOD ház és az elsődleges ház csatlakozni az eszközhöz megadott 2 SAS-kábel
* 2 Áramelosztó egységekből (PDU-k) (ajánlott) való hozzáférés
* Hálózati kábeleket
* A megadott soros kábelt
* Soros-USB converter a megfelelő illesztőprogram telepítve a számítógépen (ha szükséges)
* A megadott 4 QSFP-az-SFP + adapter 10 GbE hálózati adapterek való használatra
* [A StorSimple eszköz 10 GbE hálózati adapterek támogatott hardver](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS és Power kábelezése
Az eszköz rendelkezik az elsődleges ház és a egy EBOD ház. Ehhez a soros csatlakozású SCSI (SAS) kapcsolódási és power együtt bekábelezte egységeket.

Az eszköz beállítása az első alkalommal, amikor a SAS kábelezést először hajtsa végre a lépéseket, és végezze el a kábelezést power.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Hálózati kábelezése
Az eszköz egy aktív-készenléti konfigurációban áll: egy adott időpontban egy vezérlő modul aktív és feldolgozása közben a tartományvezérlő modul összes lemez és hálózat műveletek készenléti módban futhat. Vezérlő hiba esetén a készenléti állapotban lévő vezérlőnek azonnal aktiválja, és továbbra is a lemez és a hálózati műveletek.

Támogatja a zónaredundáns vezérlő feladatátvétele, az eszköz hálózati kábelezése, ahogyan az az alábbi lépéseket kell.

#### <a name="to-cable-for-network-connection"></a>A hálózati kapcsolat kábelezése
1. Az eszköz minden tartományvezérlőn hat hálózati adapterrel rendelkezik: négy 1 GB/s és a két 10 GB/s sebességű Ethernet portok. Tekintse meg a következő ábrán az adatok portjait a csatlakozópanel meghibásodása, az eszköz azonosítására.
   
     ![8600-as eszköz csatlakozópanel meghibásodása](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Vissza az eszközt, az adatok portok megjelenítése**
   
   | Címke | Leírás |
   | --- | --- |
   |   0,1,4,5 |1 GbE hálózati adapterek |
   |   2,3 |10 GbE hálózati adapterek |
   |   6 |Soros portok |
2. Az alábbi ábrában hálózati kábeleket. (A minimálisan hálózatkonfigurálás folytonos kék vonal jelenik meg. Magas rendelkezésre állás és teljesítmény szükséges további konfigurációs látható pontozott vonal.)

![Hálózat 4U eszköz kábelezése](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Hálózat, az eszköz kábelezése**

| Címke | Leírás |
| --- | --- |
| A |Helyi Internet-hozzáféréssel rendelkező hálózat |
| B |0. vezérlő |
| C |PCM 0-BAN |
| D |1. vezérlő |
| E |A PCM 1 |
| F |Az EBOD-vezérlő 0 |
| G |Az EBOD-vezérlő 1 |
| H I |Gazdagépek (például a fájlkiszolgálók) |
| 0-5 |Hálózati illesztők |
| 6 |Elsődleges ház |
| 7 |EBOD ház |

Ha az eszköz kábelezése, a minimális konfigurációs van szükség:

* Legalább két hálózati adaptert minden vezérlő egy cloud-Access és egy iSCSI-hálózathoz csatlakoznak. Az adat 0 port automatikusan engedélyezve és konfigurálva az eszköz soros konzolon keresztül. Adatok egy másik portra DATA 0, szereplőkkel is kell a klasszikus Azure portálon keresztül konfigurálható. Ebben az esetben a DATA 0 csatlakozás portot pedig az elsődleges hálózati Ébresztés (Internet-hozzáféréssel rendelkező hálózat). Az adatok más portok a hálózat, attól függően, a kívánt szerepkört az iSCSI SAN/LAN (VLAN) szegmens csatlakoztathatók.
* Minden vezérlő azonos adaptereinek ugyanazon a hálózaton rendelkezésre állásának biztosításához a vezérlő feladatátvétele esetén csatlakozik. Például ha az adatok 0 és DATA 3 az egyik vezérlő választja, meg kíván csatlakoztatni a megfelelő DATA 0 és DATA 3 a másik vezérlőre.

A magas rendelkezésre állást és teljesítményt szem előtt tartani:

* Ha lehetséges, minden vezérlő konfigurálása két hálózati adapter (1 GbE) felhőalapú hozzáférés és a egy másik pár iSCSI (10 GbE ajánlott).
* Ha lehetséges, csatlakoztassa hálózati adapterek minden vezérlő két különböző kapcsolókhoz egy kapcsoló meghibásodása elleni rendelkezésre állás biztosítása érdekében. Az ábrán látható a két 10 GbE hálózati adapterek, DATA 2 és DATA 3, minden tartományvezérlőről, két különböző kapcsolókhoz csatlakozik. További információkért tekintse meg a **hálózati adapterek** alatt a [magas rendelkezésre állással, a StorSimple eszköz](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Ha a 10 GbE hálózati adapterek SFP + adó használ, használja a megadott QSFP-SFP + adapter. További információért ugorjon [hardver támogatja a 10 GbE hálózati adapterek a StorSimple eszköz](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Soros portos kábelezése
A következő lépésekkel kábelezése a soros port.

#### <a name="to-cable-for-serial-connection"></a>A soros kapcsolat kábelezése
1. Az eszköz minden vezérlő, amely egy csavarkulcs ikon azonosítja a soros port van. A soros portok megkereséséhez tekintse meg az ábrát, az adatok portok az eszköz található.
2. Az eszköz csatlakozópanel meghibásodása a az aktív vezérlő megkeresése. Egy villogó kék LED azt jelzi, hogy a vezérlő aktív.
3. A megadott soros kábelt (ha szükséges, a laptopján USB – soros átalakítója) használja, és csatlakozzon a konzolon vagy a számítógép (a terminálemulációs az eszközhöz), az aktív vezérlőt a soros port.
4. A sorozat – USB-illesztőprogramok (az eszköz rendszerrel szállított) telepítése a számítógépre.
5. Állítsa be a soros kapcsolat a következőképpen:
   
   * 115 200 átviteli
   * 8 adatbitek
   * 1 stop-bit
   * Nincs paritás
   * Folyamatvezérlés beállítása **None**
6. Győződjön meg arról, hogy a kapcsolat működik-e a konzol az Enter billentyű lenyomásával. A soros konzol menüjének kell megjelennie.

> [!NOTE]
> **Lights-Out felügyeleti:** telepítésekor az eszköz egy távoli adatközpontban vagy a korlátozott hozzáférésű számítógép szoba, győződjön meg arról, a soros kapcsolatokat mindkét vezérlő, mindig csatlakoztatva egy soros konzol kapcsoló vagy egy hasonló berendezések. Ez lehetővé teszi a sávon kívüli a távvezérlés és a támogatási műveletek hálózati megszakítása vagy váratlan meghibásodások esetén.
> 
> 

Elvégezte az eszközt, a hálózati hozzáférés és a soros kapcsolat a kábelezést. A következő lépés, hogy az eszköz szoftverének konfigurálásához.

## <a name="next-steps"></a>További lépések
Most már készen áll [telepítse és konfigurálja a helyszíni StorSimple eszköz](storsimple-8000-deployment-walkthrough-u2.md).

