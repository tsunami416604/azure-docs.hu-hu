---
title: "A StorSimple virtuális tömb eszköz összefoglaló panel |} Microsoft Docs"
description: "Az eszköz összefoglaló panel StorSimple Device Manager és a StorSimple virtuális tömb állapotának figyelésére segítségével ismerteti."
services: storsimple
documentationcenter: 
author: manuaery
manager: syadav
editor: 
ms.assetid: a13c1ea7-6428-4234-84a6-0ebf51670a85
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2016
ms.author: manuaery
ms.openlocfilehash: 35413d597c3b6b1c7600241a78572b63f982d175
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>Használja az eszköz összefoglaló panel StorSimple Device Manager kapcsolódik a StorSimple virtuális tömb

## <a name="overview"></a>Áttekintés

A StorSimple Device Manager eszköz panel összegzését jeleníti meg a StorSimple virtuális tömb, amely regisztrálva van-e egy adott StorSimple az Eszközkezelő kiemelés egy rendszergazda figyelmet igénylő eszköz ismertetünk. Ez az oktatóanyag vezet be az eszköz összefoglaló panelen, a tartalom és funkcióját ismerteti, és azokat a feladatokat hajthat végre ezen a panelen.

Az eszköz összefoglaló panel az alábbi információkat jeleníti meg:

![Eszköz irányítópult](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Kezelés

A StorSimple eszköz paneljén láthatja a StorSimple eszköz kezelésére szolgáló beállítások. A parancsok látja a panelt és bal oldalán látható. Ezek a beállítások segítségével adja hozzá a megosztások vagy kötetek, vagy frissíteni, vagy feladatátvételt a virtuális tömbjét.

Az essentials területen rögzíti a fontos tulajdonságok, mint például az állapot, modell, szoftverének verziójával, valamint egy hivatkozást a **webes felhasználói felületén** a tömb. Ha a belső hálózaton, közvetlenül indítja el a [helyi webes felhasználói felület](storsimple-ova-web-ui-admin.md) felügyeletét a virtuális tömbjét.

![Eszköz alapjai](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>A StorSimple eszköz összefoglaló

* A **riasztások** csempe pillanatképet az összes aktív riasztás biztosít a virtuális tömb, a riasztás súlyosságát szerint csoportosítva. Kattintson a csempére kattintva nyissa meg a **riasztások** panel megnyitásához, és kattintson a riasztás, így azokat kapcsolatos további részletek megtekintéséhez egyéni riasztást javasolt műveletek. A riasztás is törölheti, ha a probléma megoldódott.

* A **kapacitás** csempe megjeleníti az elsődleges tárolási kiépített és a fennmaradó tartozik ugyanahhoz a virtuális eszköz viszonyítva a teljes tárterület érhető el. **Kiépített** hivatkozik, amely készített, és használatra, tárolókapacitást **fennmaradó** hivatkozik, amely az eszköz közötti létesíthetők kapacitása. A **fennmaradó rétegzett** kapacitása a rendelkezésre álló kapacitásból, amelyek többek között a felhő, létesíthetők közben a **fennmaradó helyi** a fennmaradó virtuális a tömb csatlakoztatott lemezek kapacitás.

* Az a **használati** diagram, megtekintheti a virtuális tömb keresztül használt elsődleges tárolására, valamint az elmúlt 7 napban, az alapértelmezett időszak használt felhőbeli tárhelyhez. Használja a **szerkesztése** lehetőséget a jobb felső sarkában található a diagram válassza egy másik időskálára.

* A **megosztások** vagy **kötetek** csempe megosztások vagy állapot szerint csoportosítva az eszköz a kötetek száma összegzését tartalmazza. Kattintson a csempére kattintva nyissa meg a **megosztások** vagy **kötetek** listára panel, majd egy egyéni megosztás vagy a kötetet a tulajdonságainak megtekintéséhez vagy módosításához. További információkért lásd: hogyan [megosztások kezelése](storsimple-virtual-array-manage-shares.md) vagy [kötetek kezelése](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>Következő lépések
Az alábbiak végrehajtásának módját ismerheti meg:
- [A StorSimple virtuális tömb-megosztások kezelése](storsimple-virtual-array-manage-shares.md)
    
- [A StorSimple virtuális tömb kötetek kezelése](storsimple-virtual-array-manage-volumes.md)

