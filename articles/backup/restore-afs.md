---
title: Azure-fájlmegosztások visszaállítása
description: Ismerje meg, hogyan használhatja az Azure Portalon egy teljes fájlmegosztás vagy adott fájlok visszaállításához az Azure Backup által létrehozott visszaállítási pontról.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: c22078ebd89f5f6f8299e1424d4d9e21edce8b92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586879"
---
# <a name="restore-azure-file-shares"></a>Azure-fájlmegosztások visszaállítása

Ebből a cikkből megtudhatja, hogy az Azure Portal használatával hogyan állíthatja vissza a teljes fájlmegosztást vagy adott fájlokat az [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview)által létrehozott visszaállítási pontról.

Ebben a cikkben megtudhatja, hogyan:

* Teljes Azure-fájlmegosztás visszaállítása.
* Az egyes fájlok vagy mappák visszaállítása.
* A visszaállítási művelet állapotának nyomon követése.

## <a name="steps-to-perform-a-restore-operation"></a>A visszaállítási művelet végrehajtásának lépései

Visszaállítási művelet végrehajtásához hajtsa végre az alábbi lépéseket.

### <a name="select-the-file-share-to-restore"></a>A visszaállítani kívánt fájlmegosztás kijelölése

1. Az [Azure Portalon](https://portal.azure.com/)nyissa meg a helyreállítási szolgáltatások tároló konfigurálásához használt biztonsági mentés a fájlmegosztáshoz.

1. Az áttekintő ablaktáblán válassza a **Biztonsági másolat elemek** lehetőséget a Védett **elemek** szakaszban.

    ![Biztonsági másolat elemeinek kijelölése](./media/restore-afs/backup-items.png)

1. Miután kiválasztotta a **Biztonsági másolat elemek**lehetőséget, az áttekintő ablaktábla mellett megnyílik egy új ablaktábla, amely felsorolja az összes biztonságimentés-kezelési típust.

    ![Biztonsági mentés kezelési típusai](./media/restore-afs/backup-management.png)

1. A **Biztonsági másolat elemei** ablaktáblán a **Biztonsági másolat kezelése típusa csoportban**válassza az **Azure Storage (Azure Files) lehetőséget.** Ekkor megjelenik az összes fájlmegosztás és a hozzájuk tartozó tárfiókok listája, amelyről a tároló használatával kell biztonsági másolatot kapni.

    ![Az összes fájlmegosztás listája](./media/restore-afs/file-shares.png)

1. Az Azure-fájlmegosztások listájából válassza ki azt a fájlmegosztást, amelynek a visszaállítási műveletét végre kívánja hajtani.

### <a name="full-share-recovery"></a>Teljes megosztás helyreállítása

Ezzel a visszaállítási lehetőséggel visszaállíthatja a teljes fájlmegosztást az eredeti vagy egy másik helyen.

1. Jelölje be a **Megosztás visszaállítása** jelölőnégyzetet a **Biztonsági másolat elemablakában,** amely azután jelenik meg, hogy a fájlmegosztást a [Fájlmegosztás visszaállításához](#select-the-file-share-to-restore) szakasz 5.

   ![Megosztás visszaállítása kijelölése](./media/restore-afs/restore-share.png)

1. A **Megosztás visszaállítása**lehetőséget követően a **Visszaállítás** ablaktábla megnyílik egy **Visszaállítási pont** menüvel, amely megjeleníti a kijelölt fájlmegosztáshoz rendelkezésre álló visszaállítási pontok listáját.

1. Jelölje ki a visszaállítási művelet végrehajtásához használni kívánt visszaállítási pontot, és kattintson az **OK gombra.**

    ![Visszaállítási pont kijelölése](./media/restore-afs/restore-point.png)

1. Miután az **OK**lehetőséget, a **Visszaállítás** ablaktábla menüje **a Hely visszaállítása**parancsra vált. A **Hely visszaállítása területen**adja meg, hogy hol és hogyan állítsa vissza az adatokat. Válasszon az alábbi két lehetőség közül:

    * **Eredeti hely:** Állítsa vissza a teljes fájlmegosztást ugyanarra a helyre, mint az eredeti forrás.
    * **Alternatív hely:** Állítsa vissza a teljes fájlmegosztást egy másik helyre, és tartsa meg az eredeti fájlmegosztást a helyén.

#### <a name="restore-to-the-original-location"></a>Visszaállítás az eredeti helyre

1. Válassza az **Eredeti hely lehetőséget** a helyreállítási **célként,** és adja meg, hogy ütközés esetén kihagyja vagy felülírja-e a kívánt helyet. A megfelelő kijelölés után válassza az **OK gombot.**

    ![Eredeti hely kiválasztása](./media/restore-afs/original-location.png)

1. A **visszaállítási** művelet elindításához válassza a Visszaállítás lehetőséget.

    ![A kezdéshez válassza a Visszaállítás lehetőséget.](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Visszaállítás másik helyre

1. Válassza a **Másik hely lehetőséget** a helyreállítási **célként.**
1. Válassza ki azt a céltárfiókot, ahol vissza szeretné állítani a biztonsági másolatot a **Tárfiók** legördülő listából.
1. A **Fájlmegosztás kiválasztása** legördülő lista a 2. Jelölje ki azt a fájlmegosztást, amelynek helyben vissza szeretné állítani a biztonsági másolatot.
1. A **Mappanév** mezőben adja meg azt a mappanevet, amelyet a visszaállított tartalommal rendelkező célfájlmegosztásban szeretne létrehozni.
1. Adja meg, hogy ütközés esetén kihagyja vagy felülírja-e a műveletet.
1. Miután minden mezőbe beírtuk a megfelelő értékeket, válasszuk **az OK gombot.**

    ![Alternatív hely kiválasztása](./media/restore-afs/alternate-location.png)

1. A **visszaállítási** művelet elindításához válassza a Visszaállítás lehetőséget.

    ![A kezdéshez válassza a Visszaállítás lehetőséget.](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Elemszintű helyreállítás

Ezzel a visszaállítási lehetőséggel visszaállíthatja az egyes fájlokat vagy mappákat az eredeti vagy egy másik helyen.

1. Jelölje be a **Fájlhelyreállítás** lehetőséget a **Biztonsági másolat elem ablaktáblájában,** amely azután jelenik meg, hogy a fájlmegosztást a [Fájlmegosztás visszaállításához](#select-the-file-share-to-restore) szakasz 5.

    ![Fájl-helyreállítás kiválasztása](./media/restore-afs/file-recovery.png)

1. Miután kiválasztotta a **Fájl-helyreállítás**lehetőséget, a **Visszaállítás** ablaktábla megnyílik egy **Visszaállítási pont** menüvel, amely megjeleníti a kijelölt fájlmegosztáshoz rendelkezésre álló visszaállítási pontok listáját.

1. Jelölje ki a visszaállítási művelet végrehajtásához használni kívánt visszaállítási pontot, és kattintson az **OK gombra.**

    ![Visszaállítási pont kijelölése](./media/restore-afs/restore-point.png)

1. Miután az **OK**lehetőséget, a Visszaállítás ablaktábla menüje **a Hely visszaállítása**parancsra vált. A **Hely visszaállítása területen**adja meg, hogy hol és hogyan állítsa vissza az adatokat. Válasszon az alábbi két lehetőség közül:

    * **Eredeti hely:** Állítsa vissza a kijelölt fájlokat vagy mappákat ugyanarra a fájlmegosztásra, mint az eredeti forrás.
    * **Alternatív hely:** Állítsa vissza a kijelölt fájlokat vagy mappákat egy másik helyre, és tartsa meg az eredeti fájlmegosztás tartalmát.

#### <a name="restore-to-the-original-location"></a>Visszaállítás az eredeti helyre

1. Válassza az **Eredeti hely lehetőséget** a helyreállítási **célként,** és adja meg, hogy ütközés esetén kihagyja vagy felülírja-e a kívánt helyet.

    ![Cikkszintű helyreállítás eredeti helye](./media/restore-afs/original-location-item-level.png)

1. A Visszaállítani kívánt fájlok vagy mappák kijelöléséhez válassza a **Fájl kijelölése** lehetőséget.

    ![Fájl kijelölése kiválasztása](./media/restore-afs/select-file.png)

1. A **Fájl kijelölése**lehetőséget követően a fájlmegosztási ablaktábla megjeleníti a visszaállításra kijelölt fájlmegosztás-helyreállítási pont tartalmát.

1. Jelölje be a visszaállítani kívánt fájlnak vagy mappának megfelelő jelölőnégyzetet, és válassza a **Kijelölés gombot.**

    ![Fájl vagy mappa kijelölése](./media/restore-afs/select-file-folder.png)

1. Ismételje meg a 2–4.
1. Miután kiválasztotta az összes visszaállítani kívánt elemet, válassza az **OK gombot.**

    ![A visszaállítandó összes elem kijelölése után válassza az OK gombot](./media/restore-afs/after-selecting-items.png)

1. A **visszaállítási** művelet elindításához válassza a Visszaállítás lehetőséget.

    ![A kezdéshez válassza a Visszaállítás lehetőséget.](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Visszaállítás másik helyre

1. Válassza a **Másik hely lehetőséget** a helyreállítási **célként.**
1. Válassza ki azt a céltárfiókot, ahol vissza szeretné állítani a biztonsági másolatot a **Tárfiók** legördülő listából.
1. A **Fájlmegosztás kiválasztása** legördülő lista a 2. Jelölje ki azt a fájlmegosztást, amelynek helyben vissza szeretné állítani a biztonsági másolatot.
1. A **Mappanév** mezőben adja meg azt a mappanevet, amelyet a visszaállított tartalommal rendelkező célfájlmegosztásban szeretne létrehozni.
1. Adja meg, hogy ütközés esetén kihagyja vagy felülírja-e a műveletet.
1. A Visszaállítani kívánt fájlok vagy mappák kijelöléséhez válassza a **Fájl kijelölése** lehetőséget.

    ![Jelölje ki azokat az elemeket, amelyeket vissza szeretne állítani a másik helyre](./media/restore-afs/restore-to-alternate-location.png)

1. Ha a Fájl kijelölése lehetőséget **választja,** a fájlmegosztási ablaktábla megjeleníti a visszaállításra kijelölt fájlmegosztás-helyreállítási pont tartalmát.
1. Jelölje be a visszaállítani kívánt fájlnak vagy mappának megfelelő jelölőnégyzetet, és válassza a **Kijelölés gombot.**

    ![Helyreállítási cél kiválasztása](./media/restore-afs/recovery-destination.png)

1. Ismételje meg a 6–8.
1. Miután kiválasztotta az összes visszaállítani kívánt elemet, válassza az **OK gombot.**

    ![Az összes fájl kijelölése után válassza az OK gombot](./media/restore-afs/after-selecting-all-items.png)

1. A **visszaállítási** művelet elindításához válassza a Visszaállítás lehetőséget.

## <a name="track-a-restore-operation"></a>Visszaállítási művelet nyomon követése

A visszaállítási művelet aktiválása után a biztonsági mentési szolgáltatás létrehoz egy feladatot a nyomon követéshez. Az Azure Backup értesítéseket jelenít meg a feladatról a portálon. A feladat műveleteinek megtekintéséhez jelölje ki az értesítések hivatkozását.

![Értesítések kijelölése hivatkozás](./media/restore-afs/notifications-link.png)

A Helyreállítási szolgáltatások tárolóból is figyelheti a visszaállítási folyamatot:

1. Nyissa meg a Helyreállítási szolgáltatások tárolót, ahonnan elindította a visszaállítási műveletet.
1. Az áttekintő ablaktáblán válassza **a Biztonsági mentési feladatok** lehetőséget a **Figyelés** szakaszban a különböző munkaterheléseken futó műveletek állapotának megtekintéséhez.

    ![Biztonsági mentési feladatok kiválasztása](./media/restore-afs/backup-jobs.png)

1. Válassza ki a fájlmegosztásnak megfelelő számítási feladatok nevét a visszaállítási művelet további részleteinek megtekintéséhez, például az **átvitt adatok** és **a visszaállított fájlok száma.**

    ![Lásd a visszaállított részleteket](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>További lépések

* Ismerje meg, hogyan [kezelheti az Azure fájlmegosztási biztonsági másolatokat.](manage-afs-backup.md)
