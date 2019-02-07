---
title: A feladatátvétel a StorSimple 8000-es sorozatú eszközökön vész-helyreállítási |} A Microsoft Docs
description: Ismerje meg, hogyan végezhet feladatátvételt a StorSimple-eszköz ugyanarra az eszközre.
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
ms.date: 06/23/2017
ms.author: alkohli
ms.openlocfilehash: dd207eaad1a3e821724d51a890d0882bfffda131
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55809061"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>Feladatátvétel ugyanarra az eszközre a StorSimple fizikai eszköz

## <a name="overview"></a>Áttekintés

Ebben az oktatóanyagban a feladatátvételt egy StorSimple 8000 sorozatú fizikai eszköz saját maga vészhelyzet esetén szükséges lépéseket ismerteti. A StorSimple az eszköz feladatátvételi funkciót használja, egy másik fizikai eszközre telepítheti át adatait a forrás fizikai eszköz az adatközpontban. Ebben az oktatóanyagban az útmutató vonatkozik a StorSimple 8000 sorozat szoftververziók Update 3 és újabb verzióit futtató fizikai eszközöket.

Eszköz-feladatátvétel és a egy katasztrófa utáni helyreállítás felhasználási módjáról további információkért lépjen [feladatátvétel és vészhelyreállítás a StorSimple 8000 sorozatú eszközök esetében a helyreállítási](storsimple-8000-device-failover-disaster-recovery.md).

A feladatátvételt egy másik fizikai eszköz a fizikai eszközt, Ugrás [átadja a feladatokat a ugyanabban StorSimple fizikai eszköz](storsimple-8000-device-failover-physical-device.md). A StorSimple fizikai eszköz a StorSimple Cloud Appliance feladatátvételt, lépjen a [átadja a feladatokat egy StorSimple Cloud Appliance](storsimple-8000-device-failover-cloud-appliance.md).


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy áttekintette az eszköz feladatátvételi szempontok. További információért ugorjon [általános szempontok az eszköz feladatátvételéhez](storsimple-8000-device-failover-disaster-recovery.md).


## <a name="steps-to-fail-over-to-the-same-device"></a>A feladatátvétel ugyanarra az eszközre lépések

Hajtsa végre az alábbi lépéseket, ha a feladatátvétel ugyanarra az eszközre van szüksége.

1. Minden kötet felhőbeli pillanatképek készítése az eszközön. További információért ugorjon [használata a StorSimple-Eszközkezelő szolgáltatás a biztonsági mentések létrehozását](storsimple-8000-manage-backup-policies-u2.md).
2. Az eszköz visszaállítása a gyári beállításokat. Részletes utasításait [a StorSimple eszköz visszaállítása a gyári alapértelmezett beállításokra](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
3. Keresse meg a StorSimple-Eszközkezelő szolgáltatást, majd **eszközök**. Az a **eszközök** a régi eszköz panelen kell állapotúként **Offline**.

    ![Forrás-eszköz kapcsolat nélküli módban](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Konfigurálja az eszközt, és regisztrálja újra a StorSimple-Eszközkezelő szolgáltatásban. Az újonnan regisztrált eszköz állapotúként kell **beállításra kész**. Az eszköz nevét, az új eszköz ugyanaz, mint a régi eszközök, de egy számot jelzi, hogy az eszköz gyári Alapértelmezések visszaállítása, és újra regisztrálni kiegészítve.

    ![Újonnan regisztrált eszköz beállításra kész](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Az új eszközhöz az eszköz beállításának befejezése. További információért ugorjon [4. lépés: Minimális eszközbeállítások teljesítéséhez](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup). Az a **eszközök** panelen a az eszköz állapota **Online**.

   > [!IMPORTANT]
   > **Először végezze el a minimális konfiguráció, vagy a Vészhelyreállításhoz sikertelenek lehetnek.**

    ![Online újonnan regisztrált eszköz](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Válassza ki a régi eszközt (kapcsolat nélküli állapot), és kattintson a parancssávon **átadja a feladatokat**. Az a **átadja a feladatokat** panelen válassza ki a régi eszközt a forrásként, és adja meg az újonnan regisztrált eszköz a céleszközön.

    ![Feladatátvételi összegzése](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Részletes utasításokért tekintse meg a sikertelen keresztül egy másik fizikai eszköz.

7. Egy eszköz visszaállítási feladat jön létre a figyelésére a **feladatok** panelen.

8. Miután a feladat sikeresen befejeződött, az új eszköz eléréséhez, és keresse meg a **kötettárolók** panelen. Győződjön meg arról, hogy a régi eszközről minden kötettároló át lettek-e az új eszköz.

   ![Áttelepített kötettárolók](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. A feladatátvétel befejezése után, akkor is eszköz inaktiválása és törlése a régi a portálról. A régi eszköz (offline), kattintson a jobb gombbal, majd válassza ki és **inaktiválás**. Az eszköz az inaktiválása után az eszköz állapota frissül.

     ![Forrás eszköz inaktiválása](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Válassza ki a inaktivált eszköz, kattintson a jobb gombbal, és válassza ki **törlése**. Ez törli az eszközt az eszközlistáról.

    ![A forráseszközt törölve](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>További lépések

* Miután elvégezte a feladatátvételt, szükség lehet [inaktiválja vagy törölje a StorSimple-eszköz](storsimple-8000-deactivate-and-delete-device.md).
* A StorSimple-Eszközkezelő szolgáltatás használatával kapcsolatos információkért tekintse meg a [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).

