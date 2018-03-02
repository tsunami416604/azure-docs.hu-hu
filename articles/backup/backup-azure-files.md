---
title: "Azure-fájlok biztonsági mentése az Azure-ba"
description: "Ez a cikk ismerteti az Azure-fájlmegosztások biztonsági mentésének és visszaállításának módját, valamint a felügyeleti feladatokat."
services: backup
keywords: "Ne adjon hozzá kulcsszavakat és ne szerkessze azokat a keresőoptimalizálást végző szakemberrel való egyeztetés nélkül."
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: b9bf1582aa1c1b8878b8426f60a18282598eb2b9
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="back-up-azure-file-shares"></a>Azure-fájlmegosztások biztonsági mentése

Ez a cikk részletesen bemutatja az [Azure-fájlmegosztások](../storage/files/storage-files-introduction.md) biztonsági mentését.

Ebből az útmutatóból a következőket tanulhatja meg:
> [!div class="checklist"]
> * Helyreállítási tár konfigurálása Azure Files biztonsági mentéséhez
> * Igény szerinti biztonságimásolat-készítő feladat futtatása visszaállítási pont létrehozásához
> * Fájl vagy fájlok visszaállítása egy visszaállítási pontból
> * Biztonsági mentési feladatok kezelése
> * Az Azure Files védelmének megszüntetése
> * A biztonsági mentési adatok törlése

## <a name="prerequisites"></a>Előfeltételek
Mielőtt biztonsági mentést készít egy Azure-fájlmegosztásról, győződjön meg arról, hogy az a [támogatott tárfióktípusok](troubleshoot-azure-files.md#preview-boundaries) közé tartozik. Ennek ellenőrzését követően biztosíthatja a fájlmegosztások védelmét.

## <a name="configuring-azure-file-shares-backup"></a>Azure-fájlmegosztások biztonsági mentésének konfigurálása

A helyreállítási tárak tartalmazzák az összes biztonsági mentési adatot. Ez az oktatóanyag feltételezi, hogy már létrehozott egy Azure-fájlmegosztást. Az Azure-fájlmegosztás biztonsági mentése:

1. Hozzon létre egy helyreállítási tárat a fájlmegosztással megegyező régióban. Ha már rendelkezik tárolóval, nyissa meg a tároló Áttekintés oldalát, és kattintson **Biztonsági mentés** lehetőségre.

    ![Azure-fájlmegosztás kiválasztása a biztonsági mentés céljaként](./media/backup-file-shares/overview-backup-page.png)

2. A biztonsági mentési célja menüben, a **Miről szeretne biztonsági másolatot készíteni?** elemről válassza ki az Azure-fájlmegosztás lehetőséget.

    ![Azure-fájlmegosztás kiválasztása a biztonsági mentés céljaként](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Kattintson a **Biztonsági mentés** elemre az Azure-fájlmegosztás helyreállítási tárhoz való konfigurálásához. 

   ![Kattintson a Biztonsági mentés elemre az Azure-fájlmegosztás tárolóval történő társításához](./media/backup-file-shares/set-backup-goal.png)

    Miután a tároló társítva van az Azure-fájlmegosztással, megnyílik a Biztonsági mentés menü, és kéri egy tárfiók kiválasztását. A menüben megjelenik a tárral megegyező régió összes olyan támogatott tárfiókja, amely még nincs hozzárendelve helyreállítási tárhoz.

   ![Kattintson a Biztonsági mentés elemre az Azure-fájlmegosztás tárolóval történő társításához](./media/backup-file-shares/list-of-storage-accounts.png)

4. A tárfiókok listájából válasszon ki egy fiókot, majd kattintson az **OK** gombra. Az Azure kikeresi a tárfiók azon fájljait, amelyekről biztonsági mentés készíthető. Ha nemrégiben adta hozzá a fájlmegosztásokat, és emiatt nem látja azokat a listában, hagyjon egy kis időt a fájlmegosztásoknak, hogy megjelenjenek.

   ![Kattintson a Biztonsági mentés elemre az Azure-fájlmegosztás tárolóval történő társításához](./media/backup-file-shares/discover-file-shares.png)

5. A **Fájlmegosztások** listáról válasszon ki egy vagy több fájlmegosztást, amely(ek)ről biztonsági mentést szeretne készíteni, majd kattintson az **OK** gombra.

6. A fájlmegosztások kiválasztását követően a Biztonsági mentés menü átvált a **Biztonsági mentési szabályzat** menüre. Ebben a menüben válasszon egy meglévő biztonsági mentési szabályzatot, vagy hozzon létre egy újat, és kattintson **Biztonsági mentés engedélyezése** lehetőségre. 

   ![Kattintson a Biztonsági mentés elemre az Azure-fájlmegosztás tárolóval történő társításához](./media/backup-file-shares/apply-backup-policy.png)

    A biztonsági mentési szabályzat létrehozása után pillanatkép készül a fájlmegosztásokról az ütemezett időpontban, és a rendszer megőrzi a helyreállítási pontot a választott időszakra vonatkozóan.

## <a name="create-an-on-demand-backup"></a>Igény szerinti biztonsági másolat létrehozása
Esetenként érdemes lehet létrehozni biztonsági mentési pillanatképet vagy helyreállítási pontot a biztonsági mentési szabályzat ütemezett időpontjain kívül is. Igény szerinti biztonsági mentést leggyakrabban a biztonsági mentési szabályzat konfigurálása után hoznak létre. A biztonsági szabályzat ütemezése alapján lehet, hogy órák vagy napok is eltelnek addig, amíg a rendszer pillanatképet készít. Annak érdekében, hogy adatai a biztonsági mentési szabályzat elindulásáig is védve legyenek, indítson el egy igény szerinti biztonsági mentést. Az igény szerinti biztonsági mentésre gyakran akkor is szükség van, ha módosításokat tervez végezni a fájlmegosztásain. 

### <a name="to-create-an-on-demand-backup"></a>Igény szerinti biztonsági másolat létrehozása

1. Nyissa meg a fájlmegosztás helyreállítási pontjait tartalmazó helyreállítási tárat, és kattintson a **Biztonsági másolati elemek** lehetőségre. Ekkor megjelenik a biztonsági mentési elemek típusainak listája.

   ![Kattintson a Biztonsági mentés elemre az Azure-fájlmegosztás tárolóval történő társításához](./media/backup-file-shares/list-of-backup-items.png)

2. Válassza ki a listáról az **Azure Storage (Azure Files)** lehetőséget. Megjelenik az Azure-fájlmegosztások listája.

   ![Kattintson a Biztonsági mentés elemre az Azure-fájlmegosztás tárolóval történő társításához](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Az Azure-fájlmegosztások listájában jelölje ki a kívánt fájlmegosztást. Megnyílik a kijelölt fájlmegosztáshoz tartozó Biztonságimásolat-elem menü.

   ![Kattintson a Biztonsági mentés elemre az Azure-fájlmegosztás tárolóval történő társításához](./media/backup-file-shares/backup-item-menu.png)

4. Kattintson a Biztonságimásolat-elem menü **Biztonsági mentés** lehetőségére. Mivel ez egy igény szerinti biztonsági mentési feladat, nincs társítva adatmegőrzési szabályzat a helyreállítási ponthoz. Megnyílik a **Biztonsági mentés** párbeszédpanel. Adja meg a helyreállítási pont megőrzésének utolsó napját. 
  
   ![Kattintson a Biztonsági mentés elemre az Azure-fájlmegosztás tárolóval történő társításához](./media/backup-file-shares/backup-now-menu.png)

## <a name="restore-from-backup-of-azure-file-share"></a>Azure-fájlmegosztás visszaállítása biztonsági másolatból
Ha vissza kell állítania egy teljes fájlmegosztást, illetve egyes fájlokat vagy mappákat egy helyreállítási pontról, lépjen a Biztonságimásolat-elemre az előző szakaszban leírtak szerint. Válassza a **Megosztás visszaállítása** lehetőséget egy teljes fájlmegosztás egy kívánt időpontból történő visszaállításához. Ekkor megjelenik a visszaállítási pontok listája. Jelöljön ki egyet, hogy felülírhassa a jelenlegi fájlmegosztást, vagy állítsa vissza ezt egy másik fájlmegosztásra ugyanabban a régióban.

   ![Kattintson a Biztonsági mentés elemre az Azure-fájlmegosztás tárolóval történő társításához](./media/backup-file-shares/select-restore-location.png)

## <a name="restore-individual-files-or-folders-from-backup-of-azure-file-shares"></a>Egyes fájlok és mappák visszaállítása az Azure-fájlmegosztások biztonsági másolatából
Az Azure Backup lehetővé teszi az Azure Portalon belüli visszaállítási pontok közötti tallózást. A választott fájl vagy mappa visszaállításához kattintson a Fájlhelyreállítás lehetőségre a Biztonságimásolat-elem lapon, és válasszon ki egy elemet a visszaállítási pontok listájából. Válassza ki a helyreállítás célját, majd kattintson a **Fájl kiválasztása** lehetőségre a visszaállítási pont tallózásához. Jelölje ki a választott fájlt vagy mappát, és kattintson a **Visszaállítás** elemre.

   ![Kattintson a Biztonsági mentés elemre az Azure-fájlmegosztás tárolóval történő társításához](./media/backup-file-shares/restore-individual-files-folders.png)

## <a name="manage-azure-file-share-backups"></a>Azure-fájlmegosztások biztonsági másolatainak kezelése

Különféle, a fájlmegosztási biztonsági másolatokkal kapcsolatos felügyeleti feladatokat hajthat végre a **Biztonsági mentési feladatok** lapon, többek között:
- [Feladatok figyelése](backup-azure-files.md#monitor-jobs)
- [Új szabályzat létrehozása](backup-azure-files.md#create-a-new-policy)
- [Egy fájlmegosztás védelmének leállítása](backup-azure-files.md#stop-protecting-an-azure-file-share)
- [Egy fájlmegosztás védelmének folytatása](backup-azure-files.md#resume-protection-for-azure-file-share)
- [Biztonsági mentési adatok törlése](backup-azure-files.md#delete-backup-data)

### <a name="monitor-jobs"></a>Feladatok figyelése

A **Biztonsági mentési feladatok** lapon monitorozhatja az összes feladat előrehaladását.

A **Biztonsági mentési feladatok** lapot a következő módon nyithatja meg:

- Nyissa meg a monitorozni kívánt helyreállítási tárat, és a helyreállítási tár menüjében kattintson a **Feladatok**, majd a **Biztonsági mentési feladatok** elemekre.
   ![Jelölje ki a monitorozni kívánt feladatot](./media/backup-file-shares/open-backup-jobs.png)

    Megjelenik a biztonsági mentési feladatok és azok állapotának listája.
   ![Jelölje ki a monitorozni kívánt feladatot](./media/backup-file-shares/backup-jobs-progress-list.png)

### <a name="create-a-new-policy"></a>Új szabályzat létrehozása

Új szabályzatot az Azure-fájlmegosztások mentésére vonatkozólag a helyreállítási tár **Biztonsági mentési szabályzatok** menüjében hozhat létre. A fájlmegosztások biztonsági másolatainak konfigurálásakor létrehozott összes szabályzat Azure-fájlmegosztás típusú szabályzatként jelenik meg.

A meglévő biztonsági mentési szabályzatok megtekintéséhez tegye a következőt:

- Nyissa meg a kívánt helyreállítási tárat, a szolgáltatás menüjében pedig kattintson a **Biztonsági mentési szabályzatok** lehetőségre. Az összes biztonsági mentési szabályzat megjelenik listában.

   ![Jelölje ki a monitorozni kívánt feladatot](./media/backup-file-shares/list-of-backup-policies.png)

Új biztonsági mentési szabályzat létrehozásához tegye a következőket:

1. Kattintson a helyreállítási tár menü **Biztonsági mentési szabályzatok** elemére.
2. A biztonsági mentési szabályzatok listájában kattintson a **Hozzáadás** lehetőségre.

   ![Jelölje ki a monitorozni kívánt feladatot](./media/backup-file-shares/new-backup-policy.png)

3. A **Hozzáadás** menüben válassza az **Azure-fájlmegosztás** lehetőséget. Ekkor megnyílik az Azure-fájlmegosztás Biztonsági mentési szabályzat menüje. Adja meg a szabályzat nevét, a biztonsági mentési gyakoriságot és a helyreállítási pontok megőrzési tartományát. Kattintson az OK gombra, miután meghatározta a szabályzatot.

   ![Jelölje ki a monitorozni kívánt feladatot](./media/backup-file-shares/create-new-policy.png)

### <a name="stop-protecting-an-azure-file-share"></a>Azure-fájlmegosztás védelmének megszüntetése

Ha úgy dönt, hogy kikapcsolja egy Azure-fájlmegosztás védelmét, a rendszer megkérdezi, hogy meg kívánja-e őrizni a helyreállítási pontokat. Az Azure-fájlmegosztások védelmét kétféle módon szüntetheti meg:

- állítsa le az összes jövőbeli biztonsági mentési feladatot és törölje az összes helyreállítási pontot, vagy
- állítsa le az összes jövőbeli biztonsági mentési feladatot, de hagyja meg a helyreállítási pontokat.

A helyreállítási pontok tárolóban való megőrzésének lehet költségvonzata, ugyanis a rendszer megőrzi az Azure Backup által létrehozott mögöttes pillanatképeket. Azonban a helyreállítási pontok meghagyásának előnye az, hogy helyreállíthatja a fájlmegosztást később is, ha szükséges. A helyreállítási pontok megőrzésének költségével kapcsolatos információkért tekintse meg az árképzés részleteit. Ha úgy dönt, hogy törli az összes helyreállítási pontot, a fájlmegosztás nem állítható vissza.

Azure-fájlmegosztás védelmének megszüntetése:

1. Nyissa meg a fájlmegosztás helyreállítási pontjait tartalmazó helyreállítási tárat, és kattintson a **Biztonsági másolati elemek** lehetőségre. Ekkor megjelenik a biztonsági mentési elemek típusainak listája.

   ![Kattintson a Biztonsági mentés elemre az Azure-fájlmegosztás tárolóval történő társításához](./media/backup-file-shares/list-of-backup-items.png) 

2. A **Biztonságimásolat-kezelés típusa** listából válassza ki az **Azure Storage (Azure Files)** lehetőséget. Ekkor megjelenik az (Azure Storage (Azure Files)) biztonsági mentési elemek listája.

   ![kattintson az elemre a további menü megnyitásához](./media/backup-file-shares/azure-file-share-backup-items.png) 

3. A biztonsági mentési elemek (Azure Storage (Azure Files)) listájában jelölje ki a megszüntetni kívánt biztonsági mentési elemet.

4. Az Azure-fájlmegosztási elemek között kattintson a **Továbbiak** menüre, és válassza ki a **Biztonsági mentés leállítása** lehetőséget. 

   ![kattintson az elemre a további menü megnyitásához](./media/backup-file-shares/stop-backup.png)

5. A Biztonsági mentés leállítása menüben válassza ki a **Biztonsági másolatok adatainak megőrzése** vagy a **Biztonsági másolatok adatainak törlése** lehetőséget, majd kattintson a **Biztonsági mentés leállítása** elemre.

   ![kattintson az elemre a további menü megnyitásához](./media/backup-file-shares/retain-data.png)

### <a name="resume-protection-for-azure-file-share"></a>Azure-fájlmegosztás védelmének visszaállítása

Ha a Biztonsági másolatok adatainak megőrzése lehetőséget választotta a fájlmegosztás védelmének megszüntetésekor, akkor a védelem később visszaállítható. Ha a **Biztonsági másolatok adatainak törlése** lehetőséget választotta, akkor a fájlmegosztás védelme nem állítható vissza.

A fájlmegosztás védelmének visszaállításához nyissa meg a biztonsági mentési elemet, majd kattintson a Biztonsági mentés folytatása lehetőségre. Ekkor megnyílik a biztonsági mentési szabályzat, és kiválaszthat egy szabályzatot a biztonsági mentés folytatásához.

   ![Jelölje ki a monitorozni kívánt feladatot](./media/backup-file-shares/resume-backup-job.png)

### <a name="delete-backup-data"></a>Biztonsági másolatok adatainak törlése 

Törölheti a fájlmegosztás biztonsági másolatát a Biztonsági mentés leállítása feladat során, vagy a védelem leállítása után bármikor. Érdemes lehet akár napokat vagy heteket is várni a helyreállítási pontok törlése előtt. A helyreállítási pontok visszaállításával ellentétben a biztonsági másolatok adatainak törlésekor nem törölhet meghatározott helyreállítási pontokat. Ha úgy dönt, hogy törli a biztonsági másolatok adatait, azzal törli az elemhez tartozó összes helyreállítási pontot is.

A következő eljárás azt feltételezi, hogy a virtuális gép biztonsági mentési feladata le lett állítva. Ha a biztonsági mentési feladatot leállítja, a Biztonsági mentés folytatása és a Biztonsági másolatok adatainak törlése lehetőségek érhetők el a Biztonsági mentési elem irányítópulton. Kattintson a Biztonsági másolatok adatainak törlése lehetőségre, és írja be a fájlmegosztás nevét a törlés megerősítéséhez. Igény szerint adjon meg egy törlési okot vagy megjegyzést.

## <a name="see-also"></a>Lásd még:
Az Azure-fájlmegosztásokról a következőben talál további információkat:
- [Azure-fájlmegosztás biztonsági mentése – GYIK](backup-azure-files-faq.md)
- [Azure fájlmegosztás biztonsági mentése – hibaelhárítás](troubleshoot-azure-files.md)
