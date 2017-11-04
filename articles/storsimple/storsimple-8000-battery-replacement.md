---
title: "Cserélje le a Microsoft Azure StorSimple 8000 series eszköz akkumulátorról |} Microsoft Docs"
description: "Távolítsa el, cserélje le, és a biztonsági mentési akkumulátor modul a StorSimple eszköz karbantartása ismerteti."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 174a3163082594ea6a49b7f5a78857848f8f0566
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Cserélje le a biztonsági mentési akkumulátor modul a StorSimple eszköz

## <a name="overview"></a>Áttekintés
Az elsődleges ház Power- és hűtési modul (PCM) a Microsoft Azure StorSimple eszköz rendelkezik egy további akkumulátor csomagot. Ez a csomag biztosítja a power, így a StorSimple eszköz adatok mentése az elsődleges házhoz AC áramkimaradás esetén. Ez akkumulátor csomag nevezzük a *biztonsági mentési akkumulátor modul*. A biztonsági mentési akkumulátor modul csak a StorSimple eszköz (a EBOD ház nem tartalmaz a biztonsági mentési akkumulátor modul) az elsődleges ház létezik.

Ez az oktatóanyag azt ismerteti, hogyan:

* Távolítsa el a biztonsági mentési akkumulátor modul
* Egy új biztonsági mentési akkumulátor modul telepítése
* A biztonsági mentési akkumulátor modul karbantartása

> [!IMPORTANT]
> Mielőtt eltávolítása és cseréje egy biztonsági mentési akkumulátor modult, tekintse át a biztonsági információk a [StorSimple hardver összetevő cseréje bemutatása](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Távolítsa el a biztonsági mentési akkumulátor modul
A biztonsági mentési akkumulátor modul, a StorSimple eszközt a rendszer a terepen cserélhető Cisco egységet. Mielőtt a PCM van telepítve, az akkumulátor modul az eredeti csomagban kell tárolni. A következő lépésekkel távolítsa el a biztonsági mentési telep.

#### <a name="to-remove-the-backup-battery-module"></a>A biztonsági mentési akkumulátor modul eltávolítása
1. Az Azure portálon nyissa meg a StorSimple Device Manager szolgáltatás paneljét. Ugrás a **eszközök** majd az eszközök listájában válassza ki az eszköz. Navigáljon a **figyelő** > **hardver állapotának**. A **megosztott összetevők**, nézze meg az akkumulátor állapotát.
2. Azonosítsa a PCM, amelyben az akkumulátor sikertelen volt. 1. ábra mutatja a StorSimple eszköz hátulján olvasható.
   
    ![Az eszköz elsődleges ház modulok Csatlakozópanel](./media/storsimple-battery-replacement/IC740994.png)
   
    **1. ábra** hátsó PCM és a tartományvezérlő modulok megjelenítő elsődleges eszköz
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |A vezérlő 0 |
   | 4 |1. vezérlő |
   
    3 szám a 2. ábrán látható, a figyelési kijelző PCM 0, amely megfelel a kereslet az olyan **akkumulátor tartalék** kell kell kapcsolni.
   
    ![Az eszköz PCM figyelési kijelző LED Csatlakozópanel](./media/storsimple-battery-replacement/IC740992.png)
   
    **2. ábra** vissza a PCM a figyelési kijelző LED megjelenítése
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |AC áramszünet esetén |
   | 2 |Hiba ventilátor |
   | 3 |Akkumulátor hiba |
   | 4 |PCM OK |
   | 5 |DC áramszünet esetén |
   | 6 |Kifogástalan akkumulátor |
3. A sikertelen akkumulátor PCM eltávolításához kövesse [távolítsa el a PCM](storsimple-power-cooling-module-replacement.md#remove-a-pcm).
4. A eltávolított PCM növekedési felfelé elforgatása az akkumulátor modul leíróját a következő, az alábbi ábrán jelzett és lekéréses, akár az akkumulátor eltávolítása.
   
    ![PCM akkumulátor eltávolítása](./media/storsimple-battery-replacement/IC741019.png)
   
    **3. ábra** az akkumulátor eltávolítása a PCM
5. A modul a terepen cserélhető Cisco egységet csomagolására helyezze el.
6. Térjen vissza a hibás egység Microsoft megfelelő szervizelési és kezelése.

## <a name="install-a-new-backup-battery-module"></a>Egy új biztonsági mentési akkumulátor modul telepítése
A következő lépésekkel telepíti a helyettesítő akkumulátor modulját az elsődleges szolgáltatással a StorSimple eszköz PCM.

#### <a name="to-install-the-battery-module"></a>A akkumulátor modul telepítése
1. A biztonsági mentési akkumulátor modul elhelyezni a PCM a megfelelő helyzetben.
2. Nyomja le az akkumulátor modul leíróját a következő egészen az, hogy az összekötő ülés.
3. Cserélje le az elsődleges szolgáltatással PCM a következő [energia- és hűtési modul lecseréli a StorSimple eszköz](storsimple-power-cooling-module-replacement.md).
4. A Csere befejezése után nyissa meg az eszközre, és folytassa a **figyelő** > **hardver állapotának** az Azure portálon. Győződjön meg arról, hogy a telepítés sikeres volt-e az akkumulátor állapotának ellenőrzése. Zöld állapot azt jelzi, hogy az akkumulátor állapota kifogástalan.

## <a name="maintain-the-backup-battery-module"></a>A biztonsági mentési akkumulátor modul karbantartása
A StorSimple eszközt a a biztonsági mentési akkumulátor a modul adja meg a tartományvezérlő teljesítményét power adatvesztési esemény során. Lehetővé teszi a StorSimple eszköz szabályozott módon leállítása előtt kritikus adatok mentése. A két akkumulátor feltöltött a PCMs a rendszer két egymást követő adatvesztési eseményeket is kezelni.

Az Azure portálon a **hardver állapotának** alatt a **figyelő** panel jelzi, hogy a az akkumulátor hibásan működik, vagy a záró életciklusa közeledik. Akkumulátor állapotát jelzi **PCM 0 akkumulátor** vagy **PCM 1 akkumulátor** alatt **megosztott összetevők**. Ezen a panelen megjelenik egy **csökkentett teljesítményű** állapotban a(z) élettartam végi közeledik, és **sikertelen** end életciklusa elérte a.

> [!NOTE]
> Az akkumulátor is tud jelentéseket **sikertelen** ha egyszerűen kell számítjuk fel.


Ha a **csökkentett teljesítményű** állapot jelenik meg, az alábbi intézkedéseket javasoljuk:

* Előfordulhat, hogy a rendszer észlelt egy friss az áramellátás megszakadása miatt, vagy előfordulhat, hogy a az akkumulátorok rendszeres karbantartás zajlik. Vegye figyelembe a rendszer a folytatás előtt 12 óra.
  
  * Ha az állapot továbbra is **csökkentett teljesítményű** után a létrehozni kívánt AC folyamatos kapcsolat 12 óra kapcsolja a tartományvezérlők és PCMs fut, majd az akkumulátor kell lecserélni. Adjon [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) helyettesítő biztonsági mentési akkumulátor modulként.
  * A állapotba kerül, 12 óra elteltével az OK gombra, ha az akkumulátor működik, és azt csak akkor szükséges, a karbantartási kell fizetni.
* Ha nem lett az AC Power társított veszteséget és a PCM-e kapcsolva és csatlakoztatva AC power, az akkumulátor kell lecserélni. [Forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md) helyettesítő biztonsági mentési akkumulátor modul rendezéséhez.

> [!IMPORTANT]
> Számolja fel a sikertelen akkumulátor nemzeti és regionális szabályok szerint.

## <a name="next-steps"></a>Következő lépések
További információ [StorSimple hardver összetevő cseréje](storsimple-8000-hardware-component-replacement.md).

