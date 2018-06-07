---
title: Az Azure SQL Database hosszú távú biztonsági másolatok megőrzésének kezelése |} Microsoft Docs
description: 'Útmutató: az automatikus biztonsági mentés az SQL Azure storage tárolja, és hajtsa végre a visszaállítást'
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 0ce22cae50e70ca7232e025d4009b23d62f6a198
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649227"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Az Azure SQL Database hosszú távú biztonsági másolatok megőrzésének kezelése

Konfigurálhatja az Azure SQL-adatbázis egy [hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-retention.md) (LTR) automatikusan legfeljebb tíz éve a az Azure blob storage biztonsági mentések megőrzési házirend. Ezt követően helyreállíthatja a biztonsági mentése az Azure-portálon vagy a PowerShell használatával egy adatbázist.

> [!NOTE]
> Ez a szolgáltatás a 2016. októberi előnézete eredeti kiadását részeként volt tárolja a biztonsági másolatokat az Azure helyreállítási szolgáltatások tárolóban lévő állapottal. A frissítés eltávolítja a függőséget, de a visszamenőleges kompatibilitás érdekében az eredeti API támogatott 2018 előfordulhat, hogy 31-ig. Ha az Azure szolgáltatások Recovery-tárolóban a biztonsági másolatok együttműködhet szüksége, tekintse meg [Azure helyreállítási szolgáltatások tárolót használó hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-backup-retention-configure-vault.md). 

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>Az Azure portál segítségével hosszú távú adatmegőrzési házirendek konfigurálása és a biztonsági másolatok

A következő szakaszok bemutatják a hosszú távú megőrzési konfigurálásához, tekintse meg a biztonsági másolatok hosszú távú megőrzési, és a hosszú távú megőrzési biztonsági másolat visszaállítása az Azure-portál használatával.

### <a name="configure-long-term-retention-policies"></a>Hosszú távú adatmegőrzési konfigurálása

Konfigurálhatja az SQL-adatbázis [automatikus biztonsági mentések megőrzési](sql-database-long-term-retention.md) a szolgáltatási rétegben megőrzési időtartama hosszabb időtartamra. 

1. Az Azure portálon, válassza ki az SQL server, és kattintson a **hosszú távú biztonsági másolatok megőrzésének**.

   ![biztonsági másolat hosszú távú megőrzése hivatkozás](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. Az a **-szabályzatok konfigurálása** lapra, válassza ki az adatbázist, amelyeken beállítása vagy módosítása a hosszú távú biztonsági mentési adatmegőrzési kívánja.

   ![Adatbázis kiválasztása](./media/sql-database-long-term-retention/ltr-configure-select-database.png)

3. Az a **-szabályzatok konfigurálása** ablaktábla, válassza ki, ha heti, havi vagy éves biztonsági mentések megőrzési, és adja meg az egyes megőrzési időtartama. 

   ![házirendek konfigurálása](./media/sql-database-long-term-retention/ltr-configure-policies.png)

4. Amikor végzett, kattintson **alkalmaz**.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Biztonsági másolatok megtekintése, és állítsa vissza egy biztonsági másolatból, Azure-portál használatával

Tekintse meg a biztonsági mentések, amelyek egy adott adatbázis egy balról jobbra házirend, és azokat a biztonsági mentések való visszaállítása megmaradnak. 

1. Az Azure portálon, válassza ki az SQL server, és kattintson a **hosszú távú biztonsági másolatok megőrzésének**.

   ![biztonsági másolat hosszú távú megőrzése hivatkozás](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. Az a **elérhető biztonsági másolatok** lapra, válassza ki az adatbázist, amelynek meg szeretné tekinteni a rendelkezésre álló biztonsági mentéseket.

   ![Adatbázis kiválasztása](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. Az a **elérhető biztonsági másolatok** ablaktáblában tekintse át az elérhető biztonsági másolatokat. 

   ![biztonsági másolatok megtekintése](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Válassza ki a visszaállítani kívánt biztonsági másolatot, és adja meg az új adatbázis nevét.

   ![visszaállítás](./media/sql-database-long-term-retention/ltr-restore.png)

5. Kattintson a **OK** az adatbázis biztonsági másolatból történő visszaállítását a az Azure SQL-tárolót az új adatbázishoz.

6. Az eszköztáron kattintson az értesítési ikonra a visszaállítási feladat állapotának megtekintéséhez.

   ![tárolóból való visszaállítási feladat állapota](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. A visszaállítási feladat befejezése után nyissa meg a **SQL-adatbázisok** lap használatával jeleníthetők meg az újonnan visszaállított adatbázis.

> [!NOTE]
> Innen az SQL Server Management Studióval csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például [egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>Hosszú távú adatmegőrzési házirendek konfigurálása és a biztonsági másolatok a PowerShell használatával

Az alábbi szakaszok bemutatják a PowerShell segítségével konfigurálhatja a hosszú távú biztonsági másolatok megőrzésének, és tekintse meg az Azure SQL-tároló és az Azure SQL-tároló egy biztonsági másolatból való visszaállítása biztonsági másolatok.

> [!IMPORTANT]
> Szeretné használni a legújabb AzureRM powershell balról jobbra V2 házirendek beállítása. A jelenlegi verzió: [AzureRM 4.5.0-preview](https://www.powershellgallery.com/packages/AzureRM.Sql/4.5.0-preview), ez az előzetes verzióval, ezért ez a parancs telepíti: `Install-Module -Name AzureRM.Sql -AllowPrerelease -Force`.
> Az előzetes verziót telepítésével kapcsolatos útmutatásért lásd: [Get PowerShellGet modul](https://docs.microsoft.com/en-us/powershell/gallery/installing-psget). A AzureRM powershell előfordulhat, hogy 2018 kiadás néhány nap múlva hamarosan (kellene lennie 5/18/2018), a - AllowPrelease kapcsoló figyelmen kívül hagyja a végleges verziójának telepítésekor, amikor elérhetővé válik, és a következő paranccsal " `Install-Module -Name AzureRM.Sql -Force`.

### <a name="create-an-ltr-policy"></a>Balról jobbra házirend létrehozása

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

### <a name="view-ltr-policies"></a>Nézet balról jobbra házirendek
A példa bemutatja, hogyan listázhat belül a kiszolgáló a balról jobbra házirendek

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzureRmSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzureRmSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>Egy balról jobbra házirend törlése
Ez a példa bemutatja, hogyan egy adatbázis balról jobbra házirend törlése

```powershell
Set-AzureRmSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>Biztonsági másolatok megtekintése balról jobbra

Ez a példa bemutatja, hogyan belül a kiszolgáló a balról jobbra biztonsági másolatok felsorolása. 

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

### <a name="delete-ltr-backups"></a>Balról jobbra biztonsági másolatok törlése

Ez a példa bemutatja egy balról jobbra törlése a listából, a biztonsági mentések biztonsági mentés.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzureRmSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

### <a name="restore-from-ltr-backups"></a>Állítsa vissza biztonsági másolatból balról jobbra
Ez a példa bemutatja, hogyan egy balról jobbra biztonsági másolatból történő visszaállítását. Megjegyzés: Ez az interfész nem változott, de az erőforrás-azonosító paraméter megköveteli a balról jobbra biztonsági mentési erőforrás-azonosító. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzureRmSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> Itt csatlakozhat a visszaállított adatbázis SQL Server Management Studio használatával szükséges feladatok végrehajtásához, például egy bit, az adatok kinyerése a visszaállított adatbázis másolja a már meglévő adatbázist, vagy törölje a meglévő adatbázis, és nevezze át a meglévő adatbázis nevének a visszaállított adatbázis. Lásd: [időponthoz kötött visszaállításra](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>További lépések

- A szolgáltatás által létrehozott automatikus biztonsági másolatokkal kapcsolatos további információkért lásd az [automatikus biztonsági másolatokkal](sql-database-automated-backups.md) foglalkozó témakört.
- A biztonsági másolatok hosszú távú megőrzésével kapcsolatos további információkért lásd: [biztonsági másolatok hosszú távú megőrzése](sql-database-long-term-retention.md)
