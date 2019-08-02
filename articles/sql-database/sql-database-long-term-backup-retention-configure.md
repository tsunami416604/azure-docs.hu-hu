---
title: A biztonsági másolatok hosszú távú megőrzésének kezelése Azure SQL Database | Microsoft Docs
description: Ismerje meg, hogyan tárolhat automatizált biztonsági mentéseket a SQL Azure tárolóban, majd állítsa vissza őket
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
ms.date: 04/17/2019
ms.openlocfilehash: 38ecd7797452c9a16b859da921287b8026f0660d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567800"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>A biztonsági másolatok hosszú távú megőrzésének Azure SQL Database kezelése

Azure SQL Database egyetlen vagy készletezett adatbázist is beállíthat [hosszú távú biztonsági mentési](sql-database-long-term-retention.md) adatmegőrzési szabályzattal (ltr), amely akár 10 évig automatikusan megőrzi a biztonsági mentéseket az Azure Blob Storage-ban. Ezután a Azure Portal vagy a PowerShell használatával helyreállíthat egy adatbázist a biztonsági másolatokkal.

> [!IMPORTANT]
> [Azure SQL Database felügyelt példány](sql-database-managed-instance.md) jelenleg nem támogatja a biztonsági másolatok hosszú távú megőrzését.

## <a name="use-the-azure-portal-to-configure-long-term-retention-policies-and-restore-backups"></a>A Azure Portal használata a hosszú távú adatmegőrzési házirendek konfigurálásához és a biztonsági másolatok visszaállításához

A következő részben bemutatjuk, Azure Portal hogyan konfigurálhatja a hosszú távú adatmegőrzést, megtekintheti a biztonsági mentéseket hosszú távú adatmegőrzéssel, és visszaállíthatja a biztonsági mentést a hosszú távú adatmegőrzésből.

### <a name="configure-long-term-retention-policies"></a>Hosszú távú adatmegőrzési szabályzatok konfigurálása

A SQL Database konfigurálhatja úgy, hogy az [automatizált biztonsági mentéseket](sql-database-long-term-retention.md) a szolgáltatási szinten megőrzött időtartamnál hosszabb ideig is megőrizze. 

1. A Azure Portal válassza ki az SQL Servert, majd kattintson a **biztonsági mentések kezelése**lehetőségre. A **házirendek konfigurálása** lapon *jelölje be annak az adatbázisnak a jelölőnégyzetét, amelyen a biztonsági másolatok hosszú távú megőrzési szabályzatait be szeretné állítani vagy módosítani*kívánja. Ha az adatbázis melletti jelölőnégyzet nincs bejelölve, a házirend módosításai nem lesznek érvényesek az adott adatbázisra.  

   ![biztonsági másolatok kezelése hivatkozás](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. A **házirendek konfigurálása** panelen válassza ki, hogy meg kívánja-e őrizni a hetente, havonta vagy évenkénti biztonsági mentést, és adja meg a megőrzési időtartamot. 

   ![házirendek konfigurálása](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Ha elkészült, kattintson az **alkalmaz**gombra.

> [!IMPORTANT]
> Ha engedélyezi a biztonsági másolatok hosszú távú megőrzési szabályát, akkor akár 7 napig is eltarthat, amíg az első biztonsági mentés láthatóvá válik, és visszaállítható. A LTR biztonsági mentési cadance kapcsolatos részletekért lásd a [biztonsági másolatok hosszú távú megőrzését](sql-database-long-term-retention.md)ismertető témakört.

### <a name="view-backups-and-restore-from-a-backup-using-azure-portal"></a>Biztonsági mentések és visszaállítások megtekintése Azure Portal használatával

Megtekintheti az adott adatbázishoz LTR házirenddel megőrzött biztonsági másolatokat, és visszaállíthatja azokat a biztonsági másolatokból. 

1. A Azure Portal válassza ki az SQL Servert, majd kattintson a **biztonsági mentések kezelése**lehetőségre. A **rendelkezésre álló biztonsági másolatok** lapon válassza ki azt az adatbázist, amelynek elérhető biztonsági másolatait szeretné megtekinteni.

   ![adatbázis kiválasztása](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

3. Az **elérhető biztonsági másolatok** panelen tekintse át az elérhető biztonsági másolatokat. 

   ![biztonsági másolatok megtekintése](./media/sql-database-long-term-retention/ltr-available-backups.png)

4. Válassza ki azt a biztonsági másolatot, amelyről vissza kívánja állítani, majd adja meg az új adatbázis nevét.

   ![visszaállítás](./media/sql-database-long-term-retention/ltr-restore.png)

5. Az **OK** gombra kattintva állítsa vissza az adatbázist az Azure SQL Storage-ban lévő biztonsági másolatból az új adatbázisba.

6. Az eszköztáron kattintson az értesítési ikonra a visszaállítási feladat állapotának megtekintéséhez.

   ![visszaállítási feladat állapota](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. A visszaállítási feladatok befejezése után nyissa meg az **SQL-adatbázisok** lapot az újonnan visszaállított adatbázis megtekintéséhez.

> [!NOTE]
> Innen az SQL Server Management Studióval csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például [egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére](sql-database-recovery-using-backups.md#point-in-time-restore).
>

## <a name="use-powershell-to-configure-long-term-retention-policies-and-restore-backups"></a>A hosszú távú adatmegőrzési házirendek konfigurálása és a biztonsági másolatok visszaállítása a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

A következő részben bemutatjuk, hogyan használható a PowerShell a biztonsági másolatok hosszú távú megőrzésének konfigurálására, a biztonsági mentések megtekintésére az Azure SQL Storage szolgáltatásban, valamint az Azure SQL Storage biztonsági másolatából való visszaállítás.


### <a name="rbac-roles-to-manage-long-term-retention"></a>RBAC-szerepkörök a hosszú távú adatmegőrzés kezeléséhez

A LTR biztonsági mentések kezeléséhez a következőkre lesz szüksége: 
- Előfizetés tulajdonosa vagy
- SQL Server közreműködő szerepkör az **előfizetés** hatókörében vagy
- SQL Database közreműködő szerepkör az **előfizetés** hatókörében

Ha részletesebb szabályozásra van szükség, létrehozhat egyéni RBAC-szerepköröket, és hozzárendelheti őket az előfizetési hatókörben. 

A **Get-AzSqlDatabaseLongTermRetentionBackup** és a **Restore-AzSqlDatabase** a szerepkörnek a következő engedélyekkel kell rendelkeznie:

Microsoft. SQL/Locations/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionBackups/Read Microsoft. SQL/Locations/longTermRetentionServers/longTermRetentionDatabases/ longTermRetentionBackups/olvasás
 
A **Remove-AzSqlDatabaseLongTermRetentionBackup** esetében a szerepkörnek a következő engedélyekkel kell rendelkeznie:

Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete


### <a name="create-an-ltr-policy"></a>LTR szabályzat létrehozása

```powershell
# Get the SQL server 
# $subId = “{subscription-id}”
# $serverName = “{server-name}”
# $resourceGroup = “{resource-group-name}” 
# $dbName = ”{database-name}”

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

# get the server
$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W 

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>LTR szabályzatok megtekintése
Ez a példa azt mutatja be, hogyan listázható a LTR szabályzatok a kiszolgálón belül

```powershell
# Get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | Get-AzSqlDatabaseLongTermRetentionPolicy -Current 

# Get the LTR policy of a specific database 
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName  -ResourceGroupName $resourceGroup -Current
```
### <a name="clear-an-ltr-policy"></a>LTR szabályzat törlése
Ez a példa bemutatja, hogyan törölhet egy LTR-szabályzatot egy adatbázisból.

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>LTR biztonsági mentések megtekintése

Ez a példa azt mutatja be, hogyan listázható a LTR biztonsági mentése egy kiszolgálón belül. 

```powershell
# Get the list of all LTR backups in a specific Azure region 
# The backups are grouped by the logical database id.
# Within each group they are ordered by the timestamp, the earliest
# backup first.  
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location 

# Get the list of LTR backups from the Azure region under 
# the named server. 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# Get the LTR backups for a specific database from the Azure region under the named server 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# List LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# Only list the latest LTR backup for each database 
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>LTR biztonsági mentések törlése

Ebből a példából megtudhatja, hogyan törölhet egy LTR biztonsági másolatot a biztonsági mentések listájáról.

```powershell
# remove the earliest backup 
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```
> [!IMPORTANT]
> A LTR biztonsági mentésének törlése nem fordítható vissza. Értesítéseket állíthat be az egyes törlésekről Azure Monitor a "hosszú távú adatmegőrzési biztonsági mentés törlése" művelet szűrésével. A tevékenység naplója információt tartalmaz arról, hogy ki és mikor kezdeményezte a kérést. Részletes utasításokért tekintse meg a [Tevékenységnaplók létrehozása](../azure-monitor/platform/alerts-activity-log.md) – riasztások című témakört.
>

### <a name="restore-from-ltr-backups"></a>Visszaállítás a LTR biztonsági mentésből
Ez a példa azt szemlélteti, hogyan lehet visszaállítani egy LTR biztonsági másolatból. Vegye figyelembe, hogy ez az illesztőfelület nem változott, de az erőforrás-azonosító paraméter most a LTR biztonsági mentési erőforrás-azonosítóját igényli. 

```powershell
# Restore LTR backup as an S3 database
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup -TargetDatabaseName $dbName -ServiceObjectiveName S3
```

> [!NOTE]
> Innen a SQL Server Management Studio használatával csatlakozhat a visszaállított adatbázishoz a szükséges feladatok elvégzéséhez, például a visszaállított adatbázisból a meglévő adatbázisba való másoláshoz, illetve a meglévő adatbázis törléséhez és a visszaállított adatok átnevezéséhez. adatbázist a meglévő adatbázis nevére. Lásd: időponthoz [való visszaállítás](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>További lépések

- A szolgáltatás által létrehozott automatikus biztonsági másolatokkal kapcsolatos további információkért lásd az [automatikus biztonsági másolatokkal](sql-database-automated-backups.md) foglalkozó témakört.
- A biztonsági másolatok hosszú távú megőrzésével kapcsolatos további információkért lásd: [biztonsági másolatok hosszú távú megőrzése](sql-database-long-term-retention.md)
