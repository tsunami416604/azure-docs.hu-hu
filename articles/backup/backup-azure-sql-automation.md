---
title: SQL DB az Azure VM biztonsági mentésében & PowerShellen keresztüli visszaállításhoz
description: Készítsen biztonsági másolatot és állítsa vissza az SQL-adatbázisokat az Azure-beli virtuális gépeken az Azure Backup és a PowerShell használatával.
ms.topic: conceptual
ms.date: 03/15/2019
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 9608b02869b1d41d901ec77a42cfaa6d882040e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131816"
---
# <a name="back-up-and-restore-sql-databases-in-azure-vms-with-powershell"></a>SQL-adatbázisok biztonsági mentése és visszaállítása az Azure virtuális gépein a PowerShell használatával

Ez a cikk ismerteti, hogyan azure PowerShell segítségével biztonsági másolatot készíteni, és az [Azure-beli](backup-overview.md) virtuális gép egy Azure Backup Recovery Services-tároló n belül egy SQL DB biztonsági másolatot készíteni.

Ez a cikk a következőket ismerteti:

> [!div class="checklist"]
>
> * Állítsa be a PowerShellt, és regisztrálja az Azure Recovery Services Provider.Set up PowerShell and register the Azure Recovery Services Provider.
> * Recovery Services-tároló létrehozása.
> * Konfigurálja az SQL DB biztonsági mentését egy Azure virtuális gépen belül.
> * Futtasson egy biztonsági mentési feladatot.
> * Biztonsági másolatot kell állítani az SQL DB-ről.
> * A biztonsági mentés figyelése és visszaállítása.

## <a name="before-you-start"></a>Előkészületek

* [További információ](backup-azure-recovery-services-vault-overview.md) a Recovery Services-tárolókról.
* Az [SQL-db-ek Azure-beli virtuális gépeken belüli biztonsági mentésének](backup-azure-sql-database.md#before-you-start)funkciófunkcióiról olvashat.
* Tekintse át a PowerShell-objektumhierarchiát a Recovery Services számára.

### <a name="recovery-services-object-hierarchy"></a>Helyreállítási szolgáltatások objektumhierarchiája

Az objektumhierarchiát a következő ábra foglalja össze.

![Helyreállítási szolgáltatások objektumhierarchiája](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Tekintse át az **Az.RecoveryServices** [parancsmag referenciahivatkozást](/powershell/module/az.recoveryservices) az Azure-kódtárban.

### <a name="set-up-and-install"></a>Beállítás és telepítés

Állítsa be a PowerShellt az alábbiak szerint:

1. [Töltse le az Az PowerShell legújabb verzióját.](/powershell/azure/install-az-ps) A minimális verzió szükséges 1.5.0.

2. Keresse meg az Azure Backup PowerShell-parancsmagjait ezzel a paranccsal:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Tekintse át az Azure Backup és a Recovery Services-tároló aliasait és parancsmagjait. Íme egy példa arra, hogy mit láthat. Ez nem egy teljes lista a parancsmagokról.

    ![A helyreállítási szolgáltatások parancsmagjainak listája](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Jelentkezzen be Azure-fiókjába a **Connect-AzAccount segítségével.**
5. A megjelenő weblapon a rendszer kéri, hogy adja meg a fiók hitelesítő adatait.

    * A fiók hitelesítő adatait paraméterként is megadhatja a **Connect-AzAccount** parancsmag **-Credential paraméterei**közé.
    * Ha ön egy bérlőnek dolgozó csp-partner, adja meg az ügyfelet bérlőként a bérlőazonosító vagy a bérlő elsődleges tartományneve használatával. Erre példa a **Connect-AzAccount -Tenant** fabrikam.com.

6. Társítsa a használni kívánt előfizetést a fiókhoz, mert egy fióknak több előfizetése is lehet.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Ha első alkalommal használja az Azure Backup szolgáltatást, a **Register-AzResourceProvider** parancsmaggal regisztrálja az Azure Recovery Services-szolgáltatót az előfizetéssel.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Ellenőrizze, hogy a szolgáltatók sikeresen regisztráltak-e:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. A parancs kimenetében ellenőrizze, hogy a **RegistrationState** regisztrált ra **változik-e.** Ha nem, futtassa újra a **Register-AzResourceProvider** parancsmagát.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Helyreállítási szolgáltatások tárolójának létrehozásához kövesse az alábbi lépéseket.

A Recovery Services-tároló egy Erőforrás-kezelő erőforrás, ezért egy erőforráscsoporton belül kell elhelyezni. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egy erőforráscsoportot a **New-AzResourceGroup** parancsmaggal. Erőforráscsoport létrehozásakor adja meg az erőforráscsoport nevét és helyét.

1. A tároló egy erőforráscsoportba kerül. Ha nem rendelkezik meglévő erőforráscsoporttal, hozzon létre egy újat a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). Ebben a példában egy új erőforráscsoportot hozunk létre az USA nyugati régiójában.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. A Vault létrehozásához használja a [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) parancsmag. Adja meg a tároló nak ugyanazt a helyet, amelyet az erőforráscsoporthoz használt.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Adja meg a tároló tárolóhoz használandó redundancia típusát.

    * Helyileg [redundáns tárolást](../storage/common/storage-redundancy-lrs.md) vagy [georedundáns tárolást](../storage/common/storage-redundancy-grs.md)használhat.
    * A következő példa a **-BackupStorageRedundancy** beállítást állítja be a[Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd számára a **GeoRedundant**beállításhoz. **testvault**

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>A tárolók megtekintése egy előfizetésben

Az előfizetés összes tárolójának megtekintéséhez használja a [Get-AzRecoveryServicesVault .To](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)view all vaults in the subscription, use Get-AzRecoveryServicesVault .

```powershell
Get-AzRecoveryServicesVault
```

A kimenet hasonló a következőhöz. A társított erőforráscsoport és hely meg van adva.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>A tároló környezetének beállítása

Tárolja a tárolóobjektumot egy változóban, és állítsa be a tároló környezetét.

* Számos Azure Backup-parancsmag bemenetként igényli a Recovery Services-tároló objektumát, ezért célszerű a tárolóobjektumot egy változóban tárolni.
* A tárolási környezet a tár által védett adatok típusa. Állítsa be a [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). A környezet beállítása után az összes további parancsmagra vonatkozik.

A következő példa a **testvault**tárolókörnyezetét állítja be.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>A tároló azonosítójának beolvasása

Azt tervezzük, hogy az Azure PowerShell irányelveinek megfelelően elavulttá tesszük a tárolókörnyezet-beállítást. Ehelyett tárolhatja vagy lehívhatja a tárolóazonosítót, és átadhatja a megfelelő parancsoknak az alábbiak szerint:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Biztonságimentési házirend konfigurálása

A biztonsági mentési házirend meghatározza a biztonsági mentések ütemezését, valamint azt, hogy mennyi ideig kell megtartani a biztonsági mentéshelyreállítási pontokat:

* A biztonsági mentési szabályzat legalább egy adatmegőrzési házirendhez van társítva. Az adatmegőrzési szabály határozza meg, hogy mennyi ideig a helyreállítási pont megtartása előtt törlődik.
* Tekintse meg az alapértelmezett biztonsági mentési házirend-megőrzést a [Get-AzRecoveryServicesBackupRetentionPolicyObject használatával.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)
* Tekintse meg az alapértelmezett biztonsági mentési házirend ütemezését a [Get-AzRecoveryServicesBackupPolicyObject objektummal.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)
* A [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) parancsmag használatával új biztonsági mentési házirendet hozhat létre. Adja meg az ütemezési és adatmegőrzési házirend objektumok.

Alapértelmezés szerint a kezdési időpont az Ütemezési házirendobjektumban van definiálva. A következő példában módosíthatja a kezdési időpontot a kívánt kezdési időpontra. A kívánt kezdési időpontnak UTC-ben is meg kell lennie. Az alábbi példa feltételezi, hogy a kívánt kezdési időpont 01:00 UTC a napi biztonsági mentések.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Meg kell adnia a kezdési időt csak 30 perces többszörösökben. A fenti példában csak "01:00:00" vagy "02:30:00" lehet. A kezdési időpont nem lehet "01:15:00"

A következő példa tárolja az ütemezési szabályzatot és az adatmegőrzési házirendet változókban. Ezután ezeket a változókat használja paraméterekként egy új házirendhez (**NewSQLPolicy**). **A NewSQLPolicy** napi "Teljes" biztonsági mentést készít, 180 napig megőrzi, és 2 óránként naplómentést készít

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A kimenet hasonló a következőhöz.

```output
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 01:30:00 AM      Daily                                    False                True
```

## <a name="enable-backup"></a>Biztonsági mentés engedélyezése

### <a name="registering-the-sql-vm"></a>Az SQL virtuális gép regisztrálása

Az Azure virtuális gép biztonsági mentések és az Azure file shares, backup szolgáltatás csatlakozhat az Azure Resource Manager-erőforrások és lekérni a megfelelő részleteket. Mivel az SQL egy Azure virtuális gépen belüli alkalmazás, a biztonsági mentési szolgáltatásnak engedélyre van szüksége az alkalmazás eléréséhez és a szükséges adatok lekéréséhez. Ehhez *"regisztrálnia"* kell az Azure virtuális gépet, amely tartalmazza az SQL-alkalmazást egy Recovery Services-tárolóval. Miután regisztrált egy SQL virtuális gépet egy tárolóval, csak az adott tárolóban védheti meg az SQL-db-eket. A VM regisztrálásához használja a [Register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS parancsmatot.

```powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
```

A parancs visszaadja az erőforrás "biztonsági másolat tárolóját", és az állapot "regisztrálva" lesz

> [!NOTE]
> Ha az erő paraméter nincs megadva, a felhasználónak a következő szöveggel kell megerősítenie a következő szöveggel: "Letiltja a tároló védelmét". Kérjük, hagyja figyelmen kívül ezt a szöveget, és mondja az "Y" a megerősítést. Ez egy ismert probléma, és dolgozunk, hogy távolítsa el a szöveget, és a követelmény a force paraméter.

### <a name="fetching-sql-dbs"></a>SQL DB-k beolvasása

A regisztráció után a biztonsági mentési szolgáltatás képes lesz felsorolni az összes rendelkezésre álló SQL-összetevők a virtuális gépen belül. A tárolóba még biztonsági mentésre kerülő összes SQL-összetevő megtekintéséhez használja a [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS parancsmagát

```powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
```

A kimenet az elemtípussal és a Kiszolgálónév nevű tárolóba regisztrált összes SQL-virtuális gép összes nem védett SQL-összetevőjét jeleníti meg. Tovább szűrhet egy adott SQL virtuális gépre a "-Container" paraméter átadásával, vagy használhatja a "Name" és a "ServerName" kombinációt az ItemType jelzővel együtt egy egyedi SQL-elem megérkezéséhez.

```powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
```

### <a name="configuring-backup"></a>Biztonsági másolat konfigurálása

Most, hogy rendelkezünk a szükséges SQL DB-vel és azzal a házirenddel, amelyről biztonsági másolatot kell készíteni, használhatjuk az [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) parancsmaggal az SQL DB biztonsági mentésének konfigurálásához.

```output
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
```

A parancs megvárja, amíg a konfigurálási biztonsági mentés befejeződik, és a következő kimenetet adja vissza.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Új SQL-db-ek beolvasása

Miután a gép regisztrálva van, a Biztonsági mentési szolgáltatás lekéri a rendelkezésre álló DB-k adatait. Ha a felhasználó később hozzáadja az SQL DBs/SQL-példányokat a regisztrált géphez, manuálisan kell aktiválni a biztonsági mentési szolgáltatást egy friss "lekérdezés" végrehajtásához, hogy újra megkapja a nem védett DB-ket (beleértve az újonnan hozzáadottakat is). Használja az [Initialize-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS parancsmag az SQL virtuális gép egy friss lekérdezés. A parancs megvárja, amíg a művelet befejeződik. Később a [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS parancsmag használatával leszeretné késni a legújabb nem védett SQL-összetevők listáját

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
```

A megfelelő védett elemek beolvasása után engedélyezze a biztonsági mentéseket a [fenti szakaszban meghatározottmódon.](#configuring-backup)
Ha valaki nem szeretné manuálisan észlelni az új DB-ket, választhatja az automatikus védelmet az [alábbiak szerint](#enable-autoprotection).

## <a name="enable-autoprotection"></a>Automatikus védelem engedélyezése

A felhasználó beállíthatja a biztonsági mentést úgy, hogy a jövőben hozzáadott összes DB automatikusan védett legyen egy bizonyos házirenddel. Az automatikus védelem engedélyezéséhez használja [az Enable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS parancsmac.To enable autoprotection, use Enable-AzRecoveryServicesBackupAutoProtection PS cmdlet.

Mivel az utasítás az összes jövőbeli DB-k biztonsági másolatot, a művelet sqlinstance szinten történik.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $NewSQLPolicy -VaultId $targetvault.ID
```

Az automatikus védelmi szándék megadása után az újonnan hozzáadott DB-k lekéréséhez szükséges gép lekérdezése 8 óránként ütemezett háttérfeladatként történik.

## <a name="restore-sql-dbs"></a>SQL-db-ek visszaállítása

Az Azure Backup az alábbiak szerint állíthatja vissza az Azure virtuális gépeken futó SQL Server-adatbázisokat:

* Visszaállítás egy adott dátumra vagy időpontra (a másodikra) a tranzakciónapló biztonsági másolatainak használatával. Az Azure Backup automatikusan meghatározza a megfelelő teljes különbözeti biztonsági mentést és a naplóbiztonsági mentések láncolatát, amelyek a kiválasztott idő alapján visszaállíthatók.
* Adott teljes vagy különbözeti biztonsági mentés visszaállítása egy adott helyreállítási pontra való visszaállításhoz.

Az SQL DB-k visszaállítása előtt ellenőrizze az [itt](restore-sql-database-azure-vm.md#prerequisites) említett előfeltételeket.

Először olvassa be a megfelelő biztonsági másolatot az SQL [DB-ről a Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS parancsmag használatával.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
```

### <a name="fetch-the-relevant-restore-time"></a>A megfelelő visszaállítási idő beolvasása

As outlined above, user can restore the backed-up SQL DB to a full/differential copy **OR** to a log point-in-time.

#### <a name="fetch-distinct-recovery-points"></a>Különböző helyreállítási pontok beolvasása

A [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) segítségével különböző (teljes/különbözeti) helyreállítási pontokat szeretne lekérni egy biztonsági másolatban lévő SQL DB-hez.

```powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = (Get-Date).ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
```

A kimenet hasonló a következő példához

```output
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
```

Használja a "RecoveryPointId" szűrőt vagy egy tömbszűrőt a megfelelő helyreállítási pont lekéréséhez.

```powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
```

#### <a name="fetch-point-in-time-recovery-point"></a>Időponthoz-időpont helyreállítási pont beolvasása

Ha a felhasználó vissza szeretné állítani a db-t egy bizonyos időpontra, használja a [Get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS parancsmagját. A parancsmag az adott SQL biztonsági mentési elem töretlen, folyamatos naplóláncának kezdési és befejezési időpontjait jelölő dátumok listáját adja vissza. A kívánt időpontban kell lennie ezen a tartományon belül.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

A kimenet hasonló lesz a következő példához.

```output
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
```

A fenti kimenet azt jelenti, hogy a felhasználó visszaállíthatja a megjelenített kezdési és befejezési időpont közötti bármely időpontra. Az idők UTC-ben vannak. A fenti tartományon belül bármely időpontban építsünk ps-ben.

> [!NOTE]
> A visszaállításhoz kiválasztott naplópont-időpont esetén a felhasználónak nem kell megadnia azt a kiindulási pontot, azaz a teljes biztonsági mentést, amelyből a db visszaáll. Az Azure Backup szolgáltatás gondoskodik a teljes helyreállítási terv, azaz melyik teljes biztonsági mentést választani, milyen napló biztonsági mentések alkalmazni, stb.

### <a name="determine-recovery-configuration"></a>Helyreállítási konfiguráció meghatározása

SQL DB-visszaállítás esetén a következő visszaállítási forgatókönyvek támogatottak.

* A biztonsági másolatot készült SQL DB felülbírálása egy másik helyreállítási pontból származó adatokkal - OriginalWorkloadRestore
* Az SQL DB visszaállítása új db-ként ugyanabban az SQL-példányban - AlternateWorkloadRestore
* Az SQL DB visszaállítása új db-ként egy másik SQL virtuális gép egy másik SQL-példányában – AlternateWorkloadRestore
* Az SQL DB visszaállítása .bak fájlként -RestoreAsFiles

A megfelelő helyreállítási pont (különálló vagy naplópont-in-time) beolvasása után használja a [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS parancsmamot a helyreállítási konfigurációs objektum lekéréséhez a kívánt helyreállítási tervnek megfelelően.

#### <a name="original-workload-restore"></a>Eredeti munkaterhelés-visszaállítás

A biztonsági másolatot követő adatbázis felülírásához adja meg a megfelelő jelzőt és a megfelelő helyreállítási pontot a következő példában látható módon.

##### <a name="original-restore-with-distinct-recovery-point"></a>Eredeti visszaállítás különálló helyreállítási ponttal

```powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="original-restore-with-log-point-in-time"></a>Eredeti visszaállítás a naplópont-in-time-mal

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Másik munkaterhelés-visszaállítás

> [!IMPORTANT]
> Egy biztonsági másolatot készült SQL DB lehet visszaállítani, mint egy új ADATBÁZIS egy másik SQLInstance csak egy Azure virtuális gép "regisztrált" ebben a tárolóban.

A fentiek szerint, ha a cél SQLInstance egy másik Azure-beli virtuális gép, győződjön meg arról, hogy regisztrálva van [a tárolóba,](#registering-the-sql-vm) és a megfelelő SQLInstance védett elemként jelenik meg.

```powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
```

Ezután csak adja át a megfelelő helyreállítási pontot, a cél SQL-példány a megfelelő jelzőt, ahogy az alábbiakban látható.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>A visszaállítás másik helyreállása különálló helyreállítási ponttal

```powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="alternate-restore-with-log-point-in-time"></a>A visszaállítás alternatíva a bejelentkezési pont-in-time segítségével

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

##### <a name="restore-as-files"></a>Visszaállítás fájlként

Ha a biztonsági másolat adatait adatbázis helyett .bak fájlként szeretné visszaállítani, válassza a **Visszaállítás fájlként** lehetőséget. A biztonsági másolatot tartalmazó SQL DB visszaállítható bármely cél virtuális gép, amely regisztrálva van a tárolóba.

```powershell
$TargetContainer= Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName "VM name" -VaultId $vaultID
```

##### <a name="restore-as-files-with-distinct-recovery-point"></a>Visszaállítás különálló helyreállítási ponttal rendelkező fájlokként

```powershell
$FileRestoreWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-latest-full"></a>Visszaállítás fájlként a legutóbbi teljes naplópontból

```powershell
$FileRestoreWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

##### <a name="restore-as-files-with-log-point-in-time-from-a-specified-full"></a>Visszaállítás fájlként, amely naplópont-az idő egy megadott teljes

Ha a visszaállításhoz használandó teljes egészet szeretne megadni, használja a következő parancsot:

```powershell
$FileRestoreWithLogAndSpecificFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -FromFull $FullRP -TargetContainer $TargetContainer -RestoreAsFiles -FilePath "<>" -VaultId $targetVault.ID
```

A [Get-AzRecoveryServicesBackupWorkloadRecoveryRecoveryrecoveryps](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) parancsmagból kapott végső helyreállítási pont konfigurációs objektum rendelkezik a visszaállításhoz szükséges összes vonatkozó információval, és az alábbiak szerint található.

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : No
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

A visszaállított adatbázis-név, az OverwriteWLIfpresent, a NoRecoveryMode és a targetPhysicalPath mezők szerkeszthetők. További részletek a célfájl elérési útjairól az alábbiak szerint.

```powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath
```

```output
MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
```

Állítsa be a megfelelő PS-tulajdonságokat karakterlánc-értékként az alábbiak szerint.

```powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl
```

```output
TargetServer         : <SQL server name>
TargetInstance       : <Target Instance name>
RestoredDBName       : <Target Instance name>/azurebackup1_restored_3_19_2019_1850
OverwriteWLIfpresent : Yes
NoRecoveryMode       : Disabled
targetPhysicalPath   : {azurebackup1, azurebackup1_log}
ContainerId          : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/vmappcontainer;compute;computeRG;SQLVMName
SourceResourceId     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/computeRG/VMAppContainer/SQLVMName
RestoreRequestType   : Alternate WL Restore
RecoveryPoint        : Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureWorkloadRecoveryPoint
PointInTime          : 1/1/0001 12:00:00 AM
```

> [!IMPORTANT]
> Győződjön meg arról, hogy a végső helyreállítási konfigurációs objektum rendelkezik az összes szükséges és megfelelő értékkel, mivel a visszaállítási művelet a konfigurációs objektumon alapul.

### <a name="restore-with-relevant-configuration"></a>Visszaállítás a megfelelő konfigurációval

A megfelelő helyreállítási Konfigurációs objektum beszerzése és ellenőrzése után a [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS parancsmag segítségével indítsa el a visszaállítási folyamatot.

```powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
```

A visszaállítási művelet visszaad egy nyomon követendő feladatot.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
```

## <a name="manage-sql-backups"></a>SQL-biztonsági mentések kezelése

### <a name="on-demand-backup"></a>Igény szerinti biztonsági mentés

Miután a biztonsági mentés engedélyezve lett egy adatbázishoz, a felhasználó igény szerinti biztonsági mentést is kezdeményezhet az adatbázis számára a [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS parancsmag használatával. A következő példa elindítja a teljes biztonsági mentést egy SQL DB tömörítés engedélyezve van, és a teljes biztonsági mentést meg kell őrizni 60 napig.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

Az igény szerinti biztonsági mentési parancs visszaad egy nyomon követendő feladatot.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
```

Ha a kimenet megszakad, vagy ha meg szeretné szerezni a megfelelő feladatazonosítót, [lekell szereznie a feladatok listáját az](#track-azure-backup-jobs) Azure Backup szolgáltatásból, majd kövesse nyomon, és annak részleteit.

### <a name="change-policy-for-backup-items"></a>Biztonsági másolat elemeire vonatkozó házirend módosítása

A felhasználó módosíthatja a meglévő házirendet, vagy módosíthatja a biztonsági elem házirend1-ről Policy2-re történő házirendét. Ha egy biztonsági másolatban szereplő elem házirendjei között szeretne váltani, olvassa le a megfelelő házirendet, és készítsen biztonsági másolatot, és használja az [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) parancsot biztonsági másolattal a paraméterként.

```powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
```

A parancs megvárja, amíg a konfigurálási biztonsági mentés befejeződik, és a következő kimenetet adja vissza.

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="re-register-sql-vms"></a>SQL virtuális gépek újbóli regisztrálása

> [!WARNING]
> Az újbóli regisztrálás megkísérlése előtt olvassa el ezt a [dokumentumot,](backup-sql-server-azure-troubleshoot.md#re-registration-failures) hogy megértse a hiba tüneteit és okait.

Az SQL virtuális gép újbóli regisztrálásának elindításához olvassa be a megfelelő biztonsági mentési tárolót, és adja át a regiszter-parancsmagnak.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Register-AzRecoveryServicesBackupContainer -Container $SQLContainer -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID
```

### <a name="stop-protection"></a>A védelem kikapcsolása

#### <a name="retain-data"></a>Adatok megőrzése

Ha a felhasználó le kívánja állítani a védelmet, használhatja az [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS parancsmagát. Ez leállítja az ütemezett biztonsági mentéseket, de az eddig biztonsági másolatban szereplő adatok örökre megmaradnak.

```powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
```

#### <a name="delete-backup-data"></a>Biztonsági mentési adatok törlése

Annak érdekében, hogy teljesen távolítsa el a tárolt biztonsági mentési adatokat a tárolóban, csak add "-RemoveRecoveryPoints" jelző / kapcsoló a ["disable" védelmi parancs](#retain-data).

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
```

#### <a name="disable-auto-protection"></a>Automatikus védelem letiltása

Ha az automatikus védelem SQLInstance-en lett konfigurálva, a felhasználó letilthatja azt az [Disable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS parancsmag használatával.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
```

#### <a name="unregister-sql-vm"></a>SQL virtuális gép regisztrációjának megszüntetése

Ha egy SQL-kiszolgáló összes DB-je [már nem védett, és nincs biztonsági mentési adat,](#delete-backup-data)a felhasználó leírhatja az SQL virtuális gép regisztrációját erről a tárolóból. Csak ezután a felhasználó megvédheti a DB-ket egy másik tárolóba. [Az Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS parancsmag használatával törölje az SQL virtuális gép regisztrációját.

```powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
```

### <a name="track-azure-backup-jobs"></a>Az Azure Backup feladatok nyomon követése

Fontos megjegyezni, hogy az Azure Backup csak nyomon követi a felhasználó által aktivált feladatok SQL biztonsági mentésben. Az ütemezett biztonsági mentések (beleértve a naplóbiztonsági mentéseket is) nem láthatók a portálon/powershellben. Ha azonban valamelyik ütemezett feladat sikertelen, [a rendszer biztonsági mentési riasztást](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) hoz létre, és megjelenik a portálon. [Az Azure Monitor segítségével](backup-azure-monitoring-use-azuremonitor.md) nyomon követheti az összes ütemezett feladatok és egyéb releváns információkat.

A felhasználók nyomon követhetik az igény szerinti/felhasználó által aktivált műveleteket a JobID-vel, amely az aszinkron feladatok, például a biztonsági mentés [kimenetében](#on-demand-backup) jelenik meg. A [Get-AzRecoveryServicesBackupJobDetail](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetail) PS parancsmag segítségével nyomon követheti a feladatot és annak részleteit.

```powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
```

Az igény szerinti feladatok és állapotuk listájának az Azure Backup szolgáltatásból való lekéréséhez használja a [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS parancsmagját. A következő példa adja vissza az összes folyamatban lévő SQL-feladatok.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Folyamatban lévő feladat megszakításához használja a [Stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS parancsmag.

## <a name="managing-sql-always-on-availability-groups"></a>Sql Mindig a rendelkezésre állási csoportok kezelése

Az SQL Mindig rendelkezésre állási csoportok esetén győződjön meg arról, hogy [regisztrálja a](#registering-the-sql-vm) rendelkezésre állási csoport (AG) összes csomópontját. Ha az összes csomópont regisztrációja megtörtént, az SQL rendelkezésre állási csoport objektum logikailag jön létre a védett elemek alatt. Az SQL AG alatti adatbázisok "SQLDatabase" néven jelennek meg. A csomópontok önálló példányként jelennek meg, és az alattuk lévő alapértelmezett SQL-adatbázisok is SQL-adatbázisként jelennek meg.

Tegyük fel például, hogy egy SQL AG két csomópontossal rendelkezik: "sql-server-0" és "sql-server-1" és 1 SQL AG DB. Ha mindkét csomópont regisztrálva van, ha a felhasználó [felsorolja a védhető elemeket,](#fetching-sql-dbs)akkor a következő összetevőket sorolja fel:

* SQL AG objektum – védett elemtípus SQLAvailabilityGroup néven
* SQL AG DB – védett elemtípus SQLDatabase néven
* sql-server-0 - védett elemtípus SQLInstance
* sql-server-1 - védett elemtípus SQLInstance
* Bármely alapértelmezett SQL DB -k (főadat, modell, msdb) sql-server-0 alatt - védett elemtípus SQLDatabase
* Bármely alapértelmezett SQL DB -k (főadat, modell, msdb) sql-server-1 alatt - védett elemtípus SQLDatabase

sql-server-0, sql-server-1 is szerepel a "AzureVMAppContainer", ha [a biztonsági mentési tárolók vannak felsorolva.](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0)

Csak letölteni a megfelelő SQL-adatbázis, hogy [a biztonsági mentés](#configuring-backup) és az igény szerinti biztonsági [mentés](#on-demand-backup) és visszaállítás [PS parancsmagok](#restore-sql-dbs) azonosak.
