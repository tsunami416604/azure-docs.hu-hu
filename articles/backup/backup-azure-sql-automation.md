---
title: 'Azure Backup: Biztonsági mentése és visszaállítása az SQL-adatbázisok Azure-beli virtuális gépeken az Azure Backup és a PowerShell használatával'
description: Biztonsági mentése és visszaállítása az SQL-adatbázisok Azure-beli virtuális gépeken az Azure Backup és a PowerShell használatával.
services: backup
author: pvrk
manager: vijayts
keywords: Az Azure Backup; SQL;
ms.service: backup
ms.topic: conceptual
ms.date: 03/15/2019
ms.author: pullabhk
ms.assetid: 57854626-91f9-4677-b6a2-5d12b6a866e1
ms.openlocfilehash: 6469e3b35357867b6b38b00c8b11637ba30b2960
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287555"
---
# <a name="back-up-and-restore-sql-databases-in-azure--vms-with-powershell"></a>Biztonsági mentése és visszaállítása az SQL-adatbázisok Azure-beli virtuális gépeken a PowerShell-lel

Ez a cikk ismerteti az Azure PowerShell használatával biztonsági mentése és helyreállítása egy SQL DB használatával egy Azure virtuális Gépen belül [Azure Backup](backup-overview.md) Recovery Services-tároló.

Ez az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Állítsa be a PowerShell és az Azure Recovery Services-szolgáltató regisztrálásához.
> * Recovery Services-tároló létrehozása.
> * Biztonsági mentés konfigurálása az SQL DB-hez egy Azure virtuális Gépen belül.
> * A biztonsági mentési feladat futtatása.
> * Visszaállítás a biztonsági másolat az SQL DB.
> * Figyelheti a biztonsági mentési és visszaállítási feladatok.

## <a name="before-you-start"></a>Előkészületek

* [További](backup-azure-recovery-services-vault-overview.md) Recovery Services-tárolók kapcsolatban.
* Olvassa el az a funkciók [belül Azure virtuális gépeken futó SQL-adatbázisok biztonsági mentésével](backup-azure-sql-database.md#before-you-start).
* Tekintse át a Recovery Services PowerShell objektum hierarchiában.

### <a name="recovery-services-object-hierarchy"></a>Recovery Services objektum hierarchia

Az alábbi ábrán az objektumhierarchia foglalja össze.

![Recovery Services objektum hierarchia](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Tekintse át a **Az.RecoveryServices** [parancsmag-referencia](/powershell/module/az.recoveryservices) referencia az Azure-könyvtárban.

### <a name="set-up-and-install"></a>Beállítása és telepítése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Állítsa be a PowerShell a következőképpen:

1. [Töltse le a legújabb verzióját Az PowerShell](/powershell/azure/install-az-ps). A minimálisan szükséges verzió 1.5.0.

2. Keresse meg az Azure Backup PowerShell-parancsmagok a következő paranccsal:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Tekintse át az aliasokat és a parancsmagok az Azure Backup és a Recovery Services-tároló. Íme egy példa mi látható. Akkor sem parancsmagok teljes listája.

    ![A Recovery Services-parancsmagok listája](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Jelentkezzen be az Azure-fiókjába **Connect-AzAccount**.
5. A megjelenő webhelyen kéri, hogy adjon meg a fiók hitelesítő adatait.

    * Azt is megteheti, megadhatja a fiók hitelesítő adatait a paramétert a **Connect-AzAccount** parancsmagot **-hitelesítő adat**.
    * Ha Ön CSP-partner egy bérlő esetében működik, adja meg az ügyfél egy bérlő, a Bérlőazonosítója vagy bérlői elsődleges tartománynév használatával. Például **Connect-AzAccount-bérlő** fabrikam.com.

6. Társítsa az előfizetés használni kívánt fiókkal, mert egy fiók több előfizetéssel is rendelkezik.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Ha első alkalommal használja az Azure Backup, használja a **Register-AzResourceProvider** parancsmagot, hogy az Azure Recovery Services-szolgáltató regisztrálása az előfizetéshez.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Győződjön meg arról, hogy a szolgáltatók regisztrálása sikeresen befejeződött:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. A parancs kimeneténél ellenőrizze, hogy **RegistrationState** vált **regisztrált**. Ha ez nem, futtassa a **Register-AzResourceProvider** újra a parancsmagot.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

Kövesse az alábbi lépéseket egy Recovery Services-tároló létrehozása.

A Recovery Services-tároló, a Resource Manager-erőforrással, így a erőforráscsoporton belül kell elhelyeznie. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egy erőforráscsoportot a **New-AzResourceGroup** parancsmagot. Amikor létrehoz egy erőforráscsoportot, adja meg a nevét és az erőforráscsoport helyét.

1. Egy erőforráscsoport egy tároló kerül. Ha nem rendelkezik egy meglévő erőforrást, csoport, hozzon létre egy újat a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). Ebben a példában létrehozunk egy új erőforráscsoportot az USA nyugati régiójában.

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. Használja a [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) parancsmag segítségével hozza létre a tárolót. Adja meg a tároló ugyanarra a helyre, amint lett megadva az erőforráscsoport.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Adja meg a tároló tárolására a redundancia típusát.

    * Használhat [helyileg redundáns tárolás](../storage/common/storage-redundancy-lrs.md) vagy [georedundáns tárolás](../storage/common/storage-redundancy-grs.md).
    * A következő példa készletek a **- BackupStorageRedundancy** első számú megoldás a[Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperties?view=azps-1.4.0) a cmd **testvault** beállítása  **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

### <a name="view-the-vaults-in-a-subscription"></a>A tárolók megtekintheti az előfizetéshez

Az előfizetés összes tárolók megtekintéséhez használja [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

A kimenet az alábbihoz hasonlít. A társított erőforráscsoportot és helyet állnak rendelkezésre.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>A tárolási környezet beállítása

Store a tár objektumot egy változóban, és állítsa be a tárolási környezet.

* Számos Azure Backup-parancsmaghoz szükséges bemenetként, a helyreállítási tár objektumot, hogy legyen egy változóban tárolni a a tár objektumot.
* A tárolási környezet a tár által védett adatok típusa. Állítsa be a [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). A környezet beállítását követően minden további parancsmagra vonatkozik.

A következő példa állítja a tárolási környezetet a **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Beolvassa a tároló azonosítója

A tárolási környezet beállítását az Azure PowerShell irányelveinek megfelelően kivezetése tervezzük. Ehelyett tárolására vagy beolvasni a tár Azonosítóját, és adja át azt vonatkozó parancsokat, és a következő:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Biztonsági mentési szabályzat konfigurálása

A biztonsági mentési szabályzat meghatározza a biztonsági mentések ütemezését, és mennyi ideig a biztonsági mentési helyreállítási pontot kell tárolni:

* Biztonsági mentési szabályzat legalább egy adatmegőrzési házirend társítva. A megőrzési házirend határozza meg, mennyi ideig egy helyreállítási pontot a megtartani, mielőtt törölné a rendszer.
* Az alapértelmezett biztonsági mentési szabályzat megőrzési használatával nézet [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
* Az alapértelmezett biztonsági mentési szabályzat ütemezése használatával nézet [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
* Használja a [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) parancsmaggal hozzon létre egy új biztonsági mentési szabályzatot. A ütemezése és megőrzése csoportházirend-objektumok adjon meg.

A következő példában változókat az ütemezési házirend és a megőrzési házirend tárolja. Ezután használja ezeket a változókat paraméterek egy új szabályzat (**NewSQLPolicy**). **NewSQLPolicy** napi "teljes" biztonsági mentési vesz igénybe, 180 napig őrzi meg, és a napló biztonsági mentését minden 2 óráig tart

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "MSSQL"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "MSSQL"
$NewSQLPolicy = New-AzRecoveryServicesBackupProtectionPolicy -Name "NewSQLPolicy" -WorkloadType "MSSQL" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A kimenet az alábbihoz hasonlít.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                Frequency                                IsDifferentialBackup IsLogBackupEnabled
                                                                                                                                Enabled
----                 ------------       -------------------- ----------                ---------                                -------------------- ------------------
NewSQLPolicy         MSSQL              AzureWorkload        3/15/2019 9:00:00 PM      Daily                                    False                True
```

## <a name="enable-backup"></a>Biztonsági mentés engedélyezése

### <a name="registering-the-sql-vm"></a>Az SQL virtuális gép regisztrálása

Azure virtuális gép biztonsági mentéseinek és az Azure-fájlmegosztások esetén biztonsági mentési szolgáltatás ezen Azure Resource Manager-erőforrások csatlakozik, és beolvassa a releváns adatokat. Mivel SQL egy alkalmazást az Azure virtuális Gépeken, a Backup szolgáltatás kér engedélyt az alkalmazás eléréséhez, és beolvassa a szükséges adatokat. Ehhez létre kell *"regisztráció"* az Azure virtuális Gépen, amely tartalmazza az SQL-alkalmazást a Recovery services-tárolóval. Miután regisztrált egy SQL virtuális Gépet egy tárolóval, védheti meg az SQL-adatbázisok csak a tárolóba. Használat [Register-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Register-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS-parancsmaggal regisztrálhatja a virtuális Gépet.

````powershell
 $myVM = Get-AzVM -ResourceGroupName <VMRG Name> -Name <VMName>
Register-AzRecoveryServicesBackupContainer -ResourceId $myVM.ID -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetVault.ID -Force
````

A parancs visszaadja a "biztonsági mentési tároló" ezt az erőforrást, és az állapot "regisztrálva lesz"

> [!NOTE]
> Ha nem adja meg a force paramétert, felhasználónak meg kell adnia egy szöveget tartalmazó "szeretné tiltsa le a védelmet a tároló" megerősítéséhez. Hagyja figyelmen kívül ezt a szöveget, és mondja ki a "Y", győződjön meg róla. Ez egy ismert probléma, és a szöveg és a követelmény a force paraméter eltávolítása folyamatban van

### <a name="fetching-sql-dbs"></a>SQL-adatbázisok beolvasása

Miután megtörtént a regisztrációt, biztonsági mentési szolgáltatás fogja tudni listában az összes elérhető SQL összetevőt a virtuális gépen. Megtekintheti az SQL-összetevőket, de a tároló használatára kell készíteni [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS parancsmag

````powershell
Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -VaultId $targetVault.ID
````

A kimenet megjeleníti az összes védett SQL-összetevők regisztrálva ennél a tárolónál, az elem típusa és kiszolgálónév SQL virtuális gépek között. Tovább szűrheti egy adott SQL virtuális géphez átadásával a "-Container" paramétert, vagy használjon "Name" és "ServerName" együtt ItemType kombinációja jelző, amely egy egyedi SQL elem érkeznek.

````powershell
$SQLDB = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID -Name "<Item Name>" -ServerName "<Server Name>"
````

### <a name="configuring-backup"></a>A biztonsági mentés konfigurálása

Most, hogy a szükséges SQL-adatbázis és a házirendet, amelyben meg kell készíteni, használhatjuk a [engedélyezése – AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) parancsmagot, hogy az SQL DB-hez készült biztonsági mentés konfigurálása.

````powershell
Enable-AzRecoveryServicesBackupProtection -ProtectableItem $SQLDB -Policy $NewSQLPolicy
````

A parancs megvárja, amíg a konfigurálás biztonsági mentés befejeződött, és a következő kimenetet ad vissza.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 6:00:21 PM      3/18/2019 6:01:35 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="fetching-new-sql-dbs"></a>Új SQL-adatbázisok beolvasása

A számítógép regisztrálása után Backup szolgáltatás beolvassa a részleteit, majd elérhető adatbázisok. Ha a felhasználó hozzáadja az SQL-adatbázisok vagy SQL Server-példányok a regisztrált gép később, egy megjelölése szükséges manuálisan elindítani a biztonsági mentési szolgáltatás "vizsgálatot" (az újonnan hozzáadott azokat is beleértve) nem védett adatbázisok első friss végrehajtásához újra. Használja a [Initialize-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Initialize-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS parancsmagot az SQL virtuális gép egy friss lekérdezés végrehajtásához. A parancs megvárja, amíg a művelet befejeződik. A későbbiekben használhassa a [Get-AzRecoveryServicesBackupProtectableItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupProtectableItem?view=azps-1.5.0) PS parancsmagot, hogy a legújabb nem védett SQL-összetevők listájának beolvasása

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
Initialize-AzRecoveryServicesBackupProtectableItem -Container $SQLContainer -WorkloadType MSSQL -VaultId $targetvault.ID
Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLDataBase -VaultId $targetVault.ID
````

Után a megfelelő védhető elemek megszámolása, lehetővé teszi a biztonsági másolatok a útmutatása a [fenti szakasz](#configuring-backup).
Ha egy nem szeretné, hogy manuálisan észleli az új adatbázisok, azok választhatják a autoprotection leírtak [alább](#enable-autoprotection).

## <a name="enable-autoprotection"></a>AutoProtection engedélyezése

A felhasználó konfigurálhatja a biztonsági mentés, úgy, hogy a későbbiekben minden adatbázisok automatikus védelemmel vannak ellátva bizonyos szabályzattal. Engedélyezze a autoprotection az [engedélyezése – AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS-parancsmagot.

Mivel utasításait követve teheti meg az összes jövőbeli adatbázisok, a művelet egy SQLInstance biztonsági szint.

```powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Enable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -Policy $targetPolicy -VaultId $targetvault.ID
```

Miután autoprotection célja van megadva, a lekérdezés a gép beolvassa az újonnan hozzáadott, adatbázisok kerül sor az ütemezett háttérfeladatként 8 óránként.

## <a name="restore-sql-dbs"></a>SQL-adatbázisok visszaállítása

Az Azure Backup visszaállíthatja a rendszert futtató Azure virtuális gépeken a következő SQL Server-adatbázisok:

1. Állítsa vissza az adott dátum és idő (a második) tranzakciónaplók biztonsági mentését. Az Azure Backup automatikusan meghatározza, hogy a megfelelő teljes, különbségi biztonsági mentés és a kijelölt idő alapján a lánc napló biztonsági másolatok visszaállításához szükséges.
2. Egy adott teljes vagy különbözeti biztonsági másolat visszaállítása állíthatja vissza egy adott helyreállítási pontot.

Ellenőrizze az előfeltételeket az említett [Itt](restore-sql-database-azure-vm.md#prerequisites) SQL-adatbázisok visszaállítása előtt.

Először beolvassa a megfelelő biztonsági mentés az SQL DB használatával az [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS-parancsmagot.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
````

### <a name="fetch-the-relevant-restore-time"></a>Beolvassa a releváns visszaállítás ideje

Ajánlásait, a felhasználó visszaállíthatja-e a a biztonsági másolat az SQL DB egy teljes, illetve különbségi Másolás **vagy** , a napló-időponthoz.

#### <a name="fetch-distinct-recovery-points"></a>Különböző helyreállítási pontok beolvasása

Használat [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryPoint?view=azps-1.5.0) különböző (teljes, illetve különbségi) helyreállítási pontok beolvasása egy biztonsági másolat az SQL DB-hez készült.

````powershell
$startDate = (Get-Date).AddDays(-7).ToUniversalTime()
$endDate = Get-Date.ToUniversalTime()
Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -StartDate $startdate -EndDate $endDate
````

A kimenet hasonlít az alábbi példa

````powershell
RecoveryPointId    RecoveryPointType  RecoveryPointTime      ItemName                             BackupManagemen
                                                                                                  tType
---------------    -----------------  -----------------      --------                             ---------------
6660368097802      Full               3/18/2019 8:09:35 PM   MSSQLSERVER;model             AzureWorkload
````

A "RecoveryPointId" szűrő vagy egy tömb szűrő használatával beolvassa a megfelelő helyreállítási pontot.

````powershell
$FullRP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $bkpItem -VaultId $targetVault.ID -RecoveryPointId "6660368097802"
````

#### <a name="fetch-point-in-time-recovery-point"></a>Beolvassa a időpontban a helyreállítási pont

Ha a felhasználó szeretné visszaállítani az adatbázis egy bizonyos-időponthoz, [Get-AzRecoveryServicesBackupRecoveryLogChain](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupRecoveryLogChain?view=azps-1.5.0) PS-parancsmagot. A parancsmag a dátumokat, amelyek tartalmazzák az SQL biztonsági mentési elem a megszakítás nélküli, folyamatos naplólánca kezdési és befejezési idejének listáját adja vissza. A kívánt-időponthoz ebben a tartományban kell lennie.

```powershell
Get-AzRecoveryServicesBackupRecoveryLogChain -Item $bkpItem -Item -VaultId $targetVault.ID
```

A kimenet a következő példához hasonló lesz.

````powershell
ItemName                       StartTime                      EndTime
--------                       ---------                      -------
SQLDataBase;MSSQLSERVER;azu... 3/18/2019 8:09:35 PM           3/19/2019 12:08:32 PM
````

A fenti kimeneti azt jelenti, hogy a felhasználó visszaállíthatja bármely-időponthoz a megjelenített kezdési és befejezési időpontja között. (UTC) időpontok vannak. Hozhat létre bármely-időponthoz a PS, amely a fenti tartományon belül van.

> [!NOTE]
> Ha egy napló-időponthoz jelölt ki helyreállításra, felhasználói kell nem adja meg a kiindulási pontjaként azaz teljes biztonsági mentés, amelyből az adatbázis visszaállítása. Az Azure Backup szolgáltatás gondoskodik a a teljes helyreállítási terv azaz, amely teljes biztonsági mentést válassza ki, milyen biztonsági másolataihoz a alkalmazni stb.

### <a name="determine-recovery-configuration"></a>Helyreállítási konfiguráció meghatározása

Az SQL DB restore, esetén a következő visszaállítási forgatókönyvek támogatottak.

1. A biztonsági másolat SQL DB az adatokat egy másik helyreállítási pontból - OriginalWorkloadRestore felülbírálása
2. Az SQL-adatbázis visszaállítása, az azonos SQL-példány - AlternateWorkloadRestore az új adatbázis
3. Az SQL-adatbázis visszaállítása, a másik SQL-példányt egy másik SQL VM - AlternateWorkloadRestore új adatbázis

A megfelelő helyreállítási pontot beolvasása után (különálló vagy -időponthoz jelentkezzen), használja [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS-parancsmag használatával beolvassa a helyreállítási konfigurációs objektumot a kívánt helyreállítási tervnek megfelelően.

#### <a name="original-workload-restore"></a>Eredeti munkaterhelés visszaállítása

Bírálja felül a biztonsági másolatból az Adatbázist a helyreállítási pont adatait, csak adja meg a megfelelő jelzőt és a megfelelő helyreállítási pontot, ahogyan az alábbi látható példa.

##### <a name="original-restore-with-distinct-recovery-point"></a>Eredeti visszaállítási különböző helyreállítási ponttal

````powershell
$OverwriteWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -OriginalWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="original-restore-with-log-point-in-time"></a>A napló-időponthoz eredeti visszaállítása

```powershell
$OverwriteWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem  -OriginalWorkloadRestore -VaultId $targetVault.ID
```

#### <a name="alternate-workload-restore"></a>Más számítási feladatok visszaállítása

> [!IMPORTANT]
> A biztonsági másolat az SQL DB vissza tudja állítani egy másik SQLInstance csak, egy új adatbázis, egy Azure-beli virtuális gépen "regisztrálva" Ebben a tárolóban.

Ajánlásait, ha a cél SQLInstance ablakon belül egy másik Azure virtuális Gépen, győződjön meg arról, hogy [ebben a tárolóban regisztrált](#registering-the-sql-vm) és a megfelelő SQLInstance védhető elemként jelenik meg.

````powershell
$TargetInstance = Get-AzRecoveryServicesBackupProtectableItem -WorkloadType MSSQL -ItemType SQLInstance -Name "<SQLInstance Name>" -ServerName "<SQL VM name>" -VaultId $targetVault.ID
````

Ezután adja át a megfelelő helyreállítási pontot, a cél SQL-példány a megfelelő jelző alább látható módon.

##### <a name="alternate-restore-with-distinct-recovery-point"></a>Alternatív helyreállítási különböző helyreállítási ponttal

````powershell
$AnotherInstanceWithFullConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -RecoveryPoint $FullRP -TargetItem $TargetInstance -AlternateWorkloadRestore -VaultId $targetVault.ID
````

##### <a name="alternate-restore-with-log-point-in-time"></a>A napló-időponthoz alternatív visszaállítása

```powershell
$AnotherInstanceWithLogConfig = Get-AzRecoveryServicesBackupWorkloadRecoveryConfig -PointInTime $PointInTime -Item $bkpItem -AlternateWorkloadRestore -VaultId $targetVault.ID
```

Az utolsó helyreállítási pont konfigurációs objektumot kapott [Get-AzRecoveryServicesBackupWorkloadRecoveryConfig](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupWorkloadRecoveryConfig?view=azps-1.5.0) PS parancsmag a lényeges információk a visszaállításhoz, és az alább látható módon.

````powershell
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
````

Szerkesztheti a visszaállított adatbázis nevének, OverwriteWLIfpresent, NoRecoveryMode és targetPhysicalPath mezőket. További részletek beolvasása a céloldali Fájlelérési utak alább látható módon.

````powershell
$AnotherInstanceWithFullConfig.targetPhysicalPath

MappingType SourceLogicalName SourcePath                  TargetPath
----------- ----------------- ----------                  ----------
Data        azurebackup1      F:\Data\azurebackup1.mdf    F:\Data\azurebackup1_1553001753.mdf
Log         azurebackup1_log  F:\Log\azurebackup1_log.ldf F:\Log\azurebackup1_log_1553001753.ldf
````

Állítsa be a megfelelő PS tulajdonságokat, ahogy az alábbi karakterlánc-értékeket.

````powershell
$AnotherInstanceWithFullConfig.OverwriteWLIfpresent = "Yes"
$AnotherInstanceWithFullConfig | fl

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
````

> [!IMPORTANT]
> Győződjön meg arról, hogy az utolsó helyreállítási konfigurációs objektum rendelkezik-e az szükséges, és a megfelelő értékeket, mivel a visszaállítási művelet a konfigurációs objektumot fog alapulni.

### <a name="restore-with-relevant-configuration"></a>Megfelelő konfiguráció visszaállítása

Ha a megfelelő helyreállítási konfigurációs objektumot kapott és ellenőrizve van, az a [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Restore-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS parancsmagot, hogy a visszaállítási folyamat indításához.

````powershell
Restore-AzRecoveryServicesBackupItem -WLRecoveryConfig $AnotherInstanceWithLogConfig -VaultId $targetVault.ID
````

A visszaállítási műveletet egy feladatot, amely nyomon követi adja vissza.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Restore              InProgress           3/17/2019 10:02:45 AM                                3274xg2b-e4fg-5952-89b4-8cb566gc1748
````

## <a name="manage-sql-backups"></a>Az SQL biztonsági másolatainak kezelése

### <a name="on-demand-backup"></a>Igény szerinti biztonsági mentés

Biztonsági mentés engedélyezve van az egy db-hez, ha felhasználói is indíthat egy igény szerinti biztonsági mentést használó DB [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/Backup-AzRecoveryServicesBackupItem?view=azps-1.5.0) PS-parancsmagot. A következő példa elindítja egy teljes biztonsági mentés az SQL dB-ben a tömörítéssel kompatibilis, és fenn kell tartani a teljes biztonsági mentés 60 napig.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
Backup-AzRecoveryServicesBackupItem -Item $bkpItem -BackupType Full -EnableCompression -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
````

Az ad hoc ad hoc biztonsági mentési parancsot adja vissza egy feladatot, amely nyomon követi.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
MSSQLSERVER/m... Backup               InProgress           3/18/2019 8:41:27 PM                                2516bb1a-d3ef-4841-97a3-9ba455fb0637
````

Ha a kimeneti elvész, vagy ha ismerkedhet meg a megfelelő Feladatazonosító [feladatok listájának](#track-azure-backup-jobs) az Azure Backup szolgáltatást, és ezután nyomon követése, és a részletek.

### <a name="change-policy-for-backup-items"></a>Biztonsági másolati elemek szabályzatának módosítása

Felhasználó meglévő házirend módosítására, vagy módosítja a szabályzatot a biztonsági másolatban szereplő elem Házirend1 Policy2. Házirendek készül elem átváltásához egyszerűen beolvasni a megfelelő házirend elem biztonsági mentése és, amelyekkel a [engedélyezése – AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) paraméterként biztonsági másolati elem parancsot.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName>
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType MSSQL -BackupManagementType AzureWorkload -Name "<BackupItemName>"
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1
````

A parancs megvárja, amíg a konfigurálás biztonsági mentés befejeződött, és a következő kimenetet ad vissza.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
master           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>Védelem leállítása

#### <a name="retain-data"></a>Adatok megőrzése

Ha a felhasználó által blokkolni kívánt állítsa le a védelmet, használhatják a [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS-parancsmagot. Ez az ütemezett biztonsági mentések le fog állni, de felfelé mindaddig, amíg az adatok biztonsági most őrzi meg a rendszer végtelen.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureWorkload -WorkloadType MSSQL -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>biztonsági mentési adatok törlése

Annak érdekében, hogy teljesen eltávolítja a tárolt biztonsági mentési adatok a tárolóban, adja hozzá a "-jelző RemoveRecoveryPoints' kapcsolót, a ["letiltása"alkalmazásvédelmi parancs](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

#### <a name="disable-auto-protection"></a>Az automatikus védelem letiltása

Ha egy SQLInstance autoprotection lett konfigurálva, a felhasználói letilthatja a a [Disable-AzRecoveryServicesBackupAutoProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupAutoProtection?view=azps-1.5.0) PS-parancsmagot.

````powershell
$SQLInstance = Get-AzRecoveryServicesBackupProtectableItem -workloadType MSSQL -ItemType SQLInstance -VaultId $targetVault.ID -Name "<Protectable Item name>" -ServerName "<Server Name>"
Disable-AzRecoveryServicesBackupAutoProtection -InputItem $SQLInstance -BackupManagementType AzureWorkload -WorkloadType MSSQL -VaultId $targetvault.ID
````

#### <a name="unregister-sql-vm"></a>SQL virtuális gép regisztrációjának törlése

Ha egy SQL Server összes adatbázisok [azok már nem védett, és nem a biztonsági mentési adatok állnak rendelkezésre](#delete-backup-data), felhasználói regisztrációját a tárolóból az SQL virtuális gép regisztrációját is. Felhasználó csak ezután adatbázisok egy másik tárban védhető. Használat [Unregister-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/Unregister-AzRecoveryServicesBackupContainer?view=azps-1.5.0) PS parancsmagot, hogy az SQL virtuális gép regisztrációjának törlése.

````powershell
$SQLContainer = Get-AzRecoveryServicesBackupContainer -ContainerType AzureVMAppContainer -FriendlyName <VM name> -VaultId $targetvault.ID
 Unregister-AzRecoveryServicesBackupContainer -Container $SQLContainer -VaultId $targetvault.ID
````

### <a name="track-azure-backup-jobs"></a>Az Azure biztonsági mentési feladatok nyomon követése

Fontos megjegyezni, hogy az Azure Backup csak nyomon követi a felhasználó által aktivált SQL biztonsági mentési feladatok. Ütemezett biztonsági mentések (beleértve a naplóalapú biztonsági mentések) nem láthatók a portal/PowerShell használatával. Azonban, ha minden ütemezett feladat sikertelen, egy [biztonsági mentési riasztás](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) jön létre, és látható a portálon. [Az Azure Monitor használata](backup-azure-monitoring-use-azuremonitor.md) nyomon követéséhez az ütemezett feladatok és az egyéb kapcsolódó információkat.

Felhasználók nyomon követheti a JobID, a visszaadott ad hoc ad hoc és a felhasználók által aktivált műveletek a [kimeneti](#on-demand-backup) aszinkron feladatok, mint a biztonsági mentés. Használat [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJobDetails?view=azps-1.5.0) PS-parancsmag segítségével nyomon követheti a feladat és a részletek.

````powershell
 Get-AzRecoveryServicesBackupJobDetails -JobId 2516bb1a-d3ef-4841-97a3-9ba455fb0637 -VaultId $targetVault.ID
````

Az Azure Backup szolgáltatás adhoc feladatok és azok állapotát listájának lekéréséhez használja [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS-parancsmagot. Az alábbi példa a folyamatban lévő SQL feladatok adja vissza.

```powershell
Get-AzRecoveryServicesBackupJob -Status InProgress -BackupManagementType AzureWorkload
```

Egy folyamatban lévő feladat megszakítása, használja a [Stop-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/Stop-AzRecoveryServicesBackupJob?view=azps-1.5.0) PS-parancsmagot.

## <a name="managing-sql-always-on-availability-groups"></a>SQL Always a rendelkezésre állási csoportok kezelése

SQL Always On rendelkezésre állási csoportok, győződjön meg arról, hogy [regisztrálja a csomópontokon](#registering-the-sql-vm) a rendelkezésre állási csoport (rendelkezésre állási csoport). Ha az összes csomópont végzett regisztráció, egy SQL rendelkezésre állási csoport objektum logikailag létrejön védhető elemek alapján. Az adatbázisokat az SQL rendelkezésre állási csoport "SQLDatabase" fog szerepelni. A csomópontok különálló jelenik meg, és ezek az alapértelmezett SQL-adatbázisok, valamint az SQL-adatbázisok fog szerepelni.

Például tegyük fel, egy SQL rendelkezésre állási csoport két csomópont van: "sql-kiszolgáló – 0" és "sql-kiszolgáló-1", 1 SQL rendelkezésre állási csoport DB. Miután mindkét ezeknek a csomópontoknak van regisztrálva, ha a felhasználó [védhető elemek listája](#fetching-sql-dbs), a következő összetevőket tartalmazza

1. Az SQL rendelkezésre állási csoport objektum - védhető konfigurációelem-típust, SQLAvailabilityGroup
2. Egy SQL rendelkezésre állási csoport DB - védhető elem típusa, SQL Database
3. SQL-kiszolgáló-0 - SQLInstance típusa védendő elem.
4. SQL-kiszolgáló-1 - SQLInstance típusa védendő elem.
5. Bármely alapértelmezett SQL-adatbázisok (master, model, msdb) alatt az sql-kiszolgáló-0 – SQL Database típusa védhető
6. Bármely alapértelmezett SQL-adatbázisok (master, model, msdb) alatt az sql-kiszolgáló-1 – SQL Database típusa védhető

SQL-kiszolgáló – 0, az sql-kiszolgáló-1 is megjelenik "AzureVMAppContainer", amikor [biztonsági mentési tárolók felsorolt](https://docs.microsoft.com/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupContainer?view=azps-1.5.0).

Csak beolvasni a megfelelő SQL-adatbázis a [biztonsági mentés engedélyezése](#configuring-backup) és a [ad hoc biztonsági mentés](#on-demand-backup) és [PS parancsmagok visszaállítása](#restore-sql-dbs) azonosak.
