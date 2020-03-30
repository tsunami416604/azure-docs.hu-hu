---
title: StorSimple Snapshot Manager biztonsági mentési házirendek | Microsoft dokumentumok
description: Ez a témakör azt ismerteti, hogy a StorSimple Snapshot Manager MMC beépülő modul használatával hogyan hozhat létre és kezelhetők az ütemezett biztonsági mentések vezérlésére szolgáló biztonsági mentési házirendek.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 04415d0b-42f0-4737-8afa-257fb2dbe5d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: twooley
ms.openlocfilehash: 7b2df6c8384e78094d80a17d8982b0db9edf73e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933355"
---
# <a name="use-storsimple-snapshot-manager-to-create-and-manage-backup-policies"></a>Biztonságimentési házirendek létrehozása és kezelése a StorSimple Snapshot Manager segítségével
## <a name="overview"></a>Áttekintés
A biztonsági mentési szabályzat ütemezést hoz létre a kötetadatok helyi vagy felhőbeli biztonsági mentéséhez. Biztonsági mentési házirend létrehozásakor adatmegőrzési szabályzatot is megadhat. (Legfeljebb 64 pillanatképet őrizhet meg.) A biztonsági mentési szabályzatokról a [StorSimple 8000 sorozat](storsimple-overview.md) [biztonsági másolatai:](storsimple-what-is-snapshot-manager.md#backup-types-and-backup-policies) hibrid felhőalapú megoldás című témakörben talál további információt.

Ez az oktatóanyag a következőket ismerteti:

* Biztonsági mentési házirend létrehozása
* Biztonságimentési házirend szerkesztése
* Biztonságimentési házirend törlése

## <a name="create-a-backup-policy"></a>Biztonsági mentési házirend létrehozása
Új biztonságimentési házirend létrehozásához kövesse az alábbi eljárást.

#### <a name="to-create-a-backup-policy"></a>Biztonsági mentési házirend létrehozása
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblán kattintson a jobb gombbal a **Biztonsági másolat házirendjei**elemre, majd kattintson **a Biztonságimásolat-házirend létrehozása parancsra.**

    ![Biztonsági mentési házirend létrehozása](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_BU_policy.png)

    Megjelenik **a Házirend létrehozása** párbeszédpanel.

    ![Házirend létrehozása – Általános lap](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_general.png)
3. Az **Általános** lapon töltse ki a következő információkat:

   1. A **Név** mezőbe írja be a házirend nevét.
   2. A **Kötetcsoport** mezőbe írja be a házirendhez társított kötetcsoport nevét.
   3. Válassza a **Helyi pillanatkép** vagy **a Felhőbeli pillanatkép lehetőséget.**
   4. Válassza ki a megőrzésre kívánt pillanatképek számát. Ha az **Összes**lehetőséget választja, 64 pillanatkép marad meg (ez a maximális érték).
4. Kattintson az **Ütemezés** lapra.

    ![Házirend létrehozása – Ütemezés lap](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Create_policy_schedule.png)
5. Az **Ütemezés** lapon töltse ki a következő információkat:

   1. A következő biztonsági mentés ütemezéséhez jelölje be az **Engedélyezés** jelölőnégyzetet.
   2. A **Beállítások csoportban**válassza az **Egyszer**, **A Napi**, **heti**vagy **a Havi**lehetőséget.
   3. A **Start** szövegmezőben kattintson a naptár ikonra, és válassza ki a kezdési dátumot.
   4. A **Speciális beállítások csoportban**választható ismétlési ütemezéseket és befejezési dátumot állíthat be.
   5. Kattintson az **OK** gombra.

A biztonsági mentési házirend létrehozása után a következő információk jelennek meg az **Eredmények** ablaktáblán:

* **Név** – a biztonsági mentési házirend neve.
* **Típus** – helyi pillanatkép vagy felhőbeli pillanatkép.
* **Kötetcsoport** – a házirendhez társított kötetcsoport.
* **Megőrzés** – a megőrzött pillanatképek száma; a maximális érték 64.
* **Létrehozva** – a házirend létrehozásának dátuma.
* **Engedélyezve** – a házirend érvényben van-e: **Az Igaz érték** azt jelzi, hogy érvényben van; **A Hamis** azt jelzi, hogy nincs érvényben.

## <a name="edit-a-backup-policy"></a>Biztonságimentési házirend szerkesztése
Az alábbi eljárással szerkeszti a meglévő biztonsági mentési házirendet.

#### <a name="to-edit-a-backup-policy"></a>Biztonságimásolat-készítési házirend szerkesztése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblában kattintson a **Biztonsági másolat házirendek** csomópontra. Az összes biztonsági mentési házirend megjelenik az **Eredmények** ablaktáblán.
3. Kattintson a jobb gombbal a szerkesztendő házirendre, majd kattintson a **Szerkesztés parancsra.**

    ![Biztonságimentési házirend szerkesztése](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Edit_BU_policy.png)
4. Amikor megjelenik a **Házirend létrehozása** ablak, írja be a módosításokat, majd kattintson **az OK**gombra.

## <a name="delete-a-backup-policy"></a>Biztonságimentési házirend törlése
A biztonsági másolat házirendjének törléséhez kövesse az alábbi eljárást.

#### <a name="to-delete-a-backup-policy"></a>Biztonságimásolat-készítési házirend törlése
1. Kattintson az asztal ikonra a StorSimple Snapshot Manager elindításához.
2. A **Hatókör** ablaktáblában kattintson a **Biztonsági másolat házirendek** csomópontra. Az összes biztonsági mentési házirend megjelenik az **Eredmények** ablaktáblán.
3. Kattintson a jobb gombbal a törölni kívánt biztonságimásolat-házirendre, majd kattintson a **Törlés parancsra.**
4. Amikor megjelenik a megerősítő üzenet, kattintson az **Igen**gombra.

    ![Biztonsági mentési házirend megerősítésének törlése](./media/storsimple-snapshot-manager-manage-backup-policies/HCS_SSM_Delete_BU_policy.png)

## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [kezelheti a StorSimple snapshot kezelőt a StorSimple megoldás felügyeletéhez.](storsimple-snapshot-manager-admin.md)
* Ismerje meg, hogyan tekintheti meg [és kezelheti a biztonsági mentési feladatokat a StorSimple Snapshot Manager](storsimple-snapshot-manager-manage-backup-jobs.md)segítségével.
