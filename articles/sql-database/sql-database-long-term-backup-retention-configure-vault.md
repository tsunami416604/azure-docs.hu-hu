---
title: Hosszú távú biztonsági másolatok megőrzésének - Azure SQL adatbázis konfigurálása |} Microsoft Docs
description: Útmutató az Azure Recovery Services-tároló az automatikus biztonsági másolatok tárolására, vagy visszaállíthatja őket az Azure Recovery Services-tároló
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 05/08/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: feefe68fbe6681ee4b450503606ac8c4f25d5a39
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130260"
---
# <a name="configure-and-restore-from-azure-sql-database-long-term-backup-retention-using-azure-recovery-services-vault"></a>Konfigurálja, és állítsa vissza az Azure SQL Database hosszú távú biztonsági másolatok megőrzésének Azure Recovery Services-tároló használata

Konfigurálhatja az Azure Recovery Services-tároló, majd a biztonsági mentések őrzi meg a tárolónál az Azure portálon vagy a PowerShell használatával adatbázis helyreállítása és az Azure SQL database biztonsági másolatok tárolására.

> [!NOTE]
> Hosszú távú biztonsági másolatok megőrzésének október 2016 előnézete eredeti kiadását részeként volt tárolja a biztonsági másolatokat az Azure helyreállítási szolgáltatások tárolóban lévő állapottal. A frissítés eltávolítja a függőséget, de a visszamenőleges kompatibilitás érdekében az eredeti API támogatott 2018 előfordulhat, hogy 31-ig. Ha az Azure szolgáltatások Recovery-tárolóban a biztonsági másolatok együttműködhet szüksége, tekintse meg [Azure helyreállítási szolgáltatások tárolót használó hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-backup-retention-configure-vault.md). 


## <a name="azure-portal"></a>Azure Portal

A következő szakaszok bemutatják, hogyan használható az Azure-portál konfigurálása az Azure Recovery Services-tároló, biztonsági másolatok megtekintése a tároló és a tárolóban való visszaállítása.

### <a name="configure-the-vault-register-the-server-and-select-databases"></a>A tároló konfigurálása, regisztrálja a kiszolgálót, és válassza ki az adatbázisok

Konfigurálja az Azure Recovery Services-tárolónak a [automatikus biztonsági mentések megőrzési](sql-database-long-term-retention.md) a szolgáltatási rétegben megőrzési időtartama hosszabb időtartamra. 

1. Nyissa meg a **SQL Server** a kiszolgálóhoz tartozó lapon.

   ![SQL server lap](./media/sql-database-get-started-portal/sql-server-blade.png)

2. Kattintson a **Biztonsági másolat hosszú távú megőrzése** elemre.

   ![biztonsági másolat hosszú távú megőrzése hivatkozás](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. Az a **hosszú távú biztonsági másolatok megőrzésének** a kiszolgáló lapon tekintse át és fogadja el az előzetes feltételeket (kivéve, ha Ön már megtette -, vagy ez a szolgáltatás már nincs az előzetes verzió).

   ![előzetes verziójú szolgáltatás feltételeinek elfogadása](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Hosszú távú biztonsági másolatok megőrzésének konfigurálásához jelölje ki, hogy az adatbázis a rácsban, és kattintson **konfigurálása** az eszköztáron.

   ![adatbázis kijelölése biztonsági mentés hosszú távú megőrzéséhez](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. Az a **konfigurálása** kattintson **kötelező beállítások konfigurálása** alatt **helyreállítási szolgáltatás tároló**.

   ![tároló konfigurálása hivatkozás](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. Az a **Recovery services-tároló** lapon, válassza ki egy meglévő tárolóban, ha van ilyen. Ha nem találhatók helyreállítási tárak az előfizetésben, kattintással lépjen ki a folyamatból, és hozzon létre egyet.

   ![tároló hivatkozás létrehozása](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. Az a **Recovery Services-tárolók** kattintson **Hozzáadás**.

   ![tároló hivatkozás hozzáadása](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. Az a **Recovery Services-tároló** lapján adjon meg egy érvényes nevet a Recovery Services-tároló.

   ![új tároló neve](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Válassza ki az előfizetést és az erőforráscsoportot, majd válassza ki a tároló helyét. Ha befejezte, kattintson a **Létrehozás** elemre.

   ![tároló létrehozása](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > A tárolónak ugyanabban a régióban kell lennie, mint az Azure SQL logikai kiszolgálónak, és ugyanazt az erőforráscsoportot kell használnia.
   >

10. Az új tároló létrehozása után hajtható végre a szükséges lépéseket térjen vissza a **Recovery services-tároló** lap.

11. Az a **Recovery services-tároló** lapon, kattintson arra a tárolóra, és kattintson **válasszon**.

   ![meglévő tároló kiválasztása](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. Az a **konfigurálása** lapon adjon meg egy érvényes nevet az új megőrzési házirend, az alapértelmezett megőrzési házirendet szükség szerint módosíthatja, és kattintson a **OK**.

   ![adatmegőrzési házirend definiálása](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)
   
   >[!NOTE]
   >Adatmegőrzési házirend nevek tiltása bizonyos karaktereket, szóközöket is beleértve.

13. Az a **hosszú távú biztonsági másolatok megőrzésének** az adatbázis lapján kattintson **mentése** majd **OK** a hosszú távú biztonsági mentés megőrzési házirend alkalmazása az összes kijelölt adatbázisok.

   ![adatmegőrzési házirend definiálása](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Kattintson a **Mentés** gombra a hosszú távú adatmegőrzés engedélyezéséhez az új házirend használatával a konfigurált Azure helyreállítási táron.

   ![adatmegőrzési házirend definiálása](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

> [!IMPORTANT]
> A konfigurálást követően a biztonsági másolatok hét napon belül megjelennek a tárolóban. Ne folytassa az oktatóanyagot, amíg a biztonsági másolatok meg nem jelentek a tárolóban.
>

### <a name="view-backups-in-long-term-retention-using-azure-portal"></a>Az Azure-portált használja, a hosszú távú megőrzési biztonsági másolatok megtekintése

Tekintse meg az adatbázis a biztonsági másolatok [hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-retention.md). 

1. Az Azure-portálon, nyissa meg az Azure Recovery Services-tároló az adatbázis biztonsági mentés (lépjen **összes erőforrás** , és válassza ki azt a listából az előfizetéshez tartozó erőforrások) megtekintéséhez használja az adatbázis biztonsági másolatait a tárolókapacitást a tároló.

   ![biztonsági másolatokat tartalmazó helyreállítási tár megtekintése](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Nyissa meg a **SQL-adatbázis** lap az adatbázis számára.

   ![Új minta db lap](./media/sql-database-get-started-portal/new-sample-db-blade.png)

3. Az eszköztáron kattintson a **Visszaállítás** elemre.

   ![visszaállítási eszköztár](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. Kattintson a visszaállítás lap **hosszú távú**.

5. Az Azure-tárolók biztonsági másolatai alatt kattintson a **Biztonsági másolat kiválasztása** gombra az adatbázisok a biztonsági másolatok hosszú távú megőrzése alatt álló biztonsági másolatainak megtekintéséhez.

   ![biztonsági másolatok a tárolóban](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention-using-the-azure-portal"></a>Állítson vissza egy adatbázist egy biztonsági másolatból, a hosszú távú biztonsági másolatok megőrzésének az Azure portál használatával

Az adatbázis egy új adatbázist visszaállíthatja egy biztonsági másolatból, az Azure Recovery Services-tárolóban.

1. Az a **tárolóban az Azure biztonsági mentések** a biztonsági másolat visszaállítása, majd kattintson **válasszon**.

   ![tárolóban lévő biztonsági másolat kiválasztása](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. Az **Adatbázis neve** szövegmezőben adjon meg egy nevet a visszaállított adatbázis számára.

   ![új adatbázis neve](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Kattintson az **OK** gombra az adatbázis visszaállításához a tárolóban lévő biztonsági másolatból az új adatbázisba.

4. Az eszköztáron kattintson az értesítési ikonra a visszaállítási feladat állapotának megtekintéséhez.

   ![tárolóból való visszaállítási feladat állapota](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. A visszaállítási feladat befejezése után nyissa meg a **SQL-adatbázisok** lap használatával jeleníthetők meg az újonnan visszaállított adatbázis.

   ![tárolóból visszaállított adatbázis](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!NOTE]
> Innen az SQL Server Management Studióval csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például [egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="powershell"></a>PowerShell

Az alábbi szakaszok bemutatják a PowerShell használatával konfigurálja az Azure Recovery Services-tároló, biztonsági másolatok megtekintése a tároló és a visszaállítási a tárolóból.

### <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

Használja a [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault) a recovery services-tároló létrehozásához.

> [!IMPORTANT]
> A tárolónak ugyanabban a régióban kell lennie, mint az Azure SQL logikai kiszolgálónak, és ugyanazt az erőforráscsoportot kell használnia.

```PowerShell
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-for-its-long-term-retention-backups"></a>A kiszolgáló, a hosszú távú megőrzési biztonsági mentést a recovery-tároló használandó beállítása

Használja a [Set-AzureRmSqlServerBackupLongTermRetentionVault](/powershell/module/azurerm.sql/set-azurermsqlserverbackuplongtermretentionvault) parancsmag használatával rendelje hozzá a korábban létrehozott recovery services-tároló egy adott Azure SQL-kiszolgálóra.

```PowerShell
# Set your server to use the vault to for long-term backup retention 

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Adatmegőrzési házirend létrehozása

Az adatmegőrzési házirendben állítható be, hogy milyen hosszan kívánja megtartani az adatbázis biztonsági másolatát. Használja a [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupretentionpolicyobject) parancsmagot, hogy megkapja az alapértelmezett megőrzési házirend szabályzatok létrehozására a sablont használni. A sablonban a megőrzési időtartam 2 év van beállítva. Ezután futtassa a [New-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy) végül a szabályzat létrehozásához. 

> [!NOTE]
> Néhány parancsmaggal szükséges, hogy megadta-e a tárolóban helyi futtatása előtt ([Set-AzureRmRecoveryServicesVaultContext](/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)), ez a parancsmag a néhány kapcsolódó kódtöredékek látja. A környezetben, be, mert a házirend a tároló része. Minden egyes tárolóhoz több adatmegőrzési házirendet is létrehozhat, majd az adott adatbázisokra a kívánt házirendet alkalmazhatja. 


```PowerShell
# Retrieve the default retention policy for the AzureSQLDatabase workload type
$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention value to two years (you can set to any time between 1 week and 10 years)
$retentionPolicy.RetentionDurationType = "Years"
$retentionPolicy.RetentionCount = 2
$retentionPolicyName = "my2YearRetentionPolicy"

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy
```

### <a name="configure-a-database-to-use-the-previously-defined-retention-policy"></a>Adatbázis konfigurálása a korábban meghatározott adatmegőrzési házirend használatára

Használja a [Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy](/powershell/module/azurerm.sql/set-azurermsqldatabasebackuplongtermretentionpolicy) parancsmagot, hogy az új házirend vonatkozik egy adott adatbázist.

```PowerShell
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

### <a name="view-backup-info-and-backups-in-long-term-retention"></a>Biztonsági másolatok és adataik megtekintése hosszú távú megőrzés alatt

Tekintse meg az adatbázis a biztonsági másolatok [hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-retention.md). 

Biztonsági információk megtekintéséhez használja a következő parancsmagokat:

- [Get-AzureRmRecoveryServicesBackupContainer](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupcontainer)
- [Get-AzureRmRecoveryServicesBackupItem](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupitem)
- [Get-AzureRmRecoveryServicesBackupRecoveryPoint](/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackuprecoverypoint)

```PowerShell
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$databaseNeedingRestore = $databaseName

# Set the vault context to the vault we want to restore from
#$vault = Get-AzureRmRecoveryServicesVault -ResourceGroupName $resourceGroupName
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# the following commands find the container associated with the server 'myserver' under resource group 'myresourcegroup'
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore

# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
```

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Adatbázis visszaállítása hosszú távú megőrzés alatt álló biztonsági másolatból

A hosszú távú biztonsági mentés megőrzési visszaállítás használja a [visszaállítási-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) parancsmag.

```PowerShell
# Restore the most recent backup: $availableBackups[0]
#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$restoredDatabaseName = "{new-database-name}"
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDb = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel
$restoredDb
```


> [!NOTE]
> Itt csatlakozhat a visszaállított adatbázis SQL Server Management Studio használatával szükséges feladatok végrehajtásához, például egy bit, az adatok kinyerése a visszaállított adatbázis másolja a már meglévő adatbázist, vagy törölje a meglévő adatbázis, és nevezze át a visszaállított a meglévő adatbázis nevének adatbázist. Lásd: [időponthoz kötött visszaállításra](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="how-to-cleanup-backups-in-recovery-services-vault"></a>Recovery Services-tároló törlése a biztonsági másolatok hogyan

2018. július 1-től a balról jobbra V1 API már elavult, és minden a meglévő biztonsági másolatok a helyreállítási szolgáltatás tárolók áttelepítette a balról jobbra a tároló SQL-adatbázis kezeli. Győződjön meg arról, hogy Ön már nem az eredeti biztonsági mentés van szó, hogy azok lettek távolítva a tárolók áttelepítés után. Azonban ha egy zárolás helyezve a tároló a biztonsági mentések marad van. A felesleges költségek elkerülése érdekében manuálisan eltávolíthatja a régi biztonsági másolatok a helyreállítási szolgáltatás tárolóból az alábbi parancsfájl használatával. 

```PowerShell
<#
.EXAMPLE
    .\Drop-LtrV1Backup.ps1 -SubscriptionId “{vault_sub_id}” -ResourceGroup “{vault_resource_group}” -VaultName “{vault_name}” 
#>
[CmdletBinding()]
Param (
    [Parameter(Mandatory = $true, HelpMessage="The vault subscription ID")]
    $SubscriptionId,

    [Parameter(Mandatory = $true, HelpMessage="The vault resource group name")]
    $ResourceGroup,

    [Parameter(Mandatory = $true, HelpMessage="The vault name")]
    $VaultName
)

Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $SubscriptionId

$vaults = Get-AzureRmRecoveryServicesVault
$vault = $vaults | where { $_.Name -eq $VaultName }

Set-AzureRmRecoveryServicesVaultContext -Vault $vault

$containers = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL

ForEach ($container in $containers)
{
   $canDeleteContainer = $true  
   $ItemCount = 0
   Write-Host "Working on container" $container.Name
   $items = Get-AzureRmRecoveryServicesBackupItem -container $container -WorkloadType AzureSQLDatabase
   ForEach ($item in $items)
   {
          write-host "Deleting item" $item.name
          Disable-AzureRmRecoveryServicesBackupProtection -RemoveRecoveryPoints -item $item -Force
   }

   Write-Host "Deleting container" $container.Name
   Unregister-AzureRmRecoveryServicesBackupContainer -Container $container
}
```

## <a name="next-steps"></a>További lépések

- A szolgáltatás által létrehozott automatikus biztonsági másolatokkal kapcsolatos további információkért lásd az [automatikus biztonsági másolatokkal](sql-database-automated-backups.md) foglalkozó témakört.
- A biztonsági másolatok hosszú távú megőrzésével kapcsolatos további információkért lásd: [biztonsági másolatok hosszú távú megőrzése](sql-database-long-term-retention.md)
- A biztonsági másolatokból való visszaállítással kapcsolatos további információkért lásd: [visszaállítás biztonsági másolatból](sql-database-recovery-using-backups.md)
