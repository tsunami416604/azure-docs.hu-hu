---
title: Felügyelt példány – időponthoz tartozó visszaállítás
description: SQL-adatbázis visszaállítása felügyelt példányban egy korábbi időpontra.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab, mathoma
ms.date: 08/25/2019
ms.openlocfilehash: b106b1da5d012309e8d92c8e9555ee3982602e12
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707668"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>SQL-adatbázis visszaállítása felügyelt példányban egy korábbi időpontra

Az időponthoz tartozó visszaállítás (PITR) használatával létrehozhat egy adatbázist egy másik adatbázis másolatával egy korábbi időpontban. Ez a cikk azt ismerteti, hogyan végezhető el az adatbázis egy adott időpontban történő visszaállítása egy Azure SQL Database felügyelt példányban.

Az időponthoz való visszaállítás hasznos olyan helyreállítási helyzetekben, mint például a hibák által okozott incidensek, az adatok helytelen betöltése vagy a kritikus adatok törlése. Egyszerűen tesztelésre vagy naplózásra is használható. A biztonságimásolat-fájlok az adatbázis beállításaitól függően 7 – 35 napig tartanak.

Az időponthoz történő visszaállítás a következőket teheti:

- Adatbázis visszaállítása meglévő adatbázisból.
- Adatbázis visszaállítása törölt adatbázisból.

Felügyelt példány esetén az adott időponthoz tartozó visszaállítás a következő esetekben is elvégezhető:

- Adatbázis visszaállítása ugyanarra a felügyelt példányra.
- Adatbázis visszaállítása egy másik felügyelt példányra.

> [!NOTE]
> Egy teljes felügyelt példány időponthoz tartozó visszaállítása nem lehetséges. Ez a cikk a felügyelt példányokon üzemeltetett adatbázisok adott időpontban történő visszaállítását ismerteti.

## <a name="limitations"></a>Korlátozások

Ha egy felügyelt példányról egy másikra állítja vissza a visszaállítást, mindkét példánynak ugyanabban az előfizetésben és régióban kell lennie. A régiók közötti és az előfizetések közötti visszaállítás jelenleg nem támogatott.

> [!WARNING]
> Vegye figyelembe a felügyelt példány tárolási méretét. A visszaállítani kívánt adatmérettől függően elfogyhat a példányok tárolója. Ha nincs elég hely a visszaállított adatmennyiséghez, használjon más megközelítést.

A következő táblázat a felügyelt példányok időponthoz kapcsolódó visszaállítási forgatókönyveit mutatja be:

|           |Meglévő adatbázis visszaállítása azonos felügyelt példányra| Meglévő adatbázis visszaállítása egy másik felügyelt példányra|Az eldobott adatbázis visszaállítása ugyanarra a felügyelt példányra|Az eldobott adatbázis visszaállítása egy másik felügyelt példányra|
|:----------|:----------|:----------|:----------|:----------|
|**Azure Portalra**| Igen|Nem |Nem|Nem|
|**Azure CLI**|Igen |Igen |Nem|Nem|
|**PowerShell**| Igen|Igen |Igen|Igen|

## <a name="restore-an-existing-database"></a>Meglévő adatbázis visszaállítása

A Azure Portal, a PowerShell vagy az Azure CLI használatával állítson vissza egy meglévő adatbázist ugyanarra a példányra. Ha másik példányra szeretné visszaállítani az adatbázist, használja a PowerShellt vagy az Azure CLI-t, így megadhatja a cél felügyelt példány és az erőforráscsoport tulajdonságait. Ha nem határozza meg ezeket a paramétereket, a rendszer alapértelmezés szerint az adatbázist visszaállítja a meglévő példányra. A Azure Portal jelenleg nem támogatja a visszaállítást egy másik példányra.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 
2. Lépjen a felügyelt példányra, és válassza ki a visszaállítani kívánt adatbázist.
3. Válassza a **visszaállítás** lehetőséget az adatbázis lapon:

    ![Adatbázis visszaállítása a Azure Portal használatával](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. A **visszaállítás** lapon válassza ki azt a dátumot és időpontot, amelyre vissza kívánja állítani az adatbázist.
5. Az adatbázis visszaállításához kattintson a **Confirm (megerősítés** ) gombra. Ez a művelet elindítja a visszaállítási folyamatot, amely létrehoz egy új adatbázist, és feltölti azt az eredeti adatbázisból származó adatokkal a megadott időpontban. A helyreállítási folyamattal kapcsolatos további információkért lásd: [helyreállítás ideje](sql-database-recovery-using-backups.md#recovery-time).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Ha még nincs Azure PowerShell telepítve, tekintse meg [a Azure PowerShell modul telepítését](https://docs.microsoft.com/powershell/azure/install-az-ps)ismertető témakört.

Az adatbázis PowerShell használatával történő visszaállításához adja meg a paraméterek értékét a következő parancsban. Ezután futtassa a parancsot:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source-database>"
$pointInTime = "2018-06-27T08:51:39.3882806Z"
$targetDatabase = "<Name of new database to be created>"

Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
```

Az adatbázis egy másik felügyelt példányra való visszaállításához adja meg a cél erőforráscsoport és a felügyelt példány nevét is:  

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
                              -ResourceGroupName $resourceGroupName `
                              -InstanceName $managedInstanceName `
                              -Name $databaseName `
                              -PointInTime $pointInTime `
                              -TargetInstanceDatabaseName $targetDatabase `
                              -TargetResourceGroupName $targetResourceGroupName `
                              -TargetInstanceName $targetInstanceName 
```

Részletekért lásd: [Restore-AzSqlInstanceDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha még nem telepítette az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli?view=azure-cli-latest)ismertető témakört.

Az adatbázis Azure CLI használatával történő visszaállításához adja meg a paraméterek értékét a következő parancsban. Ezután futtassa a parancsot:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Az adatbázis egy másik felügyelt példányra való visszaállításához adja meg a cél erőforráscsoport és a felügyelt példány nevét is:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Az elérhető paraméterek részletes ismertetését lásd a [CLI dokumentációjában, amely egy adatbázis helyreállítását felügyelt példányban](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)végezheti el.

---

## <a name="restore-a-deleted-database"></a>Törölt adatbázis visszaállítása

A törölt adatbázisok visszaállítását csak a PowerShell használatával végezheti el. Az adatbázis visszaállítható ugyanarra a példányra vagy egy másik példányra.

A törölt adatbázisok PowerShell használatával történő visszaállításához adja meg a paraméterek értékét a következő parancsban. Ezután futtassa a parancsot:

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"

$deleted_db = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
            -InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName 

$pointInTime = "2018-06-27T08:51:39.3882806Z"
$properties = New-Object System.Object
$properties | Add-Member -type NoteProperty -name CreateMode -Value "PointInTimeRestore"
$properties | Add-Member -type NoteProperty -name RestorePointInTime -Value $pointInTime
$properties | Add-Member -type NoteProperty -name RestorableDroppedDatabaseId -Value $deleted_db.Id
```

Ha vissza szeretné állítani a törölt adatbázist egy másik példányra, módosítsa az erőforráscsoport és a felügyelt példány nevét. Győződjön meg arról is, hogy a Location paraméter megegyezik az erőforráscsoport és a felügyelt példány helyével.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-an-existing-database"></a>Meglévő adatbázis felülírása

Meglévő adatbázis felülírásához a következőket kell tennie:

1. Dobja el a felülírni kívánt meglévő adatbázist.
2. Nevezze át az időponthoz visszaállított adatbázist az eldobott adatbázis nevére.

### <a name="drop-the-original-database"></a>Az eredeti adatbázis eldobása

Az adatbázist a Azure Portal, a PowerShell vagy az Azure CLI használatával lehet eldobni.

Az adatbázist közvetlenül a felügyelt példányhoz való csatlakozással is elvégezheti, SQL Server Management Studio elindításával (SSMS), majd futtathatja a következő Transact-SQL (T-SQL) parancsot:

```sql
DROP DATABASE WorldWideImporters;
```

A következő módszerek egyikével csatlakozhat az adatbázishoz a felügyelt példányban:

- [SSMS/Azure Data Studio Azure-beli virtuális gépen keresztül](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Pont – hely kapcsolat](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Nyilvános végpont](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

A Azure Portal válassza ki az adatbázist a felügyelt példányból, majd válassza a **Törlés**lehetőséget.

   ![Adatbázis törlése a Azure Portal használatával](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

A következő PowerShell-paranccsal elhúzhatja a felügyelt példányok egy meglévő adatbázisát:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

A következő Azure CLI-paranccsal elhúzhatja a felügyelt példányokból származó meglévő adatbázist:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Módosítsa az új adatbázisnevet, hogy az megfeleljen az eredeti adatbázis nevének.

Kapcsolódjon közvetlenül a felügyelt példányhoz, és indítsa el SQL Server Management Studio. Ezután futtassa a következő Transact-SQL (T-SQL) lekérdezést. A lekérdezés módosítja a visszaállított adatbázis nevét a felülírni kívánt eldobott adatbázisra.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

A következő módszerek egyikével csatlakozhat az adatbázishoz a felügyelt példányban:

- [Azure-beli virtuális gép](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Pont – hely kapcsolat](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Nyilvános végpont](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>Következő lépések

További információ az [automatizált biztonsági mentésekről](sql-database-automated-backups.md).
