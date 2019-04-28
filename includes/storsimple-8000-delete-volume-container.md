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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60724732"
---
A kötettároló törléséhez kell
 - Törölje a kötettárolót a kötetek. Ha a kötettárolóhoz társított kötetekkel rendelkezik, a köteteket offline állapotba először. Kövesse a [egy kötetet offline állapotba](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Miután a kötetek offline állapotban van, törölheti őket. 
 - társított biztonsági mentési szabályzatok törlése, és a felhőbeli pillanatképeket. Ellenőrizze, hogy ha a kötettárolóhoz társított biztonsági mentési szabályzatok és a felhőbeli pillanatképekkel rendelkezik. Ha igen, majd [a biztonsági mentési szabályzatok törlése](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). Ez a felhőbeli pillanatképeket is törli. 
 
Ha a kötettároló nem rendelkezik társított kötetek, biztonsági mentési házirendeket, és felhőbeli pillanatképeket, törölheti azt. A következő eljárással törölni a kötettárolót.

#### <a name="to-delete-a-volume-container"></a>A kötettároló törlése
1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Válassza ki, és válassza ki az eszközt, és folytassa a **beállítások > kezelés > kötettárolók**.

    ![Kötet tárolók panel](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Kötettárolók táblázatos listájából válassza ki a kötettárolót, törléséhez kattintson a jobb gombbal a kívánt **...**  majd **törlése**.

    ![Kötettároló törlése](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Ha a kötettároló nem társított kötetek, a biztonsági mentési házirendeket, és a felhőbeli pillanatképekkel, akkor lehet törölni. Amikor a rendszer megerősítést kér, tekintse át, és jelölje be a jelölőnégyzetet, amely megállapítja a kötettároló törlésének következményeit. Kattintson a **törlése** , majd törölni a kötettárolót.

    ![Törlés megerősítése](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

A kötettárolók listája a törölt kötettároló megfelelően frissül.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


