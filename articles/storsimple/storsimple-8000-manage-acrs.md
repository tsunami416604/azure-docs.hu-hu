---
title: Hozzáférés-vezérlési rekordok kezelése a StorSimple programban | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként használhatók a hozzáférés-vezérlési rekordok (AKR-ek) annak meghatározására, hogy mely állomások csatlakozhatnak egy kötethez a StorSimple eszközön.
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
ms.openlocfilehash: ade7da25d2307a382c17e7a3cbb26b601c34ef78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "64693238"
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Hozzáférés-vezérlési rekordok kezelése a StorSimple Manager szolgáltatással

## <a name="overview"></a>Áttekintés
A hozzáférés-vezérlési rekordok (AKR-ek) lehetővé teszik annak megadását, hogy mely állomások csatlakozhatnak egy kötethez a StorSimple eszközön. Az AR-ek egy adott kötetre vannak beállítva, és tartalmazzák az állomások iSCSI-minősített neveit (IQN). Amikor egy gazdagép megpróbál csatlakozni egy kötethez, az eszköz ellenőrzi az adott kötethez társított ACR-t az IQN-névért, és ha egyezés van, akkor létrejön a kapcsolat. A StorSimple Eszközkezelő szolgáltatás panel **konfigurációs** szakaszában található hozzáférés-vezérlési rekordok megjelenítik az összes hozzáférés-vezérlési rekordot a gazdagépek megfelelő IQN-jével.

Ez az oktatóanyag a következő gyakori ACR-feladatokkal kapcsolatos:

* Hozzáférés-vezérlési rekord hozzáadása
* Hozzáférés-vezérlőrekord szerkesztése
* Hozzáférés-vezérlési rekord törlése

> [!IMPORTANT]
> * Amikor ACR-t rendel egy kötethez, ügyeljen arra, hogy a kötetet ne érje egyidejűleg egynél több nem fürtözött állomás, mert ez megsérülhet.
> * Acr kötetből való törlésekor győződjön meg arról, hogy a megfelelő állomás nem fér hozzá a kötethez, mert a törlés olvasási és olvasási zavarokat okozhat.

## <a name="get-the-iqn"></a>Szerezd meg az IQN-t

A Windows Server 2012 rendszert futtató Windows-állomás IQN-jének leolvasásához hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]


## <a name="add-an-access-control-record"></a>Hozzáférés-vezérlési rekord hozzáadása
A StorSimple Device Manager szolgáltatás panel **konfigurációs** szakaszával a kresz-ek hozzáadásához. Általában egy ACR-t társít egy kötethez.

ACR hozzáadásához hajtsa végre az alábbi lépéseket.

#### <a name="to-add-an-acr"></a>ACR hozzáadása

1. Nyissa meg a StorSimple Eszközkezelő szolgáltatást, kattintson duplán a szolgáltatás nevére, majd a **Konfiguráció** csoportban kattintson a **Hozzáférés-vezérlési rekordok**elemre.
2. A **Hozzáférés-vezérlési rekordok** panelen kattintson a **+ Add ACR gombra.**

    ![Kattintson az ACR hozzáadása gombra](./media/storsimple-8000-manage-acrs/createacr1.png)

3. Az **ACR hozzáadása** panelen tegye a következő lépéseket:

    1. Adja meg az ACR nevét.
    
    2. Adja meg a Windows Server-állomás IQN-nevét az **iSCSI-kezdeményező neve (IQN)** területen.

    3. Az ACR létrehozásához kattintson a **Hozzáadás** gombra.

        ![Kattintson az ACR hozzáadása gombra](./media/storsimple-8000-manage-acrs/createacr2.png)

4.  Az újonnan hozzáadott ACR megjelenik az ACR táblázatos listájában.

    ![Kattintson az ACR hozzáadása gombra](./media/storsimple-8000-manage-acrs/createacr5.png)


## <a name="edit-an-access-control-record"></a>Hozzáférés-vezérlőrekord szerkesztése
A StorSimple Device Manager szolgáltatáspanel **Konfiguráció szakaszával** szerkesztheti az AR-eket.

> [!NOTE]
> Javasoljuk, hogy csak azokat az AR-eket módosítsa, amelyek jelenleg nincsenek használatban. Ha egy jelenleg használt kötethez társított ACR-t szeretne szerkeszteni, először offline állapotba kell helyeznie a kötetet.

Az ACR szerkesztéséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-edit-an-access-control-record"></a>Hozzáférés-vezérlési rekord szerkesztése
1.  Nyissa meg a StorSimple Eszközkezelő szolgáltatást, kattintson duplán a szolgáltatás nevére, majd a **Konfiguráció** csoportban kattintson a **Hozzáférés-vezérlési rekordok**elemre.

    ![Ugrás a hozzáférés-vezérlési rekordokra](./media/storsimple-8000-manage-acrs/createacr1.png)

2. A hozzáférés-vezérlési rekordok táblázatos listájában kattintson a módosítani kívánt ACR-re, és jelölje ki azt az ACR-t, amelyet módosítani szeretne.

    ![Hozzáférés-vezérlőrekordok szerkesztése](./media/storsimple-8000-manage-acrs/editacr1.png)

3. A **Hozzáférés-vezérlés szerkesztése rekordpanelen** adjon meg egy másik IQN-t, amely egy másik állomásnak felel meg.

    ![Hozzáférés-vezérlőrekordok szerkesztése](./media/storsimple-8000-manage-acrs/editacr2.png)

4. Kattintson a **Mentés** gombra. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra. 

    ![Hozzáférés-vezérlőrekordok szerkesztése](./media/storsimple-8000-manage-acrs/editacr3.png)

5. Az ACR frissítésekor értesítést kap. A táblázatos lista is frissíti, hogy tükrözze a változást.

   
## <a name="delete-an-access-control-record"></a>Hozzáférés-vezérlési rekord törlése
A StorSimple Device Manager szolgáltatáspanel **Konfiguráció szakaszával** törölheti az AR-eket.

> [!NOTE]
> Csak azokat az AR-eket törölheti, amelyek jelenleg nincsenek használatban. Ha egy jelenleg használt kötethez társított ACR-t szeretne törölni, először offline állapotba kell helyeznie a kötetet.

Hozzáférés-vezérlési rekord törléséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-an-access-control-record"></a>Hozzáférés-vezérlési rekord törlése
1.  Nyissa meg a StorSimple Eszközkezelő szolgáltatást, kattintson duplán a szolgáltatás nevére, majd a **Konfiguráció** csoportban kattintson a **Hozzáférés-vezérlési rekordok**elemre.

    ![Ugrás a hozzáférés-vezérlési rekordokra](./media/storsimple-8000-manage-acrs/createacr1.png)

2. A hozzáférés-vezérlési rekordok táblázatos listájában kattintson a törölni kívánt ACR-re, és jelölje ki azt az ACR-t, amelyet törölni szeretne.

    ![Ugrás a hozzáférés-vezérlési rekordokra](./media/storsimple-8000-manage-acrs/deleteacr1.png)

3. Kattintson a jobb gombbal a helyi menü meghívásához, és válassza a **Törlés parancsot.**

    ![Ugrás a hozzáférés-vezérlési rekordokra](./media/storsimple-8000-manage-acrs/deleteacr2.png)

4. Amikor megerősítést kér, tekintse át az adatokat, majd kattintson a **Törlés gombra.**

    ![Ugrás a hozzáférés-vezérlési rekordokra](./media/storsimple-8000-manage-acrs/deleteacr3.png)

5. A törlés befejezésekor értesítést kap. A táblázatos lista frissül, hogy tükrözze a törlést.

    ![Ugrás a hozzáférés-vezérlési rekordokra](./media/storsimple-8000-manage-acrs/deleteacr5.png)

## <a name="next-steps"></a>További lépések
* További információ [a StorSimple kötetek kezeléséről.](storsimple-8000-manage-volumes-u2.md)
* További információ [a StorSimple-kezelő szolgáltatás használatáról a StorSimple-eszköz felügyeletéhez.](storsimple-8000-manager-service-administration.md)

