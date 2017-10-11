---
title: "A StorSimple virtuális tömb szolgáltatás összefoglaló panel |} Microsoft Docs"
description: "Ismerteti a service összefoglaló panel StorSimple Device Manager, és ismerteti a StorSimple virtuális tömb állapotának figyelése céljából."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: 8a2b9a84-b0e6-48b9-b366-d16f004241a5
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2016
ms.author: manuaery
ms.openlocfilehash: 284e404c44505a98d9e0ed5abe87cd945415af56
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Használja a szolgáltatás összefoglaló panel StorSimple Device Manager kapcsolódik a StorSimple virtuális tömb
## <a name="overview"></a>Áttekintés
A szolgáltatás összefoglaló panelre a StorSimple eszköz Manager összegzését jeleníti meg a StorSimple virtuális tömbök (más néven StorSimple a helyszíni virtuális eszközök vagy virtuális eszközök), amely kapcsolódik a szolgáltatáshoz, azok a rendszergazda figyelmet igénylő kiemelve. Ez az oktatóanyag vezet be a szolgáltatás összefoglalás panelről, a tartalom és funkcióját ismerteti, és azokat a feladatokat hajthat végre ezen a panelen.

![Szolgáltatási irányítópult](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Felügyeleti parancsok és alapjai
A StorSimple összefoglaló panelen látható a StorSimple eszköz kezelő szolgáltatás, valamint a virtuális tömbök a szolgáltatásra regisztrált kezelésére szolgáló beállítások. A parancsok látja a panelt és bal oldalán látható.

Adja hozzá ezeket a beállításokat, például a különböző műveletek végrehajtásához, megosztások vagy kötetek vagy a figyelő a különböző virtuális tömbök kiszolgálón futó feladatok használatát.

Az essentials területen rögzíti a fontos tulajdonságok, mint például az erőforráscsoportot, helyét és előfizetés, amelyben a StorSimple Device Manager készült.

## <a name="storsimple-device-manager-service-summary"></a>StorSimple Device Manager szolgáltatás összegzése
* A **riasztások** csempe biztosít az összes aktív riasztás pillanatkép virtuális eszközön, riasztások súlyosság szerint csoportosítva. A csempére kattintva megnyithatja a **riasztások** panel, ahol egyéni riasztást, hogy a riasztással kapcsolatos további részletek megtekintéséhez kattintson többek között a javasolt műveletek. A riasztás is törölheti, ha a probléma megoldódott.
* A **kapacitás** csempe megjeleníti a kiépített és a fennmaradó relatív a teljes tárterület érhető el az összes virtuális eszközön van az összes virtuális eszközön elsődleges tárterület megjelenítése. **Kiépített** hivatkozik, amely készített, és használatra, tárolókapacitást **fennmaradó** hivatkozik, amely az összes virtuális eszközön létesíthetők kapacitása. A **fennmaradó rétegzett** kapacitása a rendelkezésre álló kapacitásból, amelyek többek között a felhő, létesíthetők közben a **fennmaradó helyi** a kapacitás, a virtuális tömbök csatlakoztatott lemezek fennmaradó.
* Az a **használati** diagram, a kapcsolódó metrikák látható a virtuális eszközök. Megtekintheti az összes virtuális eszközön használt elsődleges tárolására, valamint az elmúlt 7 napban, az alapértelmezett időszak virtuális eszközök által használt felhőbeli tárhelyhez. Használja a **szerkesztése** lehetőséget a jobb felső sarkában található a diagram válassza egy másik időskálára.
* A **eszközök** csempe virtuális tömbök a StorSimple Eszközkezelőben eszköz állapot szerint csoportosítva száma összegzését tartalmazza. Kattintson a csempére kattintva nyissa meg a **eszközök** panel listára, majd elemezze az eszközhöz tartozó eszközök összegzése az egyes eszköz. Adott eszközműveletek egy adott eszköz összefoglaló paneljéről is elvégezheti. Az eszköz összefoglaló panel kapcsolatos további információkért látogasson el [eszköz összefoglaló panel](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>A tevékenység naplók megtekintése
A különböző műveletek a StorSimple Device Manager belül elvégzett megtekintéséhez kattintson a **tevékenységi naplóit** hivatkozás a StorSimple szolgáltatás összefoglaló panel bal oldalán. Ehhez szükséges, hogy a **tevékenységi naplóit** panel, ahol láthatja a legutóbbi műveletek összegzését.

![Tevékenységnaplók](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>Következő lépések
Megtudhatja, hogyan [a helyi webes felhasználói felület segítségével felügyelheti a StorSimple virtuális tömb](storsimple-ova-web-ui-admin.md).

