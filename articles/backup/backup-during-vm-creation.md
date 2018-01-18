---
title: "Azure virtuális gép biztonsági mentésének engedélyezése létrehozása során |} Microsoft Docs"
description: "A lépések a virtuális gép az Azure biztonsági mentés engedélyezése a létrehozási folyamat során."
services: backup, virtual-machines
documentationcenter: 
author: markgalioto
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: 4041fc555fe4b61d10f84236dcae5156c6282fd3
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2018
---
# <a name="enable-backup-during-azure-virtual-machine-creation"></a>Biztonsági mentés engedélyezése az Azure virtuális gép létrehozása során 

Az Azure Backup szolgáltatás létrehozásához és a felhő biztonsági mentések konfigurálása felületet biztosít. Az adatok védelméhez által biztonsági másolatok, a helyreállítási pontok rendszeres időközönként nevű fogadására. Az Azure Backup georedundáns helyreállítási tárolókban tárolható helyreállítási pontokat hoz létre. Ez a cikk részletesen a biztonsági mentés engedélyezése egy virtuális gép (VM) létrehozása közben az Azure portálon.  

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba 

Ha nem már szerepel a fiókkal bejelentkezve, jelentkezzen be a [Azure-portálon](http://portal.azure.com).
 
## <a name="create-virtual-machine-with-backup-configured"></a>Biztonsági mentés konfigurálva a virtuális gép létrehozása 

1. Az Azure portál bal felső sarkában kattintson **új**. 

2. Válassza ki **számítási**, majd válassza ki a virtuális gép lemezképét.   

3. Adja meg a virtuális gép adatait. Jelentkezzen be a virtuális gép a felhasználónevet és jelszót adott meg szolgál. Amikor végzett, kattintson az **OK** gombra. 

4. Válasszon méretet a virtuális gép számára.  

5. A **beállítások > biztonsági mentési**, kattintson a **engedélyezve** elindítani a biztonsági mentési konfigurációját. Fogadja el az alapértelmezett értékeket, és kattintson a **OK** a a virtuális gép létrehozása az összefoglalás lapon lépjen a beállítások lapon. Ha szeretné megváltoztatni, hajtsa végre a következő lépéseket.  

6. Hozzon létre vagy válassza ki a Recovery Services-tároló, amely tartalmazza a virtuális gép biztonsági mentését. A recovery services-tároló létrehozásakor, dönthet úgy, hogy a tároló erőforráscsoport ki.  

    ![A virtuális gép biztonsági mentési konfigurációjának létrehozása oldal](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > Lehet, hogy a Recovery Services-tároló tartozó erőforráscsoport eltér a virtuális géphez tartozó erőforráscsoport.  
    > 
    > 

7. Alapértelmezés szerint a biztonsági mentési házirend van jelölve ahhoz, hogy a virtuális gép gyors védelme. A biztonsági mentési házirend határozza meg, hogy milyen gyakran biztonsági mentési pillanatképek, és azokat a biztonsági másolatok idő. Elfogadhatja az alapértelmezett házirendet, vagy hozzon létre, vagy válasszon egy másik biztonsági mentési házirend. A biztonsági mentési házirend szerkesztéséhez jelölje ki **a biztonsági mentési házirend** és a házirend értékeit.  

8. Ha elégedett a biztonsági mentési konfigurációhoz értékek beállítása lapon kattintson az **OK**.  

9. Az összefoglalás lapon, ha az érvényesítés, kattintson a **létrehozása** létrehozni egy virtuális gépet, amely a konfigurált biztonsági mentési beállításait használja. 

## <a name="initiate-a-backup-after-creating-the-vm"></a>A biztonsági mentés kezdeményezése után a virtuális gép létrehozása 

Bár a biztonsági mentési házirend létrehozása után célszerű kezdeti biztonsági másolat létrehozásához. A biztonsági mentési adatainak megtekintésére, az a virtuális gép egyszer a virtuális gép létrehozása sablon befejeződik, az a **műveletek** beállítása a bal oldali menüből, kattintson a **biztonsági mentési**. Ezzel az igény szerinti biztonsági mentés, állítsa vissza a teljes virtuális gépek vagy az összes lemez, fájlok helyreállítása a virtuális gép biztonsági másolatból, vagy módosítsa a virtuális géphez tartozó biztonsági mentési házirend.  

## <a name="frequently-asked-questions"></a>Gyakori kérdések 

### <a name="which-vm-images-enable-backup-at-the-time-of-vm-creation"></a>Mely Virtuálisgép-rendszerképek biztonsági mentés engedélyezése a virtuális gép létrehozásának időpontjában? 

Biztonsági mentés engedélyezése a virtuális gép létrehozásakor az alábbi lista a Microsoft által kiadott core lemezképek támogatottak. A többi virtuális gép engedélyezheti a biztonsági mentés, a virtuális gép létrehozása után. További [virtuális gép létrehozása után, a biztonságimásolat-készítés engedélyezése](quick-backup-vm-portal.md) 

- **Windows** – Windows Server 2016 adatközpontban, a Windows Server 2016 Data Center alapvető, Windows Server 2012 DataCenter, Windows Server 2012 R2 DataCenter, Windows Server 2008 R2 SP1 
- **Ubuntu** -Ubuntu Server 1710, Ubuntu Server 1704, UUbuntu Server 1604(LTS), Ubuntu Server 1404(LTS) 
- **Redhat** -RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4 
- **SUSE** -SUSE Linux Enterprise Server 11 SP4, 12 SP2, 12 SP3 
- **Debian** -Debian 8, Debian 9 
- **CentOS** -CentOS 6.9, CentOS 7.3. 
- **Oracle Linux** -Oracle Linux 6.7, 6.8, 6.9, 7.2, 7.3. 
 
### <a name="is-backup-cost-included-in-the-vm-cost"></a>A virtuális gép költség szerepel biztonsági mentési költség van? 

Nem, biztonsági mentési költségek: külön vagy a virtuális gépek költségek eltérő. Biztonsági mentés az árakkal kapcsolatos további információkért lásd: a [hely biztonsági mentési árképzési](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Mely engedélyek is szükségesek ahhoz, hogy a virtuális gép biztonsági mentése? 

Ha egy virtuális gép közreműködő, engedélyezheti a biztonsági másolat a virtuális Gépen. Ha egy egyéni biztonsági szerepkört használ, sikeresen engedélyezése a biztonsági másolat a virtuális Gépen a következő engedélyekre van szükség. 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Ha a recovery services-tároló és a virtuális gépet a különböző erőforráscsoportokra, mindenképp rendelkezik írási engedéllyel a helyreállítási szolgáltatások tároló erőforráscsoportban.  

## <a name="next-steps"></a>További lépések 

Most, hogy a virtuális gép védetté, tekintse meg a Virtuálisgép-felügyeleti feladatokat, és hogyan lehet visszaállítani a virtuális gépek a következő cikkeket. 

- [A virtuális gépek kezelése és figyelése](backup-azure-manage-vms.md) 
- [Virtuális gépek visszaállítása](backup-azure-arm-restore-vms.md) 
