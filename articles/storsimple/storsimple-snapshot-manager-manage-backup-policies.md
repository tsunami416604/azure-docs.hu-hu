---
title: "StorSimple Snapshot Manager biztonsági mentési házirendek |} Microsoft Docs"
description: "Ismerteti a StorSimple Snapshot Manager MMC beépülő modul használatával hozzon létre, és a biztonsági mentési házirend ütemezett biztonsági mentést vezérlő kezeléséhez."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 218c89e403673c16c72da95aa2c1d685bbed5a86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>StorSimple Snapshot Manager segítségével biztonsági szabályzatok létrehozása és kezelése
## <a name="overview"></a>Áttekintés
A biztonsági mentési házirend hoz létre egy ütemezést az adatok biztonsági mentése kötet helyileg vagy a felhőben. A biztonsági mentési házirend létrehozásakor adja meg egy megőrzési házirend. (Legfeljebb 64 pillanatképek őrizheti). További információ a biztonsági mentési házirendek: [biztonsági mentési típusok](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) a [StorSimple 8000 series: hibrid felhőalapú megoldás](storsimple-overview.md).

Ez az oktatóanyag azt ismerteti, hogyan:

* A biztonsági mentési házirend létrehozása
* A biztonsági mentési házirend szerkesztése
* A biztonsági mentési házirend törlése

## <a name="create-a-backup-policy"></a>A biztonsági mentési házirend létrehozása
A következő eljárással hozhat létre egy új biztonsági mentési házirend.

#### <a name="to-create-a-backup-policy"></a>A biztonsági mentési házirend létrehozása
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal **biztonsági mentési házirendek**, és kattintson a **biztonsági mentési házirend létrehozása**.

    ![A biztonsági mentési házirend létrehozása](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    A **házirend létrehozása** párbeszédpanel jelenik meg.

    ![Hozzon létre egy házirend – Általános lap](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Az a **általános** lapra, adja meg a következő adatokat:

   1. Az a **neve** szövegmezőbe írja be a házirend nevét.
   2. Az a **kötet csoport** mezőbe, írja be a kötet csoport nevét a házirendhez társított.
   3. Válassza ki vagy **helyi pillanatfelvétel** vagy **felhőalapú pillanatfelvétel**.
   4. Válassza ki a megőrizni kívánt pillanatképek számát. Ha **minden**, 64 pillanatképek lesz (a maximális) maradnak.
4. Kattintson a **ütemezés** fülre.

    ![Hozzon létre egy házirend - ütemezés lapon](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Az a **ütemezés** lapra, adja meg a következő adatokat:

   1. Kattintson a **engedélyezése** melletti jelölőnégyzetet, hogy a következő biztonsági mentés ütemezése.
   2. A **beállítások**, jelölje be **egyszer**, **napi**, **heti**, vagy **havi**.
   3. Az a **Start** szövegmezőben kattintson a naptár ikonra, és válassza ki a kezdési dátum.
   4. A **speciális beállítások**, beállíthatja a választható ismétlődő ütemezés és a befejező dátumát.
   5. Kattintson az **OK** gombra.

Miután létrehozta a biztonsági mentési házirend, az alábbi információk megjelennek a **eredmények** panelen:

* **Név** – a biztonsági mentési házirend nevét.
* **Típus** – helyi pillanatfelvétel és felhőbeli pillanatfelvétel.
* **Kötet csoport** – a kötet csoport, a házirendhez társított.
* **Megőrzési** – számú pillanatkép maradnak; a maximális 64.
* **Létrehozott** – Ez a házirend létrehozásának dátumát.
* **Engedélyezett** – hogy a házirend van érvényben: **igaz** azt mutatja, hogy érvényes; **Hamis** azt mutatja, hogy nem érvényes.

## <a name="edit-a-backup-policy"></a>A biztonsági mentési házirend szerkesztése
A következő eljárással szerkesztheti a meglévő biztonsági mentési házirend.

#### <a name="to-edit-a-backup-policy"></a>A biztonsági mentési házirend szerkesztése
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblán kattintson a **biztonsági mentési házirendek** csomópont. A biztonsági mentési házirendek megjelennek a **eredmények** ablaktáblán.
3. Kattintson a jobb gombbal a házirend szerkesztése, és kattintson a kívánt **szerkesztése**.

    ![A biztonsági mentési házirend szerkesztése](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Ha a **házirend létrehozása** ablak megjelenik, írja be a módosításokat, és kattintson **OK**.

## <a name="delete-a-backup-policy"></a>A biztonsági mentési házirend törlése
Az alábbi eljárás segítségével törölheti a biztonsági mentési házirend.

#### <a name="to-delete-a-backup-policy"></a>A biztonsági mentési házirend törlése
1. Kattintson az asztal ikonra kattintva indítsa el a StorSimple Snapshot Manager.
2. Az a **hatókör** ablaktáblán kattintson a **biztonsági mentési házirendek** csomópont. A biztonsági mentési házirendek megjelennek a **eredmények** ablaktáblán.
3. Kattintson a jobb gombbal a biztonsági mentési házirend, törlése, és kattintson a kívánt **törlése**.
4. A jóváhagyást kérő üzenet megjelenésekor kattintson **Igen**.

    ![A biztonsági mentési házirend megerősítés törlése](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>Következő lépések
* Megtudhatja, hogyan [StorSimple Snapshot Manager segítségével felügyelheti a StorSimple megoldásban](storsimple-snapshot-manager-admin.md).
* Megtudhatja, hogyan [StorSimple Snapshot Manager segítségével megtekintheti és kezelheti a biztonsági mentési feladatok](storsimple-snapshot-manager-manage-backup-jobs.md).
