---
title: Oktatóanyag – Azure Files fájlmegosztás biztonsági mentése
description: Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Azure Portal egy Recovery Services-tároló konfigurálására és az Azure-fájlmegosztás biztonsági mentésére.
ms.date: 06/10/2019
ms.topic: tutorial
ms.openlocfilehash: f3c1956a0344d300fff9a745a4733b7bef45a367
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171872"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>Azure-fájlmegosztás biztonsági mentése a Azure Portal

Ez az oktatóanyag azt ismerteti, hogyan használható a Azure Portal az [Azure-fájlmegosztás](../storage/files/storage-files-introduction.md)biztonsági mentéséhez.

Ebből az útmutatóból a következőket tanulhatja meg:
> [!div class="checklist"]
>
> * Helyreállítási tár konfigurálása Azure Files biztonsági mentéséhez
> * Igény szerinti biztonságimásolat-készítő feladat futtatása visszaállítási pont létrehozásához

## <a name="prerequisites"></a>Előfeltételek

Mielőtt biztonsági mentést készít egy Azure-fájlmegosztásról, győződjön meg arról, hogy az a [támogatott tárfióktípusok](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview) közé tartozik. Ennek ellenőrzését követően biztosíthatja a fájlmegosztások védelmét.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Az Azure-fájlmegosztás biztonsági mentésének korlátai az előzetes verzióban

Az Azure-fájlmegosztások biztonsági mentése jelenleg előzetes verzióban érhető el. Az Azure-fájlmegosztás az általános célú v1 és az általános célú v2 Storage-fiókok esetében is támogatott. Az Azure-fájlmegosztások nem támogatják az alábbi biztonsági mentési forgatókönyveket:

* Nem biztosítható az Azure-fájlmegosztások védelme olyan tárfiókokban, amelyeken engedélyezve vannak a virtuális hálózatok vagy a tűzfal.
* Azure Backup használatával nem érhető el a CLI a Azure Files védelméhez.
* Az ütemezett biztonsági mentések maximális száma naponta egy.
* Az igény szerinti biztonsági mentések maximális száma naponta négy.
* Használjon [erőforrászárat](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) a tárfiókon, hogy megelőzze a helyreállítási tárban lévő biztonsági másolatok véletlen törlését.
* Ne törölje az Azure Backuppal létrehozott pillanatképeket. A pillanatképek törlése helyreállítási pontok elvesztését és/vagy visszaállítási hibákat eredményezhet.
* Ne törölje a Azure Backup által védett fájlmegosztást. A jelenlegi megoldás törli az Azure Backup által készített összes pillanatképet, miután a fájlmegosztás törölve lett, és így elveszíti az összes visszaállítási pontot

A Storage-fiókokban tárolt Azure-fájlmegosztás biztonsági [mentése a ZRS](../storage/common/storage-redundancy-zrs.md) -replikációval jelenleg csak az USA középső régiójában (ke), az USA keleti régiójában (EUs), az USA 2. keleti régiójában (EUS2), Észak-Európában (ne), Délkelet-Ázsiában (tenger), Nyugat-Európában (mi) és az USA 2. nyugati régiójában (WUS2

## <a name="configuring-backup-for-an-azure-file-share"></a>Azure-fájlmegosztások biztonsági mentésének konfigurálása

Ez az oktatóanyag feltételezi, hogy már létrehozott egy Azure-fájlmegosztást. Az Azure-fájlmegosztás biztonsági mentése:

1. Hozzon létre egy helyreállítási tárat a fájlmegosztással megegyező régióban. Ha már rendelkezik tárolóval, nyissa meg a tároló Áttekintés oldalát, és kattintson **Biztonsági mentés** lehetőségre.

    ![Kattintson a biztonsági mentés elemre a tár Áttekintés oldalán](./media/backup-file-shares/overview-backup-page.png)

2. A **biztonsági mentés célja** menüben, a **Miről szeretne biztonsági másolatot készíteni?** területen válassza az Azure fájlmegosztás lehetőséget.

    ![Azure-fájlmegosztás kiválasztása a biztonsági mentés céljaként](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Kattintson a **Biztonsági mentés** elemre az Azure-fájlmegosztás helyreállítási tárhoz való konfigurálásához.

   ![Kattintson a Biztonsági mentés elemre az Azure-fájlmegosztás tárolóval történő társításához](./media/backup-file-shares/set-backup-goal.png)

    Miután a tároló társítva van az Azure-fájlmegosztás számára, megnyílik a biztonsági mentés menü, és megkéri, hogy válasszon ki egy Storage-fiókot. A menü megjeleníti az összes támogatott Storage-fiókot abban a régióban, ahol a tároló létezik, amely még nincs Recovery Services-tárolóhoz társítva.

   ![Válassza ki a Storage-fiókját](./media/backup-file-shares/list-of-storage-accounts.png)

4. A tárfiókok listájából válasszon ki egy fiókot, majd kattintson az **OK** gombra. Az Azure kikeresi a tárfiók azon fájljait, amelyekről biztonsági mentés készíthető. Ha nemrégiben adta hozzá a fájlmegosztásokat, és emiatt nem látja azokat a listában, hagyjon egy kis időt a fájlmegosztásoknak, hogy megjelenjenek.

   ![A fájlmegosztás észlelése folyamatban van](./media/backup-file-shares/discover-file-shares.png)

5. A **fájlmegosztás** listából válasszon ki egy vagy több olyan fájlmegosztást, amelyről biztonsági másolatot szeretne készíteni, majd kattintson **az OK**gombra.

6. A fájlmegosztások kiválasztását követően a Biztonsági mentés menü átvált a **Biztonsági mentési szabályzat** menüre. Ebben a menüben válasszon egy meglévő biztonsági mentési szabályzatot, vagy hozzon létre egy újat, és kattintson **Biztonsági mentés engedélyezése** lehetőségre.

   ![Válasszon ki egy biztonsági mentési szabályzatot, vagy hozzon létre újat](./media/backup-file-shares/apply-backup-policy.png)

    A biztonsági mentési szabályzat létrehozása után pillanatkép készül a fájlmegosztásokról az ütemezett időpontban, és a rendszer megőrzi a helyreállítási pontot a választott időszakra vonatkozóan.

## <a name="create-an-on-demand-backup"></a>Igény szerinti biztonsági másolat létrehozása

A biztonsági mentési szabályzat konfigurálása után létre kell hoznia egy igény szerinti biztonsági mentést, hogy az adatai védve legyenek a következő ütemezett biztonsági mentésig.

### <a name="to-create-an-on-demand-backup"></a>Igény szerinti biztonsági másolat létrehozása

1. Nyissa meg a fájlmegosztás helyreállítási pontjait tartalmazó helyreállítási tárat, és kattintson a **Biztonsági másolati elemek** lehetőségre. Ekkor megjelenik a biztonsági mentési elemek típusainak listája.

   ![Biztonsági másolati elemek listája](./media/backup-file-shares/list-of-backup-items.png)

2. Válassza ki a listáról az **Azure Storage (Azure Files)** lehetőséget. Megjelenik az Azure-fájlmegosztások listája.

   ![Azure-fájlmegosztás listája](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Az Azure-fájlmegosztások listájában jelölje ki a kívánt fájlmegosztást. Megnyílik a kijelölt fájlmegosztáshoz tartozó Biztonságimásolat-elem menü.

   ![A kiválasztott fájlmegosztás biztonsági másolati elemének menüje](./media/backup-file-shares/backup-item-menu.png)

4. Kattintson a Biztonságimásolat-elem menü **Biztonsági mentés** lehetőségére. Mivel ez egy igény szerinti biztonsági mentési feladat, nincs társítva adatmegőrzési szabályzat a helyreállítási ponthoz. Megnyílik a **Biztonsági mentés** párbeszédpanel. Adja meg a helyreállítási pont megőrzésének utolsó napját.

   ![Helyreállítási pont megőrzési dátumának kiválasztása](./media/backup-file-shares/backup-now-menu.png)

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőket végezte el az Azure Portalon:

> [!div class="checklist"]
>
> * Helyreállítási tár konfigurálása Azure Files biztonsági mentéséhez
> * Igény szerinti biztonságimásolat-készítő feladat futtatása visszaállítási pont létrehozásához

Folytassa a következő cikkel az Azure-fájlmegosztás biztonsági másolatából való visszaállításhoz.

> [!div class="nextstepaction"]
> [Azure-fájlmegosztás biztonsági másolatának visszaállítása](./backup-azure-files.md#restore-from-backup-of-azure-file-share)
