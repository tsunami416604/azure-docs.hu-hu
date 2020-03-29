---
title: A StorSimple 8000 sorozatú eszközök összegzésének használata | Microsoft dokumentumok
description: Ez a témakör a StorSimple Device Manager szolgáltatáseszköz-összegzését ismerteti, valamint azt, hogy miként tekintheti meg a tárolási mutatókat és a csatlakoztatott kezdeményezőket, és hogyan keresheti meg a sorozatszámot és az IQN-t.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60578222"
---
# <a name="use-the-device-summary-in-storsimple-device-manager-service"></a>Az eszközösszegzés használata a StorSimple Eszközkezelő szolgáltatásban

## <a name="overview"></a>Áttekintés
A StorSimple eszköz összefoglaló panel áttekintést nyújt egy adott StorSimple-eszköz, ellentétben a szolgáltatás összefoglaló panel, amely a Microsoft Azure StorSimple megoldásban szereplő összes eszköz adatait.

Az eszköz összefoglaló panel egy adott StorSimple Eszközkezelővel regisztrált StorSimple 8000 sorozatú eszköz összefoglaló nézetét tartalmazza, kiemelve azokat az eszközproblémákat, amelyek a rendszergazda figyelmére szorulnak. Ez az oktatóanyag bemutatja az eszköz összefoglaló paneljét, ismerteti a tartalmat és a funkciót, és ismerteti az ebből a panelből végrehajtható feladatokat.

A készülék összefoglaló paneljén a következő információk jelennek meg:

![Eszköz összefoglaló panelje](./media/storsimple-8000-device-dashboard/device-summary1.png)

## <a name="management-command-bar"></a>Felügyeleti parancssáv

A StorSimple eszköz panelen láthatja a StorSimple-eszköz kezeléséhez. A felügyeleti parancsok a penge tetején és a bal oldalon jelennek meg. Ezekkel a beállításokkal megosztásokat vagy köteteket adhat hozzá, illetve frissítheti vagy átveheti az eszközt.

![Felügyeleti parancssáv](./media/storsimple-8000-device-dashboard/device-summary2.png)

## <a name="essentials"></a>Alapvető erőforrások

A lényegi terület rögzíti néhány fontos tulajdonságok, mint például az állapot, modell, cél IQN, és a szoftver verziója. 

![Az eszköz alapvető fontosságú](./media/storsimple-8000-device-dashboard/device-summary3.png)

## <a name="monitoring"></a>Figyelés

* A **Riasztások** csempe pillanatképet ad az eszköz összes aktív riasztásáról, riasztássúlyosság a szerint csoportosítva.

    ![Riasztási csempe](./media/storsimple-8000-device-dashboard/device-summary4.png)

    Kattintson a csempére a **Riasztások** panel megnyitásához, majd kattintson egy adott riasztásra a riasztástovábbi részleteinek megtekintéséhez, beleértve az ajánlott műveleteket is. A riasztást törölheti is, ha a probléma megoldódott.

    ![Kattintson a riasztási csempére](./media/storsimple-8000-device-dashboard/device-summary10.png)

* Az **Állapot és állapot** csempe betekintést nyújt az eszköz hardverösszetevőjének állapotába, beleértve az eszköz állapotát is. Az eszköz állapota lehet offline, online, inaktiválva vagy készen áll a beállításra.

    ![Állapot- és állapotcsempe](./media/storsimple-8000-device-dashboard/device-summary5.png)

* A **Kötetek** csempe az eszköz ben lévő kötetek számát tartalmazza állapot szerint csoportosítva.

    ![Kötetek csempe](./media/storsimple-8000-device-dashboard/device-summary6.png)

    Kattintson a csempére a **Kötetek** lista panel megnyitásához, majd kattintson egy adott kötetre a tulajdonságainak megtekintéséhez vagy módosításához.
    
    ![Kattintson a Kötetek csempére](./media/storsimple-8000-device-dashboard/device-summary9.png)
    
    További információt a [kötetek kezeléséről](storsimple-8000-manage-volumes-u2.md)talál.

* A **Használat** diagramban megtekintheti az eszközön használt elsődleges tárhelyet, valamint az elmúlt 7 nap ban felhasznált felhőalapú tárhelyet, az alapértelmezett időszakot.

     ![Használati csempe](./media/storsimple-8000-device-dashboard/device-summary7.png)
    
     Másik időskála kiválasztásához használja a Diagram jobb felső sarkában található **Szerkesztés** beállítást.

     ![Használati diagram szerkesztése](./media/storsimple-8000-device-dashboard/device-summary12.png)

     Ebben a diagramban megtekintheti a teljes elsődleges tárterület (a gazdagépek által az eszközre írt adatok mennyisége) és az eszköz által egy adott időszakban felhasznált teljes felhőtárhely metrikákat.
  
     Ebben a környezetben az *elsődleges tároló* a gazdagép által írt adatok teljes mennyiségére vonatkozik, és kötettípus szerint bontható: az *elsődleges rétegzett tároló* tartalmazza a helyileg tárolt adatokat és a felhőbe rétegzett adatokat. *Az elsődleges helyileg rögzített tároló* csak helyileg tárolt adatokat tartalmaz. *A felhőalapú tárolás*ezzel szemben a felhőben tárolt adatok teljes mennyiségének mérése. Ez a tároló rétegzett adatokat és biztonsági másolatokat tartalmaz. A felhőben tárolt adatok deduplikálása és tömörítése, míg az elsődleges tároló az adatok deduplikálása és tömörítése előtt használt tárterület mennyiségét jelzi. (Összehasonlíthatja ezt a két számot, hogy képet kapjon a tömörítési arányról.) Mind az elsődleges, mind a felhőalapú tárolás esetén a megjelenített összegek a beállított követési gyakoriságon alapulnak. Ha például egy hetes gyakoriságot választ, akkor a diagram az előző hét minden napjára vonatkozó adatokat jelenít meg.

     Az idő múlásával felhasznált felhőalapú tárhely megtekintéséhez válassza a **CLOUD STORAGE USED** lehetőséget. A gazdagép által írt teljes tárterület megtekintéséhez válassza ki a **HASZNÁLT ELSŐDLEGES RÉTEGZETT TÁROLÓt** és az **ELSŐDLEGES HELYILEG RÖGZÍTETT STORAGE USED** beállításokat. 
     További információ: [A StorSimple Eszközkezelő szolgáltatás használata a StorSimple-eszköz figyeléséhez.](storsimple-monitor-device.md)


* A **Kapacitás** csempe megjeleníti az elsődleges tároló, amely ki van építve, és az eszközön marad az azonos számára rendelkezésre álló teljes tárterülethez képest. **Kiépített** utal, hogy a tárolási összeg, amely előkészített és használatra lefoglalt, **Fennmaradó** utal a fennmaradó kapacitás, amely kiépíthető az eszközön keresztül. 

    ![Használati csempe](./media/storsimple-8000-device-dashboard/device-summary8.png)

    Kattintson erre a csempére a kapacitás rétegzett és helyileg rögzített kötetek közötti kiépítésének megtekintéséhez. A **fennmaradó rétegzett** kapacitás a rendelkezésre álló kapacitás, amely kiépíthető, beleértve a felhőt, míg a **fennmaradó helyi** az eszközhöz csatlakoztatott lemezeken fennmaradó kapacitás.

    ![Kattintson a használati diagramra](./media/storsimple-8000-device-dashboard/device-summary13.png)


## <a name="next-steps"></a>További lépések
* További információ a [StorSimple szolgáltatás összefoglaló paneljéről.](storsimple-8000-service-dashboard.md)
* További információ [a StorSimple Eszközkezelő szolgáltatás használatáról a StorSimple-eszköz felügyeletéhez.](storsimple-8000-manager-service-administration.md)

