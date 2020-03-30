---
title: A StorSimple 8000 sorozatú eszközök összegzésének használata | Microsoft dokumentumok
description: Ismerteti a StorSimple szolgáltatás összefoglaló panel, és elmagyarázza, hogyan használhatja a StorSimple-megoldás állapotának figyelésére.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: alkohli
ms.openlocfilehash: c174f6ce0fb3d40af953be205a7bfcca60fbfeec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267650"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>A StorSimple 8000 sorozatú eszköz szolgáltatásösszefoglaló paneljének használata

## <a name="overview"></a>Áttekintés

A StorSimple Eszközkezelő szolgáltatás összefoglaló panel en összefoglaló nézetet biztosít a StorSimple Eszközkezelő szolgáltatáshoz csatlakoztatott összes eszközről, kiemelve azokat az eszközöket, amelyeknek a rendszergazdának figyelmet kell fordítaniuk. Ez az oktatóanyag bemutatja a szolgáltatás összefoglaló panel, ismerteti az irányítópult tartalmát és funkcióját, és ismerteti a feladatokat, amelyeket el lehet végezni ezen az oldalon.

![Szolgáltatás összegzése](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Felügyeleti parancsok

A StorSimple szolgáltatás összefoglaló panelen láthatja a StorSimple Device Manager szolgáltatás és a szolgáltatáshoz regisztrált StorSimple 8000 sorozatú eszközök kezelésének lehetőségeit. A felügyeleti parancsok a penge tetején és a bal oldalon jelennek meg.

![Parancssáv](./media/storsimple-8000-service-dashboard/service-summary2.png)

Ezekkel a beállításokkal különböző műveleteket hajthat végre, például megosztásokat vagy köteteket adhat hozzá, vagy figyelheti a StorSimple-eszközökön futó különböző feladatokat.


## <a name="essentials"></a>Alapvető erőforrások

A lényegi terület rögzíti néhány fontos tulajdonságok, például az erőforráscsoport, a hely és az előfizetés, amelyben a StorSimple Eszközkezelő jött létre.

![Alapvető erőforrások](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>StorSimple Eszközkezelő szolgáltatás összegzése

* A **Riasztások** csempe pillanatképet biztosít az összes eszköz aktív riasztásairól, riasztássúlyosság szerint csoportosítva.

    ![Riasztások csempe](./media/storsimple-8000-service-dashboard/service-summary4.png)

    A csempére kattintva megnyílik a **Riasztások** panel, ahol egy adott riasztásra kattintva megtekintheti a riasztástovábbi részleteit, beleértve az ajánlott műveleteket is. A riasztást törölheti is, ha a probléma megoldódott.

    ![Kattintson a riasztások csempére](./media/storsimple-8000-service-dashboard/service-summary8.png)

* A **Kapacitás** csempe megjeleníti az elsődleges tároló, amely ki van építve, és az összes eszközön marad az összes eszközön elérhető teljes tárterülethez viszonyítva. **Kiépített** utal, hogy a tárolási összeg, amely előkészített és használatra lefoglalt, **Fennmaradó** utal a fennmaradó kapacitás, amely kiépíthető az összes eszközön.

    ![Kapacitás csempe](./media/storsimple-8000-service-dashboard/service-summary6.png)

    A **fennmaradó rétegzett** kapacitás a rendelkezésre álló kapacitás, amely kiépíthető felhővel együtt, míg a **fennmaradó helyi** a StorSimple 8000 sorozatú eszközökhöz csatlakoztatott lemezeken fennmaradó kapacitás.


* A **Használat** diagramban láthatja az eszközökre vonatkozó mutatókat. Megtekintheti az összes eszközön használt elsődleges tárhelyet, valamint az eszközök által az elmúlt 7 napban felhasznált felhőalapú tárhelyet, az alapértelmezett időszakot. 

    ![Használati csempe](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Másik időskála kiválasztásához használja a Diagram jobb felső sarkában található **Szerkesztés** beállítást.

     ![Kattintson a használati csempe](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Diagramadatok exportálása](./media/storsimple-8000-service-dashboard/service-summary11.png)

* Az **Eszközök** csempe a StorSimple 8000 sorozatú eszközök számát tartalmazza a StorSimple Eszközkezelőben az eszköz állapota szerint csoportosítva. 

    ![Eszközök csempe](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Kattintson erre a csempére az **Eszközök** lista panel megnyitásához, majd egy adott eszközre kattintva részletezze az eszköz eszközre jellemző összegzését. Eszközspecifikus műveleteket is végrehajthat egy adott eszközösszesítő panelről. Az eszköz összesítő paneljéről az [Eszköz összesítő paneljéről az Eszközösszegzés panelen](storsimple-8000-device-dashboard.md)talál további információt.

    ![Kattintson az eszközök csempére](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>A tevékenységnaplók megtekintése

A StorSimple Eszközkezelőben végrehajtott különböző műveletek megtekintéséhez kattintson a **Tevékenységnaplók** hivatkozásra a StorSimple szolgáltatás összefoglaló panelbal oldalán. Ez a **tevékenységnaplók** panelhez vezet, ahol megtekintheti a legutóbbi végrehajtott műveletek összegzését.

![Tevékenységnaplók](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>További lépések

* További információ [aStorSimple Eszközkezelő szolgáltatás használatáról a StorSimple-eszköz felügyeletéhez.](storsimple-8000-manager-service-administration.md)

