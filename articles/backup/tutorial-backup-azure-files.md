---
title: Oktatóanyag – Az Azure Files fájlmegosztások biztonsági és biztonsági dokumentációjának
description: Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja a Recovery Services-tárolót az Azure-portálhasználatával, és hogyan készüljön biztonsági másolatot az Azure-fájlmegosztásokról.
ms.date: 06/10/2019
ms.topic: tutorial
ms.openlocfilehash: ec9074a39f2ece7878c0c3ef828dc21748d0ab89
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76293929"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>Az Azure-fájlmegosztások biztonsági és biztonsági és biztonsági dokumentációjának biztonsági és biztonsági dokumentációjának biztonsági és biztonsági dokumentációja

Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Portalt az [Azure-fájlmegosztások biztonsági rendszerének biztonsági leéséhez.](../storage/files/storage-files-introduction.md)

Ebből az útmutatóból a következőket tanulhatja meg:
> [!div class="checklist"]
>
> * Helyreállítási tár konfigurálása Azure Files biztonsági mentéséhez
> * Igény szerinti biztonságimásolat-készítő feladat futtatása visszaállítási pont létrehozásához

## <a name="prerequisites"></a>Előfeltételek

Mielőtt biztonsági mentést készít egy Azure-fájlmegosztásról, győződjön meg arról, hogy az a [támogatott tárfióktípusok](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview) közé tartozik. Ennek ellenőrzését követően biztosíthatja a fájlmegosztások védelmét.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Az Azure-fájlmegosztás biztonsági mentésének korlátai az előzetes verzióban

Az Azure-fájlmegosztások biztonsági mentése jelenleg előzetes verzióban érhető el. Az Azure-fájlmegosztások az általános célú v1-ben és az általános célú v2-es tárfiókokban is támogatottak. Az Azure-fájlmegosztások nem támogatják az alábbi biztonsági mentési forgatókönyveket:

* Az Azure-fájlok Azure Backup használatával történő védelméhez nem áll rendelkezésre CLI.
* Az ütemezett biztonsági mentések maximális száma naponta egy.
* Az igény szerinti biztonsági mentések maximális száma naponta négy.
* Használja [az erőforrás-zárolások](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) a tárfiókban a biztonsági mentések véletlen törlésének megakadályozása a Recovery Services-tárolóban.
* Ne törölje az Azure Backuppal létrehozott pillanatképeket. A pillanatképek törlése helyreállítási pontok elvesztését és/vagy visszaállítási hibákat eredményezhet.
* Ne törölje az Azure Backup által védett fájlmegosztásokat. A jelenlegi megoldás törli az Azure Backup által készített összes pillanatképet, miután a fájlmegosztást törölték, és így elveszíti az összes visszaállítási pontot

Biztonsági másolat az Azure File Shares in Storage Accounts with [zone redundáns tárolás](../storage/common/storage-redundancy-zrs.md) (ZRS) replikáció jelenleg csak a Közép-USA (CUS), Usa keleti régiója (EUS), Usa keleti régiója 2 (EUS2), Észak-Európa (NE), Délkelet-Ázsia (SEA), Nyugat-Európa (WE) és nyugat-USA 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Azure-fájlmegosztások biztonsági mentésének konfigurálása

Ez az oktatóanyag feltételezi, hogy már létrehozott egy Azure-fájlmegosztást. Az Azure-fájlmegosztás biztonsági mentése:

1. Hozzon létre egy helyreállítási tárat a fájlmegosztással megegyező régióban. Ha már rendelkezik tárolóval, nyissa meg a tároló Áttekintés oldalát, és kattintson **Biztonsági mentés** lehetőségre.

    ![Kattintson a Biztonsági másolat elemre a tároló áttekintése lapon](./media/tutorial-backup-azure-files/overview-backup-page.png)

2. A **Biztonsági másolat célja** **menüben válassza a Mit szeretne biztonsági másolatot készíteni?**

    ![Azure-fájlmegosztás kiválasztása a biztonsági mentés céljaként](./media/tutorial-backup-azure-files/choose-azure-fileshare-from-backup-goal.png)

3. Kattintson a **Biztonsági mentés** elemre az Azure-fájlmegosztás helyreállítási tárhoz való konfigurálásához.

   ![Kattintson a Biztonsági mentés elemre az Azure-fájlmegosztás tárolóval történő társításához](./media/tutorial-backup-azure-files/set-backup-goal.png)

    Miután a tároló társítva van az Azure-fájlmegosztáshoz, a Biztonsági mentés menü megnyílik, és kéri, hogy válasszon ki egy storage-fiókot. A menü ben az összes támogatott tárfiókok a régióban, ahol a tároló létezik, amely még nem társított a Recovery Services-tároló.

   ![Válassza ki tárfiókját](./media/tutorial-backup-azure-files/list-of-storage-accounts.png)

4. A tárfiókok listájából válasszon ki egy fiókot, majd kattintson az **OK** gombra. Az Azure kikeresi a tárfiók azon fájljait, amelyekről biztonsági mentés készíthető. Ha nemrégiben adta hozzá a fájlmegosztásokat, és emiatt nem látja azokat a listában, hagyjon egy kis időt a fájlmegosztásoknak, hogy megjelenjenek.

   ![A fájlmegosztások felderítése folyamatban van](./media/tutorial-backup-azure-files/discover-file-shares.png)

5. A **Fájlmegosztások** listában jelöljön ki egy vagy több olyan fájlmegosztást, amelyről biztonsági másolatot szeretne tenni, majd kattintson az **OK**gombra.

6. A fájlmegosztások kiválasztását követően a Biztonsági mentés menü átvált a **Biztonsági mentési szabályzat** menüre. Ebben a menüben válasszon egy meglévő biztonsági mentési szabályzatot, vagy hozzon létre egy újat, és kattintson **Biztonsági mentés engedélyezése** lehetőségre.

   ![Biztonsági másolat házirend kiválasztása vagy új házirend létrehozása](./media/tutorial-backup-azure-files/apply-backup-policy.png)

    A biztonsági mentési szabályzat létrehozása után pillanatkép készül a fájlmegosztásokról az ütemezett időpontban, és a rendszer megőrzi a helyreállítási pontot a választott időszakra vonatkozóan.

## <a name="create-an-on-demand-backup"></a>Igény szerinti biztonsági másolat létrehozása

A biztonsági mentési házirend konfigurálása után érdemes igény szerinti biztonsági másolatot létrehozni, hogy az adatok a következő ütemezett biztonsági mentésig védettek legyenek.

### <a name="to-create-an-on-demand-backup"></a>Igény szerinti biztonsági másolat létrehozása

1. Nyissa meg a fájlmegosztás helyreállítási pontjait tartalmazó helyreállítási tárat, és kattintson a **Biztonsági másolati elemek** lehetőségre. Ekkor megjelenik a biztonsági mentési elemek típusainak listája.

   ![Biztonsági másolat elemeinek listája](./media/tutorial-backup-azure-files/list-of-backup-items.png)

2. Válassza ki a listáról az **Azure Storage (Azure Files)** lehetőséget. Megjelenik az Azure-fájlmegosztások listája.

   ![Az Azure-fájlmegosztások listája](./media/tutorial-backup-azure-files/list-of-azure-files-backup-items.png)

3. Az Azure-fájlmegosztások listájában jelölje ki a kívánt fájlmegosztást. Megnyílik a kijelölt fájlmegosztáshoz tartozó Biztonságimásolat-elem menü.

   ![A kijelölt fájlmegosztás Biztonsági másolat elem menüje](./media/tutorial-backup-azure-files/backup-item-menu.png)

4. Kattintson a Biztonságimásolat-elem menü **Biztonsági mentés** lehetőségére. Mivel ez egy igény szerinti biztonsági mentési feladat, nincs társítva adatmegőrzési szabályzat a helyreállítási ponthoz. Megnyílik a **Biztonsági mentés** párbeszédpanel. Adja meg a helyreállítási pont megőrzésének utolsó napját.

   ![Helyreállításipont-megőrzésdátumának kiválasztása](./media/tutorial-backup-azure-files/backup-now-menu.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket végezte el az Azure Portalon:

> [!div class="checklist"]
>
> * Helyreállítási tár konfigurálása Azure Files biztonsági mentéséhez
> * Igény szerinti biztonságimásolat-készítő feladat futtatása visszaállítási pont létrehozásához

Folytassa a következő cikket egy Azure-fájlmegosztás biztonsági másolatából történő visszaállításához.

> [!div class="nextstepaction"]
> [Visszaállítás az Azure-fájlmegosztások biztonsági másolatából](restore-afs.md)
