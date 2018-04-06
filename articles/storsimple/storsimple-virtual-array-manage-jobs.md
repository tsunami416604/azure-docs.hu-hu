---
title: Megtekintheti és kezelheti a StorSimple virtuális tömb feladatok |} Microsoft Docs
description: A StorSimple Device Manager szolgáltatás feladatok lapján, és nyomon követheti a legutóbbi és a jelenlegi feladatok a StorSimple virtuális tömb ismerteti.
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
ms.openlocfilehash: 3fd1c262a8ce94d8e98f2b066a8028d974b15b1d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>A StorSimple Device Manager szolgáltatás használata a feladatok megtekintéséhez a StorSimple virtuális tömb
## <a name="overview"></a>Áttekintés
A **feladatok** panel megtekintéséhez és kezeléséhez a StorSimple eszköz Manager szolgáltatáshoz kapcsolódó virtuális tömbökön indított feladatok egyetlen központi portált biztosít. Több virtuális eszközön futó, befejezett, és a sikertelen feladatok tekintheti meg. Eredmények táblázatos formában jelennek meg.

![Feladatok panelen](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

Gyorsan megtalálhatja a feladatok olyan mezőkön, például a szűréssel érdeklődik:

* **Időtartománynak** – feladatok a dátum és idő tartomány alapján szűrhetők.
* **Eszközök** – feladatok kezdeményezett egy adott eszköz kapcsolódik a szolgáltatáshoz. A szűrt feladatok majd megjelennének a következő attribútumok alapján:
  
  * **Név** – a feladat neve lehet **minden**, **biztonsági mentés**, **Klónozás**, **feladatátvételt**, **frissítéseinekletöltése**, vagy **frissítések telepítése**.
  * **Állapot** – feladatok lehetnek **összes**, **folyamatban**, **sikeres**, vagy **sikertelen**, vagy **megszakítva**.
  * **Entitás** – a feladat társíthatók a kötet, megosztás, vagy eszköz.
  * **Eszköz** – a név az eszköz, amelyen a feladat elindult.
  * **Megkezdődött a** – az idő, amikor a feladat elindult.
  * **Időtartam** – az időtartamot a meg a feladat futott.
* **Állapot** – minden, futó, hajtható végre, vagy a sikertelen feladatok is kereshet.
* **Típusú feladattal** – a feladattípus is all, biztonsági mentési, helyreállítási, feladatátvevő, töltse le a frissítéseket, vagy telepítse a frissítéseket.

A feladatok 30 másodpercenként frissül.

## <a name="view-job-details"></a>Feladatok részleteinek megjelenítése
A következő lépésekkel minden feladat részleteinek megtekintéséhez.

#### <a name="to-view-job-details"></a>Feladat részleteinek megtekintése
1. A a **feladatok** panelen, a megfelelő szűrőket és egy lekérdezés futtatásával érdeklődik feladat(ok) megjelenítéséhez. Befejezett vagy nem futnak feladatok is kereshet.
2. Jelölje ki a feladatot a feladatok táblázatos listájáról.
   
    ![Feladat panelen](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. Kattintson a lap alján **részletek**.
4. Az a **részletek** párbeszédpanelen megtekintheti állapotát, a részletek és a vonatkozó adatok. Az alábbi ábrán egy példa a **biztonsági mentési feladat részleteit** párbeszédpanel megnyitásához.
   
    ![Feladat részletei](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Ha a virtuális gép fel van függesztve, a hipervizor a sikertelen feladatok
Ha egy feladat a folyamatban van a StorSimple virtuális tömb és az eszköz (a virtuális gép kiépítése a hipervizor) fel van függesztve, több mint 15 perc, a feladat sikertelen lesz. Ez a StorSimple virtuális tömb idő miatt folyamatban van szinkronizálva a Microsoft Azure idő. 

Látni fogja a következő hibával: "az eszköz ideje szinkronizálva a Microsoft Azure idő több mint 15 perces. Ügyeljen arra, hogy a hipervizor és szinkronizálva van egy NTP ügyfélkérelmet az eszközt. Győződjön meg arról, hogy nincsenek-e kapcsolódási problémák. Kapcsolódási problémák elhárításához futtassa a diagnosztikai tesztek a helyi webes felhasználói Felületét a virtuális eszköz."

Ezek a hibák biztonsági mentés, a visszaállítás, a frissítés és a feladatátvételi feladatok vonatkozik. Ha a virtuális gép a Hyper-v-ben lett beállítva, a számítógép idejét végül szinkronizálja a hipervizor. Ebben az esetben, ha a feladat indíthatja el.

## <a name="next-steps"></a>További lépések
[A helyi webes felhasználói felület használata a StorSimple virtuális tömb felügyeletéhez](storsimple-ova-web-ui-admin.md).

