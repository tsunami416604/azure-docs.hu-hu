---
title: Telepítse a Microsoft Azure StorSimple 8100 sorozatú eszköz |} A Microsoft Docs
description: Ismerteti, hogyan lehet kicsomagolása, állványra és a StorSimple 8100 sorozatú eszköz kábelezése üzembe helyezése és a szoftver konfigurálása előtt.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38701942"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>Kicsomagolása, állványra csatlakoztatást és a StorSimple 8100 sorozatú eszköz kábelezése
## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple 8100 egy egyetlen lemezház, állványra szerelt eszköz. Ez az oktatóanyag azt ismerteti, hogyan kicsomagolása, állványra-szerelheti és kábellel StorSimple 8100-as eszköz hardverének beállítása és a StorSimple eszköz üzembe helyezése előtt.

## <a name="unpack-your-storsimple-8100-device"></a>A StorSimple 8100 sorozatú eszköz kicsomagolása
Az alábbi lépéseket a StorSimple 8100 tárolóeszköz kicsomagolása egyértelmű, részletes utasításokat biztosítanak. Ez az eszköz egyetlen lista tartalmazza a szükséges.

### <a name="prepare-to-unpack-your-device"></a>Csomagolja ki az eszköz előkészítése
Mielőtt, az eszköz kicsomagolása, tekintse át a következő információkat.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png)![nagy súly ikon](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **figyelmeztetés!**

1. Győződjön meg arról, hogy a két személynek a ház súlyát kezelhető, ha manuálisan is kezel. Egy teljes körűen konfigurált ház legfeljebb 32 kg (70 lbs.) is mérjük.
2. Helyezze el a box egy egybesimított, szolgáltatásiszint-felületen.

Ezután kövesse az alábbi lépéseket az eszköz kicsomagolása.

#### <a name="to-unpack-your-device"></a>Az eszköz kicsomagolása
1. Vizsgálja meg a box és a csomagolási habgumiból készült crushes, darabok, víz sérülés vagy bármely más nyilvánvaló sérülések. Ha a lista vagy a csomagolási súlyosan sérült, nem a megnyitása. Adjon [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) segítséget ellenőrzéséhez, hogy az eszköz működőképes állapotban van.
2. Csomagolja ki a mezőbe. Az alábbi képen a StorSimple-eszköz a kicsomagolt nézetét mutatja be.
   
     ![Csomagolja ki a tárolóeszköz](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **A tárolóeszköz kicsomagolt nézete**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Balí se mezőbe |
   |   2 |Alsó habgumiból készült |
   |   3 |Eszköz |
   |   4 |Felső habgumiból készült |
   |   5 |Kiegészítő mezőben |
3. Után kicsomagolása, a box, győződjön meg arról, hogy:
   
   * egyetlen lemezház 1 eszköz
   * 2 tápkábelek
   * 1 fordított Ethernet-kábelek
   * 2 soros kábelt
   * a soros hozzáféréshez 1 sorozat – USB-átalakító
   * 1 hamisíthatatlan T10 csavarhúzóra
   * 4 QSFP-az-SFP + adapter 10 GbE hálózati adapterek való használatra
   * 1. állvány-csatlakoztatási kit (2 ügyféloldali rails rendelkező csatlakoztatásával hardver)
   * Első lépések a dokumentáció
     
     Ha Ön nem kapott az elemek a fent felsorolt [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md).

A következő lépés, hogy az eszköz állvány-csatlakoztatási.

## <a name="rack-mount-your-storsimple-8100-device"></a>A StorSimple 8100 sorozatú eszköz állvány-csatlakoztatás
A következő lépésekkel telepítse a StorSimple 8100 tárolóeszköz, az első és hátsó bejegyzések standard 19 hüvelyk állvány. A StorSimple 8100 sorozatú eszköz rendelkezik egy önálló elsődleges ház.

A telepítés több lépést tárgyalt, amelyek a következő eljárások áll.

> [!IMPORTANT]
> A StorSimple-eszközök kell lennie az állványra szerelt megfelelő működéséhez.
> 
> 

### <a name="prepare-the-site"></a>A hely előkészítése
Az eszköz egy standard szintű 19 hüvelyk állványra, amely rendelkezik az első és hátsó bejegyzések is telepíteni kell. A következő eljárással rack telepítés előkészítéséhez.

#### <a name="to-prepare-the-site-for-rack-installation"></a>A hely állvány telepítés előkészítéséhez
1. Győződjön meg arról, hogy az eszköz biztonságos alapja a strukturálatlan, stabil és szintű munkahelyi surface (vagy hasonlót).
2. Ellenőrizze, hogy a hely, ahol szeretne beállítani rendelkezik-e a szabványos hálózati áramellátás Visszaállt egy független forrás vagy egy állvány power terjesztési egység (PDU) szünetmentes tápegység (UPS).
3. Győződjön meg arról, hogy egy 2U tárolóhely érhető el az állványra szerelt, amelyben szeretné az eszköz csatlakoztatása.

![Figyelmeztetés ikon](./media/storsimple-safety/IC740879.png)![nagy súly ikon](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) **figyelmeztetés!**

Győződjön meg arról, hogy a két személynek a súly kezelése, ha azt manuálisan kezelik az eszköz beállítása rendelkezésre. Egy teljes körűen konfigurált ház legfeljebb 32 kg (70 lbs.) is mérjük.

### <a name="rack-prerequisites"></a>Állványra előfeltételei
A 8100-as ház készült egy standard szintű 19 hüvelyk állványra a kabinet telepítését:

* A bejegyzés a post rack 27.84 hüvelyk minimális mélységét.
* Az eszköz 32 kg legnagyobb súlyú
* 5 Pascal (0,5 mm víz mérőműszer) maximális vissza nyomás.

### <a name="rack-mounting-rail-kit"></a>Állvány csatlakoztatást sín csomag
Csatlakoztatási rails készlete áll rendelkezésre a 19 hüvelyk rack kabinet való használatra. A rails teszteltük maximális ház súlyát kezelni. Ezek a rails lehetővé teszi több ház belül az állványra szerelt terület adatveszteség nélküli telepítését.

#### <a name="to-install-the-device-on-the-rails"></a>A rails az eszköz telepítése
1. Hajtsa végre ezt a lépést, csak akkor, ha a belső rails nincsenek telepítve az eszközön. A belső rails általában gyári vannak telepítve. Rails nem települnek, ha ezután telepítse a bal oldali-sín és jobb-sín diák a ház váz oldalát. Ezek csatolás a hat metrika csavart használatával minden oldalán. Annak érdekében, a tájolás, a sín diák vannak megjelölve **LH – első** és **RH – első**, és érdekében, hogy a ház hátulról dobozának kúpos rendelkezik.<br/>
   
    ![Sín diák ház váz csatolása](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **A partnerek a ház belső sín diák csatolása**
   
    Címke | Leírás
    ----- | -----------
    1     | 3 x 4 millió csavart gomb-fő
    2     | Váz diák

2. A bal oldali külső sín és a külső megfelelő sín szerelvények csatolása az állvány kabinet függőleges tagok. A szögletes zárójelek lesznek megjelölve **LH**, **RH**, és **ezt oldal fel** végigvezeti a megfelelő tájolását.
3. Keresse meg az sín PIN-kód, az első és hátsó sín sestavení. A sín illeszkedjen az állvány-bejegyzések között, és a PIN-kód beillesztése az első és hátsó állvány post függőleges tag lyuk kiterjesztése. Győződjön meg arról, hogy a sín szerelvény szintjét.
4. A megadott metrika csavart közül kettő az védheti sín sestavení do az állványra szerelt függőleges tagok. Az előtér és a egy hátsó egy csavart használja.
5. Ismételje meg ezeket a lépéseket más sín sestavení.<br/>
   
     ![Sín diák kabinet rack csatolása](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Az állványra szerelt külső sín szerelvények csatolása**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Csavart korlátozását. |
   |   2 |Szögletes lyuk első rack post csavart |
   |   3 |Bal oldali sín első helyen PIN-kódok |
   |   4 |Csavart korlátozását. |
   |   5 |Bal oldali sín hátsó hely PIN-kódok |

### <a name="mounting-the-device-in-the-rack"></a>Csatlakoztassa az eszközt, az állvány egyik kiszolgálója
Az imént telepített rack rails használja, hajtsa végre az alábbi lépések végrehajtásával csatlakoztassa az eszközt, az állvány egyik kiszolgálója.

#### <a name="to-mount-the-device"></a>Az eszköz csatlakoztatása
1. Az asszisztens a ház lift- and, az állvány rails igazodva.
2. Gondosan helyezze az eszközt a rails, és majd leküldése az eszköz teljes mértékben az állványra szerelt kabinetfájl.<br/>
   
    ![Eszköz Beszúrás, az állvány egyik kiszolgálója](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **Csatlakoztassa az eszközt, az állvány egyik kiszolgálója**
3. Távolítsa el a bal és jobb első nyomkarima caps ingyenes caps kiindulásként. A nyomkarima caps egyszerűen alakzatot fekszenek beépülő modul.
4. A ház, az állvány egyik kiszolgálója biztonságos egy megadott Phillips – fő csavart keresztül minden egyes nyomkarima bal és jobb telepítésével.
5. Telepítse a nyomkarima caps lenyomásával őket egy helyen, és helyben illesztésének őket.<br/>
   
     ![Nyomkarima caps telepítése](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **A nyomkarima caps telepítése**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |A lemezház rögzítési csavart |

A következő lépés, hogy bekábelezése, hálózati és a soros hozzáféréshez.

## <a name="cable-your-storsimple-8100-device"></a>A StorSimple 8100 sorozatú eszköz kábelezése
A következő eljárások azt ismertetik, hogyan, hálózati és a soros kapcsolat a StorSimple 8100 sorozatú eszköz kábelezése.

### <a name="prerequisites"></a>Előfeltételek
Az eszköz a kábelezést elkezdéséhez lesz szüksége:

* A tárolóeszköz teljesen kicsomagolása és állványra szerelt.
* az eszköz használati utasításának megfelelően 2 kábelek
* A hozzáférést a (javasolt) 2 Áramelosztó egységek.
* Hálózati kábeleket
* A megadott soros kábelt
* Soros, USB átalakító a megfelelő illesztőprogram telepítve a számítógépen (ha szükséges)
* A megadott 4 QSFP-az-SFP + adapter 10 GbE hálózati adapterek való használatra
* [A StorSimple eszköz 10 GbE hálózati adapterek támogatott hardver](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Energiagazdálkodási kábelezése
Az eszköz redundáns energiagazdálkodási és hűtéssel modulok (PCMs) tartalmaz. Mindkét PCMs telepítve legyen, és csatlakozik a különböző áramforrásokhoz magas rendelkezésre állás biztosítása érdekében.

A következő lépésekkel bekábelezése teljesítményhez.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Hálózati kábelezése
Az eszköz egy aktív-készenléti konfigurációban áll: egy adott időpontban egy vezérlő modul aktív és feldolgozása közben a tartományvezérlő modul összes lemez és hálózat műveletek készenléti módban futhat. Ha egy tartományvezérlő sikertelen, a készenléti állapotban lévő vezérlőnek azonnal aktiválni, és továbbra is fennáll, a lemez és a hálózati műveletek.

Támogatja a zónaredundáns vezérlő feladatátvétele, kábelezése az eszköz hálózathoz, a következő lépésben ismertetett módon kell.

#### <a name="to-cable-for-network-connection"></a>A hálózati kapcsolat kábelezése
1. Az eszköz minden tartományvezérlőn hat hálózati adapterrel rendelkezik: négy 1 GB/s, és két 10 GB/s sebességű Ethernet-portokat. Azonosíthatja a különféle adatok portokat az eszköz a csatlakozópanel meghibásodása.
   
    ![A 8100-as eszközön csatlakozópanel meghibásodása](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Vissza az eszközt az adatok portok megjelenítése**
   
   | Címke | Leírás |
   | --- | --- |
   |   0,1,4,5 |1 GbE hálózati adapterek |
   |   2,3 |10 GbE hálózati adapterek |
   |   6 |Soros portok |
2. Az alábbi ábrában hálózati kábeleket. (A minimálisan hálózatkonfigurálás folytonos kék vonal jelenik meg. A magas rendelkezésre állás és teljesítmény szükséges további konfigurációs látható pontozott vonal.)

    ![Hálózat 2U eszköz kábelezése](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Hálózat, az eszköz kábelezése**

   |Címke | Leírás |
   |----- | ----------- |
   | A    | Helyi Internet-hozzáféréssel rendelkező hálózat |
   | B    | 0. vezérlő |
   | C    | PCM 0-BAN |
   | D    | 1. vezérlő |
   | E    | A PCM 1 |
   | F, G | Hosts |
   | 0-5  | Hálózati illesztők |



Ha az eszköz kábelezése, a minimális konfigurációs van szükség:

* Legalább két hálózati adaptert minden vezérlő egy cloud-Access és egy iSCSI-hálózathoz csatlakoznak. Az adat 0 port automatikusan engedélyezve és konfigurálva az eszköz soros konzolon keresztül. Adatok egy másik portra DATA 0, szereplőkkel is kell a klasszikus Azure portálon keresztül konfigurálható. Ebben az esetben a DATA 0 csatlakozás portot pedig az elsődleges hálózati Ébresztés (Internet-hozzáféréssel rendelkező hálózat). Az adatok más portok a hálózat, attól függően, a kívánt szerepkört az iSCSI SAN/LAN (VLAN) szegmens csatlakoztathatók.
* Minden vezérlő azonos adaptereinek ugyanazon a hálózaton rendelkezésre állásának biztosításához a vezérlő feladatátvétele esetén csatlakozik. Például ha az adatok 0 és DATA 3 az egyik vezérlő választja, meg kíván csatlakoztatni a megfelelő DATA 0 és DATA 3 a másik vezérlőre.

A magas rendelkezésre állást és teljesítményt szem előtt tartani:

* Ha lehetséges, minden vezérlő konfigurálása két hálózati adapter (1 GbE) felhőalapú hozzáférés és a egy másik pár iSCSI (10 GbE ajánlott).
* Ha lehetséges, csatlakoztassa hálózati adapterek minden vezérlő két különböző kapcsolókhoz egy kapcsoló meghibásodása elleni rendelkezésre állás biztosítása érdekében. Az ábrán látható a két 10 GbE hálózati adapterek, DATA 2 és DATA 3, minden tartományvezérlőről, két különböző kapcsolókhoz csatlakozik.

További információkért tekintse meg a **hálózati adapterek** alatt a [magas rendelkezésre állással, a StorSimple eszköz](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple).

> [!NOTE]
> Ha a 10 GbE hálózati adapterek SFP + adó használ, használja a megadott QSFP-SFP + adapter. További információért ugorjon [hardver támogatja a 10 GbE hálózati adapterek a StorSimple eszköz](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
> 
> 

### <a name="serial-port-cabling"></a>Soros portos kábelezése
A következő lépésekkel kábelezése a soros port.

#### <a name="to-cable-for-serial-connection"></a>A soros kapcsolat kábelezése
1. Az eszköz minden vezérlő, amely egy csavarkulcs ikon azonosítja a soros port van. Tekintse meg az az ábrán a [hálózati kábeleket](#network-cabling) szakaszban keresse meg a soros portok az eszköz a csatlakozópanel meghibásodása.
2. Az eszköz csatlakozópanel meghibásodása a az aktív vezérlő megkeresése. Egy villogó kék LED azt jelzi, hogy a vezérlő aktív.
3. A megadott soros kábelt (ha szükséges, a laptopján USB – soros átalakítója) használja, és csatlakozzon a konzolon vagy a számítógép (a terminálemulációs az eszközhöz), az aktív vezérlőt a soros port.
4. A sorozat – USB-illesztőprogramok (az eszköz rendszerrel szállított) telepítése a számítógépre.
5. Az alábbiak szerint állíthatja a soros kapcsolat: 115 200 átviteli, 8 adatbitek, 1 stop bit, nincs paritásos és adatfolyam vezérlés nincs értékre van állítva.
6. Győződjön meg arról, hogy a kapcsolat működik-e a konzol az Enter billentyű lenyomásával. A soros konzol menüjének kell megjelennie.

> [!NOTE]
> **Lights-Out felügyeleti**: Ha az eszköz telepítve van, korlátozott hozzáférésű számítógép szoba vagy egy távoli adatközpontban, győződjön meg arról, a soros kapcsolatokat mindkét vezérlő, mindig csatlakoztatva egy soros konzol kapcsoló vagy egy hasonló berendezések. Ez lehetővé teszi a sávon kívüli a távvezérlés és a támogatási operations, ha hálózati hibákat okozhatnak, vagy váratlan meghibásodások esetén.
> 
> 

Az eszköz most már csatlakoztatva, a, a hálózati hozzáférés és a soros kapcsolat. A következő lépés, hogy konfigurálja a szoftvert, és az eszköz üzembe helyezése.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [telepítse és konfigurálja a helyszíni StorSimple eszköz](storsimple-8000-deployment-walkthrough-u2.md).

