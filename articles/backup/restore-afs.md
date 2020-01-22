---
title: Azure-fájlmegosztás visszaállítása
description: Ebből a témakörből megtudhatja, hogyan állíthatja vissza a teljes fájlmegosztást vagy adott fájlokat egy Azure Backup szolgáltatás által létrehozott visszaállítási pontról a Azure Portal használatával.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: b16eb4120ff2d269135ae8ae6555ef4fdbdbda5d
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294395"
---
# <a name="restore-azure-file-shares"></a>Azure-fájlmegosztás visszaállítása

Ez a cikk azt ismerteti, hogyan lehet a Azure Portal használatával visszaállítani egy teljes fájlmegosztást vagy adott fájlokat a [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview) szolgáltatás által létrehozott visszaállítási pontról.

Ebből az útmutatóból megtudhatja, hogyan végezheti el a következőket:

* Teljes Azure-fájlmegosztás visszaállítása
* Egyéni fájlok vagy mappák visszaállítása
* A visszaállítási művelet állapotának nyomon követése

## <a name="steps-to-perform-restore"></a>A visszaállítás végrehajtásának lépései

### <a name="select-the-file-share-to-restore"></a>Válassza ki a visszaállítani kívánt fájlmegosztást

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a fájlmegosztás biztonsági mentésének konfigurálásához használt Recovery Services-tárolót.

2. Az **Áttekintés** panel **védett elemek** szakaszában kattintson a **biztonsági másolati elemek** elemre.

    ![Kattintson a biztonsági másolati elemek elemre](./media/restore-afs/backup-items.png)

3. Ha a **biztonsági másolati elemek**elemre kattint, megjelenik egy új panel, amely felsorolja az összes biztonsági mentési felügyeleti típust az **Áttekintés** panel mellett, az alábbi képen látható módon:

    ![Biztonságimásolat-felügyeleti típusok](./media/restore-afs/backup-management.png)

4. A **biztonsági másolati elemek**területen a **biztonsági mentési felügyelet típusa**területen válassza az **Azure Storage (Azure Files)** lehetőséget. Ekkor megjelenik az összes fájlmegosztás és a hozzájuk tartozó Storage-fiók listája a tároló használatával.

    ![Az összes fájlmegosztás listája](./media/restore-afs/file-shares.png)

5. Az Azure-fájlmegosztás listájából válassza ki azt a kívánt fájlmegosztást, amelynek el szeretné végezni a visszaállítási műveletet.

### <a name="full-share-recovery"></a>Teljes megosztás helyreállítása

Ezzel a visszaállítási lehetőséggel visszaállíthatja a teljes fájlmegosztást az eredeti vagy egy másik helyen.

1. Válassza a **megosztás visszaállítása** lehetőséget a **biztonsági mentési elem** panelen, amely akkor jelenik meg, ha a kívánt fájlmegosztást kiválasztja a visszaállítandó [fájlmegosztás kiválasztása](#select-the-file-share-to-restore) szakasz 5. lépésében.

   ![Megosztás visszaállításának kiválasztása](./media/restore-afs/restore-share.png)

2. Ha a **megosztás visszaállítása**gombra kattint, megnyílik a **visszaállítás** panel egy **visszaállítási pont** menüvel, amely a kiválasztott fájlmegosztás számára elérhető visszaállítási pontok listáját jeleníti meg.

3. Válassza ki a visszaállítási művelet végrehajtásához használni kívánt visszaállítási pontot, majd kattintson **az OK**gombra.

    ![Visszaállítási pont kiválasztása](./media/restore-afs/restore-point.png)

4. Miután rákattintott az OK gombra, a visszaállítási panel menüje visszavált a **visszaállítási helyre**. A **visszaállítási hely**mezőben adja meg, hogy hol (vagy hogyan) szeretné visszaállítani az adathelyreállítást. A következő két lehetőség közül választhat:

    * **Eredeti hely**: állítsa vissza a teljes fájlmegosztást ugyanarra a helyre, mint az eredeti forrást.
    * **Másik hely**: állítsa vissza a teljes fájlmegosztást egy másik **helyre, és**tartsa meg az eredeti fájlmegosztást.

#### <a name="restore-to-original-location"></a>Visszaállítás az eredeti helyre

1. Válassza ki az **eredeti helyet** **helyreállítási célhelyként** , és válassza ki, hogy ki kell-e hagyni vagy felülírja-e az ütközéseket. A megfelelő kijelölés megtétele után kattintson **az OK gombra** .

    ![Eredeti hely kiválasztása](./media/restore-afs/original-location.png)

2. Kattintson a **visszaállítás** gombra a visszaállítási művelet elindításához.

    ![Kattintson a visszaállítás elemre a kezdéshez](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Visszaállítás másik helyre

1. Válasszon **másik helyet** a helyreállítási célhelyként.
2. Válassza ki a cél Storage-fiókot, ahol vissza kívánja állítani a mentett tartalmat a **Storage-fiók** mező legördülő menüjében.
3. A 2. lépésben kiválasztott Storage-fiók alapján a **fájlmegosztás kiválasztása** legördülő menü megjeleníti a kiválasztott Storage-fiókban lévő fájlmegosztás listáját. Válassza ki azt a fájlmegosztást, amelyben vissza kívánja állítani a biztonsági másolatok tartalmát.
4. A **mappanév** mezőben adja meg a visszaállított tartalommal a célfájl-megosztásban létrehozni kívánt mappanevet.
5. Válassza ki, hogy a rendszer kihagyja vagy felülírja-e az ütközéseket.
6. Kattintson **az OK** gombra, miután beírta a megfelelő értékeket az összes mezőben.

    ![Másik hely kiválasztása](./media/restore-afs/alternate-location.png)

7. Kattintson a visszaállítás gombra a visszaállítási művelet elindításához.

    ![Kattintson a visszaállítás elemre a kezdéshez](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Elemszintű helyreállítás

Ezzel a visszaállítási lehetőséggel visszaállíthatja az egyes fájlokat vagy mappákat az eredeti vagy egy másik helyen.

1. Válassza ki a **Fájl-helyreállítási** lehetőséget a **biztonsági mentési elem** panelen, amely akkor jelenik meg, ha kiválasztja a kívánt fájlmegosztást a visszaállítandó [fájlmegosztás kiválasztása](#select-the-file-share-to-restore) szakasz 5. lépésében.

    ![Fájl helyreállításának kiválasztása](./media/restore-afs/file-recovery.png)

2. Ha a **fájl helyreállítása**elemre kattint, megnyílik a **visszaállítás** panel egy **visszaállítási pont** menüvel, amely megjeleníti a kiválasztott fájlmegosztás számára elérhető visszaállítási pontok listáját.

3. Válassza ki a visszaállítási művelet végrehajtásához használni kívánt visszaállítási pontot, majd kattintson **az OK**gombra.

    ![Visszaállítási pont kiválasztása](./media/restore-afs/restore-point.png)

4. Miután rákattintott az OK gombra, a visszaállítási panel menüje visszavált a **visszaállítási helyre**. A **visszaállítási hely**mezőben adja meg, hogy hol (vagy hogyan) szeretné visszaállítani az adathelyreállítást. A következő két lehetőség közül választhat:

    * **Eredeti hely**: a kijelölt fájlok/mappák visszaállítása az eredeti forrással megegyező fájlmegosztást.
    * **Másik hely**: a kijelölt fájlok/mappák visszaállítása egy másik helyre, és az eredeti **fájlmegosztás tartalmának**megtartása.

#### <a name="restore-to-original-location"></a>Visszaállítás az eredeti helyre

1. Válassza ki az **eredeti helyet** **helyreállítási célhelyként** , és válassza ki, hogy ki kell-e hagyni vagy felülírja-e az ütközéseket.

    ![Az elemszintű helyreállítás eredeti helye](./media/restore-afs/original-location-item-level.png)

2. Kattintson a **fájl kiválasztása** lehetőségre a visszaállítani kívánt fájlok és mappák kiválasztásához.

    ![Kattintson a fájl kiválasztása elemre.](./media/restore-afs/select-file.png)

3. Ha a **fájl kiválasztása**elemre kattint, megjelenik a fájlmegosztás panel, amely megjeleníti a visszaállításra kiválasztott fájlmegosztás helyreállítási pontjának tartalmát.

4. Jelölje be a visszaállítani kívánt fájlhoz vagy mappához tartozó jelölőnégyzetet, majd kattintson a **kiválasztás**gombra.

    ![Fájl vagy mappa kiválasztása](./media/restore-afs/select-file-folder.png)

5. Ismételje meg a 2-4-es lépést több fájl/mappa kiválasztásához a visszaállításhoz.
6. Miután kiválasztotta az összes visszaállítani kívánt elemet, kattintson **az OK**gombra.

    ![A visszaállítani kívánt összes elem kijelölése után kattintson az OK gombra.](./media/restore-afs/after-selecting-items.png)

7. Kattintson a visszaállítás gombra a visszaállítási művelet elindításához.

    ![Kattintson a visszaállítás elemre a kezdéshez](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Visszaállítás másik helyre

1. Válasszon **másik helyet** a helyreállítási célhelyként.
2. Válassza ki a cél Storage-fiókot, ahol vissza kívánja állítani a mentett tartalmat a **Storage-fiók** mező legördülő menüjében.
3. A 2. lépésben kiválasztott Storage-fiók alapján a **fájlmegosztás kiválasztása** legördülő menü megjeleníti a kiválasztott Storage-fiókban lévő fájlmegosztás listáját. Válassza ki azt a fájlmegosztást, amelyben vissza kívánja állítani a biztonsági másolatok tartalmát.
4. A **mappanév** mezőben adja meg a visszaállított tartalommal a célfájl-megosztásban létrehozni kívánt mappanevet.
5. Válassza ki, hogy a rendszer kihagyja vagy felülírja-e az ütközéseket.
6. Kattintson a **fájl kiválasztása** lehetőségre a visszaállítani kívánt fájlok és mappák kiválasztásához.

    ![Válassza ki azokat az elemeket, amelyeket vissza szeretne állítani a másik helyre](./media/restore-afs/restore-to-alternate-location.png)

7. Ha a **fájl kiválasztása**elemre kattint, megjelenik a fájlmegosztás panel, amely megjeleníti a visszaállításra kiválasztott fájlmegosztás helyreállítási pontjának tartalmát.
8. Jelölje be a visszaállítani kívánt fájlhoz vagy mappához tartozó jelölőnégyzetet, majd kattintson a **kiválasztás**gombra.

    ![Helyreállítási célhely kiválasztása](./media/restore-afs/recovery-destination.png)

9. Ismételje meg a 6-8-es lépést több fájl/mappa kiválasztásához a visszaállításhoz.
10. Miután kiválasztotta az összes visszaállítani kívánt elemet, kattintson **az OK**gombra.

    [Az összes fájl kijelölése után kattintson az OK gombra.](./media/restore-afs/after-selecting-all-items.png)

11. Kattintson a **visszaállítás** gombra a visszaállítási művelet elindításához.

## <a name="track-restore-operation"></a>Visszaállítási művelet nyomon követése

A visszaállítási művelet elindítását követően a Backup szolgáltatás létrehoz egy feladatot a követéshez. A Azure Backup megjeleníti a feladatokkal kapcsolatos értesítéseket a portálon. Ha meg szeretné tekinteni a feladatok műveleteit, kattintson az értesítések hivatkozásra.

![Kattintson az értesítések hivatkozásra](./media/restore-afs/notifications-link.png)

A helyreállítás folyamatát a Recovery Services-tárolóból is figyelheti. A visszaállítási művelet állapotát a következő lépésekkel ellenőrizheti:

1. Nyissa meg azt a Recovery Services-tárolót, ahonnan a visszaállítási műveletet aktiválta.
2. A különböző munkaterheléseken futó műveletek állapotának megtekintéséhez kattintson az **Áttekintés** **panel figyelés szakaszában** található **biztonsági mentési feladatok** elemre.

    ![Kattintson a biztonsági mentési feladatok lehetőségre](./media/restore-afs/backup-jobs.png)

3. Kattintson a fájlmegosztás megfelelő munkaterhelésre, hogy megtekintse a visszaállítási művelet részletes adatait, például az átvitt adatokat, a visszaállított fájlok számát stb.

    ![A visszaállított részletek megtekintése](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [kezelheti az Azure-fájlmegosztás biztonsági másolatait](manage-afs-backup.md)
