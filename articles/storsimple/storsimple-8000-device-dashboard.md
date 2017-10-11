---
title: "A StorSimple 8000 series eszköz használata összefoglaló |} Microsoft Docs"
description: "A StorSimple Device Manager szolgáltatás eszközének összegzése és a storage mérőszámainak és csatlakoztatott kezdeményezők megtekintése, és keresse meg a sorozatszámot és IQN ismerteti."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 784d3ce9d8f926b00ac1c6fbf48a05c0b04f900a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Az eszköz StorSimple Device Manager szolgáltatásban összefoglaló használata

## <a name="overview"></a>Áttekintés
A StorSimple eszköz összefoglaló panel lehetővé teszi az adott StorSimple eszköz, a szolgáltatás összefoglaló panel, amelyen adatokat tartalmaz minden olyan eszközre, amelyet a Microsoft Azure StorSimple megoldásban ellentétben információk áttekintését.

Az eszköz összefoglaló panel összegzését jeleníti meg a StorSimple 8000 series eszköz regisztrálva van-e egy adott StorSimple az Eszközkezelő kiemelés egy rendszergazda figyelmet igénylő eszköz ismertetünk. Ez az oktatóanyag vezet be az eszköz összefoglaló panelen, a tartalom és funkcióját ismerteti, és azokat a feladatokat hajthat végre ezen a panelen.

Az eszköz összefoglaló panel az alábbi információkat jeleníti meg:

![Eszköz összefoglaló panel](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Felügyeleti parancssáv

A StorSimple eszköz paneljén láthatja a StorSimple eszköz kezelésére szolgáló beállítások. A parancsok látja a panelt és bal oldalán látható. Ezek a beállítások segítségével adja hozzá a megosztások vagy kötetek, vagy frissíteni, vagy az eszköz a feladatátvétel.

![Felügyeleti parancssáv](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Alapvető erőforrások

Az essentials területen rögzíti, például fontos tulajdonságait, állapotát, modell, cél IQN-Nevének és a szoftververzió. 

![Eszköz alapjai](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Figyelés

* A **riasztások** csempe pillanatképet az összes aktív riasztás biztosít az eszközt, a riasztás súlyosságát szerint csoportosítva.

    ![Riasztások csempe](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Kattintson a csempére kattintva nyissa meg a **riasztások** panel megnyitásához, és kattintson a riasztás, így azokat kapcsolatos további részletek megtekintéséhez egyéni riasztást javasolt műveletek. A riasztás is törölheti, ha a probléma megoldódott.

    ![Kattintson a riasztások csempe](./media/storsimple-8000-device-dashboard/device-summary10.png)

* A **állapotát és állapotfigyelő** csempe a hardver összetevő állapota betekintést biztosít egy eszközt, beleértve az eszköz állapotát. Az eszköz állapota lehet offline, online, inaktív vagy készen áll a beállítása.

    ![Állapot és állapota csempe](./media/storsimple-8000-device-dashboard/device-summary5.png)

* A **kötetek** csempe állapot szerint csoportosítva az eszköz a kötetek száma összegzését tartalmazza.

    ![Adatkötetek meghajtóbetűjelei](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Kattintson a csempére kattintva nyissa meg a **kötetek** listára panelen, majd a tulajdonságainak megtekintéséhez vagy módosításához egy egyéni köteten található.
    
    ![Kattintson az adatkötetek meghajtóbetűjelei](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    További információkért lásd: hogyan [kötetek kezelése](storsimple-8000-manage-volumes-u2.md).

* Az a **használati** diagram, megtekintheti az elsődleges használ, amelyek az eszközt, és a felhő tárolására az elmúlt 7 napban, az alapértelmezett időszak felhasznált.

     ![Használata csempe](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Válasszon egy másik időskálára, használja a **szerkesztése** a diagram jobb felső sarokban lehetőséget.

     ![Használati diagram szerkesztése](./media/storsimple-8000-device-dashboard/device-summary12.png)

     Ezen a diagramon az összes elsődleges (az állomások számára az eszköz által írt adatok mennyisége) és a teljes felhő tárolására egy meghatározott időtartamra vonatkozóan az eszköz által felhasznált metrikáját tekintheti meg.
  
     Ebben a környezetben *elsődleges tárolási* a gazdagép által írt adatok teljes mennyisége hivatkozik, és a kötet típusa szerint sorolhatók: *elsődleges rétegzett tárolás* egyaránt helyben tárolt adatok és az adatok a rétegzett a felhőben. *Elsődleges helyileg rögzített tárolási* csak helyben tárolja az adatokat tartalmazza. *Felhőbeli tárhely*, másrészt van a felhőben tárolt adatok teljes mennyisége mérését. Ez a tároló rétegzett adatok és a biztonsági mentések tartalmaz. A felhőben tárolt adatok deduplikált, és a tömörített, mivel elsődleges tárolási előtt az adatok deduplikációja és tömörített tárolókapacitást jelzi. (Ha meg szeretne ismerkedni a tömörítés és a két szám összehasonlíthatja). A mind az elsődleges és a felhőalapú tárolást látható összegek konfigurált követési gyakoriságnak alapulnak. Például ha úgy dönt, hogy az egy hét gyakoriságát, majd a diagram adatainak megjelenítése minden az előző hét nap.

     Felhő tárolókapacitást időbeli használni, jelölje ki a **FELHŐALAPÚ TÁROLÓT használja a** lehetőséget. A teljes tárterület a gazdagép által írt, jelölje ki a **elsődleges RÉTEGZETT TÁROLÓT használja a** és **elsődleges HELYILEG rögzített TÁROLÓT használja a** beállítások. 
     További információkért lásd: [a StorSimple Device Manager szolgáltatás segítségével figyelheti a StorSimple eszköz](storsimple-monitor-device.md).


* A **kapacitás** csempe megjeleníti az elsődleges tárolási kiépített és a fennmaradó tartozik ugyanahhoz a rendelkezésre álló tárhelyet viszonyítva az eszközt. **Kiépített** hivatkozik, amely készített, és használatra, tárolókapacitást **fennmaradó** hivatkozik, amely az eszköz közötti létesíthetők kapacitása. 

    ![Használata csempe](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Kattintson a csempe megtekintéséhez, hogy ki van építve a kapacitás rétegzett és helyileg rögzített kötetek között. A **fennmaradó rétegzett** kapacitása a rendelkezésre álló kapacitásból, amelyek többek között a felhő, létesíthetők közben a **fennmaradó helyi** erre az eszközre csatlakoztatott lemezeken maradt kapacitás.

    ![Kattintson a használati diagramon](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>Következő lépések
* További információ a [StorSimple szolgáltatás összefoglaló panel](storsimple-8000-service-dashboard.md).
* További információ [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

