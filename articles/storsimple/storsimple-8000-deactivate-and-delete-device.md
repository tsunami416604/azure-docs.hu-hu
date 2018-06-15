---
title: A StorSimple 8000 series eszköz inaktiváljon |} Microsoft Docs
description: Ismerteti a StorSimple eszköz eltávolítása a szolgáltatás első inaktiválása és törlését is.
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
ms.date: 05/23/2017
ms.author: alkohli
ms.openlocfilehash: 3c00867a29cf8343a57e74e2aabe3971ae6837af
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874968"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>A StorSimple eszköz inaktiváljon

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti inaktiváljon a StorSimple eszköz, amely csatlakozik a StorSimple eszköz Manager szolgáltatást. Az útmutató csak a StorSimple 8000 sorozat eszközeire, beleértve a StorSimple felhő készülékek vonatkozik. Ha egy StorSimple virtuális tömb használ, majd folytassa a [inaktiválását vagy törlését a StorSimple virtuális tömb](storsimple-virtual-array-deactivate-and-delete-device.md).

Az inaktiválást az eszköz és a megfelelő StorSimple Device Manager szolgáltatás közötti kapcsolat kiszolgálója. Kezdésként érdemes lehet, hogy nem működik a StorSimple eszköz (például akkor, ha cseréje vagy az eszköz frissítése, vagy ha már nem használja a StorSimple). Ha igen, van szüksége az eszköz inaktiválása, mielőtt törölné.

Amikor az eszköz inaktiválása az eszközön helyileg tárolt adatokat már nem érhető el. Csak a felhőben tárolt az eszközzel kapcsolatos adatok állíthatók helyre.

> [!WARNING]
> Az inaktiválást állandó művelet, és nem vonható vissza. Deaktivált eszköz nem lehet regisztrálni a StorSimple eszköz Manager szolgáltatásban, kivéve, ha a gyári beállítások alaphelyzetbe áll.
>
> A gyári folyamat törli az eszközön helyileg tárolt adatok. Ezért kell pillanatképet egy felhőalapú, az adatok eszköz inaktiválása előtt. A felhő-pillanatfelvételt segítségével visszaállíthatja az adatokat egy későbbi időpontban.

Ez az oktatóanyag elolvasása, után fogja tudni:

* Eszköz inaktiválása, és törli az adatokat.
* Eszköz inaktiválása, és az adatok megőrzése mellett.

> [!NOTE]
> A StorSimple fizikai eszköz vagy a felhő készülék inaktiválása előtt állítsa le, vagy törli az ügyfelek és kiszolgálók azokon az eszközökön.


## <a name="deactivate-and-delete-data"></a>Inaktiválása és adatok törlése

Ha szeretné az eszköz teljes törlése, és nem szeretné, hogy az eszközön lévő adatok megőrzése mellett, majd hajtsa végre az alábbi lépéseket.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Az eszköz inaktiválásához és az adatok törlése

1. Egy eszköz inaktiválása előtt törölnie kell az összes kötet az eszközhöz társított tárolók (és a kötetek). Kötettárolók törölheti csak a társított biztonsági másolatok törlését követően.

    > [!NOTE]
    > A StorSimple fizikai eszköz vagy a felhő készülék inaktiválása előtt győződjön meg arról, hogy a törölt kötettároló adatait ténylegesen törlődik az eszközről. A felhőben felhasználási diagramok egyaránt képes figyelni, és amikor megjelenik a felhőalapú használata miatt a biztonsági mentések törölt dobja el, majd folytassa az eszköz inaktiválásához. Az eszköz inaktiválása előtt az vetett akkor fordul elő, ha az adatokat a tárfiókban lévő stranded van, és díjak fizetni.

2. Az eszköz az alábbiak szerint inaktiválása:
   
   1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az a **eszközök** panelen válassza ki az eszközt, amelyet meg kíván inaktiválásához kattintson a jobb gombbal, és kattintson **Deactivate**.

        ![A StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Az a **Deactivate** panel. típus az eszköz nevét annak ellenőrzéséhez, majd kattintson **Deactivate**. Az inaktiválás folyamat elindul, és néhány percet is igénybe vehet.

        ![A StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Az inaktiválást, után törölheti az eszköz teljesen. Egy eszköz törlése eltávolítja a listán szereplő eszközök kapcsolódik a szolgáltatáshoz. A szolgáltatás már nem kezelhetők a törölt eszköz. Az alábbi lépések segítségével az eszköz törlése:
   
   1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az a **eszközök** panelen válassza ki azt a deaktivált eszközt, amely törli, kattintson a jobb gombbal, és kattintson **törlése**.

        ![A StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Az a **törlése** panel. típus az eszköz nevét annak ellenőrzéséhez, majd kattintson **törlése**. A törlés néhány percet is igénybe vehet.

        ![A StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Miután a törlés sikeresen befejeződött, értesítés jelenik meg. Az eszközök listáját is frissíti a törlés.

## <a name="deactivate-and-retain-data"></a>Inaktiválása és adatainak megőrzése

Ha az eszköz törlése érdekelt, de szeretné megőrizni az adatokat, majd hajtsa végre az alábbi lépéseket:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Eszköz inaktiválása és az adatok megőrzése mellett
1. Az eszköz inaktiválásához. A kötet tárolók és az eszköz a pillanatképek maradnak.
   
   1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az a **eszközök** panelen válassza ki az eszközt, amelyet meg kíván inaktiválásához kattintson a jobb gombbal, és kattintson **Deactivate**.

         ![A StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Az a **Deactivate** panel. típus az eszköz nevét annak ellenőrzéséhez, majd kattintson **Deactivate**. Az inaktiválás folyamat elindul, és néhány percet is igénybe vehet.

         ![A StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Most átveheti a kötet-tárolók és a társított pillanatképet. Az eljárások, nyissa meg a [a StorSimple eszköz feladatátvétel és a katasztrófa utáni helyreállítás](storsimple-8000-device-failover-disaster-recovery.md).
3. Az inaktiválást és a feladatátvétel után teljesen törölheti az eszközön. Egy eszköz törlése eltávolítja a listán szereplő eszközök kapcsolódik a szolgáltatáshoz. A szolgáltatás már nem kezelhetők a törölt eszköz. Az eszköz törlése, kövesse az alábbi lépéseket:
   
   1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az a **eszközök** panelen válassza ki azt a deaktivált eszközt, amely törli, kattintson a jobb gombbal, és kattintson **törlése**.

       ![A StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Az a **törlése** panel. típus az eszköz nevét annak ellenőrzéséhez, majd kattintson **törlése**. A törlés néhány percet is igénybe vehet.

       ![A StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. Miután a törlés sikeresen befejeződött, értesítés jelenik meg. Az eszközök listáját is frissíti a törlés.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>A felhő készülék inaktiváljon

A StorSimple felhő alapplatformjaként a portálról az inaktiválást felszabadítja a, és törli a virtuális gép és a kiépítésekor létrejött erőforrásokat. A felhőalapú berendezést az inaktiválás után már nem lehet a korábbi állapotára visszaállítani.

![StorSimple felhő készülék inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Az inaktiválást eredményezi, a következő műveleteket:

* A StorSimple felhő készülék távolítja el a szolgáltatást.
* A StorSimple felhő készülék a virtuális gép törlődik.
* Operációsrendszer-lemez és adatlemezek létrehozni a StorSimple felhő alapplatformjaként törlődnek.
* Az üzemeltetett szolgáltatás és a kiépítés során létrehozott virtuális hálózat megmarad. Ha nem használ ezeket az entitásokat, akkor manuálisan kell törölnie őket.
* A StorSimple felhő készülék által létrehozott felhőalapú pillanatfelvételek megmaradnak.

A felhő készülék az Inaktiválás után törölheti az eszközök a listából. Válassza ki az inaktív eszközök, kattintson a jobb gombbal, majd **törlése**. StorSimple értesítést küld, akkor az eszköz a törölt és eszközök frissítések listája.

## <a name="next-steps"></a>Következő lépések

* A deaktivált eszköz visszaállítása a gyári beállításokat, nyissa meg a [visszaállítani az eszközt a gyári alapértelmezett beállításokra](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Technikai támogatásért [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* A StorSimple eszköz Manager szolgáltatással kapcsolatos további tudnivalókért keresse fel [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

