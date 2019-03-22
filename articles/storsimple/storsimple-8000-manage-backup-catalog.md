---
title: A StorSimple biztonságimásolat-katalógus kezelése |} A Microsoft Docs
description: Azt ismerteti, hogyan használhatja a StorSimple-Eszközkezelő szolgáltatás biztonságimásolat-katalógus lapján listában válassza ki, és törölje a biztonságimásolat-készleteket.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: 07d9e03f1631ebce88a7a7c2e33be62f21dda522
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076785"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>A biztonságimásolat-katalógus kezelése a StorSimple-Eszközkezelő szolgáltatás segítségével
## <a name="overview"></a>Áttekintés
A StorSimple-Eszközkezelő szolgáltatás **biztonságimásolat-katalógus** panel jeleníti meg az összes biztonságimásolat-készletek, amelyek jönnek létre, ha a manuális vagy ütemezett biztonsági mentések megnyílik. Ezen a lapon egy biztonsági mentési szabályzatot, vagy egy kötetet, jelölje be az összes biztonsági mentés listában, vagy a biztonsági mentések törlésével, vagy használjon olyan biztonsági másolatot vissza-vagy kötet klónozása.

Ez az oktatóanyag azt ismerteti, hogyan listában válassza ki, és a egy biztonságimásolat-készlet törlése. Ismerje meg, hogy az eszköz visszaállítása biztonsági másolatból, lépjen a [az eszköz visszaállítása a biztonságimásolat-készlet](storsimple-8000-restore-from-backup-set-u2.md). Ismerje meg, hogyan kötet klónozása, lépjen a [StorSimple-kötet klónozása](storsimple-8000-clone-volume-u2.md).

![a biztonságimásolat-katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

A **biztonságimásolat-katalógus** panel biztosít egy lekérdezés szűkítheti a biztonsági mentés beállítása. A biztonságimásolat-készleteket, amelyek lekérni, a következő paraméterek alapján szűrheti:

* **Eszköz** – az eszköz, amelyen a biztonságimásolat-készletet létrehozták.
* **A biztonsági mentési szabályzat vagy a kötet** – a biztonsági mentési szabályzat vagy a kötet a biztonságimásolat-készlet társítva.
* **A kezdő és a** – a dátum- és időtartományt a biztonságimásolat-készlet létrehozásakor.

A szűrt biztonságimásolat-készleteket majd megjelennének attribútumok közül a következők alapján:

* **Név** – a biztonsági mentési szabályzat vagy a kötet a biztonságimásolat-készlet társított nevét.
* **Méret** – a biztonságimásolat-készlet aktuális méretének.
* **Létrehozás dátuma** – a dátum és idő, amikor a biztonsági mentések létrejöttek. 
* **Típus** – Backup sets lehet a helyi pillanatképek vagy felhőalapú pillanatfelvételek. Helyi pillanatképet az összes kötet az eszközön, a helyileg tárolt biztonsági, mivel a felhőbeli pillanatképet a biztonsági mentés, a felhőben levő kötetadatokról hivatkozik. A helyi pillanatképeket gyorsabb engedélyt biztosít, mivel a felhőbeli pillanatképekkel adatrugalmasság közül választ.
* **Elindító** – a biztonsági mentések kezdeményezhetők automatikusan egy ütemezés szerint vagy manuálisan felhasználója. Használhatja a biztonsági mentési szabályzatot a biztonsági mentések ütemezéséhez. Másik lehetőségként használhatja a **biztonsági mentés** manuális biztonsági mentés készítése lehetőséget.

## <a name="list-backup-sets-for-a-backup-policy"></a>Lista biztonságimásolat-készletet a biztonsági mentési házirend
A következő lépéseket egy biztonsági mentési szabályzat az összes biztonsági mentés listázásához.

#### <a name="to-list-backup-sets"></a>A biztonságimásolat-készletek listája
1. Nyissa meg az a StorSimple-Eszközkezelő szolgáltatás, majd kattintson a **biztonságimásolat-katalógus**.

2. Szűrőbeállítások a következők szerint:
   
   1. Megadhatja az időtartományt.
   2. Válassza ki a megfelelő eszközt.
   3. Szűrés **biztonsági mentési szabályzat** a megfelelő a biztonsági másolatok megtekintéséhez.
   3. A biztonsági mentési szabályzat legördülő listából válassza ki a **összes** a biztonsági másolatok megtekintése a kiválasztott eszközön.
   4. Kattintson a **alkalmaz** a lekérdezés végrehajtásához.
      
      A kiválasztott biztonsági mentési szabályzathoz társított biztonsági másolatok meg kell jelennie a biztonságimásolat-készletek listája.

      ![Nyissa meg a biztonságimásolat-katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Válassza ki a biztonságimásolat-készletből
A következő lépéseket követve állíthatja be egy kötet vagy a biztonsági mentési szabályzat biztonsági másolat kiválasztása.

#### <a name="to-select-a-backup-set"></a>Jelölje be a biztonságimásolat-készletből
1. Nyissa meg az a StorSimple-Eszközkezelő szolgáltatás, majd kattintson a **biztonságimásolat-katalógus**.
2. Szűrőbeállítások a következők szerint:
   
   1. Megadhatja az időtartományt. 
   2. Válassza ki a megfelelő eszközt. 
   3. Kötet vagy a biztonsági mentési házirend, amelyet szeretne, válassza ki a biztonsági szűrés.
   4. Kattintson a **alkalmaz** a lekérdezés végrehajtásához.
      
      A biztonsági mentések társított a kijelölt kötetről, vagy a biztonsági mentési szabályzat meg kell jelennie a biztonságimásolat-készletek listájában.

      ![Nyissa meg a biztonságimásolat-katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Válassza ki, és bontsa ki a biztonságimásolat-készletből. Most már megtekintheti a biztonságimásolat-készleteket a benne található kötetek szerinti bontásban. A **visszaállítása** és **törlése** beállítások keresztül érhető el a helyi menü (helyi) biztonságimásolat-készlet. A biztonságimásolat-készlet, amely a kijelölt ezen műveletek egyikét végezheti.

    ![Nyissa meg a biztonságimásolat-katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>A biztonságimásolat-készlet törlése
Egy biztonsági másolat törlése, amikor már nem szeretne megőrizni a hozzá kapcsolódó adatokat. Hajtsa végre az alábbi lépéseket egy biztonságimásolat-készlet törlése.

#### <a name="to-delete-a-backup-set"></a>A biztonságimásolat-készlet törlése
 Nyissa meg az a StorSimple-Eszközkezelő szolgáltatás, majd kattintson a **biztonságimásolat-katalógus**.
1. Szűrőbeállítások a következők szerint:
   
   1. Megadhatja az időtartományt. 
   2. Válassza ki a megfelelő eszközt. 
   3. Kötet vagy a biztonsági mentési házirend, amelyet szeretne, válassza ki a biztonsági szűrés.
   4. Kattintson a **alkalmaz** a lekérdezés végrehajtásához.
      
      A biztonsági mentések társított a kijelölt kötetről, vagy a biztonsági mentési szabályzat meg kell jelennie a biztonságimásolat-készletek listájában.

      ![Nyissa meg a biztonságimásolat-katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

1. Válassza ki, és bontsa ki a biztonságimásolat-készletből. Most már megtekintheti a biztonságimásolat-készleteket a benne található kötetek szerinti bontásban. A **visszaállítása** és **törlése** beállítások keresztül érhető el a helyi menü (helyi) biztonságimásolat-készlet. Kattintson a jobb gombbal a kiválasztott biztonságimásolat-készlet és a helyi menüből válassza ki a **törlése**.

    ![Nyissa meg a biztonságimásolat-katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

1. Amikor a rendszer megerősítést kér, tekintse át a megjelenített információkat, és kattintson a **törlése**. A kijelölt biztonsági másolat véglegesen törlődik.

    ![Nyissa meg a biztonságimásolat-katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

1. Értesíteni fogjuk, ha a törlés van folyamatban, és ha ez sikeresen befejeződött. A törlés után frissítse a lekérdezés ezen az oldalon. A törölt biztonságimásolat-készlet nem jelenik meg a biztonságimásolat-készletek listájában.

    ![Nyissa meg a biztonságimásolat-katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [a biztonságimásolat-katalógus használatával állítsa vissza az eszköz biztonságimásolat-készlet](storsimple-8000-restore-from-backup-set-u2.md).
* Ismerje meg, hogyan [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).

