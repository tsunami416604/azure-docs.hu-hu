---
title: Azure-beli virtuális gép biztonsági mentése a virtuális gép beállításaiból Azure Backup
description: Ebből a cikkből megtudhatja, hogyan készíthet biztonsági mentést egy egyedi Azure-beli vagy több Azure-beli virtuális gépről a Azure Backup szolgáltatással.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: dacurwin
ms.openlocfilehash: 232c027ef60a031df53f2439586a0ae459ab67d5
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73747241"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>Azure-beli virtuális gép biztonsági mentése a virtuális gép beállításaiból

Ez a cikk azt ismerteti, hogyan lehet biztonsági másolatot készíteni az Azure-beli virtuális gépekről a [Azure Backup](backup-overview.md) szolgáltatással. Az Azure-beli virtuális gépek biztonsági mentését több módszer használatával végezheti el:

- Egyetlen Azure-beli virtuális gép: a cikk utasításai azt írják le, hogyan lehet biztonsági másolatot készíteni egy Azure-beli virtuális gépről közvetlenül a virtuális gép beállításairól.
- Több Azure-beli virtuális gép: beállíthat egy Recovery Services-tárolót, és konfigurálhat több Azure-beli virtuális gép biztonsági mentését. Ehhez a forgatókönyvhöz kövesse az [ebben a cikkben](backup-azure-arm-vms-prepare.md) szereplő utasításokat.

## <a name="before-you-start"></a>Előkészületek

1. [Ismerje meg](backup-architecture.md#how-does-azure-backup-work) , hogyan működik a biztonsági mentés, és [ellenőrizze](backup-support-matrix.md#azure-vm-backup-support) a támogatási követelményeket.
2. Az Azure-beli virtuális gépek biztonsági mentésének [áttekintése](backup-azure-vms-introduction.md) .

### <a name="azure-vm-agent-installation"></a>Azure VM-ügynök telepítése

Az Azure-beli virtuális gépek biztonsági mentéséhez Azure Backup telepít egy bővítményt a gépen futó virtuálisgép-ügynökön. Ha a virtuális gép Azure Piactéri rendszerképből lett létrehozva, az ügynök futni fog. Bizonyos esetekben például létrehozhat egy egyéni virtuális gépet, vagy áttelepítheti a gépet a helyszínről. Előfordulhat, hogy manuálisan kell telepítenie az ügynököt.

- Ha manuálisan kell telepítenie a virtuálisgép-ügynököt, kövesse a Windows vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) [rendszerű](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) virtuális gépekre vonatkozó utasításokat.
- Miután telepítette az ügynököt, a biztonsági mentés engedélyezésekor Azure Backup telepíti a biztonsági mentési bővítményt az ügynöknek. A felhasználó beavatkozása nélkül frissíti és kibővíti a bővítményt.

## <a name="back-up-from-azure-vm-settings"></a>Biztonsági mentés az Azure-beli virtuális gép beállításaiból

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Kattintson a **minden szolgáltatás** elemre, majd a szűrő mezőbe írja be a **virtuális gépeket**, majd kattintson a **virtuális gépek**elemre.
3. A virtuális gépek listájából válassza ki azt a virtuális gépet, amelyről biztonsági másolatot szeretne készíteni.
4. A virtuális gép menüben kattintson a **biztonsági mentés**elemre.
5. A **Recovery Services**-tárolóban tegye a következőket:
   - Ha már rendelkezik tárolóval, kattintson a **meglévő kiválasztása**elemre, majd válasszon ki egy tárolót.
   - Ha nem rendelkezik tárolóval, kattintson az **új létrehozása**lehetőségre. Adja meg a tároló nevét. Ugyanabban a régióban és erőforráscsoporthoz lett létrehozva, mint a virtuális gép. Ezek a beállítások nem módosíthatók, ha közvetlenül a virtuális gép beállításaiból engedélyezi a biztonsági mentést.

   ![Biztonsági mentés engedélyezése varázsló](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. A **biztonsági mentési szabályzat kiválasztása**területen tegye a következőket:

   - Hagyja meg az alapértelmezett házirendet. Ezzel a beállítással naponta egyszer biztonsági másolatot készít a virtuális gépről, és 30 napig megőrzi a biztonsági mentéseket a tárolóban.
   - Ha rendelkezik ilyennel, válasszon ki egy meglévő biztonsági mentési szabályzatot.
   - Hozzon létre egy új szabályzatot, és adja meg a házirend-beállításokat.  

   ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Kattintson a **biztonsági mentés engedélyezése**elemre. Ez a biztonsági mentési szabályzatot a virtuális géppel társítja.

    ![Biztonsági mentés engedélyezése gomb](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. A konfigurációs folyamat nyomon követhető a portál értesítéseiben.
9. A feladatok befejezése után a virtuális gép menüjében kattintson a **biztonsági mentés**elemre. A lapon látható a virtuális gép biztonsági mentési állapota, a helyreállítási pontokra vonatkozó információk, a futó feladatok és a kiadott riasztások.

   ![Biztonsági mentés állapota](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. A biztonsági mentés engedélyezése után a rendszer egy kezdeti biztonsági mentést futtat. A kezdeti biztonsági mentést azonnal elindíthatja, vagy megvárhatja, amíg a biztonsági mentési ütemezésnek megfelelően elindul.
    - A kezdeti biztonsági mentés befejezéséig a **legutóbbi biztonsági mentés állapota** **Figyelmeztetés (kezdeti biztonsági mentés függőben)** .
    - Ha meg szeretné tekinteni, hogy mikor fog futni a következő ütemezett biztonsági mentés, kattintson a biztonsági mentési szabályzat nevére.

> [!NOTE]
> Azure Backup szolgáltatás létrehoz egy különálló erőforráscsoportot (a virtuálisgép-erőforráscsoport kivételével) a pillanatkép tárolására a **AzureBackupRG_geography_number** elnevezési formátumával (például: AzureBackupRG_northeurope_1). Az ebben az erőforráscsoportban található adatok az Azure-beli virtuális gép biztonsági mentési házirendjének "az azonnali helyreállítási pillanatkép megtartása" szakaszában megadott időtartam alatt lesznek tárolva. Az erőforráscsoport zárolásának alkalmazása biztonsági mentési hibákhoz vezethet.<br>
Ezt az erőforráscsoportot ki kell zárni bármely név/címke korlátozásból, mivel a korlátozási szabályzat letilthatja az erőforrás-pont gyűjtemények létrehozását a biztonsági mentési hibák miatt.

## <a name="run-a-backup-immediately"></a>Biztonsági mentés azonnali futtatása

1. A biztonsági mentés azonnali futtatásához a virtuális gép menüjében kattintson a **biztonsági** mentés > **biztonsági mentés most**lehetőségre.

    ![Biztonsági mentés futtatása](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. A **biztonsági mentés most** a Calendar (naptár) vezérlőelem használatával válassza ki, hogy a helyreállítási pont Mikor maradjon meg > és **az OK gombra**.

    ![Biztonsági mentés megőrzésének napja](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. A portál értesítéseiből megtudhatja, hogy a biztonsági mentési feladatok aktiválva lettek. A biztonsági mentés előrehaladásának figyeléséhez kattintson az **összes feladat megtekintése**elemre.

## <a name="back-up-from-the-recovery-services-vault"></a>Biztonsági mentés a Recovery Services-tárból

A cikk utasításait követve engedélyezheti az Azure-beli virtuális gépek biztonsági mentését egy Azure Backup Recovery Services-tároló beállításával, valamint a biztonsági mentés engedélyezésével a tárban.

## <a name="next-steps"></a>További lépések

- Ha a jelen cikkben ismertetett eljárások valamelyikével kapcsolatban nehézségekbe ütközik, tekintse meg a [hibaelhárítási útmutatót](backup-azure-vms-troubleshoot.md).
- [További](backup-azure-manage-vms.md) információ a biztonsági másolatok kezeléséről.
