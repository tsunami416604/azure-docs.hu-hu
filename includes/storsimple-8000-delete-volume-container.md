---
title: fájl belefoglalása
description: fájl belefoglalása
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 06/08/2018
ms.author: alkohli
ms.custom: include file
ms.openlocfilehash: e683d17422321b780a1c01b3011292f2e2c631cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179166"
---
Kötettároló törléséhez
 - kötetek törlése a kötettárolóban. Ha a kötettárolóhoz társított kötetek vannak, először ezeket a köteteket állítsa offline állapotba. Kövesse a [Kötet offline állapotba helyezése](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline)című rész lépéseit. Miután a kötetek offline állapotba kerültek, törölheti őket. 
 - a társított biztonsági mentési szabályzatok és a felhőbeli pillanatképek törlése. Ellenőrizze, hogy a kötettároló rendelkezik-e társított biztonsági mentési szabályzatokkal és felhőbeli pillanatképekkel. Ha igen, [törölje a biztonsági mentési házirendeket.](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy) Ez is törli a felhőbeli pillanatképek. 
 
Ha a kötettároló nem rendelkezik társított kötetekkel, biztonsági mentési szabályzatokkal és felhőbeli pillanatképekkel, törölheti azt. Kötettároló törléséhez hajtsa végre az alábbi eljárást.

#### <a name="to-delete-a-volume-container"></a>Kötettároló törlése
1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Jelölje ki az eszközt, kattintson az eszközre, majd lépjen a **Beállítások > > kötettárolók kezelése**elemre.

    ![Térfogattárolók penge](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. A kötettárolók táblázatos listájából válassza ki a törölni kívánt kötettárolót, kattintson a jobb gombbal **...** és válassza a **Törlés parancsot.**

    ![Kötettároló törlése](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Ha egy kötettároló nem rendelkezik társított kötetekkel, biztonsági mentési szabályzatokkal és felhőbeli pillanatképekkel, akkor törölhető. Amikor megerősítést kér, tekintse át és jelölje be a kötettároló törlésének hatását tartalmazó jelölőnégyzetet. Kattintson a **Törlés** gombra a kötettároló törléséhez.

    ![Törlés megerősítése](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

A kötettárolók listája frissül, hogy tükrözze a törölt kötettárolót.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


