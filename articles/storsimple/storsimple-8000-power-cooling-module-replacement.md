---
title: Az a StorSimple 8000 sorozatú eszköz a PCM cseréje |} A Microsoft Docs
description: Azt ismerteti, hogyan távolítsa el, és cserélje le a teljesítmény- és hűtési modul (PCM) a StorSimple-eszközön
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 42561570e24aec5edd33248ef1738e53175e480e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632463"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Cserélje le a Power és hűtéssel modul a StorSimple eszköz
## <a name="overview"></a>Áttekintés
Egy tápegység és hűtéssel ventilátorok szabályozott az elsődleges és a EBOD ház áll a teljesítmény és a hűtési modul (PCM) a Microsoft Azure StorSimple eszközön. Csak egy modellje, amely az összes rendszerházon minősítéssel PCM van. Az elsődleges ház egy 764 W PCM minősítéssel, és a egy 580 W PCM a EBOD ház minősítéssel. Bár a PCMs az elsődleges ház és a EBOD ház különböző, a csere eljárás megegyezik.

Ez az oktatóanyag a következőket ismerteti:

* A PCM eltávolítása
* Telepítse a PCM cseréje

> [!IMPORTANT]
> Mielőtt eltávolítása és a egy PCM cseréje tekintse át a biztonsági információk [StorSimple összetevő hardvercseréhez](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Mielőtt egy a PCM cseréje
Vegye figyelembe a következő fontos problémák a a PCM cseréje előtt:

* Ha nem sikerül, a PCM az áramellátás, a hibás modul telepítve hagyja, de a tápkábel eltávolítása. A ventilátor power fogadjon a ház, és továbbra is biztosítani a megfelelő hűtéssel továbbra is. Ha nem sikerül a ventilátor, a PCM kell azonnal cserélni.
* Mielőtt eltávolítaná a PCM, bontsa a kapcsolatot a teljesítmény a PCM kikapcsolja a fő kapcsoló (ha van ilyen), vagy az elektromos hálózathoz fizikailag eltávolításával. Ez biztosítja a rendszer figyelmeztetést, hogy a power leállítása rövidesen.
* Győződjön meg arról, hogy a többi PCM működési folyamatos rendszer működéséhez a hibás PCM cseréje előtt. Hibás PCM kell helyettesíteni egy teljesen működőképes PCM minél hamarabb.
* A PCM modul helyettesítő csak néhány percet vesz igénybe, de kell elvégezni, hogy életbe hibás PCM eltávolításának 10 percen belül.
* Vegye figyelembe, hogy a csere 764 W PCM modulok postáztuk az előállító nem tartalmaznak a biztonsági mentési akkumulátor modul. Szüksége lesz az akkumulátor távolítsa el a hibás PCM és beszúrni a csere modul előtt váltja fel. További információkért lásd: hogyan [távolítsa el, és a egy biztonsági mentési akkumulátor modul beszúrása](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>A PCM eltávolítása
Amikor elkészült, a Power és hűtéssel modul (PCM) eltávolítása a Microsoft Azure StorSimple-eszköz, kövesse az alábbi utasításokat.

> [!NOTE]
> Mielőtt eltávolítja a PCM, győződjön meg arról, hogy rendelkezik-e a megfelelő helyettesítő (az elsődleges ház W 764) vagy a EBOD ház a W 580.

#### <a name="to-remove-a-pcm"></a>A PCM eltávolítása
1. A klasszikus Azure portálon kattintson a **beállítások > figyelő > hardverállapot**. A PCM összetevők állapotának ellenőrzéséhez **Shared components** azonosítására PCM sikertelen volt:
   
   * Ha egy tápegység a PCM 0-ban nem sikerült, állapotának **tápegység a PCM 0-ban** piros színűvé változik.
   * Ha a PCM 1-tápegység sikertelen, az állapota **tápegység a PCM 1** piros színűvé változik.
   * Ha a PCM 1 összegyűjtésének sikertelen volt, vagy állapotának **0 hűtés a PCM 0-ban** vagy **1 hűtés a PCM 0-ban** piros színűvé változik.
2. Keresse meg a elsődleges ház-jének a hibás PCM. Ha egy 8600-as modell futtatja, azonosítsa az elsődleges ház megnézzük a előlapján LED megjelenített System-egység azonosító szám. Az alapértelmezett érték a jelenik meg a elsődleges tárolóeszközön egység azonosítója **00**, mivel az alapértelmezett érték a egység azonosítója megjelenik a EBOD tárolóeszközön **01**. A következő ábra és táblázat magyarázza el, a LED megjelenített előlapja.
   
    ![Az OPS előlapján rendszer-azonosító](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **1. ábra** az eszköz első panel  
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Vypnutí gomb |
   | 2 |Rendszer power |
   | 3 |A modul hibája |
   | 4 |Logikai hiba |
   | 5 |Egység azonosító megjelenítése |
3. A monitorozási jelző LED-ek az elsődleges ház hátulján is használható, a hibás PCM azonosítása. Tekintse meg a következő ábra és táblázat segít megérteni, hogyan keresse meg a hibás PCM a LED-ek használatával. Például ha a LED megfelelő a **ventilátor sikertelen** van bekapcsolásával; a ventilátor nem sikerült. Hasonlóképpen ha a LED megfelelő **AC sikertelen** van bekapcsolásával, az áramellátás sikertelen volt. 
   
    ![Az eszköz PCM monitorozási jelző LED-ek csatlakozópanel meghibásodása](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **2. ábra** vissza a PCM-jelző LED-EK
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |AC áramszünet esetén |
   | 2 |Ventilátor hibája |
   | 3 |Tartalék akkumulátor |
   | 4 |A PCM OK |
   | 5 |Tartományvezérlő áramszünet esetén |
   | 6 |Kifogástalan állapotú akkumulátor |
4. Tekintse meg a következő ábra a háttér keresse meg a hibás PCM modul a StorSimple eszközt. A bal oldalon a PCM 0-ban, és a jobb oldalon pedig a PCM 1. Az alábbi táblázat azt ismerteti, hogy a modulok.
   
     ![Az eszköz elsődleges ház modulok csatlakozópanel meghibásodása](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **3. ábra** oldalán a beépülő modulok rendelkező eszköz 
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |0. vezérlő |
   | 4 |1. vezérlő |
5. Kapcsolja ki a hibás PCM, és húzza ki a forrás. Most már eltávolíthatja a PCM.
6. A zárolás és az USB- és mutatóujj között a PCM leíró oldalán bonyolultnak, és azok egymáshoz nyissa meg a leíró nyomja össze.
   
    ![Nyitó PCM leíró](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **4. ábra** a PCM leíró megnyitása
7. A leíró fogja túl, és távolítsa el a PCM.
   
    ![A PCM eszköz eltávolítása](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **5. ábra** a PCM eltávolítása

## <a name="install-a-replacement-pcm"></a>Telepítse a PCM cseréje
Kövesse ezeket az utasításokat egy PCM telepítse a StorSimple-eszköz. Győződjön meg arról, hogy beszúrta-e a biztonsági mentési akkumulátor modult váltja fel a PCM (764 W PCMs vonatkozik) telepítése előtt. További információkért lásd: hogyan [távolítsa el, és a egy biztonsági mentési akkumulátor modul beszúrása](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>A PCM telepítése
1. Győződjön meg arról, hogy rendelkezik-e a megfelelő helyettesítő PCM a ház. Az elsődleges ház kell egy 764 W PCM, és a EBOD ház egy 580 W PCM van szüksége. A 580 W PCM az elsődleges ház a vagy a 764 W PCM a a EBOD ház használata nem meg kell próbálni. A következő képen látható határozza meg az adatokat a címke, amely a PCM rögzítettek.
   
    ![Eszköz PCM felirat](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **6. ábra** PCM felirat
2. Ellenőrizze, hogy a ház, különös tekintettel az összekötők kárt. 
   
   > [!NOTE]
   > **Ha minden olyan összekötő elgörbülve ne telepítse a modul.**
   > 
   > 
3. A PCM leíró nyitva, az ujját a modul a ház be.
   
    ![A PCM eszköz telepítése](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **7. ábra** a PCM telepítése
4. A PCM leíró manuálisan lezárja. Egy-két kattintással hallott kell, a leíró zárolás bekövetkezése várható.
   
   > [!NOTE]
   > Annak biztosítása érdekében, hogy az összekötő PIN-kód foglalkozó rendelkezik, akkor is óvatosan tug a leíró a a zárolás feloldása nélkül. A PCM kicsúszik, azt jelenti, hogy a zárolás bezárult, mielőtt az összekötők foglalkozó.
   
5. Csatlakozás a kábelek, az áramforrás és a PCM.
6. A törzs mentesség bálákban biztonságos.
7. Kapcsolja be a PCM.
8. Győződjön meg arról, hogy sikeres volt-e a csere: az Azure Portalon a StorSimple-Eszközkezelő szolgáltatás, keresse meg az eszköz majd **beállítások > figyelő > hardverállapot**. Alatt a **Shared components**, állapotát, a PCM zölden kell megjelennie.
   
   > [!NOTE]
   > A teljesen inicializálni a PCM cseréje néhány percig is eltarthat.

## <a name="next-steps"></a>További lépések
Tudjon meg többet [StorSimple összetevő hardvercseréhez](storsimple-8000-hardware-component-replacement.md).

