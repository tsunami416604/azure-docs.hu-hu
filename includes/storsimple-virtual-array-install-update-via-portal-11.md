---
title: fájl belefoglalása
description: fájl belefoglalása
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 07/18/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: 65d5a88f7b5d059deb633f062639e455c64ef2f7
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67179643"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Frissítések telepítése az Azure Portalon keresztül

1. Nyissa meg a StorSimple-eszközkezelőt, és válassza az **Eszközök** elemet. A szolgáltatáshoz csatlakozó eszközök listájából válassza ki a frissíteni kívánt eszközt, majd kattintson rá.

2. A **Beállítások**területen kattintson az **eszközök frissítései**elemre.  

3. Megjelenik a szoftverfrissítések elérhetőségét jelző üzenet. A frissítések kereséséhez a **Vizsgálat** gombot is használhatja. Jegyezze fel a futtatott szoftver verzióját. 

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    A rendszer értesítést küld, amikor a vizsgálat elindul, és sikeresen befejeződik.
 
4. Ha elkészült a frissítések vizsgálata, kattintson a **Frissítések letöltése** gombra. Az **új frissítések**területen tekintse át a kibocsátási megjegyzéseket. Azt is vegye figyelembe, hogy a frissítések letöltése után meg kell erősítenie a telepítést. Kattintson az **OK** gombra.

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    A feltöltés indításáról és sikeres befejezéséről értesítést kap.

5. Az **eszköz frissítései**területen kattintson a **telepítés**elemre.

     ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. Az **új frissítések**területen a rendszer figyelmezteti, hogy a frissítés megszakadt. Mivel a virtuális tömb egy egyetlen csomóponttal rendelkező eszköz, ezért az eszköz a frissítést követően újraindul. Ez megszakítja a folyamatban lévő I/O-műveleteket. A frissítések telepítéséhez kattintson az **OK** gombra.

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    A telepítési feladat indításáról értesítést kap.

7.  Miután a telepítési feladatot sikeresen befejeződik, kattintson a **feladatok megtekintése** hivatkozásra. Ez a művelet a **frissítések telepítése** panelre lép. A feladatról részletes információt itt talál. 

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Ha az 1. verziójú szoftverfrissítési (10.0.10296.0) verziót futtató virtuális tömböt futtat, akkor a 1,1-es frissítés már fut, és készen áll. A hátralévő lépéseket kihagyhatja. 

    Ha a 0,6-es (10.0.10293.0) szoftververzió-verziót futtató virtuális tömbtel indult, a rendszer frissíti a 1,0-es frissítését. Megjelenik egy másik üzenet, amely jelzi, hogy a frissítések elérhetők. Ismételje meg a 4-7-es lépést a 1,1-es frissítés telepítéséhez.

    

