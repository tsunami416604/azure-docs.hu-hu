---
title: A Microsoft Azure StorSimple 8100 eszköz telepítése
description: A cikk azt ismerteti, hogy a szoftver telepítése és konfigurálása előtt hogyan csomagolhatja ki, állványozhatja és kábelezheti a StorSimple 8100 eszközt.
author: alkohli
ms.assetid: 6098a01e-c031-488a-a8d7-0b607ce665e1
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 113b72ddf7e5d508c8a0b577d4004d4fbd83e8e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267585"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8100-device"></a>A StorSimple 8100 eszköz kicsomagolása, rackbe szerelhető és kábelezése

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple 8100 egyetlen házra szerelt eszköz. Ez az oktatóanyag bemutatja, hogyan csomagolhatja ki, állványra szerelhető és kábelezheti a StorSimple 8100 eszközhardvert a StorSimple-eszköz konfigurálása és telepítése előtt.

## <a name="unpack-your-storsimple-8100-device"></a>A StorSimple 8100 eszköz kicsomagolása
A következő lépések világos és részletes útmutatást nyújtanak a StorSimple 8100 tárolóeszköz kicsomagolásához. Az eszköz egyetlen dobozban érkezik.

### <a name="prepare-to-unpack-your-device"></a>Felkészülés a készülék kicsomagolására
Az eszköz kicsomagolása előtt tekintse át az alábbi információkat.

![Figyelem](./media/storsimple-safety/IC740879.png)![Icon nehéz](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) súlyú ikon **FIGYELEM!**

1. Győződjön meg arról, hogy két ember áll rendelkezésre a ház súlyának kezeléséhez, ha manuálisan kezeli. Egy teljesen konfigurált ház súlya akár 32 kg (70 lbs.).
2. Helyezze a csomagot egy sima, vízszintes felületre.

Ezután hajtsa végre az alábbi lépéseket az eszköz kicsomagolásához.

#### <a name="to-unpack-your-device"></a>A készülék kicsomagolása
1. Vizsgálja meg a dobozt és a térkitöltő anyagot, hogy nincsenek-e rajta ütődés, vágás, nedvesség vagy más egyértelmű sérülés nyomai. Ha a csomagon jelentős sérülések láthatók, ne nyissa ki. Kérjük, lépjen kapcsolatba a [Microsoft támogatási szolgálatával,](storsimple-8000-contact-microsoft-support.md) és segítsen felmérni, hogy az eszköz megfelelőállapotban van-e.
2. Bontsa ki a dobozt. Az alábbi képen a StorSimple-eszköz kicsomagolt nézete látható.
   
     ![A tárolóeszköz kicsomagolása](./media/storsimple-8100-hardware-installation/HCSUnpackyour2Udevice.png)
   
    **A tárolóeszköz kicsomagolt nézete**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Csomagolás |
   |   2 |Alsó hab |
   |   3 |Eszköz |
   |   4 |Felső hab |
   |   5 |Kiegészítő doboz |
3. A csomag felbontása után ellenőrizze, hogy megvannak-e a következők:
   
   * 1 egysoros eszköz
   * 2 tápkábel
   * 1 crossover Ethernet kábel
   * 2 soros konzolkábel
   * 1 soros-USB konverter a soros hozzáféréshez
   * 1 hamisíthatatlan T10 csavarhúzó
   * 4 QSFP-SFP+ adapter 10 GbE hálózati interfészhez
   * 1 rackbe szerelhető készlet (2 oldalsín szerelőhardverrel)
   * Első lépések dokumentációja
     
     Ha a fenti elemek közül egyik sem kapott értesítést, forduljon a [Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md)

A következő lépés az eszköz állványra szerelése.

## <a name="rack-mount-your-storsimple-8100-device"></a>Rackbe szerelhető a StorSimple 8100 eszköz
A Következő lépésekkel telepítheti a StorSimple 8100 tárolóeszközt egy szabványos, 19 hüvelykes állványba, első és hátsó oszlopokkal. A StorSimple 8100 eszköz egyetlen elsődleges házzal rendelkezik.

A telepítés több lépésből áll, amelyek mindegyikét a következő eljárások tárgyalják.

> [!IMPORTANT]
> A megfelelő működéshez állványra kell állítani a StorSimple eszközöket.
> 
> 

### <a name="prepare-the-site"></a>A webhely előkészítése
A készüléket egy szabványos, 19 hüvelykes állványba kell beszerelni, amely első és hátsó oszlopokkal is rendelkezik. Az alábbi eljárással készülhet elő a rack-telepítésre.

#### <a name="to-prepare-the-site-for-rack-installation"></a>A hely előkészítése a rack telepítésre
1. Győződjön meg arról, hogy az eszköz egy sima, stabil és vízszintes munkafelületen (vagy ahhoz hasonlón) helyezkedik el.
2. Ellenőrizze, hogy a beállítani kívánt hely rendelkezik-e független forrásból származó hálózati árammal vagy szünetmentes áramforrással (UPS) rendelkező rack-áramelosztó egységgel (PDU).
3. Győződjön meg arról, hogy egy 2U foglalat áll rendelkezésre azon az állványon, amelybe csatlakoztatni kívánja az eszközt.

![Figyelem](./media/storsimple-safety/IC740879.png)![Icon nehéz](./media/storsimple-8100-hardware-installation/HCS_HeavyWeight_Icon.png) súlyú ikon **FIGYELEM!**

Ha manuálisan kezeli az eszköz beállítását, győződjön meg arról, hogy két ember áll rendelkezésre a súly kezeléséhez. Egy teljesen konfigurált ház súlya akár 32 kg (70 lbs.).

### <a name="rack-prerequisites"></a>Rack-előfeltételek
A 8100-as ház egy szabványos, 19 hüvelykes rackszekrénybe való beépítésre készült, a következőkkel:

* Minimális mélysége 27,84 hüvelyk rack post utáni.
* A készülék maximális tömege 32 kg
* Maximális ellennyomás 5 Pascal (0,5 mm-es vízmérő).

### <a name="rack-mounting-rail-kit"></a>Rackbe szerelhető sínkészlet
A 19 hüvelykes rackszekrényhez egy sor szerelősín is rendelkezésre áll. A síneket a ház maximális tömegének kezelésére tesztelték. Ezek a sínek is lehetővé teszi konstitúrák telepítése több ház nélkül veszteség helyet a rack.

#### <a name="to-install-the-device-on-the-rails"></a>A készülék sínre való felszerelése
1. Ezt a lépést csak akkor hajtsa végre, ha a belső sínek nincsenek telepítve a készülékre. Jellemzően, a belső sínek vannak telepítve a gyárban. Ha nincsenek sínek felszerelve, akkor szerelje fel a bal és a jobb oldali süldényeket a ház alvázának oldalára. Ezek tulajdonítanak segítségével hat metrikus csavarok mindkét oldalon. A tájékozódás érdekében a síncsúszdák **LH – Front** és **RH – Front**jelzéssel vannak ellátva, és a ház hátsó része felé elhelyezett vége kúpos véggel rendelkezik.<br/>
   
    ![Síncsúszdák rögzítése a ház alvázához](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)

    **Belső sínlemezek rögzítése a ház oldalához**
   
    Címke | Leírás
    ----- | -----------
    1     | M 3x4 gombfejű csavarok
    2     | Alváz diák

2. Csatlakoztassa a bal külső sínt és a jobb külső sínszerelvényeket az állványszekrény függőleges tagjaihoz. A zárójelek vannak jelölve **LH,** **RH**, és **Ez az oldal felfelé** végigvezeti Önt a megfelelő tájolást.
3. Keresse meg a sín csapjait a sínszerelvény elülső és hátsó végénél. Nyújtsa ki a sínt, hogy illeszkedjen az állvány oszlopok közé, és helyezze be a csapokat az első és hátsó rack oszlop függőleges tag lyukak. Győződjön meg arról, hogy a sínszerelvény vízszintes.
4. A mellékelt metrikus csavarok közül kettőt használjon a sínszerelvény rack függőleges részeihez való rögzítéséhez. Használjon egy csavart az első és egy hátul.
5. Ismételje meg ezeket a lépéseket a másik vasúti szerelvénynél.<br/>
   
     ![Síncsúszdák rögzítése az állványszekrényhez](./media/storsimple-8100-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Külső sínszerelvények rögzítése az állványhoz**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Szorítócsavar |
   |   2 |Szögletes lyukú első rack utócsavar |
   |   3 |Bal oldali sín elülső helye csapok |
   |   4 |Szorítócsavar |
   |   5 |Bal oldali sín hátsó hely csapok |

### <a name="mounting-the-device-in-the-rack"></a>A készülék felszerelése az állványra
Az imént telepített állványsínek használatával hajtsa végre a következő lépéseket a készülék rackbe való felszereléséhez.

#### <a name="to-mount-the-device"></a>A készülék csatlakoztatása
1. Asszisztenssel emelje fel a burkolatot, és igazítsa az állványsínekhez.
2. Óvatosan helyezze be a készüléket a sínekbe, majd nyomja be teljesen a készüléket az állványszekrénybe.<br/>
   
    ![Eszköz behelyezése az állványba](./media/storsimple-8100-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **A készülék felszerelése az állványra**
3. Távolítsa el a bal és jobb első karima sapkák húzza a sapkák szabad. A karima sapkák egyszerűen pattintsa rá a karimák.
4. Rögzítse a burkolatot az állványon egy phillips-fejű csavar felszerelésével minden karimán keresztül, balra és jobbra.
5. Szerelje fel a karimasapkákat úgy, hogy a helyére nyomja őket, és a helyére pattintsa őket.<br/>
   
     ![Karimasapkák felszerelése](./media/storsimple-8100-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **A peremsapkák felszerelése**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Ház rögzítő csavar |

A következő lépés az, hogy kábel a készülék a hatalom, a hálózat és a soros hozzáférés.

## <a name="cable-your-storsimple-8100-device"></a>A StorSimple 8100 sorozatú eszköz kábelezése
Az alábbi eljárások bemutatják, hogyan kábelezheti a StorSimple 8100 eszközt a tápellátáshoz, a hálózathoz és a soros kapcsolatokhoz.

### <a name="prerequisites"></a>Előfeltételek
Az eszköz kábelezésének megkezdéséhez az alábbiakra lesz szüksége:

* A tárolóeszköz, teljesen kicsomagolva és rackbe szerelve.
* 2 tápkábel, amelyet a készülékhez érkezett
* Hozzáférés 2 energiaelosztó egységhez (ajánlott).
* Hálózati kábelek
* Biztosított soros kábelek
* Soros USB-átalakító a számítógépre telepített megfelelő illesztőprogrammal (ha szükséges)
* Biztosított 4 QSFP-SFP+ adapter 10 GbE hálózati interfészhez
* [A StorSimple eszköz 10 GbE hálózati interfészének támogatott hardvere](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="power-cabling"></a>Tápkábelezés
A készülék redundáns táp- és hűtőmodulokat (PCM-eket) tartalmaz. A magas rendelkezésre állás biztosítása érdekében mindkét PCM-et különböző áramforrásokhoz kell telepíteni és csatlakoztatni.

Hajtsa végre az alábbi lépéseket a kábelezéshez.

[!INCLUDE [storsimple-cable-8100-for-power](../../includes/storsimple-cable-8100-for-power.md)]

### <a name="network-cabling"></a>Hálózati kábelezés
Az eszköz aktív készenléti konfiguráció: az egyik vezérlőmodul aktív, és az összes lemez- és hálózati műveletet feldolgozza, miközben a másik vezérlőmodul készenléti állapotban van. Ha egy vezérlő meghibásodik, a készenléti vezérlő azonnal aktiválódik, és folytatja az összes lemez- és hálózati műveletet.

A redundáns vezérlő feladatátvételének támogatásához az alábbi lépésekben leírtak szerint kábeleznie kell az eszközhálózatot.

#### <a name="to-cable-for-network-connection"></a>A hálózati kapcsolat kábelkötege
1. A készülék minden vezérlőn hat hálózati csatolmány található: négy 1 Gb/s és két 10 Gb/s-os Ethernet port. Azonosítsa az eszköz hátoldalán lévő különböző adatportokat.
   
    ![8100-as eszköz hátlapja](./media/storsimple-8100-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **Az eszköz hátsó része adatportokkal**
   
   | Címke | Leírás |
   | --- | --- |
   |   0,1,4,5 |1 GbE hálózati interfészek |
   |   2,3 |10 GbE hálózati interfészek |
   |   6 |Soros portok |
2. A hálózati kábelezést az alábbi ábrán található. (A minimális hálózati konfigurációt folyamatos kék vonalak mutatják. A magas rendelkezésre álláshoz és teljesítményhez szükséges további konfigurációt pontozott vonalak mutatják.)

    ![Kábelezze a 2U-s eszközt a hálózathoz](./media/storsimple-8100-hardware-installation/HCSCableYour2UDeviceforNetwork.png)

    **Hálózati kábelezés a készülékhez**

   |Címke | Leírás |
   |----- | ----------- |
   | A    | LAN internet-hozzáféréssel |
   | B    | Vezérlő 0 |
   | C#    | PCM 0 |
   | D    | Vezérlő 1 |
   | E    | PCM 1 |
   | F, G | Hosts |
   | 0-5  | Hálózati illesztők |



A készülék kábelezése során a minimális konfiguráció a következőket igényli:

* Minden vezérlőn legalább két hálózati csatoló csatlakozik, amelyek közül egy a felhőalapú hozzáféréshez és egy az iSCSI-hez. A DATA 0 port automatikusan engedélyezve van és konfigurálva van az eszköz soros konzolján keresztül. A DATA 0-n kívül egy másik adatportot is konfigurálni kell a klasszikus Azure-portálon keresztül. Ebben az esetben csatlakoztassa a DATA 0 portot az elsődleges helyi hálózathoz (internet-hozzáféréssel rendelkező hálózathoz). A többi adatport a tervezett szerepkörtől függően csatlakoztatható a hálózat SAN/iSCSI LAN (VLAN) szegmenséhez.
* Azonos összeköttetések minden vezérlő n, amely ugyanahhoz a hálózathoz csatlakozik, hogy biztosítsa a rendelkezésre állást, ha a vezérlő feladatátvétel történik. Ha például úgy dönt, hogy az egyik adatkezelőhöz csatlakoztatja a DATA 0-t és a DATA 3-at, akkor a megfelelő DATA 0-t és DATA 3-at a másik vezérlőhöz kell csatlakoztatnia.

Ne feledje, hogy a magas rendelkezésre állás és teljesítmény érdekében:

* Ha lehetséges, konfiguráljon egy hálózati adaptert a felhőalapú hozzáféréshez (1 GbE) és egy másik párt az iSCSI-hoz (10 GbE ajánlott) minden vezérlőn.
* Ha lehetséges, csatlakoztasson hálózati adaptereket az egyes vezérlőkből két különböző kapcsolóhoz, hogy biztosítsa a rendelkezésre állást a kapcsolómeghibásodás okán. Az ábra a két 10 GbE hálózati interfészt, a DATA 2-t és a DATA 3-at ábrázolja, minden vezérlőből, amely két különböző kapcsolóhoz csatlakozik.

További információt a [StorSimple-eszköz magas rendelkezésre állási követelményei](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple)alatti **hálózati felületekben** talál.

> [!NOTE]
> Ha SFP+ adó-vevőket használ a 10 GbE hálózati interfészekhez, használja a mellékelt QSFP-SFP+ adaptereket. További információt a [StorSimple-eszköz 10 GbE hálózati csatolójának támogatott hardvere című oldalon](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)talál.
> 
> 

### <a name="serial-port-cabling"></a>Soros port kábelezése
A soros port kábelezéséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-cable-for-serial-connection"></a>Kábelkötegelő soros kapcsolathoz
1. A készülék minden vezérlőn soros port található, amelyet egy csavarkulcs ikon azonosít. Az eszköz hátoldalán található soros portok megkereséséhez a [Hálózati kábelezés](#network-cabling) részben található ábrán tájékozódhat.
2. Azonosítsa az eszköz hátsíkján lévő aktív vezérlőt. A villogó kék LED azt jelzi, hogy a vezérlő aktív.
3. Használja a mellékelt soros kábeleket (ha szükséges, a laptop USB-soros átalakítóját), és csatlakoztassa a konzolt vagy a számítógépet (a terminálemulációval az eszközhöz) az aktív vezérlő soros portjához.
4. Telepítse a (készülékkel együtt szállított) soros USB-illesztőprogramokat a számítógépre.
5. A soros kapcsolat beállítása a következőképpen: 115 200 baud, 8 adatbit, 1 stopbit, nincs paritás, és a folyamatvezérlés beállítása Nincs értékre.
6. Ellenőrizze, hogy működik-e a kapcsolat az Enter billentyű lenyomásával a konzolon. Meg kell jelennie egy soros konzolmenünek.

> [!NOTE]
> **Lights-Out Management**: Ha az eszköz telepítve van egy távoli adatközpontban vagy egy korlátozott hozzáférésű számítógépteremben, győződjön meg arról, hogy a két vezérlő soros kapcsolata mindig soros konzolkapcsolóhoz vagy hasonló berendezéshez csatlakozik. Ez lehetővé teszi a sávon kívüli távvezérlési és támogatási műveleteket, ha hálózati zavarok vagy váratlan hibák jelentkeznek.
> 
> 

A készülék most már kábelezve van a tápellátás, a hálózati hozzáférés és a soros kapcsolat számára. A következő lépés a szoftver konfigurálása és az eszköz telepítése.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [telepítheti és konfigurálhatja a helyszíni StorSimple-eszközt.](storsimple-8000-deployment-walkthrough-u2.md)

