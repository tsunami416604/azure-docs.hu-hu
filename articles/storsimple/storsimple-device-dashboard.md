---
title: "A StorSimple Manager eszköz irányítópult |} Microsoft Docs"
description: "Ismerteti a StorSimple Manager szolgáltatás eszközök irányítópult és a tárolási metrikák és a csatlakoztatott kezdeményezők megtekintéséhez, és keresse meg a sorozatszámot és IQN-Nevének használatával."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 6c213969-a385-461f-b698-78ef5b8a79cc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0d8035b9608ca3bac3d4822c7c755b81c96d481e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-device-dashboard-in-storsimple-manager-service"></a>Az eszköz irányítópultot a StorSimple Manager szolgáltatásban  

## <a name="overview"></a>Áttekintés
A StorSimple Manager eszköz irányítópult lehetővé teszi az adott StorSimple eszköz, a szolgáltatás irányítópultját, ami biztosít az eszközök a Microsoft Azure StorSimple megoldásban szereplő összes alkalmazás adatai ellentétben információk áttekintését.

![Irányítópult-oldalon eszköz](./media/storsimple-device-dashboard/StorSimple_DeviceDashbaord1M.png)

Az irányítópult a következő információkat tartalmazza:

* **Diagram – terület** – megtekintheti a megfelelő tárolási metrikák az irányítópult tetején a diagramterületen. Ezen a diagramon az összes elsődleges (az állomások számára az eszköz által írt adatok mennyisége) és a teljes felhő tárolására egy meghatározott időtartamra vonatkozóan az eszköz által felhasznált metrikáját tekintheti meg.
  
     Ebben a környezetben *elsődleges tárolási* a gazdagép által írt adatok teljes mennyisége hivatkozik, és a kötet típusa szerint sorolhatók: *elsődleges rétegzett tárolás* egyaránt helyben tárolt adatok és az adatok a rétegzett a felhő; *elsődleges helyileg rögzített tárolási* csak helyben tárolja az adatokat tartalmazza. *Felhőbeli tárhely*, másrészt van a felhőben tárolt adatok teljes mennyisége mérését. Ez magában foglalja a rétegzett adatok és a biztonsági mentéseket. Vegye figyelembe, hogy a felhőben tárolt adatok deduplikációja és tömöríti, mivel elsődleges tárolási tárolókapacitást előtt az adatok deduplikációja és tömöríti azt jelzi. (Ha meg szeretne ismerkedni a tömörítés és a két szám összehasonlíthatja). A mind az elsődleges és a felhőalapú tárolást látható összegek konfigurált követési gyakoriságnak alapul. Például ha úgy dönt, hogy az egy hét gyakoriságát, majd a diagram jeleníti meg adatok az előző hét naponta.
  
     A diagram az alábbiak szerint állíthatja be:
  
  * Felhő tárolókapacitást időbeli használni, jelölje ki a **FELHŐALAPÚ TÁROLÓT használja a** lehetőséget. A teljes tárterület a gazdagép által írt, jelölje ki a **elsődleges RÉTEGZETT TÁROLÓT használja a** és **elsődleges HELYILEG rögzített TÁROLÓT használja a** beállítások. Az ábrán látható mindkét lehetőség van kiválasztva; a diagram, ezért a felhő- és tárolási elsődleges tárolási összegek jeleníti meg. Vegye figyelembe, hogy a 2. frissítés telepítése előtt használt összes elsődleges tárhelyhez által képviselt a **elsődleges RÉTEGZETT TÁROLÓT használja a** sor.
  * A diagram jobb felső sarkában a legördülő menü segítségével adjon meg egy 1 hét, hónap 1, 3 hónapos vagy 1 év időszakot. Vegye figyelembe, hogy a legfelső szintű diagram frissülnek naponta csak egyszer, és ezért az előző napi összegek fogja tartalmazni.
    
    További információkért lásd: [a StorSimple Manager szolgáltatás segítségével figyelheti a StorSimple eszköz](storsimple-monitor-device.md).
* **Használati áttekintése** – A(z) a **használatának áttekintése** területen látható használt elsődleges tárolókapacitást kiosztott tárolókapacitást és a maximális tárolókapacitás az eszközhöz. Ezen értékek számára elérhető tárterület maximális mennyisége összehasonlításával láthatja egy pillanat alatt Ha be kell szereznie a további tárhely. Ne feledje, hogy ez az Áttekintés 15 percenként frissül, a frissítési gyakoriság eltérő, előfordulhat, hogy megjelenítése mint eltérő számú látható a diagramterületen fenti, amelyet naponta frissít. További információkért lásd: [a StorSimple Manager szolgáltatás segítségével figyelheti a StorSimple eszköz](storsimple-monitor-device.md).
* **Riasztások** – a **riasztások** terület tartalmazza a riasztások az eszköz áttekintése. Riasztások súlyosság szerint vannak csoportosítva, és a szám áll a minden súlyossági szintű riasztások számát. A riasztás súlyosságát kattintva megnyithatja a hatókörön belüli ábrázolása mutatjuk be, csak az adott súlyossági szint, az eszköz a riasztásokat a riasztások lapján.
* **Feladatok** – a **feladatok** területen látható legutóbbi feladat tevékenység eredményeit. Ezzel biztosítsák meg, hogy a rendszer a várt módon működik, vagy megőrizheti azt jelzi, hogy kell-e venni a kijavítására a lépéseket. További információ a közelmúltban befejezett feladatok megtekintéséhez kattintson **feladatok sikeres volt. az elmúlt 24 órában**.
* A **gyors áttekintő** terület jobb oldalán a irányítópult nyújtanak hasznos információkat, például az eszköz típusa, sorozatszámát, állapot, leírás és kötetek száma.

Feladatátvétel konfigurálása is, és az eszköz irányítópultról csatlakoztatott kezdeményezők megtekintése.

Ezen a lapon elvégezhető a gyakori feladatok a következők:

* Csatlakoztatott kezdeményezők megtekintése
* Az eszköz sorozatszámát keresése
* Az eszköz cél IQN keresése

## <a name="view-connected-initiators"></a>Csatlakoztatott kezdeményezők megtekintése
Az iSCSI-kezdeményezők, az eszköz csatlakozik kattintva megtekintheti a **nézet csatlakoztatott kezdeményezők** található hivatkozás a **gyors áttekintő** terület az eszköz irányítópult. Ez a lap felsorolja táblázatos a kezdeményezőket, amelyeknek az eszköz sikeresen csatlakozott. Minden kezdeményezőnél látható:

* Az iSCSI minősített nevét (IQN) a csatlakoztatott kezdeményező.
* A hozzáférés-vezérlési rekordot (ACR), amely lehetővé teszi a csatlakoztatott kezdeményező neve.
* A csatlakoztatott kezdeményező IP-címét.
* A hálózati adapterek, amely a kezdeményező a tárolóeszköz csatlakozik. Ezek között lehet DATA 0 DATA 5.
* Minden kötet, amely a csatlakoztatott kezdeményező hozzáférhetnek ACR aktuális konfigurációjának megfelelően.

Ha a listában szereplő váratlan kezdeményezők lásd, vagy nem látja a virág, tekintse át a ACR konfigurációját. Legfeljebb 512 kezdeményező csatlakozhat az eszközt.

## <a name="find-the-device-serial-number"></a>Az eszköz sorozatszámát keresése
Az eszközön a Microsoft többutas I/O (MPIO) konfigurálásakor szükség lehet az eszköz sorozatszámát. A következő lépésekkel található az eszköz sorozatszámát.

#### <a name="to-find-the-device-serial-number"></a>Az eszköz sorozatszámát kereséséhez
1. Navigáljon a **eszközök** > **irányítópult**.
2. Az irányítópult jobb oldali ablaktáblán keresse meg a **gyors áttekintő** területen.
3. Görgessen lefelé, és keresse meg a sorozatszámot.

## <a name="find-the-device-target-iqn"></a>Az eszköz cél IQN keresése
Szükség lehet az eszköz cél IQN a Challenge Handshake Authentication Protocol (CHAP) a StorSimple eszköz konfigurálásakor. A következő lépésekkel található az eszköz célja IQN.

### <a name="to-find-the-device-target-iqn"></a>Az eszköz cél IQN kereséséhez
1. Navigáljon a **eszközök** > **irányítópult**.
2. Az irányítópult jobb oldali ablaktáblán keresse meg a **gyors áttekintő** területen.
3. Görgessen lefelé, és keresse meg a célként megadott IQN.

## <a name="next-steps"></a>Következő lépések
* További információ a [StorSimple Manager szolgáltatás irányítópultját](storsimple-service-dashboard.md).
* További információ [a StorSimple Manager szolgáltatás használata a StorSimple eszköz felügyeletéhez](storsimple-manager-service-administration.md).

