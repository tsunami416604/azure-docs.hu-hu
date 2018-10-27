---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 350dbc286c060ad10c21ebe1e8715b910871f837
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166038"
---
<!--author=alkohli last changed: 11/02/17 -->

#### <a name="to-install-updates-via-the-azure-portal"></a>Frissítések telepítése az Azure Portalon keresztül

1. Nyissa meg a StorSimple-eszközkezelőt, és válassza az **Eszközök** elemet. A szolgáltatáshoz csatlakozó eszközök listájából válassza ki a frissíteni kívánt eszközt, majd kattintson rá.

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. A **Beállítások** panelen kattintson az **Eszközfrissítések** elemre.

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Megjelenik a szoftverfrissítések elérhetőségét jelző üzenet. A frissítések kereséséhez a **Vizsgálat** gombot is használhatja. Jegyezze fel a szoftververzió futtatja. 

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    Ha a vizsgálat indításáról és sikeres befejezéséről értesítést kap.

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. Ha elkészült a frissítések vizsgálata, kattintson a **Frissítések letöltése** gombra.

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. Az a **új frissítések** panelen tekintse át a kibocsátási megjegyzéseket. Azt is vegye figyelembe, hogy a frissítések letöltése után meg kell erősítenie a telepítést. Kattintson az **OK** gombra.

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. A feltöltés indításáról és sikeres befejezéséről értesítést kap.

     ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. Az **Eszközfrissítések** panelen kattintson a **Telepítés** elemre.

     ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. Az **Új frissítések** panelen a rendszer figyelmezteti, hogy a frissítési folyamat megszakítja az eszköz egyéb folyamatait. Mivel a virtuális tömb egy egyetlen csomóponttal rendelkező eszköz, ezért az eszköz a frissítést követően újraindul. Ez megszakítja a folyamatban lévő I/O-műveleteket. A frissítések telepítéséhez kattintson az **OK** gombra.

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. A telepítési feladat indításáról értesítést kap.

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  A telepítési feladat sikeres befejezése után a telepítés eredményeinek megtekintéséhez kattintson a **Feladat megtekintése** hivatkozásra az **Eszközfrissítések** panelen. 

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Ez a művelet eltarthat, hogy a **frissítések telepítése** panelen. A feladatról részletes információt itt talál.

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Szoftver verziója (10.0.10293.0) 0.6-os frissítés fut a virtuális tömb lépések, ha most futtatja az 1. frissítés, és hajthatja végre. Folytassa a hátralévő lépéseket. Ha a virtuális tömb a 0.6-os (10.0.10293.0) frissítés előtti szoftvert verziót futtató, most frissülnek a 0.6-os frissítés. Jelzi, hogy-e elérhető frissítések egy másik üzenet jelenik meg. Ismételje meg a 4 – 8 1. frissítés telepítéséhez.

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

