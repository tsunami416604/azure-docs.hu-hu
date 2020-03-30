---
title: StorSimple Virtual Array feladatok megtekintése és kezelése | Microsoft dokumentumok
description: Ez a témakör a StorSimple Device Manager szolgáltatás feladatok lap, és hogyan lehet használni a StorSimple virtuális tömb legutóbbi és jelenlegi feladatok nyomon követésére.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60302499"
---
# <a name="use-the-storsimple-device-manager-service-to-view-jobs-for-the-storsimple-virtual-array"></a>A StorSimple Eszközkezelő szolgáltatás használata a StorSimple virtuális tömb feladatainak megtekintéséhez
## <a name="overview"></a>Áttekintés
A **Feladatok** panel egyetlen központi portált biztosít a StorSimple Eszközkezelő szolgáltatáshoz csatlakoztatott virtuális tömbökön elindított feladatok megtekintéséhez és kezeléséhez. Több virtuális eszköz futó, befejezett és sikertelen feladatait tekintheti meg. Az eredmények táblázatos formátumban jelennek meg.

![Jobs penge](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)

A következő mezők szűrésével gyorsan megtalálhatja az önt érdeklő feladatokat:

* **Időtartomány** – A feladatok a dátum és az idő tartománya alapján szűrhetők.
* **Eszközök** – A feladatok a szolgáltatáshoz csatlakoztatott adott eszközön jönnek létre. A szűrt feladatok táblázatba foglalása a következő attribútumok alapján lesznek táblázatba foglalva:
  
  * **Név** – A feladat neve lehet **All**, **Backup**, **Clone**, **Feladatátvétel,** **Frissítések letöltése**vagy **Frissítések telepítése.**
  * **Állapot** – A feladatok lehetnek **Mind**, **Folyamatban,** **Sikeresek**vagy **Sikertelenek**vagy **Visszavontak.**
  * **Entitás** – A feladatok kötethez, megosztáshoz vagy eszközhöz társíthatók.
  * **Eszköz** – Annak az eszköznek a neve, amelyen a feladat elindult.
  * **Started on** – A feladat elindításának időpontja.
  * **Időtartam** – az az időtartam, amelyen a feladat futott.
* **Állapot** – Az összes, futó, befejezett vagy sikertelen feladatot megkeresheti.
* **Feladat típusa** – A feladat típusa lehet az összes, biztonsági mentés, visszaállítás, feladatátvétel, frissítések letöltése vagy frissítések telepítése.

A feladatok listája 30 másodpercenként frissül.

## <a name="view-job-details"></a>Feladatok részleteinek megjelenítése
Hajtsa végre az alábbi lépéseket bármely feladat részleteinek megtekintéséhez.

#### <a name="to-view-job-details"></a>Feladat részleteinek megtekintése
1. A **Feladatok** panelen jelenítse meg az önt érdeklő feladatot(oka)t a megfelelő szűrőkkel rendelkező lekérdezés futtatásával. Megkeresheti a befejezett vagy futó feladatokat.
2. Válasszon ki egy feladatot a feladatok táblázatos listájából.
   
    ![Munka penge](./media/storsimple-virtual-array-manage-jobs/ova-jobs-blade.png)
3. A lap alján kattintson a **Részletek gombra.**
4. A **Részletek** párbeszédpanelen megtekintheti az állapot-, a részleteket és az időstatisztikákat. A következő ábrán a Biztonsági másolat készítés részletei párbeszédpanel **látható.**
   
    ![Feladat részletei](./media/storsimple-virtual-array-manage-jobs/ova-jobs-details.png)

#### <a name="job-failures-when-the-virtual-machine-is-paused-in-the-hypervisor"></a>Feladathibák, ha a virtuális gép szünetel a hipervizorban
Ha egy feladat folyamatban van a StorSimple virtuális tömb, és az eszköz (a hipervizorban kiépített virtuális gép) 15 percnél hosszabb ideig szünetel, a feladat sikertelen lesz. Ez annak köszönhető, hogy a StorSimple virtuális tömb ideje nincs szinkronban a Microsoft Azure-idő. 

A következő hibaüzenet jelenik meg: "Az eszköz ideje több mint 15 perccel nincs szinkronban a Microsoft Azure-idővel. Győződjön meg arról, hogy a hipervizor és az eszköz idők szinkronizálva vannak egy NTP-kiszolgálóval. Ellenőrizze, hogy nincsenek-e csatlakozási problémák. A kapcsolódási problémák elhárításához futtasson diagnosztikai teszteket a virtuális eszköz helyi webes felhasználói felületéről."

Ezek a hibák a biztonsági mentési, visszaállítási, frissítési és feladatátvételi feladatokra vonatkoznak. Ha a virtuális gép a Hyper-V-ben van kiépítve, a gép végül szinkronizálja az időt a hipervizorral. Ha ez megtörténik, újraindíthatja a feladatot.

## <a name="next-steps"></a>További lépések
[Ismerje meg, hogyan használhatja a helyi webes felhasználói felületet a StorSimple virtuális tömb felügyeletére.](storsimple-ova-web-ui-admin.md)

