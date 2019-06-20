---
title: Az Azure Backup szolgáltatással az Azure Files-fájlmegosztások biztonsági mentése
description: Ez az oktatóanyag azt ismerteti, hogyan az Azure-fájlmegosztások biztonsági mentése.
services: backup
author: dcurwin
ms.author: dacurwin
ms.date: 06/10/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 474d5454e30c35d3f3ccf4ea994093ef47bd6ceb
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275991"
---
# <a name="back-up-azure-file-shares"></a>Azure-fájlmegosztások biztonsági mentése
Ez a cikk azt ismerteti, hogyan használhatja az Azure Portalt az [Azure-fájlmegosztások](../storage/files/storage-files-introduction.md) biztonsági mentésére és visszaállítására.

Ebből az útmutatóból a következőket tanulhatja meg:
> [!div class="checklist"]
> * Helyreállítási tár konfigurálása Azure Files biztonsági mentéséhez
> * Igény szerinti biztonságimásolat-készítő feladat futtatása visszaállítási pont létrehozásához


## <a name="prerequisites"></a>Előfeltételek
Mielőtt biztonsági mentést készít egy Azure-fájlmegosztásról, győződjön meg arról, hogy az a [támogatott tárfióktípusok](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview) közé tartozik. Ennek ellenőrzését követően biztosíthatja a fájlmegosztások védelmét.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>A fájlmegosztás biztonsági mentésének korlátozásai az Azure-előzetes verzió használata során
Az Azure-fájlmegosztások biztonsági mentése jelenleg előzetes verzióban érhető el. Azure-fájlmegosztások mindkét általános célú v1-ben, és az általános célú v2-tárfiókok támogatottak. Az Azure-fájlmegosztások nem támogatják az alábbi biztonsági mentési forgatókönyveket:
- Nem biztosítható az Azure-fájlmegosztások védelme olyan tárfiókokban, amelyeken engedélyezve vannak a virtuális hálózatok vagy a tűzfal.
- Nincs a CLI nem érhető el az Azure Backup használatával az Azure Files védelméhez.
- Az ütemezett biztonsági mentések maximális száma naponta egy.
- Az igény szerinti biztonsági mentések maximális száma naponta négy.
- Használjon [erőforrászárat](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) a tárfiókon, hogy megelőzze a helyreállítási tárban lévő biztonsági másolatok véletlen törlését.
- Ne törölje az Azure Backuppal létrehozott pillanatképeket. A pillanatképek törlése helyreállítási pontok elvesztését és/vagy visszaállítási hibákat eredményezhet.
- Ne törölje az Azure Backup által védett fájlmegosztások. Az aktuális megoldáshoz fog törölni a fájlmegosztás törlése után az Azure Backup által készített minden pillanatkép, és ezért elveszíti az összes visszaállítási pont

Vissza a Tárfiókokban lévő Azure-fájlmegosztások regisztrálásához [zónaredundáns tárolás](../storage/common/storage-redundancy-zrs.md) (ZRS) replikációs érhető el jelenleg csak központi velünk a Kapcsolatot (CUS), East US (EUS), USA keleti RÉGIÓJA 2 (EUS2), északi Európa (NE), Ausztrália Ázsia (SEA), Nyugat-Európa (WE) és az USA nyugati RÉGIÓJA 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Azure-fájlmegosztások biztonsági mentésének konfigurálása
Ez az oktatóanyag feltételezi, hogy már létrehozott egy Azure-fájlmegosztást. Az Azure-fájlmegosztás biztonsági mentése:

1. Hozzon létre egy helyreállítási tárat a fájlmegosztással megegyező régióban. Ha már rendelkezik tárolóval, nyissa meg a tároló Áttekintés oldalát, és kattintson **Biztonsági mentés** lehetőségre.

    ![A tároló áttekintés oldalát a biztonsági mentés gombra](./media/backup-file-shares/overview-backup-page.png)

2. Az a **biztonsági mentés célja** menüben a **választható biztonsági mentéséhez?** , Azure-fájlmegosztás kiválasztása.

    ![Azure-fájlmegosztás kiválasztása a biztonsági mentés céljaként](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Kattintson a **Biztonsági mentés** elemre az Azure-fájlmegosztás helyreállítási tárhoz való konfigurálásához.

   ![Kattintson a Biztonsági mentés elemre az Azure-fájlmegosztás tárolóval történő társításához](./media/backup-file-shares/set-backup-goal.png)

    Miután a tároló társítva az Azure-fájlmegosztást, a biztonsági mentés menü megnyílik, és kéri egy tárfiók kiválasztását. A menüben megjelenik az összes támogatott Tárfiókok, amelyek már nem egy Recovery Services-tároló kapcsolódnak a tárral megegyező régióban.

   ![Válassza ki a tárfiókot](./media/backup-file-shares/list-of-storage-accounts.png)

4. A tárfiókok listájából válasszon ki egy fiókot, majd kattintson az **OK** gombra. Az Azure kikeresi a tárfiók azon fájljait, amelyekről biztonsági mentés készíthető. Ha nemrégiben adta hozzá a fájlmegosztásokat, és emiatt nem látja azokat a listában, hagyjon egy kis időt a fájlmegosztásoknak, hogy megjelenjenek.

   ![Fájlmegosztások kell felderíteni](./media/backup-file-shares/discover-file-shares.png)

5. Az a **fájlmegosztások** válasszon ki egy vagy több biztonsági mentése, és kattintson a kívánt fájlmegosztást **OK**.

6. A fájlmegosztások kiválasztását követően a Biztonsági mentés menü átvált a **Biztonsági mentési szabályzat** menüre. Ebben a menüben válasszon egy meglévő biztonsági mentési szabályzatot, vagy hozzon létre egy újat, és kattintson **Biztonsági mentés engedélyezése** lehetőségre.

   ![Válasszon ki egy biztonsági mentési szabályzatot, vagy hozzon létre egy újat](./media/backup-file-shares/apply-backup-policy.png)

    A biztonsági mentési szabályzat létrehozása után pillanatkép készül a fájlmegosztásokról az ütemezett időpontban, és a rendszer megőrzi a helyreállítási pontot a választott időszakra vonatkozóan.

## <a name="create-an-on-demand-backup"></a>Igény szerinti biztonsági másolat létrehozása
Miután a biztonsági mentési szabályzathoz, érdemes egy igény szerinti biztonsági mentést annak érdekében, hogy az adatok védelme csak a következő ütemezett biztonsági mentés.


### <a name="to-create-an-on-demand-backup"></a>Igény szerinti biztonsági másolat létrehozása

1. Nyissa meg a fájlmegosztás helyreállítási pontjait tartalmazó helyreállítási tárat, és kattintson a **Biztonsági másolati elemek** lehetőségre. Ekkor megjelenik a biztonsági mentési elemek típusainak listája.

   ![Biztonsági mentési elemek listája](./media/backup-file-shares/list-of-backup-items.png)

2. Válassza ki a listáról az **Azure Storage (Azure Files)** lehetőséget. Megjelenik az Azure-fájlmegosztások listája.

   ![Azure-fájlmegosztások listája](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. Az Azure-fájlmegosztások listájában jelölje ki a kívánt fájlmegosztást. Megnyílik a kijelölt fájlmegosztáshoz tartozó Biztonságimásolat-elem menü.

   ![A kiválasztott fájlra vonatkozó biztonsági mentési elem menü megosztása](./media/backup-file-shares/backup-item-menu.png)

4. Kattintson a Biztonságimásolat-elem menü **Biztonsági mentés** lehetőségére. Mivel ez egy igény szerinti biztonsági mentési feladat, nincs társítva adatmegőrzési szabályzat a helyreállítási ponthoz. Megnyílik a **Biztonsági mentés** párbeszédpanel. Adja meg a helyreállítási pont megőrzésének utolsó napját.

   ![A helyreállítási pont megőrzése dátum kiválasztása](./media/backup-file-shares/backup-now-menu.png)


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket végezte el az Azure Portalon:

> [!div class="checklist"]
> * Helyreállítási tár konfigurálása Azure Files biztonsági mentéséhez
> * Igény szerinti biztonságimásolat-készítő feladat futtatása visszaállítási pont létrehozásához

Folytassa a következő cikk az Azure-fájlmegosztások biztonsági másolatból történő visszaállítását.

> [!div class="nextstepaction"]
> [Visszaállítása az Azure-fájlmegosztások biztonsági másolatából](./backup-azure-files.md#restore-from-backup-of-azure-file-share)
 
