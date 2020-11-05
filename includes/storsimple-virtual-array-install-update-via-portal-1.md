---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: e64db93347794ccfc85252205c6d92a68e904635
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376215"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Frissítések telepítése az Azure Portalon keresztül

1. Nyissa meg a StorSimple-eszközkezelőt, és válassza az **Eszközök** elemet. A szolgáltatáshoz csatlakozó eszközök listájából válassza ki a frissíteni kívánt eszközt, majd kattintson rá.

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate1m.png) 

2. A **Beállítások** panelen kattintson az **Eszközfrissítések** elemre.

    ![2. eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-04/azupdate2m.png)  

3. Megjelenik a szoftverfrissítések elérhetőségét jelző üzenet. A frissítések kereséséhez a **Vizsgálat** gombot is használhatja. Jegyezze fel a futtatott szoftver verzióját. 

    ![3. eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate3m1.png)

    A rendszer értesítést küld, amikor a vizsgálat elindul, és sikeresen befejeződik.

    ![4. eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate5m.png)

4. Ha elkészült a frissítések vizsgálata, kattintson a **Frissítések letöltése** gombra.

    ![5. eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate6m.png)

5. Az **új frissítések** panelen tekintse át a kibocsátási megjegyzéseket. Azt is vegye figyelembe, hogy a frissítések letöltése után meg kell erősítenie a telepítést. Kattintson az **OK** gombra.

    ![6. eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate7m.png)

6. A feltöltés indításáról és sikeres befejezéséről értesítést kap.

     ![7. eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate8m.png)

5. Az **Eszközfrissítések** panelen kattintson a **Telepítés** elemre.

     ![8. eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate11m1.png)

6. Az **Új frissítések** panelen a rendszer figyelmezteti, hogy a frissítési folyamat megszakítja az eszköz egyéb folyamatait. Mivel a virtuális tömb egy egyetlen csomóponttal rendelkező eszköz, ezért az eszköz a frissítést követően újraindul. Ez megszakítja a folyamatban lévő I/O-műveleteket. A frissítések telepítéséhez kattintson az **OK** gombra.

    ![9. eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate12m.png)

7. A telepítési feladat indításáról értesítést kap.

    ![10. eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate13m.png)

8.  A telepítési feladat sikeres befejezése után a telepítés eredményeinek megtekintéséhez kattintson a **Feladat megtekintése** hivatkozásra az **Eszközfrissítések** panelen. 

    ![11. eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate15m1.png)

    Ez a művelet a **frissítések telepítése** panelre lép. A feladatról részletes információt itt talál.

    ![12. eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate16m1.png)

9. Ha a 0,6-es (10.0.10293.0) szoftverfrissítés-verziót futtató virtuális tömböt futtat, akkor most már az 1. frissítést futtatja, és készen áll. A hátralévő lépéseket kihagyhatja. Ha a 0,6-es (10.0.10293.0) frissítés előtti verziót futtató virtuális tömbtel indult, a rendszer frissíti a 0,6-es frissítését. Megjelenik egy másik üzenet, amely jelzi, hogy a frissítések elérhetők. Ismételje meg az 4-8-es lépést az 1. frissítés telepítéséhez.

    ![13. eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-1/azupdate17.png)

