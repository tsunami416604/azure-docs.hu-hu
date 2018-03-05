---
title: "Állítsa vissza az Azure SQL Data Warehouse (PowerShell) |} Microsoft Docs"
description: "PowerShell feladatok Azure SQL Data Warehouse visszaállítására."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: ac62f154-c8b0-4c33-9c42-f480808aa1d2
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: backup-restore
ms.date: 02/27/2018
ms.author: barbkess
ms.openlocfilehash: 533907ccbae5db3b68ede2ee1b226e663a04cb64
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
---
# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Állítsa vissza az Azure SQL Data Warehouse (PowerShell)
> [!div class="op_single_selector"]
> * [– Áttekintés][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

Ebben a cikkben, megtudhatja, hogyan lehet visszaállítani az PowerShell használata Azure SQL Data Warehouse.

## <a name="before-you-begin"></a>Előkészületek
**A DTU-kapacitásának ellenőrzése.** Minden egyes SQL Data Warehouse egy SQL server (pl. myserver.database.windows.net), amely rendelkezik egy alapértelmezett DTU-kvótáról üzemelteti.  SQL Data Warehouse visszaállítása előtt ellenőrizze, hogy az az SQL server elég fennmaradó DTU-kvótáról az adatbázis visszaállítása folyamatban van. DTU-igény kiszámításához, vagy kérjen további DTU, lásd: [DTU-kvótát Módosítás kérése][Request a DTU quota change].

### <a name="install-powershell"></a>A PowerShell telepítése
Azure PowerShell használatához az SQL Data Warehouse szolgáltatással, akkor telepítse az Azure PowerShell 1.0-ás vagy újabb verziója.  A verzió futtatásával ellenőrizheti **Get-Module - ListAvailable-Name AzureRM**.  A legújabb verzió telepíthető [Microsoft Webplatform-telepítő][Microsoft Web Platform Installer].  A legújabb verzió telepítésével kapcsolatban lásd: [How to install and configure Azure PowerShell][How to install and configure Azure PowerShell] (Az Azure PowerShell telepítése és konfigurálása).

## <a name="restore-an-active-or-paused-database"></a>Az aktív vagy szüneteltetett adatbázis visszaállítása
A pillanatkép használatát egy adatbázis visszaállítása a [visszaállítási-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] PowerShell-parancsmagot.

1. Nyissa meg a Windows PowerShellt.
2. Csatlakozás az Azure-fiókjával, és a fiókjához társított előfizetéseket listája.
3. Válassza ki az előfizetést, állítani az adatbázist.
4. Az adatbázis visszaállítási pontok listája
5. Válassza ki a kívánt visszaállítási pontot a RestorePointCreationDate használatával.
6. Állítsa vissza az adatbázist a kívánt visszaállítási pontot.
7. Győződjön meg arról, hogy a visszaállított adatbázis online állapotban.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> Miután a visszaállítás befejeződött, a helyreállított adatbázis követve konfigurálhatja [konfigurálása az adatbázis helyreállítása után][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>Törölt adatbázis visszaállítása
A törölt adatbázisok visszaállításához használja a [visszaállítási-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] parancsmag.

1. Nyissa meg a Windows PowerShellt.
2. Csatlakozás az Azure-fiókjával, és a fiókjához társított előfizetéseket listája.
3. Válassza ki az előfizetést, amely tartalmazza a visszaállítandó törölt adatbázis.
4. Az adott törölt adatbázis beolvasása.
5. A törölt adatbázis visszaállítása.
6. Győződjön meg arról, hogy a visszaállított adatbázis online állapotban.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> Miután a visszaállítás befejeződött, a helyreállított adatbázis követve konfigurálhatja [konfigurálása az adatbázis helyreállítása után][Configure your database after recovery].
> 
> 

## <a name="restore-from-an-azure-geographical-region"></a>Egy Azure-földrajzi régióban visszaállítása
Adatbázis helyreállítása, használja a [visszaállítási-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] parancsmag.

1. Nyissa meg a Windows PowerShellt.
2. Csatlakozás az Azure-fiókjával, és a fiókjához társított előfizetéseket listája.
3. Válassza ki az előfizetést, állítani az adatbázist.
4. A helyreállítani kívánt adatbázis beolvasása.
5. Az adatbázist a helyreállítási kérelmet létrehozni.
6. Ellenőrizze a földrajzi visszaállított adatbázis állapotát.

```Powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> A visszaállítás befejezése után az adatbázis konfigurálásához lásd: [konfigurálása az adatbázis helyreállítása után][Configure your database after recovery].
> 
> 

A helyreállított adatbázis TDE-kompatibilis akkor lesz a forrásadatbázis TDE engedélyezve van.

## <a name="next-steps"></a>További lépések
Az üzleti folytonosságot biztosító szolgáltatásokat az Azure SQL Database kiadásainak kapcsolatos információkért olvassa el a [Azure SQL Database üzleti folytonosság – áttekintés][Azure SQL Database business continuity overview].

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restore-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
