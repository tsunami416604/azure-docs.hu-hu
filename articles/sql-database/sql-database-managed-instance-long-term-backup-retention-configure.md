---
title: 'Felügyelt példány: Hosszú távú biztonsági mentés megőrzése (PowerShell)'
description: Megtudhatja, hogyan tárolhatja és állíthatja vissza az automatikus biztonsági mentéseket külön Azure Blob-tárolókban egy Azure SQL Database által felügyelt példányhoz a PowerShell használatával.
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
ms.date: 04/19/2020
ms.openlocfilehash: 24eacb555704593fe44bc2d949de44de163345bc
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677104"
---
# <a name="manage-azure-sql-database-managed-instance-long-term-backup-retention-powershell"></a>Az Azure SQL Database által felügyelt példány hosszú távú biztonsági mentésének kezelése (PowerShell)

Az Azure SQL Database felügyelt példányában konfigurálhat egy [hosszú távú biztonsági mentési adatmegőrzési](sql-database-long-term-retention.md#managed-instance-support) szabályzatot (LTR) korlátozott nyilvános előzetes verziófunkcióként. Ez lehetővé teszi, hogy automatikusan megőrizze az adatbázis-biztonsági mentések külön Azure Blob tárolótárolókban akár 10 évig. Ezután helyreállíthatja az adatbázist ezekkel a biztonsági másolatokkal a PowerShell segítségével.

   > [!IMPORTANT]
   > A felügyelt példányok LTR-je jelenleg korlátozott előzetes verzióban érhető el, és eseti alapon érhető el az EA- és CSP-előfizetésekhez. A regisztráció igényléséhez hozzon létre egy [Azure-támogatási jegyet](https://azure.microsoft.com/support/create-ticket/) a **Biztonsági mentés, visszaállítás és üzletmenet-folytonosság/hosszú távú biztonsági mentés megőrzése**című támogatási témakörben. 


Az alábbi szakaszok bemutatják, hogyan konfigurálhatja a PowerShellt a hosszú távú biztonsági mentés megőrzésének konfigurálásával, az Azure SQL storage biztonsági másolatainak megtekintésével és az Azure SQL storage biztonsági mentéséből történő visszaállítással.

## <a name="rbac-roles-to-manage-long-term-retention"></a>RBAC szerepkörök a hosszú távú megőrzés kezeléséhez

A **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** és **restore-AzSqlInstanceDatabase**rendszerben az alábbi szerepkörök egyikével kell rendelkeznie:

- Előfizetés tulajdonosi szerepköre vagy
- Felügyelt példány közreműködői szerepkör vagy
- Egyéni szerepkör a következő engedélyekkel:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

Az **Remove-AzSqlInstanceDatabaseLongTermRetentionBackup**esetén az alábbi szerepkörök egyikével kell rendelkeznie:

- Előfizetés tulajdonosi szerepköre vagy
- Egyéni szerepkör a következő engedélyekkel:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> A felügyelt példány közreműködői szerepkör nem rendelkezik engedéllyel az LTR biztonsági másolatainak törléséhez.

RBAC-engedélyeket lehet adni az *előfizetési* vagy *erőforráscsoport* hatókörében. Azonban az eldobott példányhoz tartozó LTR-biztonsági mentések eléréséhez az engedélyt az adott példány *előfizetési* hatókörében kell megadni.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

## <a name="create-an-ltr-policy"></a>LTR-házirend létrehozása

```powershell
# get the Managed Instance
$subId = "<subscriptionId>"
$instanceName = "<instanceName>"
$resourceGroup = "<resourceGroupName>"
$dbName = "<databaseName>"

Connect-AzAccount
Select-AzSubscription -SubscriptionId $subId

$instance = Get-AzSqlInstance -Name $instanceName -ResourceGroupName $resourceGroup

# create LTR policy with WeeklyRetention = 12 weeks. MonthlyRetention and YearlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W

# create LTR policy with WeeklyRetention = 12 weeks, YearlyRetention = 5 years and WeekOfYear = 16 (week of April 15). MonthlyRetention = 0 by default.
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup -WeeklyRetention P12W -YearlyRetention P5Y -WeekOfYear 16
```

## <a name="view-ltr-policies"></a>LTR-házirendek megtekintése

Ez a példa bemutatja, hogyan sorolható fel az LTR-házirendek egy példányon belül

```powershell
# gets the current version of LTR policy for the database
$ltrPolicies = Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup
```

## <a name="clear-an-ltr-policy"></a>LtR-házirend törlése

Ez a példa bemutatja, hogyan lehet törölni egy LTR-házirendet egy adatbázisból

```powershell
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

## <a name="view-ltr-backups"></a>LTR-biztonsági mentések megtekintése

Ez a példa bemutatja, hogyan listázható az LTR biztonsági mentések egy példányon belül.

```powershell
# get the list of all LTR backups in a specific Azure region
# backups are grouped by the logical database id, within each group they are ordered by the timestamp, the earliest backup first
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location

# get the list of LTR backups from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName

# get the LTR backups for a specific database from the Azure region under the given managed instance
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -DatabaseName $dbName

# list LTR backups only from live databases (you have option to choose All/Live/Deleted)
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -DatabaseState Live

# only list the latest LTR backup for each database
$ltrBackups = Get-AzSqlInstanceDatabaseLongTermRetentionBackup -Location $instance.Location -InstanceName $instanceName -OnlyLatestPerDatabase
```

## <a name="delete-ltr-backups"></a>LTR-biztonsági mentések törlése

Ez a példa bemutatja, hogyan lehet törölni egy LTR biztonsági mentést a biztonsági mentések listájáról.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> Az LTR biztonsági mentésének törlése nem visszafordítható. A példány törlése után az LTR biztonsági másolatának törléséhez előfizetési hatókör-engedéllyel kell rendelkeznie. Az Azure Monitorban az egyes törlésekről szóló értesítéseket a "Hosszú távú megőrzési biztonsági mentés törlése" művelet szűrésével állíthatja be. A tevékenységnapló információkat tartalmaz arról, hogy ki és mikor kérte a kérelmet. Részletes utasításokat a [Tevékenységnapló-riasztások létrehozása](../azure-monitor/platform/alerts-activity-log.md) című témakörben talál.

## <a name="restore-from-ltr-backups"></a>Visszaállítás ltr biztonsági mentések

Ez a példa bemutatja, hogyan állítható vissza egy LTR biztonsági másolatból. Ne feledje, hogy ez a felület nem változott, de az erőforrás-azonosító paraméterhez most az LTR biztonsági másolat erőforrás-azonosítója szükséges.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName
```

> [!IMPORTANT]
> Ha a példány törlése után szeretne visszaállítani egy LTR-biztonsági mentésből, a példány előfizetéséhez hatókörrel rendelkező engedélyekkel kell rendelkeznie, és az előfizetésnek aktívnak kell lennie. A nem kötelező -ResourceGroupName paramétert is ki kell hagynia.

> [!NOTE]
> Innen az SQL Server Management Studióval csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére. Lásd [az idővisszaállítás idáig.](sql-database-recovery-using-backups.md#point-in-time-restore)

## <a name="next-steps"></a>További lépések

- A szolgáltatás által létrehozott automatikus biztonsági másolatokkal kapcsolatos további információkért lásd az [automatikus biztonsági másolatokkal](sql-database-automated-backups.md) foglalkozó témakört.
- A biztonsági másolatok hosszú távú megőrzésével kapcsolatos további információkért lásd: [biztonsági másolatok hosszú távú megőrzése](sql-database-long-term-retention.md)
