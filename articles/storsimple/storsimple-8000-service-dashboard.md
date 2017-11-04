---
title: "A StorSimple 8000 series eszköz használata összefoglaló |} Microsoft Docs"
description: "Ismerteti a StorSimple szolgáltatás összefoglaló panelre, és ismerteti a StorSimple megoldásban állapotának figyelése céljából."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: d987a4ae170f21532a886552cbe1eb5a0d25fc3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>A szolgáltatás összefoglaló panelre a StorSimple 8000 series eszköz használata

## <a name="overview"></a>Áttekintés

A StorSimple Device Manager szolgáltatás összefoglaló panel összegzését jeleníti meg az eszközök csatlakoznak a StorSimple Device Manager szolgáltatás, a rendszergazda figyelmet igénylő eszközök kiemelve. Ez az oktatóanyag vezet be a szolgáltatás összefoglaló panelre, ismerteti az irányítópult-tartalom és a függvény, és azokat a feladatokat hajthat végre ezen a lapon.

![Szolgáltatás összegzése](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Felügyeleti parancsok

A StorSimple szolgáltatás összefoglaló panelen látható a StorSimple eszköz Manager szolgáltatás és a StorSimple 8000 sorozat eszközeire, a szolgáltatásra regisztrált kezelésére szolgáló beállítások. A parancsok látja a panelt és bal oldalán látható.

![Parancssáv](./media/storsimple-8000-service-dashboard/service-summary2.png)

Adja hozzá ezeket a beállításokat, például a különböző műveletek végrehajtásához, megosztások vagy kötetek vagy a figyelő a StorSimple eszközön futó feladatok a különböző használja.


## <a name="essentials"></a>Alapvető erőforrások

Az essentials területen rögzíti a fontos tulajdonságok, mint például az erőforráscsoportot, helyét és előfizetés, amelyben a StorSimple Device Manager készült.

![Alapvető erőforrások](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>StorSimple Device Manager szolgáltatás összegzése

* A **riasztások** csempe biztosít az összes aktív riasztás pillanatképet minden eszközön, a riasztás súlyosságát szerint csoportosítva.

    ![Riasztások csempe](./media/storsimple-8000-service-dashboard/service-summary4.png)

    A csempére kattintva megnyithatja a **riasztások** panel, ahol egyéni riasztást, hogy a riasztással kapcsolatos további részletek megtekintéséhez kattintson többek között a javasolt műveletek. A riasztás is törölheti, ha a probléma megoldódott.

    ![Kattintson a riasztások csempe](./media/storsimple-8000-service-dashboard/service-summary8.png)

* A **kapacitás** csempe megjeleníti a kiépített és a fennmaradó relatív a teljes tárterület érhető el az összes eszközön van minden eszközön elsődleges tárterület megjelenítése. **Kiépített** hivatkozik, amely készített, és használatra, tárolókapacitást **fennmaradó** hivatkozik, amely az összes eszközön létesíthetők kapacitása.

    ![A kapacitás csempe](./media/storsimple-8000-service-dashboard/service-summary6.png)

    A **fennmaradó rétegzett** kapacitása a rendelkezésre álló kapacitásból, amelyek többek között a felhő, létesíthetők közben a **fennmaradó helyi** a kapacitás, a StorSimple 8000 csatlakoztatott lemezek fennmaradó sorozat eszközeire.


* Az a **használati** diagram, a kapcsolódó metrikák láthatja az eszközökhöz. Megtekintheti az összes eszközön használt elsődleges tárhely és az elmúlt 7 napban, az alapértelmezett időszak eszközök által használt felhőbeli tárhelyhez. 

    ![Használata csempe](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Válasszon egy másik időskálára, használja a **szerkesztése** a diagram jobb felső sarokban lehetőséget.

     ![Kattintson a használata csempe](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![A diagram adatok exportálása](./media/storsimple-8000-service-dashboard/service-summary11.png)

* A **eszközök** csempe a StorSimple 8000 series eszközök a StorSimple eszköz állapot szerint csoportosítva száma összegzését tartalmazza. 

    ![Eszközök csempe](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Kattintson a csempére kattintva nyissa meg a **eszközök** panel listára, majd elemezze az eszközhöz tartozó eszközök összegzése az egyes eszköz. Egy adott eszköz összefoglaló paneljéről eszközspecifikus műveletek is elvégezheti. Az eszköz összefoglaló panel kapcsolatos további információkért látogasson el [eszköz összefoglaló panel](storsimple-8000-device-dashboard.md).

    ![Kattintson az eszközök csempe](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>A tevékenység naplók megtekintése

A különböző műveletek a StorSimple Device Manager belül elvégzett megtekintéséhez kattintson a **tevékenységi naplóit** hivatkozás a StorSimple szolgáltatás összefoglaló panel bal oldalán. Ehhez szükséges, hogy a **tevékenységi naplóit** panel, ahol láthatja a legutóbbi műveletek összegzését.

![Tevékenységnaplók](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>Következő lépések

* További tudnivalók a [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

