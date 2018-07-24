---
title: Egy StorSimple 8000 sorozatú eszköz inaktiválása és törlése |} A Microsoft Docs
description: Ismerteti a StorSimple-eszköz eltávolítása a szolgáltatás inaktiválása először, és majd törlésével.
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
ms.openlocfilehash: a2b764e76cd1987c83e7be38d365c1dfa8513db9
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214788"
---
# <a name="deactivate-and-delete-a-storsimple-device"></a>A StorSimple eszköz inaktiválása és törlése

## <a name="overview"></a>Áttekintés

Ez a cikk ismerteti, amely a StorSimple-Eszközkezelő szolgáltatás csatlakozik a StorSimple eszköz inaktiválása és törlése. Ez a cikk az útmutató csak a StorSimple 8000 sorozat eszközeire, beleértve a StorSimple Cloud Appliance vonatkozik. Ha a StorSimple Virtual Array használ, folytassa a [inaktiválását vagy törlését a StorSimple Virtual Array](storsimple-virtual-array-deactivate-and-delete-device.md).

Inaktiválási adatbázisai, az eszköz és a megfelelő StorSimple-Eszközkezelő szolgáltatás közötti kapcsolat. Előfordulhat, hogy szeretné nem működik a StorSimple eszköz (például akkor, ha az eszköz frissítése vagy cseréje, vagy ha már nem használt StorSimple). Ha igen, meg kell inaktiválhatja az eszközt, mielőtt törölheti azt.

Ha az eszköz inaktiválása, a helyileg az eszközön tárolt adatok többé nem érhető el. Csak a társított az eszközt, hogy a felhőben tárolt adatok helyreállíthatók legyenek.

> [!WARNING]
> Inaktiválási állandó művelet, és nem vonható vissza. Inaktivált eszköz nem lehet regisztrálni a StorSimple-Eszközkezelő szolgáltatással, kivéve, ha a gyári alaphelyzetbe áll.
>
> A gyári beállítások visszaállítása a folyamat törlések helyileg az eszközön tárolt összes adatot. Ezért kell végeznie egy felhőbeli pillanatképet az összes eszköz inaktiválása előtt. A felhőbeli pillanatképet minden az adatok helyreállítása egy későbbi szakaszban teszi lehetővé.

Ebben az oktatóanyagban elolvasásával fogja tudni:

* Eszköz inaktiválása és törölheti az adatokat.
* Eszköz inaktiválása és megőrizni az adatokat.

> [!NOTE]
> A StorSimple fizikai eszköz vagy a felhőalapú berendezés inaktiválása előtt le, vagy törli az ügyfelek és a gazdagépek, amelyek attól függnek, hogy az eszközről.


## <a name="deactivate-and-delete-data"></a>Inaktiválás és törlés adatok

Ha érdekli az eszköz teljes törlése, és nem szeretné megőrizni az adatokat az eszközön, majd hajtsa végre az alábbi lépéseket.

#### <a name="to-deactivate-the-device-and-delete-the-data"></a>Az eszköz inaktiválása és az adatok törlése

1. Mielőtt az eszköz inaktiválása, törölnie kell a összes kötet az eszközhöz társított tárolókat (és a köteteket). Kötettárolók csak az azokhoz kapcsolódó biztonsági mentési törlését követően törölheti.

    > [!NOTE]
    > A StorSimple fizikai eszköz vagy a felhőalapú berendezés inaktiválása előtt győződjön meg arról, hogy ténylegesen a törölt kötettároló az adatok törlődnek az eszközről. A felhő felhasználási diagramok nyomon követheti, és amikor megjelenik a felhőhasználat, dobja el a törölt biztonsági másolatok miatt, majd folytassa az eszköz inaktiválása. Az eszköz inaktiválása előtt legördülő akkor fordul elő, ha az adatok a storage-fiókban stranded van, és díjat fizetni.

2. Az eszköz inaktiválása a következőképpen:
   
   1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az a **eszközök** panelen válassza ki az eszközt, akinél szeretné inaktiválni, kattintson a jobb gombbal, és kattintson **inaktiválás**.

        ![A StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Az a **inaktiválás** panelről, írja be az eszköz nevét annak ellenőrzéséhez, és kattintson **inaktiválás**. Az inaktiválás folyamat elindul, és néhány percet vesz igénybe.

        ![A StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)

3. Inaktiválási, után törölheti az eszköz teljes mértékben. Eszköz törlése eltávolítja a szolgáltatáshoz csatlakozó eszközök listájából. A szolgáltatás már nem kezelhetők a törölt eszközt. Használja az alábbi lépéseket az eszköz törléséhez:
   
   1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az a **eszközök** panelen válassza ki az inaktív eszközt, amelyet szeretne törölni, kattintson a jobb gombbal, és kattintson **törlése**.

        ![A StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Az a **törlése** panelről, írja be az eszköz nevét annak ellenőrzéséhez, és kattintson **törlése**. A Törlés befejezése néhány percet vesz igénybe.

        ![A StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. A törlés sikeres befejezése után értesítést kap. Az eszközlista is frissíti a törlés.

## <a name="deactivate-and-retain-data"></a>Inaktiválás és adatainak megőrzése

Ha érdekli az eszköz törlése, de szeretné megőrizni az adatokat, majd hajtsa végre az alábbi lépéseket:

#### <a name="to-deactivate-a-device-and-retain-the-data"></a>Az adatok megőrzése és az eszköz inaktiválása
1. Az eszköz inaktiválása. A kötettárolók és az eszköz a pillanatképek marad.
   
   1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az a **eszközök** panelen válassza ki az eszközt, akinél szeretné inaktiválni, kattintson a jobb gombbal, és kattintson **inaktiválás**.

         ![A StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate1.png)
   2. Az a **inaktiválás** panelről, írja be az eszköz nevét annak ellenőrzéséhez, és kattintson **inaktiválás**. Az inaktiválás folyamat elindul, és néhány percet vesz igénybe.

         ![A StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate2.png)
2. Most már adhatók át a kötettárolókat, és a társított pillanatképet. Az eljárások ismertetéséért Ugrás [feladatátvétel és vészhelyreállítás a StorSimple-eszköz helyreállítását](storsimple-8000-device-failover-disaster-recovery.md).
3. Inaktiválás és a feladatátvétel után törölheti az eszköz teljes mértékben. Eszköz törlése eltávolítja a szolgáltatáshoz csatlakozó eszközök listájából. A szolgáltatás már nem kezelhetők a törölt eszközt. Az eszköz törléséhez hajtsa végre az alábbi lépéseket:
   
   1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Az a **eszközök** panelen válassza ki az inaktív eszközt, amelyet szeretne törölni, kattintson a jobb gombbal, és kattintson **törlése**.

       ![A StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate5.png)
   2. Az a **törlése** panelről, írja be az eszköz nevét annak ellenőrzéséhez, és kattintson **törlése**. A Törlés befejezése néhány percet vesz igénybe.

       ![A StorSimple eszköz inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate6.png)
   3. A törlés sikeres befejezése után értesítést kap. Az eszközlista is frissíti a törlés.

     
## <a name="deactivate-and-delete-a-cloud-appliance"></a>Inaktiválás és törlés a felhőalapú berendezés

A StorSimple Cloud Appliance inaktiválása a portálról felszabadítja, és törli a virtuális gép és annak üzembe helyezésekor létrehozott erőforrásokat. A felhőalapú berendezést az inaktiválás után már nem lehet a korábbi állapotára visszaállítani.

![A StorSimple Cloud Appliance inaktiválása](./media/storsimple-8000-deactivate-and-delete-device/deactivate7.png)

Inaktiválási eredményeket a következő műveleteket:

* A StorSimple felhőalapú készülék távolítja el a szolgáltatást.
* A StorSimple felhőalapú készülék a virtuális gép törlődik.
* Operációsrendszer-lemez és a StorSimple felhőalapú berendezéshez létrehozott adatlemezek megmaradnak. Ha ezek az entitások nem használ, akkor manuálisan kell törölnie őket.
* Az üzemeltetett szolgáltatás és a kiépítés során létrehozott virtuális hálózat megmarad. Ha ezek az entitások nem használ, akkor manuálisan kell törölnie őket.
* A StorSimple felhőalapú berendezés által létrehozott felhőalapú pillanatfelvételek megmaradnak.

A felhőalapú berendezést az Inaktiválás után törölheti az eszközök a listából. Válassza ki az inaktivált eszköz, kattintson a jobb gombbal, majd kattintson **törlése**. A StorSimple értesíti, ha az eszköz törlését és az eszközök listája frissül.

## <a name="next-steps"></a>További lépések

* Az inaktív eszköz visszaállítása a gyári beállításokat, lépjen a [állítsa alaphelyzetbe az eszközt a gyári alapértelmezett beállításokra](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).
* Technikai támogatásért [forduljon a Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* A StorSimple-Eszközkezelő szolgáltatás használatával kapcsolatos további tudnivalókért keresse fel [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).

