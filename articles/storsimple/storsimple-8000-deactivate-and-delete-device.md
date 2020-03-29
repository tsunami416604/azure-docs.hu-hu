---
title: StorSimple 8000 sorozatú eszköz kikapcsolása és törlése | Microsoft dokumentumok
description: A StorSimple-eszköz eltávolítását ismerteti, ha először inaktiválja, majd törli azt.
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
ms.date: 07/23/2018
ms.author: alkohli
ms.openlocfilehash: 116ac5c4efda87b5d16336dd326d516299f6955d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61481939"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>StorSimple-eszköz inaktiválása és törlése

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti, hogyan inaktiválhatja és törölheti a StorSimple-eszköz, amely csatlakozik egy StorSimple Eszközkezelő szolgáltatás. Az ebben a cikkben szereplő útmutatás csak a StorSimple 8000 sorozatú eszközökre vonatkozik, beleértve a StorSimple felhőalapú készülékeket is. Ha StorSimple virtuális tömböt használ, akkor lépjen [az Inaktiválás és a StorSimple virtuális tömb törlése](storsimple-virtual-array-deactivate-and-delete-device.md)elemre.

Az inaktiválás megszakítja az eszköz és a megfelelő StorSimple Eszközkezelő szolgáltatás közötti kapcsolatot. Előfordulhat, hogy egy StorSimple eszközt ki szeretne használni (például ha kicseréli vagy frissíti az eszközt, vagy ha már nem használja a StorSimple-t). Ha igen, a törlés előtt inaktiválnia kell az eszközt.

Az eszköz inaktiválásakor az eszközön helyileg tárolt adatok már nem érhetők el. Csak a felhőben tárolt eszközhöz társított adatok helyrehozhatók.

> [!WARNING]
> Az inaktiválás állandó művelet, és nem lehet visszavonni. Az inaktivált eszköz csak akkor regisztrálható a StorSimple Eszközkezelő szolgáltatásban, ha visszaáll a gyári alapértékekre.
>
> A gyári beállítások visszaállítási folyamata törli az eszközön helyileg tárolt összes adatot. Ezért az eszköz inaktiválása előtt el kell készítenie az összes adat felhőbeli pillanatképét. Ez a felhőbeli pillanatkép lehetővé teszi az összes adat helyreállítását egy későbbi szakaszban.

Elolvasása után ez a bemutató, akkor képes lesz arra, hogy:

* Inaktiváljon egy eszközt, és törölje az adatokat.
* Inaktiváljon egy eszközt, és őrizze meg az adatokat.

> [!NOTE]
> A StorSimple fizikai eszköz vagy felhőalapú eszköz inaktiválása előtt állítsa le vagy törölje az adott eszköztől függő ügyfeleket és gazdagépeket.


## <a name="deactivate-and-delete-data"></a>Adatok inaktiválása és törlése

Ha teljesen szeretné leplezni az eszközt, és nem szeretné megőrizni az adatokat az eszközön, akkor hajtsa végre a következő lépéseket.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Az eszköz inaktiválásához és az adatok

1. Az eszköz inaktiválása előtt törölnie kell az eszközhöz társított összes kötettárolót (és kötetet). Kötettárolók csak a társított biztonsági mentések törlése után törölhető.

    > [!NOTE]
    > A StorSimple fizikai eszköz vagy felhőalapú eszköz inaktiválása előtt győződjön meg arról, hogy a törölt kötettárolóból származó adatok ténylegesen törlődnek az eszközről. Figyelemmel kísérheti a felhőfelhasználási diagramokat, és amikor a törölt biztonsági mentések miatt a felhőhasználat csökken, folytathatja az eszköz inaktiválását. Ha inaktiválja az eszközt, mielőtt ez a csökkenés bekövetkezne, az adatok a tárfiókban vesztegelnek, és terheléseket halmoznak fel.

2. Kapcsolja ki az eszközt az alábbiak szerint:
   
   1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az **Eszközök** panelen jelölje ki az inaktiválni kívánt eszközt, kattintson a jobb gombbal, majd kattintson **az Inaktiválás parancsra.**

        ![StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Az **Inaktiválás** panelen írja be a megerősítendő eszköznevét, majd kattintson **az Inaktiválás gombra.** A folyamat inaktiválása és néhány percet vesz igénybe.

        ![StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Az inaktiválás után teljesen törölheti az eszközt. Az eszköz törlésével eltávolítja azt a szolgáltatáshoz csatlakoztatott eszközök listájáról. A szolgáltatás ezután már nem tudja kezelni a törölt eszközt. Az eszköz törléséhez kövesse az alábbi lépéseket:
   
   1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az **Eszközök** panelen jelölje ki a törölni kívánt inaktivált eszközt, kattintson a jobb gombbal, majd kattintson a **Törlés parancsra.**

        ![StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. A **Törlés** panelen írja be a megerősítendő eszköznevét, majd kattintson a **Törlés gombra.** A törlés néhány percet vesz igénybe.

        ![StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. A törlés sikeres befejezése után értesítést kap. Az eszközlista is frissül, hogy tükrözze a törlést.

## <a name="deactivate-and-retain-data"></a>Adatok inaktiválása és megőrzése

Ha szeretné kitölteni az eszközt, de meg szeretné őrizni az adatokat, hajtsa végre az alábbi lépéseket:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Eszköz inaktiválása és az adatok megőrzése
1. Kapcsolja ki az eszközt. Az eszköz összes kötettárolója és pillanatképe megmarad.
   
   1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az **Eszközök** panelen jelölje ki az inaktiválni kívánt eszközt, kattintson a jobb gombbal, majd kattintson **az Inaktiválás parancsra.**

         ![StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Az **Inaktiválás** panelen írja be a megerősítendő eszköznevét, majd kattintson **az Inaktiválás gombra.** A folyamat inaktiválása és néhány percet vesz igénybe.

         ![StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Most már feladatátvételt a kötettárolók és a kapcsolódó pillanatképek. Az eljárások, megy [feladatátvételi és vész-helyreállítási a StorSimple-eszköz.](storsimple-8000-device-failover-disaster-recovery.md)
3. Az inaktiválás és a feladatátvétel után teljesen törölheti az eszközt. Az eszköz törlésével eltávolítja azt a szolgáltatáshoz csatlakoztatott eszközök listájáról. A szolgáltatás ezután már nem tudja kezelni a törölt eszközt. Az eszköz törléséhez hajtsa végre az alábbi lépéseket:
   
   1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az **Eszközök** panelen jelölje ki a törölni kívánt inaktivált eszközt, kattintson a jobb gombbal, majd kattintson a **Törlés parancsra.**

       ![StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. A **Törlés** panelen írja be a megerősítendő eszköznevét, majd kattintson a **Törlés gombra.** A törlés néhány percet vesz igénybe.

       ![StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. A törlés sikeres befejezése után értesítést kap. Az eszközlista is frissül, hogy tükrözze a törlést.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Felhőalapú készülék inaktiválása és törlése

A StorSimple felhőalapú berendezés, a portálin való inaktiválása felszabadítja és törli a virtuális gépet, és az erőforrások at a kiépítéskor létrehozott. A felhőalapú berendezést az inaktiválás után már nem lehet a korábbi állapotára visszaállítani.

![StorSimple felhőalapú berendezés inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Az inaktiválás a következő műveleteket eredményezi:

* A StorSimple felhőberendezés törlődik a szolgáltatásból.
* A StorSimple felhőalapú berendezés virtuális gépe törlődik.
* A StorSimple cloud Appliance számára létrehozott operációsrendszer-lemez és adatlemezek megmaradnak. Ha nem használja ezeket az entitásokat, törölje őket manuálisan.
* A kiépítés során létrehozott üzemeltetett szolgáltatás és virtuális hálózat megmarad. Ha nem használja ezeket az entitásokat, törölje őket manuálisan.
* A StorSimple cloud Appliance által létrehozott felhőbeli pillanatképek megmaradnak.

A felhőalapú készülék inaktiválása után törölheti azt az eszközök listájából. Jelölje ki az inaktivált eszközt, kattintson a jobb gombbal, majd kattintson a **Törlés parancsra.** A StorSimple értesíti, ha az eszköz törlődik, és az eszközök frissítései.

## <a name="next-steps"></a>További lépések

* Az inaktivált eszköz gyári alapértékekre való visszaállításához nyissa [meg az Eszköz visszaállítása gyári alapértelmezett beállításokra című lehetőséget.](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings)
* Technikai segítségért [forduljon a Microsoft támogatási szolgálatához.](storsimple-8000-contact-microsoft-support.md)
* Ha többet szeretne megtudni a StorSimple Eszközkezelő szolgáltatás használatáról, olvassa el [a StorSimple Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez című webhelyet.](storsimple-8000-manager-service-administration.md)

