---
title: Biztonsági mentés engedélyezése Azure-beli virtuális gép létrehozásakor Azure Backup
description: Ismerteti, hogyan engedélyezhető a biztonsági mentés, ha Azure-beli virtuális gépet hoz létre Azure Backup használatával.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: dacurwin
ms.openlocfilehash: 90f69371457bbfe37789b12971343f738ff35e8e
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639715"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Biztonsági mentés engedélyezése Azure-beli virtuális gép létrehozásakor

Az Azure Virtual Machines (VM) biztonsági mentéséhez használja a Azure Backup szolgáltatást. A virtuális gépek a biztonsági mentési szabályzatban megadott ütemezésnek megfelelően készülnek, a helyreállítási pontok pedig biztonsági másolatokból jönnek létre. A helyreállítási pontok tárolása Recovery Services-tárolókban történik.

Ez a cikk azt ismerteti, hogyan engedélyezhető a biztonsági mentés a virtuális gép (VM) a Azure Portalban történő létrehozásakor.  

## <a name="before-you-start"></a>Előkészületek

- Győződjön meg arról, [hogy mely operációs](backup-support-matrix-iaas.md#supported-backup-actions) rendszerek támogatottak, ha a virtuális gép létrehozásakor engedélyezi a biztonsági mentést.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha még nem jelentkezett be a fiókjába, jelentkezzen be a [Azure Portalba](https://portal.azure.com).

## <a name="create-a-vm-with-backup-configured"></a>Virtuális gép létrehozása a Backup szolgáltatással konfigurálva

1. A Azure Portal kattintson az **erőforrás létrehozása**elemre.

2. Az Azure Marketplace-en kattintson a **számítás**elemre, majd válasszon ki egy virtuálisgép-rendszerképet.

3. Állítsa be a virtuális gépet a [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) -vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) -utasításoknak megfelelően.

4. A **kezelés** lap **biztonsági mentés engedélyezése**területén kattintson **a be**gombra.
5. Azure Backup biztonsági mentéseket egy Recovery Services-tárolóba. Kattintson az **új létrehozása** lehetőségre, ha nem rendelkezik meglévő tárolóval.
6. Fogadja el a javasolt tár nevét, vagy adja meg a sajátját.
7. Itt adhatja meg vagy hozhatja létre azt az erőforráscsoportot, amelyben a tároló található. Az erőforráscsoport-tároló eltérő lehet a virtuálisgép-erőforráscsoporthoz.

    ![Virtuális gép biztonsági mentésének engedélyezése](./media/backup-during-vm-creation/enable-backup.png)

8. Fogadja el az alapértelmezett biztonsági mentési szabályzatot, vagy módosítsa a beállításokat.
    - A biztonsági mentési szabályzat meghatározza, hogy a virtuális gép milyen gyakran készítsen biztonsági mentési pillanatképeket, és hogy mennyi ideig tart a biztonsági másolatok megőrzése.
    - Az alapértelmezett házirend naponta egyszer biztonsági másolatot készít a virtuális gépről.
    - Az Azure-beli virtuális gépekre vonatkozó saját biztonsági mentési szabályzatok testreszabásával napi vagy heti biztonsági mentést készíthet.
    - [További](backup-azure-vms-introduction.md#backup-and-restore-considerations) információ az Azure-beli virtuális gépek biztonsági mentésével kapcsolatos megfontolásokról.
    - [További](backup-instant-restore-capability.md) információ az azonnali visszaállítási funkcióról.

      ![Alapértelmezett biztonsági mentési szabályzat](./media/backup-during-vm-creation/daily-policy.png)


> [!NOTE]
> Azure Backup szolgáltatás létrehoz egy különálló erőforráscsoportot (a virtuálisgép-erőforráscsoport kivételével) a pillanatkép tárolására a **AzureBackupRG_geography_number** elnevezési formátumával (példa: AzureBackupRG_northeurope_1). Az ebben az erőforráscsoportban található adatok az Azure-beli virtuális gép biztonsági mentési szabályzatának az *azonnali helyreállítási pillanatkép* megtartása szakaszában megadott időtartam alatt lesznek tárolva.  Az erőforráscsoport zárolásának alkalmazása biztonsági mentési hibákhoz vezethet.<br>
Ezt az erőforráscsoportot ki kell zárni bármely név/címke korlátozásból, mivel a korlátozási szabályzat letilthatja az erőforrás-pont gyűjtemények létrehozását a biztonsági mentési hibák miatt.


## <a name="start-a-backup-after-creating-the-vm"></a>Biztonsági mentés indítása a virtuális gép létrehozása után

A virtuális gép biztonsági mentése a biztonsági mentési szabályzatnak megfelelően fog futni. Javasoljuk azonban, hogy futtasson egy kezdeti biztonsági mentést.

A virtuális gép létrehozása után tegye a következőket:

1. A virtuális gép tulajdonságainál kattintson a **biztonsági mentés**elemre. A virtuális gép állapota kezdeti biztonsági mentés, amíg a kezdeti biztonsági mentés nem fut
2. Kattintson a **biztonsági mentés most** lehetőségre egy igény szerinti biztonsági mentés futtatásához.

    ![Igény szerinti biztonsági mentés futtatása](./media/backup-during-vm-creation/run-backup.png)

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>Védett virtuális gép üzembe helyezése Resource Manager-sablonnal

Az előző lépések azt ismertetik, hogyan használható a Azure Portal egy virtuális gép létrehozásához és egy Recovery Services-tárolóban való megvédéséhez. Egy vagy több virtuális gép gyors üzembe helyezéséhez és Recovery Services-tárolóban való védeleméhez tekintse meg a [Windows rendszerű virtuális gép üzembe helyezése és a biztonsági mentés engedélyezése](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)című témakört.



## <a name="next-steps"></a>További lépések

Most, hogy megvédte a virtuális gépet, megtudhatja, hogyan kezelheti és állíthatja vissza őket.

- [Virtuális gépek kezelése és figyelése](backup-azure-manage-vms.md)
- [Virtuális gép visszaállítása](backup-azure-arm-restore-vms.md)

Ha bármilyen problémába ütközik, [tekintse át](backup-azure-vms-troubleshoot.md) a hibaelhárítási útmutatót.
