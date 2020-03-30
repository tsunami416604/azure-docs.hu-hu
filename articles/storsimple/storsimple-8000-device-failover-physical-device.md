---
title: Feladatátvétel, vészhelyreállítás egy másik StorSimple 8000-eszközre
description: Ismerje meg, hogyan kell feladatátvételt a StorSimple 8000 sorozatú fizikai eszköz egy másik fizikai eszközre.
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
ms.openlocfilehash: 9e4e890ab5491e46ffe5ea0e1c168d168f9cc729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77468609"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>Feladatátvétel egy StorSimple 8000 sorozatú fizikai eszköznek

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag ismerteti a StorSimple 8000 sorozatú fizikai eszköz egy másik StorSimple fizikai eszközre való átlépésekhez szükséges lépéseket, ha katasztrófa történik. A StorSimple az eszköz feladatátvételi szolgáltatásával telepíti át az adatokat az adatközpontban lévő fizikai forráseszközről egy másik fizikai eszközre. Az oktatóanyagban található útmutatás a 3- as és újabb frissítéssel működő StorSimple 8000 sorozatú fizikai eszközökre vonatkozik.

Ha többet szeretne megtudni az eszközfeladat-átvételről és arról, hogyan használja a katasztrófa utáni helyreállítást, nyissa meg [a Feladatátvételés vészhelyreállítást a StorSimple 8000 sorozatú eszközökszámára.](storsimple-8000-device-failover-disaster-recovery.md)

Ha egy StorSimple fizikai eszköz egy StorSimple felhőalapú berendezés, megy [a feladatátvétel egy StorSimple felhőalapú berendezés.](storsimple-8000-device-failover-cloud-appliance.md) Ha saját magának szeretne átadni egy fizikai eszközt, nyissa [meg a Feladatátvétel lehetőséget ugyanarra a StorSimple fizikai eszközre.](storsimple-8000-device-failover-same-device.md)


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy áttekintette az eszköz feladatátvételi szempontokat. További információt az [eszközfeladat-átvétel általános szempontjai](storsimple-8000-device-failover-disaster-recovery.md)című területen talál.

- Rendelkeznie kell egy StorSimple 8000-es sorozatú fizikai eszköz telepítve az adatközpontban. Az eszköznek 3. További információért látogasson el a helyszíni [StorSimple-eszköz telepítése című webhelyre.](storsimple-8000-deployment-walkthrough-u2.md)


## <a name="steps-to-fail-over-to-a-physical-device"></a>Fizikai eszköz feladatátvételi lépései

Hajtsa végre az alábbi lépéseket az eszköz célfizikai eszközre való visszaállításához.

1. Ellenőrizze, hogy a feladatátvételre kívánt kötettároló rendelkezik-e társított felhőbeli pillanatképekkel. További információt a [StorSimple Eszközkezelő szolgáltatás használata biztonsági másolatok létrehozásához](storsimple-8000-manage-backup-policies-u2.md)című területen talál.
2. Nyissa meg a StorSimple Eszközkezelőt, és kattintson **az Eszközök gombra.** Az **Eszközök** panelen nyissa meg a szolgáltatáshoz csatlakoztatott eszközök listáját.
    ![Eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Jelölje ki és kattintson a forráseszközre. A forráseszköz rendelkezik a feladatátvételhez kívánt kötettárolókkal. Nyissa meg **a Beállítások > kötettárolók lapot.**
4. Válassza ki azt a kötettárolót, amelyet át szeretne adni egy másik eszköznek. Kattintson a kötettárolóra a tárolón belüli kötetek listájának megjelenítéséhez. Jelöljön ki egy kötetet, kattintson a jobb gombbal, és válassza a **Kapcsolat nélküli módra váltáshoz** a kötetet. Ismételje meg ezt a folyamatot a kötettároló összes kötetén.
5. Ismételje meg az előző lépést az összes kötettárolóesetében, amelyet át szeretne adni egy másik eszköznek.
6. Menj vissza az **Eszközök** panelhez. A parancssávon kattintson a **Feladatátvétel gombra.**
    ![Kattintson a feladatátvételre](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. A **Feladatátvétel** panelen hajtsa végre a következő lépéseket:
   
   1. Kattintson **a Forrás gombra.** A kötettárolók a felhőbeli pillanatképekhez társított kötetek jelennek meg. Csak a megjelenített tárolók jogosultak feladatátvételre. A kötettárolók listájában válassza ki azokat a kötettárolókat, amelyeket át szeretne adni. **Csak a társított felhőbeli pillanatképekkel és offline kötetekkel rendelkező kötettárolók jelennek meg.**

       ![Forrás kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Kattintson **a Cél gombra.** Az előző lépésben kiválasztott kötettárolókhoz válasszon ki egy céleszközt az elérhető eszközök legördülő listájából. Csak azok az eszközök jelennek meg a listában, amelyek elegendő kapacitással rendelkeznek a forráskötet-tárolók befogadásához.

        ![Cél kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Végül tekintse át az összes feladatátvételi beállítást az **Összegzés csoportban.** A beállítások áttekintése után jelölje be a jelölőnégyzetet, amely jelzi, hogy a kijelölt kötettárolókban lévő kötetek offline állapotban vannak. Kattintson az **OK** gombra.

       ![Feladatátvételi beállítások áttekintése](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. A StorSimple feladatátvételi feladatot hoz létre. Kattintson a feladat értesítésfigyelése a feladatátvételi feladat a **Feladatok** panelen keresztül.

    Ha a feladatátvételi rendszer által felügyelt kötettároló helyi kötetekkel rendelkezik, akkor a tárolóban minden helyi kötet (nem rétegzett kötetek) egyes visszaállítási feladatok jelennek meg. Ezek a visszaállítási feladatok eltarthat egy ideig, hogy teljes. Valószínű, hogy a feladatátvételi feladat korábban befejeződhet. Ezek a kötetek csak a visszaállítási feladatok befejezése után lesznek helyi garanciák.

    ![Feladatátvételi feladat figyelése](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. A feladatátvétel befejezése után nyissa meg az **Eszközök** panelt.
   
   1. Válassza ki azt az eszközt, amelyet a feladatátvételi folyamat céleszközeként használt.

       ![Eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Nyissa meg a **Kötettárolók** panelt. Az összes kötettárolót, valamint a régi eszköz köteteit fel kell sorolni.

       ![Célkötet-tárolók megtekintése](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>További lépések

* Miután végrehajtotta a feladatátvételt, előfordulhat, hogy [inaktiválnia vagy törölnie kell a StorSimple eszközt.](storsimple-8000-deactivate-and-delete-device.md)
* A StorSimple Eszközkezelő szolgáltatás használatáról a [StorSimple Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez című oldalon](storsimple-8000-manager-service-administration.md)talál.

