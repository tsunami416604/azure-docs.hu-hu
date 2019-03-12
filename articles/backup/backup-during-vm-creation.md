---
title: Azure virtuális gépek biztonsági mentésének engedélyezése a létrehozása során
description: Tudnivalók az Azure virtuális gépek biztonsági mentésének engedélyezése a létrehozási folyamat során.
services: backup, virtual-machines
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2018
ms.author: trinadhk
ms.openlocfilehash: fd2beaa39f03d4f2342c94bf1cd8b8aea7440e62
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780443"
---
# <a name="enable-backup-when-you-create-an-azure-virtual-machine"></a>Amikor létrehoz egy Azure virtuális gép biztonsági mentésének engedélyezése

Az Azure Backup szolgáltatás használatával az Azure virtuális gépek (VM) biztonsági mentése. Virtuális gépek biztonsági mentése egy biztonsági mentési szabályzatban megadott ütemezés szerint, és helyreállítási pontokat hoz létre, a biztonsági mentésekből. Helyreállítási pontok a Recovery Services-tárolókra vannak tárolva.

Ez a cikk részletesen a biztonsági mentés engedélyezése, amíg egy virtuális gépet (VM) hoz létre az Azure Portalon.  

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha még nem jelentkezett be a fiókjába, jelentkezzen be a [az Azure portal](https://portal.azure.com).
 
## <a name="create-a-vm-with-backup-configured"></a>Biztonsági mentés konfigurálva a virtuális gép létrehozása 

1. Az Azure portal bal felső sarkában válassza **új**.

1. Válassza ki **számítási**, majd válassza ki a virtuális gép egy lemezképet.

1. Írja be a virtuális gép adatait. A felhasználónév és jelszó megadásával használandó jelentkezzen be a virtuális Gépet. Ha elkészült, válassza ki a **OK**. 

1. Válasszon méretet a virtuális gép számára.  

1. A **beállítások** > **biztonsági mentési**válassza **engedélyezve** a biztonsági mentési konfiguráció beállításainak megnyitásához.

   - Az alapértelmezett értékek elfogadásához válassza **OK** a a **beállítások** lapot. Majd kattintva megnyithatja a **összegzése** lapon a virtuális gép létrehozásához. Hagyja ki a 6 – 8.
   - Biztonsági mentés konfigurációjának módosításához kövesse az alábbi lépésekkel.  

1. Létrehozhat vagy kiválaszthat egy Recovery Services-tárolót a virtuális gép a biztonsági másolatok tárolásához. Recovery Services-tároló létrehozásával, kiválaszthatja a tároló egy erőforráscsoportot.  

    ![Biztonsági mentés konfigurációs beállításokat a virtuális gép létrehozása lap](./media/backup-during-vm-creation/create-vm-backup-config.png) 

    > [!NOTE] 
    > A Recovery Services-tároló az erőforráscsoportot, mint az erőforráscsoport, a virtuális gép eltérő lehet.  

1. Alapértelmezés szerint egy biztonsági mentési szabályzat van jelölve, hogy a virtuális gép védelmét. Biztonsági mentési szabályzat határozza meg, hogy milyen gyakran kell biztonsági mentési pillanatképek és mennyi ideig ezeket a biztonsági másolatok megőrzése. 

   - Elfogadhatja az alapértelmezett házirendet, vagy hozzon létre, vagy egy másik biztonsági mentési házirend kiválasztása. 
   - A biztonsági mentési szabályzat szerkesztéséhez jelölje ki **biztonsági mentési szabályzat** és módosítsa az értékeket.  

1. Ha elkészült, a biztonsági mentési konfiguráció beállításértékek, válassza ki a **OK** a a **beállítások** lapot.  

1. Az a **összegzése** lap érvényesítése után, válassza ki **létrehozás** hozhat létre virtuális Gépet, amely a konfigurált biztonsági mentési beállításait használja. 

## <a name="start-a-backup-after-creating-the-vm"></a>Biztonsági mentést elindítani a virtuális gép létrehozása után 

Annak ellenére, hogy konfigurálta a biztonsági mentési szabályzatot a virtuális gép, tanácsos hozzon létre egy kezdeti biztonsági mentés. 

Lépjen a virtuális gép létrehozása sablon befejezését követően **műveletek** a bal oldali menüben, és válasszon **biztonsági mentési** a virtuális gép biztonsági mentési részleteinek megtekintéséhez. Ezen az oldalon használhatja:

- Indítson egy igény szerinti biztonsági mentést.
- Állítsa vissza egy teljes virtuális Gépet, vagy az összes lemez.
- Fájlok visszaállítása egy virtuális gép biztonsági mentése.
- Módosítsa a virtuális Géphez társított biztonsági mentési házirendet.  

## <a name="use-a-resource-manager-template-to-deploy-a-protected-vm"></a>A védett virtuális gépek üzembe helyezése Resource Manager-sablon használatával

Az előző lépések bemutatják, hogyan hozzon létre egy virtuális gépet, és védelmét, a Recovery Services-tárolót az Azure portal használatával. Gyors üzembe helyezése az egy vagy több virtuális gépet, és azok védelme Recovery Services-tárolóban, tekintse meg a sablon [Windows virtuális gép üzembe helyezése és a biztonsági mentés engedélyezése](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/).

## <a name="frequently-asked-questions"></a>Gyakori kérdések 

### <a name="which-vm-images-support-backup-configuration-during-vm-creation"></a>Melyik Virtuálisgép-rendszerképek támogatja a virtuális gépek létrehozása során a biztonsági mentési konfigurációt?

Az alábbi fő képeket a Microsoft által közzétett támogatja a biztonsági mentés engedélyezése a virtuális gépek létrehozása során. A többi virtuális gép engedélyezheti a biztonsági mentés, a virtuális gép létrehozása után. További tudnivalókért lásd: [virtuális gép létrehozása után, a biztonságimásolat-készítés engedélyezése](quick-backup-vm-portal.md).

- **Windows** – Windows Server 2016 Datacenter, Windows Server 2016 Datacenter mag, Windows Server 2012 Datacenter, Windows Server 2012 R2 Datacenter, Windows Server 2008 R2 SP1 
- **Ubuntu** – Ubuntu Server 17.10, az Ubuntu Server 17.04, az Ubuntu Server 16.04 (LTS), az Ubuntu Server 14.04 (LTS) 
- **Red Hat** -RHEL 6.7, 6.8, 6.9, 7.2, 7.3, 7.4 
- **SUSE** – SUSE Linux Enterprise Server 11 SP4, 12 SP2, 12 SP3 
- **Debian** – Debian 8, a Debian 9 
- **CentOS** -CentOS 6.9, a CentOS 7.3. 
- **Oracle Linux** – Oracle Linux 6.7, 6.8, 6.9, 7.2, 7.3. 
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>A virtuális gép díja magában foglalja a biztonsági mentési mennyibe kerül? 

Nem. Biztonsági mentési díjakat külön, a virtuális gép költségeket. További információ a backup díjszabása: [Azure Backup díjszabása](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-on-a-vm"></a>Milyen engedélyek szükségesek a virtuális gép biztonsági mentésének engedélyezése? 

Ha Ön egy virtuális gép közreműködő, engedélyezheti a virtuális gép biztonsági mentése. Ha egy egyéni szerepkör használata esetén szüksége a virtuális gép biztonsági mentésének engedélyezése a következő engedélyekkel: 

- Microsoft.RecoveryServices/Vaults/write 
- Microsoft.RecoveryServices/Vaults/read 
- Microsoft.RecoveryServices/locations/* 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read 
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write 
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write 
- Microsoft.RecoveryServices/Vaults/backupPolicies/read 
- Microsoft.RecoveryServices/Vaults/backupPolicies/write 
 
Ha a Recovery Services-tároló és a virtuális gép eltérő erőforráscsoportokban, győződjön meg arról, van is írási engedélye a Recovery Services-tároló az erőforráscsoportban.  

## <a name="next-steps"></a>További lépések 

Most, hogy a virtuális gép már védett, lásd: megtudhatja, hogyan kezelhetik és visszaállíthatják a virtuális gépek a következő cikkeket:

- [A virtuális gépek kezelése és figyelése](backup-azure-manage-vms.md) 
- [Virtuális gépek visszaállítása](backup-azure-arm-restore-vms.md) 
