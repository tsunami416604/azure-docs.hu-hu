---
title: Hozzáférés-vezérlési rekordok kezelése a StorSimple Virtual Array |} A Microsoft Docs
description: Ismerteti, hogyan lehet megállapítani a gazdagépek csatlakozhat a StorSimple Virtual Array egy köteten hozzáférés-vezérlési rekordok (ACR-EK) kezelése.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38718886"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>Hozzáférés-vezérlési rekordok kezelése a StorSimple Virtual Array StorSimple Eszközkezelő

## <a name="overview"></a>Áttekintés

Hozzáférés-vezérlési rekordok (ACR-EK) adja meg azt a kötetet a StorSimple Virtual Array (más néven a StorSimple helyszíni virtuális eszköz) a gazdagépek kapcsolódhatnak teszi lehetővé. ACR-EK vannak beállítva, hogy egy adott kötet, és az iSCSI minősített nevét (IQN-nevekre vonatkozóan) a gazdagépeket tartalmaz. Egy gazdagép egy kötetre csatlakozni próbál, ha az eszköz ellenőrzi, az ACR-REL társított az IQN-név a kötetet, és ha egyezést, majd a kapcsolat létrejött. A **hozzáférés-vezérlési rekordok** paneljén a **konfigurációs** a Device Manager szolgáltatás szakaszában minden hozzáférés-vezérlési bejegyzés a megfelelő IQN-nevekre vonatkozóan a gazdagépek a jeleníti meg.

![Hozzáférés-vezérlési rekordok kezelése](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

Ez az oktatóanyag a következő gyakori ACR kapcsolatos feladatokat ismerteti:

* IQN Nevének lekérése
* Adjon hozzá egy hozzáférés-vezérlési rekord
* Egy hozzáférés-vezérlési rekord szerkesztése
* Egy hozzáférés-vezérlési rekord törlése

> [!IMPORTANT]
> 
> * Az ACR hozzárendelése egy kötetet, ha körültekintően, hogy a kötet sem egyidejűleg elérhető egynél több nem fürtözött gazdagépen, mert ez a kötet, megsérülhet.
> * Egy ACR-t egy kötet törlésekor győződjön meg arról, hogy a megfelelő gazdagép nem fér hozzá a kötetet, mert a törlés egy írható-olvasható megszakítás eredményezheti.


## <a name="get-the-iqn"></a>IQN Nevének lekérése

A következő lépésekkel Windows Server 2012 rendszert futtató Windows-gazdagép IQN Nevének lekérése.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>Adjon hozzá egy ACR-t

Használhat **hozzáférés-vezérlési rekordok** paneljén a **konfigurációs** ACR-EK hozzáadása a StorSimple-Eszközkezelő szolgáltatás szakaszában. Általában egy ACR társítani egy kötetet.

Információ egy ACR-t társít egy kötetet, nyissa meg [kötet hozzáadása](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

> [!IMPORTANT]
> Az ACR hozzárendelése egy kötetet, ha körültekintően, hogy a kötet sem egyidejűleg elérhető egynél több nem fürtözött gazdagépen, mert ez a kötet, megsérülhet.


A következő lépésekkel adja hozzá egy ACR-t.

#### <a name="to-add-an-acr"></a>Az ACR hozzáadása

1. Szolgáltatás kezdőlapján válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevét, és ezután belül a **konfigurációs** területén kattintson **hozzáférés-vezérlési rekordok**.
2. Az a **hozzáférés-vezérlési rekordok** panelen kattintson a **Hozzáadás**.
3. Az a **ACR hozzáadása** panelen tegye a következőket:
   
    1. Adja meg az ACR **nevét**.
    
    2. A **iSCSI-kezdeményező neve**, adja meg a Windows-állomás IQN-név. A Windows Server-gazdagép IQN Nevének lekérése, tegye a következőket:
   
    3. Indítsa el a Microsoft iSCSI-kezdeményezőt a Windows-gazdagépen. Az iSCSI-kezdeményező tulajdonságai ablakban a a **konfigurációs** lapra, válassza ki, másolja a karakterláncot, a **kezdeményező neve** mező.
    Illessze be ezt a karakterláncot a **IQN** mezőbe a **ACR hozzáadása** panelen.
   
    6. Kattintson a **Hozzáadás** az ACR hozzáadása.  
   
        ![Hozzáférés-vezérlési rekordok hozzáadása](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. Táblázatos végeredménynek megfelelően frissül.

## <a name="edit-an-acr"></a>Az ACR szerkesztése

Használja a **hozzáférés-vezérlési rekordok** paneljén a **konfigurációs** szakaszban szerkesztheti az ACR-EK az Azure Portalon a Device Manager szolgáltatás.

> [!NOTE]
> Ne módosítsa egy ACR-t, amely jelenleg használatban van. Egy olyan kötetre, amely jelenleg használatban van társítva ACR szerkesztéséhez, érdemes először állítsa offline.


Hajtsa végre az alábbi lépések végrehajtásával szerkesztheti egy ACR-t.

#### <a name="to-edit-an-acr"></a>Az ACR szerkesztése

1. Szolgáltatás kezdőlapján válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevét, és ezután belül a **konfigurációs** szakaszban **hozzáférés-vezérlési rekordok**.
2. Az a **hozzáférés-vezérlési rekordok** panelen, a hozzáférés-vezérlési rekordok táblázatos listájából, kattintson duplán az ACR-REL, amelyet módosítani szeretne.
3. Az a **szerkesztési hozzáférés-vezérlési rekordok** panelen tegye a következőket:
   
    1. Adja meg az ACR-REL az IQN-t.
   
    2. Kattintson a **mentése** menteni a módosított ACR a panel tetején. A következő megerősítő üzenetet látja:
   
        ![Hozzáférés-vezérlési rekordok szerkesztése](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Egy hozzáférés-vezérlési rekord törlése

Használja a **konfigurációs** lap ACR-EK törlése az Azure Portalon.

> [!NOTE]
> 
> * Ne töröljön egy ACR-t, amely jelenleg használatban van. Töröl egy olyan kötetre, amely jelenleg használatban van társítva, hogy be kell először állítsa offline.
> * Egy ACR-t egy kötet törlésekor győződjön meg arról, hogy a megfelelő gazdagép nem fér hozzá a kötetet, mert a törlés egy írható-olvasható megszakítás eredményezheti.


Hajtsa végre az alábbi lépéseket egy hozzáférés-vezérlési rekord törlése.

#### <a name="to-delete-an-access-control-record"></a>Egy hozzáférés-vezérlési rekord törlése

1. Szolgáltatás kezdőlapján válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevét, és ezután belül a **konfigurációs** szakaszban **hozzáférés-vezérlési rekordok**.

2. Az a **hozzáférés-vezérlési rekordok** panelen, a hozzáférés-vezérlési rekordok táblázatos listájából, kattintson duplán az ACR-REL, amelyet törölni kíván.

3. Szerkesztés access control rekordok panelen kattintson **törlése**.
   
    ![ACR-EK törlése](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Amikor a rendszer megerősítést kér, kattintson a **törlése** a törlés folytatásához. A törlés táblázatos frissül.
   
   ![Figyelmeztető üzenet](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>További lépések

* Tudjon meg többet [kötetek hozzáadása és konfigurálása az ACR-EK](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume).

