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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179643"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Frissítések telepítése az Azure Portalon keresztül

1. Nyissa meg a StorSimple-eszközkezelőt, és válassza az **Eszközök** elemet. A szolgáltatáshoz csatlakozó eszközök listájából válassza ki a frissíteni kívánt eszközt, majd kattintson rá.

2. A **Beállítások csoportban**kattintson **az Eszközfrissítések**elemre.  

3. Megjelenik a szoftverfrissítések elérhetőségét jelző üzenet. A frissítések kereséséhez a **Vizsgálat** gombot is használhatja. Jegyezze fel a futtatott szoftververziót. 

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    A vizsgálat sikeres indításakor és befejezéséről értesítést kap.
 
4. Ha elkészült a frissítések vizsgálata, kattintson a **Frissítések letöltése** gombra. Az **Új frissítések csoportban**tekintse át a kibocsátási megjegyzéseket. Azt is vegye figyelembe, hogy a frissítések letöltése után meg kell erősítenie a telepítést. Kattintson az **OK** gombra.

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    A feltöltés indításáról és sikeres befejezéséről értesítést kap.

5. Az **Eszközfrissítések**csoportban kattintson a **Telepítés gombra.**

     ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. Az **Új frissítések területen**a rendszer figyelmezteti, hogy a frissítés zavaró. Mivel a virtuális tömb egy egyetlen csomóponttal rendelkező eszköz, ezért az eszköz a frissítést követően újraindul. Ez megszakítja a folyamatban lévő I/O-műveleteket. A frissítések telepítéséhez kattintson az **OK** gombra.

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    A telepítési feladat indításáról értesítést kap.

7.  A telepítési feladat sikeres befejezése után kattintson a **Feladat megtekintése** hivatkozás parancsra. Ezzel a művelettel a **Frissítések telepítése** panelen lép fel. A feladatról részletes információt itt talál. 

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Ha az 1.0.10296.0-s verziójú virtuális tömböt futtató virtuális tömböt indította, akkor most az 1.1-es frissítést futtatja, és kész. Kihagyhatja a fennmaradó lépéseket. 

    Ha a 0.6-os (10.0.10293.0) verziót futtató virtuális tömböt futtató virtuális tömböt futtató virtuális tömböt az 1.0 frissítésre frissít. Megjelenik egy másik üzenet, amely jelzi, hogy a frissítések elérhetők. Ismételje meg a 4-7.

    

