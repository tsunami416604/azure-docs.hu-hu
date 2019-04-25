---
title: A Microsoft Azure StorSimple 8000 sorozatú eszköz akkumulátor cseréje |} A Microsoft Docs
description: Ismerteti, hogyan lehet eltávolítani, cserélje le, és kezelése a StorSimple eszközön a biztonsági mentési akkumulátor modul.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 4ebf3f28d40e0461d140a3fe74fb940720f26db6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60418938"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Cserélje le a StorSimple eszközön a biztonsági mentési akkumulátor modul

## <a name="overview"></a>Áttekintés
Az elsődleges ház Power és hűtéssel modul (PCM) a Microsoft Azure StorSimple eszközön rendelkezik egy további akkumulátor csomagot. Ez a csomag biztosít power, úgy, hogy a StorSimple-eszköz adatokat mentsen az elsődleges ház az AC áramkimaradás esetén. Ez a csomag akkumulátor nevezzük a *a biztonsági mentési akkumulátor modul*. A biztonsági mentési akkumulátor modul csak az elsődleges ház az a StorSimple eszköz (a EBOD ház nem tartalmaz. a biztonsági mentési akkumulátor modul) létezik.

Ez az oktatóanyag a következőket ismerteti:

* Távolítsa el a biztonsági mentési akkumulátor modul
* Egy új biztonsági mentési akkumulátor-modul telepítése
* A biztonsági mentési akkumulátor modul karbantartása

> [!IMPORTANT]
> Mielőtt eltávolítása és cseréje egy biztonsági mentési akkumulátor modult, tekintse át a biztonsági információk a [összetevő hardvercseréhez StorSimple bemutatása](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Távolítsa el a biztonsági mentési akkumulátor modul
A biztonsági mentési akkumulátor-modul a StorSimple eszközhöz egy mezőben telepen cserélhető egység. A PCM van telepítve, mielőtt az akkumulátor-modul eredeti csomagolásának kell tárolni. A következő lépésekkel távolítsa el a biztonsági mentési akkumulátor.

#### <a name="to-remove-the-backup-battery-module"></a>A biztonsági mentési akkumulátor moduljának eltávolítása
1. Az Azure Portalon nyissa meg a StorSimple-Eszközkezelő szolgáltatás paneljén. Lépjen a **eszközök** , és válassza ki azt az eszközt az eszközök a listából. Navigáljon a **figyelő** > **hardverállapot**. A **megosztott összetevők**, tekintse meg az akkumulátor állapotát.
2. Ahol sikertelen volt az akkumulátor a PCM azonosítása. 1. ábra a StorSimple-eszköz-jének mutatja.
   
    ![Az eszköz elsődleges ház modulok csatlakozópanel meghibásodása](./media/storsimple-battery-replacement/IC740994.png)
   
    **1. ábra** oldalán PCM és vezérlő modulok megjelenítő elsődleges eszköz
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |0. vezérlő |
   | 4 |1. vezérlő |
   
    A 2. ábrán a 3-as számú eredményobjektumokról állapotjelző vezetett a PCM 0-ban, amely megfelel a **tartalék akkumulátor** kell kell kapcsolni.
   
    ![Az eszköz PCM figyelési jelző LED-ek csatlakozópanel meghibásodása](./media/storsimple-battery-replacement/IC740992.png)
   
    **2. ábra** vissza a PCM monitorozási jelző LED-ek megjelenítése
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |AC áramszünet esetén |
   | 2 |Ventilátor hibája |
   | 3 |Tartalék akkumulátor |
   | 4 |A PCM OK |
   | 5 |Tartományvezérlő áramszünet esetén |
   | 6 |Kifogástalan állapotú akkumulátor |
3. A sikertelen akkumulátor a PCM eltávolításához kövesse [távolítsa el a PCM](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm).
4. A eltávolítva a PCM áthelyezése felfelé elforgatása az akkumulátor-modul leíró, az alábbi ábrán jelzett módon, és lekérheti a töltöttségi eltávolítás legfeljebb.
   
    ![Akkumulátor a PCM eltávolítása](./media/storsimple-battery-replacement/IC741019.png)
   
    **3. ábra** az akkumulátor a PCM eltávolítása
5. A modul a terepen egység csomagolására helyezze el.
6. A hibás egység térjen vissza a Microsoft a megfelelő karbantartási és kezelésére.

## <a name="install-a-new-backup-battery-module"></a>Egy új biztonsági mentési akkumulátor-modul telepítése
A következő lépésekkel található az a StorSimple eszköz elsődleges ház a PCM cseréje akkumulátor-modul telepítéséhez.

#### <a name="to-install-the-battery-module"></a>Az akkumulátor-moduljának telepítése
1. Helyezze el a biztonsági mentési akkumulátor modul az a PCM a megfelelő tájolásban.
2. Nyomja le az akkumulátor-modul leíró, egészen az összekötő felhasználószám.
3. A PCM az elsődleges a ház cseréje az alábbi az irányelveket a [cserélje le a Power és hűtéssel modul a StorSimple eszköz](storsimple-8000-power-cooling-module-replacement.md).
4. A csere befejeződése után nyissa meg az eszközt, és folytassa a **figyelő** > **hardverállapot** az Azure Portalon. Győződjön meg arról, hogy a telepítés sikeres volt-e, hogy az akkumulátor állapotának ellenőrzése. Zöld állapot azt jelzi, hogy az akkumulátor kifogástalan állapotú.

## <a name="maintain-the-backup-battery-module"></a>A biztonsági mentési akkumulátor modul karbantartása
A StorSimple-eszköz a biztonsági mentési akkumulátor modul biztosít a vezérlő energiagazdálkodási energiagazdálkodási adatvesztési esemény során. Lehetővé teszi a StorSimple-eszköz menteni a kritikus fontosságú adatokat, amelyek mellett szabályozott módon leállítása előtt. A két akkumulátor feltöltött a PCMs a rendszer két egymást követő adatvesztési eseményeket képes kezelni.

Az Azure Portalon a **hardverállapot** alatt a **figyelő** panel azt jelzi, hogy az akkumulátor hibás, vagy a teljes életciklusa közeledik. Telep állapota jelzi **akkumulátor a PCM 0-ban** vagy **akkumulátor a PCM 1** alatt **megosztott összetevők**. Ezen a panelen megjelenik egy **csökkentett teljesítményű** hamarosan eléri a teljes életciklusa, az állami és **sikertelen** teljes életciklusa elérte a.

> [!NOTE]
> Az akkumulátor jelenthetik **sikertelen** ha egyszerűen kell számítunk fel díjat.


Ha a **csökkentett teljesítményű** állapot jelenik meg, javasoljuk, hogy a következő lépések:

* Előfordulhat, hogy a rendszer észlelt egy közelmúltbeli áramkimaradás vagy az akkumulátor előfordulhat, hogy legyen karbantartás alatt áll rendszeres. Figyelje meg a rendszer a folytatás előtt 12 órán át.
  
  * Ha az állapot továbbra is **csökkentett teljesítményű** után 12 órányi folyamatos kapcsolatot AC power a tartományvezérlők és PCMs fut, majd az akkumulátor kell cserélni. Adjon [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) egy biztonsági mentési akkumulátor cseréje modul.
  * Ha az állapot rendben 12 óra elteltével válik, az akkumulátor már működik, és csak szükség a karbantartási számítunk fel.
* Ha nem lett egy társított hálózati áramellátás Visszaállt elvesztését, és a PCM van kapcsolva és csatlakoztatva hálózati áramellátás Visszaállt, az akkumulátor kell cserélni. [Forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) sorrendjének a biztonsági mentési akkumulátor cseréje modul.

> [!IMPORTANT]
> Tud megszabadulni a sikertelen akkumulátor nemzeti és regionális előírásoknak megfelelően.

## <a name="next-steps"></a>További lépések
Tudjon meg többet [StorSimple összetevő hardvercseréhez](storsimple-8000-hardware-component-replacement.md).

