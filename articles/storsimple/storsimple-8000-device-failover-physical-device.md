---
title: A StorSimple feladatátvétel, a StorSimple 8000 sorozatú fizikai eszköz a vész-helyreállítási |} A Microsoft Docs
description: Ismerje meg, hogyan végezhet feladatátvételt a StorSimple 8000 sorozatú fizikai eszköz egy másik fizikai eszközre.
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
ms.openlocfilehash: 5fcf95a1a3033a5150945dbd841f12d50ebb023b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60577249"
---
# <a name="fail-over-to-a-storsimple-8000-series-physical-device"></a>A StorSimple 8000 sorozatú fizikai eszköz átadása

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban a vészhelyzet esetén a StorSimple 8000 sorozatú fizikai eszköz egy másik fizikai StorSimple-eszközre való átadásához szükséges lépéseket ismerteti. A StorSimple az eszköz feladatátvételi funkciót használja, egy másik fizikai eszközre telepítheti át adatait a forrás fizikai eszköz az adatközpontban. Ebben az oktatóanyagban az útmutató vonatkozik a StorSimple 8000 sorozat szoftververziók Update 3 és újabb verzióit futtató fizikai eszközöket.

Eszköz-feladatátvétel és a egy katasztrófa utáni helyreállítás felhasználási módjáról további információkért lépjen [feladatátvétel és vészhelyreállítás a StorSimple 8000 sorozatú eszközök esetében a helyreállítási](storsimple-8000-device-failover-disaster-recovery.md).

A StorSimple fizikai eszköz a StorSimple Cloud Appliance feladatátvételt, lépjen a [átadja a feladatokat egy StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md). Saját maga egy fizikai eszköz feladatainak átadása, lépjen a [átadja a feladatokat a ugyanabban StorSimple fizikai eszköz](storsimple-8000-device-failover-same-device.md).


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy áttekintette az eszköz feladatátvételi szempontok. További információért ugorjon [általános szempontok az eszköz feladatátvételéhez](storsimple-8000-device-failover-disaster-recovery.md).

- A StorSimple 8000 sorozatú fizikai eszköz az adatközpontban telepített kell rendelkeznie. Az eszköz Update 3 vagy újabb szoftvert verzióját kell futtatnia. További információért ugorjon [a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-u2.md).


## <a name="steps-to-fail-over-to-a-physical-device"></a>Feladatátvétel fizikai eszközre lépései

A következő lépésekkel állítsa vissza az eszköz a fizikai céleszközt.

1. Győződjön meg arról, hogy a kötettároló feladatátvételét szeretné a felhőbeli pillanatképek vannak társítva. További információért ugorjon [használata a StorSimple-Eszközkezelő szolgáltatás a biztonsági mentések létrehozását](storsimple-8000-manage-backup-policies-u2.md).
2. Nyissa meg a StorSimple Device Managert, és kattintson a **eszközök**. Az a **eszközök** panelen nyissa meg a szolgáltatáshoz csatlakoztatott eszközök listájához.
    ![Eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev1.png)
3. Válassza ki, majd kattintson a forráseszközt. A forráseszköz kötettárolók, amelyeket szeretne a feladatátvételt. Lépjen a **beállítások > Kötettárolók**.
4. Válassza ki a kötettárolót, amely átadja a feladatokat egy másik eszközön szeretné. Kattintson a kötettároló ebben a tárolóban lévő kötetek listájának megjelenítéséhez. Válassza ki a kötetet, kattintson a jobb gombbal, majd kattintson **Offline állapotba** a kötet offline állapotba. A kötettároló a minden kötet esetében ismételje meg a folyamatot.
5. Ismételje meg az előző lépésben, átadja a feladatokat egy másik eszközön szeretné kötettárolók.
6. Lépjen vissza a **eszközök** panelen. A parancssávon kattintson **átadja a feladatokat**.
    ![Kattintson a sikertelen keresztül](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev2.png)
    
7. Az a **átadja a feladatokat** panelen hajtsa végre az alábbi lépéseket:
   
   1. Kattintson a **forrás**. A felhőbeli pillanatképekkel társított kötetek kötettárolók jelennek meg. Csak a megjelenített tárolók jogosultak a feladatátvételhez. Kötettárolóinak listájában válassza ki a kötettárolókat, amelyet szeretne átadja a feladatokat. **Csak a felhőbeli pillanatképek és offline kötetek kötettárolók jelennek meg.**

       ![Forrás kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev5.png)
   2. Kattintson a **cél**. Az előző lépésben kiválasztott kötettárolók, a rendelkezésre álló eszközök a legördülő listából válassza ki a céleszközt. A listában csak a forrás-kötettároló befogadásához elegendő kapacitással rendelkező eszközök jelennek meg.

        ![Cél kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev6.png)

   3. Végül tekintse át a alatt feladatátvételi beállításokat **összefoglalás**. Tekintse át a beállításokat, jelölje be a jelölőnégyzetet, amely azt jelzi, hogy a kiválasztott kötettárolókban kötetek offline állapotban. Kattintson az **OK** gombra.

       ![Tekintse át a feladatátvételi beállításokat](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev8.png)
  
8. A StorSimple létrehoz egy feladatátvételi feladatot. Az értesítés-n keresztül a feladatátvételi feladat figyeléséhez kattintson a **feladatok** panelen.

    Ha a meghibásodott adatbázison a kötettároló helyi köteteken, majd megjelenik minden helyi kötet (nem a rétegzett kötetek) a tárolóban lévő egyes visszaállítási feladat. Ezek visszaállítási feladat hosszabb időt vehet igénybe végrehajtásához. Valószínű, hogy a feladatátvételi feladat korábban esetleg befejezéséhez. Ezek a kötetek lesz a helyi garanciákat, csak a visszaállítási feladat befejezése után.

    ![A figyelő feladatátvételi feladat](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

9. A feladatátvétel befejezése után nyissa meg a **eszközök** panelen.
   
   1. Válassza ki az eszközt, hogy az eszköznek a feladatátvételi folyamat használták.

       ![Eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

   2. Nyissa meg a **Kötettárolók** panelen. Minden kötettárolók, a köteteket a régi eszközről, együtt kell szerepelnie.

       ![Nézet target kötettárolók](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev16.png)


## <a name="next-steps"></a>További lépések

* Miután elvégezte a feladatátvételt, szükség lehet [inaktiválja vagy törölje a StorSimple-eszköz](storsimple-8000-deactivate-and-delete-device.md).
* A StorSimple-Eszközkezelő szolgáltatás használatával kapcsolatos információkért tekintse meg a [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).

