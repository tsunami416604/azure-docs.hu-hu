---
title: Felügyelt példány – időponthoz(PITR)
description: Sql-adatbázis visszaállítása egy kezelt példányban egy korábbi időpontra.
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
ms.openlocfilehash: 27f465e6864d0ff639e825c8a816d86648bd8853
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268807"
---
# <a name="restore-a-sql-database-in-a-managed-instance-to-a-previous-point-in-time"></a>SQL-adatbázis visszaállítása egy kezelt példányban egy korábbi időpontra

Az időponthoz való tal(PITR) segítségével hozzon létre egy adatbázist egy másik adatbázis másolataként a múltban. Ez a cikk ismerteti, hogyan lehet egy adatbázis-visszaállítás egy Azure SQL Database felügyelt példányban egy adatbázis.This article describes how to do a point-in-time restore of a Database in an Azure SQL Database managed instance.

Az időponthoz való visszaállítás olyan helyreállítási forgatókönyvekben hasznos, mint például a hibák által okozott incidensek, a helytelenül betöltött adatok vagy a kritikus adatok törlése. Egyszerűen tesztelésre vagy naplózásra is használhatja. A biztonsági másolat fájlokat az adatbázis beállításaitól függően 7–35 napig tároljuk.

Az időponthoz való visszaállítás visszaállíthatja az adatbázist:

- egy meglévő adatbázisból.
- törölt adatbázisból.
- ugyanahhoz a felügyelt példányhoz vagy egy másik felügyelt példányhoz. 

## <a name="limitations"></a>Korlátozások

A felügyelt példány időpont-visszaállítása a következő korlátozásokkal rendelkezik:

- Amikor az egyik felügyelt példányról a másikra állít vissza, mindkét példánynak ugyanabban az előfizetésben és régióban kell lennie. Régiók közötti és több előfizetéses visszaállítás jelenleg nem támogatott.
- A teljes felügyelt példány időponthoz való visszavisszaállítása nem lehetséges. Ez a cikk csak azt ismerteti, hogy mi lehetséges: egy felügyelt példányon tárolt adatbázis időponthoz, és az időponthoz,</a0>

> [!WARNING]
> Vegye figyelembe a felügyelt példány tárolási méretét. A visszaállítandó adatok méretétől függően előfordulhat, hogy elfogy a példánytároló. Ha nincs elég hely a visszaállított adatok számára, használjon más megközelítést.

Az alábbi táblázat a felügyelt példányok időponthoz való visszaállítási forgatókönyveit mutatja be:

|           |Meglévő adatbázis visszaállítása ugyanarra a felügyelt példányra| Meglévő adatbázis visszaállítása másik felügyelt példányra|Visszaállítás eldobása ADATBÁZIS ugyanahhoz a felügyelt példányhoz|Az eldobott adatbázis visszaállítása egy másik felügyelt példányra|
|:----------|:----------|:----------|:----------|:----------|
|**Azure-portál**| Igen|Nem |Igen|Nem|
|**Azure CLI**|Igen |Igen |Nem|Nem|
|**Powershell**| Igen|Igen |Igen|Igen|

## <a name="restore-an-existing-database"></a>Meglévő adatbázis visszaállítása

Állítsa vissza a meglévő adatbázist ugyanarra a példányra az Azure Portalon, a PowerShell vagy az Azure CLI használatával. Adatbázis-visszaállítás egy másik példány, használja a PowerShell vagy az Azure CLI, így megadhatja a tulajdonságokat a cél felügyelt példány és erőforráscsoport. Ha nem adja meg ezeket a paramétereket, az adatbázis alapértelmezés szerint visszaáll a meglévő példányra. Az Azure Portal jelenleg nem támogatja a visszaállítást egy másik példányba.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com) 
2. Nyissa meg a felügyelt példányt, és jelölje ki a visszaállítani kívánt adatbázist.
3. Az adatbázis lapon válassza a **Visszaállítás** lehetőséget:

    ![Adatbázis visszaállítása az Azure Portal használatával](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

4. A **Visszaállítás** lapon jelölje ki annak a dátumnak és időpontnak a pontját, amelybe vissza szeretné állítani az adatbázist.
5. Az adatbázis visszaállításához válassza a **Megerősítés** lehetőséget. Ez a művelet elindítja a visszaállítási folyamatot, amely létrehoz egy új adatbázist, és feltölti azt az eredeti adatbázisból származó adatokkal a megadott időpontban. A helyreállítási folyamatról további információt a Helyreállítási idő című témakörben [talál.](sql-database-recovery-using-backups.md#recovery-time)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Ha még nincs telepítve az Azure PowerShell, [olvassa el az Azure PowerShell-modul telepítése című témakört.](https://docs.microsoft.com/powershell/azure/install-az-ps)

Az adatbázis powershell használatával történő visszaállításához adja meg a következő parancsban szereplő paraméterek értékeit. Ezután futtassa a következő parancsot:

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

Az adatbázis visszaállításához adjon meg egy másik felügyelt példányt, adja meg a célerőforrás-csoport és a célfelügyelt példány nevét is:  

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

További információt a [Restore-AzSqlInstanceDatabase című témakörben talál.](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqlinstancedatabase)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha még nincs telepítve az Azure CLI, [olvassa el az Azure CLI telepítése](/cli/azure/install-azure-cli?view=azure-cli-latest)című témakört.

Az adatbázis visszaállítása az Azure CLI használatával adja meg az értékeket a paraméterek et a következő parancsban. Ezután futtassa a következő parancsot:

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```

Az adatbázis visszaállításához adjon meg egy másik felügyelt példányt, adja meg a célerőforrás-csoport és a felügyelt példány nevét is:  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

A rendelkezésre álló paraméterek részletes magyarázatát a CLI dokumentációjában találja, [amely az adatbázisok felügyelt példányban történő visszaállítását tartalmazza.](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)

---

## <a name="restore-a-deleted-database"></a>Törölt adatbázis visszaállítása

A törölt adatbázis visszaállítása a PowerShell vagy az Azure Portal használatával végezhető el. Ha vissza szeretne állítani egy törölt adatbázist ugyanarra a példányra, használja az Azure Portalon vagy a PowerShellben. Törölt adatbázis visszaállítása egy másik példányra, használja a PowerShell. 

### <a name="portal"></a>Portál 


Felügyelt adatbázis helyreállításához az Azure Portalon, nyissa meg a felügyelt példány áttekintése lapot, és válassza **a Törölt adatbázisok**lehetőséget. Válasszon egy visszaállítani kívánt törölt adatbázist, és írja be a biztonsági másolatból visszaállított adatokkal létrehozandó új adatbázis nevét.

  ![Képernyőkép a törölt Azure SQL-példány adatbázisának visszaállításáról](./media/sql-database-recovery-using-backups/restore-deleted-sql-managed-instance-annotated.png)

### <a name="powershell"></a>PowerShell

Ha egy adatbázist ugyanabba a példányba szeretne visszaállítani, frissítse a paraméterértékeket, majd futtassa a következő PowerShell parancsot: 

```powershell-interactive
$subscriptionId = "<Subscription ID>"
Get-AzSubscription -SubscriptionId $subscriptionId
Select-AzSubscription -SubscriptionId $subscriptionId

$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$deletedDatabaseName = "<Source database name>"
$targetDatabaseName = "<target database name>"

$deletedDatabase = Get-AzSqlDeletedInstanceDatabaseBackup -ResourceGroupName $resourceGroupName `
-InstanceName $managedInstanceName -DatabaseName $deletedDatabaseName

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName
```

Az adatbázis visszaállításához adjon meg egy másik felügyelt példányt, adja meg a célerőforrás-csoport és a célfelügyelt példány nevét is:

```powershell-interactive
$targetResourceGroupName = "<Resource group of target managed instance>"
$targetInstanceName = "<Target managed instance name>"

Restore-AzSqlinstanceDatabase -Name $deletedDatabase.Name `
   -InstanceName $deletedDatabase.ManagedInstanceName `
   -ResourceGroupName $deletedDatabase.ResourceGroupName `
   -DeletionDate $deletedDatabase.DeletionDate `
   -PointInTime UTCDateTime `
   -TargetInstanceDatabaseName $targetDatabaseName `
   -TargetResourceGroupName $targetResourceGroupName `
   -TargetInstanceName $targetInstanceName 
```

## <a name="overwrite-an-existing-database"></a>Meglévő adatbázis felülírása

Meglévő adatbázis felülírásához a következőket kell tennie:

1. Dobja el a felülírni kívánt meglévő adatbázist.
2. Nevezze át a visszaállított időpontban lévő adatbázist az eldobott adatbázis nevére.

### <a name="drop-the-original-database"></a>Az eredeti adatbázis eldobása

Az azure portal, a PowerShell vagy az Azure CLI használatával eldobhatja az adatbázist.

Az adatbázist úgy is eldobhatja, hogy közvetlenül csatlakozik a felügyelt példányhoz, elindítja az SQL Server Management Studio (SSMS) rendszert, majd futtatja a következő Transact-SQL (T-SQL) parancsot:

```sql
DROP DATABASE WorldWideImporters;
```

A felügyelt példányban az alábbi módszerek egyikével csatlakozhat az adatbázishoz:

- [SSMS/Azure Data Studio egy Azure virtuális gépen keresztül](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Pontról helyre](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Nyilvános végpont](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portal"></a>[Portál](#tab/azure-portal)

Az Azure Portalon válassza ki az adatbázist a felügyelt példányból, majd válassza a **Törlés**lehetőséget.

   ![Adatbázis törlése az Azure Portal használatával](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

A következő PowerShell-paranccsal eldobjon egy meglévő adatbázist egy felügyelt példányból:

```powershell
$resourceGroupName = "<Resource group name>"
$managedInstanceName = "<Managed instance name>"
$databaseName = "<Source database>"

Remove-AzSqlInstanceDatabase -Name $databaseName -InstanceName $managedInstanceName -ResourceGroupName $resourceGroupName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A következő Azure CLI-paranccsal eldobjon egy meglévő adatbázist egy felügyelt példányból:

```azurecli-interactive
az sql midb delete -g mygroupname --mi myinstancename -n mymanageddbname
```

---

### <a name="alter-the-new-database-name-to-match-the-original-database-name"></a>Az új adatbázis nevének módosítása az eredeti adatbázisnévnek megfelelően

Csatlakozzon közvetlenül a felügyelt példányhoz, és indítsa el az SQL Server Management Studio-t. Ezután futtassa a következő Transact-SQL (T-SQL) lekérdezést. A lekérdezés a visszaállított adatbázis nevét felülírni kívánt adatbázisra módosítja.

```sql
ALTER DATABASE WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```

A felügyelt példányban az alábbi módszerek egyikével csatlakozhat az adatbázishoz:

- [Azure virtuális gép](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Pontról helyre](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Nyilvános végpont](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>További lépések

További információ az [automatikus biztonsági mentésekről.](sql-database-automated-backups.md)
