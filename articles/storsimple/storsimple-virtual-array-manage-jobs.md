---
title: A StorSimple Virtual Array feladatok megtekintése és kezelése |} A Microsoft Docs
description: A StorSimple-Eszközkezelő szolgáltatás feladatok lapján, és hogyan használható a legutóbbi és az aktuális feladatok nyomon követheti a StorSimple Virtual Array ismertetése
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 31879821-b599-4609-a7f4-d4b0f658a933
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/11/2016
ms.author: alkohli
ms.openlocfilehash: dbab2aaab2c12bef07748f54e5864d042f1c982a
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333564"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>A StorSimple-Eszközkezelő szolgáltatás használata a StorSimple Virtual Array feladatok megtekintése
## <a name="overview"></a>Áttekintés
A **feladatok** paneljén tekinthetők meg és a virtuális tömböket, amely a StorSimple-Eszközkezelő szolgáltatás csatlakozik az elindított feladatok kezelése egyetlen központi portált biztosít. Több virtuális eszközön futó, befejezett, és a sikertelen feladatok tekintheti meg. Eredmények táblázatos formátumban jelennek meg.

![Feladatok panelen](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Gyorsan megtalálhatja a feladatok, például a mezők szűrése érdekli:

* **Időtartomány** – feladatok a dátum és idő tartomány alapján szűrhetők.
* **Eszközök** – feladatok kezdeményezett csatlakozik a szolgáltatáshoz egy adott eszközön. A szűrt feladatok majd megjelennének attribútumok közül a következők alapján:
  
  * **Név** – a feladat neve lehet **minden**, **Backup**, **Klónozás**, **átadja a feladatokat**, **frissítésekletöltése**, vagy **frissítések telepítése**.
  * **Állapot** – feladatok lehetnek **összes**, **folyamatban**, **sikeres**, vagy **sikertelen**, vagy **Visszavonva**.
  * **Entitás** – lehet, hogy a feladatok egy kötet, megosztás vagy egy eszköz társítva.
  * **Eszköz** – az a név az eszköz, amelyen a feladat el lett indítva.
  * **A lépések** – az idő, amikor a feladat el lett indítva.
  * **Időtartam** – a teljes időtartamát az a feladat futtatása óta.
* **Állapot** – minden, futó, befejeződött vagy meghiúsult feladatok kereshet.
* **Feladat típusa** – a feladat típusa, all, biztonsági mentési és visszaállítási, feladatátvétel, frissítések letöltése vagy telepítse a frissítéseket.

A feladatok 30 másodpercenként frissül.

## <a name="view-job-details"></a>Feladatok részleteinek megjelenítése
Hajtsa végre az alábbi lépéseket az összes feladat részleteinek megtekintéséhez.

#### <a name="to-view-job-details"></a>Feladat részleteinek megtekintése
1. Az a **feladatok** panelen egy lekérdezés futtatásával megfelelő szűrőket érdekelné feladat(ok) megjelenítéséhez. A befejezett vagy futó feladatok is kereshet.
2. A feladatok táblázatos listájából válasszon ki egy feladatot.
   
    ![Feladat panel](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. Kattintson a lap alján **részletek**.
4. Az a **részletek** párbeszédpanelen állapotát, adatait és idő statisztikákat is megtekintheti. Az alábbi ábrán egy példa a **biztonsági mentési feladat részleteit** párbeszédpanel bezárásához.
   
    ![Feladat részletei](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Ha a virtuális gép fel van függesztve, a hipervizorban lévő feladat sikertelen
Ha egy feladat a folyamatban van a StorSimple Virtual Array és az eszköz (hipervizoron üzembe helyezett virtuális gép) fel van függesztve, a nagyobb, mint 15 perc, a feladat sikertelen lesz. Ez a StorSimple Virtual Array idő miatt folyamatban van a Microsoft Azure időt szinkronizálva. 

Látni fogja a következő hibával: "az eszközön beállított idő nincs szinkronizálva a Microsoft Azure idő szerint több mint 15 perc. Győződjön meg arról, hogy a hipervizor és az eszköz hányszor szinkronizálva legyenek az NTP-kiszolgálót. Győződjön meg arról, hogy nincsenek kapcsolódási problémák. Kapcsolódási problémák elhárításához futtasson diagnosztikai teszteket a helyi webes felhasználói felületen, a virtuális eszköz."

Ezek a hibák biztonsági mentés, visszaállítás, frissítési és feladatátvételi feladatok vonatkoznak. Ha a virtuális gép kiépítése a Hyper-V, a gép idejét idővel szinkronizálja a hipervizor. Után történik, hogy indítsa újra a feladatot.

## <a name="next-steps"></a>További lépések
[Ismerje meg, hogyan felügyelete a StorSimple virtuális tömb helyi webes felületén használandó](storsimple-ova-web-ui-admin.md).

