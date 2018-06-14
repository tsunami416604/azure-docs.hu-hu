---
title: A StorSimple virtuális tömb hozzáférés-vezérlési rekordokat kezelése |} Microsoft Docs
description: Annak meghatározásához, hogy mely gazdagépek csatlakozhat a StorSimple virtuális tömb kötetein hozzáférés-vezérlési rekordokat (ACRs) kezelését ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: e154bb4f-faab-4d92-a593-900c3ddc9595
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2ce65aa4efba735305208f7a6d761bc2814d1b8f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875941"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Hozzáférés-vezérlési rekordokat kezelheti a StorSimple virtuális tömb StorSimple Eszközkezelő

## <a name="overview"></a>Áttekintés

Hozzáférés-vezérlési rekordokat (ACRs) lehetővé teszik annak megadását, hogy mely gazdagépek csatlakozhat a StorSimple virtuális tömb (más néven a StorSimple helyszíni virtuális eszköz) a kötet. ACRs vannak beállítva, hogy egy adott kötet, és az iSCSI minősített nevét (IQN-nevekre vonatkozóan), a gazdagépek tartalmaz. Állomás megpróbál egy kötet csatlakozni, amikor az eszköz ellenőrzi, hogy a köteten a IQN-nevének társított ACR, és ha van egyezés, majd a kapcsolat létrejön. A **hozzáférés-vezérlési rekordokat** panel belül a **konfigurációs** szakasz az Eszközkezelő szolgáltatás access control rekordok a megfelelő IQN-nevekre vonatkozóan a gazdagépek jeleníti meg.

![Hozzáférés-vezérlési rekordokat kezelése](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

Ez az oktatóanyag azt ismerteti, hogy a következő általános ACR kapcsolatos feladatok:

* IQN-Nevének lekérése
* Egy hozzáférés-vezérlési rekordot hozzáadása
* Egy hozzáférés-vezérlési rekordot szerkesztése
* Egy hozzáférés-vezérlési rekordot törlése

> [!IMPORTANT]
> 
> * Amikor egy ACR rendel egy olyan kötetre, gondoskodunk, hogy a kötet nem egyidejűleg hozzáfér egynél több nem fürtözött gazdagépen, mert ez a kötet, megsérülhet.
> * Ha töröl egy ACR a kötetről, győződjön meg arról, hogy a megfelelő gazdagép nem fér hozzá a kötetet, mert a Törlés a írható-olvasható szüneteltetése eredményezheti.


## <a name="get-the-iqn"></a>IQN-Nevének lekérése

A következő lépésekkel egy Windows Server 2012 rendszert futtató Windows-állomás IQN-Nevének lekérése.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Egy ACR hozzáadása

Használhat **hozzáférés-vezérlési rekordokat** panel belül a **konfigurációs** ACRs hozzáadása a StorSimple Device Manager szolgáltatás szakasza. Általában egy ACR hozzárendel egy kötetet.

Egy mennyiségi egy ACR társításával kapcsolatos információkért nyissa meg a [kötet hozzáadása](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Amikor egy ACR rendel egy olyan kötetre, gondoskodunk, hogy a kötet nem egyidejűleg hozzáfér egynél több nem fürtözött gazdagépen, mert ez a kötet, megsérülhet.


A következő lépésekkel adja hozzá az ACR.

#### <a name="to-add-an-acr"></a>Egy ACR hozzáadása

1. A szolgáltatás követően lapon válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, majd belül a **konfigurációs** területen kattintson **hozzáférés-vezérlési rekordokat**.
2. Az a **hozzáférés-vezérlési rekordokat** panelen kattintson a **Hozzáadás**.
3. Az a **hozzáadása ACR** panelen tegye a következőket:
   
    1. Adja meg az ACR **nevét**.
    
    2. A **iSCSI-kezdeményező neve**, adja meg a Windows-állomás IQN nevét. A Windows Server-állomás IQN-Nevének lekérése, tegye a következőket:
   
    3. Indítsa el a Microsoft iSCSI-kezdeményezőt a Windows-gazdagépen. ISCSI-kezdeményező tulajdonságai ablakban a a **konfigurációs** lapra, válassza ki, másolja a karakterláncot, a **kezdeményező neve** mező.
    Illessze be a karakterláncot a a **IQN** mező mellett a **hozzáadása ACR** panelen.
   
    6. Kattintson a **Hozzáadás** a ACR hozzáadni.  
   
        ![Hozzáférés-vezérlési rekordokat hozzáadása](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. A táblázatos felsorolása a hozzáadást megfelelően frissül.

## <a name="edit-an-acr"></a>Egy ACR szerkesztése

Használja a **hozzáférés-vezérlési rekordokat** panel belül a **konfigurációs** szakasz az Eszközkezelő szolgáltatás ACRs szerkesztése az Azure portálon.

> [!NOTE]
> Ne módosítsa az ACR, amely jelenleg használatban van. Egy olyan kötet, amely jelenleg használatban van társított ACR szerkesztéséhez először hajtson a kötet offline állapotba.


A következő lépésekkel egy ACR szerkesztése.

#### <a name="to-edit-an-acr"></a>Egy ACR szerkesztése

1. Szolgáltatás kezdőlapja, jelölje ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, majd belül a **konfigurációs** szakaszban **hozzáférés-vezérlési rekordokat**.
2. Az a **hozzáférés-vezérlési rekordokat** panelen, a hozzáférés-vezérlési rekordokat táblázatos listájában kattintson duplán a módosítani kívánt ACR.
3. Az a **Szerkesztés hozzáférés-vezérlési rekordokat** panelen tegye a következőket:
   
    1. A ACR IQN-Nevének megadnia.
   
    2. Kattintson a **mentése** menteni a módosított ACR a panel tetején. A következő megerősítő üzenetet látja:
   
        ![Hozzáférés-vezérlési rekordokat szerkesztése](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Egy hozzáférés-vezérlési rekordot törlése

Használja a **konfigurációs** lap ACRs törlése az Azure portálon.

> [!NOTE]
> 
> * Ne törölje az ACR, amely jelenleg használatban van. Egy olyan kötet, amely jelenleg használatban van társított ACR törléséhez először hajtson a kötet offline állapotba.
> * Ha töröl egy ACR a kötetről, győződjön meg arról, hogy a megfelelő gazdagép nem fér hozzá a kötetet, mert a Törlés a írható-olvasható szüneteltetése eredményezheti.


A következő lépésekkel egy hozzáférés-vezérlési rekordot törlése.

#### <a name="to-delete-an-access-control-record"></a>Egy hozzáférés-vezérlési rekordot törlése

1. Szolgáltatás kezdőlapja, jelölje ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, majd belül a **konfigurációs** szakaszban **hozzáférés-vezérlési rekordokat**.

2. Az a **hozzáférés-vezérlési rekordokat** panelen, a hozzáférés-vezérlési rekordokat táblázatos listájában kattintson duplán a törölni kívánt ACR.

3. A Szerkesztés hozzáférést vezérlő rekordok paneljén kattintson **törlése**.
   
    ![ACRS törlése](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Amikor felszólítja a megerősítésre, kattintson **törlése** a törlés folytatásához. A táblázatos felsorolása a törlés frissül.
   
   ![Figyelmeztető üzenet](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>Következő lépések

* További információ [kötetek hozzáadásával és konfigurálásával ACRs](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

