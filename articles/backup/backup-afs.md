---
title: Azure-fájlmegosztás biztonsági mentése a Azure Portal
description: Ismerje meg, hogyan használhatja a Azure Portal az Azure-fájlmegosztás biztonsági mentésére az Recovery Services-tárolóban
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: a77f7fd0ec21eae60a7313a9ffa889fbef4372c6
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82978024"
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

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a fájlmegosztás biztonsági mentésének konfigurálásához használni kívánt Recovery Services-tárolót.

1. A **Recovery Services** -tároló ablaktáblán kattintson a felül található menü **+ biztonsági mentés** elemére.

   ![Recovery Services-tároló](./media/backup-afs/recovery-services-vault.png)

    1. A **biztonsági mentés célja** panelen állítsa be, hogy **hol fut** a számítási feladat? **a legördülő** listából válassza az **Azure** lehetőséget.

          ![Az Azure kiválasztása munkaterhelésként](./media/backup-afs/backup-goal.png)

    2. A **Miről szeretne biztonsági másolatot készíteni?** területen válassza ki az **Azure-fájlmegosztás** elemet a legördülő listából.

          ![Azure-fájlmegosztás kiválasztása](./media/backup-afs/select-azure-file-share.png)

    3. Válassza a biztonsági mentés lehetőséget, hogy regisztrálja az Azure file share bővítményt a **tárolóban** .

          ![Válassza a biztonsági mentés lehetőséget az Azure-fájlmegosztás tárolóval való hozzárendeléséhez](./media/backup-afs/register-extension.png)

1. Miután kiválasztotta a **biztonsági mentést**, megnyílik a **biztonsági mentés** panel. A védelemmel ellátni kívánt fájlmegosztás tárolására szolgáló Storage-fiók **kiválasztásához kattintson a hivatkozás** szövegére a Storage- **fiók** szövegmező alatt.

   ![Válassza a kiválasztás hivatkozást](./media/backup-afs/choose-select-link.png)

1. A jobb oldalon megnyílik a **Storage-fiók kiválasztása panel** , amely a felderített támogatott Storage-fiókok listáját tartalmazza. Ezek a tárolóhoz vannak társítva, vagy ugyanabban a régióban találhatók, mint a tároló, de még nincs hozzárendelve egyetlen Recovery Services-tárolóhoz sem.

1. A felderített Storage-fiókok listájából válasszon ki egy fiókot, majd kattintson **az OK gombra**.

   ![Válasszon a felderített Storage-fiókok közül](./media/backup-afs/select-discovered-storage-account.png)

1. A következő lépés azon fájlmegosztás kiválasztása, amelyekről biztonsági másolatot szeretne készíteni. Kattintson a **Hozzáadás** gombra a **állnak a Backup** szakaszhoz.

   ![Válassza ki a biztonsági mentéshez használandó fájlmegosztást](./media/backup-afs/select-file-shares-to-back-up.png)

1. A jobb oldalon megnyílik a **fájlmegosztás helyi kiválasztása** panel. Az Azure olyan fájlmegosztás esetén keresi a Storage-fiókot, amelyekről biztonsági másolatot lehet készíteni. Ha nemrég adta hozzá a fájlmegosztást, és nem látja őket a listában, hagyjon némi időt a fájlmegosztás megjelenítésére.

1. A **fájlmegosztás kiválasztása** listából válasszon ki egy vagy több olyan fájlmegosztást, amelyről biztonsági másolatot szeretne készíteni. Kattintson az **OK** gombra.

   ![Fájlmegosztás kiválasztása](./media/backup-afs/select-file-shares.png)

1. A fájlmegosztás biztonsági mentési szabályzatának kiválasztásához három lehetőség közül választhat:

   * Válassza ki az alapértelmezett házirendet.<br>
   Ezzel a beállítással engedélyezheti a napi biztonsági mentést, amely 30 napig megőrzi a rendszerét. Ha nem rendelkezik meglévő biztonsági mentési házirenddel a tárolóban, megnyílik a Backup (biztonsági mentés) ablaktábla az alapértelmezett házirend-beállításokkal. Ha az alapértelmezett beállításokat szeretné kiválasztani, közvetlenül a **biztonsági mentés engedélyezése**lehetőségre kattinthat.

   * Új szabályzat létrehozása <br>

      1. Ha új biztonsági mentési szabályzatot szeretne létrehozni a fájlmegosztás számára, kattintson a hivatkozás szövegre a **biztonsági mentési szabályzat** szakaszban a legördülő lista alatt.<br>

         ![Új szabályzat létrehozása](./media/backup-afs/create-new-policy.png)

      1. A jobb oldalon megnyílik a **biztonsági mentési házirend** környezeti panelje. Adja meg a szabályzat nevét a szövegmezőben, és válassza ki a megőrzési időszakot a követelménynek megfelelően. Alapértelmezés szerint csak a napi megőrzési beállítás engedélyezett. Ha hetente, havonta vagy évenkénti megőrzést szeretne, jelölje be a megfelelő jelölőnégyzetet, és adja meg a kívánt megőrzési értéket.

      1. A megőrzési értékek és az érvényes házirend nevének megadása után kattintson az OK gombra.<br>

         ![Adja meg a szabályzat nevét és a megőrzési értékeket](./media/backup-afs/policy-name.png)

   * Válasszon egy meglévő biztonsági mentési szabályzatot <br>

   Ha az egyik meglévő biztonsági mentési szabályzatot szeretné kiválasztani a védelem konfigurálásához, válassza ki a kívánt szabályzatot a **biztonsági mentési** szabályzat legördülő listából.<br>

   ![Meglévő házirend kiválasztása](./media/backup-afs/choose-existing-policy.png)

1. Kattintson a **biztonsági mentés engedélyezése** elemre a fájlmegosztás védelmének megkezdéséhez.

   ![Válassza a biztonsági mentés engedélyezése lehetőséget.](./media/backup-afs/enable-backup.png)

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
