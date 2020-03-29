---
title: StorSimple Virtual Array eszköz összefoglaló panel | Microsoft dokumentumok
description: Ismerteti a StorSimple Eszközkezelő eszközösszefoglaló paneljét, és ismerteti, hogyan használhatja a StorSimple virtuális tömb állapotának figyelésére.
services: storsimple
documentationcenter: ''
author: manuaery
manager: syadav
editor: ''
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 9edc0b552f5c2f38e646bc4b44dd8df5c16b0457
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61408506"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>A StorSimple virtuális tömbhöz csatlakoztatott StorSimple Eszközkezelő eszközösszesítő paneljének használata

## <a name="overview"></a>Áttekintés

A StorSimple Eszközkezelő eszközpanel egy adott StorSimple Eszközkezelővel regisztrált StorSimple virtuális tömb összefoglaló nézetét tartalmazza, kiemelve azokat az eszközproblémákat, amelyek a rendszergazda figyelmére szorulnak. Ez az oktatóanyag bemutatja az eszköz összefoglaló paneljét, ismerteti a tartalmat és a funkciót, és ismerteti az ebből a panelből végrehajtható feladatokat.

A készülék összefoglaló paneljén a következő információk jelennek meg:

![Az eszköz irányítópultja](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Kezelés

A StorSimple eszköz panelen láthatja a StorSimple-eszköz kezeléséhez. A felügyeleti parancsok a penge tetején és a bal oldalon jelennek meg. Ezekkel a beállításokkal megosztásokat vagy köteteket adhat hozzá, vagy frissítheti vagy átveheti a virtuális tömböt.

A lényegi terület rögzíti néhány fontos tulajdonság, mint például az állapot, modell, szoftver verzió, valamint egy linket a **webes felhasználói felület** a tömb. Ha belső hálózatot kezel, közvetlenül elindíthatja a [helyi webes felhasználói felületet](storsimple-ova-web-ui-admin.md) a virtuális tömb felügyeletéhez.

![Az eszköz alapvető fontosságú](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>StorSimple eszköz összegzése

* A **Riasztások** csempe pillanatképet biztosít a virtuális tömb összes aktív riasztásáról, riasztássúlyosság a szerint csoportosítva. Kattintson a csempére a **Riasztások** panel megnyitásához, majd kattintson egy adott riasztásra a riasztástovábbi részleteinek megtekintéséhez, beleértve az ajánlott műveleteket is. A riasztást törölheti is, ha a probléma megoldódott.

* A **Kapacitás** csempe megjeleníti az elsődleges tároló, amely ki van építve, és a virtuális eszközön marad az azonos számára rendelkezésre álló teljes tárterülethez képest. **Kiépített** utal, hogy a tárolási összeg, amely előkészített és használatra lefoglalt, **Fennmaradó** utal a fennmaradó kapacitás, amely kiépíthető az eszközön keresztül. A **fennmaradó rétegzett** kapacitás a rendelkezésre álló kapacitás, amely kiépíthető, beleértve a felhőt, míg a **fennmaradó helyi** a virtuális tömbhöz csatlakoztatott lemezeken fennmaradó kapacitás.

* A **Használat** diagramban megtekintheti a virtuális tömbben használt elsődleges tárhelyet, valamint az elmúlt 7 nap ban felhasznált felhőalapú tárhelyet, az alapértelmezett időszakot. A diagram jobb felső sarkában található **Szerkesztés** beállítással válasszon másik időskálát.

* A **Megosztások** vagy **kötetek** csempe az eszköz megosztásainak vagy köteteinek állapotát összegző számát tartalmazza. Kattintson a csempére a **Megosztások** vagy **a Kötetek** lista panel megnyitásához, majd kattintson egy adott megosztásra vagy kötetre a tulajdonságainak megtekintéséhez vagy módosításához. További információt a [megosztások kezeléséről](storsimple-virtual-array-manage-shares.md) és [a kötetek kezeléséről](storsimple-virtual-array-manage-volumes.md)talál.

## <a name="next-steps"></a>További lépések
Az alábbiak végrehajtásának módját ismerheti meg:
- [Megosztások kezelése storSimple virtuális tömbön](storsimple-virtual-array-manage-shares.md)
    
- [Kötetek kezelése StorSimple virtuális tömbön](storsimple-virtual-array-manage-volumes.md)

