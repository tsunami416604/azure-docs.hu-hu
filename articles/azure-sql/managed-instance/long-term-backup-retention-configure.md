---
title: 'Felügyelt Azure SQL-példány: hosszú távú biztonsági mentés megőrzése (PowerShell)'
description: Megtudhatja, hogyan tárolhatja és állíthatja vissza az automatizált biztonsági mentéseket egy Azure SQL felügyelt példány különálló Azure Blob Storage-tárolójában a PowerShell használatával.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: mathoma, carlrab
manager: craigg
ms.date: 04/29/2020
ms.openlocfilehash: f7625f7dda4553a160f11ecd7f6d5c44943f6a6b
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84338736"
---
# <a name="manage-azure-sql-managed-instance-long-term-backup-retention-powershell"></a>Az Azure SQL felügyelt példányainak hosszú távú biztonsági mentési megőrzése (PowerShell) kezelése
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányain a [biztonsági mentés hosszú távú megőrzési](../database/long-term-retention-overview.md#sql-managed-instance-support) szabályzatát (ltr) korlátozott nyilvános előzetes verzióként is konfigurálhatja. Ez lehetővé teszi az adatbázis biztonsági másolatainak automatikus megőrzését különálló Azure Blob Storage-tárolókban akár 10 évig. Ezt követően a PowerShell használatával helyreállíthat egy adatbázist a biztonsági másolatokkal.

   > [!IMPORTANT]
   > A felügyelt példányok LTR jelenleg korlátozott előzetes verzióban érhető el, és az EA-és CSP-előfizetések esetében eseti alapon érhető el. A regisztráció igényléséhez hozzon létre egy [Azure-támogatási jegyet](https://azure.microsoft.com/support/create-ticket/). A probléma típusa beállításnál válassza a technikai probléma, SQL Database felügyelt példány lehetőséget, majd a probléma típusa beállításnál válassza a **biztonsági mentés, visszaállítás és Üzletmenet-folytonosság/hosszú távú biztonsági mentés**lehetőséget. A kérelemben adja meg, hogy a felügyelt példányhoz tartozó LTR korlátozott nyilvános előzetes verziójával szeretne-e regisztrálni.

A következő részben bemutatjuk, hogyan használható a PowerShell a biztonsági másolatok hosszú távú megőrzésének konfigurálására, a biztonsági mentések megtekintésére az Azure SQL Storage szolgáltatásban, valamint az Azure SQL Storage biztonsági másolatából való visszaállítás.

## <a name="rbac-roles-to-manage-long-term-retention"></a>RBAC-szerepkörök a hosszú távú adatmegőrzés kezeléséhez

A **Get-AzSqlInstanceDatabaseLongTermRetentionBackup** és a **Restore-AzSqlInstanceDatabase**esetében a következő szerepkörök egyikének kell lennie:

- Előfizetés tulajdonosi szerepköre vagy
- Felügyelt példány közreműködői szerepkör vagy
- Egyéni szerepkör a következő engedélyekkel:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionManagedInstanceBackups/read`
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/read`

A **Remove-AzSqlInstanceDatabaseLongTermRetentionBackup**esetében a következő szerepkörök egyikének kell lennie:

- Előfizetés tulajdonosi szerepköre vagy
- Egyéni szerepkör a következő engedélyekkel:
  - `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

> [!NOTE]
> A felügyelt példány közreműködői szerepkör nem rendelkezik engedéllyel a LTR biztonsági mentések törléséhez.

RBAC engedélyek is megadhatók az *előfizetés* vagy az *erőforráscsoport* hatókörében. Az eldobott példányhoz tartozó LTR biztonsági mentések eléréséhez azonban az engedélyt az adott példány *előfizetési* hatókörében kell megadni.

- `Microsoft.Sql/locations/longTermRetentionManagedInstances/longTermRetentionDatabases/longTermRetentionManagedInstanceBackups/delete`

## <a name="create-an-ltr-policy"></a>LTR szabályzat létrehozása

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

## <a name="view-ltr-policies"></a>LTR szabályzatok megtekintése

Ez a példa bemutatja, hogyan listázhatja a LTR szabályzatokat egy adott példányon belül

```powershell
# gets the current version of LTR policy for the database
$ltrPolicies = Get-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
    -DatabaseName $dbName -ResourceGroupName $resourceGroup
```

## <a name="clear-an-ltr-policy"></a>LTR szabályzat törlése

Ez a példa bemutatja, hogyan törölhet egy LTR-szabályzatot egy adatbázisból.

```powershell
Set-AzSqlInstanceDatabaseBackupLongTermRetentionPolicy -InstanceName $instanceName `
   -DatabaseName $dbName -ResourceGroupName $resourceGroup -RemovePolicy
```

## <a name="view-ltr-backups"></a>LTR biztonsági mentések megtekintése

Ez a példa bemutatja, hogyan listázhatja a LTR biztonsági másolatait egy példányon belül.

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

## <a name="delete-ltr-backups"></a>LTR biztonsági mentések törlése

Ebből a példából megtudhatja, hogyan törölhet egy LTR biztonsági másolatot a biztonsági mentések listájáról.

```powershell
# remove the earliest backup
$ltrBackup = $ltrBackups[0]
Remove-AzSqlInstanceDatabaseLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId
```

> [!IMPORTANT]
> A LTR biztonsági mentésének törlése nem fordítható vissza. Ha törölni szeretne egy LTR biztonsági mentést a példány törlése után, rendelkeznie kell előfizetés-hatókör engedéllyel. Értesítéseket állíthat be az egyes törlésekről Azure Monitor a "hosszú távú adatmegőrzési biztonsági mentés törlése" művelet szűrésével. A tevékenység naplója információt tartalmaz arról, hogy ki és mikor kezdeményezte a kérést. Részletes utasításokért tekintse meg a [Tevékenységnaplók létrehozása – riasztások](../../azure-monitor/platform/alerts-activity-log.md) című témakört.

## <a name="restore-from-ltr-backups"></a>Visszaállítás a LTR biztonsági mentésből

Ez a példa azt szemlélteti, hogyan lehet visszaállítani egy LTR biztonsági másolatból. Vegye figyelembe, hogy ez az illesztőfelület nem változott, de az erőforrás-azonosító paraméter most a LTR biztonsági mentési erőforrás-azonosítóját igényli.

```powershell
# restore a specific LTR backup as an P1 database on the instance $instanceName of the resource group $resourceGroup
Restore-AzSqlInstanceDatabase -FromLongTermRetentionBackup -ResourceId $ltrBackup.ResourceId `
   -TargetInstanceName $instanceName -TargetResourceGroupName $resourceGroup -TargetInstanceDatabaseName $dbName
```

> [!IMPORTANT]
> Ha a példányt a példány törlése után szeretné visszaállítani egy LTR biztonsági másolatból, rendelkeznie kell a példány előfizetéséhez tartozó engedélyekkel, és az előfizetésnek aktívnak kell lennie. A nem kötelező-ResourceGroupName paramétert is el kell hagyni.

> [!NOTE]
> Innen az SQL Server Management Studióval csatlakozhat a visszaállított adatbázishoz a szükséges feladatok végrehajtásához, például egy adatelem kinyeréséhez a visszaállított adatbázisból a meglévő adatbázisba való beillesztés érdekében, vagy a meglévő adatbázis törléséhez és a visszaállított adatbázis átnevezéséhez a meglévő adatbázis nevére. Lásd: [időponthoz való visszaállítás](../database/recovery-using-backups.md#point-in-time-restore).

## <a name="next-steps"></a>Következő lépések

- A szolgáltatás által létrehozott automatikus biztonsági másolatokkal kapcsolatos további információkért lásd az [automatikus biztonsági másolatokkal](../database/automated-backups-overview.md) foglalkozó témakört.
- A biztonsági másolatok hosszú távú megőrzésével kapcsolatos további információkért lásd: [biztonsági másolatok hosszú távú megőrzése](../database/long-term-retention-overview.md)
