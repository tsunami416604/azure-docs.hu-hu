---
title: Azure virtuális gépek biztonsági mentésének engedélyezése a létrehozása során
description: A lépések az Azure virtuális gépek biztonsági mentésének engedélyezése a létrehozási folyamat során.
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup, virtual-machines
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: 518d171c96b9c4f9bf3e195a7130f4c022b7ad07
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879876"
---
# <a name="enable-backup-during-azure-virtual-machine-creation"></a>Biztonsági mentés engedélyezése az Azure virtuális gép létrehozása közben 

Az Azure Backup szolgáltatás létrehozása és a felhőbeli biztonsági mentések konfigurálása felületet biztosít. Az adatok védelme a biztonsági másolatok, az úgynevezett helyreállítási pontok rendszeres időközönként végrehajtásával. Az Azure Backup georedundáns helyreállítási tárolókban tárolható helyreállítási pontokat hoz létre. Ez a cikk részletesen bemutatja a biztonsági mentés engedélyezése egy virtuális gépet (VM) létrehozása közben az Azure Portalon.  

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Ha Ön nem már a fiókjával bejelentkezett, jelentkezzen be a [az Azure portal](http://portal.azure.com).
 
## <a name="create-virtual-machine-with-backup-configured"></a>Biztonsági mentés konfigurálva a virtuális gép létrehozása 

1. Az Azure portal bal felső sarkában kattintson **új**. 

2. Válassza ki **számítási**, majd válassza ki a virtuális gép egy lemezképet.   

3. Adja meg a virtuális gép adatait. A felhasználónév és a megadott jelszó segítségével jelentkezzen be a virtuális gép. Amikor végzett, kattintson az **OK** gombra. 

4. Válasszon méretet a virtuális gép számára.  

5. A **beállításai > biztonsági mentési**, kattintson a **engedélyezve** viszi, megjelenik a biztonsági mentési beállításait. Fogadja el az alapértelmezett értéket, és kattintson a **OK** összegző lapja a virtuális gép létrehozásához lépjen a beállítások oldalon. Ha azt szeretné, módosítsa az értékeket a következő lépésekkel.  

6. Létrehozhat vagy kiválaszthat egy Recovery Services-tároló, amely tárolja a virtuális gép biztonsági mentését. Recovery services-tároló létrehozásakor kiválaszthatja a tároló egy erőforráscsoportot.  

    ![A virtuális gép biztonsági mentési konfiguráció lap létrehozása](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > A Recovery Services-tároló az erőforráscsoportot, mint az erőforráscsoport, a virtuális gép eltérő lehet.  
    > 
    > 

7. Alapértelmezés szerint a biztonsági mentési szabályzatot, hogy gyorsan megvédheti a virtuális gép van jelölve. Biztonsági mentési szabályzat határozza meg, hogy milyen gyakran kell biztonsági mentési pillanatképek és a biztonsági másolatok ezeket. Elfogadhatja az alapértelmezett házirendet, vagy hozzon létre, vagy egy másik biztonsági mentési házirend kiválasztása. A biztonsági mentési szabályzat szerkesztéséhez jelölje ki **biztonsági mentési szabályzat** és módosítsa az értékeket a házirend.  

8. Ha elégedett a biztonsági mentés konfigurációs értékeket, a beállítások oldalon kattintson a **OK**.  

9. Az összefoglalás lapon, ha az érvényesítés, kattintson a **létrehozás** hozhat létre egy virtuális gépet, amely a konfigurált biztonsági mentési beállításait használja. 

## <a name="initiate-a-backup-after-creating-the-vm"></a>A virtuális gép létrehozása után egy biztonsági mentés kezdeményezéséhez 

Bár a biztonsági mentési szabályzat létrehozása után hasznos lehet egy kezdeti biztonsági mentés létrehozásához. A biztonsági mentési adatainak megtekintésére, az a virtuális gépet egyszer a virtuális gép létrehozása sablon végeztével a a **műveletek** beállítása a bal oldali menüben, kattintson a **biztonsági mentési**. Ezzel indítson egy igény szerinti biztonsági mentést, egy teljes virtuális Gépet, vagy az összes lemez visszaállítása, visszaállítja a fájlokat a virtuális gép biztonsági mentése vagy a virtuális géphez társított biztonsági mentési szabályzat módosítása.  

## <a name="using-a-resource-manager-template-to-deploy-a-protected-vm"></a>Védett virtuális gép üzembe helyezése Resource Manager-sablon használatával

Az előző lépések azt ismertetik, hogyan hozzon létre egy virtuális gépet, és védje azt egy Recovery Services-tárolót az Azure portal használatával. Ha szeretne gyorsan egy vagy több virtuális gépek üzembe helyezése és azok védelme Recovery Services-tárolóba, tekintse meg a sablon [Windows virtuális gép üzembe helyezése és a biztonsági mentés engedélyezése](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="frequently-asked-questions"></a>Gyakori kérdések 

### <a name="which-vm-images-enable-backup-at-the-time-of-vm-creation"></a>Melyik Virtuálisgép-rendszerképek biztonsági mentés engedélyezése a virtuális gép létrehozása idején? 

A biztonsági mentés engedélyezése a virtuális gépek létrehozása során az alábbi lista a Microsoft által közzétett core lemezképek támogatottak. A többi virtuális gép engedélyezheti a biztonsági mentés, a virtuális gép létrehozása után. További [virtuális gép létrehozása után, a biztonságimásolat-készítés engedélyezése](quick-backup-vm-portal.md) 

- **Windows** – Windows Server 2016 adatközpontban, a Windows Server 2016 adatközpont alapvető, a Windows Server 2012 DataCenter, Windows Server 2012 R2 DataCenter, Windows Server 2008 R2 SP1 
- **Ubuntu** – Ubuntu Server 1710-es, Ubuntu Server 1704-es, UUbuntu kiszolgáló 1604(LTS), Ubuntu Server 1404(LTS) 
- **Red Hat** -RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4 
- **SUSE** – SUSE Linux Enterprise Server 11 SP4, 12 SP2, 12 SP3 
- **Debian** – Debian 8, a Debian 9 
- **CentOS** -CentOS 6.9, a CentOS 7.3. 
- **Oracle Linux** – Oracle Linux 6.7, 6.8, 6.9, 7.2, 7.3. 
 
### <a name="is-backup-cost-included-in-the-vm-cost"></a>A virtuális gép díja magában foglalja a biztonsági mentés költsége van? 

Nem, biztonsági mentési díjakat külön, illetve különálló, a virtuális gépek költségeit. További információ a backup díjszabása: a [a Backup díjszabása hely](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Milyen engedélyek szükségesek a virtuális gép biztonsági mentésének engedélyezése? 

Ha egy virtuális gép közreműködő, engedélyezheti a virtuális gép biztonsági mentése. Egyéni szerepkör használja, ha a virtuális gép biztonsági mentésének sikeres engedélyezése a következő engedélyekkel kell. 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Ha a recovery services-tároló és a virtuális gép eltérő erőforráscsoportokban, győződjön meg a recovery services tár erőforráscsoportja van is írási engedélye.  

## <a name="next-steps"></a>További lépések 

Most, hogy a virtuális gép védetté, tekintse meg a Virtuálisgép-kezelési feladatok és a virtuális gépek visszaállítása a következő cikkeket. 

- [A virtuális gépek kezelése és figyelése](backup-azure-manage-vms.md) 
- [Virtuális gépek visszaállítása](backup-azure-arm-restore-vms.md) 
