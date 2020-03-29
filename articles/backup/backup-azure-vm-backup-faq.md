---
title: GYAKORI KÉRDÉSEK – Az Azure-beli virtuális gépek biztonsági mentése
description: Ebben a cikkben az Azure-beli virtuális gépek azure-biztonsági mentési szolgáltatással való biztonsági mentésével kapcsolatos gyakori kérdésekre adott válaszok at.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 5d2f702b49e1e7aeb2ab33008556e91264b39427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705411"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Gyakori kérdések – Az Azure virtuális gépeinek biztonsági és biztonsági

Ez a cikk választ ad az Azure-beli virtuális gépek azure-biztonsági mentési [szolgáltatással](backup-introduction-to-azure-backup.md) való biztonsági mentésével kapcsolatos gyakori kérdésekre.

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Mely virtuálisgép-lemezképek engedélyezhetők a biztonsági mentéshez, amikor létrehozom őket?

Virtuális gép létrehozásakor engedélyezheti a támogatott operációs [rendszereket](backup-support-matrix-iaas.md#supported-backup-actions) futtató virtuális gépek biztonsági mentését

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>A biztonsági mentési költség szerepel a virtuális gép költségében?

Nem. A biztonsági mentési költségek elkülönülnek a virtuális gép költségeitől. További információ az [Azure Backup díjszabásáról.](https://azure.microsoft.com/pricing/details/backup/)

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Milyen engedélyek szükségesek a virtuális gép biztonsági mentésének engedélyezéséhez?

Ha ön egy virtuális gép közreműködője, engedélyezheti a biztonsági mentést a virtuális gép. Ha egyéni szerepkört használ, a következő engedélyekre van szüksége a virtuális gép biztonsági mentésének engedélyezéséhez:

- Microsoft.RecoveryServices/Vaults/write
- Microsoft.RecoveryServices/Vaults/read
- Microsoft.RecoveryServices/locations/*
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read
- Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write
- Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write
- Microsoft.RecoveryServices/Vaults/backupPolicies/read
- Microsoft.RecoveryServices/Vaults/backupPolicies/write

Ha a Recovery Services-tároló és a virtuális gép különböző erőforráscsoportokkal rendelkezik, győződjön meg arról, hogy rendelkezik írási engedélyekkel a Recovery Services-tároló erőforráscsoportjában.  

### <a name="does-an-on-demand-backup-job-use-the-same-retention-schedule-as-scheduled-backups"></a>Az igény szerinti biztonsági mentési feladat ugyanazt az adatmegőrzési ütemezést használja, mint az ütemezett biztonsági mentések?

Nem. Adja meg az igény szerinti biztonsági mentési feladat megőrzési tartományát. Alapértelmezés szerint 30 napig megmarad, amikor a portálról aktiválódik.

### <a name="i-recently-enabled-azure-disk-encryption-on-some-vms-will-my-backups-continue-to-work"></a>Nemrég engedélyeztem az Azure Disk Encryption szolgáltatást néhány virtuális gépen. A biztonsági mentések továbbra is működni fognak?

Engedélyek biztosítása az Azure Backup számára a Key Vault eléréséhez. Adja meg az engedélyeket a PowerShellben az [Azure Backup PowerShell](backup-azure-vms-automation.md) dokumentációjának **Biztonsági mentés engedélyezése** szakaszában leírtak szerint.

### <a name="i-migrated-vm-disks-to-managed-disks-will-my-backups-continue-to-work"></a>Virtuálisgép-lemezeket telepítettem felügyelt lemezekre. A biztonsági mentések továbbra is működni fognak?

Igen, a biztonsági mentések zökkenőmentesen működnek. Nem kell újrakonfigurálni semmit.

### <a name="why-cant-i-see-my-vm-in-the-configure-backup-wizard"></a>Miért nem látom a virtuális gépemet a Biztonsági mentés konfigurálása varázslóban?

A varázsló csak a tárolóval azonos régióban lévő virtuális gépeket sorolja fel, és amelyekről még nem készül biztonsági mentés.

### <a name="my-vm-is-shut-down-will-an-on-demand-or-a-scheduled-backup-work"></a>A virtuális gépem leállt. Az igény szerinti vagy az ütemezett biztonsági mentés működni fog?

Igen. A biztonsági mentések a számítógép leállításakor futnak. A helyreállítási pont összeomlás-konzisztensként van megjelölve.

### <a name="can-i-cancel-an-in-progress-backup-job"></a>Megszakíthatok egy folyamatban lévő biztonsági mentési feladatot?

Igen. A biztonsági mentési feladat **felvételi pillanatkép** állapotban szakítható meg. Nem szakíthatja meg a feladatot, ha a pillanatképről történő adatátvitel folyamatban van.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Engedélyeztem az Azure Backup Service által létrehozott erőforráscsoport zárolását (azaz `AzureBackupRG_<geo>_<number>`), továbbra is működni fognak a biztonsági mentések?

Ha zárolja az Azure Backup Service által létrehozott erőforráscsoportot, a biztonsági mentések sikertelenek lesznek, mivel legfeljebb 18 visszaállítási pont van.

A felhasználónak el kell távolítania a zárolást, és törölnie kell a visszaállítási pont gyűjteményét az adott erőforráscsoportból, hogy a jövőbeli biztonsági mentések sikeresek legyenek, kövesse az [alábbi lépéseket](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) a visszaállítási pontgyűjtemény eltávolításához.

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Támogatja az Azure biztonsági mentése a szabványos SSD által kezelt lemezeket?

Igen, az Azure Backup támogatja a [szabványos SSD-felügyelt lemezeket.](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/)

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Biztonsági másolatot tudunk készíteni egy virtuális gépről egy ÍRÁSgyorsító (WA) képes lemezzel?

Pillanatképek nem készíthetők a WA-kompatibilis lemezen. Az Azure Backup szolgáltatás azonban kizárhatja a WA-kompatibilis lemezt a biztonsági mentésből.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Van egy virtuális gép write accelerator (WA) lemezekkel és SAP HANA telepítve. Hogyan lehet biztonsági másolatot csinálni?

Az Azure Backup nem tud biztonsági másolatot készíteni a WA-kompatibilis lemezről, de kizárhatja azt a biztonsági mentésből. A biztonsági másolat azonban nem biztosítja az adatbázis konzisztenciáját, mert a WA-kompatibilis lemezen lévő adatokról nem készül biztonsági másolat. Ezzel a konfigurációval biztonsági másolatot készíthet a lemezekről, ha az operációs rendszer lemezének biztonsági mentését, valamint a wa-kompatibilis lemezek biztonsági mentését szeretné.

Privát előzetes verziót futtatunk egy SAP HANA biztonsági mentéshez 15 perces RPO-val. Az SQL DB biztonsági mentéshez hasonló módon készült, és a backInt felületet használja az SAP HANA által hitelesített külső megoldásokhoz. Ha érdekli, írjon `AskAzureBackupTeam@microsoft.com` nekünk a témával Iratkozzon fel a privát előzetes **verzióaz SAP HANA biztonsági mentéséhez az Azure-beli virtuális gépeken.**

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Mi a maximális késleltetés, amire számíthatok a biztonsági mentés kezdési időpontjában a virtuális gép biztonsági mentési szabályzatában beállított ütemezett biztonsági mentési időtől?

Az ütemezett biztonsági mentés az ütemezett biztonsági mentési időtől számított 2 órán belül aktiválódik. Ha például 100 virtuális gép biztonsági mentési kezdési időpontja 2:00-ra van ütemezve, akkor legfeljebb 4:00-ig az összes 100 virtuális gép biztonsági mentési feladattal rendelkezik. Ha az ütemezett biztonsági mentések szünetelnek a kimaradás miatt, és újrapróbálkoztak,akkor a biztonsági mentés az ütemezett kétórás időszakon kívül is elkezdődhet.

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>Mi a napi biztonsági mentési pont minimálisan engedélyezett megőrzési tartománya?

Az Azure Virtual Machine biztonsági mentési szabályzata legalább hét nap, legfeljebb 9999 nap megőrzési tartományt támogat. A hét napnál rövidebb virtuális gép biztonsági mentési szabályzatának bármilyen módosítása szükséges egy frissítést, hogy megfeleljen a minimális megőrzési tartomány hét napig.

### <a name="can-i-backup-or-restore-selective-disks-attached-to-a-vm"></a>Készíthetek biztonsági másolatot vagy visszaállíthatók a virtuális géphez csatlakoztatott szelektív lemezekről?

Az Azure Backup mostantól támogatja a szelektív lemezbiztonsági mentést és visszaállítást az Azure virtuális gép biztonsági mentési megoldásával.

Az Azure Backup ma támogatja az összes lemez (operációs rendszer és adatok) biztonsági mentését a virtuális gépben a virtuális gép biztonsági mentési megoldásának használatával. A exclude-disk funkcióval lehetőséget kap arra, hogy egy vagy néhány biztonsági másolatot készítsen a virtuális gép számos adatlemezéről. Ez hatékony és költséghatékony megoldást nyújt a biztonsági mentési és visszaállítási igényekhez. Minden helyreállítási pont a biztonsági mentési műveletben szereplő lemezek adatait tartalmazza, ami lehetővé teszi, hogy a lemezek egy részhalmaza visszaálljon az adott helyreállítási pontról a visszaállítási művelet során. Ez a pillanatképből és a tárolóból történő visszaállításra vonatkozik.

Az előnézetre való feliratkozáshoz írjon nekünk aAskAzureBackupTeam@microsoft.com

## <a name="restore"></a>Visszaállítás

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Hogyan dönthetem el, hogy csak a lemezeket vagy a teljes virtuális gép visszaállítása?

Gondoljon egy virtuális gép-visszaállításra, mint egy Azure-beli virtuális gép gyors létrehozási lehetőségére. Ez a beállítás módosítja a lemezneveket, a lemezek által használt tárolókat, a nyilvános IP-címeket és a hálózati adapterek nevét. A változás a virtuális gép létrehozásakor megőrzi az egyedi erőforrásokat. A virtuális gép nem kerül hozzá egy rendelkezésre állási csoporthoz.

A visszaállítási lemez opciót a következők reklisítése esetén használhatja:

- A létrehozott virtuális gép testreszabása. Módosíthatja például a méretet.
- Adja meg azolyan konfigurációs beállításokat, amelyek nem voltak ott a biztonsági mentés idején.
- A létrehozott erőforrások elnevezési konvenciójának szabályozása.
- Adja hozzá a virtuális gép egy rendelkezésre állási csoport.
- Adjon hozzá más beállítást, amelyet a PowerShell vagy egy sablon használatával kell konfigurálni.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Visszatudom állítani a felügyelt lemezekre való frissítés után a nem felügyelt virtuálisgép-lemezek biztonsági másolatait?

Igen, használhatja a lemezek áttelepítése előtt készített biztonsági másolatokat a nem felügyeltről a felügyeltre.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Hogyan lehet visszaállítani egy virtuális gépet egy, a virtuális gép felügyelt lemezekre történő migrálását megelőző visszaállítási pontra?

A visszaállítási folyamat ugyanaz marad. Ha a helyreállítási pont olyan időpontban van, amikor a virtuális gép nem felügyelt lemezekkel [volt, a lemezek et nem felügyeltként állíthatja vissza.](tutorial-restore-disk.md#unmanaged-disks-restore) Ha a virtuális gép akkor felügyelt lemezeket, [visszaállíthatja a lemezeket felügyelt lemezek.](tutorial-restore-disk.md#managed-disk-restore) Ezután [létrehozhat egy virtuális gép ezekből a lemezekből](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[Erről a](backup-azure-vms-automation.md#restore-an-azure-vm) PowerShellben olvashat bővebben.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Visszaállíthatom a törölt virtuális gépét?

Igen. Még akkor is, ha törli a virtuális gép, akkor megy a megfelelő biztonsági mentési elemet a tárolóban, és állítsa vissza a helyreállítási pont.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Hogyan lehet visszaállítani egy virtuális gép azonos rendelkezésre állási csoportok?

Felügyelt lemez Azure VM esetén a rendelkezésre állási csoportok visszaállítását úgy engedélyezi, hogy a sablonban egy beállítást biztosít a felügyelt lemezekként való visszaállítás során. Ez a sablon az Elérhetőségi csoportok nevű bemeneti **paraméterrel rendelkezik.**

### <a name="how-do-we-get-faster-restore-performances"></a>Hogyan juthatunk gyorsabb visszaállítási teljesítmények?

[Az azonnali visszaállítás](backup-instant-restore-capability.md) iménymentési és azonnali visszaállítása a pillanatképekből.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Mi történik, ha módosítjuk a titkosított virtuális gép key vault beállításait?

Miután módosította a titkosított virtuális gép KeyVault-beállításait, a biztonsági mentések továbbra is működni fognak az új részletekkel. Azonban a visszaállítás után egy helyreállítási pont a módosítás előtt, vissza kell állítania a titkos kulcsokat a KeyVault,mielőtt létrehozhatja a virtuális gép belőle. További információkért olvassa el ezt a [cikket](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret)

Az olyan műveletek, mint a titkos/kulcsesés-átütemezés, nem igénylik ezt a lépést, és ugyanaz a KeyVault használható a visszaállítás után.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>Hozzáférhetek a virtuális gép, miután visszaállt, mert egy virtuális gép megszakadt a kapcsolat a tartományvezérlővel?

Igen, a virtuális gép, miután visszaállította a tartományvezérlővel megszakadt kapcsolat miatt. További információkért olvassa el ezt a [cikket](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps)

## <a name="manage-vm-backups"></a>Virtuális gép biztonsági mentéseinek kezelése

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Mi történik, ha módosítok egy biztonsági mentési házirendet?

A virtuális gép biztonsági másolatot a módosított vagy új szabályzat ütemezési és megőrzési beállításaival.

- Ha a megőrzést meghosszabbítják, a meglévő helyreállítási pontokat az új házirendnek megfelelően jelölik meg és tartják meg.
- Ha a megőrzés csökken, a helyreállítási pontok metszésre vannak megjelölve a következő tisztítási feladatban, majd törlődnek.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Hogyan helyezhetek át egy, az Azure Backup által biztonsági másolatáltal támogatott virtuális gépegy másik erőforráscsoportba?

1. Ideiglenesen állítsa le a biztonsági mentést, és őrizze meg a biztonsági mentési adatokat.
2. Helyezze át a virtuális gép a cél erőforráscsoportba.
3. Újra engedélyezett biztonsági mentés ugyanabban a vagy új tárolóban.

Visszaállíthatja a virtuális gép a rendelkezésre álló visszaállítási pontok, amelyek az áthelyezési művelet előtt létrehozott.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy"></a>Van-e korlátozva az azonos biztonsági mentési szabályzattal társítható virtuális gépek száma?

Igen, van egy 100 virtuális gép, amely társítható ugyanahhoz a biztonsági mentési szabályzata a portálról. Azt javasoljuk, hogy több mint 100 virtuális gép esetén hozzon létre több biztonsági mentési szabályzatot azonos ütemezéssel vagy eltérő ütemezéssel.
