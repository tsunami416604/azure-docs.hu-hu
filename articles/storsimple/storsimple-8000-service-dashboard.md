---
title: Használja a StorSimple 8000 sorozatú eszköz összegzésének |} A Microsoft Docs
description: Ismerteti a StorSimple szolgáltatás összefoglalás panelén, és ismerteti a StorSimple-megoldásokra állapotának figyelése céljából.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60633137"
---
# <a name="use-the-service-summary-blade-for-storsimple-8000-series-device"></a>A szolgáltatásösszegző panel használata a StorSimple 8000 sorozatú eszköz

## <a name="overview"></a>Áttekintés

A StorSimple-Eszközkezelő szolgáltatás összefoglalás panelén a StorSimple-Eszközkezelő szolgáltatásban, egy rendszergazda beavatkozást igénylő eszközök kiemelése csatlakoztatott eszközök összefoglaló nézetét jeleníti meg. Ebben az oktatóanyagban vezet be a szolgáltatás összefoglalás panelén, az irányítópult tartalmát és funkciót ismerteti és azokat a feladatokat hajthat végre ezen az oldalon.

![Szolgáltatás összegzése](./media/storsimple-8000-service-dashboard/service-summary1.png)


## <a name="management-commands"></a>Felügyeleti parancsok

A StorSimple szolgáltatás összefoglalás panelén láthatja a beállításokat a StorSimple-Eszközkezelő szolgáltatás és a StorSimple 8000 sorozatú eszközöket regisztrálni ezt a szolgáltatást. A parancsok és a bal oldalon a panel tetején megjelenik.

![Parancssáv](./media/storsimple-8000-service-dashboard/service-summary2.png)

Használja ezeket a beállításokat csatlakozva különféle műveleteket végezhet, például a részvények vagy a kötet hozzáadása, vagy a figyelő a különböző feladatok futtatása a StorSimple eszközön.


## <a name="essentials"></a>Alapvető erőforrások

Az essentials területen rögzíti az egyes fontos tulajdonságai, például az erőforráscsoport, helyét és előfizetés, amelyben a StorSimple-Eszközkezelő létrehozása történt.

![Alapvető erőforrások](./media/storsimple-8000-service-dashboard/service-summary3.png)

## <a name="storsimple-device-manager-service-summary"></a>StorSimple-Eszközkezelő szolgáltatás összegzése

* A **riasztások** csempe egy pillanatkép összes aktív riasztás biztosít minden eszközön, a riasztás súlyossága szerint csoportosítva.

    ![Riasztások csempe](./media/storsimple-8000-service-dashboard/service-summary4.png)

    A csempékre kattintva a **riasztások** panel, ahol az egyes riasztást a riasztással kapcsolatos további részletek megtekintéséhez kattintson így azokat is ajánlott műveletek. A riasztás is törölheti, ha a probléma megoldódott.

    ![Kattintson a riasztások csempe](./media/storsimple-8000-service-dashboard/service-summary8.png)

* A **kapacitás** csempe megjeleníti az elsődleges tároló kiosztott és a fennmaradó képest az összes szabad tárhely összes eszközön továbbított összes eszközön jeleníti meg. **Üzembe helyezett** hivatkozik, amely készített, és használatra, lefoglalt tárhely **fennmaradó** hivatkozik a fennmaradó kapacitás, amelyek kioszthatóak az összes eszközön.

    ![A kapacitás csempe](./media/storsimple-8000-service-dashboard/service-summary6.png)

    A **fennmaradó rétegzett** kapacitás a rendelkezésre álló kapacitást, amelyek kioszthatóak többek között a felhőben, amíg a **fennmaradó helyi** a StorSimple 8000-es csatlakoztatott lemezek fennmaradó kapacitás sorozatú eszközöket.


* Az a **használati** diagramra, megtekintheti a releváns metrikákat az eszközökön. Megtekintheti az összes eszközön használt elsődleges tároló és a felhőalapú tárolás az elmúlt 7 napban, az alapértelmezett időtartam-eszközök által felhasznált. 

    ![Használat csempe](./media/storsimple-8000-service-dashboard/service-summary7.png) 

    Válasszon egy másik időpontot méretezési, használja a **szerkesztése** a diagram jobb felső sarokban lévő beállítást.

     ![Kattintson a használat csempe](./media/storsimple-8000-service-dashboard/service-summary10.png)

     ![Exportálási diagram adatai](./media/storsimple-8000-service-dashboard/service-summary11.png)

* A **eszközök** csempe összesíti a StorSimple 8000 sorozatú eszközök az a StorSimple-Eszközkezelő eszköz állapota szerint csoportosítva számát. 

    ![Eszközök csempe](./media/storsimple-8000-service-dashboard/service-summary5.png)

    Ez a csempe megnyitásához kattintson a **eszközök** panel listából, majd kattintson az egy adott eszköz az eszközre adott eszközösszegzés adatfelhasználás. Eszközspecifikus műveleteket is elvégezheti egy adott eszköz összefoglalás panelje. Az összefoglalás panel kapcsolatos további információkért látogasson el [eszköz összefoglalás panel](storsimple-8000-device-dashboard.md).

    ![Kattintson az eszközök csempe](./media/storsimple-8000-service-dashboard/service-summary9.png)

## <a name="view-the-activity-logs"></a>A Tevékenységnaplók megtekintése

A különböző műveletek belül a StorSimple-Eszközkezelő végzett megtekintéséhez kattintson a **tevékenységeket tartalmazó naplók** a StorSimple szolgáltatás összefoglalás panelén bal oldalán található hivatkozást. Ezzel továbblép a **tevékenységeket tartalmazó naplók** panel, ahol megtekintheti a legutóbbi végrehajtott műveletek összegzése.

![Tevékenységnaplók](./media/storsimple-8000-service-dashboard/activity-logs1.png)
## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).

