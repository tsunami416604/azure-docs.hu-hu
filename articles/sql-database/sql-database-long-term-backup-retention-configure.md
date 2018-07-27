---
title: Az Azure SQL-adatbázis hosszú távú megőrzésének kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan tárolhatja az automatikus biztonsági másolatokat az SQL Azure storage-ban, és hajtsa végre a visszaállítást
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 6f0d8a3a09ce000ddff078614c4febfc44ac941f
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39264943"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Az Azure SQL-adatbázis hosszú távú megőrzésének kezelése

Konfigurálhatja az Azure SQL database egy [hosszú távú adatmegőrzés](sql-database-long-term-retention.md) házirend (LTR) automatikusan az Azure blob storage-biztonsági mentések megőrzési idejét, akár 10 évig. Ezt követően helyreállíthatja a segítségével ezeket a biztonsági másolatokat az Azure portal vagy a PowerShell használatával.

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Az Azure portal használatával konfigurálhatja a hosszú távú adatmegőrzési házirendek, és biztonsági mentések visszaállítása

A következő szakaszok bemutatják, hogyan a hosszú távú megőrzésének konfigurálása, biztonsági másolatok megtekintése hosszú távú megőrzés alatt, és a hosszú távú megőrzéséből biztonsági másolatának visszaállítása az Azure portal használatával.

### <a name="configure-long-term-retention-policies"></a>Hosszú távú adatmegőrzési szabályzatok konfigurálása

Konfigurálhatja az SQL Database [automatikus biztonsági másolatok megőrzésére](sql-database-long-term-retention.md) szolgáltatásszint megőrzési idejénél hosszabb időtartamra. 

1. Az Azure Portalon válassza ki az SQL Servert, és kattintson a **biztonsági másolatok kezelése**. Az a **szabályzatok konfigurálása** lapra, válassza ki az adatbázist, amelyen szeretné, vagy módosíthatja a hosszú távú biztonsági mentés megőrzési házirendeket.

   ![biztonsági másolatokat kapcsolat kezelése](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. Az a **szabályzatok konfigurálása** ablaktábla, válassza ki, ha szeretné, heti, havi vagy éves biztonsági mentések megőrzési idejét, és adja meg a megőrzési időszak minden. 

   ![szabályzatok konfigurálása](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Amikor végzett, kattintson a **alkalmaz**.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Biztonsági másolatok megtekintéséhez, és állítsa vissza biztonsági másolatból az Azure portal használatával

Tekintse meg a biztonsági mentések, amelyek egy adott adatbázis egy LTR-szabályzat, és ezeket a biztonsági mentéseket való visszaállítása megmaradnak. 

1. Az Azure Portalon válassza ki az SQL Servert, és kattintson a **biztonsági másolatok kezelése**. Az a **elérhető biztonsági másolatok** lapra, válassza ki az adatbázist, amelynek meg szeretné tekinteni az elérhető biztonsági másolatok.

   ![adatbázis kiválasztása](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. Az a **elérhető biztonsági másolatok** panelen tekintse át az elérhető biztonsági másolatok. 

   ![biztonsági másolatok megtekintése](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Válassza ki a biztonsági mentés, amelyből szeretné visszaállítani, és adja meg az új adatbázis nevét.

   ![visszaállítás](./media/sql-database-long-term-retention/ltr-restore.png)

5. Kattintson a **OK** állíthatja vissza az adatbázist az Azure SQL-tároló biztonsági másolatból az új adatbázisba.

6. Az eszköztáron kattintson az értesítési ikonra a visszaállítási feladat állapotának megtekintéséhez.

   ![visszaállítási feladat állapota](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Ha a visszaállítási feladat befejeződött, nyissa meg a **SQL-adatbázisok** lap használatával jeleníthetők meg az újonnan visszaállított adatbázis.

> [!NOTE]
> Innen az SQL Server Management Studióval csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például [egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>Hosszú távú adatmegőrzési szabályzatok konfigurálása és a biztonsági másolatok visszaállításához PowerShell használatával

A következő szakaszok bemutatják, hogyan konfigurálhatja a hosszú távú adatmegőrzés, a biztonsági másolatok megtekintéséhez az Azure SQL-tároló és az Azure SQL-tároló egy biztonsági másolatból való visszaállítása a PowerShell használatával.

> [!IMPORTANT]
> Az alábbi PowerShell-verziók támogatott LTR V2 API:
- [Azurerm.SQL-hez – 4.5.0](https://www.powershellgallery.com/packages/AzureRM.Sql/4.5.0) vagy újabb
- [AzureRM-6.1.0](https://www.powershellgallery.com/packages/AzureRM/6.1.0) vagy újabb
> 

### <a name="create-an-ltr-policy"></a>Az LTR-szabályzat létrehozása

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId $subId

# get the server
$server = Get-AzureRmSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetetion = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>Az LTR-szabályzatok megtekintése
Ez a példa bemutatja, hogyan kell felsorolni az LTR házirendeket egy kiszolgálón belül

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzureRmSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzureRmSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Az LTR-szabályzat törlése
Ez a példa bemutatja, hogyan az adatbázisból az LTR-házirend törlése

```powershell
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Az LTR biztonsági másolatok megtekintése

Ez a példa bemutatja, hogyan kell felsorolni az LTR biztonsági mentések egy kiszolgálón belül. 

```powershell
# Get the list of all LTR backups in a specific Azure region 
# The backups are grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest
# backup first.  
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzureRmSqlDatabaseLongTermRetentionBackup -LocationName $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>Az LTR biztonsági másolatainak törlése

Ez a példa bemutatja a törlése az LTR biztonsági mentést a biztonsági másolatok listáját.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzureRmSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

### <a name="restore-from-ltr-backups"></a>Vissza az LTR biztonsági másolataiból
Ez a példa bemutatja, hogyan az LTR biztonsági másolatból történő visszaállítását. Ne feledje, ez az interfész nem változott, de az erőforrás-azonosító paraméter mostantól csak az LTR biztonsági mentési erőforrás-azonosítója. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> Itt a visszaállított adatbázis szükséges feladatok végrehajtásához az SQL Server Management Studio használatával csatlakozhat, például egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba történő másolásához, vagy törölje a meglévő adatbázis és a visszaállított átnevezése az adatbázis a meglévő adatbázis nevére. Lásd: [időponthoz kötött visszaállítás pont](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>További lépések

- A szolgáltatás által létrehozott automatikus biztonsági másolatokkal kapcsolatos további információkért lásd az [automatikus biztonsági másolatokkal](sql-database-automated-backups.md) foglalkozó témakört.
- A biztonsági másolatok hosszú távú megőrzésével kapcsolatos további információkért lásd: [biztonsági másolatok hosszú távú megőrzése](sql-database-long-term-retention.md)
