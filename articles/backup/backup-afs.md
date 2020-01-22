---
title: Azure-fájlmegosztás biztonsági mentése a Azure Portal
description: Ismerje meg, hogyan használhatja a Azure Portal az Azure-fájlmegosztás biztonsági mentésére az Recovery Services-tárolóban
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: b2e2053857147513a95b3ae72b82d55450ebcffa
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294512"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Azure-fájlmegosztás biztonsági mentése egy Recovery Services-tárolóban

Ez a cikk azt ismerteti, hogyan használható a Azure Portal az [Azure-fájlmegosztás](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)biztonsági mentéséhez.

Az útmutató segítségével megtanulhatja a következőket:

* Recovery Services-tároló létrehozása
* Állnak felderítése és biztonsági mentések konfigurálása
* Igény szerinti biztonságimásolat-készítő feladat futtatása visszaállítási pont létrehozásához

## <a name="prerequisites"></a>Előfeltételek

* Azonosítsa vagy hozzon létre egy [Recovery Services](#create-a-recovery-services-vault) tárolót ugyanabban a régióban, mint a fájlmegosztást üzemeltető Storage-fiók.

* Győződjön meg arról, hogy a fájlmegosztás szerepel a [támogatott Storage-fiókok](#limitations-for-azure-file-share-backup-during-preview)egyikében.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Az Azure-fájlmegosztás biztonsági mentésének korlátozásai az előzetes verzióban

Az Azure-fájlmegosztás biztonsági mentése előzetes verzióban érhető el. Az Azure-fájlmegosztás az általános célú v1 és az általános célú v2 Storage-fiókok esetében is támogatott. Az Azure-fájlmegosztás biztonsági mentésének korlátai:

* A Storage-fiókokban tárolt Azure-fájlmegosztás biztonsági mentésének támogatása a [zóna redundáns tárolási](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) (ZRS) replikálásával jelenleg [ezekre a régiókra](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares)korlátozódik.
* A Azure Backup jelenleg az Azure-fájlmegosztás napi biztonsági mentésének ütemezését támogatja.
* Az ütemezett biztonsági mentések maximális száma naponta egy.
* Az igény szerinti biztonsági mentések maximális száma naponta négy.
* Használjon [erőforrászárat](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) a tárfiókon, hogy megelőzze a helyreállítási tárban lévő biztonsági másolatok véletlen törlését.
* Ne törölje az Azure Backuppal létrehozott pillanatképeket. A pillanatképek törlése helyreállítási pontok elvesztését és/vagy visszaállítási hibákat eredményezhet.
* Ne törölje a Azure Backup által védett fájlmegosztást. A jelenlegi megoldás törli az Azure Backup által a fájlmegosztás törlése után készített összes pillanatképet, ezért az összes visszaállítási pont el fog veszni.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Tárolási replikáció módosítása

Alapértelmezés szerint a [tárolók a Geo-redundáns tárolást (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)használják.

* Ha a tároló elsődleges biztonsági mentési mechanizmusa, javasoljuk, hogy használja a GRS-t.

* A [helyileg redundáns tárolás (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) alacsony díjszabású megoldásként használható.

Módosítsa a tárolási replikálás típusát a következőképpen:

1. Az új tárolóban kattintson a **Tulajdonságok** elemre a **Beállítások** szakaszban.

2. A **Tulajdonságok**alatt a **biztonsági mentés konfigurálása**területen kattintson a **frissítés**elemre.

3. Válassza ki a tárolási replikálás típusát, majd kattintson a **Mentés**gombra.

    ![Biztonsági mentési konfiguráció frissítése](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> A tárolási replikálási típus nem módosítható a tároló beállítása és a biztonsági mentési elemek létrehozása után. Ha ezt szeretné tenni, újra létre kell hoznia a tárolót.
>

## <a name="discover-file-shares-and-configure-backup"></a>Fájlmegosztás felderítése és a biztonsági mentés konfigurálása

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a fájlmegosztás biztonsági mentéséhez használni kívánt Recovery Services-tárolót.

2. A **Recovery Services** -tároló irányítópultján válassza a **+ biztonsági mentés**lehetőséget.

   ![Recovery Services-tároló](./media/backup-afs/recovery-services-vault.png)

   a. A **biztonsági mentés célja**beállításnál állítsa be, hogy **hol fut a** számítási feladat? az **Azure**-ban.

    ![Az Azure-fájlmegosztás biztonsági mentési célként való kiválasztása](./media/backup-afs/backup-goal.png)

    b.    A **Miről szeretne biztonsági másolatot készíteni**, válassza ki az **Azure-fájlmegosztás** lehetőséget a legördülő menüből.

    c.    Kattintson a biztonsági mentés elemre az Azure-fájlmegosztás bővítménynek a **tárolóban** való regisztrálásához.

      ![Kattintson a biztonsági mentés elemre az Azure-fájlmegosztás tárolóval való hozzárendeléséhez](./media/backup-afs/register-extension.png)

3. Ha a **biztonsági mentés**gombra kattint, megnyílik a biztonsági mentés panel, és megkéri, hogy válasszon ki egy Storage-fiókot a felderített támogatott Storage-fiókok listájából. Ezek a tárolóhoz vannak társítva, vagy ugyanabban a régióban találhatók, mint a tároló, de még nincs hozzárendelve egyetlen Recovery Services-tárolóhoz sem.

   ![Adattároló fiók kiválasztása](./media/backup-afs/select-storage-account.png)

4. A felderített Storage-fiókok listájából válasszon ki egy fiókot, majd kattintson **az OK**gombra. Az Azure kikeresi a tárfiók azon fájljait, amelyekről biztonsági mentés készíthető. Ha nemrég adta hozzá a fájlmegosztást, és nem látja őket a listában, adjon meg egy kis időt a fájlmegosztás megjelenéséhez.

    ![Fájlmegosztás felfedése](./media/backup-afs/discovering-file-shares.png)

5. A **fájlmegosztás** listából válasszon ki egy vagy több olyan fájlmegosztást, amelyről biztonsági másolatot szeretne készíteni, majd kattintson **az OK**gombra.

6. A fájlmegosztás kiválasztása után a **Backup** menü a biztonsági **mentési szabályzatra**vált. Ebből a menüből válasszon ki egy meglévő biztonsági mentési szabályzatot, vagy hozzon létre egy újat, majd kattintson a **biztonsági mentés engedélyezése**elemre.

    ![Biztonsági mentési házirend kiválasztása](./media/backup-afs/select-backup-policy.png)

A biztonsági mentési szabályzat létrehozása után pillanatkép készül a fájlmegosztásokról az ütemezett időpontban, és a rendszer megőrzi a helyreállítási pontot a választott időszakra vonatkozóan.

## <a name="create-an-on-demand-backup"></a>Igény szerinti biztonsági másolat létrehozása

Időnként előfordulhat, hogy biztonsági mentési pillanatképet vagy helyreállítási pontot szeretne készíteni a biztonsági mentési házirendben ütemezett időpontokon kívül. Az igény szerinti biztonsági mentés gyakori oka, hogy a biztonsági mentési szabályzatot már konfigurálta. A biztonsági szabályzat ütemezése alapján lehet, hogy órák vagy napok is eltelnek addig, amíg a rendszer pillanatképet készít. Annak érdekében, hogy adatai a biztonsági mentési szabályzat elindulásáig is védve legyenek, indítson el egy igény szerinti biztonsági mentést. Szükség van egy igény szerinti biztonsági mentés létrehozására, mielőtt tervezett változtatásokat hajt végre a fájlmegosztás számára.

### <a name="to-create-an-on-demand-backup"></a>Igény szerinti biztonsági másolat létrehozása

1. Nyissa meg a fájlmegosztás biztonsági mentéséhez használt Recovery Services-tárolót, majd kattintson az **Áttekintés** panel **védett elemek** szakaszában található **biztonsági másolati elemek elemre** .

   ![Kattintson a biztonsági másolati elemek elemre](./media/backup-afs/backup-items.png)

2. Ha a **biztonsági másolati elemek**elemre kattint, megjelenik egy új panel, amely felsorolja az összes **biztonsági mentési felügyeleti típust** az **Áttekintés** panel mellett, az alábbiak szerint:

   ![A biztonságimásolat-felügyeleti típusok listája](./media/backup-afs/backup-management-types.png)

3. A **biztonságimásolat-kezelési típusok**listájából válassza az **Azure Storage (Azure Files)** lehetőséget. Ekkor megjelenik az összes fájlmegosztás és a hozzájuk tartozó, a tároló használatával készített biztonsági másolat.

   ![Azure Storage-(Azure Files-) biztonsági másolati elemek](./media/backup-afs/azure-files-backup-items.png)

4. Az Azure-fájlmegosztások listájában jelölje ki a kívánt fájlmegosztást. Megjelenik a **biztonsági mentési elemek** részletei. A **biztonsági mentési elem** menüben kattintson a **biztonsági mentés most**elemre. Mivel ez egy igény szerinti biztonsági mentési feladat, nincs társítva adatmegőrzési szabályzat a helyreállítási ponthoz.

   ![Kattintson a biztonsági mentés most lehetőségre](./media/backup-afs/backup-now.png)

5. Megnyílik a **biztonsági mentés** panel. Adja meg a helyreállítási pont megőrzésének utolsó napját. Igény szerinti biztonsági mentés esetén 10 évig is megtarthatja a maximumot.

   ![Megőrzési dátum kiválasztása](./media/backup-afs/retention-date.png)

6. Az igény szerinti biztonsági mentési feladatok futtatásának megerősítéséhez kattintson **az OK** gombra.

7. A portál értesítéseinek figyelésével nyomon követheti a biztonsági mentési feladatok futtatásának befejezését. A feladat előrehaladását a tároló irányítópultján követheti nyomon > a **biztonsági mentési feladatok** > **folyamatban**van.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [állíthatja vissza az Azure-fájlmegosztást](restore-afs.md)

* Ismerje meg, hogyan [kezelheti az Azure-fájlmegosztás biztonsági másolatait](manage-afs-backup.md)
