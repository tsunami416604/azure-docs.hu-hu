---
title: StorSimple Virtual Array szolgáltatás összefoglaló panel | Microsoft dokumentumok
description: Ismerteti a StorSimple Eszközkezelő szolgáltatás összefoglaló panel, és elmagyarázza, hogyan használhatja a StorSimple virtuális tömb állapotának figyelésére.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 9c05bddaeb3c34400db1ec75c624ef00a85d9444
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "64720702"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>A StorSimple virtuális tömbhöz csatlakoztatott StorSimple Eszközkezelő szolgáltatás-összefoglaló paneljének használata
## <a name="overview"></a>Áttekintés
A StorSimple Eszközkezelő szolgáltatásösszefoglaló panelje összefoglaló nézetet biztosít a szolgáltatáshoz csatlakoztatott StorSimple virtuális tömbökről (más néven StorSimple helyszíni virtuális eszközökről vagy virtuális eszközökről), kiemelve azokat, amelyeknek rendszerre van szükségük az adminisztrátor figyelmét. Ez az oktatóanyag bemutatja a szolgáltatás összefoglaló panel, ismerteti a tartalom és a funkció, és ismerteti a feladatokat, amelyeket el lehet végezni ebből a panelből.

![Szolgáltatás irányítópultja](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Felügyeleti parancsok és alapvető tudnivalók
A StorSimple összefoglaló panelen láthatja a StorSimple Eszközkezelő szolgáltatás, valamint a szolgáltatáshoz regisztrált virtuális tömbök kezelésének lehetőségeit. A felügyeleti parancsok a penge tetején és a bal oldalon jelennek meg.

Ezekkel a beállításokkal különböző műveleteket hajthat végre, például megosztásokat vagy köteteket adhat hozzá, vagy figyelheti a virtuális tömbökön futó különböző feladatokat.

A lényegi terület rögzíti néhány fontos tulajdonságok, például az erőforráscsoport, a hely és az előfizetés, amelyben a StorSimple Eszközkezelő jött létre.

## <a name="storsimple-device-manager-service-summary"></a>StorSimple Eszközkezelő szolgáltatás összegzése
* A **Riasztások** csempe pillanatképet biztosít az összes virtuális eszközön az összes aktív riasztásról, riasztási súlyosság szerint csoportosítva. A csempére kattintva megnyílik a **Riasztások** panel, ahol egy adott riasztásra kattintva megtekintheti a riasztástovábbi részleteit, beleértve az ajánlott műveleteket is. A riasztást törölheti is, ha a probléma megoldódott.
* A **Kapacitás** csempe megjeleníti az elsődleges tároló, amely ki van építve, és az összes virtuális eszközök képest a teljes rendelkezésre álló tárterület az összes virtuális eszközök. **Kiépített** utal, hogy a tárolási összeg, amely előkészített és használatra lefoglalt, **Fennmaradó** utal a fennmaradó kapacitás, amely kiépíthető az összes virtuális eszközök. A **fennmaradó rétegzett** kapacitás a rendelkezésre álló kapacitás, amely kiépíthető, beleértve a felhőt, míg a **fennmaradó helyi** a virtuális tömbökhöz csatlakoztatott lemezeken fennmaradó kapacitás.
* A **Használat** diagramban láthatja a virtuális eszközök relevancia-mutatóit. Megtekintheti az összes virtuális eszközön használt elsődleges tárhelyet, valamint a virtuális eszközök által az elmúlt 7 napban felhasznált felhőalapú tárhelyet, az alapértelmezett időszakot. A diagram jobb felső sarkában található **Szerkesztés** beállítással válasszon másik időskálát.
* **Az Eszközök** csempe összegzést nyújt a StorSimple Eszközkezelőben lévő virtuális tömbök eszközállapot szerint csoportosítva. Kattintson erre a csempére az **Eszközök** lista panel megnyitásához, majd egy adott eszközre kattintva részletezze az eszköz eszközre jellemző összegzését. Eszközspecifikus műveleteket is végrehajthat egy adott eszközösszesítő panelről. Az eszköz összesítő paneljéről az [Eszköz összesítő paneljéről az Eszközösszegzés panelen](storsimple-virtual-array-device-summary.md)talál további információt.

## <a name="view-the-activity-logs"></a>A tevékenységnaplók megtekintése
A StorSimple Eszközkezelőben végrehajtott különböző műveletek megtekintéséhez kattintson a **Tevékenységnaplók** hivatkozásra a StorSimple szolgáltatás összefoglaló panelbal oldalán. Ez a **tevékenységnaplók** panelhez vezet, ahol megtekintheti a legutóbbi végrehajtott műveletek összegzését.

![Tevékenységnaplók](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [használhatja a helyi webes felhasználói felületet a StorSimple virtuális tömb felügyeletére.](storsimple-ova-web-ui-admin.md)

