---
title: Használja a StorSimple 8000 sorozatú eszköz összegzésének |} A Microsoft Docs
description: A StorSimple-Eszközkezelő szolgáltatás eszköz összegzésének és használható a storage-mérőszámok és csatlakoztatott kezdeményezők megtekintheti, és keresse meg a sorozatszám és IQN ismerteti.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 1d88af2c0739c30b2562bad7660015b890e8159c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578222"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Az eszköz StorSimple-Eszközkezelő szolgáltatás összegzésének használata

## <a name="overview"></a>Áttekintés
A StorSimple eszköz összefoglalás panelje áttekintheti az adatokat egy adott StorSimple-eszköz szakembereket a szolgáltatásösszegző panel, amely tartalmazza a Microsoft Azure StorSimple megoldás az összes eszköz adatait jeleníti meg.

Az összefoglalás panel a StorSimple 8000 sorozatú eszköz, amely regisztrálva van a megadott StorSimple-Eszközkezelő, összefoglaló áttekintést nyújt a kiemelés eszközt egy rendszergazda figyelmet igénylő problémák. Ebben az oktatóanyagban az összefoglalás panel vezet be, ismerteti a tartalom és a függvény és azokat a feladatokat hajthat végre ezen a panelen.

Az összefoglalás panel az alábbi információkat jeleníti meg:

![Eszköz összefoglalás panelje](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Felügyeleti parancssávon

A StorSimple eszköz paneljén kezelése a StorSimple-eszköz beállításainak megjelenítéséhez. A parancsok és a bal oldalon a panel tetején megjelenik. Ezek a beállítások segítségével adja hozzá a megosztások vagy kötetek, vagy frissítse vagy az eszköz feladatainak átadása.

![Felügyeleti parancssávon](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Alapvető erőforrások

Az essentials területen rögzíti, például a fontos tulajdonságait, állapotát, modell, cél IQN neve és a szoftververzió. 

![Eszköz alapjai](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Figyelés

* A **riasztások** csempe egy pillanatkép összes aktív riasztás biztosít az eszközt, a riasztás súlyossága szerint csoportosítva.

    ![Riasztások csempe](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Kattintson a csempére kattintva nyissa meg a **riasztások** panelen, majd kattintson a további információhoz a riasztásokhoz, így minden egyes riasztás javasolt műveleteket. A riasztás is törölheti, ha a probléma megoldódott.

    ![Kattintson a riasztás csempe](./media/storsimple-8000-device-dashboard/device-summary10.png)

* A **állapotának** csempe révén betekintést kaphat a hardver összetevő állapota egy eszközhöz, beleértve az eszköz állapotát. Az eszköz állapota lehet offline állapotban van, online, inaktív vagy beállításra kész.

    ![Állapot csempe](./media/storsimple-8000-device-dashboard/device-summary5.png)

* A **kötetek** csempe állapot szerint csoportosítja az eszköz a kötetek számát összegzését tartalmazza.

    ![Kötetek csempe](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Kattintson a csempére kattintva nyissa meg a **kötetek** panel listában, és kattintson az egyéni kötet megtekintéséhez vagy a hozzá tartozó tulajdonságok módosításával.
    
    ![Kattintson a kötetek csempe](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    További információkért lásd: hogyan [kötetek kezelése](storsimple-8000-manage-volumes-u2.md).

* Az a **használati** diagramra, megtekintheti az eszközön használt elsődleges tároló, és a felhőalapú tárolás az elmúlt 7 napban, az alapértelmezett időszakban felhasznált.

     ![Használat csempe](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Válasszon egy másik időpontot méretezési, használja a **szerkesztése** a diagram jobb felső sarokban lévő beállítást.

     ![Használati diagram szerkesztése](./media/storsimple-8000-device-dashboard/device-summary12.png)

     Ezen a diagramon az összes elsődleges tárhely (a gazdagépeket az eszköz által írt adatok mennyisége) és a egy időszakon belül az eszköz által felhasznált teljes felhőalapú tárolás metrikáinak is megtekintheti.
  
     Ebben a környezetben *elsődleges tárolási* hivatkozik a gazdagép által írt adatok teljes mennyisége, és a kötet típusa szerint kell csoportosítani: *elsődleges rétegzett tárolási* egyaránt helyileg tárolt rétegzett adatok és adatok a felhőben. *Elsődleges helyileg rögzített tároló* csak az helyileg tárolt adatokat is tartalmaz. *Felhőalapú tárolás*, másrészről, van egy mérték, a felhőben tárolt adatok teljes mennyisége. Ez a tároló magában foglalja a rétegzett adatok és a biztonsági mentéseket. A felhőben tárolt adatok deduplikált, és a tömörített, mivel az elsődleges tárolási azt jelzi, hogy a előtt az adatok tömörítve és deduplikált felhasznált tárterület mérete. (A két szám, hogy képet kapjon a tömörítési arány a összehasonlíthatja). Mindkét elsődleges és a felhőalapú tárolást, az összegek alapulnak a nyomon követési gyakoriság konfigurálása. Például ha úgy dönt, hogy az egy hét gyakoriságát, majd a diagram adatai jelennek meg az előző hét naponta.

     Felhőbeli tárhely időszakban felhasznált megtekintéséhez válassza ki a **CLOUD STORAGE használt** lehetőséget. A teljes tárterület a gazdagép által írt, jelölje ki a **elsődleges RÉTEGZETT tárolási használt** és **elsődleges HELYILEG rögzített tárolására használt** beállítások. 
     További információkért lásd: [a StorSimple-Eszközkezelő szolgáltatás segítségével figyelheti a StorSimple-eszköz](storsimple-monitor-device.md).


* A **kapacitás** csempe megjeleníti az elsődleges tárolási ugyanahhoz a rendelkezésre álló tárhelyet képest az eszköz kiépítése és a fennmaradó is. **Üzembe helyezett** hivatkozik, amely készített, és használatra, lefoglalt tárhely **fennmaradó** hivatkozik a fennmaradó kapacitás, amelyek kioszthatóak az eszközön. 

    ![Használat csempe](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Kattintson a csempe megtekintéséhez, hogy ki van építve a kapacitás rétegzett és a helyileg rögzített kötetek között. A **fennmaradó rétegzett** kapacitás a rendelkezésre álló kapacitást, amelyek kioszthatóak többek között a felhőben, amíg a **fennmaradó helyi** erre az eszközre csatlakoztatott lemezen fennmaradó kapacitás.

    ![Kattintson a használati diagramon](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>További lépések
* Tudjon meg többet a [StorSimple szolgáltatás összefoglalás panelén](storsimple-8000-service-dashboard.md).
* Tudjon meg többet [a StorSimple-Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md).

