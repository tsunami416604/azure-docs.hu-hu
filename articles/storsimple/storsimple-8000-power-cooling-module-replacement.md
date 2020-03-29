---
title: Cseréljen le egy PCM-et a StorSimple 8000 sorozatú eszközre | Microsoft dokumentumok
description: A cikk bemutatja, hogyan távolítható el és cserélhető ki a Power and Cooling Module (PCM) a StorSimple eszközön
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60632463"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Az energiaellátási és hűtési rendszer cseréje a StorSimple-eszközön
## <a name="overview"></a>Áttekintés
A Power and Cooling Module (PCM) a Microsoft Azure StorSimple eszköz ben áll a tápegység és a hűtési ventilátorok, amelyek vezérlik az elsődleges és EBOD burkolatok. A PCM-nek csak egy olyan modellje van, amely minden házhoz rendelkezik. Az elsődleges ház 764 W-os PCM tanúsítvánnyal rendelkezik, az EBOD ház pedig 580 W-os PCM tanúsítvánnyal rendelkezik. Bár az elsődleges ház és az EBOD-ház PCM-jei eltérőek, a csereeljárás azonos.

Ez az oktatóanyag a következőket ismerteti:

* PCM eltávolítása
* Csere PCM telepítése

> [!IMPORTANT]
> A PCM eltávolítása és cseréje előtt tekintse át a Biztonsági információkat a [StorSimple hardverösszetevő-csere területén.](storsimple-8000-hardware-component-replacement.md)


## <a name="before-you-replace-a-pcm"></a>PcM cseréje előtt
A PCM cseréje előtt vegye figyelembe az alábbi fontos problémákat:

* Ha a PCM áramellátása meghibásodik, hagyja a hibás modult telepítve, de távolítsa el a tápkábelt. A ventilátor továbbra is kap energiát a burkolat, és továbbra is biztosítja a megfelelő hűtés. Ha a ventilátor meghibásodik, a PCM-et azonnal ki kell cserélni.
* A PCM eltávolítása előtt húzza ki a hálózati áramot a PCM-ből a főkapcsoló kikapcsolásával (ahol van), vagy fizikailag távolítsa el a tápkábelt. Ez figyelmezteti a rendszert arra, hogy áramkimaradás várható.
* A hibás PCM cseréje előtt győződjön meg arról, hogy a másik PCM működőképes a rendszer folyamatos működéséhez. A hibás PCM-et a lehető leghamarabb teljesen működőképes PCM-re kell cserélni.
* A PCM modul cseréje mindössze néhány percet vesz igénybe, de a túlmelegedés elkerülése érdekében a meghibásodott PCM eltávolítását követő 10 percen belül be kell fejezni.
* Ne feledje, hogy a gyárból szállított 764 W-os PCM modulok nem tartalmazzák a tartalék akkumulátormodult. A csere előtt ki kell venni az akkumulátort a hibás PCM-ből, majd be kell helyeznie a cseremodulba. További információt a [tartalék akkumulátormodul eltávolításáról és behelyezéséről](storsimple-8000-battery-replacement.md)talál.

## <a name="remove-a-pcm"></a>PCM eltávolítása
Kövesse ezeket az utasításokat, ha készen áll a power and cooling module (PCM) eltávolítására a Microsoft Azure StorSimple eszközről.

> [!NOTE]
> A PCM eltávolítása előtt ellenőrizze, hogy rendelkezik-e megfelelő cserével (764 W az elsődleges házhoz vagy 580 W az EBOD házhoz).

#### <a name="to-remove-a-pcm"></a>PCM eltávolítása
1. A klasszikus Azure-portálon kattintson a **Beállítások > > hardver állapotának figyelése**elemre. Ellenőrizze a PCM-összetevők állapotát a Megosztott összetevők csoportban, hogy **megállapítsa,** melyik PCM nem sikerült:
   
   * Ha a PCM 0 tápegysége meghibásodott, a **PCM 0 tápegységének** állapota piros lesz.
   * Ha a PCM 1 tápegysége meghibásodott, a **PCM 1 tápegységének** állapota piros lesz.
   * Ha a PCM 1 ventilátora meghibásodott, a **0 hűtés pcm 0** vagy a **PCM 0 hűtése állapota** piros lesz.
2. Keresse meg a sikertelen PCM hátoldalán az elsődleges ház. Ha 8600-as modellt futtat, azonosítsa az elsődleges burkolatot az előlapon látható rendszeregység-azonosító számmal. Az elsődleges házon megjelenő alapértelmezett egységazonosító **00**, míg az EBOD házon megjelenő alapértelmezett egységazonosító **01**. Az alábbi ábra és táblázat ismerteti a LED-kijelző előlapját.
   
    ![Rendszerazonosító az előlapi OPS panelen](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **1. ábra** A készülék előlapján  
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Elnémítás gomb |
   | 2 |A rendszer teljesítménye |
   | 3 |Modulhiba |
   | 4 |Logikai hiba |
   | 5 |Egységazonosító megjelenítése |
3. Az elsődleges ház hátulján található ellenőrző indikátor LED-ek a hibás PCM azonosítására is használhatók. Az alábbi ábrán és táblázatban megtudhatja, hogyan lehet a LED-ek segítségével megtalálni a hibás PCM-et. Ha például a **ventilátorhiba-nak** megfelelő LED világít, a ventilátor meghibásodott. Hasonlóképpen, ha az **AC Fail-nak** megfelelő LED világít, a tápegység meghibásodott. 
   
    ![Hátlap az eszköz PCM monitoring jelző LED-ek](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **2. ábra** A PCM háttora kijelző LED-ekkel
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |Hálózati áramkimaradás |
   | 2 |Ventilátor hiba |
   | 3 |Akkumulátor hiba |
   | 4 |PCM – rendben |
   | 5 |DC áramkimaradás |
   | 6 |Az akkumulátor kifogástalan |
4. A sikertelen PCM-modul megkereséséhez tekintse meg a StorSimple-eszköz hátrészének alábbi ábráját. A PCM 0 a bal oldalon, a PCM 1 pedig a jobb oldalon található. Az alábbi táblázat ismerteti a modulokat.
   
     ![Az eszköz elsődleges házmoduljainak hátlapja](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **3. ábra** A készülék háttere plug-in modulokkal 
   
   | Címke | Leírás |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Vezérlő 0 |
   | 4 |Vezérlő 1 |
5. Kapcsolja ki a hibás PCM-et, és húzza ki a tápkábelt. Most már eltávolíthatja a PCM-et.
6. Fogja meg a reteszt és a PCM fogantyú oldalát a hüvelyk- és mutatóujja között, és nyomja össze őket a fogantyú kinyitásához.
   
    ![PCM-leíró megnyitása](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **4.** A PCM-fogantyú megnyitása
7. Fogja meg a fogantyút, és távolítsa el a PCM-et.
   
    ![Eszköz PCM eltávolítása](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **5. ábra** A PCM eltávolítása

## <a name="install-a-replacement-pcm"></a>Csere PCM telepítése
Kövesse ezeket az utasításokat a PCM storSimple-eszközbe való telepítéséhez. A csere PCM telepítése előtt győződjön meg arról, hogy behelyezte a tartalék akkumulátormodult (csak 764 W-os PCM-ekre vonatkozik). További információt a [tartalék akkumulátormodul eltávolításáról és behelyezéséről](storsimple-8000-battery-replacement.md)talál.

#### <a name="to-install-a-pcm"></a>PCM telepítése
1. Ellenőrizze, hogy a megfelelő csere PCM-et használja-e ehhez a házhoz. Az elsődleges háznak 764 W-os PCM-re van szüksége, az EBOD háznak pedig 580 W-os PCM-re. Ne próbálja meg használni az 580 W-os PCM-et az elsődleges házban, vagy a 764 W-os PCM-et az EBOD-házban. Az alábbi képen látható, hogy hol kell azonosítani ezt az információt a PCM-hez rögzített címkén.
   
    ![Eszköz PCM-címkéje](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **6. ábra** PCM címke
2. Ellenőrizze, hogy nem sérült-e meg a burkolat, különös figyelmet fordítva a csatlakozókra. 
   
   > [!NOTE]
   > **Ne szerelje fel a modult, ha a csatlakozócsapok elhajlottak.**
   > 
   > 
3. Ha a PCM fogantyú nyitott helyzetben van, csúsztassa a modult a burkolatba.
   
    ![Eszköz PCM telepítése](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **7. ábra** A PCM telepítése
4. Manuálisan zárja be a PCM-fogantyút. Kattanást kell hallania, ahogy a fogantyú reteszbekapcsol.
   
   > [!NOTE]
   > Annak érdekében, hogy a csatlakozócsapok bekapcsolódjanak, óvatosan megrántja a fogantyút anélkül, hogy kioldané a reteszt. Ha a PCM kicsúszik, az azt jelenti, hogy a reteszt a csatlakozók bekapcsolása előtt becsukták.
   
5. Csatlakoztassa a tápkábeleket az áramforráshoz és a PCM-hez.
6. Rögzítsék a feszültségmentesítő bálákat.
7. Kapcsolja be a PCM-et.
8. Ellenőrizze, hogy a csere sikeres volt-e: a StorSimple Eszközkezelő szolgáltatás Azure-portálján keresse meg az eszközt, majd lépjen a **Beállítások > a hardver állapotának >.** A **Megosztott összetevők**alatt a PCM állapotának zöldnek kell lennie.
   
   > [!NOTE]
   > Eltarthat néhány percig, amíg a csere PCM teljesen inicializálódik.

## <a name="next-steps"></a>További lépések
További információ a [StorSimple hardverösszetevő-cseréjéről.](storsimple-8000-hardware-component-replacement.md)

