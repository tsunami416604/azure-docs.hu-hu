---
title: GYIK – Azure-beli virtuális gépek biztonsági mentése
description: Ebből a cikkből megismerheti az Azure-beli virtuális gépek Azure Backup szolgáltatással történő biztonsági mentésével kapcsolatos gyakori kérdésekre adott válaszokat.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 5d2f702b49e1e7aeb2ab33008556e91264b39427
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705411"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Gyakori kérdések – Azure-beli virtuális gépek biztonsági mentése

Ez a cikk az Azure-beli virtuális gépek [Azure Backup](backup-introduction-to-azure-backup.md) szolgáltatással történő biztonsági mentésével kapcsolatos gyakori kérdésekre ad választ.

## <a name="backup"></a>Biztonsági mentés

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Milyen virtuálisgép-rendszerképeket lehet engedélyezni a biztonsági mentéshez, amikor létrehozom őket?

Amikor létrehoz egy virtuális gépet, engedélyezheti a biztonsági mentést a [támogatott operációs rendszereket](backup-support-matrix-iaas.md#supported-backup-actions) futtató virtuális gépeken

### <a name="is-the-backup-cost-included-in-the-vm-cost"></a>A virtuális gép ára tartalmazza a biztonsági mentés költségeit?

Nem. A biztonsági mentési költségek elkülönül a virtuális gép költségeitől. További információ a [Azure Backup díjszabásáról](https://azure.microsoft.com/pricing/details/backup/).

### <a name="which-permissions-are-required-to-enable-backup-for-a-vm"></a>Milyen engedélyek szükségesek a virtuális gép biztonsági mentésének engedélyezéséhez?

Ha Ön virtuálisgép-közreműködő, akkor engedélyezheti a biztonsági mentést a virtuális gépen. Ha egyéni szerepkört használ, a következő engedélyekkel engedélyezheti a biztonsági mentést a virtuális gépen:

- Microsoft. Recoveryservices szolgáltatónál/tárolók/írás
- Microsoft. Recoveryservices szolgáltatónál/tárolók/olvasás
- Microsoft. Recoveryservices szolgáltatónál/Locations/*
- Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/protectionContainers/protectedItems/*/READ
- Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/READ
- Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/protectionContainers/protectedItems/Write
- Microsoft. Recoveryservices szolgáltatónál/Vault/backupFabrics/backupProtectionIntent/Write
- Microsoft. Recoveryservices szolgáltatónál/Vault/backupPolicies/READ
- Microsoft. Recoveryservices szolgáltatónál/tárolók/backupPolicies/írás

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

Igen. A biztonsági mentési feladatot megszakíthatja a **Pillanatképek állapotának megkezdéséhez** . Nem lehet megszakítani egy feladatot, ha a pillanatképből való adatátvitel folyamatban van.

### <a name="i-enabled-lock-on-resource-group-created-by-azure-backup-service-ie-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Engedélyezve van a Azure Backup szolgáltatás által létrehozott erőforráscsoport zárolása (azaz `AzureBackupRG_<geo>_<number>`), a biztonsági mentések továbbra is működni fognak?

Ha zárolja Azure Backup szolgáltatás által létrehozott erőforráscsoportot, a biztonsági mentések sikertelenek lesznek, mivel legfeljebb 18 visszaállítási pont lehet.

A felhasználónak el kell távolítania a zárolást, és törölnie kell a visszaállítási pont gyűjteményét abból a célból, hogy a biztonsági mentések sikeresek legyenek, a visszaállítási pontok gyűjteményének eltávolításához [kövesse az alábbi lépéseket](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) .

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Támogatja az Azure Backup a standard SSD által felügyelt lemezeket?

Igen, Azure Backup támogatja a [standard SSD felügyelt lemezeket](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Tud biztonsági másolatot készíteni egy virtuális gépről írásgyorsító (WA)-kompatibilis lemezzel?

Nem lehet pillanatképeket felvenni a WA-kompatibilis lemezre. A Azure Backup szolgáltatás azonban kizárhatja a WA-kompatibilis lemezt a biztonsági mentésből.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Van egy írásgyorsító (WA) lemezzel rendelkező virtuális gép, és SAP HANA telepítve. Hogyan biztonsági mentést?

Azure Backup nem tud biztonsági másolatot készíteni a WA-kompatibilis lemezről, de kizárhatja azt a biztonsági mentésből. A biztonsági mentés azonban nem biztosít adatbázis-konzisztenciát, mert a WA-kompatibilis lemezen található információk nem rendelkeznek biztonsági mentéssel. Ebben a konfigurációban biztonsági mentést készíthet a lemezekről, ha az operációs rendszer lemezének biztonsági mentését kívánja végezni, valamint a nem a WA-kompatibilis lemezek biztonsági mentését.

Privát előzetes verzióban futtatunk egy SAP HANA biztonsági mentést 15 perces RPO. Ez az SQL-adatbázis biztonsági mentésének hasonló módja, és a backInt felületet használja az SAP HANA által tanúsított külső megoldások számára. Ha érdekli, küldjön e-mailt a `AskAzureBackupTeam@microsoft.com` címen, és **Jelentkezzen be az Azure-beli virtuális gépeken futó SAP HANA biztonsági mentésére szolgáló privát előzetes verzióra**.

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Mi a maximális késleltetés a virtuális gép biztonsági mentési házirendjében beállított ütemezett biztonsági mentési idő alapján a biztonsági mentés kezdési idején?

Az ütemezett biztonsági mentés az ütemezett biztonsági mentés időpontjától számított 2 órán belül aktiválódik. Ha például az 100 virtuális gép biztonsági mentési ideje a 2:00 ÓRAKOR van ütemezve, 4:00 akkor az összes 100-es virtuális gép esetében a teljes biztonsági mentési feladatot fogja biztosítani. Ha az ütemezett biztonsági mentések a leállás és a folytatás/újrapróbálkozás miatt szüneteltek, akkor a biztonsági mentés az ütemezett kétórás időszakon kívül is elindítható.

### <a name="what-is-the-minimum-allowed-retention-range-for-daily-backup-point"></a>Mi a minimálisan megengedett megőrzési időtartam a napi biztonsági mentési ponthoz?

Az Azure-beli virtuális gépek biztonsági mentési szabályzata legfeljebb 9999 napig érvényes megőrzési időtartamot támogat hét nap alatt. A meglévő virtuális gépek biztonsági mentési szabályzatának hét napnál rövidebb ideig történő módosítása a hét nap minimális megőrzési időtartamának kielégítéséhez frissítést igényel.

### <a name="can-i-backup-or-restore-selective-disks-attached-to-a-vm"></a>Készíthetek biztonsági mentést vagy visszaállítani a virtuális géphez csatolt szelektív lemezeket?

A Azure Backup mostantól támogatja a szelektív lemezek biztonsági mentését és visszaállítását az Azure-beli virtuális gép biztonsági mentési megoldásával.

Napjainkban a Azure Backup támogatja a virtuális gép összes lemezének (operációs rendszerének és adattípusának) biztonsági mentését egy virtuális gépen, a virtuális gépek biztonsági mentési megoldásával együtt. A lemezek kizárása funkcióval lehetősége van arra, hogy a virtuális gép számos adatlemezéről egy vagy több biztonsági másolatot készítsen. Ez hatékony és költséghatékony megoldást kínál a biztonsági mentési és visszaállítási igények kielégítésére. Mindegyik helyreállítási pont a biztonsági mentési műveletben található lemezek adatait tartalmazza, így a visszaállítási művelet során a megadott helyreállítási pontról visszaállított lemezek egy részhalmaza is elérhető. Ez a pillanatképből és a tárolóból történő visszaállításra vonatkozik.

Az előzetes verzióra való feliratkozáshoz írjon nekünk a következő címen: AskAzureBackupTeam@microsoft.com

## <a name="restore"></a>Visszaállítás

### <a name="how-do-i-decide-whether-to-restore-disks-only-or-a-full-vm"></a>Hogyan eldönteni, hogy csak lemezeket vagy teljes virtuális gépet szeretne-e visszaállítani?

Egy Azure-beli virtuális gép gyors létrehozási lehetőségeként tekintse át a virtuális gép visszaállítását. Ez a beállítás megváltoztatja a lemezeket, a nyilvános IP-címeket és a hálózati adapterek nevét. A módosítás egyedi erőforrásokat tart fenn a virtuális gépek létrehozásakor. A virtuális gép nincs felvéve egy rendelkezésre állási csoportba.

Használhatja a lemez visszaállítása lehetőséget, ha a következőket kívánja használni:

- Szabja testre a létrehozott virtuális gépet. Módosítsa például a méretet.
- Adja meg a biztonsági mentés időpontjában nem található konfigurációs beállításokat.
- A létrehozott erőforrások elnevezési konvenciójának szabályozása.
- Adja hozzá a virtuális gépet egy rendelkezésre állási csoporthoz.
- Adja meg az egyéb beállításokat, amelyeket a PowerShell vagy egy sablon használatával kell konfigurálni.

### <a name="can-i-restore-backups-of-unmanaged-vm-disks-after-i-upgrade-to-managed-disks"></a>Visszaállíthatom a nem felügyelt virtuális gépek biztonsági másolatait a felügyelt lemezekre való frissítés után?

Igen, használhat olyan biztonsági másolatokat, amelyeket a lemezek a nem felügyelt állapotból felügyelt rendszerbe való migrálása előtt készítettek.

### <a name="how-do-i-restore-a-vm-to-a-restore-point-before-the-vm-was-migrated-to-managed-disks"></a>Hogyan lehet visszaállítani egy virtuális gépet egy, a virtuális gép felügyelt lemezekre történő migrálását megelőző visszaállítási pontra?

A visszaállítási folyamat változatlan marad. Ha a helyreállítási pont olyan időpontra van állítva, amikor a virtuális gép nem felügyelt lemezekkel rendelkezik, a [lemezeket nem felügyelt állapotba állíthatja vissza](tutorial-restore-disk.md#unmanaged-disks-restore). Ha a virtuális gép felügyelt lemezekkel rendelkezik, akkor a [lemezeket felügyelt lemezként állíthatja vissza](tutorial-restore-disk.md#managed-disk-restore). Ezután [létrehozhat egy virtuális gépet ezekből a lemezekről](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[További](backup-azure-vms-automation.md#restore-an-azure-vm) információ a PowerShellben való használatáról.

### <a name="can-i-restore-the-vm-thats-been-deleted"></a>Visszaállíthatom a törölt virtuális gépet?

Igen. Még ha törli a virtuális gépet is, a tárolóban található megfelelő biztonsági mentési elemre kattintva visszaállíthatja a helyreállítási pontot.

### <a name="how-to-restore-a-vm-to-the-same-availability-sets"></a>Virtuális gép visszaállítása ugyanazon rendelkezésre állási csoportokra

A felügyelt lemezes Azure-beli virtuális gépek esetében a rendelkezésre állási csoportokra történő visszaállítás a felügyelt lemezként való visszaállításkor a sablonban beállítás engedélyezésével engedélyezhető. Ez a sablon tartalmazza a **rendelkezésre állási készletek**nevű bemeneti paramétert.

### <a name="how-do-we-get-faster-restore-performances"></a>Hogyan lehet gyorsabb visszaállítási teljesítményt kapni?

Az [azonnali visszaállítási](backup-instant-restore-capability.md) funkció segíti a gyorsabb biztonsági mentést, és azonnal helyreállítja a pillanatképeket.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Mi történik, ha módosítjuk a titkosított virtuális gép Key Vault-beállításait?

Miután módosította a kulcstároló beállításait a titkosított virtuális gép számára, a biztonsági mentések továbbra is az új készlettel fognak működni. A helyreállítási pontról a módosítás előtti visszaállítás után azonban vissza kell állítania a kulcstartóban lévő titkos kulcsokat, mielőtt létrehozza a virtuális gépet. További információkért tekintse meg ezt a [cikket](https://docs.microsoft.com/azure/backup/backup-azure-restore-key-secret)

Az olyan műveletek, mint a titkos kulcs/kulcsok átadása nem igénylik ezt a lépést, és ugyanazt a kulcstartót is használhatják a visszaállítás után.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>A virtuális gép a tartományvezérlővel megszakadt kapcsolattal rendelkező virtuális gépek miatt is elérhető a helyreállításhoz?

Igen, a virtuális gépet a rendszer visszaállította a tartományvezérlővel megszakadt kapcsolatot biztosító virtuális gép miatt. További információkért tekintse meg ezt a [cikket](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps)

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
