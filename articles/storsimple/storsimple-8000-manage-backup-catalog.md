---
title: A StorSimple biztonságimásolat-katalógus kezelése |} Microsoft Docs
description: Ismerteti a StorSimple Device Manager szolgáltatás biztonságimásolat-katalógus lapnak a használatával listában válassza ki, és törölje a biztonsági mentés.
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
ms.openlocfilehash: ac2577c6cd350d6d437d55e61ec73d954cb24893
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874926"
---
# <a name="use-the-storsimple-device-manager-service-to-manage-your-backup-catalog"></a>A StorSimple Device Manager szolgáltatással, a biztonságimásolat-katalógus kezelése
## <a name="overview"></a>Áttekintés
A StorSimple Device Manager szolgáltatás **biztonságimásolat-katalógus** csempe megjeleníti a biztonsági mentés során manuális vagy ütemezett biztonsági mentést készít a létrehozott. Ezen a lapon a biztonsági mentési házirend, vagy egy kötetet, jelölje be a biztonsági másolatok felsorolása, vagy törölje a biztonsági mentések, vagy a biztonsági másolat használatával állítsa vissza vagy egy kötet klónozása.

Ez az oktatóanyag ismerteti, hogyan listában válassza ki, és egy biztonságimásolat-készlet törlése. További információk az eszköz visszaállítása biztonsági másolatból, [szeretné visszaállítani az eszközt egy biztonságimásolat-készlet](storsimple-8000-restore-from-backup-set-u2.md). Kötet klónozása útmutató, keresse fel [klónozza a StorSimple-kötet](storsimple-8000-clone-volume-u2.md).

![Biztonságimásolat-katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog.png) 

A **biztonságimásolat-katalógus** panel biztosít egy lekérdezést szűkítheti a biztonsági másolat beállítása. A biztonságimásolat-készletek, amelyek lekérése, a következő paraméterek alapján szűrheti:

* **Eszköz** – az eszköz, amelyre a biztonságimásolat-készlet létrehozása.
* **A biztonsági mentési házirend, vagy a kötet** – a biztonsági mentési házirend, vagy a kötet a biztonságimásolat-készlet társítva.
* **A kezdő és a** – a dátum és idő tartomány, a biztonságimásolat-készlet létrehozásakor.

A szűrt biztonsági mentés majd megjelennének a következő attribútumok alapján:

* **Név** – a biztonsági mentési házirend, vagy a kötet a biztonságimásolat-készlet társított nevét.
* **Méret** – a biztonságimásolat-készlet tényleges méretének.
* **Létrehozás** – a dátum és idő, amikor a biztonsági mentés készült. 
* **Típus** – biztonsági másolat beállítása helyi pillanatképeket és felhőalapú pillanatfelvételek. Egy helyi pillanatfelvétel minden kötet adatait az eszközön, helyileg tárolt biztonsági másolatot, mivel egy felhőalapú pillanatfelvétel hivatkozik a felhőben tárolt kötet adatok biztonsági mentését. Helyi pillanatképeket gyorsabb hozzáférést biztosít, mivel felhőalapú pillanatfelvételek az adatrugalmasság közül választ.
* **Elindító** – a biztonsági mentések kezdeményezheti automatikusan egy ütemezés szerint vagy manuálisan egy felhasználó által. A biztonsági mentési házirend segítségével biztonsági mentés ütemezése. Másik lehetőségként használhatja a **biztonsági másolatok készítéséhez** beállítás manuális biztonsági mentés végrehajtása.

## <a name="list-backup-sets-for-a-backup-policy"></a>Lista biztonságimásolat-készletet a biztonsági mentési házirend
Az alábbi lépésekkel egy biztonsági mentési házirend biztonsági másolatok felsorolása.

#### <a name="to-list-backup-sets"></a>A lista biztonságimásolat-készletek
1. Nyissa meg a StorSimple Device Manager szolgáltatást, és kattintson a **biztonságimásolat-katalógus**.

2. Az alábbiak szerint szűrheti a lehetőségek közül:
   
   1. Adja meg azt az időtartományt.
   2. Válassza ki a megfelelő eszközt.
   3. Szűrés **biztonsági mentési házirend** megtekintéséhez a megfelelő biztonsági mentés.
   3. A biztonsági mentési házirend legördülő listából válassza ki a **összes** kattintva megtekintheti az összes biztonsági mentés a kiválasztott eszköz.
   4. Kattintson a **alkalmaz** a lekérdezés végrehajtásához.
      
      A kijelölt biztonsági mentési házirend társított biztonsági másolatok meg kell jelennie a biztonságimásolat-készletek listájában.

      ![Ugrás a biztonságimásolat-katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

## <a name="select-a-backup-set"></a>Válassza ki a biztonságimásolat-készletből
Az alábbi lépésekkel válassza ki a biztonsági másolatot egy kötet vagy a biztonsági mentési házirend.

#### <a name="to-select-a-backup-set"></a>Jelölje be a biztonságimásolat-készletből
1. Nyissa meg a StorSimple Device Manager szolgáltatást, és kattintson a **biztonságimásolat-katalógus**.
2. Az alábbiak szerint szűrheti a lehetőségek közül:
   
   1. Adja meg azt az időtartományt. 
   2. Válassza ki a megfelelő eszközt. 
   3. Szűrés a biztonsági mentés, válassza ki szeretne kötet vagy a biztonsági mentési házirend.
   4. Kattintson a **alkalmaz** a lekérdezés végrehajtásához.
      
      A biztonsági mentések társított a kijelölt kötetről, vagy a biztonsági mentési házirend megjelenjen-e a biztonságimásolat-készletek listájában.

      ![Ugrás a biztonságimásolat-katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Válassza ki, és bontsa ki a biztonságimásolat-készletből. Most már megtekintheti a biztonsági mentés, a benne található kötetek szerinti bontásban. A **visszaállítása** és **törlése** beállítások érhetők el a helyi menüt (kattintson a jobb gombbal) a biztonsági másolat készletéhez. Ezek a műveletek egyikét a biztonságimásolat-készlet, verziószámával a hajthatja végre.

    ![Ugrás a biztonságimásolat-katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog2.png)

## <a name="delete-a-backup-set"></a>A biztonságimásolat-készlet törlése
Törli az egy biztonsági mentés, ha már nem szeretne a vele társított adatok megőrzése mellett. A következő lépésekkel egy biztonságimásolat-készlet törlése.

#### <a name="to-delete-a-backup-set"></a>A biztonságimásolat-készlet törlése
 Nyissa meg a StorSimple Device Manager szolgáltatást, és kattintson a **biztonságimásolat-katalógus**.
2. Az alábbiak szerint szűrheti a lehetőségek közül:
   
   1. Adja meg azt az időtartományt. 
   2. Válassza ki a megfelelő eszközt. 
   3. Szűrés a biztonsági mentés, válassza ki szeretne kötet vagy a biztonsági mentési házirend.
   4. Kattintson a **alkalmaz** a lekérdezés végrehajtásához.
      
      A biztonsági mentések társított a kijelölt kötetről, vagy a biztonsági mentési házirend megjelenjen-e a biztonságimásolat-készletek listájában.

      ![Ugrás a biztonságimásolat-katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog1.png)

3. Válassza ki, és bontsa ki a biztonságimásolat-készletből. Most már megtekintheti a biztonsági mentés, a benne található kötetek szerinti bontásban. A **visszaállítása** és **törlése** beállítások érhetők el a helyi menüt (kattintson a jobb gombbal) a biztonsági másolat készletéhez. Kattintson a jobb gombbal a kijelölt biztonságimásolat-készlet és a helyi menüben válassza a **törlése**.

    ![Ugrás a biztonságimásolat-katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog3.png)

4. Amikor felszólítja a megerősítésre, tekintse át a megjelenített információkat, majd kattintson **törlése**. A kijelölt biztonsági véglegesen törlődik.

    ![Ugrás a biztonságimásolat-katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog4.png)  

5. Meg fog értesítést kapni a Törlés folyamatban van, és ha sikeresen befejeződött. A törlés után frissítse a lekérdezést. ezen a lapon. A törölt biztonságimásolat-készlet nem fog megjelenni a biztonságimásolat-készletek listájában.

    ![Ugrás a biztonságimásolat-katalógus](./media/storsimple-8000-manage-backup-catalog/bucatalog7.png)

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [a biztonságimásolat-katalógus használatával állítsa vissza az eszköz biztonságimásolat-készletből](storsimple-8000-restore-from-backup-set-u2.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

