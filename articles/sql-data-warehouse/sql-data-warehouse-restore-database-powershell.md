---
title: Állítsa vissza az Azure SQL Data Warehouse (PowerShell) |} A Microsoft Docs
description: PowerShell-feladatok az Azure SQL Data Warehouse visszaállításához.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: dbf86bfc82706586dfb438b167d13b32b6a4b968
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2018
ms.locfileid: "42056857"
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
**A dtu-k kapacitásának ellenőrzése.** Minden egyes SQL Data Warehouse egy SQL Servert (például a myserver.database.windows.net) rendelkezik alapértelmezett DTU-kvótát üzemelteti.  SQL Data Warehouse a visszaállításhoz, ellenőrizze, hogy az az SQL Servernek elég fennmaradó DTU-kvótába a visszaállított adatbázis számára. Szükséges DTU kiszámításához, vagy kérjen további DTU kezelésével kapcsolatos információkért lásd: [DTU-kvóta módosítási kérése][Request a DTU quota change].

### <a name="install-powershell"></a>A PowerShell telepítése
Annak érdekében, hogy az Azure PowerShell használata az SQL Data warehouse-ba, szüksége lesz Azure PowerShell 1.0-s vagy újabb verzió telepítéséhez.  A verzió ellenőrzéséhez futtassa **Get-Module - ListAvailable-Name-AzureRM**.  A legújabb verzió telepíthető [Microsoft Webplatform-telepítő][Microsoft Web Platform Installer].  A legújabb verzió telepítésével kapcsolatban lásd: [How to install and configure Azure PowerShell][How to install and configure Azure PowerShell] (Az Azure PowerShell telepítése és konfigurálása).

## <a name="restore-an-active-or-paused-database"></a>Egy aktív vagy szüneteltetett adatbázis visszaállítása
Adatbázis visszaállítása egy pillanatkép használata a [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] PowerShell-parancsmagot.

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

Connect-AzureRmAccount
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
> A visszaállítás befejezését követően a helyreállított adatbázis konfigurálhatja az alábbi [konfigurálása az adatbázis helyreállítása után][Configure your database after recovery].
> 
> 

## <a name="restore-a-deleted-database"></a>Törölt adatbázis visszaállítása
Törölt adatbázis visszaállítása, használja a [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] parancsmagot.

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

Connect-AzureRmAccount
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
> A visszaállítás befejezését követően a helyreállított adatbázis konfigurálhatja az alábbi [konfigurálása az adatbázis helyreállítása után][Configure your database after recovery].
> 
> 

## <a name="restore-from-an-azure-geographical-region"></a>Egy Azure-földrajzi régió visszaállítása
Adatbázis helyreállítása, használja a [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] parancsmagot.

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
Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>"

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
[Restore-AzureRmSqlDatabase]: https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
