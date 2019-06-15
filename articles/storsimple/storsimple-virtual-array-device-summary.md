---
title: A StorSimple Virtual Array eszköz összefoglalás panel |} A Microsoft Docs
description: Az összefoglalás panel a StorSimple-Eszközkezelő és ismerteti a StorSimple Virtual Array állapotának figyelésére használható.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61408506"
---
# <a name="use-the-device-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>A StorSimple-Eszközkezelő az összefoglalás panel használata a StorSimple Virtual Array csatlakozik

## <a name="overview"></a>Áttekintés

Az StorSimple-Eszközkezelő panel, amely a megadott StorSimple-Eszközkezelő, regisztrálva van a StorSimple Virtual Array összefoglaló áttekintést nyújt a kiemelés eszközt egy rendszergazda figyelmet igénylő problémák. Ebben az oktatóanyagban az összefoglalás panel vezet be, ismerteti a tartalom és a függvény és azokat a feladatokat hajthat végre ezen a panelen.

Az összefoglalás panel az alábbi információkat jeleníti meg:

![Az eszköz irányítópultja](./media/storsimple-virtual-array-device-summary/device-blade.png)



## <a name="management"></a>Kezelés

A StorSimple eszköz paneljén kezelése a StorSimple-eszköz beállításainak megjelenítéséhez. A parancsok és a bal oldalon a panel tetején megjelenik. Ezek a beállítások segítségével adja hozzá a megosztások vagy kötetek, vagy frissítse vagy átadja a feladatokat a virtuális tömb.

Az essentials területen rögzíti, például az állapot- és modell, szoftver verziója, valamint mutató hivatkozás fontos tulajdonságok némelyike a **webes felhasználói felület** a tömb. Ha egy belső hálózaton, közvetlenül elindíthatja a [helyi webes felületén](storsimple-ova-web-ui-admin.md) felügyelheti a virtuális tömb.

![Eszköz alapjai](./media/storsimple-virtual-array-device-summary/device-essentials.png)

## <a name="storsimple-device-summary"></a>A StorSimple eszköz összegzésének

* A **riasztások** csempe egy pillanatkép összes aktív riasztás biztosít a virtuális tömb, a riasztás súlyossága szerint csoportosítva. Kattintson a csempére kattintva nyissa meg a **riasztások** panelen, majd kattintson a további információhoz a riasztásokhoz, így minden egyes riasztás javasolt műveleteket. A riasztás is törölheti, ha a probléma megoldódott.

* A **kapacitás** csempe megjeleníti az elsődleges tárolási ugyanahhoz a rendelkezésre álló tárhelyet képest a virtuális eszköz üzembe helyezett és a fennmaradó is. **Üzembe helyezett** hivatkozik, amely készített, és használatra, lefoglalt tárhely **fennmaradó** hivatkozik a fennmaradó kapacitás, amelyek kioszthatóak az eszközön. A **fennmaradó rétegzett** kapacitás a rendelkezésre álló kapacitást, amelyek kioszthatóak többek között a felhőben, amíg a **fennmaradó helyi** a virtuális tömb csatlakoztatott lemezek fennmaradó kapacitás.

* Az a **használati** diagramra, megtekintheti az elsődleges tárolási, a virtuális tömb futásideje, valamint a felhőalapú tárolás az elmúlt 7 napban, az alapértelmezett időszakban felhasznált. Használja a **szerkesztése** lehetőséget a jobb felső sarokban, a diagram válassza egy másik időpontot méretezési.

* A **megosztások** vagy **kötetek** csempe összefoglalja a megosztások vagy kötetek az eszközön állapot szerint csoportosítva. A csempére kattintva nyissa meg a **megosztások** vagy **kötetek** panel listában, és kattintson egy egyéni megosztás vagy kötet lehet megtekinteni és módosítani a tulajdonságait. További információkért lásd: hogyan [megosztások kezelése](storsimple-virtual-array-manage-shares.md) vagy [kötetek kezelése](storsimple-virtual-array-manage-volumes.md).

## <a name="next-steps"></a>További lépések
Az alábbiak végrehajtásának módját ismerheti meg:
- [A StorSimple Virtual Array-megosztások kezelése](storsimple-virtual-array-manage-shares.md)
    
- [A StorSimple Virtual Array a kötetek kezelése](storsimple-virtual-array-manage-volumes.md)

