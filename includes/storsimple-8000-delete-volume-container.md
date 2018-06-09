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
ms.openlocfilehash: e7f3f80c886f90a8bc3ae8c38e7d101c506439a6
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35250222"
---
A kötettároló törléséhez kell
 - Törölje a kötettároló a köteteket. A kötettároló kötetek vannak társítva, ha ezek a kötetek offline állapotba először. Kövesse a [kötet offline állapotba](../articles/storsimple/storsimple-8000-manage-volumes-u2.md#take-a-volume-offline). Miután a kötetek offline módban, törölhetik azokat. 
 - törli a kapcsolódó biztonsági mentési házirendet, és felhőalapú pillanatfelvételek. Ellenőrizze, hogy ha a kötettároló a biztonsági mentési házirendek és felhőbeli pillanatképeket vannak-e társítva. Ha igen, majd [törölje a biztonsági mentési házirendek](../articles/storsimple/storsimple-8000-manage-backup-policies-u2.md#delete-a-backup-policy). A felhőalapú pillanatfelvételek törlődni fog. 
 
Amikor a kötettárolóhoz nincs társított kötetek, a biztonsági mentési házirendek és a felhőalapú pillanatfelvételek, törölheti azt. A kötettároló törli az alábbi eljárás végrehajtásához.

#### <a name="to-delete-a-volume-container"></a>A kötettároló törlése
1. A StorSimple-eszközkezelő szolgáltatásban kattintson az **Eszközök** elemre. Válassza ki, és kattintson az eszközre, és folytassa a **beállítások > kezelés > kötettárolók**.

    ![Kötet tárolók panel](./media/storsimple-8000-create-volume-container/createvolumecontainer2.png)

2. Kötettárolók táblázatos listában jelölje ki szeretne törölni, kattintson a jobb gombbal a kötettároló **...**  majd **törlése**.

    ![Kötettároló törlése](./media/storsimple-8000-delete-volume-container/deletevolumecontainer1.png)

3. Ha a kötettároló nincs társított kötetek, a biztonsági mentési házirendek, és felhőbeli pillanatképeket, majd törölheti. Amikor felszólítja a megerősítésre, tekintse át, és jelölje be a jelölőnégyzetet, amely meghatározza, hogy a törlés, a kötettároló hatását. Kattintson a **törlése** majd törölni a kötet tárolót.

    ![Törlés megerősítése](./media/storsimple-8000-delete-volume-container/deletevolumecontainer2.png)

A kötet tárolók listája a törölt kötettároló megfelelően frissül.

![](./media/storsimple-8000-delete-volume-container/deletevolumecontainer5.png)


