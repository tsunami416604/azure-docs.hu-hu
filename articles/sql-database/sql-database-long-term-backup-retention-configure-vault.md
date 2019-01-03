---
title: A biztonsági másolatok hosszú távú – Azure SQL Database konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan tárolhatja az automatikus biztonsági másolatokat az Azure Recovery Services-tároló és az Azure Recovery Services-tároló visszaállítása
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma,carlrab
manager: craigg
ms.date: 10/05/2018
ms.openlocfilehash: d8ff07d43d1efbadf1ddd397b6690c93c54c4f27
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53603548"
---
# <a name="configure-long-term-backup-retention-using-azure-recovery-services-vault"></a>Az Azure Recovery Services-tároló használatával hosszú távú megőrzésének konfigurálása

Konfigurálhatja az Azure SQL database biztonsági másolatainak tárolására, és majd a használatával őrzi meg az Azure portal vagy a PowerShell használatával a tárban lévő biztonsági másolatok adatbázis helyreállítása az Azure Recovery Services-tároló.

> [!NOTE]
> Részeként előzetes verziójának 2016. október a hosszú távú megőrzésének kezdeti verziójában a biztonsági mentések mentette az Azure-szolgáltatások Recovery Services-tárolóra. Ez a frissítés eltávolítja ezt a függőséget, de a visszamenőleges kompatibilitás érdekében az eredeti API támogatja a 2018. május 31-ig. Ha az Azure-szolgáltatások Recovery-tárolóban lévő biztonsági másolatok kezelése van szüksége, tekintse meg [Azure szolgáltatások Recovery Services-tároló használatával hosszú távú adatmegőrzés](sql-database-long-term-backup-retention-configure-vault.md).

## <a name="azure-portal"></a>Azure Portal

A következő szakaszok bemutatják, hogyan használhatja az Azure Portalon az Azure Recovery Services-tároló konfigurálása biztonsági másolatok megtekintéséhez a tároló és a tárolóból a visszaállítási.

### <a name="configure-the-vault-register-the-server-and-select-databases"></a>A tároló konfigurálása, regisztrálja a kiszolgálót, és válassza ki az adatbázisok

Az Azure Recovery Services-tároló konfigurálása [automatikus biztonsági másolatok megőrzésére](sql-database-long-term-retention.md) szolgáltatásszint megőrzési idejénél hosszabb időtartamra.

1. Nyissa meg a **SQL Server** a kiszolgálóhoz tartozó lapon.

   ![az SQL server lap](./media/sql-database-get-started-portal/sql-server-blade.png)

2. Kattintson a **Biztonsági másolat hosszú távú megőrzése** elemre.

   ![biztonsági másolat hosszú távú megőrzése hivatkozás](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. Az a **hosszú távú adatmegőrzés** a kiszolgáló lapon tekintse át és fogadja el az előzetes verziójú szolgáltatás feltételeit (kivéve, ha Ön már megtette –, vagy ez a funkció már nem előzetes verzióban érhető el).

   ![előzetes verziójú szolgáltatás feltételeinek elfogadása](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Hosszú távú megőrzés konfigurálásához válassza ki, hogy az adatbázis a rácsban, és kattintson a **konfigurálása** az eszköztáron.

   ![adatbázis kijelölése biztonsági mentés hosszú távú megőrzéséhez](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. Az a **konfigurálása** kattintson **kötelező beállítások konfigurálása** alatt **Recovery Services-tároló**.

   ![tároló konfigurálása hivatkozás](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. Az a **Recovery services-tároló** lapon, válassza ki egy meglévő tárolót, ha van ilyen. Ha nem találhatók helyreállítási tárak az előfizetésben, kattintással lépjen ki a folyamatból, és hozzon létre egyet.

   ![tároló hivatkozás létrehozása](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. Az a **Recovery Services-tárolók** kattintson **Hozzáadás**.

   ![tároló hivatkozás hozzáadása](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)

8. Az a **Recovery Services-tároló** területén adjon meg egy érvényes nevet a Recovery Services-tároló.

   ![új tároló neve](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Válassza ki az előfizetést és az erőforráscsoportot, majd válassza ki a tároló helyét. Ha befejezte, kattintson a **Létrehozás** elemre.

   ![tároló létrehozása](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > A tárolónak ugyanabban a régióban kell lennie, mint az Azure SQL logikai kiszolgálónak, és ugyanazt az erőforráscsoportot kell használnia.

10. Az új tároló létrehozása után hajtsa végre a szükséges lépéseket, térjen vissza a **Recovery services-tároló** lapot.

11. Az a **Recovery services-tároló** lapon, kattintson a tárolóra, majd kattintson **kiválasztása**.

   ![meglévő tároló kiválasztása](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. Az a **konfigurálása** lapon adjon meg egy érvényes nevet az új adatmegőrzési házirend, az alapértelmezett házirendet szükség szerint módosíthatja, és kattintson a **OK**.

   ![adatmegőrzési házirend definiálása](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

   > [!NOTE]
   > Adatmegőrzési szabályzat neve nem engedélyezi bizonyos karaktereket, szóközöket is beleértve.

13. Az a **hosszú távú adatmegőrzés** az adatbázishoz tartozó lap, kattintson **mentése** majd **OK** a alkalmazni a hosszú távú adatmegőrzési házirend a kiválasztott adatbázisokra.

   ![adatmegőrzési házirend definiálása](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Kattintson a **Mentés** gombra a hosszú távú adatmegőrzés engedélyezéséhez az új házirend használatával a konfigurált Azure helyreállítási táron.

   ![adatmegőrzési házirend definiálása](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

> [!IMPORTANT]
> A konfigurálást követően a biztonsági másolatok hét napon belül megjelennek a tárolóban. Ne folytassa az oktatóanyagot, amíg a biztonsági másolatok meg nem jelentek a tárolóban.

### <a name="view-backups-in-long-term-retention-using-azure-portal"></a>Biztonsági másolatok megtekintése hosszú távú megőrzés alatt az Azure portal használatával

Az adatbázisok biztonsági másolatai a kapcsolatos információk megtekintéséhez [hosszú távú adatmegőrzés](sql-database-long-term-retention.md).

1. Az Azure Portalon nyissa meg az Azure Recovery Services-tárolót az adatbázisok biztonsági másolatai (lépjen a **összes erőforrás** , és jelölje ki a listából az előfizetéshez tartozó erőforrások) megtekintéséhez a az adatbázis biztonsági mentések által felhasznált tárterület mennyisége a tároló.

   ![biztonsági másolatokat tartalmazó helyreállítási tár megtekintése](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Nyissa meg a **SQL-adatbázis** az adatbázishoz tartozó lap.

   ![Új minta db oldal](./media/sql-database-get-started-portal/new-sample-db-blade.png)

3. Az eszköztáron kattintson a **Visszaállítás** elemre.

   ![visszaállítási eszköztár](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. A visszaállítási lapon kattintson a **hosszú távú**.

5. Az Azure-tárolók biztonsági másolatai alatt kattintson a **Biztonsági másolat kiválasztása** gombra az adatbázisok a biztonsági másolatok hosszú távú megőrzése alatt álló biztonsági másolatainak megtekintéséhez.

   ![biztonsági másolatok a tárolóban](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

### <a name="restore-a-database-from-a-backup-in-long-term-backup-retention-using-the-azure-portal"></a>Adatbázis visszaállítása biztonsági másolatból a hosszú távú megőrzés az Azure portal használatával

Az adatbázis egy új adatbázisra az Azure helyreállítási tárban lévő biztonsági másolatból állítsa vissza.

1. Az a **az Azure-tárolók biztonsági másolatai** lap, kattintson a biztonsági mentés, visszaállítás, és kattintson **kiválasztása**.

   ![tárolóban lévő biztonsági másolat kiválasztása](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. Az **Adatbázis neve** szövegmezőben adjon meg egy nevet a visszaállított adatbázis számára.

   ![új adatbázis neve](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Kattintson az **OK** gombra az adatbázis visszaállításához a tárolóban lévő biztonsági másolatból az új adatbázisba.

4. Az eszköztáron kattintson az értesítési ikonra a visszaállítási feladat állapotának megtekintéséhez.

   ![tárolóból való visszaállítási feladat állapota](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Ha a visszaállítási feladat befejeződött, nyissa meg a **SQL-adatbázisok** lap használatával jeleníthetők meg az újonnan visszaállított adatbázis.

   ![tárolóból visszaállított adatbázis](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!NOTE]
> Innen az SQL Server Management Studióval csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például [egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="powershell"></a>PowerShell

A következő szakaszok bemutatják, hogyan konfigurálhatja az Azure Recovery Services-tároló, biztonsági másolatok megtekintéséhez a tároló és a tárolóban való visszaállítása a PowerShell használatával.

### <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

Használja a [New-AzureRmRecoveryServicesVault](/powershell/module/azurerm.recoveryservices/new-azurermrecoveryservicesvault) hozhat létre egy recovery services-tárolót.

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

### <a name="set-your-server-to-use-the-recovery-vault-for-its-long-term-retention-backups"></a>A helyreállítási tár használata a hosszú távú megőrzési időtartamú biztonsági mentések kiszolgáló

Használja a [Set-AzureRmSqlServerBackupLongTermRetentionVault](/powershell/module/azurerm.sql/set-azurermsqlserverbackuplongtermretentionvault) parancsmag használatával társítja a korábban létrehozott recovery services-tároló egy adott Azure SQL-kiszolgálóval.

```PowerShell
# Set your server to use the vault to for long-term backup retention

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Adatmegőrzési házirend létrehozása

Az adatmegőrzési házirendben állítható be, hogy milyen hosszan kívánja megtartani az adatbázis biztonsági másolatát. Használja a [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup/get-azurermrecoveryservicesbackupretentionpolicyobject) -parancsmaggal beolvasható az alapértelmezett adatmegőrzési szabályzatok létrehozására szolgáló sablonként használni. Ez a sablon a megőrzési időszak 2 év van beállítva. Ezután futtassa a [New-AzureRmRecoveryServicesBackupProtectionPolicy](/powershell/module/azurerm.recoveryservices.backup/new-azurermrecoveryservicesbackupprotectionpolicy) végül létrehozzuk a szabályzatot.

> [!NOTE]
> Egyes parancsmagok futtatásához szükséges, hogy beállította-e a tárolási környezet futtatása előtt ([Set-AzureRmRecoveryServicesVaultContext](/powershell/module/azurerm.recoveryservices/set-azurermrecoveryservicesvaultcontext)), ez a parancsmag feltűnik néhány kapcsolódó kódrészletben láthatja. A környezet be kell állítani, mert a házirend a tároló részét képezi. Minden egyes tárolóhoz több adatmegőrzési házirendet is létrehozhat, majd az adott adatbázisokra a kívánt házirendet alkalmazhatja.

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

Használja a [Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy](/powershell/module/azurerm.sql/set-azurermsqldatabasebackuplongtermretentionpolicy) parancsmag használatával alkalmazza az új házirendet egy adott adatbázisban.

```PowerShell
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

### <a name="view-backup-info-and-backups-in-long-term-retention"></a>Biztonsági másolatok és adataik megtekintése hosszú távú megőrzés alatt

Az adatbázisok biztonsági másolatai a kapcsolatos információk megtekintéséhez [hosszú távú adatmegőrzés](sql-database-long-term-retention.md).

Biztonsági mentési adatok megtekintéséhez használja a következő parancsmagokat:

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

Használja a biztonsági másolat hosszú távú megőrzéséből visszaállítása a [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) parancsmagot.

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
> Itt a visszaállított adatbázis szükséges feladatok végrehajtásához az SQL Server Management Studio használatával csatlakozhat, például egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba történő másolásához, vagy törölje a meglévő adatbázis és a visszaállított átnevezése az adatbázis a meglévő adatbázis nevére. Lásd: [időponthoz kötött visszaállítás pont](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="how-to-cleanup-backups-in-recovery-services-vault"></a>Hogyan karbantartása biztonsági mentés a Recovery Services-tárolóban

2018. július 1. az LTR V1 API már elavult, és minden a meglévő biztonsági másolatok a helyreállítási szolgáltatás tárolók át lettek telepítve az SQL Database által kezelt LTR storage tárolók. Győződjön meg arról, hogy már nem kell az eredeti biztonsági mentéseket, hogy azok lettek távolítva a tárolók az áttelepítés után. Azonban ha a tároló helyezi el a zárolást a biztonsági mentések marad. A felesleges költségek elkerülése érdekében azt is manuálisan távolítsa el a régi biztonsági másolatok a Recovery Services-tárolót, a következő parancsfájl használatával.

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
