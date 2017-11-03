---
title: "A feladatátvétel a StorSimple 8000 sorozat eszközeire vész-helyreállítási |} Microsoft Docs"
description: "Útmutató: a StorSimple eszköz ugyanarra az eszközre a feladatátvétel."
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
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: acc8929dc3476e9590e8e4d9526b38b7c0719570
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>A StorSimple fizikai eszköz ugyanarra az eszközre történő feladatátvételt

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag a feladatátvételt a StorSimple 8000 series fizikai az eszköz magát katasztrófa esetén szükséges lépéseket ismerteti. StorSimple eszköz feladatátvételi szolgáltatását használja egy másik fizikai eszközre át adatokat a forrás fizikai eszközt az adatközpontban. Ez az oktatóanyag az útmutató a StorSimple 8000 Series érvényes 3 és újabb verzióit szoftververziók frissítést futtató fizikai eszközöket.

Eszköz feladatátvételi, és hogyan használja a katasztrófa utáni helyreállítás kapcsolatos további tudnivalókért keresse fel [feladatátvétel és a katasztrófa utáni helyreállítás a StorSimple 8000 sorozat eszközeire](storsimple-8000-device-failover-disaster-recovery.md).

Feladatok átadása egy másik fizikai eszköz fizikai eszköz, keresse fel [átveheti az ugyanazon fizikai StorSimple-eszköz](storsimple-8000-device-failover-physical-device.md). Feladatok átadása a StorSimple felhő készülék StorSimple fizikai eszköz, keresse fel [feladatok átadása a StorSimple felhő készülék](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy átolvasta eszköz feladatátvételi szempontokat. További információkért látogasson el [eszköz feladatátvételi a gyakori szempontok](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>Feladatok átadása a ugyanarra az eszközre lépései

Ha feladatátvételt ugyanarra az eszközre van szüksége, hajtsa végre az alábbi lépéseket.

1. A kötetek felhőalapú pillanatfelvételek az eszközt a hálózatról. További információkért látogasson el [StorSimple az Eszközkezelő szolgáltatás a biztonsági mentések létrehozását](storsimple-8000-manage-backup-policies-u2.md).
2. Az eszköz visszaállítása a gyári beállításokat. Részletes utasításait [a StorSimple eszköz visszaállítása a gyári alapértelmezett beállításokra](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Nyissa meg a StorSimple eszköz Manager szolgáltatáshoz, és válassza ki **eszközök**. Az a **eszközök** panelen jelennek meg a régi eszköz **Offline**.

    ![Forrás-eszköz kapcsolat nélküli módban](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Állítsa be az eszközt, és regisztrálja újra a StorSimple eszköz Manager szolgáltatásban. Az újonnan regisztrált eszközre kell megjeleníteni **már beállíthat**. Az eszköz nevét az új eszköz ugyanaz, mint a régi eszköz, azonban kiegészül a számot jelzi, hogy az eszköz gyári Alapértelmezések visszaállítása, és újra regisztrálni.

    ![Újonnan regisztrált eszköz beállítása kész](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Az új eszközhöz az eszköz beállításának befejezése. További információkért látogasson el [4. lépés: minimális eszközbeállítások végrehajtása](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). Az a **eszközök** panel, az eszköz állapota a **Online**.

   > [!IMPORTANT]
   > **Először végezze el a minimális konfiguráció, vagy a vész-Helyreállítási sikertelenek lehetnek.**

    ![Online az újonnan regisztrált eszközre](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Válassza ki azt a régi eszközt (kapcsolat nélküli állapota), majd kattintson a parancssávon **feladatátvételt**. Az a **feladatátvételt** panelen válassza ki azt a régi eszközt forrásként, és adja meg a céleszközt az újonnan regisztrált eszköz.

    ![Feladatátvételi összegzése](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Részletes útmutatásért tekintse meg [átveheti egy másik fizikai eszköz](#fail-over-to-another-physical-device).

7. Egy eszköz-visszaállítási feladat jön létre, hogy a figyelheti a **feladatok** panelen.

8. Miután a feladat sikeresen befejeződött, az új eszközhöz való hozzáféréshez, és keresse meg a **kötettárolók** panelen. Győződjön meg arról, hogy a régi eszközről kötettárolók átvitelét az új eszköz.

   ![Kötettárolók áttelepítése](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. A feladatátvétel befejezése után inaktiválása, és törli a régi eszközt a portálról. A régi eszköz (offline), kattintson a jobb gombbal, majd válassza ki és **Deactivate**. Az eszközt az Inaktiválás után az eszköz állapota frissül.

     ![Forrás eszköz inaktiválása](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Válassza ki a deaktivált eszközt, kattintson a jobb gombbal, és válassza **törlése**. Ez törli az eszközt az eszközök listája.

    ![Forrás eszköz törlése](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>Következő lépések

* Miután elvégezte a feladatátvételt, esetleg [inaktiválja vagy törölje a StorSimple eszköz](storsimple-8000-deactivate-and-delete-device.md).
* A StorSimple Device Manager szolgáltatás használatával kapcsolatos információkért látogasson el [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

