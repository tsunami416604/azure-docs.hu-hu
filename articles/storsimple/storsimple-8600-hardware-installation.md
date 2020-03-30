---
title: A Microsoft Azure StorSimple 8600 eszköz telepítése
description: A cikk azt ismerteti, hogy a szoftver telepítése és konfigurálása előtt hogyan csomagolhatja ki, állványozhatja és kábelezheti a StorSimple 8600 eszközt.
author: alkohli
ms.assetid: 3d82ba5f-3e34-40dc-9c33-50f952bc6be8
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 8d118e71b29b54b034788c37916091ad8f8b996f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254754"
---
# <a name="unpack-rack-mount-and-cable-your-storsimple-8600-device"></a>A StorSimple 8600 eszköz kicsomagolása, rackbe szerelhető és kábelezése

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés
A Microsoft Azure StorSimple 8600 egy kétházas eszköz, amely egy elsődleges és egy EBOD-házból áll. Ez az oktatóanyag bemutatja, hogyan csomagolhatja ki, állványra szerelhető és kábelezheti a StorSimple 8600 eszközhardvert a StorSimple szoftver konfigurálása előtt.

## <a name="unpack-your-storsimple-8600-device"></a>A StorSimple 8600 eszköz kicsomagolása
A következő lépések világos és részletes útmutatást nyújtanak a StorSimple 8600 tárolóeszköz kicsomagolásához. Ez az eszköz két dobozban kerül kiszállításra, az egyik az elsődleges ház, a másik pedig az EBOD ház. Ezt a két dobozt ezután egyetlen dobozba helyezik.

### <a name="prepare-to-unpack-your-device"></a>Felkészülés a készülék kicsomagolására
Az eszköz kicsomagolása előtt tekintse át az alábbi információkat.

![Figyelem](./media/storsimple-safety/IC740879.png)![Icon nehéz](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) súlyú ikon **FIGYELEM!**

1. Ha manuálisan kezeli, győződjön meg arról, hogy két ember áll rendelkezésre az eszköz súlyának kezeléséhez. Egy teljesen konfigurált ház súlya akár 32 kg (70 lbs.).
2. Helyezze a csomagot egy sima, vízszintes felületre.

Ezután hajtsa végre az alábbi lépéseket az eszköz kicsomagolásához.

#### <a name="to-unpack-your-device"></a>A készülék kicsomagolása
1. Vizsgálja meg a dobozt és a térkitöltő anyagot, hogy nincsenek-e rajta ütődés, vágás, nedvesség vagy más egyértelmű sérülés nyomai. Ha a csomagon jelentős sérülések láthatók, ne nyissa ki. Kérjük, lépjen kapcsolatba a [Microsoft támogatási szolgálatával,](storsimple-8000-contact-microsoft-support.md) és segítsen felmérni, hogy az eszköz megfelelőállapotban van-e.
2. Nyissa ki a külső dobozt, majd vegye ki az elsődleges és az EBOD háznak megfelelő két dobozt. Most már kicsomagolhatja az elsődleges és Az EBOD-házakat. Az alábbi ábra az egyik ház kicsomagolt nézetét mutatja be.
   
    ![A tárolóeszköz kicsomagolása](./media/storsimple-8600-hardware-installation/HCSUnpackyour4Udevice.png)
   
    **A tárolóeszköz kicsomagolt nézete**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Csomagolás |
   |   2 |SAS kábelek (tartozékokban és kábeltálcákban) |
   |   3 |Alsó hab |
   |   4 |Eszköz |
   |   5 |Felső hab |
   |   6 |Kiegészítő doboz |
3. A két doboz kicsomagolása után győződjön meg arról, hogy:
   
   * 1 elsődleges ház (az elsődleges ház és az EBOD ház két külön dobozban van)
   * 1 EBOD ház
   * 4 tápkábel, 2 minden dobozban
   * 2 SAS kábelek (az elsődleges ház csatlakoztatásához az EBOD házhoz)
   * 1 crossover Ethernet kábel
   * 2 soros konzolkábel
   * 1 soros-USB konverter a soros hozzáféréshez
   * 4 QSFP-SFP+ adapter 10 GbE hálózati interfészhez
   * 2 rack-re szerelhető készlet (4 oldalsín szerelőhardverrel, 2-es az elsődleges házhoz és EBOD ház), 1 minden dobozban
   * Első lépések dokumentációja
     
     Ha a fenti elemek közül egyik sem kapott értesítést, forduljon a [Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md)  

A következő lépés az eszköz állványra szerelése.

## <a name="rack-mount-your-storsimple-8600-device"></a>Rackbe szerelhető a StorSimple 8600 eszköz
A következő lépésekkel telepítheti a StorSimple 8600 tárolóeszközt egy szabványos, 19 hüvelykes állványba, első és hátsó oszlopokkal. Ez az eszköz két házzal rendelkezik: egy elsődleges házzal és egy EBOD házzal. Mindkettőt rackbe kell szerelni.

A telepítés több lépésből áll, amelyek mindegyikét a következő eljárások tárgyalják.

> [!IMPORTANT]
> A megfelelő működéshez állványra kell állítani a StorSimple eszközöket.
> 
> 

### <a name="site-preparation"></a>A helyszín előkészítése
A burkolatokat egy szabványos, 19 hüvelykes állványba kell helyezni, amely első és hátsó oszlopokkal is rendelkezik. Az alábbi eljárással készülhet elő a rack-telepítésre.

#### <a name="to-prepare-the-site-for-rack-installation"></a>A hely előkészítése a rack telepítésre
1. Győződjön meg arról, hogy az elsődleges és az EBOD házak biztonságosan pihennek egy sima, stabil és vízszintes munkafelületen (vagy hasonló).
2. Ellenőrizze, hogy a beállítani kívánt hely rendelkezik-e független forrásból származó szabványos hálózati áramforrással vagy szünetmentes áramforrással (UPS) rendelkező rack-es energiaelosztó egységgel (PDU).
3. Győződjön meg arról, hogy egy 4U (2 X 2U) foglalat áll rendelkezésre azon az állványon, amelybe a burkolatokat fel kívánja szerelni.

![Figyelem](./media/storsimple-safety/IC740879.png)![Icon nehéz](./media/storsimple-8600-hardware-installation/HCS_HeavyWeight_Icon.png) súlyú ikon **FIGYELEM!**

 Ha manuálisan kezeli az eszköz beállítását, győződjön meg arról, hogy két ember áll rendelkezésre a súly kezeléséhez. Egy teljesen konfigurált ház súlya akár 32 kg (70 lbs.).

### <a name="rack-prerequisites"></a>Rack-előfeltételek
A burkolatokat szabványos, 19 hüvelykes rackszekrénybe való beépítésre tervezték, amely a következőket rendelkezik:

* Minimális mélysége 27,84 hüvelyk rack post utáni
* A készülék maximális tömege 32 kg
* Maximális ellennyomás 5 Pascal (0,5 mm-es vízmérő)

### <a name="rack-mounting-rail-kit"></a>Rackbe szerelhető sínkészlet
A 19 hüvelykes rackszekrényhez egy sor szerelősín is használható. A síneket a ház maximális tömegének kezelésére tesztelték. Ezek a sínek is lehetővé teszi a telepítés több burkolatok elvesztése nélkül helyet a rack. Először telepítse az EBOD házat.

#### <a name="to-install-the-ebod-enclosure-on-the-rails"></a>Az EBOD ház telepítése a sínekre
1. Ezt a lépést csak akkor hajtsa végre, ha a belső sínek nincsenek telepítve a készülékre. Jellemzően, a belső sínek vannak telepítve a gyárban. Ha nincsenek sínek felszerelve, akkor szerelje fel a bal és a jobb oldali süldényeket a ház alvázának oldalára. Ezek tulajdonítanak segítségével hat metrikus csavarok mindkét oldalon. A tájékozódás érdekében a síncsúszdák **LH – Front** és **RH – Front**jelzéssel vannak ellátva, és a ház hátsó része felé elhelyezett vége kúpos véggel rendelkezik.
   
    ![Síncsúszdák rögzítése a ház alvázához](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoEnclosureChassis.png)
   
    **Síncsúszdák rögzítése a ház oldalához**
   
   | Címke | Leírás |
   | --- | --- |
   |  1 |M 3x4 gombfejű csavarok |
   |  2 |Alváz diák |
2. Csatlakoztassa a bal sín és a jobb vasúti szerelvények a rack szekrény függőleges tagok. A zárójelek vannak jelölve **LH,** **RH**, és **Ez az oldal felfelé** végigvezeti Önt a helyes tájolást.
3. Keresse meg a sín csapjait a sínszerelvény elülső és hátsó végénél. Nyújtsa ki a sínt, hogy illeszkedjen az állvány oszlopok közé, és helyezze be a csapokat az első és a hátsó rack oszlop függőleges tag lyukakba. Győződjön meg arról, hogy a sínszerelvény vízszintes.
4. Rögzítse a sínszerelvényt az állvány függőleges tagjaihoz a mellékelt két metrikus csavar segítségével. Használjon egy csavart az első és egy hátul.
5. Ismételje meg ezeket a lépéseket a másik vasúti szerelvénynél.
   
     ![Síncsúszdák rögzítése az állványszekrényhez](./media/storsimple-8600-hardware-installation/HCSAttachingRailSlidestoRackCabinet.png)
   
    **Sínszerelvények rögzítése az állványhoz**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Szorítócsavar |
   |   2 |Szögletes lyukú első rack utócsavar |
   |   3 |Bal első sín hely csapok |
   |   4 |Szorítócsavar |
   |   5 |Bal hátsó sín hely csapok |

### <a name="mounting-the-ebod-enclosure-in-the-rack"></a>Az EBOD ház rögzítése az állványra
Az imént telepített racksínek használatával hajtsa végre a következő lépéseket az EBOD ház állványra való felszereléséhez.

#### <a name="to-mount-the-ebod-enclosure"></a>Az EBOD ház csatlakoztatása
1. Asszisztenssel emelje fel a burkolatot, és igazítsa az állványsínekhez.
2. Óvatosan helyezze be a burkolatot a sínekbe, majd nyomja be teljesen az állványszekrénybe.
   
    ![Eszköz behelyezése az állványba](./media/storsimple-8600-hardware-installation/HCSInsertingDeviceintheRack.png)
   
    **A burkolat felszerelése az állványra**
3. Távolítsa el a bal és jobb első karima sapkák húzza a sapkák szabad. A karima sapkák egyszerűen pattintsa rá a karimák.
4. Rögzítse a burkolatot az állványba egy phillips-fejű csavar felszerelésével minden karimán keresztül, bal és jobb oldalon.
5. Szerelje fel a karimasapkákat úgy, hogy a helyére nyomja őket, és a helyére pattintsa őket.
   
     ![Karimasapkák felszerelése](./media/storsimple-8600-hardware-installation/HCSInstallingFlangeCaps.png)
   
    **A peremsapkák felszerelése**
   
   | Címke | Leírás |
   | --- | --- |
   |   1 |Ház rögzítő csavar |

### <a name="mounting-the-primary-enclosure-in-the-rack"></a>Az elsődleges ház felszerelése az állványra
Miután befejezte az EBOD-ház csatlakoztatását, az elsődleges ház csatlakoztatása ugyanazon lépéseket követve kell csatlakoztatnia.

> [!NOTE]
> * Lehetőség van néhány üres bővítőhely az állványközött az elsődleges ház és az EBOD ház között.
> * A megadott 2 m-es SAS-kábellel csatlakoztassa az elsődleges ház az EBOD ház.
> * Nincsenek korlátozások a fejegység nek az EBOD egységhez való relatív elhelyezésére. Ezért az elsődleges ház a felső tárolóhelyre és az EBOD ház alatt helyezhető el – vagy fordítva.
> 
> 

A következő lépés az, hogy kábel a készülék a hatalom, a hálózat és a soros hozzáférés.

## <a name="cable-your-storsimple-8600-device"></a>A StorSimple 8600 sorozatú eszköz kábelezése
Az alábbi eljárások bemutatják, hogyan kábelezheti a StorSimple 8600 eszközt a tápellátáshoz, a hálózathoz és a soros kapcsolatokhoz.

### <a name="prerequisites"></a>Előfeltételek
Mielőtt elkezdené kábelezni a készüléket, a következőkre lesz szüksége:

* Az elsődleges ház és az EBOD ház, teljesen kicsomagolva
* 4 tápkábel (2 az elsődleges és az EBOD házhoz), amelyeket a készülékhez
* 2 A készülékhez mellékelt SAS kábelek, amelyek az EBOD házat az elsődleges burkolathoz csatlakoztatják
* Hozzáférés 2 energiaelosztó egységhez (PDU) (ajánlott)
* Hálózati kábelek
* Biztosított soros kábelek
* Soros-USB konverter a számítógépre telepített megfelelő illesztőprogrammal (ha szükséges)
* Biztosított 4 QSFP-SFP+ adapter 10 GbE hálózati interfészhez
* [A StorSimple eszköz 10 GbE hálózati interfészének támogatott hardvere](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)

### <a name="sas-and-power-cabling"></a>SAS és power kábelezés
Az eszköz rendelkezik egy elsődleges ház és egy EBOD ház is. Ehhez az egységeket össze kell kapcsolni a soros csatlakoztatott SCSI (SAS) kapcsolathoz és tápellátáshoz.

Az eszköz első beállításakor először hajtsa végre a SAS-kábelezés lépéseit, majd hajtsa végre a kábelezés lépéseit.

[!INCLUDE [storsimple-cable-8600-for-SAS](../../includes/storsimple-sas-cable-8600.md)]

[!INCLUDE [storsimple-cable-8600-for-power](../../includes/storsimple-cable-8600-for-power.md)]

### <a name="network-cabling"></a>Hálózati kábelezés
Az eszköz aktív készenléti konfigurációban van: az egyik vezérlőmodul aktív, és az összes lemez- és hálózati műveletet feldolgozza, miközben a másik vezérlőmodul készenléti állapotban van. Vezérlőhiba esetén a készenléti vezérlő azonnal aktiválja és folytatja az összes lemez- és hálózati műveletet.

A redundáns vezérlő feladatátvételének támogatásához az alábbi lépéseknek köszönhetően kábelkábeleznie kell az eszközhálózatot.

#### <a name="to-cable-for-network-connection"></a>A hálózati kapcsolat kábelkötege
1. A készülék minden vezérlőn hat hálózati csatolmány található: négy 1 Gb/s és két 10 Gb/s-os Ethernet port. Az eszköz hátoldalán található adatportok azonosításához lásd az alábbi ábrát.
   
     ![8600-as eszköz hátlapja](./media/storsimple-8600-hardware-installation/HCSBackplaneof2UDevicewithPortsLabeled.jpg)
   
    **A készülék háta az adatportokkal**
   
   | Címke | Leírás |
   | --- | --- |
   |   0,1,4,5 |1 GbE hálózati interfészek |
   |   2,3 |10 GbE hálózati interfészek |
   |   6 |Soros portok |
2. A hálózati kábelezést az alábbi ábrán található. (A minimális hálózati konfigurációt folyamatos kék vonalak mutatják. A magas rendelkezésre állás és teljesítmény érdekében a további konfigurációt pontozott vonalak mutatják.)

![Kábelezze a 4U-s eszközt a hálózathoz](./media/storsimple-8600-hardware-installation/HCSCableYour4UDeviceforNetwork.png)

**Hálózati kábelezés a készülékhez**

| Címke | Leírás |
| --- | --- |
| A |LAN internet-hozzáféréssel |
| B |Vezérlő 0 |
| C# |PCM 0 |
| D |Vezérlő 1 |
| E |PCM 1 |
| F |EBOD vezérlő 0 |
| G |EBOD-vezérlő 1 |
| H,Én |Állomások (például fájlkiszolgálók) |
| 0-5 |Hálózati illesztők |
| 6 |Elsődleges burkolat |
| 7 |EBOD ház |

A készülék kábelezése során a minimális konfiguráció a következőket igényli:

* Minden vezérlőn legalább két hálózati csatoló csatlakozik, amelyek közül egy a felhőalapú hozzáféréshez és egy az iSCSI-hez. A DATA 0 port automatikusan engedélyezve van és konfigurálva van az eszköz soros konzolján keresztül. A DATA 0-n kívül egy másik adatportot is konfigurálni kell a klasszikus Azure-portálon keresztül. Ebben az esetben csatlakoztassa a DATA 0 portot az elsődleges helyi hálózathoz (internet-hozzáféréssel rendelkező hálózathoz). A többi adatport a tervezett szerepkörtől függően csatlakoztatható a hálózat SAN/iSCSI LAN (VLAN) szegmenséhez.
* Azonos összeköttetések minden vezérlő n, amely ugyanahhoz a hálózathoz csatlakozik, hogy biztosítsa a rendelkezésre állást, ha a vezérlő feladatátvétel történik. Ha például úgy dönt, hogy az egyik adatkezelőhöz csatlakoztatja a DATA 0-t és a DATA 3-at, akkor a megfelelő DATA 0-t és DATA 3-at a másik vezérlőhöz kell csatlakoztatnia.

Ne feledje, hogy a magas rendelkezésre állás és teljesítmény érdekében:

* Ha lehetséges, konfiguráljon egy hálózati adaptert a felhőalapú hozzáféréshez (1 GbE) és egy másik párt az iSCSI-hoz (10 GbE ajánlott) minden vezérlőn.
* Ha lehetséges, csatlakoztasson hálózati adaptereket az egyes vezérlőkből két különböző kapcsolóhoz, hogy biztosítsa a rendelkezésre állást a kapcsolómeghibásodás okán. Az ábra a két 10 GbE hálózati interfészt, a DATA 2-t és a DATA 3-at ábrázolja, minden vezérlőből, amely két különböző kapcsolóhoz csatlakozik. További információt a [StorSimple-eszköz magas rendelkezésre állási követelményei](storsimple-8000-system-requirements.md#high-availability-requirements-for-storsimple)alatti **hálózati felületekben** talál.

> [!NOTE]
> Ha SFP+ adó-vevőket használ a 10 GbE hálózati interfészekhez, használja a mellékelt QSFP-SFP+ adaptereket. További információt a [StorSimple-eszköz 10 GbE hálózati csatolójának támogatott hardvere című oldalon](storsimple-supported-hardware-for-10-gbe-network-interfaces.md)talál.
> 
> 

### <a name="serial-port-cabling"></a>Soros port kábelezése
A soros port kábelezéséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-cable-for-serial-connection"></a>Kábelkötegelő soros kapcsolathoz
1. A készülék minden vezérlőn soros port található, amelyet egy csavarkulcs ikon azonosít. A soros portok megkereséséhez olvassa el az eszköz hátoldalán található adatportokat bemutató ábrát.
2. Azonosítsa az eszköz hátsíkján lévő aktív vezérlőt. A villogó kék LED azt jelzi, hogy a vezérlő aktív.
3. Használja a mellékelt soros kábelt (ha szükséges, a laptop USB-soros átalakítóját), és csatlakoztassa a konzolt vagy a számítógépet (a terminálemulációval az eszközhöz) az aktív vezérlő soros portjához.
4. Telepítse a (készülékkel együtt szállított) soros USB-illesztőprogramokat a számítógépre.
5. Állítsa be a soros kapcsolatot az alábbiak szerint:
   
   * 115 200 baud
   * 8 adatbit
   * 1 stop bit
   * Nincs paritás
   * Az áramlásvezérlés beállítása **Nincs**
6. Ellenőrizze, hogy működik-e a kapcsolat az Enter billentyű lenyomásával a konzolon. Meg kell jelennie egy soros konzolmenünek.

> [!NOTE]
> **Lights-Out Management:** Ha az eszköz egy távoli adatközpontba vagy korlátozott hozzáférésű számítógépterembe van telepítve, győződjön meg arról, hogy a két vezérlősoros kapcsolata mindig soros konzolkapcsolóhoz vagy hasonló berendezéshez csatlakozik. Ez lehetővé teszi a sávon kívüli távvezérlési és támogatási műveleteket hálózati zavarok vagy váratlan hibák esetén.
> 
> 

Befejezte az eszköz kábelezését az áramellátás, a hálózati hozzáférés és a soros kapcsolat számára. A következő lépés a szoftver konfigurálása az eszközön.

## <a name="next-steps"></a>További lépések
Most már készen áll a [helyszíni StorSimple eszköz üzembe helyezésére és konfigurálására.](storsimple-8000-deployment-walkthrough-u2.md)

