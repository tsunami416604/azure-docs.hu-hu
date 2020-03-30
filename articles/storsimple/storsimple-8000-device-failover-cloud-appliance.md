---
title: Feladatátvétel és vészhelyreállítás egy StorSimple felhőalapú berendezésbe
description: Ismerje meg, hogyan kell feladatátvételt a StorSimple 8000 sorozatú fizikai eszköz egy felhőalapú berendezés.
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
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 347b899608d4322a7873b9f80f38ca1c767194d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77468745"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Feladatátvétel a StorSimple felhőalapú berendezésnek

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag ismerteti a StorSimple 8000 sorozatú fizikai eszköz storSimple felhőalapú berendezésre való feladatátvételhez szükséges lépéseket, ha katasztrófa történik. A StorSimple az eszköz feladatátvételi szolgáltatásával telepíti át az adatokat az adatközpontban lévő fizikai forráseszközről egy Azure-ban futó felhőberendezésre. Az oktatóanyagban található útmutató a StorSimple 8000 sorozatú fizikai eszközökre és a szoftververziókat futtató 3- as és újabb verziót futtató felhőberendezésekre vonatkozik.

Ha többet szeretne megtudni az eszközfeladat-átvételről és arról, hogyan használja a katasztrófa utáni helyreállítást, nyissa meg [a Feladatátvételés vészhelyreállítást a StorSimple 8000 sorozatú eszközökszámára.](storsimple-8000-device-failover-disaster-recovery.md)

Ha egy StorSimple fizikai eszközt egy másik fizikai eszközre szeretne átadni, nyissa [meg a Feladatátvétel t a StorSimple fizikai eszközre.](storsimple-8000-device-failover-physical-device.md) Ha saját magának szeretne átadni egy eszközt, nyissa [meg a Feladatátvétel lehetőséget ugyanarra a StorSimple fizikai eszközre.](storsimple-8000-device-failover-same-device.md)

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy áttekintette az eszköz feladatátvételi szempontokat. További információt az [eszközfeladat-átvétel általános szempontjai](storsimple-8000-device-failover-disaster-recovery.md)című területen talál.

- Az eljárás futtatása előtt létre kell hoznia és konfigurálnia kell egy StorSimple felhőberendezést. A 3. A 8020 modell 64 TB-os, és prémium szintű storage-ot használ. További információ: [StorSimple cloud appliance telepítése és kezelése.](storsimple-8000-cloud-appliance-u2.md)

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>A felhőalapú berendezés feladatátvétellépései

Hajtsa végre a következő lépéseket az eszköz visszaállításához a cél StorSimple Cloud Appliance.

1.  Ellenőrizze, hogy a feladatátvételre kívánt kötettároló rendelkezik-e társított felhőbeli pillanatképekkel. További információt a [StorSimple Eszközkezelő szolgáltatás használata biztonsági másolatok létrehozásához](storsimple-8000-manage-backup-policies-u2.md)című területen talál.
2. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az **Eszközök** panelen nyissa meg a szolgáltatáshoz csatlakoztatott eszközök listáját.
    ![Eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Jelölje ki és kattintson a forráseszközre. A forráseszköz rendelkezik a feladatátvételhez kívánt kötettárolókkal. Nyissa meg **a Beállítások > kötettárolók lapot.**

    ![Eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Válassza ki azt a kötettárolót, amelyet át szeretne adni egy másik eszköznek. Kattintson a kötettárolóra a tárolón belüli kötetek listájának megjelenítéséhez. Jelöljön ki egy kötetet, kattintson a jobb gombbal, és válassza a **Kapcsolat nélküli módra váltáshoz** a kötetet.

    ![Eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Ismételje meg ezt a folyamatot a kötettároló összes kötetén.

     ![Eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Ismételje meg az előző lépést az összes kötettárolóesetében, amelyet át szeretne adni egy másik eszköznek.

7. Menj vissza az **Eszközök** panelhez. A parancssávon kattintson a **Feladatátvétel gombra.**

    ![Kattintson a feladatátvételre](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. A **Feladatátvétel** panelen hajtsa végre a következő lépéseket:
   
    1. Kattintson **a Forrás gombra.** Válassza ki a feladatátvételhez a kötettárolókat. **Csak a társított felhőbeli pillanatképekkel és offline kötetekkel rendelkező kötettárolók jelennek meg.**
        ![Forrás kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Kattintson **a Cél gombra.** Válasszon ki egy célfelhő-berendezést az elérhető eszközök legördülő listájából. **Csak azok az eszközök jelennek meg a listában, amelyek elegendő kapacitással rendelkeznek a forráskötet-tárolók befogadásához.**

        ![Cél kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Tekintse át a feladatátvételi beállításokat az **Összegzés** csoportban, és jelölje be azt a jelölőnégyzetet, amely jelzi, hogy a kijelölt kötettárolókban lévő kötetek offline állapotban vannak. 

        ![Feladatátvételi beállítások áttekintése](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Létrejön egy feladatátvételi feladat. A feladatátvételi feladat figyeléséhez kattintson a feladat értesítésére.

    ![Feladatátvételi feladat figyelése](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. A feladatátvétel befejezése után lépjen vissza az **Eszközök** panelre.

    1. Válassza ki a feladatátvétel célként használt eszközt.

       ![Eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Kattintson **a Kötettárolók gombra.** Az összes kötettárolót, valamint a régi eszköz köteteit fel kell sorolni.

       Ha a feladatátvételi rendszer által megadott kötettároló helyileg rögzített köteteket tartalmaz, ezek a kötetek felzett kötetként lesznek megdöntve. A helyileg rögzített kötetek nem támogatottak a StorSimple felhőalapú berendezésen.

       ![Célkötet-tárolók megtekintése](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>További lépések

* Miután végrehajtotta a feladatátvételt, előfordulhat, hogy [inaktiválnia vagy törölnie kell a StorSimple eszközt.](storsimple-8000-deactivate-and-delete-device.md)

* A StorSimple Eszközkezelő szolgáltatás használatáról a [StorSimple Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez című oldalon](storsimple-8000-manager-service-administration.md)talál.

