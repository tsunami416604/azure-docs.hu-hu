---
title: A StorSimple feladatátvétel, a StorSimple Cloud Appliance vész-helyreállítási |} A Microsoft Docs
description: Ismerje meg, hogyan végezhet feladatátvételt a StorSimple 8000 sorozatú fizikai eszköz a felhőalapú berendezéshez.
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
ms.openlocfilehash: 45c521fd044fa258b8052a3f0de48784cf4160e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584393"
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>A StorSimple Cloud Appliance átadása

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban a feladatátvételt egy StorSimple 8000 sorozatú fizikai eszköz a StorSimple Cloud Appliance vészhelyzet esetén szükséges lépéseket ismerteti. A StorSimple az eszköz feladatátvételi funkciót használja, telepítheti át adatait a forrás fizikai eszköz az adatközpontban található Azure-ban futó a felhőalapú berendezés. Ebben az oktatóanyagban az útmutató a StorSimple 8000 sorozatú fizikai eszközök és a felhőalapú berendezések szoftververziók Update 3 és újabb verzióit futtató vonatkozik.

Eszköz-feladatátvétel és a egy katasztrófa utáni helyreállítás felhasználási módjáról további információkért lépjen [feladatátvétel és vészhelyreállítás a StorSimple 8000 sorozatú eszközök esetében a helyreállítási](storsimple-8000-device-failover-disaster-recovery.md).

A feladatátvételt egy másik fizikai eszközt a StorSimple fizikai eszköz, Ugrás [átadja a feladatokat a StorSimple fizikai eszköz](storsimple-8000-device-failover-physical-device.md). A maga az eszköz feladatainak átadása, lépjen a [átadja a feladatokat a ugyanabban StorSimple fizikai eszköz](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy áttekintette az eszköz feladatátvételi szempontok. További információért ugorjon [általános szempontok az eszköz feladatátvételéhez](storsimple-8000-device-failover-disaster-recovery.md).

- A StorSimple felhőalapú készülék létrehozása és konfigurálása az eljárás futtatása előtt kell rendelkeznie. Ha a frissítés 3 szoftver verziója fut, vagy később, fontolja meg egy 8020-as felhőalapú berendezés a vészhelyreállítás. A 8020-as modell pedig 64 TB rendelkezik, és Premium szintű tárolást használ. További információért ugorjon [üzembe helyezése és kezelése a StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>A felhőalapú berendezésre történő feladatátvételt lépések

A következő lépésekkel állíthatja vissza az eszköz StorSimple Cloud Appliance cél.

1.  Győződjön meg arról, hogy a kötettároló feladatátvételét szeretné a felhőbeli pillanatképek vannak társítva. További információért ugorjon [használata a StorSimple-Eszközkezelő szolgáltatás a biztonsági mentések létrehozását](storsimple-8000-manage-backup-policies-u2.md).
2. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az a **eszközök** panelen nyissa meg a szolgáltatáshoz csatlakoztatott eszközök listájához.
    ![Eszköz kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Válassza ki, majd kattintson a forráseszközt. A forráseszköz kötettárolók, amelyeket szeretne a feladatátvételt. Lépjen a **beállítások > Kötettárolók**.

    ![Válasszon eszközt](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Válassza ki a kötettárolót, amely átadja a feladatokat egy másik eszközön szeretné. Kattintson a kötettároló ebben a tárolóban lévő kötetek listájának megjelenítéséhez. Válassza ki a kötetet, kattintson a jobb gombbal, majd kattintson **Offline állapotba** a kötet offline állapotba.

    ![Válasszon eszközt](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. A kötettároló a minden kötet esetében ismételje meg a folyamatot.

     ![Válasszon eszközt](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Ismételje meg az előző lépésben, átadja a feladatokat egy másik eszközön szeretné kötettárolók.

7. Lépjen vissza a **eszközök** panelen. A parancssávon kattintson **átadja a feladatokat**.

    ![Kattintson a sikertelen keresztül](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. Az a **átadja a feladatokat** panelen hajtsa végre az alábbi lépéseket:
   
    1. Kattintson a **forrás**. Válassza ki a kötettároló feladatátvételét. **Csak a felhőbeli pillanatképek és offline kötetek kötettárolók jelennek meg.**
        ![Forrás kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Kattintson a **cél**. Az elérhető eszközök a legördülő listából válassza ki a cél a felhőalapú berendezés. **A listában csak a forrás-kötettároló befogadásához elegendő kapacitással rendelkező eszközök jelennek meg.**

        ![Cél kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Tekintse át a feladatátvételi beállításokat alatt **összefoglalás** , és jelölje be a jelölőnégyzetet, amely azt jelzi, hogy a kiválasztott kötettárolókban kötetek offline állapotban. 

        ![Tekintse át a feladatátvételi beállításokat](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. Egy feladatátvételi feladat jön létre. A feladatátvételi feladat figyeléséhez kattintson az értesítés.

    ![A figyelő feladatátvételi feladat](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. A feladatátvétel befejezését követően váltson vissza a **eszközök** panelen.

    1. Válassza ki az eszközt, hogy a feladatátvételhez célként használt.

       ![Válasszon eszközt](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Kattintson a **Kötettárolók**. Minden kötettárolók, a köteteket a régi eszközről, együtt kell szerepelnie.

       Ha rendelkezik a meghibásodott adatbázison a kötettároló helyileg rögzített kötetekről, a köteteket feladatátvétel történt, a rétegzett kötetek. A gyors helyi kötetek nem támogatottak a StorSimple Cloud Appliance.

       ![Nézet target kötettárolók](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>További lépések

* Miután elvégezte a feladatátvételt, szükség lehet [inaktiválja vagy törölje a StorSimple-eszköz](storsimple-8000-deactivate-and-delete-device.md).

* A StorSimple-Eszközkezelő szolgáltatás használatával kapcsolatos információkért tekintse meg a [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).

