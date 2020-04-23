---
title: Azure-fájlmegosztás biztonsági mentése a Azure Portal
description: Ismerje meg, hogyan használhatja a Azure Portal az Azure-fájlmegosztás biztonsági mentésére az Recovery Services-tárolóban
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: da2c7fa4cc5c3b7b948604a6f6d3999671cb3697
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101300"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Azure-fájlmegosztás biztonsági mentése egy Recovery Services-tárolóban

Ez a cikk azt ismerteti, hogyan használható a Azure Portal az [Azure-fájlmegosztás](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)biztonsági mentéséhez.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

* Recovery Services-tároló létrehozása.
* Fájlmegosztás felderítése és biztonsági mentések konfigurálása.
* Futtasson egy igény szerinti biztonsági mentési feladatot egy visszaállítási pont létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

* Azonosítsa vagy hozzon létre egy [Recovery Services](#create-a-recovery-services-vault) tárolót ugyanabban a régióban, mint a fájlmegosztást futtató Storage-fiók.
* Győződjön meg arról, hogy a fájlmegosztás szerepel a [támogatott Storage-fiókok](azure-file-share-support-matrix.md)egyikében.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Tárolási replikáció módosítása

Alapértelmezés szerint a [tárolók a Geo-redundáns tárolást (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)használják.

* Ha a tároló elsődleges biztonsági mentési mechanizmusa, javasoljuk, hogy használja a GRS.
* A [helyileg redundáns tárolás (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) alacsony díjszabású megoldásként használható.

A tárolási replikálás típusának módosítása:

1. Az új tárolóban válassza a **Tulajdonságok** lehetőséget a **Beállítások** szakaszban.

1. A **Tulajdonságok** lap **biztonsági mentés konfigurálása**területén válassza a **frissítés**lehetőséget.

1. Válassza ki a tárolási replikálás típusát, majd kattintson a **Mentés**gombra.

    ![Biztonsági mentési konfiguráció frissítése](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> A tárolási replikálási típus nem módosítható a tároló beállítása és a biztonsági mentési elemek létrehozása után. Ha ezt szeretné tenni, újra létre kell hoznia a tárolót.
>

## <a name="discover-file-shares-and-configure-backup"></a>Fájlmegosztás felderítése és a biztonsági mentés konfigurálása

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a fájlmegosztás biztonsági mentéséhez használni kívánt Recovery Services-tárolót.

1. A **Recovery Services** -tároló irányítópultján válassza a **+ biztonsági mentés**lehetőséget.

   ![Recovery Services-tároló](./media/backup-afs/recovery-services-vault.png)

    a. A **biztonsági mentés célja**beállításnál állítsa be, hogy **hol fut a** számítási feladat? az **Azure**-ban.

    ![Az Azure-fájlmegosztás biztonsági mentési célként való kiválasztása](./media/backup-afs/backup-goal.png)

    b.  A **Miről szeretne biztonsági másolatot készíteni?** területen válassza ki az **Azure-fájlmegosztás** elemet a legördülő listából.

    c.  Válassza a biztonsági mentés lehetőséget, hogy regisztrálja az Azure file share bővítményt a **tárolóban** .

    ![Válassza a biztonsági mentés lehetőséget az Azure-fájlmegosztás tárolóval való hozzárendeléséhez](./media/backup-afs/register-extension.png)

1. Miután kiválasztotta a **biztonsági mentést**, megnyílik a **biztonsági mentés** panel, és megkéri, hogy válasszon ki egy Storage-fiókot a felderített támogatott Storage-fiókok listájából. Ezek a tárolóhoz vannak társítva, vagy ugyanabban a régióban találhatók, mint a tároló, de még nincs hozzárendelve egyetlen Recovery Services-tárolóhoz sem.

1. A felderített Storage-fiókok listájából válasszon ki egy fiókot, majd kattintson **az OK gombra**. Az Azure olyan fájlmegosztás esetén keresi a Storage-fiókot, amelyekről biztonsági másolatot lehet készíteni. Ha nemrég adta hozzá a fájlmegosztást, és nem látja őket a listában, hagyjon némi időt a fájlmegosztás megjelenítésére.

    ![Fájlmegosztás felfedése](./media/backup-afs/discovering-file-shares.png)

1. A **fájlmegosztás** listából válasszon ki egy vagy több olyan fájlmegosztást, amelyről biztonsági másolatot szeretne készíteni. Kattintson az **OK** gombra.

   ![Fájlmegosztás kiválasztása](./media/backup-afs/select-file-shares.png)

1. A fájlmegosztás kiválasztása után a **Backup** menü a biztonsági **mentési szabályzatra**vált. Ebből a menüből válasszon ki egy meglévő biztonsági mentési szabályzatot, vagy hozzon létre egy újat. Ezután válassza a **biztonsági mentés engedélyezése**lehetőséget.

    ![Biztonsági mentési házirend kiválasztása](./media/backup-afs/select-backup-policy.png)

Miután beállította a biztonsági mentési szabályzatot, a rendszer a fájlmegosztás pillanatképét az ütemezett időpontban hozza létre. A helyreállítási pontot a kiválasztott időszakra is megőrzi a rendszer.

>[!NOTE]
>Azure Backup mostantól támogatja az Azure-fájlmegosztás biztonsági mentésének napi/heti/havi/éves megőrzését biztosító szabályzatokat.

## <a name="create-an-on-demand-backup"></a>Igény szerinti biztonsági másolat létrehozása

Időnként előfordulhat, hogy biztonsági mentési pillanatképet vagy helyreállítási pontot szeretne készíteni a biztonsági mentési házirendben ütemezett időpontokon kívül. Az igény szerinti biztonsági mentés gyakori oka, hogy a biztonsági mentési szabályzatot már konfigurálta. A biztonsági mentési szabályzatban foglalt ütemezés alapján a pillanatkép elkészítése előtt óra vagy nap lehet. Annak érdekében, hogy adatai a biztonsági mentési szabályzat elindulásáig is védve legyenek, indítson el egy igény szerinti biztonsági mentést. Szükség van egy igény szerinti biztonsági mentés létrehozására, mielőtt tervezett változtatásokat hajt végre a fájlmegosztás számára.

### <a name="create-a-backup-job-on-demand"></a>Igény szerinti biztonsági mentési feladatok létrehozása

1. Nyissa meg a fájlmegosztás biztonsági mentésére használt Recovery Services-tárolót. Az **Áttekintés** panelen válassza a **védett elemek** alatt található **biztonsági másolati elemek** lehetőséget.

   ![Biztonsági másolati elemek kiválasztása](./media/backup-afs/backup-items.png)

1. Miután kiválasztotta a **biztonsági másolati elemeket**, megjelenik egy új ablaktábla, amely felsorolja az összes **biztonsági mentési felügyeleti típust** az **Áttekintés** ablaktábla mellett.

   ![A biztonságimásolat-felügyeleti típusok listája](./media/backup-afs/backup-management-types.png)

1. A **biztonságimásolat-kezelés típusa** listából válassza az **Azure Storage (Azure Files)** lehetőséget. Ekkor megjelenik az összes fájlmegosztás és a hozzájuk tartozó megfelelő tárolási fiókok listája a tároló használatával.

   ![Azure Storage-(Azure Files-) biztonsági másolati elemek](./media/backup-afs/azure-files-backup-items.png)

1. Az Azure-fájlmegosztás listájából válassza ki a kívánt fájlmegosztást. Megjelenik a **biztonsági mentési elemek** részletei. A **biztonsági mentési elem** menüben válassza a **biztonsági mentés**lehetőséget. Mivel ez a biztonsági mentési feladatok igény szerint vannak, a helyreállítási ponthoz nincs adatmegőrzési szabály társítva.

   ![Biztonsági mentés kiválasztása](./media/backup-afs/backup-now.png)

1. Megnyílik a **biztonsági mentés** panel. Adja meg a helyreállítási pont megőrzésének utolsó napját. Igény szerinti biztonsági mentés esetén 10 évig is megtarthatja a maximumot.

   ![Megőrzési dátum kiválasztása](./media/backup-afs/retention-date.png)

1. Az **OK** gombra kattintva erősítse meg a-t futtató igény szerinti biztonsági mentési feladatot.

1. A portál értesítéseinek figyelésével nyomon követheti a biztonsági mentési feladatok futtatásának befejezését. A feladatok előrehaladását a tároló irányítópultján követheti nyomon. Válassza**a folyamatban lévő** **biztonsági mentési feladatok** > lehetőséget.

>[!NOTE]
>Azure Backup zárolja a Storage-fiókot, amikor a megfelelő fiókban lévő fájlmegosztás védelmét konfigurálja. Ez védelmet biztosít egy olyan Storage-fiók véletlen törlésével szemben, amely biztonsági másolatokat tartalmazó fájlmegosztást tartalmaz.

## <a name="best-practices"></a>Ajánlott eljárások

* Ne töröljön Azure Backup által létrehozott pillanatképeket. A pillanatképek törlése helyreállítási pontok elvesztését és/vagy visszaállítási hibákat eredményezhet.

* Ne távolítsa el Azure Backup a Storage-fiók zárolását. Ha törli a zárolást, a Storage-fiók a véletlen törléssel lesz kitéve, és ha törölve van, akkor a pillanatképek és a biztonsági másolatok elvesznek.

## <a name="next-steps"></a>További lépések

Az alábbiak végrehajtásának módját ismerheti meg:

* [Azure-fájlmegosztás visszaállítása](restore-afs.md)
* [Azure-fájlmegosztás biztonsági másolatainak kezelése](manage-afs-backup.md)
