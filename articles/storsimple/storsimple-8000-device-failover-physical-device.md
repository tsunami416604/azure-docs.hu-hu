---
title: StorSimple feladatátvétel, a StorSimple 8000 series fizikai eszköz vészhelyreállítás |} Microsoft Docs
description: 'Útmutató: a StorSimple 8000 series fizikai eszköz egy másik fizikai eszközre feladatátvételt.'
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
ms.date: 05/03/2017
ms.author: alkohli
ms.openlocfilehash: f3ac9545a341fc24ca12c9f2547805d6956cd98a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874863"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Feladatok átadása a StorSimple 8000 series fizikai eszköz

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag a StorSimple 8000 series fizikai eszközöket, hogy egy másik fizikai StorSimple-eszköz feladatátvételt, ha egy olyan vészhelyzet esetén szükséges lépéseket ismerteti. StorSimple eszköz feladatátvételi szolgáltatását használja egy másik fizikai eszközre át adatokat a forrás fizikai eszközt az adatközpontban. Ez az oktatóanyag az útmutató a StorSimple 8000 Series érvényes 3 és újabb verzióit szoftververziók frissítést futtató fizikai eszközöket.

Eszköz feladatátvételi, és hogyan használja a katasztrófa utáni helyreállítás kapcsolatos további tudnivalókért keresse fel [feladatátvétel és a katasztrófa utáni helyreállítás a StorSimple 8000 sorozat eszközeire](storsimple-8000-device-failover-disaster-recovery.md).

Feladatok átadása a StorSimple felhő készülék StorSimple fizikai eszköz, keresse fel [feladatok átadása a StorSimple felhő készülék](storsimple-8000-device-failover-cloud-appliance.md). Maga az fizikai eszköz feladatátvételt, keresse fel [átveheti az ugyanazon fizikai StorSimple-eszköz](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy átolvasta eszköz feladatátvételi szempontokat. További információkért látogasson el [eszköz feladatátvételi a gyakori szempontok](storsimple-8000-device-failover-disaster-recovery.md).

- A StorSimple 8000 series fizikai eszközt az adatközpontban telepített kell rendelkeznie. Az eszköz Update 3-as vagy újabb szoftvert kell futtatnia. További információkért látogasson el [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Feladatok átadása a fizikai eszköz lépései

A következő lépésekkel állítsa vissza az eszköz a fizikai céleszközön.

1. Győződjön meg arról, hogy a feladatátvétel kívánt kötettároló felhőalapú pillanatfelvételek vannak társítva. További információkért látogasson el [StorSimple az Eszközkezelő szolgáltatás a biztonsági mentések létrehozását](storsimple-8000-manage-backup-policies-u2.md).
2. Nyissa meg a StorSimple Eszközkezelő, és kattintson a **eszközök**. Az a **eszközök** panelen, lépjen a szolgáltatáshoz kapcsolódó eszközök listáját.
    ![Válassza ki az eszköz](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Válassza ki, és kattintson a forrás-eszközre. A forráseszköz átadni kívánt kötettárolók. Ugrás a **beállítások > Kötettárolók**.
4. Válassza ki a kötettároló szeretné, hogy áthelyezze a feladatokat egy másik eszközre. Kattintson a kötettároló található kötetek listájának megjelenítéséhez. Válassza ki a kötetet, kattintson a jobb gombbal, majd kattintson **Offline állapotba** a kötet offline állapotba. Ismételje meg ezt a folyamatot a a kötettároló összes kötetet.
5. Ismételje meg az előző lépésben szeretné, hogy áthelyezze a feladatokat egy másik eszközre kötettárolók.
6. Lépjen vissza a **eszközök** panelen. A parancssávon kattintson **feladatátvételt**.
    ![Kattintson a sikertelen keresztül](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. Az a **feladatátvételt** panelen végezze el a következő lépéseket:
   
   1. Kattintson a **forrás**. A felhőalapú pillanatfelvételek társított kötetek kötettárolók jelennek meg. Csak a megjelenített tárolók jogosultak a feladatátvételre. A kötettárolók, jelölje ki a kötettárolók szeretné, hogy áthelyezze a feladatokat. **Csak a hozzárendelt felhő pillanatképek és a kapcsolat nélküli kötetekkel kötettárolók jelennek meg.**

       ![Forrás kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Kattintson a **cél**. Az előző lépésben kiválasztott kötettárolók válasszon ki a legördülő listából válassza ki az elérhető eszközök a céleszközön. Csak azokat az eszközöket, amelyek forrás kötettárolók befogadásához elegendő kapacitással rendelkeznek a listában jelennek meg.

        ![Válassza ki a cél](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Végül tekintse át a feladatátvételi beállítások **összegzés**. Tekintse át a beállításokat, jelölje be a jelölőnégyzetet, amely azt jelzi, hogy a kijelölt kötet tárolókban lévő kötetek offline módban. Kattintson az **OK** gombra.

       ![Tekintse át a feladatátvételi beállításait](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. StorSimple létrehoz egy feladatátvételi feladatot. A feladatátvételi feladatban keresztül figyelheti a feladat értesítési kattintson a **feladatok** panelen.

    Ha az Ön meghibásodott kötettároló helyi köteteken, majd megjelenik minden helyi kötet (nem a rétegzett kötetek) a tárolóban levő egyes visszaállítási feladat. A visszaállítási feladatok befejezéséhez meglehetősen hosszabb időt vehet igénybe. Valószínű, hogy a feladatátvételi feladatban korábbi lehet végrehajtani. Ezek a kötetek lesz a helyi garanciákat, csak a visszaállítási feladatok végrehajtását követően.

    ![A figyelő feladatátvételi feladatban](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. Miután a feladatátvétel befejeződött, lépjen a **eszközök** panelen.
   
   1. Válassza ki az eszközt, hogy a céleszközt, a feladatátvételi folyamat volt megadva.

       ![Válassza ki az eszköz](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Lépjen a **Kötettárolók** panelen. Minden kötettárolók, a köteteket a régi eszközről együtt kell szerepelnie.

       ![Nézet target kötettárolók](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>Következő lépések

* Miután elvégezte a feladatátvételt, esetleg [inaktiválja vagy törölje a StorSimple eszköz](storsimple-8000-deactivate-and-delete-device.md).
* A StorSimple Device Manager szolgáltatás használatával kapcsolatos információkért látogasson el [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

