---
title: Azure-beli virtuális gépek az Azure Backup szolgáltatással biztonsági mentésével kapcsolatos gyakori kérdések
description: Azure-beli virtuális gépek az Azure Backup szolgáltatással biztonsági mentésével kapcsolatos gyakori kérdésekre adott válaszok.
services: backup
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: sogup
ms.openlocfilehash: 9f233af316bd6022b93a7208bf3fae37e913e6af
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58885264"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Gyakori kérdések – biztonsági mentése Azure virtuális gépek

Ez a cikk az Azure virtuális gépek biztonsági mentésével kapcsolatos gyakori kérdésekre is válaszol a [Azure Backup](backup-introduction-to-azure-backup.md) szolgáltatás.


## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Melyik Virtuálisgép-rendszerképek biztonsági mentés esetén is engedélyezhető, ha hozhatok létre?
Egy virtuális gép létrehozásakor engedélyezheti a futó virtuális gépek biztonsági mentési [támogatott operációs rendszerek](backup-support-matrix-iaas.md#supported-backup-actions)
 
### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>A virtuális gép díja magában foglalja a biztonsági mentési mennyibe kerül? 

Nem. Biztonsági mentési díjakat külön, a virtuális gép költségeket. Tudjon meg többet [Azure Backup árairól](https://azure.microsoft.com/pricing/details/backup/).
 
### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Milyen engedélyek szükségesek egy virtuális gép biztonsági mentésének engedélyezése? 

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


### <a name="what-azure-vms-can-you-back-up-using-azure-backup"></a>Milyen Azure-beli virtuális gépek is biztonsági másolatot készíteni az Azure Backup használatával?

Tekintse át a [támogatási mátrix](backup-support-matrix-iaas.md) részletei és korlátozásokról.

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Egy igény szerinti biztonsági mentési feladat használja ugyanazt a megőrzési ütemezést, ütemezett biztonsági mentések?
Nem. Adja meg a megőrzési tartomány egy igény szerinti biztonsági mentési feladat. Alapértelmezés szerint 30 napig őrzi meg a portálról elindításakor.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Nemrég engedélyeztem az Azure Disk Encryption szolgáltatást néhány virtuális gépen. A biztonsági mentések továbbra is működni fognak?
Key Vault elérése érdekében az Azure Backup engedélyeket biztosítanak. Adja meg az engedélyeket a PowerShellben leírtak szerint a **biztonsági mentés engedélyezése** című rész a [Azure Backup PowerShell](backup-azure-vms-automation.md) dokumentációját.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Felügyelt lemezeket Virtuálisgép-lemezek szeretnék áttelepíteni. A biztonsági mentések továbbra is működni fognak?
Igen, biztonsági mentések problémamentesen működik. Hiba esetén nem kell semmit újrakonfigurálása.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Miért nem látom, hogy a biztonsági mentés konfigurálása varázslóban a virtuális gépem?
A varázsló csak sorolja fel a virtuális gépek és a tárolónak ugyanabban a régióban, és már, hogy nem készül biztonsági másolat.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>Virtuális gép leállt. Egy igény szerinti vagy ütemezett biztonsági mentési munkahelyi lesz?
Igen. Biztonsági másolatok futtassa, ha egy gép le van állítva. A helyreállítási pont van megjelölve rendszerösszeomlás egységes.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Egy folyamatban lévő biztonsági mentési feladat lemondható?
Igen. A biztonsági mentési feladat megszakítható egy **véve pillanatkép** állapota. Egy feladat nem szakítható meg, ha folyamatban van a pillanatkép adatátvitel.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprggeonumber-will-my-backups-continue-to-work"></a>I engedélyezve van az Azure Backup szolgáltatással (vagyis) által létrehozott erőforráscsoport zárolása `AzureBackupRG_<geo>_<number>`), a biztonsági mentések továbbra is működni fog?
Ha zárolhatja az Azure Backup szolgáltatás által létrehozott erőforráscsoportot, biztonsági mentés sikertelen, mert legfeljebb 18 visszaállítási pontok indul el.

Távolítsa el a zárolást, és törölje a jelet ebből az erőforráscsoportból a visszaállításipont-gyűjtemény annak érdekében, hogy a jövőbeni biztonsági mentések sikeres, a felhasználónak kell [kövesse az alábbi lépéseket](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) eltávolítása a visszaállításipont-gyűjtemény.

### <a name="does-the-backup-policy-consider-daylight-saving-time-dst"></a>Vegye figyelembe a nyári időszámításra (cél) a biztonsági mentési szabályzatot?
Nem. A dátum és idő a helyi számítógépen a alkalmazni aktuális nyári időszámítás helyi. Ütemezett biztonsági mentések beállított ideje a helyi idő nyári Időszámítás miatt eltérhetnek.

### <a name="how-many-data-disks-can-i-attach-to-a-vm-backed-up-by-azure-backup"></a>Hány adatlemez csatolható a virtuális gép biztonsági mentése az Azure Backup?
Az Azure Backup legfeljebb 16 lemez a biztonsági mentést virtuális gépeket. 16 lemez támogatása megtalálható a [azonnali visszaállítása](backup-instant-restore-capability.md).

### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Biztosítja az Azure támogatja a biztonsági mentést standard SSD felügyelt lemez?
Az Azure Backup támogatja [SSD standard szintű felügyelt lemezek](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/). SSD-managed Disks szolgáltatásba az Azure virtuális gépek adjon meg egy új típusú tartós tárolási. SSD felügyelt lemezek támogatása megtalálható a [azonnali visszaállítása](backup-instant-restore-capability.md).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Hogy készíthető lemez írási gyorsító WA-kompatibilis virtuális gépek?
Pillanatképek nem használhatók a WA-kompatibilis lemezen. Azonban az Azure Backup szolgáltatás is WA-kompatibilis lemezének kizárása a biztonsági másolatból. Lemez kizárása a virtuális gépek WA-kompatibilis lemezek csak azonnali visszaállítása frissített előfizetések esetén támogatott.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Írási gyorsító (CS) lemezekkel rendelkező virtuális gép van, és az SAP HANA telepítve. Hogyan készíthetek biztonsági másolatot?
Az Azure Backup a WA-kompatibilis lemez nem készíthető, de lehet kizárni, biztonsági másolatból. Azonban a biztonsági mentés módszer nem biztosítja adatbázis-konzisztencia, mert a WA-kompatibilis lemezen lévő adatokat nem készül. Ez a konfiguráció lemezek készíthető, ha azt szeretné, operációsrendszer-lemez biztonsági mentés, és a lemezek, amelyek nem WA-kompatibilis.

Privát előzetes verzió, az SAP HANA biztonsági mentés egy 15 perces RPO-val futtatja azt. Az SQL-adatbázis biztonsági mentése hasonló módon épül, és az SAP HANA által hitelesített külső megoldások a backInt felületet használja. Ha érdekli, e-mailt küldjön `AskAzureBackupTeam@microsoft.com` témájával **regisztrálhat az Azure virtuális gépeken futó SAP HANA biztonsági mentéséhez a privát előzetes verzió**.


## <a name="restore"></a>Visszaállítás

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Hogyan válasszak visszaállítása csak lemezek-e, vagy egy teljes virtuális Gépet?
Úgy gondolja, hogy a virtuális gép visszaállítási mint gyors létrehozási lehetőséget egy Azure virtuális gép. Ez a beállítás módosítja a lemez nevét, a lemezek, a nyilvános IP-címek és a hálózati adapterek nevét által használt tárolókat. A módosítás megtartja az egyedi erőforrásokat egy virtuális gép létrehozásakor. A virtuális gép egy rendelkezésre állási csoport nem hozzá.

A visszaállítási lemez lehetőséget is használhatja, ha azt szeretné, hogy:
  * Testre szabhatja a virtuális gép, amely létrejön. Például módosíthatja a méretét.
  * Adja hozzá a konfigurációs beállítások, amelyek nem létezik a biztonsági mentés idején.
  * Vezérlő elnevezési létrehozott erőforrásokat.
  * A virtuális gép hozzáadása egy rendelkezésre állási csoporthoz.
  * Adja hozzá a bármely más beállítás, amely a PowerShell vagy egy sablon használatával kell konfigurálni.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Vissza tudok állítani biztonsági nem felügyelt virtuális gépek lemezeinek biztonsági másolatait magasabb a managed Disks szolgáltatásba?
Igen, használhatja a lemezek migrált felügyelt kódba előtt készített biztonsági másolatokat.
- Alapértelmezés szerint a virtuális gép visszaállítási feladat létrehoz egy nem felügyelt virtuális gép.
- Azonban állítsa vissza a lemezeket, és ezek segítségével felügyelt virtuális gép létrehozása.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Hogyan állíthatok vissza virtuális gép visszaállítási pontra a virtuális gép felügyelt lemezekre történő áttelepítése előtt?
Alapértelmezés szerint egy virtuális gép visszaállítási feladat nem felügyelt lemezekkel rendelkező virtuális Gépet hoz létre. A felügyelt lemezekkel rendelkező virtuális gép létrehozásához:
1. [Nem felügyelt lemezek visszaállítása](tutorial-restore-disk.md#restore-a-vm-disk).
2. [A visszaállított lemez átalakítása felügyelt lemezek](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk).
3. [A felügyelt lemezekkel rendelkező virtuális gép létrehozása](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[További](backup-azure-vms-automation.md#restore-an-azure-vm) nyújt a PowerShellben.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Vissza tudok állítani a virtuális gép, amely törölve lett?
Igen. Akkor is, ha törli a virtuális gép, megfelelő biztonsági mentési léphet a tárolóban lévő elemet, és visszaállítása egy helyreállítási pontból.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Virtuális gépek visszaállítása az egyazon rendelkezésre állási csoportok?
Felügyelt lemez Azure virtuális gép az a rendelkezésre állási csoportok visszaállítása engedélyezve van azáltal, hogy egy beállítást a sablonban, felügyelt lemezek visszaállítása során. Ez a sablon rendelkezik a bemeneti paraméter nevű **rendelkezésre állási csoportok**.

### <a name="how-do-we-get-faster-restore-performances"></a>Hogyan be gyorsabb helyreállítási teljesítmény?
A gyorsabb visszaállítás teljesítmény, hogy költöznek [azonnali visszaállítása](backup-instant-restore-capability.md) képesség.

## <a name="manage-vm-backups"></a>Virtuális gép biztonsági mentéseinek kezelése

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Mi történik, ha egy biztonsági mentési szabályzat módosítani?
A virtuális gép biztonsági másolat a módosított vagy új szabályzat ütemezése és megőrzése beállításainak használatával.

- Ha a megőrzési időtartamot, a meglévő helyreállítási pontok megjelölve, és tartani az új szabályzatának megfelelően.
- Ha csökkenti a megőrzési időtartamot, a helyreállítási pontokat a helyreállítási pontok a következő tisztítási feladat során törlendőként megjelölve, és törlődnek.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Hogyan helyezhetek át egy virtuális gép biztonsági mentése az Azure Backup egy másik erőforráscsoportban található?

1. Ideiglenesen állítsa le a biztonsági mentés, és a biztonsági másolatok adatainak megőrzése.
2. A virtuális gép áthelyezése a céloldali erőforráscsoport.
3. Az ugyanazon vagy egy új tárolóban újraengedélyezése biztonsági mentés.

A virtuális gép visszaállíthatja az áthelyezési művelet előtt létrehozott rendelkezésre álló helyreállítási pontokból.
