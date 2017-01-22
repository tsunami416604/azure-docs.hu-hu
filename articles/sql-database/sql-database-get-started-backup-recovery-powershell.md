---
title: "Ismerkedés az Azure SQL Database-adatbázisok biztonsági mentésével és visszaállításával az adatok védelme és helyreállítása érdekében az Azure PowerShell használatával | Microsoft Docs"
description: "Az oktatóanyag bemutatja, hogyan állíthatja vissza az adatokat automatikus biztonsági mentésekből egy adott időpontra, hogyan tárolhatja az automatikus biztonsági másolatokat az Azure helyreállítási tárban, és hogyan állíthatja vissza az Azure helyreállítási tárból azokat a PowerShell használatával"
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 12/19/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 68a4ed7aad946dda644a0f085c48fd33f453e018
ms.openlocfilehash: 15d5cb803332133c8015a8ba23ca5751b8abc29a


---


# <a name="get-started-with-backup-and-restore-for-data-protection-and-recovery-using-powershell"></a>Ismerkedés a biztonsági mentéssel és a visszaállítással az adatok védelme és helyreállítása érdekében a PowerShell használatával

Ez a kezdeti lépéseket ismertető oktatóanyag bemutatja, hogyan hajthatja végre a következő műveleteket az Azure PowerShell-lel:

- Adatbázisok meglévő biztonsági másolatainak megtekintése
- Adatbázis visszaállítása egy korábbi időpontra
- Adatbázisokról készült biztonsági mentések fájljai hosszú távú megőrzésének konfigurálása az Azure helyreállítási tárban
- Adatbázis visszaállítása az Azure helyreállítási tárból

**Becsült időtartam**: Az oktatóanyag teljesítése körülbelül 30 percet vesz igénybe (feltéve, hogy megfelel a szükséges előfeltételeknek).


## <a name="prerequisites"></a>Előfeltételek

* Rendelkeznie kell Azure-fiókkal. [Nyithat egy ingyenes Azure-fiókot](/pricing/free-trial/?WT.mc_id=A261C142F) vagy [aktiválhatja a Visual Studio előfizetői előnyeit](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). 

* Egy olyan fiókkal kell az Azure-hoz kapcsolódnia, amely tagja az előfizetés-tulajdonosi vagy a közreműködői szerepkörnek. A szerepköralapú hozzáférés-vezérlésről (RBAC) többet is megtudhat az [Azure Portalon végzett hozzáférés-vezérlés alapvető tudnivalóit ismertető](../active-directory/role-based-access-control-what-is.md) témakörben.

* Ehhez telepítenie és futtatnia kell az Azure PowerShell legújabb verzióját. Részletes információk: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

* Ön elvégezte [Az Azure SQL Database-kiszolgálók, -adatbázisok és -tűzfalszabályok Azure Portallal és az SQL Server Management Studióval történő használatának első lépései](sql-database-get-started.md) oktatóanyagot vagy az azzal egyenértékű [PowerShell-verziót](sql-database-get-started-powershell.md). Ha még nem tette, végezze el ezt az előfeltételként szolgáló oktatóanyagot, vagy hajtsa végre a [PowerShell-verzió](sql-database-get-started-powershell.md) végén található PowerShell-szkriptet a továbblépés előtt.

> [!TIP]
> Ugyanezen feladatokat az [Azure Portalon](sql-database-get-started-backup-recovery.md) is végrehajthatja egy, a kezdeti lépéseket ismertető oktatóanyagban.

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-a-database"></a>A legrégebbi visszaállítási pont megtekintése egy adatbázis szolgáltatás által létrehozott biztonsági másolatai közül

Az oktatóanyag ezen szakaszában az adatbázis [szolgáltatás által létrehozott automatikus biztonsági másolatai](sql-database-automated-backups.md) közül a legrégebbi visszaállítási pontra vonatkozó információkat tekintjük át. 

Az adatbázist bármely időpontra visszaállíthatja a legkorábbi visszaállítási pont és a legutolsó rendelkezésre álló biztonsági másolat (6 perccel az aktuális időpont előtt) között. 

A következő kódrészlet a [Get-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/get-azurermsqldatabase) parancsmag használatával lekéri a visszaállítani kívánt adatbázis legkorábbi visszaállítási pontját. A parancsmag UTC-időpontot ad vissza, a következő kódrészletekből azonban látható, hogyan használható a helyi idő. Az éles adatbázisok legutolsó rendelkezésre álló visszaállítási pontja általában az aktuális időpontnál hat perccel korábbra esik, így a legutolsó visszaállítási pont megadása egyszerűen az aktuális időpontot hat perccel megelőző időpont megadását jelenti. 

```
# Get available restore points

$resourceGroupName = "{resource-group-name}"
$serverName = "{server-name}"
$databaseName = "{database-name}"

$databaseToRestore = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

$earliestRestorePoint = $databaseToRestore.EarliestRestoreDate.ToLocalTime()
$latestRestorePoint = (Get-Date).AddMinutes(-6).ToLocalTime()

Write-Host "'$databaseName' on '$serverName' can be restored to any point-in-time between '$earliestRestorePoint' thru '$latestRestorePoint'"
```



## <a name="restore-a-database-to-a-previous-point-in-time"></a>Adatbázis visszaállítása egy korábbi időpontra

Az oktatóanyag ezen szakaszában az adatbázist egy új adatbázisba állítja vissza egy adott időpontra. 

>[!NOTE]
>Nem módosíthatja a kiszolgálót, amelyikre az adott időpontra való visszaállítást végzi. Eltérő kiszolgálóra való visszaállításhoz használja a [georedundáns visszaállítást](sql-database-disaster-recovery.md#recover-using-geo-restore). Azt is vegye figyelembe, hogy egy [rugalmas adatbáziskészletet](sql-database-elastic-jobs-overview.md) vagy másik tarifacsomagot is választhat a visszaállításhoz. 

A következő kódrészlet a [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase) parancsmag használatával visszaállítja az előző kódrészlettel beállított $databaseToRestore adatbázist. A **-PointInTime** paraméterhez a következőhöz hasonló UTC-formátumú időpont megadása szükséges: *12/09/2016 20:00:00*. A kódrészlet elvégzi az átalakítást.

```
# Restore a database to a previous point in time

#$resourceGroupName = {resource-group-name}
#$serverName = {server-name}
$newRestoredDatabaseName = "{new-database-name}"
$localTimeToRestoreTo = "{12/9/2016 12:00:00 PM}" # change to a valid restore point for your database
$restorePointInTime = (Get-Date $localTimeToRestoreTo).ToUniversalTime()
$newDatabaseEdition = "Basic"
$newDatabaseServiceLevel = "Basic"

Write-Host "Restoring database '$databaseName' to its state at:"(Get-Date $restorePointInTime).ToLocalTime()", to a new database named '$newRestoredDatabaseName'."

$restoredDb = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime $restorePointInTime -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $newRestoredDatabaseName -Edition $newDatabaseEdition -ServiceObjectiveName $newDatabaseServiceLevel `
 -ResourceId $databaseToRestore.ResourceID

$restoredDb
```

> [!NOTE]
> Innen az [SQL Server Management Studióval](https://msdn.microsoft.com/library/mt238290.aspx) csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például [egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Automatikus biztonsági mentések hosszú távú megőrzésének konfigurálása Azure helyreállítási tárban 

Az oktatóanyag ezen szakaszában [konfigurál egy Azure helyreállítási tárat az automatikus biztonsági másolatok megőrzésére](sql-database-long-term-retention.md) a szolgáltatásszint megőrzési idejénél hosszabb időtartamra (legfeljebb 10 évre). 


> [!TIP]
> A hosszú távú megőrzési időtartamú biztonsági másolatok törléséről lásd a [hosszú távú megőrzési időtartamú biztonsági mentések törlésével](sql-database-long-term-retention-delete.md) foglalkozó témakört.


### <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

> [!IMPORTANT]
> A tárolónak ugyanabban a régióban kell lennie, mint az Azure SQL logikai kiszolgálónak, és ugyanazt az erőforráscsoportot kell használnia.

A következő kódrészlet a [New-AzureRmRecoveryServicesVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/new-azurermrecoveryservicesvault) és a [Set-AzureRmRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesbackupproperties) parancsmagok használatával létrehoz egy új Recovery Services-tárolót, és beállítja a tárolóban lévő biztonsági másolatok redundanciaszintjét. 

```
# Create a recovery services vault

#$resourceGroupName = "{resource-group-name}"
#$serverName = "{server-name}"
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = "{new-vault-name}"

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $ResourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
```

### <a name="set-your-server-to-use-the-recovery-vault-you-just-created-for-its-long-term-retention-backups"></a>Kiszolgáló konfigurálása a biztonsági másolatok hosszú távú megőrzésére létrehozott helyreállítási tároló használatára

A következő kódrészlet a [Set-AzureRmSqlServerBackupLongTermRetentionVault](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqlserverbackuplongtermretentionvault) parancsmag használatával társítja a korábban létrehozott Recovery Services-tárolót egy adott Azure SQL-kiszolgálóval.

```
# Set your server to use the vault to for long-term backup retention 

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id
```

### <a name="create-a-retention-policy"></a>Adatmegőrzési házirend létrehozása

Az adatmegőrzési házirendben állítható be, hogy milyen hosszan kívánja megtartani az adatbázis biztonsági másolatát. 

A következő kódrészlet a [Get-AzureRmRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupretentionpolicyobject) parancsmag használatával lekéri az új házirendek létrehozása során sablonként alkalmazott alapértelmezett adatmegőrzési házirendet. A sablont úgy konfiguráljuk, hogy 2 évig őrizze meg a biztonsági másolatokat, majd a [New-AzureRmRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/new-azurermrecoveryservicesbackupprotectionpolicy) futtatásával végül létrehozzuk az új házirendet. 

Vegye figyelembe, hogy egyes parancsmagok futtatásához szükséges a környezet előzetes beállítása ([Set-AzureRmRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/resourcemanager/azurerm.recoveryservices/v2.3.0/set-azurermrecoveryservicesvaultcontext)), ezért ez a parancsmag feltűnik néhány kapcsolódó kódrészletben. Beállítjuk a környezetet, mivel a házirend a tároló részét képezi. Minden egyes tárolóhoz több adatmegőrzési házirendet is létrehozhat, majd az adott adatbázisokra a kívánt házirendet alkalmazhatja. 


```
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

A következő kódrészlet a [Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/set-azurermsqldatabasebackuplongtermretentionpolicy) parancsmag használatával alkalmazza az új házirendet egy adott adatbázisra.

```
# Enable long-term retention for a specific SQL database
$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id
```

> [!IMPORTANT]
> A konfigurálást követően a biztonsági másolatok hét napon belül megjelennek a tárolóban. Folytassa az oktatóanyagot, amint a biztonsági másolatok megjelentek a tárolóban.

## <a name="view-backup-info-and-backups-in-long-term-retention"></a>Biztonsági másolatok és adataik megtekintése hosszú távú megőrzés alatt

Az oktatóanyag ezen szakaszában az adatbázisok biztonsági másolataival kapcsolatos információkat tekint meg a [biztonsági másolatok hosszú távú megőrzése alatt](sql-database-long-term-retention.md). 

A következő kódrészletek információkat kérnek le a [Get-AzureRmRecoveryServicesBackupContainer](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupcontainer), [Get-AzureRmRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackupitem) és [Get-AzureRmRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.recoveryservices.backup/v2.3.0/get-azurermrecoveryservicesbackuprecoverypoint) parancsmagok használatával.

```
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


## <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Adatbázis visszaállítása hosszú távú megőrzés alatt álló biztonsági másolatból

Az oktatóanyag ezen szakaszában az adatbázist egy új adatbázisba állítja helyre egy, az Azure helyreállítási tárban elérhető biztonsági másolatból.

Ahogy az oktatóanyagban korábban bemutatott, adott időpontra visszaállító kódrészletek, úgy a hosszú távra megőrzött biztonsági másolatokból való visszaállítás is a [Restore-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/restore-azurermsqldatabase) parancsmagot használja, azzal a kivétellel, hogy a **-FromLongTermRetentionBackup** paramétert alkalmazza (a korábban használt **-FromPointInTimeBackup** paraméter helyett).

```
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
> Innen az SQL Server Management Studióval csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például [egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="complete-azure-powershell-script-to-restore-from-a-previous-point-in-time-and-to-configure-and-restore-from-long-term-backup-retention-using-powershell"></a>Teljes Azure PowerShell-szkript egy adott múltbeli időpontból való visszaállításhoz és a hosszú távra megőrzött biztonsági másolat konfigurálásához és visszaállításához PowerShell használatával

> [!NOTE]
> Ha a szkript végén a legfrissebb biztonsági másolatot próbálja visszaállítani, a rendszer egy *Null tömbbe nem lehet indexelni* kivételt ad, ha a tárolóban nem található biztonsági másolat.

```
# Sign in to Azure and set the subscription to work with
########################################################

$subscriptionId = "{subscription-id}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $subscriptionId


# User variables
################

$myResourceGroupName = "{resource-group-name}"
$myServerName = "{server-name}"
$myDatabaseToRestoreFromPointInTime = "{database-name}"
$myLocalTimeToRestoreTo = "{12/08/2016 16:00:00}" # change to a valid restore point for your database
$myNewDatabaseRestoredFromPointInTime = "{new-database-name}"

$myRecoveryServiceVaultName = "{vault-name}"
$myRetentionPolicyDurationType = "{duration-period}" # set to Years, Months, or Weeks
$myRetentionPolicyDuration = {2}
$myRetentionPolicyName = "{retention-policy-name}"
$myDatabaseToRestoreFromLTR = "{database-name}"
$myNewDatabaseRestoredFromLTR = "{new-database-name}"


# Get available restore points for a specific database
######################################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$databaseName = $myDatabaseToRestoreFromPointInTime


$databaseToRestore = Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName

$earliestRestorePoint = $databaseToRestore.EarliestRestoreDate.ToLocalTime()
$latestRestorePoint = (Get-Date).AddMinutes(-6).ToLocalTime()

Write-Host "'$databaseName' on '$serverName' can be restored to any point-in-time between '$earliestRestorePoint' thru '$latestRestorePoint'"


# Restore a database to a previous point in time
################################################

$newRestoredDatabaseName = $myNewDatabaseRestoredFromPointInTime
$localTimeToRestoreTo = $myLocalTimeToRestoreTo
$restorePointInTime = (Get-Date $localTimeToRestoreTo).ToUniversalTime()
$newDatabaseEdition = "Basic"
$newDatabaseServiceLevel = "Basic"

Write-Host "Restoring database '$databaseName' to its state at:"(Get-Date $restorePointInTime).ToLocalTime()", to a new database named '$newRestoredDatabaseName'."

$restoredDb = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime $restorePointInTime -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $newRestoredDatabaseName -Edition $newDatabaseEdition -ServiceObjectiveName $newDatabaseServiceLevel `
 -ResourceId $databaseToRestore.ResourceID

$restoredDb



# CONFIGURE LONG-TERM RETENTION

# Create a recovery services vault
##################################

$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$serverLocation = (Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroupName).Location
$recoveryServiceVaultName = $myRecoveryServiceVaultName

$vault = New-AzureRmRecoveryServicesVault -Name $recoveryServiceVaultName -ResourceGroupName $resourceGroupName -Location $serverLocation 
Set-AzureRmRecoveryServicesBackupProperties -BackupStorageRedundancy LocallyRedundant -Vault $vault
$vault

Set-AzureRmSqlServerBackupLongTermRetentionVault -ResourceGroupName $resourceGroupName -ServerName $serverName -ResourceId $vault.Id

# Retrieve the default retention policy for the AzureSQLDatabase workload type
##############################################################################

$retentionPolicy = Get-AzureRmRecoveryServicesBackupRetentionPolicyObject -WorkloadType AzureSQLDatabase

# Set the retention values
$retentionPolicy.RetentionDurationType = $myRetentionPolicyDurationType
$retentionPolicy.RetentionCount = $myRetentionPolicyDuration

$retentionPolicyName = $myRetentionPolicyName

# Set the vault context to the vault you are creating the policy for
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Create the new policy
$policy = New-AzureRmRecoveryServicesBackupProtectionPolicy -name $retentionPolicyName -WorkloadType AzureSQLDatabase -retentionPolicy $retentionPolicy
$policy

$policyState = "enabled"
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -State $policyState -ResourceId $policy.Id


$databaseNeedingRestore = $myDatabaseToRestoreFromLTR

# Set the vault context to the vault we want to restore from
Set-AzureRmRecoveryServicesVaultContext -Vault $vault

# Get the container associated with your vault
$container = Get-AzureRmRecoveryServicesBackupContainer -ContainerType AzureSQL -FriendlyName $vault.Name

# Get the long-term retention metadata associated with a specific database
$item = Get-AzureRmRecoveryServicesBackupItem -Container $container -WorkloadType AzureSQLDatabase -Name $databaseNeedingRestore


# Get all available backups for the previously indicated database
# Optionally, set the -StartDate and -EndDate parameters to return backups within a specific time period
$availableBackups = Get-AzureRmRecoveryServicesBackupRecoveryPoint -Item $item
$availableBackups
###

# This command restores the most recent backup: $availableBackups[0]
$resourceGroupName = $myResourceGroupName
$serverName = $myServerName
$restoredDatabaseName = $myNewDatabaseRestoredFromLTR
$edition = "Basic"
$performanceLevel = "Basic"

$restoredDbFromLtr = Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $availableBackups[0].Id -ResourceGroupName $resourceGroupName `
 -ServerName $serverName -TargetDatabaseName $restoredDatabaseName -Edition $edition -ServiceObjectiveName $performanceLevel

$restoredDbFromLtr
```

## <a name="next-steps"></a>Következő lépések

- A szolgáltatás által létrehozott automatikus biztonsági másolatokkal kapcsolatos további információkért lásd az [automatikus biztonsági másolatokkal](sql-database-automated-backups.md) foglalkozó témakört.
- A biztonsági másolatok hosszú távú megőrzésével kapcsolatos további információkért lásd: [biztonsági másolatok hosszú távú megőrzése](sql-database-long-term-retention.md)
- A biztonsági másolatokból való visszaállítással kapcsolatos további információkért lásd: [visszaállítás biztonsági másolatból](sql-database-recovery-using-backups.md)


<!--HONumber=Dec16_HO4-->


