---
title: "StorSimple feladatátvétel, a StorSimple felhő berendezésen vész-helyreállítási |} Microsoft Docs"
description: "Útmutató: a StorSimple 8000 sorozat fizikai eszköz egy felhőalapú készülék a feladatátvételt."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: ec8bebf2854e84a37e84b45564e80fc20b63d8d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-to-your-storsimple-cloud-appliance"></a>Feladatok átadása a StorSimple felhő készülék

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag a StorSimple 8000 series fizikai eszközöket, hogy a StorSimple felhő készülék feladatátvételt, ha egy olyan vészhelyzet esetén szükséges lépéseket ismerteti. A StorSimple eszköz feladatátvételi szolgáltatását használja át a forrás fizikai eszközt az adatközpontban lévő adatok egy Azure-ban futó felhő készülék. Ez az oktatóanyag az útmutató a StorSimple 8000 series fizikai eszközöket és a felhő készülékek 3 és újabb verzióit futtató a szoftver verziója frissítés vonatkozik.

Eszköz feladatátvételi, és hogyan használja a katasztrófa utáni helyreállítás kapcsolatos további tudnivalókért keresse fel [feladatátvétel és a katasztrófa utáni helyreállítás a StorSimple 8000 sorozat eszközeire](storsimple-8000-device-failover-disaster-recovery.md).

Feladatok átadása a StorSimple fizikai eszköz egy másik fizikai eszközön, keresse fel [feladatok átadása a StorSimple fizikai eszköz](storsimple-8000-device-failover-physical-device.md). Maga az eszköz feladatátvételt, keresse fel [átveheti az ugyanazon fizikai StorSimple-eszköz](storsimple-8000-device-failover-same-device.md).

## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy átolvasta eszköz feladatátvételi szempontokat. További információkért látogasson el [eszköz feladatátvételi a gyakori szempontok](storsimple-8000-device-failover-disaster-recovery.md).

- A StorSimple felhő készülék létrehozása és konfigurálása a művelet futtatása előtt kell rendelkeznie. Ha a futó 3 ügyfélszoftverének verziójára frissítése, vagy később, érdemes lehet egy 8020-as modellt felhő készüléknek az a vész-Helyreállítási. A 8020-as modellt modell 64 TB-os rendelkezik, és a prémium szintű Storage használja. További információkért látogasson el [központi telepítése és kezelése a StorSimple felhő készülék](storsimple-8000-cloud-appliance-u2.md).

## <a name="steps-to-fail-over-to-a-cloud-appliance"></a>Feladatok átadása a felhő készülék lépései

A következő lépésekkel visszaállíthatja az eszköz StorSimple felhő készülék célja.

1.  Győződjön meg arról, hogy a feladatátvétel kívánt kötettároló felhőalapú pillanatfelvételek vannak társítva. További információkért látogasson el [StorSimple az Eszközkezelő szolgáltatás a biztonsági mentések létrehozását](storsimple-8000-manage-backup-policies-u2.md).
2. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az a **eszközök** panelen, lépjen a szolgáltatáshoz kapcsolódó eszközök listáját.
    ![Válassza ki az eszköz](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev1.png)
3. Válassza ki, és kattintson a forrás-eszközre. A forráseszköz átadni kívánt kötettárolók. Ugrás a **beállítások > Kötettárolók**.

    ![Válassza ki az eszköz](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev2.png)
    
4. Válassza ki a kötettároló szeretné, hogy áthelyezze a feladatokat egy másik eszközre. Kattintson a kötettároló található kötetek listájának megjelenítéséhez. Válassza ki a kötetet, kattintson a jobb gombbal, majd kattintson **Offline állapotba** a kötet offline állapotba.

    ![Válassza ki az eszköz](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev5.png)

5. Ismételje meg ezt a folyamatot a a kötettároló összes kötetet.

     ![Válassza ki az eszköz](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev7.png)

6. Ismételje meg az előző lépésben szeretné, hogy áthelyezze a feladatokat egy másik eszközre kötettárolók.

7. Lépjen vissza a **eszközök** panelen. A parancssávon kattintson **feladatátvételt**.

    ![Kattintson a sikertelen keresztül](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev8.png)
8. Az a **feladatátvételt** panelen végezze el a következő lépéseket:
   
    1. Kattintson a **forrás**. Válassza ki a kötettárolók, hogy áthelyezze a feladatokat. **Csak a hozzárendelt felhő pillanatképek és a kapcsolat nélküli kötetekkel kötettárolók jelennek meg.**
        ![Forrás kiválasztása](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev11.png)
    2. Kattintson a **cél**. Válassza ki a cél felhő készülék az elérhető eszközök a legördülő listából. **Csak azokat az eszközöket, amelyek forrás kötettárolók befogadásához elegendő kapacitással rendelkeznek a listában jelennek meg.**

        ![Válassza ki a cél](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev12.png)

    3. Tekintse át a feladatátvételi beállítások **összegzés** , és jelölje be a jelölőnégyzetet, amely azt jelzi, hogy a kijelölt kötet tárolókban lévő kötetek offline módban. 

        ![Tekintse át a feladatátvételi beállításait](./media/storsimple-8000-device-failover-disaster-recovery/failover-cloud-dev13.png)

9. A feladatátvételi feladatban jön létre. A feladatátvételi feladatban figyeléséhez, kattintson a feladat értesítésre.

    ![A figyelő feladatátvételi feladatban](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev13.png)

10. A feladatátvétel elvégzése után térjen vissza a **eszközök** panelen.

    1. Válassza ki az eszközt, a feladatátvétel céljaként használt.

       ![Válassza ki az eszköz](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev14.png)

    2. Kattintson a **Kötettárolók**. Minden kötettárolók, a köteteket a régi eszközről együtt kell szerepelnie.

       Ha a kötet tároló, amely akkor feladatátvételt rendelkezik helyileg rögzített kötetek, a köteteket feladatátvétel történt rétegzett kötetek. Helyileg rögzített kötetek nem támogatottak a StorSimple-felhő készüléken.

       ![Nézet target kötettárolók](./media/storsimple-8000-device-failover-disaster-recovery/failover-phy-dev17.png)


## <a name="next-steps"></a>Következő lépések

* Miután elvégezte a feladatátvételt, esetleg [inaktiválja vagy törölje a StorSimple eszköz](storsimple-8000-deactivate-and-delete-device.md).

* A StorSimple Device Manager szolgáltatás használatával kapcsolatos információkért látogasson el [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

