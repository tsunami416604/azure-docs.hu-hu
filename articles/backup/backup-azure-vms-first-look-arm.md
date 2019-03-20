---
title: Biztonsági mentés egy Azure virtuális Gépen a virtuális gép beállításait, az Azure Backup szolgáltatással
description: Ismerje meg, hogyan készíthet biztonsági másolatot egy Azure virtuális Gépen az Azure Backup szolgáltatással
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/17/2019
ms.author: raynew
ms.openlocfilehash: 2fe786d90612feff312983dbd25dc6d691be6e70
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58089777"
---
# <a name="back-up-an-azure-vm-from-the-vm-settings"></a>A virtuális gép beállításainak biztonsági mentése Azure virtuális gép

Ez a cikk bemutatja, hogyan készíthet biztonsági másolatot az Azure virtuális gépek a [Azure Backup](backup-overview.md) szolgáltatás. Készíthet biztonsági másolatot az Azure virtuális gépek többféleképpen használatával:

- Egyetlen Azure virtuális Gépen: Ez a cikk utasításait ismerteti, hogyan közvetlenül a virtuális gép beállításainak biztonsági mentése Azure virtuális Gépeken.
- Több Azure virtuális gépek: Állítsa be a Recovery Services-tárolót, és a biztonsági mentés konfigurálása több Azure-beli virtuális gépek. Kövesse a [Ez a cikk](backup-azure-arm-vms-prepare.md) ehhez a forgatókönyvhöz.

 

## <a name="before-you-start"></a>Előkészületek

1. [Ismerje meg,](backup-architecture.md#how-does-azure-backup-work) backup működését, és [ellenőrzése](backup-support-matrix.md#azure-vm-backup-support) követelmények támogatására. 
2. [Áttekintés](backup-azure-vms-introduction.md) az Azure virtuális gépek biztonsági mentését.

### <a name="azure-vm-agent-installation"></a>Azure Virtuálisgép-ügynök telepítése

Annak érdekében, hogy az Azure virtuális gépek biztonsági mentése, az Azure Backup egy kiterjesztést telepít a Virtuálisgép-ügynök fut a gépen. Ha a virtuális gép az Azure marketplace-lemezképből lett létrehozva, akkor az ügynök fog futni. Bizonyos esetekben, például ha egy egyéni virtuális Gépet hoz létre, vagy telepít át egy helyszíni. szüksége lehet az ügynök manuális telepítése. 

- Ha a Virtuálisgép-ügynök manuális telepítése, kövesse a következő útmutatót: [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) virtuális gépeket. 
- Az ügynök telepítése után, ha engedélyezi a biztonsági mentés, az Azure Backup telepíti a biztonsági mentési bővítményt az ügynököt. Ez frissíti, és a javítások a bővítményt, felhasználói beavatkozás nélkül.

## <a name="back-up-from-azure-vm-settings"></a>Az Azure-beli Virtuálisgép-beállítások biztonsági mentése


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Kattintson a **minden szolgáltatás** , írja be a szűrő **virtuális gépek**, és kattintson a **virtuális gépek**. 
3. A virtuális gépek listájából válassza ki a virtuális gép biztonsági.
4. A virtuális gép menüben kattintson a **Backup**. 
5. A **Recovery Services-tároló**, tegye a következőket:
   - Ha már rendelkezik tárolóval, kattintson a **válasszon meglévő**, és válassza ki a tárolót.
   - Ha nem rendelkezik egy tárolót, kattintson a **új létrehozása**. Adja meg a tároló nevét. Azonos régió és az erőforrás tartozik, mint a virtuális gép létrejön. Ezek a beállítások nem módosíthatók közvetlenül a virtuális gép beállításait a biztonsági mentésének engedélyezésekor.

   ![Biztonsági mentés engedélyezése varázsló](./media/backup-azure-vms-first-look-arm/vm-menu-enable-backup-small.png)

6. A **biztonsági mentési házirend kiválasztása**, tegye a következőket:

   - Hagyja bejelölve az alapértelmezett szabályzat. Virtuális gép megadott időpontban naponta biztonsági mentését, és 30 napig őrzi meg a biztonsági másolatok a tárolóban.
   - Ha rendelkezik ilyennel, válassza ki egy meglévő biztonsági mentési szabályzatot.
   - Hozzon létre egy új szabályzatot, és a házirend-beállítások megadása.  

   ![Biztonsági mentési házirend kiválasztása](./media/backup-azure-vms-first-look-arm/set-backup-policy.png)

7. Kattintson a **biztonsági mentés engedélyezése**. A biztonsági mentési házirendet társítja a virtuális Gépet. 

    ![Biztonsági mentés engedélyezése gomb](./media/backup-azure-vms-first-look-arm/vm-management-menu-enable-backup-button.png)

8. A konfigurációs folyamat előrehaladását a portál értesítési a követheti nyomon.
9. Miután a feladat befejeződik, a virtuális gép menüben kattintson **Backup**. Az oldal biztonsági mentési állapotát a virtuális gép, a helyreállítási pontok, a futó feladatok és a kiadott riasztások adatait jeleníti meg.

   ![Biztonsági mentés állapota](./media/backup-azure-vms-first-look-arm/backup-item-view-update.png)

10. Miután engedélyezte a biztonsági mentés, egy kezdeti biztonsági mentés futtatása. A kezdeti biztonsági mentés azonnali indítása, vagy várjon, amíg a biztonsági mentési ütemezés szerint kezdődik.
    - A kezdeti biztonsági mentés befejezéséig az **legutóbbi biztonsági mentés állapota** állapota **figyelmeztetés (kezdeti biztonsági mentés folyamatban)**.
    - A következő ütemezett biztonsági mentés futtatásakor megtekintéséhez kattintson a biztonsági mentési szabályzat neve.
    
   

> [!NOTE]
> Az Azure Backup tárolja a helyreállítási pontokat az elnevezési formátummal hoz létre egy külön erőforráscsoportot (nem a virtuális gép erőforráscsoportja) **AzureBackupRG_geography_number** (Példa: AzureBackupRG_northeurope_1). Ez az erőforráscsoport nem szabad zárolás.



## <a name="run-a-backup-immediately"></a>A biztonsági mentés azonnali futtatása 

1. Egy biztonsági mentést, azonnal futtatni a virtuális gép menüben kattintson a **biztonsági mentési** > **biztonsági mentés**.

    ![Biztonsági mentés futtatása](./media/backup-azure-vms-first-look-arm/backup-now-update.png)

2. A **biztonsági mentés** használja a naptárvezérlőt annak csak válassza ki, amikor a rendszer megőrzi a helyreállítási pont >, és **OK**.
  
    ![A biztonsági másolatok nap](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

3. Portál értesítések értesítik, hogy a biztonsági mentési feladat elindult. Kattintson a biztonsági mentési folyamat állapotának monitorozásához **az összes feladat megtekintése**.




## <a name="back-up-from-the-recovery-services-vault"></a>Biztonsági mentés a Recovery Services-tároló

Kövesse az utasításokat ebben a cikkben az Azure virtuális gépek biztonsági mentésének engedélyezése az Azure biztonsági mentési Recovery Services-tároló beállításával, és a tárolóban lévő biztonsági mentés engedélyezése.

## <a name="next-steps"></a>További lépések

- Ha ez a cikk azokkal az eljárásokkal nehézségeket, tekintse meg a [hibaelhárítási útmutató](backup-azure-vms-troubleshoot.md).
- [Ismerje meg](backup-azure-manage-vms.md) a biztonsági másolatok kezelése.

