---
title: Az Azure-fájlmegosztások biztonsági és biztonsági és biztonsági dokumentációjának biztonsági és biztonsági dokumentációjának biztonsági és biztonsági dokumentációja
description: Megtudhatja, hogy az Azure Portal használatával hogyan lehet biztonsági másolatot adni az Azure-fájlmegosztásokról a Recovery Services-tárolóban
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938204"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Az Azure-fájlmegosztások biztonsági mentése a Recovery Services-tárolóban

Ez a cikk bemutatja, hogyan használhatja az Azure Portalon az [Azure-fájlmegosztások biztonsági.](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)

Ebben a cikkben megtudhatja, hogyan:

* Recovery Services-tároló létrehozása.
* Fedezze fel a fájlmegosztásokat, és konfigurálja a biztonsági másolatokat.
* Futtasson egy igény szerinti biztonsági mentési feladatot egy visszaállítási pont létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

* Azonosítsa vagy hozzon létre egy [Recovery Services-tárolót](#create-a-recovery-services-vault) ugyanabban a régióban, mint a fájlmegosztást tároló tárfiók.
* Győződjön meg arról, hogy a fájlmegosztás a [támogatott tárfióktípusok](#limitations-for-azure-file-share-backup-during-preview)egyikében található.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Az Azure-fájlmegosztás biztonsági mentésének korlátai az előzetes verzióban

Az Azure-fájlmegosztások biztonsági mentése jelenleg előzetes verzióban érhető el. Az Azure-fájlmegosztások az általános célú v1-ben és az általános célú v2-es tárfiókokban is támogatottak. Az Azure-fájlmegosztások biztonsági mentésének korlátai az alábbiak:

* A [zónaredundáns tárolással](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) (ZRS) replikálást biztosító tárfiókokban az Azure-fájlmegosztások biztonsági mentésének támogatása jelenleg ezekre a [régiókra](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares)korlátozódik.
* Az Azure Backup jelenleg támogatja az Azure-fájlmegosztások ütemezett napi egyszeri biztonsági mentésének konfigurálását.
* Az ütemezett biztonsági mentések maximális száma naponta egy.
* Az igény szerinti biztonsági mentések maximális száma naponta négy.
* Használja [az erőforrás-zárolások](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) a tárfiókban a biztonsági mentések véletlen törlésének megakadályozása a Recovery Services-tárolóban.
* Ne törölje az Azure Backup által létrehozott pillanatképeket. A pillanatképek törlése helyreállítási pontok elvesztését vagy visszaállítási hibákat eredményezhet.
* Ne törölje az Azure Backup által védett fájlmegosztásokat. A jelenlegi megoldás törli az Azure Backup által a fájlmegosztás törlése után készített összes pillanatképet, így az összes visszaállítási pont elvész.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Tárolóreplikáció módosítása

Alapértelmezés szerint a tárolók [georedundáns tárolást (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)használnak.

* Ha a tároló az elsődleges biztonsági mentési mechanizmus, azt javasoljuk, hogy grs használata.
* Használhatja [a helyileg redundáns tárolás (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) alacsony költségű lehetőségként.

A tárolóreplikáció típusának módosítása:

1. Az új tárolóban válassza a **Tulajdonságok lehetőséget** a **Beállítások** szakaszban.

1. A **Tulajdonságok** lap **Biztonsági másolat beállítása területén**válassza a **Frissítés**lehetőséget.

1. Jelölje ki a tárolóreplikáció típusát, majd válassza a **Mentés gombot.**

    ![Biztonsági másolat konfigurációjának frissítése](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> A tároló beállítása után nem módosíthatja a tároló replikációs típusát, és biztonsági mentési elemeket tartalmaz. Ha ezt szeretné tenni, újra létre kell hoznia a tárolót.
>

## <a name="discover-file-shares-and-configure-backup"></a>Fájlmegosztások felderítése és biztonsági mentés konfigurálása

1. Az [Azure Portalon](https://portal.azure.com/)nyissa meg a helyreállítási szolgáltatások tároló, amelynek segítségével biztonsági másolatot a fájlmegosztásról.

1. A **Helyreállítási szolgáltatások tárolóirányítópultján** válassza a **+Backup**lehetőséget.

   ![Recovery Services-tároló](./media/backup-afs/recovery-services-vault.png)

    a. A **Biztonsági másolat célja beállításban**adja meg, hogy hol fut a számítási **feladatok?** **Azure**

    ![Az Azure File Share kiválasztása biztonsági mentési célként](./media/backup-afs/backup-goal.png)

    b.  A **Mit szeretne biztonsági másolatot tenni?** válassza az Azure File **Share** elemet a legördülő listából.

    c.  Válassza **a Biztonsági mentés** lehetőséget az Azure-fájlmegosztási bővítmény regisztrálásához a tárolóban.

    ![Válassza a Biztonsági mentés lehetőséget az Azure-fájlmegosztás trezorral való társításához](./media/backup-afs/register-extension.png)

1. Miután a **Biztonsági másolat**lehetőséget, megnyílik a **Biztonsági másolat** ablaktábla, és a felderített támogatott tárfiókok listájából ki kell választania egy tárfiókot. Vagy ehhez a tárolóhoz vannak társítva, vagy ugyanabban a régióban vannak, mint a tároló, de még nem társítva egyetlen Recovery Services-tárolóhoz sem.

   ![Adattároló fiók kiválasztása](./media/backup-afs/select-storage-account.png)

1. A felderített tárfiókok listájából válasszon ki egy fiókot, és válassza az **OK gombot.** Az Azure megkeresi a tárfiókban a fájlmegosztások, amelyek ről lehet biztonsági másolatot. Ha nemrég adta hozzá a fájlmegosztásokat, és nem látja őket a listában, hagyjon egy kis időt a fájlmegosztások megjelenésére.

    ![Fájlmegosztások felderítése](./media/backup-afs/discovering-file-shares.png)

1. A **Fájlmegosztások** listából jelöljön ki egy vagy több olyan fájlmegosztást, amelyről biztonsági másolatot szeretne tenni. Válassza **az OK gombot.**

1. Miután kiválasztotta a fájlmegosztásokat, a **Biztonsági másolat** menü a Biztonsági **másolat házirendre**vált. Ebből a menüből válasszon ki egy meglévő biztonsági mentési házirendet, vagy hozzon létre egy újat. Ezután válassza **a Biztonsági másolat engedélyezése**lehetőséget.

    ![Válassza a Biztonsági mentési házirend lehetőséget](./media/backup-afs/select-backup-policy.png)

A biztonsági mentési házirend beállítása után a fájlmegosztások pillanatképe az ütemezett időpontban készül el. A helyreállítási pont is megmarad a kiválasztott időszakra.

## <a name="create-an-on-demand-backup"></a>Igény szerinti biztonsági másolat létrehozása

Esetenként előfordulhat, hogy biztonsági mentési pillanatképet vagy helyreállítási pontot szeretne létrehozni a biztonsági mentési házirendben ütemezett időpontokon kívül. Az igény szerinti biztonsági mentés létrehozásának gyakori oka közvetlenül a biztonsági mentési házirend konfigurálása után. A biztonsági mentési szabályzat ütemezése alapján lehet, hogy órák vagy napok, amíg egy pillanatkép készül. Annak érdekében, hogy adatai a biztonsági mentési szabályzat elindulásáig is védve legyenek, indítson el egy igény szerinti biztonsági mentést. A fájlmegosztások tervezett módosítása előtt gyakran szükség van igény szerinti biztonsági mentés létrehozására.

### <a name="create-a-backup-job-on-demand"></a>Biztonságimásolat-készítés igény szerint

1. Nyissa meg azt a Helyreállítási szolgáltatások tárolót, amelyet a fájlmegosztás biztonsági mentéséhez használt. Az **Áttekintő** ablaktáblán válassza a **Biztonsági másolat elemek** lehetőséget a Védett **elemek** szakaszban.

   ![Biztonsági másolat elemeinek kijelölése](./media/backup-afs/backup-items.png)

1. Miután a **Biztonsági másolat elemek**lehetőséget választja, az **Áttekintő** ablaktábla mellett megjelenik egy új ablaktábla, amely az összes **biztonságimentés-kezelési típust** felsorolja.

   ![A biztonsági másolat kezelési típusainak listája](./media/backup-afs/backup-management-types.png)

1. A **Biztonsági másolat kezelése típusa** listában válassza az **Azure Storage (Azure Files) lehetőséget.** Ekkor megjelenik az összes fájlmegosztás és a megfelelő tárfiókok listája, amelyről a tároló használatával kell biztonsági másolatot kapni.

   ![Azure Storage (Azure Files) biztonsági mentési elemei](./media/backup-afs/azure-files-backup-items.png)

1. Az Azure-fájlmegosztások listájából válassza ki a kívánt fájlmegosztást. Megjelennek **a biztonsági másolat elemének** részletei. A **Biztonsági másolat segédprogram menüjében** válassza a **Biztonsági mentés parancs át .** Mivel ez a biztonsági mentési feladat igény szerint érhető el, nincs adatmegőrzési szabály a helyreállítási ponthoz társítva.

   ![Válassza a Biztonsági mentés lehetőséget most](./media/backup-afs/backup-now.png)

1. Megnyílik **a Biztonsági mentés most** ablaktábla. Adja meg a helyreállítási pont megőrzésének utolsó napját. Igény szerinti biztonsági mentés esetén legfeljebb 10 év lehet.

   ![Megőrzési dátum kiválasztása](./media/backup-afs/retention-date.png)

1. Válassza **az OK gombot** az igény szerinti biztonsági mentési feladat megerősítéséhez.

1. Figyelje a portál értesítéseit a biztonsági mentési feladat futtatási befejezésének nyomon követéséhez. Figyelheti a feladat előrehaladását a tároló irányítópultján. Válassza a Folyamatban lévő **biztonsági mentési feladatok** > **lehetőséget.**

## <a name="next-steps"></a>További lépések

Az alábbiak végrehajtásának módját ismerheti meg:
* [Azure-fájlmegosztások visszaállítása](restore-afs.md)
* [Azure-fájlmegosztási biztonsági mentések kezelése](manage-afs-backup.md)
