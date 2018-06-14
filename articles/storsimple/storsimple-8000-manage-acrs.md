---
title: Hozzáférés-vezérlési rekordokat a StorSimple kezelése |} Microsoft Docs
description: Hozzáférés-vezérlési rekordokat (ACRs) használja annak meghatározásához, hogy mely gazdagépek csatlakozhat egy kötetet a StorSimple eszköz ismerteti.
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
ms.date: 05/31/2017
ms.author: alkohli
ms.openlocfilehash: 9173e34f889ce1c082b20bb382cb6ca9a03dd797
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874905"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>A StorSimple Manager szolgáltatással hozzáférés-vezérlési rekordokat kezelése

## <a name="overview"></a>Áttekintés
Hozzáférés-vezérlési rekordokat (ACRs) lehetővé teszik annak megadását, hogy mely gazdagépek csatlakozhat a StorSimple eszközön levő kötet. ACRs vannak beállítva, hogy egy adott kötet, és az iSCSI minősített nevét (IQN-nevekre vonatkozóan), a gazdagépek tartalmaz. Állomás megpróbál egy kötet csatlakozni, amikor az eszköz ellenőrzi, hogy a köteten a IQN-nevének társított az ACR, és ha van egyezés, majd a kapcsolat létrejön. A hozzáférés-vezérlés rögzít a **konfigurációs** a StorSimple Device Manager szolgáltatás panelre szakasza access control rekordok a megfelelő IQN-nevekre vonatkozóan a gazdagépek jeleníti meg.

Ez az oktatóanyag azt ismerteti, hogy a következő általános ACR kapcsolatos feladatok:

* Egy hozzáférés-vezérlési rekordot hozzáadása
* Egy hozzáférés-vezérlési rekordot szerkesztése
* Egy hozzáférés-vezérlési rekordot törlése

> [!IMPORTANT]
> * Amikor egy ACR rendel egy olyan kötetre, gondoskodunk, hogy a kötet nem egyidejűleg hozzáfér egynél több nem fürtözött gazdagépen, mert ez a kötet, megsérülhet.
> * Ha töröl egy ACR a kötetről, győződjön meg arról, hogy a megfelelő gazdagép nem fér hozzá a kötetet, mert a Törlés a írható-olvasható szüneteltetése eredményezheti.

## <a name="get-the-iqn"></a>IQN-Nevének lekérése

A következő lépésekkel egy Windows Server 2012 rendszert futtató Windows-állomás IQN-Nevének lekérése.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Egy hozzáférés-vezérlési rekordot hozzáadása
Használja a **konfigurációs** szakasz ACRs hozzáadása a StorSimple Device Manager szolgáltatás a panelen. Általában egy ACR fog társítani egy kötetet.

A következő lépésekkel adja hozzá az ACR.

#### <a name="to-add-an-acr"></a>Egy ACR hozzáadása

1. Nyissa meg a StorSimple eszköz Manager szolgáltatáshoz, kattintson duplán a szolgáltatás nevére, majd belül a **konfigurációs** kattintson **hozzáférés-vezérlési rekordokat**.
2. Az a **hozzáférés-vezérlési rekordokat** panelen kattintson a **+ Hozzáadás ACR**.

    ![Kattintson a Hozzáadás ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. Az a **hozzáadása ACR** panelen tegye a következőket:

    1. Adja meg az ACR nevét.
    
    2. Adja meg az a Windows Server-állomás IQN nevét **iSCSI kezdeményező nevét (IQN)**.

    3. Kattintson a **Hozzáadás** a ACR létrehozásához.

        ![Kattintson a Hozzáadás ACR](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Az újonnan hozzáadott ACR ACRs táblázatos listáját jeleníti meg.

    ![Kattintson a Hozzáadás ACR](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Egy hozzáférés-vezérlési rekordot szerkesztése
Használja a **konfigurációs** szakasz ACRs szerkesztése a StorSimple Device Manager szolgáltatás a panelen.

> [!NOTE]
> Javasoljuk, hogy csak a jelenleg nem használható ACRs módosítása. Egy olyan kötet, amely jelenleg használatban van társított ACR szerkesztéséhez először érdemes a kötet offline állapotba.

A következő lépésekkel egy ACR szerkesztése.

#### <a name="to-edit-an-access-control-record"></a>Egy hozzáférés-vezérlési rekordot szerkesztése
1.  Nyissa meg a StorSimple eszköz Manager szolgáltatáshoz, kattintson duplán a szolgáltatás nevére, majd belül a **konfigurációs** kattintson **hozzáférés-vezérlési rekordokat**.

    ![Ugrás a hozzáférés-vezérlési rekordokat](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Kattintson a hozzáférés-vezérlési rekordokat táblázatos listája, és válassza ki a módosítani kívánt ACR.

    ![Hozzáférés-vezérlési rekordokat szerkesztése](./media/storsimple-8000-manage-acrs/editacr1.png)

3. Az a **Szerkesztés hozzáférés-vezérlési rekordot** panelen adjon meg egy másik IQN-Nevének megfelelő egy másik gazdagépre.

    ![Hozzáférés-vezérlési rekordokat szerkesztése](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Kattintson a **Save** (Mentés) gombra. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. 

    ![Hozzáférés-vezérlési rekordokat szerkesztése](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Értesítést kap a ACR frissül. A táblázatos listázása is frissíti a változásnak.

   
## <a name="delete-an-access-control-record"></a>Egy hozzáférés-vezérlési rekordot törlése
Használja a **konfigurációs** szakasz ACRs törli a StorSimple Device Manager szolgáltatás a panelen.

> [!NOTE]
> Csak azokat a ACRs, amelyek jelenleg nem törölhetők. Egy olyan kötet, amely jelenleg használatban van társított ACR törléséhez először érdemes a kötet offline állapotba.

A következő lépésekkel egy hozzáférés-vezérlési rekordot törlése.

#### <a name="to-delete-an-access-control-record"></a>Egy hozzáférés-vezérlési rekordot törlése
1.  Nyissa meg a StorSimple eszköz Manager szolgáltatáshoz, kattintson duplán a szolgáltatás nevére, majd belül a **konfigurációs** kattintson **hozzáférés-vezérlési rekordokat**.

    ![Ugrás a hozzáférés-vezérlési rekordokat](./media/storsimple-8000-manage-acrs/createacr1.png)

2. Kattintson a hozzáférés-vezérlési rekordokat táblázatos listája, és válassza ki a törölni kívánt ACR.

    ![Ugrás a hozzáférés-vezérlési rekordokat](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Kattintson a jobb gombbal a helyi menü el, és válassza ki a **törlése**.

    ![Ugrás a hozzáférés-vezérlési rekordokat](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Amikor felszólítja a megerősítésre, tekintse át az adatokat, és kattintson a **törlése**.

    ![Ugrás a hozzáférés-vezérlési rekordokat](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. Értesítést kap a törlés befejeződött. A táblázatos felsorolása a törlés frissül.

    ![Ugrás a hozzáférés-vezérlési rekordokat](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>Következő lépések
* További információ [felügyelete a StorSimple-köteteket](storsimple-8000-manage-volumes-u2.md).
* További információ [a StorSimple Manager szolgáltatás használata a StorSimple eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md).

