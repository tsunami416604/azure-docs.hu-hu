---
title: Feladatátvétel és vészhelyreállítás ugyanahhoz a StorSimple 8000-eszközhöz
description: Ismerje meg, hogyan kell feladatátvételt a StorSimple-eszköz ugyanazon az eszközön.
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
ms.openlocfilehash: c8fe2d7ec7649f47f6cb9c8ae2c83f19c15691b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471805"
---
# <a name="fail-over-your-storsimple-physical-device-to-same-device"></a>A StorSimple fizikai eszköz ének átadása ugyanarra az eszközre

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag ismerteti a StorSimple 8000 sorozatú fizikai eszközök önmagának való feladatátvételhez szükséges lépéseket, ha katasztrófa történik. A StorSimple az eszköz feladatátvételi szolgáltatásával telepíti át az adatokat az adatközpontban lévő fizikai forráseszközről egy másik fizikai eszközre. Az oktatóanyagban található útmutatás a 3- as és újabb frissítéssel működő StorSimple 8000 sorozatú fizikai eszközökre vonatkozik.

Ha többet szeretne megtudni az eszközfeladat-átvételről és arról, hogyan használja a katasztrófa utáni helyreállítást, nyissa meg [a Feladatátvételés vészhelyreállítást a StorSimple 8000 sorozatú eszközökszámára.](storsimple-8000-device-failover-disaster-recovery.md)

Ha egy fizikai eszközt egy másik fizikai eszközre szeretne átadni, nyissa [meg a Feladatátvétel lehetőséget ugyanarra a StorSimple fizikai eszközre.](storsimple-8000-device-failover-physical-device.md) Ha egy StorSimple fizikai eszköz egy StorSimple felhőalapú berendezés, megy [a feladatátvétel egy StorSimple felhőalapú berendezés.](storsimple-8000-device-failover-cloud-appliance.md)


## <a name="prerequisites"></a>Előfeltételek

- Győződjön meg arról, hogy áttekintette az eszköz feladatátvételi szempontokat. További információt az [eszközfeladat-átvétel általános szempontjai](storsimple-8000-device-failover-disaster-recovery.md)című területen talál.


## <a name="steps-to-fail-over-to-the-same-device"></a>Az ugyanarra az eszközre való feladatátvétel lépései

Hajtsa végre a következő lépéseket, ha ugyanarra az eszközre kell átadnia a feladatokat.

1. Felhőbeli pillanatképeket készíthet az eszköz összes kötetéről. További információt a [StorSimple Eszközkezelő szolgáltatás használata biztonsági másolatok létrehozásához](storsimple-8000-manage-backup-policies-u2.md)című területen talál.
2. Állítsa vissza az eszközt a gyári alapértékekre. Kövesse a [StorSimple eszköz gyári alapértelmezett beállításaira való visszaállítására](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)vonatkozó részletes utasításokat.
3. Nyissa meg a StorSimple Eszközkezelő szolgáltatást, és válassza **az Eszközök**lehetőséget. Az **Eszközök** panelen a régi eszköznek offline állapotban kell **megjelennie.**

    ![Forráseszköz offline módban](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev2.png)

4. Konfigurálja az eszközt, és regisztrálja újra a StorSimple Eszközkezelő szolgáltatással. Az újonnan regisztrált eszköznek **készen kell megjelennie a beállításra.** Az új eszköz neve megegyezik a régi eszközzel, de egy számmal jelzi, hogy az eszköz gyári alapértékre állt vissza, és újra regisztrálva lett.

    ![Újonnan regisztrált eszköz készen áll a beállításra](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev3.png)
5. Az új eszköz esetében végezze el az eszköz beállítását. További információ: [4.](storsimple-8000-deployment-walkthrough-u2.md#step-4-complete-minimum-device-setup) Az **Eszközök** panelen az eszköz állapota **Online**állapotra változik.

   > [!IMPORTANT]
   > **Először hajtsa végre a minimális konfigurációt, vagy a vész-vész-, sikertelen lehet.**

    ![Újonnan regisztrált eszköz online](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev7.png)

6. Jelölje ki a régi eszközt (állapot offline állapot), majd a parancssávon kattintson a **Feladatátvétel**gombra. A **Feladatátvétel** panelen válassza ki a régi eszközt forrásként, és adja meg a céleszközt újonnan regisztrált eszközként.

    ![Feladatátvétel összegzése](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev11.png)

    Részletes utasításokat a Feladatátvétel egy másik fizikai eszközre című dokumentumban talál.

7. Létrejön egy eszköz-visszaállítási feladat, amely a **Feladatok** panelről figyelhető.

8. Miután a feladat sikeresen befejeződött, nyissa meg az új eszközt, és keresse meg a **Kötet tárolók** panelt. Ellenőrizze, hogy a régi eszköz összes kötettárolója átlett-e az új eszközre.

   ![Áttelepített kötettárolók](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev13.png)

9. A feladatátvétel befejezése után inaktiválhatja és törölheti a régi eszközt a portálról. Jelölje ki a régi eszközt (offline), kattintson a jobb gombbal, majd válassza **az Inaktiválás parancsot.** Az eszköz inaktiválása után az eszköz állapota frissül.

     ![A forráseszköz inaktiválva](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev14.png)

10. Jelölje ki az inaktivált eszközt, kattintson a jobb gombbal, majd válassza **a Törlés parancsot.** Ezzel törli az eszközt az eszközök listájáról.

    ![A forráseszköz törölve](./media/storsimple-8000-device-failover-disaster-recovery/failover-single-dev15.png)



## <a name="next-steps"></a>További lépések

* Miután végrehajtotta a feladatátvételt, előfordulhat, hogy [inaktiválnia vagy törölnie kell a StorSimple eszközt.](storsimple-8000-deactivate-and-delete-device.md)
* A StorSimple Eszközkezelő szolgáltatás használatáról a [StorSimple Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez című oldalon](storsimple-8000-manager-service-administration.md)talál.

