---
title: "A StorSimple biztonságimásolat-katalógus kezelése |} Microsoft Docs"
description: "Ismerteti a StorSimple Manager szolgáltatás biztonságimásolat-katalógus lapnak a használatával listában válassza ki, és egy kötet a biztonságimásolat-készletek törlése."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: ad81bee9-fe43-40b3-a384-b15fb274ecd9
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 04/28/2016
ms.author: v-sharos
ms.openlocfilehash: 5ee9855e1428c7a2d871d9c215d302c5c3b7101a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-your-backup-catalog"></a>A StorSimple Manager szolgáltatás segítségével a biztonságimásolat-katalógus kezelése
## <a name="overview"></a>Áttekintés
A StorSimple Manager szolgáltatás **biztonságimásolat-katalógus** lap megjeleníti a biztonsági mentés során manuális vagy ütemezett biztonsági mentést készít a létrehozott. Ezen a lapon a biztonsági mentési házirend, vagy egy kötetet, jelölje be a biztonsági másolatok felsorolása, vagy törölje a biztonsági mentések, vagy a biztonsági másolat használatával állítsa vissza vagy egy kötet klónozása.

Ez az oktatóanyag ismerteti, hogyan listában válassza ki, és egy biztonságimásolat-készlet törlése. További információk az eszköz visszaállítása biztonsági másolatból, [szeretné visszaállítani az eszközt egy biztonságimásolat-készlet](storsimple-restore-from-backup-set.md). Kötet klónozása útmutató, keresse fel [klónozza a StorSimple-kötet](storsimple-clone-volume.md).

![Biztonságimásolat-katalógus](./media/storsimple-manage-backup-catalog/backupcatalog.png) 

A **biztonságimásolat-katalógus** lap megjeleníti a lekérdezés szűkítheti a biztonsági másolat beállítása. A biztonságimásolat-készletek, amelyek lekérése, a következő paraméterek alapján szűrheti:

* **Eszköz** – az eszköz, amelyre a biztonságimásolat-készlet létrehozása.
* **Biztonsági mentési házirend, vagy a kötet** – a biztonsági mentési házirend, vagy a kötet a biztonságimásolat-készlet társítva.
* **A kezdő és a** – a dátum és idő tartomány, a biztonságimásolat-készlet létrehozásakor.

A szűrt biztonsági mentés majd megjelennének a következő attribútumok alapján:

* **Név** – a biztonsági mentési házirend, vagy a kötet a biztonságimásolat-készlet társított nevét.
* **Méret** – a biztonságimásolat-készlet tényleges méretének.
* **Létrehozás** – a dátum és idő, amikor a biztonsági mentés készült. 
* **Típus** – biztonsági másolat beállítása helyi pillanatképeket és felhőalapú pillanatfelvételek. Egy helyi pillanatfelvétel minden kötet adatait az eszközön, helyileg tárolt biztonsági másolatot, mivel egy felhőalapú pillanatfelvétel hivatkozik a felhőben tárolt kötet adatok biztonsági mentését. Helyi pillanatképeket gyorsabb hozzáférést biztosít, mivel felhőalapú pillanatfelvételek az adatrugalmasság közül választ.
* **Elindító** – a biztonsági mentések kezdeményezheti automatikusan egy ütemezés szerint vagy manuálisan egy felhasználó által. A biztonsági mentési házirend segítségével biztonsági mentés ütemezése. Másik lehetőségként használhatja a **biztonsági másolatok készítéséhez** beállítás manuális biztonsági mentés végrehajtása.

## <a name="list-backup-sets-for-a-volume"></a>Lista biztonságimásolat-készletet kötetre
Az alábbi lépésekkel egy kötetet a biztonsági másolatok felsorolása.

#### <a name="to-list-backup-sets"></a>A lista biztonságimásolat-készletek
1. A StorSimple Manager szolgáltatás lapján kattintson a **biztonságimásolat-katalógus** fülre.
2. Az alábbiak szerint szűrheti a lehetőségek közül:
   
   1. Válassza ki a megfelelő eszközt.
   2. A legördülő listában válasszon ki egy kötetet, a megfelelő biztonsági másolatok megtekintése.
   3. Adja meg azt az időtartományt.
   4. Kattintson a pipa ikonra ![Pipa ikon](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) a lekérdezés végrehajtásához.
      
      A kijelölt kötet társított biztonsági másolatok meg kell jelennie a biztonságimásolat-készletek listájában.

## <a name="select-a-backup-set"></a>Válassza ki a biztonságimásolat-készletből
Az alábbi lépésekkel válassza ki a biztonsági másolatot egy kötet vagy a biztonsági mentési házirend.

#### <a name="to-select-a-backup-set"></a>Jelölje be a biztonságimásolat-készletből
1. A StorSimple Manager szolgáltatás lapján kattintson a **biztonságimásolat-katalógus** fülre.
2. Az alábbiak szerint szűrheti a lehetőségek közül:
   
   1. Válassza ki a megfelelő eszközt.
   2. A legördülő listában válassza ki szeretne biztonsági másolatot az kötet vagy a biztonsági mentési házirend kiválasztásához.
   3. Adja meg azt az időtartományt.
   4. Kattintson a pipa ikonra ![Pipa ikon](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) a lekérdezés végrehajtásához.
      
      A biztonsági mentések társított a kijelölt kötetről, vagy a biztonsági mentési házirend megjelenjen-e a biztonságimásolat-készletek listájában.
3. Válassza ki, és bontsa ki a biztonságimásolat-készletből. A **visszaállítása** és **törlése** beállítások jelennek meg a lap alján. Ezek a műveletek egyikét a biztonságimásolat-készlet, verziószámával a hajthatja végre.

## <a name="delete-a-backup-set"></a>A biztonságimásolat-készlet törlése
Törli az egy biztonsági mentés, ha már nem szeretne a vele társított adatok megőrzése mellett. A következő lépésekkel egy biztonságimásolat-készlet törlése.

#### <a name="to-delete-a-backup-set"></a>A biztonságimásolat-készlet törlése
1. A StorSimple Manager szolgáltatás lapján kattintson a **biztonságimásolat-katalógus lapon**.
2. Az alábbiak szerint szűrheti a lehetőségek közül:
   
   1. Válassza ki a megfelelő eszközt.
   2. A legördülő listában válassza ki szeretne biztonsági másolatot az kötet vagy a biztonsági mentési házirend kiválasztásához.
   3. Adja meg azt az időtartományt.
   4. Kattintson a pipa ikonra ![Pipa ikon](./media/storsimple-manage-backup-catalog/HCS_CheckIcon.png) a lekérdezés végrehajtásához.
      
      A biztonsági mentések társított a kijelölt kötetről, vagy a biztonsági mentési házirend megjelenjen-e a biztonságimásolat-készletek listájában.
3. Válassza ki, és bontsa ki a biztonságimásolat-készletből. A **visszaállítása** és **törlése** beállítások jelennek meg a lap alján. Kattintson a **Törlés** gombra.
4. Meg fog értesítést kapni a Törlés folyamatban van, és ha sikeresen befejeződött. A törlés után frissítse a lekérdezést. ezen a lapon. A törölt biztonságimásolat-készlet nem fog megjelenni a biztonságimásolat-készletek listájában.

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [a biztonságimásolat-katalógus használatával állítsa vissza az eszköz biztonságimásolat-készletből](storsimple-restore-from-backup-set.md).
* Megtudhatja, hogyan [felügyelete a StorSimple eszközt a StorSimple Manager szolgáltatás segítségével](storsimple-manager-service-administration.md).

