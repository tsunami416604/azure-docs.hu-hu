---
title: Amikor létrehoz egy Azure virtuális Gépen az Azure Backup biztonsági mentés engedélyezése
description: Ismerteti, hogyan lehet egy Azure virtuális gép létrehozásakor az Azure Backup biztonsági mentés engedélyezése.
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: raynew
ms.openlocfilehash: d96b898c8f72abd7e4eb3522ae046e9fc926f387
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403578"
---
# <a name="enable-backup-when-you-create-an-azure-vm"></a>Biztonsági mentés engedélyezése Azure-beli virtuális gép létrehozásakor

Az Azure Backup szolgáltatás használatával az Azure virtuális gépek (VM) biztonsági mentése. Virtuális gépek biztonsági mentése egy biztonsági mentési szabályzatban megadott ütemezés szerint, és helyreállítási pontokat hoz létre, a biztonsági mentésekből. Helyreállítási pontok a Recovery Services-tárolókra vannak tárolva.

Ez a cikk részletesen bemutatja a biztonsági mentés engedélyezése, ha egy virtuális gépet (VM) hoz létre az Azure Portalon.  

## <a name="before-you-start"></a>Előkészületek

- [Ellenőrizze](backup-support-matrix-iaas.md#supported-backup-actions) mely operációs rendszerek támogatottak, ha a biztonsági mentés engedélyezése egy virtuális gép létrehozásakor.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha még nem jelentkezett be a fiókjába, jelentkezzen be a [az Azure portal](https://portal.azure.com).
 
## <a name="create-a-vm-with-backup-configured"></a>Biztonsági mentés konfigurálva a virtuális gép létrehozása

1. Az Azure Portalon, kattintson a **erőforrás létrehozása**.

2. Az Azure Marketplace-en, kattintson a **számítási**, majd válassza ki a Virtuálisgép-rendszerkép.

3. Állítsa be a szolgáltatást a virtuális gép a [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) vagy [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) utasításokat.

4. Az a **felügyeleti** lap **biztonsági mentés engedélyezése**, kattintson a **a**.
5. Az Azure Backup biztonsági mentése Recovery Services-tárolóba. Kattintson a **hozzon létre új** Ha nem rendelkezik egy meglévő tároló.
6. Fogadja el a javasolt tár nevét, vagy saját maga hozza létre.
7. Adja meg, vagy hozzon létre egy erőforráscsoportot, amelyben a tároló található. Lehet, hogy a virtuális gép erőforráscsoportja eltér az erőforrás-csoport tároló.

    ![Virtuális gépek biztonsági mentésének engedélyezése](./media/backup-during-vm-creation/enable-backup.png) 

8. Fogadja el az alapértelmezett biztonsági mentési szabályzatot, vagy módosítsa a beállításokat.
    - Biztonsági mentési szabályzat meghatározza, hogy miként gyakori biztonsági mentési pillanatképek a virtuális gép, és mennyi ideig ezeket a biztonsági másolatok megőrzése. 
    - A virtuális gép naponta egyszer készít az alapértelmezett szabályzat.
    - Testre szabhatja a saját egy Azure virtuális gép biztonsági mentések naponta vagy hetente elvégzendő biztonsági mentési szabályzat.
    - [További](backup-azure-vms-introduction.md#backup-and-restore-considerations) az Azure virtuális gépek biztonsági mentési szempontokról.
    - [További](backup-instant-restore-capability.md) kapcsolatos azonnali funkciójának helyreállításához.

      ![Alapértelmezett biztonsági mentési szabályzat](./media/backup-during-vm-creation/daily-policy.png) 


## <a name="start-a-backup-after-creating-the-vm"></a>Biztonsági mentést elindítani a virtuális gép létrehozása után 

A virtuális gépek biztonsági mentését a biztonsági mentési szabályzatának megfelelően fog futni. Azt javasoljuk azonban, hogy egy kezdeti biztonsági mentés futtatása. 

Ha a virtuális gép létrejött, tegye a következőket:

1. A virtuális gép tulajdonságai között kattintson **Backup**. A virtuális gép állapota Kezdeti biztonsági mentés függőben, amíg le nem fut a kezdeti biztonsági mentés
2. Kattintson a **biztonsági másolat készítése** egy igény szerinti biztonsági mentést futtatni.

    ![Egy igény szerinti biztonsági mentés futtatása](./media/backup-during-vm-creation/run-backup.png) 

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>A védett virtuális gépek üzembe helyezése Resource Manager-sablon használatával

Az előző lépések bemutatják, hogyan hozzon létre egy virtuális gépet, és védelmét, a Recovery Services-tárolót az Azure portal használatával. Gyorsan egy vagy több virtuális gép üzembe helyezése és azok védelme Recovery Services-tárolóban, tekintse meg a sablon [Windows virtuális gép üzembe helyezése és a biztonsági mentés engedélyezése](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).



## <a name="next-steps"></a>További lépések 

Most, hogy a virtuális gép már védett, ismerje meg, hogyan kezelheti és állíthatja vissza őket.

- [Felügyelheti és figyelheti a virtuális gépek](backup-azure-manage-vms.md) 
- [Virtuális gép visszaállítása](backup-azure-arm-restore-vms.md) 

Ha problémába ütközik, [tekintse át](backup-azure-vms-troubleshoot.md) a hibaelhárítási útmutató.
