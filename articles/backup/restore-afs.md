---
title: Azure-fájlmegosztás visszaállítása
description: Ebből a témakörből megtudhatja, hogyan állíthatja vissza a teljes fájlmegosztást vagy adott fájlokat a Azure Backup által létrehozott visszaállítási pontról a Azure Portal használatával.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: 5668328637ae9b5a5dd3db02085a7f15de2a2456
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82980645"
---
# <a name="restore-azure-file-shares"></a>Azure-fájlmegosztás visszaállítása

Ez a cikk azt ismerteti, hogyan lehet a Azure Portal használatával visszaállítani egy teljes fájlmegosztást vagy adott fájlokat a [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview)által létrehozott visszaállítási pontról.

Ebből a cikkből megtudhatja, hogyan végezheti el a következőket:

* Teljes Azure-fájlmegosztás visszaállítása.
* Egyéni fájlok vagy mappák visszaállítása.
* A visszaállítási művelet állapotának nyomon követése.

## <a name="steps-to-perform-a-restore-operation"></a>A visszaállítási művelet végrehajtásának lépései

A visszaállítási művelet végrehajtásához kövesse az alábbi lépéseket.

### <a name="select-the-file-share-to-restore"></a>Válassza ki a visszaállítani kívánt fájlmegosztást

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a fájlmegosztás biztonsági mentésének konfigurálásához használt Recovery Services-tárolót.

1. Az Áttekintés panelen válassza a **védett elemek** szakaszban található **biztonsági másolati elemek** lehetőséget.

    ![Biztonsági másolati elemek kiválasztása](./media/restore-afs/backup-items.png)

1. A **biztonsági másolati elemek**kiválasztása után megnyílik egy új ablaktábla, amely felsorolja az összes biztonságimásolat-kezelési típust az Áttekintés ablaktábla mellett.

    ![Biztonságimásolat-felügyeleti típusok](./media/restore-afs/backup-management.png)

1. A **biztonsági mentési elemek** ablaktáblán, a **biztonsági mentési felügyelet típusa**területen válassza az **Azure Storage (Azure Files)** lehetőséget. Ekkor megjelenik az összes fájlmegosztás és a hozzájuk tartozó Storage-fiókok listája a tároló használatával.

    ![Az összes fájlmegosztás listája](./media/restore-afs/file-shares.png)

1. Az Azure-fájlmegosztás listájából válassza ki azt a fájlmegosztást, amelynek el szeretné végezni a visszaállítási műveletet.

### <a name="full-share-recovery"></a>Teljes megosztás helyreállítása

Ezzel a visszaállítási lehetőséggel visszaállíthatja a teljes fájlmegosztást az eredeti helyen vagy egy másik helyen.

1. Válassza a **megosztás visszaállítása** lehetőséget a **biztonsági másolati elem** ablaktáblán, amely akkor jelenik meg, ha a fájlmegosztást a visszaállítandó [fájlmegosztás kiválasztása](#select-the-file-share-to-restore) szakasz 5. lépésében azt követően megjelenő.

   ![Megosztás visszaállításának kiválasztása](./media/restore-afs/restore-share.png)

1. A **megosztás visszaállítása**lehetőség kiválasztását követően megnyílik a **visszaállítás** panel. A visszaállítási művelet végrehajtásához használni kívánt visszaállítási pont kiválasztásához kattintson **a hivatkozás szövege elemre** a **visszaállítási pont** szövegmező alatt.

    ![Kattintson a kijelölés gombra a visszaállítási pont kiválasztásához.](./media/restore-afs/select-restore-point.png)

1. A jobb oldalon megnyílik a **visszaállítási pont** környezetének kiválasztása ablaktábla, amely felsorolja a kiválasztott fájlmegosztás számára elérhető visszaállítási pontokat. Válassza ki azt a visszaállítási pontot, amelyet a visszaállítási művelet végrehajtásához használni szeretne, majd kattintson **az OK gombra**.

    ![Visszaállítási pont kiválasztása](./media/restore-afs/restore-point.png)

    >[!NOTE]
    >Alapértelmezés szerint a **visszaállítási pont kiválasztása** ablaktábla felsorolja az elmúlt 30 nap visszaállítási pontjait. Ha egy adott időtartam alatt létrehozott visszaállítási pontokat szeretné megtekinteni, adja meg a tartományt a megfelelő **kezdési** és **befejezési időpontot** kiválasztva, majd kattintson a **frissítés** gombra.

1. A következő lépés a **visszaállítás helyének**kiválasztása. A **Helyreállítás célhelye** szakaszban adja meg, hogy hol vagy hogyan szeretné visszaállítani az adatgyűjtést. A váltógomb használatával válassza ki a következő két lehetőség egyikét:

    * **Eredeti hely**: állítsa vissza a teljes fájlmegosztást ugyanarra a helyre, mint az eredeti forrást.
    * **Másik hely**: állítsa vissza a teljes fájlmegosztást egy másik helyre, és tartsa meg az eredeti fájlmegosztást.

#### <a name="restore-to-the-original-location"></a>Visszaállítás az eredeti helyre

1. Válassza ki a **helyreállítási célhelyként**az **eredeti helyet** , és válassza ki, hogy az ütközések esetén ki kell-e hagyni vagy felülírja a megfelelő lehetőséget az **ütközések** legördülő listájának kiválasztásával.

1. A visszaállítási művelet elindításához válassza a **visszaállítás** lehetőséget.

    ![Az indításhoz válassza a visszaállítás lehetőséget.](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Visszaállítás másik helyre

1. Válasszon **másik helyet** a **helyreállítási célhelyként**.
1. Válassza ki azt a cél Storage-fiókot, ahol vissza kívánja állítani a biztonsági másolatban szereplő tartalmat a **Storage-fiók** legördülő listájából.
1. A **fájlmegosztás kiválasztása** legördülő lista megjeleníti a 2. lépésben kiválasztott Storage-fiókban lévő fájlmegosztást. Válassza ki azt a fájlmegosztást, amelyben vissza kívánja állítani a biztonsági másolatok tartalmát.
1. A **mappanév** mezőben adja meg a visszaállított tartalommal a célfájl-megosztásban létrehozni kívánt mappanevet.
1. Válassza ki, hogy a rendszer kihagyja vagy felülírja-e az ütközéseket.
1. A megfelelő értékek az összes mezőben való megadása után válassza a **visszaállítás** lehetőséget a visszaállítási művelet elindításához.

    ![Másik hely kiválasztása](./media/restore-afs/alternate-location.png)

### <a name="item-level-recovery"></a>Elemszintű helyreállítás

Ezzel a visszaállítási lehetőséggel visszaállíthatja az egyes fájlokat vagy mappákat az eredeti helyen vagy egy másik helyen.

1. Válassza ki a **Fájl-helyreállítási** lehetőséget a **biztonsági másolati elem** panelen, amely akkor jelenik meg, ha a fájlmegosztást a visszaállítandó [fájlmegosztás kiválasztása](#select-the-file-share-to-restore) szakasz 5. lépésében megjelenő gombra kattint.

    ![Fájl helyreállításának kiválasztása](./media/restore-afs/file-recovery.png)

1. A fájl- **helyreállítás**lehetőség kiválasztását követően megnyílik a **visszaállítás** panel. A visszaállítási művelet végrehajtásához használni kívánt visszaállítási pont kiválasztásához kattintson **a hivatkozás szövege elemre** a **visszaállítási pont** szövegmező alatt.

    ![Kattintson a kijelölés gombra a visszaállítási pont kiválasztásához.](./media/restore-afs/select-restore-point.png)

1. A jobb oldalon megnyílik a **visszaállítási pont** környezetének kiválasztása ablaktábla, amely felsorolja a kiválasztott fájlmegosztás számára elérhető visszaállítási pontokat. Válassza ki azt a visszaállítási pontot, amelyet a visszaállítási művelet végrehajtásához használni szeretne, majd kattintson **az OK gombra**.

    ![Visszaállítási pont kiválasztása](./media/restore-afs/restore-point.png)

1. A következő lépés a **visszaállítás helyének**kiválasztása. A **Helyreállítás célhelye** szakaszban adja meg, hogy hol vagy hogyan szeretné visszaállítani az adatgyűjtést. A váltógomb használatával válassza ki a következő két lehetőség egyikét:

    * **Eredeti hely**: a kijelölt fájlok vagy mappák visszaállítása az eredeti forrással megegyező fájlmegosztást.
    * **Másik hely**: állítsa vissza a kijelölt fájlokat vagy mappákat egy másik helyre, és tartsa meg az eredeti fájlmegosztás tartalmát.

#### <a name="restore-to-the-original-location"></a>Visszaállítás az eredeti helyre

1. Válassza ki az **eredeti helyet** **helyreállítási célhelyként**, és válassza ki, hogy ki szeretné-e hagyni vagy felülírja-e az ütközéseket, ha az **ütközések** legördülő listában a megfelelő lehetőséget választja.

    ![Az elemszintű helyreállítás eredeti helye](./media/restore-afs/original-location-item-level.png)

1. A visszaállítani kívánt fájlok vagy mappák kiválasztásához kattintson a **fájl hozzáadása** gombra. Ekkor megnyílik a jobb oldalon egy helyi ablaktábla, amely megjeleníti a visszaállításhoz kiválasztott fájlmegosztás-helyreállítási pont tartalmát.

    ![Válassza a fájl hozzáadása lehetőséget](./media/restore-afs/add-file.png)

1. Jelölje be a visszaállítani kívánt fájlhoz vagy mappához tartozó jelölőnégyzetet, majd válassza a **kiválasztás**lehetőséget.

    ![Fájl vagy mappa kiválasztása](./media/restore-afs/select-file-folder.png)

1. Ismételje meg a 2 – 4. lépést több fájl vagy mappa kiválasztásához a visszaállításhoz.
1. Miután kiválasztotta az összes visszaállítani kívánt elemet, válassza a **visszaállítás** lehetőséget a visszaállítási művelet elindításához.

    ![Az indításhoz válassza a visszaállítás lehetőséget.](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Visszaállítás másik helyre

1. Válasszon **másik helyet** a **helyreállítási célhelyként**.
1. Válassza ki azt a cél Storage-fiókot, ahol vissza kívánja állítani a biztonsági másolatban szereplő tartalmat a **Storage-fiók** legördülő listájából.
1. A **fájlmegosztás kiválasztása** legördülő lista megjeleníti a 2. lépésben kiválasztott Storage-fiókban lévő fájlmegosztást. Válassza ki azt a fájlmegosztást, amelyben vissza kívánja állítani a biztonsági másolatok tartalmát.
1. A **mappanév** mezőben adja meg a visszaállított tartalommal a célfájl-megosztásban létrehozni kívánt mappanevet.
1. Válassza ki, hogy a rendszer kihagyja vagy felülírja-e az ütközéseket.
1. A visszaállítani kívánt fájlok vagy mappák kiválasztásához kattintson a **fájl hozzáadása** gombra. Ekkor megnyílik a jobb oldali helyi ablaktábla, amely a visszaállításhoz kiválasztott fájlmegosztás-helyreállítási pont tartalmát jeleníti meg.

    ![Válassza ki azokat az elemeket, amelyeket vissza szeretne állítani a másik helyre](./media/restore-afs/restore-to-alternate-location.png)

1. Jelölje be a visszaállítani kívánt fájlhoz vagy mappához tartozó jelölőnégyzetet, majd válassza a **kiválasztás**lehetőséget.

    ![Helyreállítási célhely kiválasztása](./media/restore-afs/recovery-destination.png)

1. Ismételje meg a 6 – 8. lépést a visszaállításhoz több fájl vagy mappa kiválasztásához.
1. Miután kiválasztotta az összes visszaállítani kívánt elemet, válassza a **visszaállítás** lehetőséget a visszaállítási művelet elindításához.

    ![Az összes fájl kijelölése után kattintson az OK gombra.](./media/restore-afs/after-selecting-all-items.png)

## <a name="track-a-restore-operation"></a>Visszaállítási művelet nyomon követése

A visszaállítási művelet elindítását követően a Backup szolgáltatás létrehoz egy feladatot a követéshez. A Azure Backup megjeleníti a feladatokkal kapcsolatos értesítéseket a portálon. A feladatokhoz tartozó műveletek megtekintéséhez válassza az értesítések hiperhivatkozást.

![Értesítések hiperhivatkozásának kiválasztása](./media/restore-afs/notifications-link.png)

A visszaállítási folyamat figyelését a Recovery Services-tárolóból is nyomon követheti:

1. Nyissa meg azt a Recovery Services-tárolót, ahonnan a visszaállítási műveletet aktiválta.
1. Az Áttekintés ablaktáblán válassza a **figyelés** szakaszban található **biztonsági mentési feladatok** lehetőséget a különböző munkaterheléseken futó műveletek állapotának megtekintéséhez.

    ![Biztonsági mentési feladatok kiválasztása](./media/restore-afs/backup-jobs.png)

1. Válassza ki a fájlmegosztás megfelelő munkaterhelési nevet a visszaállítási művelet részletes adatainak megtekintéséhez, például az **átvitt adatokhoz** és a **visszaállított fájlok számához**.

    ![A visszaállított részletek megtekintése](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [kezelheti az Azure-fájlmegosztás biztonsági másolatait](manage-afs-backup.md).
