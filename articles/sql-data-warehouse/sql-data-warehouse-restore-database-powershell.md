---
title: Állítsa vissza az Azure SQL Data Warehouse (PowerShell) |} A Microsoft Docs
description: PowerShell-feladatok az Azure SQL Data Warehouse visszaállításához.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6324eb11b334fd6a00e30d6f2fc6d1bec3f7a82c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57870823"
---
# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Állítsa vissza az Azure SQL Data Warehouse (PowerShell)
> [!div class="op_single_selector"]
> * [– Áttekintés][Overview]
> * [Portal][Portal]
> * [PowerShell][PowerShell]
> * [REST][REST]
> 
> 

Ebben a cikkben megtudhatja hogyan állíthatja vissza az Azure SQL Data Warehouse PowerShell-lel.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**A dtu-k kapacitásának ellenőrzése.** Minden egyes SQL Data Warehouse egy SQL Servert (például a myserver.database.windows.net) rendelkezik alapértelmezett DTU-kvótát üzemelteti.  SQL Data Warehouse a visszaállításhoz, ellenőrizze, hogy az az SQL Servernek elég fennmaradó DTU-kvótába a visszaállított adatbázis számára. Szükséges DTU kiszámításához, vagy kérjen további DTU kezelésével kapcsolatos információkért lásd: [DTU-kvóta módosítási kérése][Request a DTU quota change].

### <a name="install-powershell"></a>A PowerShell telepítése
Annak érdekében, hogy az Azure PowerShell használata az SQL Data warehouse-ba, szüksége lesz az Azure PowerShell telepítéséhez.  A verzió ellenőrzéséhez futtassa **Get-Module - ListAvailable-Name Az**.  A legújabb verzió telepítésével kapcsolatban lásd: [How to install and configure Azure PowerShell][How to install and configure Azure PowerShell] (Az Azure PowerShell telepítése és konfigurálása).

## <a name="restore-an-active-or-paused-database"></a>Egy aktív vagy szüneteltetett adatbázis visszaállítása
Adatbázis visszaállítása egy pillanatkép használata a [Restore-AzSqlDatabase] [ Restore-AzSqlDatabase] PowerShell-parancsmagot.

1. Nyissa meg a Windows PowerShellt.
2. Azure-fiókjához csatlakozhat, és a fiókjához társított összes előfizetés listázása.
3. Válassza ki az előfizetést, amely tartalmazza az adatbázist vissza kell állítani.
4. Az adatbázis visszaállítási pontok listája.
5. Válassza ki a kívánt visszaállítási pont a RestorePointCreationDate használatával.
6. Állítsa vissza az adatbázist a kívánt helyreállítási pontot.
7. Győződjön meg arról, hogy a visszaállított adatbázis online állapotban.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName)).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

> [!NOTE]
> A visszaállítás befejezését követően a helyreállított adatbázis konfigurálhatja az alábbi [konfigurálása az adatbázis helyreállítása után][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>Törölt adatbázis visszaállítása
Törölt adatbázis visszaállítása, használja a [Restore-AzSqlDatabase] [ Restore-AzSqlDatabase] parancsmagot.

1. Nyissa meg a Windows PowerShellt.
2. Azure-fiókjához csatlakozhat, és a fiókjához társított összes előfizetés listázása.
3. Válassza ki az előfizetést, amelyhez vissza kell állítani a törölt adatbázist tartalmaz.
4. Az adott törölt adatbázis beolvasása.
5. A törölt adatbázis visszaállítása.
6. Győződjön meg arról, hogy a visszaállított adatbázis online állapotban.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

> [!NOTE]
> A visszaállítás befejezését követően a helyreállított adatbázis konfigurálhatja az alábbi [konfigurálása az adatbázis helyreállítása után][Configure your database after recovery].
> 
> 

## <a name="restore-from-an-azure-geographical-region"></a>Egy Azure-földrajzi régió visszaállítása
Adatbázis helyreállítása, használja a [Restore-AzSqlDatabase] [ Restore-AzSqlDatabase] parancsmagot.

> [!NOTE]
> A geo-visszaállítás, az optimalizált számítási teljesítményszint végezhetők el! Ehhez adja meg az optimalizált számítási ServiceObjectiveName, mint egy nem kötelező paraméter. 
>
> 

1. Nyissa meg a Windows PowerShellt.
2. Azure-fiókjához csatlakozhat, és a fiókjához társított összes előfizetés listázása.
3. Válassza ki az előfizetést, amely tartalmazza az adatbázist vissza kell állítani.
4. A helyreállítani kívánt adatbázis beolvasása.
5. A helyreállítási kérelem, az adatbázis létrehozásához.
6. Ellenőrizze a geo-visszaállított adatbázis állapotát.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> A visszaállítás befejezése után az adatbázis beállítása: [konfigurálása az adatbázis helyreállítása után][Configure your database after recovery].
> 
> 

A helyreállított adatbázis TDE-kompatibilis akkor lesz a forrásadatbázis TDE-t.

## <a name="next-steps"></a>További lépések
Az üzletmenet-folytonossági funkciókat az Azure SQL Database-kiadás kapcsolatos további információkért olvassa el a [Azure SQL Database üzletmenet folytonossága – áttekintés][Azure SQL Database business continuity overview].

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
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
