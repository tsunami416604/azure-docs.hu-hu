---
title: "Hozzáférés-vezérlési rekordokat a StorSimple kezelése |} Microsoft Docs"
description: "Hozzáférés-vezérlési rekordokat (ACRs) használja annak meghatározásához, hogy mely gazdagépek csatlakozhat egy kötetet a StorSimple eszköz ismerteti."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 2f1475d8-36a5-4cc4-84b9-adf8a310b60c
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: alkohli
ms.openlocfilehash: a87624b5706c1d9b8c2b9926e5580996a89ce984
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>A StorSimple Manager szolgáltatással hozzáférés-vezérlési rekordokat kezelése
## <a name="overview"></a>Áttekintés
Hozzáférés-vezérlési rekordokat (ACRs) lehetővé teszik annak megadását, hogy mely gazdagépek csatlakozhat a StorSimple eszközön levő kötet. ACRs vannak beállítva, hogy egy adott kötet, és az iSCSI minősített nevét (IQN-nevekre vonatkozóan), a gazdagépek tartalmaz. Állomás megpróbál egy kötet csatlakozni, amikor az eszköz ellenőrzi, hogy a köteten a IQN-nevének társított az ACR, és ha van egyezés, majd a kapcsolat létrejön. A hozzáférés-vezérlés szakaszban rögzíti a a **konfigurálása** lap megjeleníti az összes a hozzáférési vezérlési rekordot a megfelelő IQN-nevekre vonatkozóan a gazdagépek.

Ez az oktatóanyag azt ismerteti, hogy a következő általános ACR kapcsolatos feladatok:

* Egy hozzáférés-vezérlési rekordot hozzáadása 
* Egy hozzáférés-vezérlési rekordot szerkesztése 
* Egy hozzáférés-vezérlési rekordot törlése 

> [!IMPORTANT]
> * Amikor egy ACR rendel egy olyan kötetre, gondoskodunk, hogy a kötet nem egyidejűleg hozzáfér egynél több nem fürtözött gazdagépen, mert ez a kötet, megsérülhet. 
> * Ha töröl egy ACR a kötetről, győződjön meg arról, hogy a megfelelő gazdagép nem fér hozzá a kötetet, mert a Törlés a írható-olvasható szüneteltetése eredményezheti.
> 
> 

## <a name="add-an-access-control-record"></a>Egy hozzáférés-vezérlési rekordot hozzáadása
A StorSimple Manager szolgáltatás használ **konfigurálása** ACRs adásához. Általában egy ACR fog társítani egy kötetet.

A következő lépésekkel adja hozzá az ACR.

#### <a name="to-add-an-access-control-record"></a>Egy hozzáférés-vezérlési rekordot hozzáadása
1. A szolgáltatás követően lapon válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, és kattintson a **konfigurálása** fülre.
2. A táblázatos felsorolása a **hozzáférés-vezérlési rekordokat**, szállítási egy **neve** az ACR.
3. Adja meg az a Windows-állomás IQN nevét **iSCSI-kezdeményező neve**. A Windows Server-állomás IQN-Nevének lekérése, tegye a következőket:
   
   * Indítsa el a Microsoft iSCSI-kezdeményezőt a Windows-gazdagépen.
   * Az **iSCSI-kezdeményező tulajdonságai** ablakban a **Konfiguráció** lapon jelölje ki, majd másolja a **Kezdeményező neve** mezőben lévő karakterláncot.
   * Illessze be a karakterláncot a a **iSCSI-kezdeményező neve** mezőjét a klasszikus Azure portálon ACRs táblán.
4. Kattintson a **mentése** az újonnan létrehozott ACR mentéséhez. A táblázatos felsorolása a hozzáadást megfelelően frissülnek.

## <a name="edit-an-access-control-record"></a>Egy hozzáférés-vezérlési rekordot szerkesztése
Használja a **konfigurálása** ACRs szerkesztése a klasszikus Azure portálra a lap. 

> [!NOTE]
> Csak a jelenleg nem használható ACRs módosíthatja. Egy olyan kötet, amely jelenleg használatban van társított ACR szerkesztéséhez először érdemes a kötet offline állapotba.
> 
> 

A következő lépésekkel egy ACR szerkesztése.

#### <a name="to-edit-an-access-control-record"></a>Egy hozzáférés-vezérlési rekordot szerkesztése
1. A szolgáltatás követően lapon válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, és kattintson a **konfigurálása** fülre.
2. A hozzáférés-vezérlési rekordokat táblázatos listája, a a módosítani kívánt ACR mutasson.
3. Egy új nevet és/vagy IQN megadnia a ACR.
4. Kattintson a **mentése** menteni a módosított ACR. A táblázatos felsorolása a változásnak frissülni fog.

## <a name="delete-an-access-control-record"></a>Egy hozzáférés-vezérlési rekordot törlése
Használja a **konfigurálása** ACRs törlése a klasszikus Azure portálra a lap. 

> [!NOTE]
> Csak azokat a ACRs, amelyek jelenleg nem törölhetők. Egy olyan kötet, amely jelenleg használatban van társított ACR törléséhez először érdemes a kötet offline állapotba.
> 
> 

A következő lépésekkel egy hozzáférés-vezérlési rekordot törlése.

#### <a name="to-delete-an-access-control-record"></a>Egy hozzáférés-vezérlési rekordot törlése
1. A szolgáltatás követően lapon válassza ki a szolgáltatást, kattintson duplán a szolgáltatás nevére, és kattintson a **konfigurálása** fülre.
2. A hozzáférés-vezérlési rekordokat (ACRs) táblázatos listája, a a törölni kívánt ACR mutasson.
3. A Törlés ikonra (**x**) a rendkívül jobb oldali oszlopban jelenik meg a kiválasztott ACR. Kattintson a **x** törli az ACR ikonra.
4. Amikor felszólítja a megerősítésre, kattintson **Igen** a törlés folytatásához. A táblázatos felsorolása frissíti a törlés.

## <a name="next-steps"></a>Következő lépések
* További információ [felügyelete a StorSimple-köteteket](storsimple-manage-volumes.md).
* További információ [a StorSimple Manager szolgáltatás használata a StorSimple eszköz felügyeletéhez](storsimple-manager-service-administration.md).

