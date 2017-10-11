---
title: "A StorSimple eszköz inaktiváljon |} Microsoft Docs"
description: "Ismerteti a StorSimple eszköz eltávolítása a szolgáltatás első inaktiválása és törlését is."
services: storsimple
documentationcenter: 
author: SharS
manager: timlt
editor: 
ms.assetid: 155cda38-c5ae-45dc-b7e8-6444494afc9e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2017
ms.author: anbacker
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c000a642aa088ac80cc7077453b87e9a47f96900
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="deactivate-and-delete-a-storsimple-8000-series-device-via-storsimple-manager-service"></a>Inaktiválása és a StorSimple Manager szolgáltatással a StorSimple 8000 series eszköz törlése
## <a name="overview"></a>Áttekintés
Kezdésként érdemes lehet, hogy nem működik a StorSimple eszköz (például akkor, ha cseréje vagy az eszköz frissítése, vagy ha már nem használja a StorSimple). Ha ez a helyzet, szüksége lesz az eszköz inaktiválása, mielőtt törölné. Az eszköz és a megfelelő StorSimple Manager szolgáltatás közötti kapcsolat inaktiválása kiszolgálója. Ez az oktatóanyag ismerteti a StorSimple eszköz eltávolítása a szolgáltatás első inaktiválása és törlését is. 

Ha az eszköz inaktiválása az eszközön helyileg tárolt adatokat már nem lesznek elérhetők. Csak a felhőben tárolt az eszközzel kapcsolatos adatok állíthatók helyre.  

> [!WARNING]
> Az inaktiválást állandó művelet, és nem vonható vissza. Deaktivált eszköz nem lehet regisztrálni a StorSimple Manager szolgáltatásban, kivéve, ha először visszaáll az alapértelmezett gyári beállításokat. 
> 
> A gyári folyamat törli az eszközön helyileg tárolt adatok. Ezért fontos, hogy pillanatképet egy felhőalapú, az adatok egy eszköz inaktiválása előtt. Ez lehetővé teszi az adatok helyreállítása egy későbbi időpontban.
> 
> 

Ez az oktatóanyag azt ismerteti, hogyan:

* Eszköz inaktiválása és az adatok törlése
* Eszköz inaktiválása és az adatok megőrzése mellett

Azt is bemutatja, hogyan inaktiválása és törlési működik a StorSimple virtuális eszköz.

> [!NOTE]
> A StorSimple fizikai vagy virtuális eszköz inaktiválása előtt győződjön meg arról, állítsa le vagy törölje az ügyfelek és kiszolgálók azokon az eszközökön.
> 
> 

## <a name="deactivate-and-delete-data"></a>Inaktiválása és adatok törlése
Ha szeretné az eszköz teljes törlése, és nem szeretné, hogy az eszközön lévő adatok megőrzése mellett, majd hajtsa végre az alábbi lépéseket.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Az eszköz inaktiválásához és az adatok törlése
1. A eszköz inaktiválása előtt törölnie kell az összes kötet az eszközhöz társított tárolók (és a kötetek). Kötettárolók törölheti csak a társított biztonsági másolatok törlését követően.
2. Az eszköz az alábbiak szerint inaktiválása:
   
   1. A StorSimple Manager szolgáltatás **eszközök** lapon, válassza ki az eszköz inaktiválása, és a lap alján kattintson a kívánt **Deactivate**.
   2. Ekkor megjelenik egy megerősítő üzenet. Kattintson a **Igen** folytatja. Az inaktiválás folyamat elindítja és néhány percet igénybe vehet.
3. Az inaktiválást, után törölheti az eszköz teljesen. Egy eszköz törlése eltávolítja a listán szereplő eszközök kapcsolódik a szolgáltatáshoz. A szolgáltatás már nem kezelhetők a törölt eszköz. Az alábbi lépések segítségével az eszköz törlése:
   
   1. A StorSimple Manager szolgáltatás **eszközök** lapon, válassza ki a törölni kívánt inaktív eszközök.
   2. Az oldalon alján kattintson **törlése**.
   3. Megerősítést kér bekéri. Kattintson a **Igen** folytatja.
      
      Törli az eszköz néhány percig is eltarthat.

## <a name="deactivate-and-retain-data"></a>Inaktiválása és adatainak megőrzése
Ha az eszköz törlése érdekelt, de szeretné megőrizni az adatokat, majd hajtsa végre az alábbi lépéseket.

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Eszköz inaktiválása és az adatok megőrzése mellett
1. Az eszköz inaktiválásához. A kötet tárolók és az eszköz a pillanatképek megmaradnak.
   
   1. A StorSimple Manager szolgáltatás **eszközök** lapon, válassza ki az eszköz inaktiválása, és a lap alján kattintson a kívánt **Deactivate**.
   2. Ekkor megjelenik egy megerősítő üzenet. Kattintson a **Igen** folytatja. Az inaktiválás folyamat elindítja és néhány percet igénybe vehet.
2. Most átveheti a kötet-tárolók és a társított pillanatképet. Az eljárások, nyissa meg a [a StorSimple eszköz feladatátvétel és a katasztrófa utáni helyreállítás](storsimple-device-failover-disaster-recovery.md).
3. Az inaktiválást és a feladatátvétel után teljesen törölheti az eszközön. Egy eszköz törlése eltávolítja a listán szereplő eszközök kapcsolódik a szolgáltatáshoz. A szolgáltatás már nem kezelhetők a törölt eszköz. Hajtsa végre az alábbi lépéseket, hogy törli az eszközt:
   
   1. A StorSimple Manager szolgáltatás **eszközök** lapon, válassza ki a törölni kívánt inaktív eszközök.
   2. Az oldalon alján kattintson **törlése**.
   3. Megerősítést kér bekéri. Kattintson a **Igen** folytatja.
      
      Törli az eszköz néhány percig is eltarthat.

## <a name="deactivate-and-delete-a-virtual-device"></a>A virtuális eszköz inaktiváljon
A StorSimple virtuális eszköz inaktiválása felszabadítja a a virtuális gép. Törölheti a virtuális gép és a kiépítésekor létrejött erőforrásokat. A virtuális eszközt az inaktiválás után már nem lehet a korábbi állapotára visszaállítani. 

Az inaktiválást eredményezi, a következő műveleteket:

* A StorSimple virtuális eszközt a rendszer eltávolítja.
* Eltávolítja az OSDisk és az adatlemezek a StorSimple virtuális eszköz létrehozása.
* Az üzemeltetett szolgáltatás és a kiépítés során létrehozott virtuális hálózat megmarad. Ha nem használ ezeket az entitásokat, akkor manuálisan kell törölnie őket.
* A StorSimple virtuális eszköz által létrehozott felhőalapú pillanatfelvételek megmaradnak.

## <a name="next-steps"></a>Következő lépések
* A deaktivált eszköz visszaállítása a gyári beállításokat, nyissa meg a [visszaállítani az eszközt a gyári alapértelmezett beállításokra](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Technikai támogatásért [forduljon a Microsoft Support](storsimple-contact-microsoft-support.md).
* A StorSimple Manager szolgáltatással kapcsolatos további tudnivalókért keresse fel [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md). 

