---
title: Azure-fájlmegosztások biztonsági másolatainak kezelése
description: Ez a cikk az Azure Backup által biztonsági másolatot készítő Azure-fájlmegosztások kezelésével és figyelésével kapcsolatos gyakori feladatokat ismerteti.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: cb764fa441c063328dc350cf26f42c5bc7a0ca99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247656"
---
# <a name="manage-azure-file-share-backups"></a>Azure-fájlmegosztások biztonsági másolatainak kezelése

Ez a cikk az Azure Backup által biztonsági másolatot készítő Azure-fájlmegosztások kezelésével és figyelésével kapcsolatos gyakori feladatokat [ismerteti.](https://docs.microsoft.com/azure/backup/backup-overview) Megtudhatja, hogyan kell felügyeleti feladatokat a Recovery Services tárolóban.

## <a name="monitor-jobs"></a>Feladatok figyelése

Amikor biztonsági mentési vagy visszaállítási műveletet indít el, a biztonsági mentési szolgáltatás létrehoz egy feladatot a nyomon követéshez. A **Biztonsági mentési feladatok** lapon monitorozhatja az összes feladat előrehaladását.

A **Biztonsági mentési feladatok** lapot a következő módon nyithatja meg:

1. Nyissa meg azt a Helyreállítási szolgáltatások tárolót, amelyet a fájlmegosztások biztonsági másolatának konfigurálásához használt. Az **Áttekintő** ablaktáblán válassza a **Biztonsági mentési feladatok** lehetőséget a **Figyelés** szakaszban.

   ![Biztonsági mentési feladatok a figyelési szakaszban](./media/manage-afs-backup/backup-jobs.png)

1. Az **OK**lehetőség kiválasztása után a **Biztonsági másolat ablaktábla** felsorolja az összes feladat állapotát. Válassza ki a figyelni kívánt fájlmegosztásnak megfelelő számítási feladatok nevét.

   ![Számítási feladatok neve](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Új szabályzat létrehozása

Létrehozhat egy új szabályzatot az Azure-fájlmegosztások biztonsági mentéséhez a Recovery Services-tároló **Biztonsági mentési szabályzatok** szakaszából. A fájlmegosztások biztonsági mentésének konfigurálásakor létrehozott összes házirend **megjelenik**az Azure File Share **házirendtípussal.**

A meglévő biztonsági mentési házirendek megtekintése:

1. Nyissa meg azt a Helyreállítási szolgáltatások tárolót, amelya fájlmegosztás biztonsági mentésének konfigurálásához használt. A Helyreállítási szolgáltatások tárolójának menüjében válassza a **Biztonsági mentési házirendek** lehetőséget a **Kezelés** szakaszban. A tárolóban konfigurált összes biztonsági mentési házirend megjelenik.

   ![Az összes biztonsági mentési házirend](./media/manage-afs-backup/all-backup-policies.png)

1. Az **Azure File Share-re**vonatkozó szabályzatok megtekintéséhez válassza az **Azure Fájlmegosztás** elemet a jobb felső sarokban található legördülő listából.

   ![Az Azure-fájlmegosztás kiválasztása](./media/manage-afs-backup/azure-file-share.png)

Új biztonságimentési házirend létrehozása:

1. A **Biztonsági másolat házirendek** ablaktábláján válassza a **+ Hozzáadás lehetőséget.**

   ![Új biztonsági mentési házirend](./media/manage-afs-backup/new-backup-policy.png)

1. A **Hozzáadás** ablaktáblán válassza az **Azure File Share (Azure Fájlmegosztás)** **lehetőséget házirendtípusként.** Megnyílik az Azure File Share **biztonsági mentési házirend** ablaktáblája. **Azure File Share** Adja meg a helyreállítási pontok házirend nevét, biztonsági mentési gyakoriságát és megőrzési tartományát. A házirend definiálása után válassza az **OK gombot.**

   ![A biztonsági mentési házirend meghatározása](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Házirend módosítása

A biztonsági mentési házirend módosításával módosíthatja a biztonsági mentés gyakoriságát vagy a megőrzési tartományt.

Házirend módosítása:

1. Nyissa meg azt a Helyreállítási szolgáltatások tárolót, amelya fájlmegosztás biztonsági mentésének konfigurálásához használt. A Helyreállítási szolgáltatások tárolójának menüjében válassza a **Biztonsági mentési házirendek** lehetőséget a **Kezelés** szakaszban. A tárolóban konfigurált összes biztonsági mentési házirend megjelenik.

   ![Az összes biztonsági mentési házirend a tárolóban](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Az Azure-fájlmegosztásra vonatkozó szabályzatok megtekintéséhez válassza az **Azure Fájlmegosztás** elemet a jobb felső sarokban található legördülő listából. Jelölje ki a módosítani kívánt biztonságimentési házirendet.

   ![Az Azure fájlmegosztás módosítása](./media/manage-afs-backup/azure-file-share-modify.png)

1. Megnyílik **az Ütemezés** ablaktábla. Szükség szerint szerkesztheti a **Biztonsági másolat ütemezését** és **megőrzési tartományát,** majd válassza a **Mentés gombot.** Megjelenik egy "Frissítés folyamatban" üzenet az ablaktáblán. A házirend-módosítások sikeres frissítése után megjelenik a "Sikeresen frissített biztonsági mentési házirend" üzenet.

   ![A módosított házirend mentése](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Egy fájlmegosztás védelmének leállítása

Az Azure-fájlmegosztások védelmét kétféle módon szüntetheti meg:

* Állítsa le az összes jövőbeli biztonsági mentési feladatot, és *törölje az összes helyreállítási pontot.*
* Állítsa le az összes jövőbeli biztonsági mentési feladatot, de *hagyja a helyreállítási pontokat*.

Előfordulhat, hogy a helyreállítási pontok tárolása során a helyreállítási pontok at, mert az Azure Backup által létrehozott alapul szolgáló pillanatképek megmaradnak. A helyreállítási pontok elhagyása azzal az előnnyel jár, hogy később visszaállíthatja a fájlmegosztást. A helyreállítási pontok elhagyásának költségeiről az [árképzés részleteiben](https://azure.microsoft.com/pricing/details/backup/)talál további információt. Ha úgy dönt, hogy törli az összes helyreállítási pontot, nem tudja visszaállítani a fájlmegosztást.

Azure-fájlmegosztás védelmének megszüntetése:

1. Nyissa meg a fájlmegosztás-helyreállítási pontokat tartalmazó Helyreállítási szolgáltatások tárolóját. A Védett elemek szakaszban válassza a Biztonsági másolat **elemeit.** **Backup Items** Megjelenik a biztonságimásolat-listaelem-típusok listája.

   ![Biztonsági másolat elemei](./media/manage-afs-backup/backup-items.png)

1. A **Biztonságimásolat-kezelés típusa** listából válassza ki az **Azure Storage (Azure Files)** lehetőséget. Megjelenik **a biztonsági mentési elemek (Azure Storage (Azure Files))** listája.

   ![Válassza ki az Azure Storage (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. A **biztonsági mentési elemek (Azure Storage (Azure Files))** listában válassza ki azt a biztonsági mentési elemet, amelyhez le szeretné állítani a védelmet.

1. Válassza a **Biztonsági mentés leállítása** lehetőséget.

   ![Válassza a Biztonsági mentés leállítása lehetőséget](./media/manage-afs-backup/stop-backup.png)

1. A **Biztonsági másolat leállítása** ablaktáblán jelölje be a **Biztonsági másolat adatainak megőrzése** vagy **a Biztonsági másolat adatainak törlése jelölőnégyzetet.** Ezután válassza **a Biztonsági mentés leállítása**lehetőséget.

    ![Biztonsági másolat adatainak megőrzése vagy biztonsági másolat adatainak törlése](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Egy fájlmegosztás védelmének folytatása

Ha a **Biztonsági másolat adatainak megőrzése** beállítás a fájlmegosztás védelmének leállításakor lett bejelölve, lehetséges a védelem folytatása. Ha a **Biztonsági másolat adatainak törlése** beállítás van megadva, a fájlmegosztás védelme nem folytatható.

Az Azure-fájlmegosztás védelmének folytatása:

1. Nyissa meg a fájlmegosztás-helyreállítási pontokat tartalmazó Helyreállítási szolgáltatások tárolóját. A Védett elemek szakaszban válassza a Biztonsági másolat **elemeit.** **Backup Items** Megjelenik a biztonságimásolat-listaelem-típusok listája.

   ![Biztonsági másolat elemei a folytatáshoz](./media/manage-afs-backup/backup-items-resume.png)

1. A **Biztonságimásolat-kezelés típusa** listából válassza ki az **Azure Storage (Azure Files)** lehetőséget. Megjelenik **a biztonsági mentési elemek (Azure Storage (Azure Files))** listája.

   ![Az Azure Storage listája (Azure-fájlok)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. A **biztonsági mentési elemek (Azure Storage (Azure Files))** listában válassza ki azt a biztonsági mentési elemet, amelyhez folytatni szeretné a védelmet.

1. Válassza a **Biztonsági mentés folytatása** lehetőséget.

   ![Válassza a Biztonsági mentés folytatása lehetőséget](./media/manage-afs-backup/resume-backup.png)

1. Megnyílik **a Biztonsági másolat házirend ablaktáblája.** Válassza ki a biztonsági mentés folytatásához választott házirendet.

1. Miután kiválasztotta a biztonsági mentési házirendet, válassza a **Mentés lehetőséget.** A portálon megjelenik egy "Frissítés folyamatban" üzenet. Miután a biztonsági mentés sikeresen folytatódott, megjelenik a "Sikeresen frissített biztonsági mentési szabályzat a védett Azure-fájlmegosztáshoz" üzenet jelenik meg.

   ![Sikeresen frissített biztonsági mentési házirend](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Biztonsági mentési adatok törlése

Törölheti a fájlmegosztás biztonsági másolatát a **Biztonsági mentés leállítása,** illetve a védelem leállítása után bármikor. Hasznos lehet napokat vagy akár heteket várni a helyreállítási pontok törlése előtt. A biztonsági mentési adatok törlésekor nem választhat ki törölni kívánt helyreállítási pontokat. Ha úgy dönt, hogy törli a biztonsági mentési adatokat, akkor a fájlmegosztáshoz társított összes helyreállítási pontot törli.

A következő eljárás feltételezi, hogy a fájlmegosztás védelme leállt.

Az Azure-fájlmegosztás biztonsági mentési adatainak törlése:

1. A biztonsági mentési feladat leállítása után a **Biztonsági másolat és** a Biztonsági másolat **adatainak törlése** beállítás elérhető a Biztonsági másolat **irányítópultján.** Válassza a **Biztonsági másolat adatainak törlése** lehetőséget.

   ![Biztonsági mentési adatok törlése](./media/manage-afs-backup/delete-backup-data.png)

1. Megnyílik **a Biztonsági másolat adatainak törlése** ablaktábla. A törlés megerősítéséhez adja meg a fájlmegosztás nevét. További információt az **Ok** vagy a **Megjegyzések** mezőben adhat meg. Miután biztos annektált a biztonsági mentési adatok törlésében, válassza a **Törlés**lehetőséget.

   ![Törlési adatok megerősítése](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Tárfiók regisztrációjának megszüntetése

Egy adott tárfiók fájlmegosztások védelme egy másik helyreállítási szolgáltatások tárolóhasználatával, először [állítsa le a védelmet az adott tárfiók összes fájlmegosztása.](#stop-protection-on-a-file-share) Ezután törölje a fiókot a védelemhez használt aktuális helyreállítási szolgáltatások tárolójából.

A következő eljárás feltételezi, hogy a regisztrációról lekívánandó tárfiók összes fájlmegosztásának védelme leállt.

A tárfiók regisztrációjának megszüntetése:

1. Nyissa meg a Helyreállítási szolgáltatások tárolóját, ahol a tárfiók regisztrálva van.
1. Az **Áttekintő** ablaktáblán válassza a **Biztonsági másolat infrastruktúra** lehetőséget a **Kezelés** szakaszban.

   ![Biztonsági mentési infrastruktúra kiválasztása](./media/manage-afs-backup/backup-infrastructure.png)

1. Megnyílik **a Biztonsági másolat infrastruktúrája** ablaktábla. Válassza **a tárfiókok** az **Azure Storage-fiókok** szakaszban.

   ![Tárfiókok kiválasztása](./media/manage-afs-backup/storage-accounts.png)

1. Miután **kiválasztotta a Tárfiókok lehetőséget,** megjelenik a tárolóval regisztrált tárfiókok listája.
1. Kattintson a jobb gombbal a regisztrációmegszüntetéséhez kívánt tárfiókra, és válassza **a Regisztráció megszüntetése parancsot.**

   ![Regisztráció megszüntetése jelölés e kijelölése](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>További lépések

További információt az [Azure-fájlmegosztások biztonsági másolatának elhárítása című témakörben](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files)talál.
