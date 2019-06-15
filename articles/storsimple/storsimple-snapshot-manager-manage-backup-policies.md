---
title: A StorSimple Snapshot Manager biztonsági mentési szabályzatok |} A Microsoft Docs
description: Ismerteti, hogyan hozhat létre, és az ütemezett biztonsági mentések szabályozó biztonsági mentési házirendek kezelése a StorSimple Snapshot Manager MMC beépülő modul használatához.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: v-sharos
ms.openlocfilehash: 7dac26b058b959011e38b4373369b8a1115d2705
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64687280"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>A StorSimple Snapshot Manager segítségével létrehozhat, és a biztonsági mentési házirendek kezelése
## <a name="overview"></a>Áttekintés
Biztonsági mentési szabályzat helyi vagy felhőbeli biztonsági mentéséről adatmennyiség ütemezés hoz létre. Biztonsági mentési szabályzat létrehozásakor megadhat egy adatmegőrzési szabályt is. (Legfeljebb 64 pillanatképek is megőrizheti.) További információ a biztonsági mentési szabályzatok: [biztonsági mentési típusok](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) a [a StorSimple 8000 sorozat: egy hibrid felhőmegoldás](storsimple-overview.md).

Ez az oktatóanyag a következőket ismerteti:

* A biztonsági mentési szabályzat létrehozása
* Biztonsági mentési szabályzat szerkesztése
* A biztonsági mentési szabályzat törlése

## <a name="create-a-backup-policy"></a>A biztonsági mentési szabályzat létrehozása
A következő eljárással hozhat létre egy új biztonsági mentési szabályzat.

#### <a name="to-create-a-backup-policy"></a>Biztonsági mentési szabályzat létrehozása
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán kattintson a jobb gombbal **biztonsági mentési házirendek**, és kattintson a **biztonsági mentési házirend létrehozása**.

    ![A biztonsági mentési szabályzat létrehozása](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    A **hozzon létre egy házirendet** párbeszédpanel jelenik meg.

    ![Hozzon létre egy házirendet – Általános lap](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Az a **általános** lapra, végezze el a következő információkat:

   1. Az a **neve** szövegmezőbe írja be a házirend nevét.
   2. Az a **kötetcsoport** beviteli mező, írja be a szabályzathoz társított a kötet csoport neve.
   3. Ezek közül bármelyikre **helyi pillanatkép** vagy **Felhőbeli pillanatkép**.
   4. Válassza ki a megőrizni a pillanatképek számát. Ha **összes**, 64 pillanatképek lesz (maximum) őrződnek meg.
4. Kattintson a **ütemezés** fülre.

    ![Hozzon létre egy házirend - Ütemezés lap](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Az a **ütemezés** lapra, végezze el a következő információkat:

   1. Kattintson a **engedélyezése** melletti jelölőnégyzetet, hogy a következő biztonsági mentés ütemezése.
   2. A **beállítások**válassza **egyszer**, **napi**, **heti**, vagy **havi**.
   3. Az a **Start** szövegbeviteli mezőben kattintson a naptár ikonra, és válassza ki a kezdő dátum.
   4. A **speciális beállítások**, opcionális ismétlési ütemezések és a befejező dátum lehet beállítani.
   5. Kattintson az **OK** gombra.

Miután létrehozott egy biztonsági mentési szabályzatot, a következő információkat megjelenik a **eredmények** panelen:

* **Név** – biztonsági mentési szabályzat neve.
* **Típus** – helyi pillanatfelvétel és felhőbeli pillanatkép.
* **Kötetcsoport** – a kötet csoport a szabályzathoz társított.
* **Adatmegőrzési** – pillanatképek számának őrződnek meg; a maximális 64.
* **Létrehozott** – Ez a szabályzat létrehozásának dátumát.
* **Engedélyezett** – -e a házirend van érvényben: **Igaz** jelzi, hogy érvényben; **Hamis** azt jelzi, hogy azt ne legyen hatással.

## <a name="edit-a-backup-policy"></a>Biztonsági mentési szabályzat szerkesztése
Az alábbi eljárás segítségével egy meglévő biztonsági mentési szabályzatot.

#### <a name="to-edit-a-backup-policy"></a>Biztonsági mentési szabályzat szerkesztése
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán kattintson a **biztonsági mentési házirendek** csomópont. A biztonsági mentési házirendek megjelennek a **eredmények** ablaktáblán.
3. Kattintson a jobb gombbal a szabályzat szerkesztéséhez, és kattintson a kívánt **szerkesztése**.

    ![Biztonsági mentési szabályzat szerkesztése](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Ha a **hozzon létre egy házirendet** ablak, írja be a módosításokat, és kattintson a **OK**.

## <a name="delete-a-backup-policy"></a>A biztonsági mentési szabályzat törlése
Az alábbi eljárás segítségével egy biztonsági mentési szabályzat törlése.

#### <a name="to-delete-a-backup-policy"></a>Biztonsági mentési szabályzat törlése
1. Kattintson az asztali ikonra a StorSimple Snapshot Manager elindításához.
2. Az a **hatókör** ablaktáblán kattintson a **biztonsági mentési házirendek** csomópont. A biztonsági mentési házirendek megjelennek a **eredmények** ablaktáblán.
3. Kattintson a jobb gombbal a biztonsági mentési szabályzat törlése, és kattintson a kívánt **törlése**.
4. A megerősítést kérő üzenet megjelenésekor kattintson **Igen**.

    ![Megerősítés a biztonsági mentési szabályzat törlése](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [felügyelete a StorSimple megoldás StorSimple Snapshot Manager használata](storsimple-snapshot-manager-admin.md).
* Ismerje meg, hogyan [biztonsági mentési feladatok megtekintése és kezelése a StorSimple Snapshot Manager használata](storsimple-snapshot-manager-manage-backup-jobs.md).
