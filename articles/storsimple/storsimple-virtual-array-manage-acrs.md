---
title: Hozzáférés-vezérlési rekordok kezelése a StorSimple virtual array-hez | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy miként kezelhetők a hozzáférés-vezérlési rekordok (AKR-ek) annak meghatározásához, hogy mely állomások csatlakozhatnak a StorSimple virtuális tömb köteteihez.
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
ms.openlocfilehash: 1dfc1b0e0576402624bfe62de0e206d9bd7cd1b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "64724420"
---
# <a name="use-storsimple-device-manager-to-manage-access-control-records-for-storsimple-virtual-array"></a>A StorSimple Eszközkezelő vel kezelheti a StorSimple virtuális tömb hozzáférés-vezérlési rekordjait

## <a name="overview"></a>Áttekintés

A hozzáférés-vezérlési rekordok (AKR-ek) lehetővé teszik annak megadását, hogy mely állomások csatlakozhatnak egy kötethez a StorSimple virtuális tömb (más néven a Helyszíni StorSimple virtuális eszköz) kötetéhez. Az AR-ek egy adott kötetre vannak beállítva, és tartalmazzák az állomások iSCSI-minősített neveit (IQN). Amikor egy gazdagép megpróbál csatlakozni egy kötethez, az eszköz ellenőrzi az adott kötethez társított ACR-t az IQN-névért, és ha egyezés van, akkor létrejön a kapcsolat. Az Eszközkezelő szolgáltatás **Konfiguráció** szakaszában található **hozzáférés-vezérlési rekordok** panel megjeleníti az összes hozzáférés-vezérlési rekordot a gazdagépek megfelelő IQN-jével.

![Hozzáférés-vezérlési rekordok kezelése](./media/storsimple-virtual-array-manage-acrs/ova-manage-acrs.png)

Ez az oktatóanyag a következő gyakori ACR-feladatokkal kapcsolatos:

* Szerezd meg az IQN-t
* Hozzáférés-vezérlési rekord hozzáadása
* Hozzáférés-vezérlőrekord szerkesztése
* Hozzáférés-vezérlési rekord törlése

> [!IMPORTANT]
> 
> * Amikor ACR-t rendel egy kötethez, ügyeljen arra, hogy a kötetet ne érje egyidejűleg egynél több nem fürtözött állomás, mert ez megsérülhet.
> * Acr kötetből való törlésekor győződjön meg arról, hogy a megfelelő állomás nem fér hozzá a kötethez, mert a törlés olvasási és olvasási zavarokat okozhat.


## <a name="get-the-iqn"></a>Szerezd meg az IQN-t

A Windows Server 2012 rendszert futtató Windows-állomás IQN-jének leolvasásához hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-get-iqn](../../includes/storsimple-get-iqn.md)]

## <a name="add-an-acr"></a>ACR hozzáadása

A StorSimple Eszközkezelő szolgáltatás **Konfiguráció** szakaszában a **hozzáférés-vezérlési rekordok** panelen használhatja az AR-ek hozzáadásához. Általában egy ACR-t társít egy kötethez.

Az ACR kötethez való társításáról a kötet hozzáadásáról a [majd a majd a kötet et.](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume)

> [!IMPORTANT]
> Amikor ACR-t rendel egy kötethez, ügyeljen arra, hogy a kötetet ne érje egyidejűleg egynél több nem fürtözött állomás, mert ez megsérülhet.


ACR hozzáadásához hajtsa végre az alábbi lépéseket.

#### <a name="to-add-an-acr"></a>ACR hozzáadása

1. A szolgáltatás céllapján jelölje ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, majd a **Konfiguráció** csoportban kattintson a **Hozzáférés-vezérlési rekordok**elemre.
2. A **Hozzáférés-vezérlési rekordok** panelen kattintson a **Hozzáadás**gombra.
3. Az **ACR hozzáadása** panelen tegye a következőket:
   
    1. Adja meg az ACR **nevét**.
    
    2. Az **iSCSI-kezdeményező neve csoportban**adja meg a Windows-állomás IQN-nevét. A Windows Server-állomás IQN-jének levételéhez tegye a következőket:
   
    3. Indítsa el a Microsoft iSCSI-kezdeményezőt a Windows-gazdagépen. Az iSCSI-kezdeményező tulajdonságai ablakban a **Konfiguráció** lapon jelölje ki, majd másolja a **Kezdeményező neve** mezőben lévő sztringet.
    Illessze be ezt a karakterláncot az **ACR hozzáadása** panel **IQN** mezőjébe.
   
    6. Az ACR hozzáadásához kattintson a **Hozzáadás** gombra.  
   
        ![Hozzáférés-vezérlési rekordok hozzáadása](./media/storsimple-virtual-array-manage-acrs/ova-add-acrs.png)
4. A táblázatos lista frissül, hogy tükrözze ezt a kiegészítést.

## <a name="edit-an-acr"></a>ACR szerkesztése

Az Azure Portalon az Eszközkezelő szolgáltatás **Konfigurációs** szakaszában található **hozzáférés-vezérlési rekordok** paneljét használhatja az AR-ek szerkesztéséhez.

> [!NOTE]
> Ne módosítsa a jelenleg használatban lévő ACR-t. Ha egy jelenleg használt kötethez társított ACR-t szeretne szerkeszteni, először offline állapotba kell helyeznie a kötetet.


Az ACR szerkesztéséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-edit-an-acr"></a>ACR szerkesztése

1. A szolgáltatás céllapján jelölje ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, majd a **Konfiguráció** szakasz **Hozzáférés-vezérlési rekordok**területén.
2. A **hozzáférés-vezérlési rekordok** panelen a hozzáférés-vezérlési rekordok táblázatos listájából kattintson duplán a módosítani kívánt Hozzáférési szabályszabály-szabályra.
3. A **Hozzáférés-vezérlési rekordok szerkesztése** panelen tegye a következőket:
   
    1. Adja meg az IQN-t az ACR-hez.
   
    2. A módosított ACR mentéséhez kattintson a panel tetején található **Mentés** gombra. A következő megerősítő üzenet jelenik meg:
   
        ![Hozzáférés-vezérlőrekordok szerkesztése](./media/storsimple-virtual-array-manage-acrs/ova-edit-acrs.png)

## <a name="delete-an-access-control-record"></a>Hozzáférés-vezérlési rekord törlése

Az Azure Portal **konfigurációs** lapján törölheti az AR-eket.

> [!NOTE]
> 
> * Ne töröljön olyan ACR-t, amely jelenleg használatban van. Ha egy jelenleg használt kötethez társított ACR-t szeretne törölni, először offline állapotba kell helyeznie a kötetet.
> * Acr kötetből való törlésekor győződjön meg arról, hogy a megfelelő állomás nem fér hozzá a kötethez, mert a törlés olvasási és olvasási zavarokat okozhat.


Hozzáférés-vezérlési rekord törléséhez hajtsa végre az alábbi lépéseket.

#### <a name="to-delete-an-access-control-record"></a>Hozzáférés-vezérlési rekord törlése

1. A szolgáltatás céllapján jelölje ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, majd a **Konfiguráció** szakasz **Hozzáférés-vezérlési rekordok**területén.

2. A **hozzáférés-vezérlési rekordok** panelen a hozzáférés-vezérlési rekordok táblázatos listájából kattintson duplán a törölni kívánt Hozzáférési szabályszabály-szabályra.

3. A Hozzáférés-vezérlési rekordok szerkesztése panelen kattintson a **Törlés gombra.**
   
    ![ACRS törlése](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs.png)

4. Amikor megerősítést kér, a törlés folytatásához kattintson a **Törlés** gombra. A táblázatos lista frissül, hogy tükrözze a törlést.
   
   ![Figyelmeztető üzenet](./media/storsimple-virtual-array-manage-acrs/ova-del-acrs-warning.png)

## <a name="next-steps"></a>További lépések

* További információ a [kötetek hozzáadásáról és az ACR-ek konfigurálásáról.](storsimple-virtual-array-deploy3-iscsi-setup.md#step-3-add-a-volume)

