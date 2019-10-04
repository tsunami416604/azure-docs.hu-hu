---
title: SQL Database felügyelt példány – időponthoz történő visszaállítás | Microsoft Docs
description: SQL felügyelt példányban lévő adatbázis visszaállítása egy korábbi időpontra.
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
ms.openlocfilehash: 67f13d16dcf6bbe4fa13fe3a6e78d3e4d61e1999
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862134"
---
# <a name="restore-a-sql-managed-instance-database-to-a-previous-point-in-time"></a>SQL felügyelt példány adatbázisának visszaállítása egy korábbi időpontra

Az időponthoz tartozó visszaállítás (PITR) lehetővé teszi, hogy egy adatbázist egy másik adatbázis másolata hozzon létre egy korábbi időpontban. Ez a cikk azt ismerteti, hogyan hajtható végre egy adatbázis egy adott időpontban történő visszaállítása felügyelt példányban.

Az időponthoz való visszaállítás olyan helyreállítási forgatókönyvekben használható, mint például a hibák által okozott incidensek, az adatok helytelen betöltése, a kritikus adatok törlése és egyéb problémák, valamint egyszerűen tesztelési vagy auditálási célokra. Az adatbázis beállításaitól függően a biztonságimásolat-fájlok 7 és 35 nap közötti időtartamra vannak tárolva.

Az időponthoz való visszaállítás a következőket eredményezheti:

- Adatbázis visszaállítása meglévő adatbázisból.
- Adatbázis visszaállítása törölt adatbázisból.

Emellett a felügyelt példányok esetében az időponthoz tartozó visszaállítás a következőket is felhasználhatja: 

- Adatbázis visszaállítása ugyanarra a felügyelt példányra.
- Adatbázis visszaállítása egy másik felügyelt példányra.


> [!NOTE]
> Egy teljes felügyelt példány időponthoz tartozó visszaállítása nem lehetséges. A jelen cikkben ismertetett módon a felügyelt példányokon üzemeltetett adatbázis időponthoz tartozó visszaállítása is lehetséges.


## <a name="limitations"></a>Korlátozások

Egy másik felügyelt példányra történő visszaállításkor mindkét példánynak ugyanabban az előfizetésben és régióban kell lennie. A régiók közötti és az előfizetések közötti visszaállítások jelenleg nem támogatottak.

> [!WARNING]
> Ügyeljen arra, hogy a felügyelt példány tárolási mérete – az adatvisszaállítás méretétől függően – kifogyhat a példányok tárolóján. Ha nincs elég hely a visszaállított adatmennyiséghez, használjon alternatív megközelítést.

A következő táblázat a felügyelt példányok időponthoz kapcsolódó helyreállítási forgatókönyveit mutatja be:

|           |Meglévő adatbázis visszaállítása| Meglévő adatbázis visszaállítása|Az eldobott adatbázis visszaállítása| Az eldobott adatbázis visszaállítása|
|:----------|:----------|:----------|:----------|:----------|
|Cél| Ugyanez a MI|Egy másik MI |Ugyanez a MI|Egy másik MI |
|Azure Portal| Igen|Nem |Nem|Nem|
|Azure CLI|Igen |Igen |Nem|Nem|
|PowerShell| Igen|Igen |Igen|Igen|


## <a name="restore-existing-database"></a>Meglévő adatbázis visszaállítása

A Azure Portal, a PowerShell vagy az Azure CLI használatával állítson vissza egy meglévő adatbázist ugyanarra a példányra. Állítsa vissza az adatbázist egy másik példányra a PowerShell vagy az Azure CLI használatával a felügyelt példány és az erőforráscsoport tulajdonságainak megadásával. Ha ezek a paraméterek nincsenek megadva, alapértelmezés szerint a rendszer visszaállítja az adatbázist a meglévő példányra. A másik példányra való visszaállítás jelenleg nem támogatott a Azure Portalon keresztül. 

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
1. Navigáljon a felügyelt példányhoz, és válassza ki a visszaállítani kívánt adatbázist. 
1. Az adatbázis lapon válassza a **visszaállítás** lehetőséget. 

    ![Meglévő adatbázis visszaállítása](media/sql-database-managed-instance-point-in-time-restore/restore-database-to-mi.png)

1. A **visszaállítás** lapon válassza ki azt a dátumot és időpontot az előzményekben, amelybe vissza kívánja állítani az adatbázist.
1. Az adatbázis visszaállításához kattintson a **Confirm (megerősítés** ) gombra. Ezzel elindítja a visszaállítási folyamatot, amely létrehoz egy új adatbázist, és az eredeti adatbázisból származó adatokkal tölti fel az adatokat a kívánt időpontban. A helyreállítási folyamattal kapcsolatos további információkért lásd: [helyreállítás ideje](sql-database-recovery-using-backups.md#recovery-time). 

1. Felügyelt példány keresése
1. Válassza ki a visszaállítani kívánt adatbázist
1. Az adatbázis képernyőn kattintson a visszaállítási művelet elemre.
1. A visszaállítási képernyőn válassza ki a dátum és idő pontot az előzményekben, amelyekhez az adatbázist állítja vissza
1. A jóváhagyást követően a visszaállítási folyamat elindul, és az adatbázis méretétől függően létrejön az új adatbázis, és az eredeti adatbázisból származó adatokkal lesz feltöltve a kívánt időpontban. A visszaállítási folyamat során a biztonsági mentések című cikk használatával végezheti el a helyreállítást.


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Ha még nincs Azure PowerShell telepítve, tekintse meg [a Azure PowerShell modul telepítését](https://docs.microsoft.com/powershell/azure/install-az-ps)ismertető témakört.

Az adatbázis PowerShell használatával történő visszaállításához frissítse a paramétereket az értékekkel, és futtassa a következő parancsot:

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

Az adatbázis egy másik felügyelt példányra való visszaállításához állítsa be a cél erőforráscsoport nevét és a cél felügyelt példányának nevét.  

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

Ha az adatbázist az Azure CLI használatával szeretné visszaállítani, frissítse a paramétereket az értékekkel, és futtassa a következő parancsot:


```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename |
-n mymanageddbname --dest-name targetmidbname --time "2018-05-20T05:34:22"
```


Az adatbázis egy másik felügyelt példányra való visszaállításához állítsa be a cél erőforráscsoport nevét és a cél felügyelt példányának nevét.  

```azurecli-interactive
az sql midb restore -g mygroupname --mi myinstancename -n mymanageddbname |
       --dest-name targetmidbname --time "2018-05-20T05:34:22" |
       --dest-resource-group mytargetinstancegroupname |
       --dest-mi mytargetinstancename
```

Az elérhető paraméterek részletes ismertetését lásd: a [felügyelt példány parancssori](https://docs.microsoft.com/cli/azure/sql/midb?view=azure-cli-latest#az-sql-midb-restore)felülete. 

---

## <a name="restore-a-deleted-database"></a>Törölt adatbázis visszaállítása 
 
A törölt adatbázisok visszaállítása csak a PowerShell használatával végezhető el. Az adatbázis visszaállítható ugyanarra a példányra vagy egy másik példányra. 

A törölt adatbázisok PowerShell használatával történő visszaállításához frissítse a paramétereket az értékekkel, és futtassa a következő parancsot:

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

Ha vissza szeretné állítani a törölt adatbázist egy másik példányra, módosítsa az erőforráscsoport nevét és a felügyelt példány nevét.

A Location paraméternek meg kell egyeznie az erőforráscsoport és a felügyelt példány helyével.

```powershell-interactive
$resourceGroupName = "<Second resource group name>"
$managedInstanceName = "<Second managed instance name>"

$location = "West Europe"

$restoredDBName = "WorldWideImportersPITR"
$resource_id = "subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Sql/managedInstances/$managedInstanceName/databases/$restoredDBName"

New-AzResource -Location $location -Properties $properties `
        -ResourceId $resource_id -ApiVersion "2017-03-01-preview" -Force
```

## <a name="overwrite-existing-database"></a>Meglévő adatbázis felülírása 
 
Meglévő adatbázis felülírásához a következőket kell tennie:

1. DOBJA el a felülírni kívánt meglévő adatbázist.
1. Nevezze át az időponthoz visszaállított adatbázist az eldobott adatbázis nevére. 


### <a name="drop-original-database"></a>Eredeti adatbázis eldobása 
 
Az adatbázis eldobása a Azure Portal, a PowerShell vagy az Azure CLI használatával végezhető el. 

Az adatbázist elhúzhatja úgy is, hogy közvetlenül a felügyelt példányhoz csatlakozik, elindítja SQL Server Management Studiot (SSMS), és futtatja az alábbi Transact-SQL (T-SQL) parancsot.

```sql
DROP DATABASE WorldWideImporters;
```

A felügyelt példány-adatbázishoz való kapcsolódáshoz használja az alábbi módszerek egyikét: 

- [SQL virtuális gép](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Pont – hely kapcsolat](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Nyilvános végponthoz](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

# <a name="portaltabazure-portal"></a>[Portál](#tab/azure-portal)

A Azure Portal válassza ki az adatbázist a felügyelt példányból, és válassza a **Törlés**lehetőséget.

   ![Meglévő adatbázis visszaállítása](media/sql-database-managed-instance-point-in-time-restore/delete-database-from-mi.png)

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


### <a name="alter-new-database-name-to-original"></a>Új adatbázis nevének módosítása az eredetire

Kapcsolódjon közvetlenül a felügyelt példányhoz, indítsa el SQL Server Management Studio, majd hajtsa végre a következő Transact-SQL (T-SQL) lekérdezést, hogy módosítsa a visszaállított adatbázis nevét a felülírni kívánt eldobott adatbázisra. 


```sql
ALTER WorldWideImportersPITR MODIFY NAME = WorldWideImporters;
```


A felügyelt példány-adatbázishoz való kapcsolódáshoz használja az alábbi módszerek egyikét: 

- [SQL virtuális gép](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-vm)
- [Pont – hely kapcsolat](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-configure-p2s)
- [Nyilvános végponthoz](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-public-endpoint-configure)

## <a name="next-steps"></a>További lépések

Ismerje meg a [hosszú távú adatmegőrzést](sql-database-long-term-retention.md) és az [automatizált biztonsági mentéseket](sql-database-automated-backups.md). 
