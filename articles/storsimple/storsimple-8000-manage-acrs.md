---
title: StorSimple-ben a hozzáférés-vezérlési rekordok kezelése |} A Microsoft Docs
description: Útmutatás segítségével (ACR-EK) hozzáférés-vezérlési rekordok meghatározhatja, mely állomások csatlakozhat egy kötet a StorSimple eszközön.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38597497"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>A StorSimple Manager szolgáltatás használata kezelheti a hozzáférés-vezérlési rekordok

## <a name="overview"></a>Áttekintés
Hozzáférés-vezérlési rekordok (ACR-EK) lehetővé teszik annak megadását, mely állomások csatlakozhat egy kötet a StorSimple eszközön. ACR-EK vannak beállítva, hogy egy adott kötet, és az iSCSI minősített nevét (IQN-nevekre vonatkozóan) a gazdagépeket tartalmaz. Ha egy gazdagép egy kötetre csatlakozni próbál, az eszköz ellenőrzi az ACR-REL társított az IQN-név a kötetet, és ha van egyezés, majd a kapcsolat létrejött. A hozzáférés-vezérlést rögzíti a **konfigurációs** a StorSimple-Eszközkezelő szolgáltatás paneljén szakaszában minden hozzáférés-vezérlési bejegyzés a megfelelő IQN-nevekre vonatkozóan a gazdagépek a megjelenítéséhez.

Ez az oktatóanyag a következő gyakori ACR kapcsolatos feladatokat ismerteti:

* Adjon hozzá egy hozzáférés-vezérlési rekord
* Egy hozzáférés-vezérlési rekord szerkesztése
* Egy hozzáférés-vezérlési rekord törlése

> [!IMPORTANT]
> * Az ACR hozzárendelése egy kötetet, ha körültekintően, hogy a kötet sem egyidejűleg elérhető egynél több nem fürtözött gazdagépen, mert ez a kötet, megsérülhet.
> * Egy ACR-t egy kötet törlésekor győződjön meg arról, hogy a megfelelő gazdagép nem fér hozzá a kötetet, mert a törlés egy írható-olvasható megszakítás eredményezheti.

## <a name="get-the-iqn"></a>IQN Nevének lekérése

A következő lépésekkel Windows Server 2012 rendszert futtató Windows-gazdagép IQN Nevének lekérése.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Adjon hozzá egy hozzáférés-vezérlési rekord
Használja a **konfigurációs** szakasz a StorSimple-Eszközkezelő szolgáltatás paneljén hozzáadása az ACR-EK. Általában egy ACR fog társítani egy kötetet.

A következő lépésekkel adja hozzá egy ACR-t.

#### <a name="to-add-an-acr"></a>Az ACR hozzáadása

1. Keresse meg a StorSimple-Eszközkezelő szolgáltatást, kattintson duplán a szolgáltatás nevét, és ezután belül a **konfigurációs** területén kattintson **hozzáférés-vezérlési rekordok**.
2. Az a **hozzáférés-vezérlési rekordok** panelen kattintson a **+ ACR hozzáadása**.

    ![Kattintson a Hozzáadás ACR](./media/storsimple-8000-manage-acrs/createacr1.png)

3. Az a **ACR hozzáadása** panelen tegye a következőket:

    1. Adjon meg egy nevet az ACR.
    
    2. Adja meg a csoportban a Windows Server-állomás IQN neve **iSCSI kezdeményező neve (IQN)**.

    3. Kattintson a **Hozzáadás** az ACR létrehozása.

        ![Kattintson a Hozzáadás ACR](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Az újonnan hozzáadott ACR ACR-EK táblázatos jeleníti meg.

    ![Kattintson a Hozzáadás ACR](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Egy hozzáférés-vezérlési rekord szerkesztése
Használja a **konfigurációs** szakasz a StorSimple-Eszközkezelő szolgáltatás paneljén szerkesztése az ACR-EK.

> [!NOTE]
> Javasoljuk, hogy csak adott ACR-EK, amelyek jelenleg nem használt módosítása. Egy olyan kötetre, amely jelenleg használatban van társítva ACR szerkesztéséhez, előbb végre kell hajtania a kötet offline.

Hajtsa végre az alábbi lépések végrehajtásával szerkesztheti egy ACR-t.

#### <a name="to-edit-an-access-control-record"></a>Egy hozzáférés-vezérlési rekord szerkesztése
1.  Keresse meg a StorSimple-Eszközkezelő szolgáltatást, kattintson duplán a szolgáltatás nevét, és ezután belül a **konfigurációs** területén kattintson **hozzáférés-vezérlési rekordok**.

    ![Ugrás a hozzáférés-vezérlési rekordok](./media/storsimple-8000-manage-acrs/createacr1.png)

2. A hozzáférés-vezérlési rekordok táblázatos listájában tekintse kattintson, és válassza ki a módosítani kívánt ACR.

    ![Hozzáférés-vezérlési rekordok szerkesztése](./media/storsimple-8000-manage-acrs/editacr1.png)

3. Az a **hozzáférés-vezérlési rekord szerkesztése** panelen adjon meg egy másik IQN-Nevének megfelelő egy másik gazdagépre.

    ![Hozzáférés-vezérlési rekordok szerkesztése](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Kattintson a **Save** (Mentés) gombra. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. 

    ![Hozzáférés-vezérlési rekordok szerkesztése](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Értesítést kap az ACR-REL frissül. A táblázatos ajánlati is frissíti a változás tükrözése érdekében.

   
## <a name="delete-an-access-control-record"></a>Egy hozzáférés-vezérlési rekord törlése
Használja a **konfigurációs** szakasz a StorSimple-Eszközkezelő szolgáltatás paneljén törli az ACR-EK.

> [!NOTE]
> Törölheti csak ezen ACR-EK, amelyek jelenleg nem használja. A töröl egy olyan kötetre, amely jelenleg használatban van társítva, akkor előbb végre kell hajtania a kötet offline.

Hajtsa végre az alábbi lépéseket egy hozzáférés-vezérlési rekord törlése.

#### <a name="to-delete-an-access-control-record"></a>Egy hozzáférés-vezérlési rekord törlése
1.  Keresse meg a StorSimple-Eszközkezelő szolgáltatást, kattintson duplán a szolgáltatás nevét, és ezután belül a **konfigurációs** területén kattintson **hozzáférés-vezérlési rekordok**.

    ![Ugrás a hozzáférés-vezérlési rekordok](./media/storsimple-8000-manage-acrs/createacr1.png)

2. A hozzáférés-vezérlési rekordok táblázatos listájából, kattintson, és jelölje be az ACR-REL, amelyet törölni kíván.

    ![Ugrás a hozzáférés-vezérlési rekordok](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Kattintson a jobb gombbal indítja el a helyi menüt, és válassza ki **törlése**.

    ![Ugrás a hozzáférés-vezérlési rekordok](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Amikor a rendszer megerősítést kér, tekintse át az adatokat, és kattintson a **törlése**.

    ![Ugrás a hozzáférés-vezérlési rekordok](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. A Törlés befejezése után értesítést kap. A törlés táblázatos frissül.

    ![Ugrás a hozzáférés-vezérlési rekordok](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>További lépések
* Tudjon meg többet [kezelése a StorSimple-kötetek](storsimple-8000-manage-volumes-u2.md).
* Tudjon meg többet [a StorSimple Manager szolgáltatás használata a StorSimple-eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md).

