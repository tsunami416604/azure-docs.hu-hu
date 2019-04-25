---
title: A StorSimple Virtual Array szolgáltatás összefoglalás panelén |} A Microsoft Docs
description: A szolgáltatás összefoglalás panelén a StorSimple-Eszközkezelő és ismerteti a StorSimple Virtual Array állapotának figyelésére használható.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60337923"
---
# <a name="use-the-service-summary-blade-for-storsimple-device-manager-connected-to-storsimple-virtual-array"></a>A StorSimple-Eszközkezelő szolgáltatás összefoglalás panelén használata a StorSimple Virtual Array csatlakozik.
## <a name="overview"></a>Áttekintés
A StorSimple Virtual Arrayt (más néven a StorSimple a helyi virtuális vagy virtuális), amely csatlakozik a szolgáltatáshoz, kiemelése, amelyek olyan rendszerre van szüksége összefoglaló nézetét jeleníti meg az a StorSimple-Eszközkezelő szolgáltatás összefoglalás panelén a rendszergazda figyelmet. Ebben az oktatóanyagban vezet be a szolgáltatás összefoglalás panelén, a tartalom- és funkciót ismerteti és azokat a feladatokat hajthat végre ezen a panelen.

![Szolgáltatási irányítópult](./media/storsimple-virtual-array-service-summary/service-blade.png)

## <a name="management-commands-and-essentials"></a>Felügyeleti parancsok és essentials
A StorSimple összefoglaló panelén kezelése a StorSimple-Eszközkezelő szolgáltatás, valamint a virtuális tömbök szolgáltatáshoz regisztrált beállításainak megjelenítéséhez. A parancsok és a bal oldalon a panel tetején megjelenik.

Ezek a beállítások használata csatlakozva különféle műveleteket végezhet, például a részvények vagy a kötet hozzáadása, vagy a figyelő a különböző feladatok, a virtuális tömbök futó.

Az essentials területen rögzíti az egyes fontos tulajdonságai, például az erőforráscsoport, helyét és előfizetés, amelyben a StorSimple-Eszközkezelő létrehozása történt.

## <a name="storsimple-device-manager-service-summary"></a>StorSimple-Eszközkezelő szolgáltatás összegzése
* A **riasztások** csempe egy pillanatkép összes aktív riasztás biztosít virtuális eszközön, riasztás súlyossága szerint csoportosítva. A csempékre kattintva a **riasztások** panel, ahol az egyes riasztást a riasztással kapcsolatos további részletek megtekintéséhez kattintson így azokat is ajánlott műveletek. A riasztás is törölheti, ha a probléma megoldódott.
* A **kapacitás** csempe megjeleníti az elsődleges tároló kiosztott és a fennmaradó összes virtuális eszközön, az összes szabad tárhely viszonyított összes virtuális eszközön jeleníti meg. **Üzembe helyezett** hivatkozik, amely készített, és használatra, lefoglalt tárhely **fennmaradó** hivatkozik a fennmaradó kapacitás, amelyek kioszthatóak az összes virtuális eszközön. A **fennmaradó rétegzett** kapacitás a rendelkezésre álló kapacitást, amelyek kioszthatóak többek között a felhőben, amíg a **fennmaradó helyi** a virtuális tömbök csatlakoztatott lemezek fennmaradó kapacitás.
* Az a **használati** diagramra, megtekintheti a releváns metrikákat a virtuális eszközök. Megtekintheti az összes virtuális eszközön használt elsődleges tároló, valamint a felhőalapú tárolás az elmúlt 7 napban, az alapértelmezett időszak virtuális eszközök által felhasznált. Használja a **szerkesztése** lehetőséget a jobb felső sarokban, a diagram válassza egy másik időpontot méretezési.
* A **eszközök** csempe összesíti a virtuális tömbök az a StorSimple-Eszközkezelő eszköz állapota szerint csoportosítva számát. Ez a csempe megnyitásához kattintson a **eszközök** panel listából, majd kattintson az egy adott eszköz az eszközre adott eszközösszegzés adatfelhasználás. Eszköz műveleteket is elvégezheti egy adott eszköz összefoglalás panelje. Az összefoglalás panel kapcsolatos további információkért látogasson el [eszköz összefoglalás panel](storsimple-virtual-array-device-summary.md).

## <a name="view-the-activity-logs"></a>A Tevékenységnaplók megtekintése
A különböző műveletek belül a StorSimple-Eszközkezelő végzett megtekintéséhez kattintson a **tevékenységeket tartalmazó naplók** a StorSimple szolgáltatás összefoglalás panelén bal oldalán található hivatkozást. Ezzel továbblép a **tevékenységeket tartalmazó naplók** panel, ahol megtekintheti a legutóbbi végrehajtott műveletek összegzése.

![Tevékenységnaplók](./media/storsimple-virtual-array-service-summary/activity-log.png)

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [felügyelete a StorSimple virtuális tömb helyi webes felületén használatával](storsimple-ova-web-ui-admin.md).

