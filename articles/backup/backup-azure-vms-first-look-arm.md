---
title: Azure-beli virtuális gép biztonsági és biztonsági másolatot a virtuális gép beállításaiból
description: Ebben a cikkben megtudhatja, hogyan készíthet biztonsági másolatot egy egyes számú Azure virtuális gépről vagy több Azure-beli virtuális gépről az Azure Backup szolgáltatással.
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: 72d6e5657add3e815bb0d77fadbdbc716712bee5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705445"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Azure-beli virtuális gép biztonsági és biztonsági másolatot a virtuális gép beállításaiból

Ez a cikk bemutatja, hogyan készíthet biztonsági másolatot az Azure-beli virtuális gépekről az [Azure Backup](backup-overview.md) szolgáltatással. Az Azure virtuális gépeiről néhány módszerrel is biztonsági másolatot tud hatolni:

- Egyetlen Azure-virtuális gép: A cikkben található utasításokat ismerteti, hogyan biztonsági másolatot egy Azure virtuális gép közvetlenül a virtuális gép beállításait.
- Több Azure-beli virtuális gép: Beállíthat egy Recovery Services-tárolót, és konfigurálhatja a biztonsági mentést több Azure-beli virtuális géphez. Ebben a [forgatókönyvben](backup-azure-arm-vms-prepare.md) kövesse az ebben a cikkben található utasításokat.

## <a name="before-you-start"></a>Előkészületek

1. [Ismerje meg,](backup-architecture.md#how-does-azure-backup-work) hogyan működik a biztonsági mentés, és [ellenőrizze a](backup-support-matrix.md#azure-vm-backup-support) támogatási követelményeket.
2. Az Azure virtuális gépek biztonsági mentésének [áttekintése.](backup-azure-vms-introduction.md)

### <a name="azure-vm-agent-installation"></a>Az Azure virtuálisgép-ügynök telepítése

Az Azure-beli virtuális gépek biztonsági mentése érdekében az Azure Backup telepít egy bővítményt a gépen futó virtuálisgép-ügynökre. Ha a virtuális gép egy Azure piactéri lemezképből lett létrehozva, az ügynök futni fog. Bizonyos esetekben például egy egyéni virtuális gépet hoz létre, vagy áttelepít egy számítógépet a helyszíni. előfordulhat, hogy manuálisan kell telepítenie az ügynököt.

- Ha manuálisan kell telepítenie a virtuálisgép-ügynököt, kövesse a [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) virtuális gépekre vonatkozó utasításokat.
- Az ügynök telepítése után, amikor engedélyezi a biztonsági mentést, az Azure Backup telepíti a biztonsági mentési bővítményt az ügynökhöz. Frissíti és kifoltezi a bővítményt felhasználói beavatkozás nélkül.

## <a name="back-up-from-azure-vm-settings"></a>Biztonsági másolatot az Azure virtuális gép beállításairól

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Kattintson a **Minden szolgáltatás elemre,** és a Szűrő mezőbe írja be a **Virtuális gépek**, majd a Virtuális **gépek (Virtuális gépek) (Virtuális gépek)** elemre.
3. A virtuális gépek listájából válassza ki a biztonsági másolatot létrehozni kívánt virtuális gépet.
4. Kattintson a Virtuálisgép menü **Biztonsági másolat parancsára.**
5. A **Recovery Services-tárolóban**tegye a következőket:
   - Ha már rendelkezik tárolóval, kattintson **a Meglévő kiválasztása**elemre, és válasszon ki egy tárolót.
   - Ha nem rendelkezik trezornal, kattintson **az Új létrehozása gombra.** Adja meg a tároló nevét. Ugyanabban a régióban és erőforráscsoportban jön létre, mint a virtuális gép. Ezeket a beállításokat nem módosíthatja, ha közvetlenül a virtuális gép beállításaiból engedélyezi a biztonsági mentést.

   ![Biztonsági mentés engedélyezése varázsló](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. A **Biztonsági másolat megadása területen**tegye a következőket:

   - Hagyja meg az alapértelmezett házirendet. Ez a megadott időpontban naponta egyszer biztonsági másolatot készít a virtuális gépről, és 30 napig megőrzi a biztonsági másolatokat a tárolóban.
   - Ha rendelkezik ilyen, válasszon ki egy meglévő biztonsági mentési szabályzatot.
   - Hozzon létre egy új házirendet, és adja meg a házirend-beállításokat.  

   ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Kattintson **a Biztonsági másolat engedélyezése gombra.** Ez társítja a biztonsági mentési szabályzatot a virtuális géphez.

    ![Biztonsági mentés engedélyezése gomb](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. Nyomon követheti a konfigurációs folyamataait a portálértesítéseiben.
9. A feladat befejezése után kattintson a Virtuálisgép menü **Biztonsági másolat parancsára.** A lap a virtuális gép biztonsági mentési állapotát, a helyreállítási pontokra, a futó feladatokra és a kiadott riasztásokra vonatkozó információkat jeleníti meg.

   ![Biztonsági mentés állapota](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. A biztonsági mentés engedélyezése után egy kezdeti biztonsági mentés fut. Azonnal elindíthatja a kezdeti biztonsági mentést, vagy megvárhatja, amíg elindul a biztonsági mentésütemezésnek megfelelően.
    - Amíg a kezdeti biztonsági mentés befejeződik, az **utolsó biztonsági mentés állapota** figyelmeztetésként jelenik meg **(kezdeti biztonsági mentés függőben)**.
    - Ha meg szeretné tekinteni, hogy mikor fut a következő ütemezett biztonsági mentés, kattintson a biztonsági másolat nevére.

## <a name="run-a-backup-immediately"></a>Biztonsági másolat azonnali futtatása

1. A biztonsági mentés azonnali futtatásához kattintson a Virtuálisgép menü > **Biztonsági mentés e-biztonsági mentése parancsra.** **Backup**

    ![Biztonsági mentés futtatása](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. A **Biztonsági másolat most** a naptár vezérlővel választhatja ki, amíg a helyreállítási pont meg nem őrződik > és az OK **gombra .**

    ![Biztonsági mentés megőrzési napja](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. A portálértesítések tudatják Önvel, hogy a biztonsági mentési feladat aktiválva van. A biztonsági mentés állapotának figyeléséhez kattintson **az Összes feladat megtekintése gombra.**

## <a name="back-up-from-the-recovery-services-vault"></a>Biztonsági mentés a Helyreállítási szolgáltatások tárolójából

Kövesse az ebben a cikkben található utasításokat az Azure-beli virtuális gépek biztonsági mentésének engedélyezéséhez egy Azure Backup Recovery Services-tároló beállításával, és a biztonsági mentés engedélyezésével a tárolóban.

>[!NOTE]
> **Az Azure Backup mostantól támogatja a szelektív lemezbiztonsági mentést és visszaállítást az Azure virtuális gép biztonsági mentési megoldásával.**
>
>Az Azure Backup ma támogatja az összes lemez (operációs rendszer és adatok) biztonsági mentését a virtuális gépben a virtuális gép biztonsági mentési megoldásának használatával. A exclude-disk funkcióval lehetőséget kap arra, hogy egy vagy néhány biztonsági másolatot készítsen a virtuális gép számos adatlemezéről. Ez hatékony és költséghatékony megoldást nyújt a biztonsági mentési és visszaállítási igényekhez. Minden helyreállítási pont a biztonsági mentési műveletben szereplő lemezek adatait tartalmazza, ami lehetővé teszi, hogy a lemezek egy részhalmaza visszaálljon az adott helyreállítási pontról a visszaállítási művelet során. Ez a pillanatképből és a tárolóból történő visszaállításra vonatkozik.
>
>**Az előnézetre való feliratkozáshoz írjon nekünk aAskAzureBackupTeam@microsoft.com**

## <a name="next-steps"></a>További lépések

- Ha nehézségei vannak a cikkben szereplő eljárások bármelyikével, olvassa el a [hibaelhárítási útmutatót.](backup-azure-vms-troubleshoot.md)
- További információ a biztonsági mentések [kezeléséről.](backup-azure-manage-vms.md)
