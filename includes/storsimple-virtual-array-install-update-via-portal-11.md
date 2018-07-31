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
ms.openlocfilehash: a83095b8330ccf08d777e48389c17058c6d29527
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2018
ms.locfileid: "39348469"
---
#### <a name="to-install-updates-via-the-azure-portal"></a>Frissítések telepítése az Azure Portalon keresztül

1. Nyissa meg a StorSimple-eszközkezelőt, és válassza az **Eszközök** elemet. A szolgáltatáshoz csatlakozó eszközök listájából válassza ki a frissíteni kívánt eszközt, majd kattintson rá.

2. A **beállítások**, kattintson a **Eszközfrissítések**.  

3. Megjelenik a szoftverfrissítések elérhetőségét jelző üzenet. A frissítések kereséséhez a **Vizsgálat** gombot is használhatja. Jegyezze fel a szoftververzió futtatja. 

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate3m1.png)

    Ha a vizsgálat indításáról és sikeres befejezéséről értesítést kap.
 
4. Ha elkészült a frissítések vizsgálata, kattintson a **Frissítések letöltése** gombra. A **új frissítések**, tekintse át a kibocsátási megjegyzéseket. Azt is vegye figyelembe, hogy a frissítések letöltése után meg kell erősítenie a telepítést. Kattintson az **OK** gombra.

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate6m.png)

    A feltöltés indításáról és sikeres befejezéséről értesítést kap.

5. A **Eszközfrissítések**, kattintson a **telepítése**.

     ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate11m1.png)

6. A **új frissítések**, figyelmeztetést kap, hogy azokat a káros-e a frissítésre. Mivel a virtuális tömb egy egyetlen csomóponttal rendelkező eszköz, ezért az eszköz a frissítést követően újraindul. Ez megszakítja a folyamatban lévő I/O-műveleteket. A frissítések telepítéséhez kattintson az **OK** gombra.

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate12m.png)

    A telepítési feladat indításáról értesítést kap.

7.  A telepítési feladat sikeres befejeződése után kattintson a **feladat megtekintése** hivatkozásra. Ez a művelet eltarthat, hogy a **frissítések telepítése** panelen. A feladatról részletes információt itt talál. 

    ![eszköz frissítése](../includes/media/storsimple-virtual-array-install-update-via-portal-11/azupdate16m1.png)

8. Ha egy virtuális tömböt, 1. frissítés (10.0.10296.0) szoftver verziója fut, most futtatja 1.1-es frissítés, és hajthatja végre. Folytassa a hátralévő lépéseket. 

    Ha a virtuális tömb szoftver verziója (10.0.10293.0) 0.6-os frissítés fut, most frissülnek az 1.0-ás frissítés. Jelzi, hogy-e elérhető frissítések egy másik üzenet jelenik meg. Ismételje meg a 4-7 1.1-es frissítés telepítése.

    

