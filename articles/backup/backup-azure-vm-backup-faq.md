---
title: GYIK – Azure-beli virtuális gépek biztonsági mentése
description: Ebből a cikkből megismerheti az Azure-beli virtuális gépek Azure Backup szolgáltatással történő biztonsági mentésével kapcsolatos gyakori kérdésekre adott válaszokat.
ms.reviewer: sogup
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 03e2f004fa54ee235eabc49afd6abd7532a6ee44
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/17/2020
ms.locfileid: "88262772"
---
# <a name="frequently-asked-questions-back-up-azure-vms"></a>Gyakori kérdések – Azure-beli virtuális gépek biztonsági mentése

Ez a cikk az Azure-beli virtuális gépek [Azure Backup](./backup-overview.md) szolgáltatással történő biztonsági mentésével kapcsolatos gyakori kérdésekre ad választ.

## <a name="backup"></a>Backup

### <a name="which-vm-images-can-be-enabled-for-backup-when-i-create-them"></a>Milyen virtuálisgép-rendszerképeket lehet engedélyezni a biztonsági mentéshez, amikor létrehozom őket?

Virtuális gép létrehozásakor engedélyezheti a biztonsági mentést a [támogatott operációs rendszereket](backup-support-matrix-iaas.md#supported-backup-actions)futtató virtuális gépeken.

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

### <a name="i-enabled-a-lock-on-the-resource-group-created-by-azure-backup-service-for-example-azurebackuprg_geo_number-will-my-backups-continue-to-work"></a>Engedélyeztem a Azure Backup szolgáltatás által létrehozott erőforráscsoport zárolását (például: `AzureBackupRG_<geo>_<number>` ). A biztonsági mentések továbbra is működni fognak?

Ha zárolja az Azure Backup szolgáltatás által létrehozott erőforráscsoportot, a biztonsági mentések sikertelenek lesznek, mivel a rendszer legfeljebb 18 visszaállítási pontot tud megkezdeni.

Távolítsa el a zárolást, és törölje a visszaállítási pont gyűjteményét az adott erőforráscsoporthoz, hogy a biztonsági mentések sikeresek legyenek. A visszaállítási pontok gyűjteményének eltávolításához [kövesse az alábbi lépéseket](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#clean-up-restore-point-collection-from-azure-portal) .

### <a name="does-azure-backup-support-standard-ssd-managed-disks"></a>Támogatja az Azure Backup a standard SSD által felügyelt lemezeket?

Igen, Azure Backup támogatja a [standard SSD felügyelt lemezeket](https://azure.microsoft.com/blog/announcing-general-availability-of-standard-ssd-disks-for-azure-virtual-machine-workloads/).

### <a name="can-we-back-up-a-vm-with-a-write-accelerator-wa-enabled-disk"></a>Tud biztonsági másolatot készíteni egy virtuális gépről írásgyorsító (WA)-kompatibilis lemezzel?

Nem lehet pillanatképeket felvenni a WA-kompatibilis lemezre. A Azure Backup szolgáltatás azonban kizárhatja a WA-kompatibilis lemezt a biztonsági mentésből.

### <a name="i-have-a-vm-with-write-accelerator-wa-disks-and-sap-hana-installed-how-do-i-back-up"></a>Van egy írásgyorsító (WA) lemezzel rendelkező virtuális gép, és SAP HANA telepítve. Hogyan biztonsági mentést?

Azure Backup nem tud biztonsági másolatot készíteni a WA-kompatibilis lemezről, de kizárhatja azt a biztonsági mentésből. A biztonsági mentés azonban nem biztosít adatbázis-konzisztenciát, mert a WA-kompatibilis lemezen található információk nem rendelkeznek biztonsági mentéssel. Ebben a konfigurációban biztonsági mentést készíthet a lemezekről, ha az operációs rendszer lemezének biztonsági mentését kívánja végezni, valamint a nem a WA-kompatibilis lemezek biztonsági mentését.

A Azure Backup egy 15 perces RPO rendelkező SAP HANA-adatbázisokhoz biztosít streaming biztonsági mentési megoldást. Az SAP által tanúsított Backint natív biztonsági mentési támogatást biztosítanak a SAP HANA natív API-jai kihasználásához. További [információ a SAP HANA adatbázisok Azure-beli virtuális gépeken történő biztonsági mentéséről](./sap-hana-db-about.md).

### <a name="what-is-the-maximum-delay-i-can-expect-in-backup-start-time-from-the-scheduled-backup-time-i-have-set-in-my-vm-backup-policy"></a>Mi a maximális késleltetés a virtuális gép biztonsági mentési házirendjében beállított ütemezett biztonsági mentési idő alapján a biztonsági mentés kezdési idején?

Az ütemezett biztonsági mentés az ütemezett biztonsági mentés időpontjától számított 2 órán belül aktiválódik. Ha például az 100 virtuális gép biztonsági mentésének kezdési időpontja a 2:00 ÓRAKOR van ütemezve, akkor a legújabb 4:00-kor a 100 teljes biztonsági mentési feladatokra vonatkozó folyamatban van. Ha az ütemezett biztonsági mentések leállása miatt szüneteltek, és folytatták vagy újrapróbálták, akkor a biztonsági mentés az ütemezett két órás időszakon kívül is elindítható.

### <a name="what-is-the-minimum-allowed-retention-range-for-a-daily-backup-point"></a>Mennyibe kerül a napi biztonsági mentési pontok minimálisan megengedett megőrzési tartománya?

Az Azure-beli virtuális gépek biztonsági mentési szabályzata legfeljebb 9999 napig érvényes megőrzési időtartamot támogat hét nap alatt. A meglévő virtuális gépek biztonsági mentési szabályzatának hét napnál rövidebb ideig történő módosítása a hét nap minimális megőrzési időtartamának kielégítéséhez frissítést igényel.

### <a name="what-happens-if-i-change-the-case-of-the-name-of-my-vm-or-my-vm-resource-group"></a>Mi történik, ha módosítom a virtuális gép vagy a saját virtuálisgép-erőforráscsoport nevét?

Ha módosítja a virtuális gép vagy a virtuálisgép-erőforráscsoport esetét (a felső vagy az alsó értékre), akkor a biztonsági mentési elem neve nem változik. Azonban ez a várt Azure Backup viselkedés. Az eset változása nem jelenik meg a biztonsági másolati elemben, de a háttérben frissül.

### <a name="can-i-back-up-or-restore-selective-disks-attached-to-a-vm"></a>Készíthetek biztonsági mentést vagy visszaállítani a virtuális géphez csatolt szelektív lemezeket?

A Azure Backup mostantól támogatja a szelektív lemezek biztonsági mentését és visszaállítását az Azure-beli virtuális gép biztonsági mentési megoldásával. További információ: [szelektív lemezek biztonsági mentése és visszaállítása Azure-beli virtuális gépekhez](selective-disk-backup-restore.md).

### <a name="are-managed-identities-preserved-if-a-tenant-change-occurs-during-backup"></a>Megőrzöttek-e a felügyelt identitások, ha a bérlő változása a biztonsági mentés során történik?

Ha a [bérlő megváltozik](https://docs.microsoft.com/azure/devops/organizations/accounts/change-azure-ad-connection) , le kell tiltania és újra engedélyeznie kell a [felügyelt identitásokat](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) , hogy a biztonsági mentések újra működjenek.

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

A visszaállítási folyamat változatlan marad. Ha a helyreállítási pont olyan időpontra van állítva, amikor a virtuális gép nem felügyelt lemezekkel rendelkezik, a [lemezeket nem felügyelt állapotba állíthatja vissza](tutorial-restore-disk.md#unmanaged-disks-restore). Ha a virtuális gép felügyelt lemezekkel rendelkezik, a [lemezeket felügyelt lemezként állíthatja vissza](tutorial-restore-disk.md#managed-disk-restore). Ezután [létrehozhat egy virtuális gépet ezekből a lemezekről](tutorial-restore-disk.md#create-a-vm-from-the-restored-disk).

[További](backup-azure-vms-automation.md#restore-an-azure-vm) információ a PowerShellben való használatáról.

### <a name="if-the-restore-fails-to-create-the-vm-what-happens-to-the-disks-included-in-the-restore"></a>Ha a visszaállítás nem sikerül létrehozni a virtuális gépet, mi történik a visszaállításban található lemezekkel?

Felügyelt virtuális gépek visszaállítása esetén, még akkor is, ha a virtuális gép létrehozása meghiúsul, a lemezeket továbbra is visszaállítja a rendszer.

### <a name="can-i-restore-a-vm-thats-been-deleted"></a>Visszaállíthatok egy törölt virtuális gépet?

Igen. Még ha törli is a virtuális gépet, a tárolóban található megfelelő biztonsági másolati elemre kattintva visszaállíthatja a helyreállítási pontot.

### <a name="how-do-i-restore-a-vm-to-the-same-availability-sets"></a>Hogyan egy virtuális gépet ugyanarra a rendelkezésre állási csoportra visszaállítani?

A felügyelt lemezes Azure-beli virtuális gépek esetében a rendelkezésre állási csoportokra való visszaállítást engedélyezheti, ha a sablonban a felügyelt lemezként való visszaállításkor lehetőséget biztosít. Ez a sablon tartalmazza a **rendelkezésre állási készletek**nevű bemeneti paramétert.

### <a name="how-do-we-get-faster-restore-performances"></a>Hogyan lehet gyorsabb visszaállítási teljesítményt kapni?

Az [azonnali visszaállítási](backup-instant-restore-capability.md) funkció segíti a gyorsabb biztonsági mentést, és azonnal helyreállítja a pillanatképeket.

### <a name="what-happens-when-we-change-the-key-vault-settings-for-the-encrypted-vm"></a>Mi történik, ha módosítjuk a titkosított virtuális gép Key Vault-beállításait?

Miután módosította a kulcstároló beállításait a titkosított virtuális gép számára, a biztonsági mentések továbbra is az új készlettel fognak működni. A helyreállítási pont visszaállítása után azonban a módosítás előtt vissza kell állítania a kulcstartóban lévő titkos kulcsokat, mielőtt a virtuális gépet létre tudja hozni belőle. További információkért tekintse meg [ezt a cikket](./backup-azure-restore-key-secret.md).

Az olyan műveletek, mint a titkos kulcs/kulcsok átadása nem igénylik ezt a lépést, és ugyanezt a kulcstartót is használhatja a visszaállítás után.

### <a name="can-i-access-the-vm-once-restored-due-to-a-vm-having-broken-relationship-with-domain-controller"></a>A virtuális gép a tartományvezérlővel megszakadt kapcsolattal rendelkező virtuális gépek miatt is elérhető a helyreállításhoz?

Igen, a virtuális gépet a rendszer visszaállította a tartományvezérlővel megszakadt kapcsolatot biztosító virtuális gép miatt. További információkért tekintse meg ezt a [cikket](./backup-azure-arm-restore-vms.md#post-restore-steps)

## <a name="manage-vm-backups"></a>Virtuális gép biztonsági mentéseinek kezelése

### <a name="what-happens-if-i-modify-a-backup-policy"></a>Mi történik, ha módosítom a biztonsági mentési szabályzatot?

A virtuális gép biztonsági mentése a módosított vagy az új szabályzatban szereplő ütemterv és adatmegőrzési beállítások használatával történik.

- Ha a megőrzés kibővül, a meglévő helyreállítási pontok az új szabályzatnak megfelelően vannak megjelölve és tárolva.
- Ha a megőrzés csökken, a helyreállítási pontok a következő karbantartási feladatokban törlésre vannak megjelölve, és ezt követően törlődnek.

### <a name="how-do-i-move-a-vm-backed-up-by-azure-backup-to-a-different-resource-group"></a>Hogyan egy olyan virtuális gépet, amelyről biztonsági másolatot készít Azure Backup egy másik erőforráscsoporthoz?

1. Átmenetileg állítsa le a biztonsági mentést, és őrizze meg a biztonsági mentési adatait.
2. Azure Backup konfigurált virtuális gépek áthelyezéséhez hajtsa végre a következő lépéseket:

   1. Keresse meg a virtuális gép helyét.
   2. Keressen egy erőforráscsoportot a következő elnevezési mintával: `AzureBackupRG_<location of your VM>_1` . Például *AzureBackupRG_westus2_1*
   3. A Azure Portalban tekintse meg a **rejtett típusok megjelenítése**részt.
   4. Keresse meg a **Microsoft. számítás/restorePointCollections** típusú erőforrást, amely az elnevezési mintával rendelkezik `AzureBackup_<name of your VM that you're trying to move>_###########` .
   5. Az erőforrás törlése. Ez a művelet csak az azonnali helyreállítási pontokat törli, a tárolóban lévő biztonsági másolatból nem.
   6. A törlési művelet befejezése után áthelyezheti a virtuális gépet.

3. Helyezze át a virtuális gépet a célként megadott erőforrás-csoportba.
4. Folytassa a biztonsági mentést.

Visszaállíthatja a virtuális gépet az áthelyezési művelet előtt létrehozott, elérhető visszaállítási pontokból.

### <a name="what-happens-after-i-move-a-vm-to-a-different-resource-group"></a>Mi történik, ha egy virtuális gépet másik erőforráscsoporthoz helyezek át?

Ha egy virtuális gépet egy másik erőforráscsoporthoz helyez át, akkor ez egy új virtuális gép, amely a Azure Backupt illeti.

Miután áthelyezte a virtuális gépet egy új erőforráscsoporthoz, a virtuális gépet újra védetté teheti ugyanazon a tárolóban vagy egy másik tárolóban. Mivel ez egy új virtuális gép a Azure Backuphoz, külön díjat számítunk fel.

Ha szükséges, a régi virtuális gép visszaállítási pontjai a visszaállításhoz lesznek elérhetők. Ha nincs szüksége erre a biztonsági mentési adatokra, leállíthatja a régi virtuális gép védelmét az adatok törlése lehetőséggel.

### <a name="is-there-a-limit-on-number-of-vms-that-can-beassociated-with-the-same-backup-policy"></a>Van korlátozva a virtuális gépek száma, amelyek ugyanahhoz a biztonsági mentési szabályzathoz társíthatók?

Igen, legfeljebb 100 virtuális gép lehet, amely ugyanahhoz a biztonsági mentési szabályzathoz társítható a portálról. Javasoljuk, hogy több mint 100 virtuális gépen hozzon létre több biztonsági mentési szabályzatot ugyanazzal az ütemtervtel vagy eltérő ütemtervtel.

### <a name="how-can-i-view-the-retention-settings-for-my-backups"></a>Hogyan tekinthetem meg a biztonsági másolatok megőrzési beállításait?

Jelenleg a virtuális géphez rendelt biztonsági mentési házirend alapján megtekintheti a biztonsági mentési elem (VM) szintjén megőrzött adatmegőrzési beállításokat. 

A biztonsági másolatok megőrzési beállításainak megtekintésére az egyik lehetőség, hogy megkeresi a virtuális gép biztonsági mentési elemének [irányítópultját](https://docs.microsoft.com/azure/backup/backup-azure-manage-vms#view-vms-on-the-dashboard) a Azure Portal. A biztonsági mentési házirendre mutató hivatkozásra kattintva megtekintheti a virtuális géphez társított napi, heti, havi és éves adatmegőrzési pontok megőrzési időtartamát.

A [Backup Explorer](https://docs.microsoft.com/azure/backup/monitor-azure-backup-with-backup-explorer) használatával megtekintheti az összes virtuális gép megőrzési beállításait egyetlen ablaktáblán belül. Keresse meg a Backup Explorert bármely Recovery Services-tárolóból, nyissa meg a **biztonsági mentési elemek** lapot, és válassza ki a speciális nézetet az egyes virtuális gépek részletes adatmegőrzési adatainak megtekintéséhez.  