---
title: Gyakori kérdések az Azure-beli virtuális gépek biztonsági mentéséről Azure Backup
description: Válaszok az Azure-beli virtuális gépek Azure Backup használatával történő biztonsági mentésével kapcsolatos gyakori kérdésekre.
author: sogup
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: sogup
ms.openlocfilehash: a26cc2fcdc381361912085b0980a736f040c5b0a
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465292"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Gyakori kérdések – Azure-beli virtuális gépek biztonsági mentése

Ez a cikk az Azure-beli virtuális gépek [Azure Backup](backup-introduction-to-azure-backup.md) szolgáltatással történő biztonsági mentésével kapcsolatos gyakori kérdésekre ad választ.


## <a name="backup"></a>Tartalék

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Milyen virtuálisgép-rendszerképeket lehet engedélyezni a biztonsági mentéshez, amikor létrehozom őket?
Amikor létrehoz egy virtuális gépet, engedélyezheti a biztonsági mentést a [támogatott operációs rendszereket](backup-support-matrix-iaas.md#supported-backup-actions) futtató virtuális gépeken

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>A virtuális gép ára tartalmazza a biztonsági mentés költségeit?

Nem. A biztonsági mentési költségek elkülönül a virtuális gép költségeitől. További információ a [Azure Backup díjszabásáról](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Milyen engedélyek szükségesek a virtuális gép biztonsági mentésének engedélyezéséhez?

Ha Ön virtuálisgép-közreműködő, akkor engedélyezheti a biztonsági mentést a virtuális gépen. Ha egyéni szerepkört használ, a következő engedélyekkel engedélyezheti a biztonsági mentést a virtuális gépen:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Ha a Recovery Services-tároló és a virtuális gép eltérő erőforráscsoportokat tartalmaz, győződjön meg arról, hogy rendelkezik írási engedéllyel a Recovery Services-tároló erőforráscsoporthoz.  


### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Az igény szerinti biztonsági mentési feladatok ugyanazt a megőrzési ütemezést használják, mint az ütemezett biztonsági mentések?
Nem. Egy igény szerinti biztonsági mentési feladatokhoz tartozó megőrzési tartomány megadása. Alapértelmezés szerint 30 napig őrzi meg a rendszer a portálról való aktiváláskor.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Nemrég engedélyeztem az Azure Disk Encryption szolgáltatást néhány virtuális gépen. A biztonsági mentések továbbra is működni fognak?
Adja meg az Azure Backup számára a Key Vault eléréséhez szükséges engedélyeket. Az [Azure Backup PowerShell](backup-azure-vms-automation.md) dokumentációjában a **biztonsági mentés engedélyezése** című részben leírtak szerint határozza meg az engedélyeket a PowerShellben.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Áttelepítettem a VM-lemezeket a felügyelt lemezekre. A biztonsági mentések továbbra is működni fognak?
Igen, a biztonsági mentések zökkenőmentesen működnek. Semmit nem kell újrakonfigurálnia.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Miért nem látom a virtuális gépemet a Biztonsági mentés konfigurálása varázslóban?
A varázsló csak azokat a virtuális gépeket sorolja fel, amelyek ugyanabban a régióban találhatók, mint a tároló, és amelyekről még nem készül biztonsági mentés.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>A virtuális gép le van állítva. Egy igény szerinti vagy ütemezett biztonsági mentés is működik?
Igen. A biztonsági mentések a gép leállításakor futnak. A helyreállítási pont az összeomlás-konzisztensként van megjelölve.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Törölhetek egy folyamatban lévő biztonsági mentési feladatot?
Igen. A biztonsági mentési feladatot megszakíthatja  a pillanatképek állapotának megkezdéséhez. Nem lehet megszakítani egy feladatot, ha a pillanatképből való adatátvitel folyamatban van.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprggeonumber-will-my-backups-continue-to-work"></a>Engedélyezve van a Azure Backup szolgáltatás által létrehozott erőforráscsoport zárolása (azaz `AzureBackupRG_<geo>_<number>`a biztonsági mentések továbbra is működni fognak?
Ha zárolja Azure Backup szolgáltatás által létrehozott erőforráscsoportot, a biztonsági mentések sikertelenek lesznek, mivel legfeljebb 18 visszaállítási pont lehet.

A felhasználónak el kell távolítania a zárolást, és törölnie kell a visszaállítási pont gyűjteményét abból a célból, hogy a biztonsági mentések sikeresek legyenek, a visszaállítási pontok gyűjteményének eltávolításához [kövesse az alábbi lépéseket](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) .


### <a name="does-azure-backup-support-standard-ssd-managed-disk"></a>Támogatja az Azure Backup a standard SSD felügyelt lemezt?
A Azure Backup támogatja a [standard SSD felügyelt lemezeket](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/). Az SSD által felügyelt lemezek új típusú tartós tárolást biztosítanak az Azure-beli virtuális gépek számára. Az SSD által felügyelt lemezek támogatása az [azonnali visszaállításban](backup-instant-restore-capability.md)érhető el.

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Tud biztonsági másolatot készíteni egy virtuális gépről írásgyorsító (WA)-kompatibilis lemezzel?
Nem lehet pillanatképeket felvenni a WA-kompatibilis lemezre. A Azure Backup szolgáltatás azonban kizárhatja a WA-kompatibilis lemezt a biztonsági mentésből.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Van egy írásgyorsító (WA) lemezzel rendelkező virtuális gép, és SAP HANA telepítve. Hogyan biztonsági mentést?
Azure Backup nem tud biztonsági másolatot készíteni a WA-kompatibilis lemezről, de kizárhatja azt a biztonsági mentésből. A biztonsági mentés azonban nem biztosít adatbázis-konzisztenciát, mert a WA-kompatibilis lemezen található információk nem rendelkeznek biztonsági mentéssel. Ebben a konfigurációban biztonsági mentést készíthet a lemezekről, ha az operációs rendszer lemezének biztonsági mentését kívánja végezni, valamint a nem a WA-kompatibilis lemezek biztonsági mentését.

Privát előzetes verzióban futtatunk egy SAP HANA biztonsági mentést 15 perces RPO. Ez az SQL-adatbázis biztonsági mentésének hasonló módja, és a backInt felületet használja az SAP HANA által tanúsított külső megoldások számára. Ha érdekli, küldjön e-mailt `AskAzureBackupTeam@microsoft.com` a tulajdonosnak a **privát előzetes verzióra az Azure-beli virtuális gépek SAP HANA biztonsági mentéséhez**.

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Mi a maximális késleltetés a virtuális gép biztonsági mentési házirendjében beállított ütemezett biztonsági mentési idő alapján a biztonsági mentés kezdési idején?
Az ütemezett biztonsági mentés az ütemezett biztonsági mentés időpontjától számított 2 órán belül aktiválódik. Pl.: Ha az 100 virtuális gép biztonsági mentésének kezdési időpontja 2:00 órakor van ütemezve, akkor a 100VMs Max 4:00 am értékkel lesz folyamatban. Ha az ütemezett biztonsági mentések leállása miatt szüneteltek, és a rendszer újrapróbálkozik, akkor a biztonsági mentés az ütemezett 2hr-ablakon kívül is elindítható.

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>Mi a minimálisan megengedett megőrzési időtartam a napi biztonsági mentési ponthoz?
Az Azure-beli virtuális gépek biztonsági mentési szabályzata legalább 7 napos megőrzési időtartamot támogat legfeljebb 9999 napig. A meglévő virtuális gépek biztonsági mentési házirendjének a 7 napnál rövidebb idő alatt történő módosítása egy frissítést igényel, hogy megfeleljen a minimális megőrzési időtartamnak (7 nap).

## <a name="restore"></a>Visszaállítás

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Hogyan eldönteni, hogy csak lemezeket vagy teljes virtuális gépet szeretne-e visszaállítani?
Egy Azure-beli virtuális gép gyors létrehozási lehetőségeként tekintse át a virtuális gép visszaállítását. Ez a beállítás megváltoztatja a lemezeket, a nyilvános IP-címeket és a hálózati adapterek nevét. A módosítás egyedi erőforrásokat tart fenn a virtuális gépek létrehozásakor. A virtuális gép nincs felvéve egy rendelkezésre állási csoportba.

Használhatja a lemez visszaállítása lehetőséget, ha a következőket kívánja használni:
  * Szabja testre a létrehozott virtuális gépet. Módosítsa például a méretet.
  * Adja meg a biztonsági mentés időpontjában nem található konfigurációs beállításokat.
  * A létrehozott erőforrások elnevezési konvenciójának szabályozása.
  * Adja hozzá a virtuális gépet egy rendelkezésre állási csoporthoz.
  * Adja meg az egyéb beállításokat, amelyeket a PowerShell vagy egy sablon használatával kell konfigurálni.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Visszaállíthatom a nem felügyelt virtuális gépek biztonsági másolatait a felügyelt lemezekre való frissítés után?
Igen, használhat olyan biztonsági másolatokat, amelyeket a lemezek a nem felügyelt állapotból felügyelt rendszerbe való migrálása előtt készítettek.
- Alapértelmezés szerint a Restore VM-feladatok nem felügyelt virtuális gépet hoznak létre.
- Azonban visszaállíthatja a lemezeket, és felhasználhatja őket egy felügyelt virtuális gép létrehozásához.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Hogyan lehet visszaállítani egy virtuális gépet egy, a virtuális gép felügyelt lemezekre történő migrálását megelőző visszaállítási pontra?
Alapértelmezés szerint a visszaállítási virtuálisgép-feladatok létrehoznak egy nem felügyelt lemezekkel rendelkező virtuális gépet. Virtuális gép létrehozása felügyelt lemezekkel:
1. [Visszaállítás a](tutorial-restore-disk.md#restore-a-vm-disk)nem felügyelt lemezekre.
2. [Konvertálja a visszaállított lemezeket](tutorial-restore-disk.md#convert-the-restored-disk-to-a-managed-disk)a felügyelt lemezekre.
3. [Hozzon létre egy virtuális gépet a Managed Disks szolgáltatással](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[További](backup-azure-vms-automation.md#restore-an-azure-vm) információ a PowerShellben való használatáról.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Visszaállíthatom a törölt virtuális gépet?
Igen. Még ha törli a virtuális gépet is, a tárolóban található megfelelő biztonsági mentési elemre kattintva visszaállíthatja a helyreállítási pontot.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Virtuális gép visszaállítása ugyanazon rendelkezésre állási csoportokra
A felügyelt lemezes Azure-beli virtuális gépek esetében a rendelkezésre állási csoportokra történő visszaállítás a felügyelt lemezként való visszaállításkor a sablonban beállítás engedélyezésével engedélyezhető. Ez a sablon tartalmazza a **rendelkezésre állási készletek**nevű bemeneti paramétert.

### <a name="how-do-we-get-faster-restore-performances"></a>Hogyan lehet gyorsabb visszaállítási teljesítményt kapni?
A gyorsabb visszaállítási teljesítmény érdekében a rendszer [azonnali visszaállítási](backup-instant-restore-capability.md) képességre vált.

## <a name="manage-vm-backups"></a>Virtuális gép biztonsági mentéseinek kezelése

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Mi történik, ha módosítom a biztonsági mentési szabályzatot?
A virtuális gép biztonsági mentése a módosított vagy az új szabályzatban szereplő ütemterv és adatmegőrzési beállítások használatával történik.

- Ha a megőrzés kibővül, a meglévő helyreállítási pontok az új szabályzatnak megfelelően vannak megjelölve és tárolva.
- Ha a megőrzés csökken, a helyreállítási pontok a következő karbantartási feladatokban törlésre vannak megjelölve, és ezt követően törlődnek.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Hogyan egy olyan virtuális gépet, amelyről biztonsági másolatot készít Azure Backup egy másik erőforráscsoporthoz?

1. Átmenetileg állítsa le a biztonsági mentést, és őrizze meg a biztonsági mentési adatait.
2. Helyezze át a virtuális gépet a célként megadott erőforrás-csoportba.
3. A biztonsági mentés ismételt engedélyezése azonos vagy új tárolóban.

Visszaállíthatja a virtuális gépet az áthelyezési művelet előtt létrehozott, elérhető visszaállítási pontokból.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy"></a>Van korlátozva a virtuális gépek száma, amelyek ugyanahhoz a biztonsági mentési szabályzathoz társíthatók?
Igen, legfeljebb 100 virtuális gép lehet, amely ugyanahhoz a biztonsági mentési szabályzathoz társítható a portálról. Javasoljuk, hogy több mint 100 virtuális gépen hozzon létre több biztonsági mentési szabályzatot ugyanazzal az ütemtervtel vagy eltérő ütemtervtel.
