---
title: A biztonsági mentések hosszú távú megőrzésének kezelése
description: Ismerje meg, hogyan tárolhatja az automatikus biztonsági másolatokat az SQL Azure-tárolóban, majd visszaállíthatja azokat
services: sql-database
ms.service: sql-database
ms.subservice: backup-restore
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 08/21/2019
ms.openlocfilehash: a560f4f1399792a7b150b37c3c048ccc0079b98d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74420793"
---
# <a name="manage-azure-sql-database-long-term-backup-retention"></a>Az Azure SQL Database hosszú távú biztonsági mentésének kezelése

Az Azure SQL Database-ben konfigurálhat egy vagy egy [hosszú távú biztonsági mentési adatmegőrzési](sql-database-long-term-retention.md) szabályzattal (LTR) rendelkező, egyetlen vagy készletes adatbázist, amely automatikusan megőrzi az adatbázis biztonsági másolatait külön Azure Blob-tárolókban akár 10 évig. Ezután az Azure Portalon vagy a PowerShellen keresztül helyreállíthatja az adatbázist ezekkel a biztonsági másolatokkal.

> [!IMPORTANT]
> [Az Azure SQL-adatbázis által felügyelt példány](sql-database-managed-instance.md) jelenleg nem támogatja a hosszú távú biztonsági mentés megőrzését.

## <a name="using-azure-portal"></a>Az Azure Portal használata

A következő szakaszok bemutatják, hogyan használhatja az Azure Portalon a hosszú távú megőrzéskonfigurálása, a biztonsági mentések hosszú távú megőrzése, és a biztonsági mentés visszaállítása a hosszú távú megőrzési.

### <a name="configure-long-term-retention-policies"></a>Hosszú távú adatmegőrzési házirendek konfigurálása

Az SQL Database konfigurálható úgy, hogy az [automatikus biztonsági mentések](sql-database-long-term-retention.md) a szolgáltatási szint megőrzési időszakátélinél hosszabb ideig őrizze meg az automatikus biztonsági mentéseket.

1. Az Azure Portalon jelölje ki az SQL-kiszolgálót, majd kattintson **a Biztonsági mentések kezelése gombra.** A **Házirendek konfigurálása** lapon jelölje be annak az adatbázisnak a jelölőnégyzetét, amelyen hosszú távú biztonsági mentési adatmegőrzési házirendeket szeretne beállítani vagy módosítani. Ha az adatbázis melletti jelölőnégyzet nincs bejelölve, a házirend módosításai nem lesznek érvényesek az adott adatbázisra.  

   ![biztonsági mentések kezelése hivatkozás](./media/sql-database-long-term-retention/ltr-configure-ltr.png)

2. A **Házirendek konfigurálása** ablaktáblán válassza ki, hogy meg szeretné-e őrizni a heti, havi vagy éves biztonsági mentéseket, és adja meg az egyes biztonsági mentési időszakot.

   ![házirendek konfigurálása](./media/sql-database-long-term-retention/ltr-configure-policies.png)

3. Ha elkészült, kattintson **az Alkalmaz gombra.**

> [!IMPORTANT]
> Ha engedélyezi a hosszú távú biztonsági mentési adatmegőrzési házirendet, akár 7 napig is eltarthat, amíg az első biztonsági mentés láthatóvá válik, és elérhetővé válik a visszaállításhoz. Az LTR biztonsági mentési cadance részleteiről a [hosszú távú biztonsági mentés megőrzését](sql-database-long-term-retention.md)találja.

### <a name="view-backups-and-restore-from-a-backup"></a>Biztonsági másolatok és visszaállítás megtekintése biztonsági másolatból

Tekintse meg az LTR-házirenddel rendelkező adott adatbázis biztonsági másolatait, és állítsa vissza azokat a biztonsági másolatokat.

1. Az Azure Portalon jelölje ki az SQL-kiszolgálót, majd kattintson **a Biztonsági mentések kezelése gombra.** Az **Elérhető biztonsági másolatok** lapon jelölje ki azt az adatbázist, amelynek elérhető biztonsági másolatait látni szeretné.

   ![adatbázis kiválasztása](./media/sql-database-long-term-retention/ltr-available-backups-select-database.png)

1. Az **Elérhető biztonsági mentések** ablaktáblán tekintse át a rendelkezésre álló biztonsági másolatokat.

   ![biztonsági mentések megtekintése](./media/sql-database-long-term-retention/ltr-available-backups.png)

1. Jelölje ki azt a biztonsági másolatot, amelyből vissza szeretné állítani, majd adja meg az új adatbázis nevét.

   ![visszaállítás](./media/sql-database-long-term-retention/ltr-restore.png)

1. Kattintson **az OK** gombra az adatbázis visszaállításához az Azure SQL storage biztonsági mentéséből az új adatbázisba.

1. Az eszköztáron kattintson az értesítési ikonra a visszaállítási feladat állapotának megtekintéséhez.

   ![visszaállítási feladat állapota](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

1. A visszaállítási feladat befejezése után nyissa meg az **SQL-adatbázisok** lapot az újonnan visszaállított adatbázis megtekintéséhez.

> [!NOTE]
> Innen az SQL Server Management Studióval csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például [egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére](sql-database-recovery-using-backups.md#point-in-time-restore).

## <a name="using-powershell"></a>A PowerShell használata

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> A PowerShell Azure Resource Manager modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Ezekről a parancsmagokról az [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)című témakörben található. Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak.

Az alábbi szakaszok bemutatják, hogyan konfigurálhatja a PowerShellt a hosszú távú biztonsági mentés megőrzésének konfigurálásával, az Azure SQL storage biztonsági másolatainak megtekintésével és az Azure SQL storage biztonsági mentéséből történő visszaállítással.

### <a name="rbac-roles-to-manage-long-term-retention"></a>RBAC szerepkörök a hosszú távú megőrzés kezeléséhez

A **Get-AzSqlDatabaseLongTermRetentionBackup** és **restore-AzSqlDatabase**rendszerben az alábbi szerepkörök egyikével kell rendelkeznie:

- Előfizetés tulajdonosi szerepköre vagy
- SQL Server közreműködői szerepkör vagy
- Egyéni szerepkör a következő engedélyekkel:

   Microsoft.Sql/locations/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionBackups/read Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/read

Az **Remove-AzSqlDatabaseLongTermRetentionBackup**esetén az alábbi szerepkörök egyikével kell rendelkeznie:

- Előfizetés tulajdonosi szerepköre vagy
- Egyéni szerepkör a következő engedélyekkel:

   Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

> [!NOTE]
> Az SQL Server Közreműködőszerepkör nem rendelkezik engedéllyel az LTR biztonsági másolatainak törléséhez.

RBAC-engedélyeket lehet adni az *előfizetési* vagy *erőforráscsoport* hatókörében. Az eldobott kiszolgálóhoz tartozó LTR-biztonsági mentések eléréséhez azonban az engedélyt a kiszolgáló *előfizetési* hatókörében kell megadni.

- Microsoft.Sql/locations/longTermRetentionServers/longTermRetentionDatabases/longTermRetentionBackups/delete

### <a name="create-an-ltr-policy"></a>LTR-házirend létrehozása

```powershell
# get the SQL server
$subId = "<subscriptionId>"
$serverName = "<serverName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$server = Get-AzSqlServer -ServerName $serverName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

### <a name="view-ltr-policies"></a>LTR-házirendek megtekintése

Ez a példa bemutatja, hogyan sorolható fel az LTR-házirendek a kiszolgálón belül

```powershell
# get all LTR policies within a server
$ltrPolicies = Get-AzSqlDatabase -ResourceGroupName Default-SQL-WestCentralUS -ServerName trgrie-ltr-server | `
    Get-AzSqlDatabaseLongTermRetentionPolicy -Current

# get the LTR policy of a specific database
$ltrPolicies = Get-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -Current
```

### <a name="clear-an-ltr-policy"></a>LtR-házirend törlése

Ez a példa bemutatja, hogyan lehet törölni egy LTR-házirendet egy adatbázisból

```powershell
Set-AzSqlDatabaseBackupLongTermRetentionPolicy -ServerName $serverName -DatabaseName $dbName `
    -ResourceGroupName $resourceGroup -RemovePolicy
```

### <a name="view-ltr-backups"></a>LTR-biztonsági mentések megtekintése

Ez a példa bemutatja, hogyan listázható az LTR biztonsági mentések egy kiszolgálón belül.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location

# get the list of LTR backups from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName

# get the LTR backups for a specific database from the Azure region under the named server
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlDatabaseLongTermRetentionBackup -Location $server.Location -ServerName $serverName -OnlyLatestPerDatabase
```

### <a name="delete-ltr-backups"></a>LTR-biztonsági mentések törlése

Ez a példa bemutatja, hogyan lehet törölni egy LTR biztonsági mentést a biztonsági mentések listájáról.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Az LTR biztonsági mentésének törlése nem visszafordítható. Az LTR biztonsági másolat ának a kiszolgáló törlése után való törléséhez előfizetési hatókör-engedéllyel kell rendelkeznie. Az Azure Monitorban az egyes törlésekről szóló értesítéseket a "Hosszú távú megőrzési biztonsági mentés törlése" művelet szűrésével állíthatja be. A tevékenységnapló információkat tartalmaz arról, hogy ki és mikor kérte a kérelmet. Részletes utasításokat a [Tevékenységnapló-riasztások létrehozása](../azure-monitor/platform/alerts-activity-log.md) című témakörben talál.

### <a name="restore-from-ltr-backups"></a>Visszaállítás ltr biztonsági mentések

Ez a példa bemutatja, hogyan állítható vissza egy LTR biztonsági másolatból. Ne feledje, hogy ez a felület nem változott, de az erőforrás-azonosító paraméterhez most az LTR biztonsági másolat erőforrás-azonosítója szükséges.

```powershell
# restore a specific LTR backup as an P1 database on the server $serverName of the resource group $resourceGroup
Restore-AzSqlDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId -ServerName $serverName -ResourceGroupName $resourceGroup `
    -TargetDatabaseName $dbName -ServiceObjectiveName P1
```

> [!IMPORTANT]
> Ha a kiszolgáló törlése után szeretne visszaállítani egy LTR-biztonsági másolatból, a kiszolgáló előfizetéséhez hatókörrel rendelkező engedélyekkel kell rendelkeznie, és az előfizetésnek aktívnak kell lennie. A nem kötelező -ResourceGroupName paramétert is ki kell hagynia.

> [!NOTE]
> Innen az SQL Server Management Studióval csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére. Lásd [az idővisszaállítás idáig.](sql-database-recovery-using-backups.md#point-in-time-restore)

## <a name="next-steps"></a>További lépések

- A szolgáltatás által létrehozott automatikus biztonsági másolatokkal kapcsolatos további információkért lásd az [automatikus biztonsági másolatokkal](sql-database-automated-backups.md) foglalkozó témakört.
- A biztonsági másolatok hosszú távú megőrzésével kapcsolatos további információkért lásd: [biztonsági másolatok hosszú távú megőrzése](sql-database-long-term-retention.md)
