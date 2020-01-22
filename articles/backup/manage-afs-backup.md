---
title: Azure-fájlmegosztások biztonsági másolatainak kezelése
description: Ez a cikk a Azure Backup szolgáltatás által biztonsági mentés alatt álló Azure-fájlmegosztás felügyeletének és figyelésének általános feladatait ismerteti.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: a5477d021b6e3600693e183d8707e11592b7cc38
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294551"
---
# <a name="manage-azure-file-share-backups"></a>Azure-fájlmegosztások biztonsági másolatainak kezelése

Ez a cikk a [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) szolgáltatás által biztonsági mentés alatt álló Azure-fájlmegosztás felügyeletének és figyelésének általános feladatait ismerteti. Megtudhatja, hogyan hajthatja végre a következő felügyeleti feladatokat a Recovery Services-tárolóban:

* [Feladatok figyelése](#monitor-jobs)
* [Új szabályzat létrehozása](#create-a-new-policy)
* [Házirend módosítása](#modify-policy)
* [Egy fájlmegosztás védelmének leállítása](#stop-protection-on-a-file-share)
* [Egy fájlmegosztás védelmének folytatása](#resume-protection-on-a-file-share)
* [Biztonsági mentési adatok törlése](#delete-backup-data)
* [Storage-fiók regisztrációjának törlése](#unregister-storage-account)

## <a name="monitor-jobs"></a>Feladatok figyelése

A biztonsági mentési vagy visszaállítási művelet elindításakor a Backup szolgáltatás létrehoz egy feladatot a követéshez. A **Biztonsági mentési feladatok** lapon monitorozhatja az összes feladat előrehaladását.

A **Biztonsági mentési feladatok** lapot a következő módon nyithatja meg:

1. Nyissa meg a fájlmegosztás biztonsági mentésének konfigurálásához használt Recovery Services-tárolót. Az **Áttekintés** panelen kattintson a **figyelés** szakasz **biztonsági mentési feladatok** elemére.

   ![Biztonsági mentési feladatok a figyelés szakaszban](./media/manage-afs-backup/backup-jobs.png)

2. Ha az OK gombra kattint, megjelenik a **biztonsági mentési feladatok** panel, amely felsorolja az összes feladat állapotát. A figyelni kívánt fájlmegosztás megfelelő munkaterhelési nevére kattinthat.

   ![Munkaterhelés neve](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Új szabályzat létrehozása

Létrehozhat egy új szabályzatot az Azure-fájlmegosztás biztonsági mentéséhez a Recovery Services- **tároló biztonsági mentési házirendek** szakaszának használatával. A fájlmegosztás biztonsági mentésének konfigurálásakor létrehozott szabályzatok az Azure-fájlmegosztás mezőben jelennek meg.

A meglévő biztonsági mentési szabályzatok megtekintéséhez tegye a következőt:

1. Nyissa meg a fájlmegosztás biztonsági mentésének konfigurálásához használt Recovery Services-tárolót, és a Recovery Services tároló menüjében kattintson a **biztonsági mentési házirendek** elemre a kezelés szakaszban. A tárolóban konfigurált összes biztonsági mentési szabályzat megjelenik.

   ![Minden biztonsági mentési szabályzat](./media/manage-afs-backup/all-backup-policies.png)

2. Az Azure-fájlmegosztás egyedi házirendjeinek megtekintéséhez válassza az **Azure-fájlmegosztás** lehetőséget a jobb felső sarokban lévő legördülő listából.

   ![Azure-fájlmegosztás kiválasztása](./media/manage-afs-backup/azure-file-share.png)

Új biztonsági mentési szabályzat létrehozásához tegye a következőket:

1. Kattintson a **+ Hozzáadás** elemre a biztonsági mentési házirendek panelen.

   ![Új biztonsági mentési szabályzat](./media/manage-afs-backup/new-backup-policy.png)

2. A **Hozzáadás** panelen válassza az **Azure-fájlmegosztás** lehetőséget a **házirend típusaként** . Ekkor megnyílik az Azure-fájlmegosztás biztonsági mentési szabályzatának panelje. Adja meg a házirend nevét, a biztonsági mentési gyakoriságot és a helyreállítási pontok megőrzési tartományát. A szabályzat meghatározásakor kattintson **az OK** gombra.

   ![A biztonsági mentési szabályzat megadása](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Házirend módosítása

A biztonsági mentési szabályzat módosításával módosíthatja a biztonsági mentési gyakoriságot vagy a megőrzési tartományt.

Szabályzat módosítása:

1. Nyissa meg a fájlmegosztás biztonsági mentésének konfigurálásához használt Recovery Services-tárolót, és a Recovery Services tároló menüjében kattintson a **biztonsági mentési házirendek** elemre a kezelés szakaszban. A tárolóban konfigurált összes biztonsági mentési szabályzat megjelenik.

   ![A tár összes biztonsági mentési szabályzata](./media/manage-afs-backup/all-backup-policies-modify.png)

2. Az Azure-fájlmegosztás specifikus házirendjeinek megtekintéséhez válassza az **Azure-fájlmegosztás** lehetőséget a jobb felső sarokban lévő legördülő listából. Kattintson a módosítani kívánt biztonsági mentési szabályzatra.

   ![A módosítandó Azure-fájlmegosztás](./media/manage-afs-backup/azure-file-share-modify.png)

3. Ekkor megnyílik az **ütemterv** panel. Szerkessze a biztonsági mentés ütemtervét vagy a megőrzési tartományt igény szerint, és kattintson a **Mentés**gombra. Megjelenik egy "frissítés folyamatban" üzenet a panelen, és a szabályzat módosításainak sikeres frissítése után a "a biztonsági mentési szabályzat frissítése sikerült" üzenet jelenik meg.

   ![A módosított szabályzat mentése](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Egy fájlmegosztás védelmének leállítása

Az Azure-fájlmegosztások védelmét kétféle módon szüntetheti meg:

* Az összes jövőbeli biztonsági mentési feladat leállítása és *az összes helyreállítási pont törlése*
* Az összes jövőbeli biztonsági mentési feladat leállítása, de *a helyreállítási pontok elhagyása*

Előfordulhat, hogy a tárolóban lévő helyreállítási pontok elhagyása után a rendszer megőrzi a Azure Backup által létrehozott mögöttes pillanatképeket. Azonban a helyreállítási pontok meghagyásának előnye az, hogy helyreállíthatja a fájlmegosztást később is, ha szükséges. A helyreállítási pontok elhagyása költségével kapcsolatos további információkért tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/backup/). Ha úgy dönt, hogy törli az összes helyreállítási pontot, a fájlmegosztás nem állítható vissza.

Azure-fájlmegosztás védelmének megszüntetése:

1. Nyissa meg a fájlmegosztási helyreállítási pontokat tartalmazó Recovery Services-tárolót, és kattintson a védett elemek szakaszban található **biztonsági másolatok elemre** . Ekkor megjelenik a biztonsági mentési elemek típusainak listája.

   ![Biztonsági másolati elemek](./media/manage-afs-backup/backup-items.png)

2. A **Biztonságimásolat-kezelés típusa** listából válassza ki az **Azure Storage (Azure Files)** lehetőséget. Megjelenik az **(Azure Storage (Azure Files)) biztonsági mentési elemeinek** listája.

   ![Azure Storage kiválasztása (Azure Files)](./media/manage-afs-backup/azure-storage-azure-files.png)

3. A **biztonsági mentési elemek (Azure Storage (Azure Files)** ) listájában válassza ki azt a biztonsági mentési elemet, amelynek a védelmét le szeretné állítani.

4. Válassza a **biztonsági mentés leállítása** lehetőséget a **biztonsági mentési elem** menüjében.

   ![Válassza a biztonsági mentés leállítása lehetőséget.](./media/manage-afs-backup/stop-backup.png)

5. A **biztonsági mentés leállítása** panelen válassza a **biztonsági mentési adat megőrzése** vagy a **biztonsági mentési adat törlése** lehetőséget, majd kattintson a **biztonsági mentés leállítása**elemre.

    ![Válassza a biztonsági mentési adat megőrzése vagy törlése lehetőséget.](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Egy fájlmegosztás védelmének folytatása

Ha a **biztonsági mentési adat megőrzése** beállítást választotta, amikor a fájlmegosztás védelmét leállították, akkor lehetséges a védelem folytatása. Ha a **Biztonsági másolatok adatainak törlése** lehetőséget választotta, akkor a fájlmegosztás védelme nem állítható vissza.

Az Azure-fájlmegosztás védelmének folytatása:

1. Nyissa meg a fájlmegosztási helyreállítási pontokat tartalmazó Recovery Services-tárolót, és kattintson a védett elemek szakaszban található **biztonsági másolatok elemre** . Ekkor megjelenik a biztonsági mentési elemek típusainak listája.

   ![A folytatáshoz tartozó biztonsági mentési elemek](./media/manage-afs-backup/backup-items-resume.png)

2. A **Biztonságimásolat-kezelés típusa** listából válassza ki az **Azure Storage (Azure Files)** lehetőséget. Megjelenik az **(Azure Storage (Azure Files)) biztonsági mentési elemeinek** listája.

   ![Az Azure Storage (Azure Files) listája](./media/manage-afs-backup/azure-storage-azure-files.png)

3. A **biztonsági mentési elemek (Azure Storage (Azure Files)** ) listájában válassza ki azt a biztonsági mentési elemet, amelynek a védelmét folytatni kívánja.

4. Válassza a **biztonsági mentés folytatása** lehetőséget a **biztonsági mentési elem** menüjében.

   ![Válassza a biztonsági mentés folytatása lehetőséget.](./media/manage-afs-backup/resume-backup.png)

5. Ekkor megnyílik a **biztonsági mentési szabályzat** panel, és kiválaszthatja a biztonsági mentés folytatásához választott szabályzatot.

6. A kívánt **biztonsági mentési szabályzat**kiválasztása után kattintson a **Mentés** gombra. Egy "frissítés folyamatban" üzenet jelenik meg a portálon, és a biztonsági mentés sikeres folytatása után megjelenik a "sikeres biztonsági mentési szabályzat frissítése a védett Azure-fájlmegosztás számára" üzenet.

   ![A biztonsági mentési szabályzat frissítése sikerült](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Biztonsági mentési adatok törlése

A fájlmegosztás biztonsági mentését törölheti a **biztonsági mentés leállítása** feladatokban, vagy a védelem leállítását követően bármikor. Érdemes lehet akár napokat vagy heteket is várni a helyreállítási pontok törlése előtt. A biztonsági mentési információk törlésekor nem választhat meghatározott helyreállítási pontokat a törléshez. Ha úgy dönt, hogy törli a biztonsági mentési adatait, a fájlmegosztás összes helyreállítási pontját törli.

A következő eljárás azt feltételezi, hogy a fájlmegosztás védelme le van állítva.

Az Azure-fájlmegosztás biztonsági mentési adatfájljainak törlése:

1. A biztonsági mentési feladatok leállítása után a biztonsági mentés **folytatása** és a **biztonsági mentési adatok törlése** lehetőség a biztonsági mentési **elem** irányítópultján érhető el. Kattintson a biztonsági **másolat törlése** lehetőségre a **biztonsági mentési elem** menüjében.

   ![Biztonsági mentési adatok törlése](./media/manage-afs-backup/delete-backup-data.png)

2. Ekkor megnyílik a **biztonsági másolatok törlése** panel. A törlés megerősítéséhez írja be a fájlmegosztás nevét. Szükség esetén megadhatja a törlés vagy a **Megjegyzés** **okát** . Ha biztos benne, hogy törli a biztonsági mentési adatait, kattintson a **Törlés** gombra.

   ![Az adattörlés megerősítése](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-storage-account"></a>Storage-fiók regisztrációjának törlése

Ha egy másik Recovery Services-tárolóval szeretne védelemmel ellátni egy adott tárolási fiókban lévő fájlmegosztást, először állítsa le a Storage-fiókban lévő [összes fájlmegosztás védelmét](#stop-protection-on-a-file-share) . Ezután törölje a fiók regisztrációját a védelemhez használt aktuális Recovery Services-tárból.

A következő eljárás azt feltételezi, hogy a rendszer leállította a védelmet a regisztrációt megszüntető Storage-fiókban lévő összes fájlmegosztás esetében.

A Storage-fiók regisztrációjának törlése:

1. Nyissa meg a Recovery Services-tárolót, ahol a Storage-fiók regisztrálva van.
2. Kattintson a **biztonsági mentési infrastruktúra** lehetőségre az **Áttekintés** panel **kezelés** területén.

   ![Kattintson a biztonsági mentési infrastruktúra elemre.](./media/manage-afs-backup/backup-infrastructure.png)

3. Megnyílik a **biztonsági mentési infrastruktúra** panel. A panel **Azure Storage-fiókok** szakaszában kattintson a **Storage-fiókok** elemre.

   ![Kattintson a Storage-fiókok elemre.](./media/manage-afs-backup/storage-accounts.png)

4. Miután rákattintott a **Storage-fiókok**elemre, megjelenik a tárolóban regisztrált Storage-fiókok listája.
5. Kattintson a jobb gombbal arra a Storage-fiókra, amelynek regisztrációját törölni szeretné, majd válassza a **Regisztráció törlése**lehetőséget.

   ![Regisztráció törlése](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Következő lépések

További információ: [biztonsági mentési/visszaállítási hibák elhárítása az Azure-fájlmegosztás esetében](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files)
