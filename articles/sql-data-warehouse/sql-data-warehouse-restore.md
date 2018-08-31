---
title: Állítsa vissza az Azure SQL Data Warehouse |} A Microsoft Docs
description: Útmutató az Azure SQL Data Warehouse visszaállítása módja.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/29/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 583346f2297f590d8e9484c0a3c19c947de7f740
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2018
ms.locfileid: "43191178"
---
# <a name="restoring-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse visszaállítása 
Ebben a cikkben megtudhatja, hogyan végezze el a következőket:

- Visszaállítási pont létrehozása
- Automatikus visszaállítási pont vagy egy felhasználói visszaállítási pont visszaállítása
- Törölt adatbázis visszaállítása
- Georedundáns biztonsági másolatból
- Az adattárház másolatának létrehozása egy felhasználói visszaállítási pont

## <a name="before-you-begin"></a>Előkészületek
**A dtu-k kapacitásának ellenőrzése.** Minden egyes SQL Data Warehouse egy SQL Servert (például a myserver.database.windows.net) rendelkezik alapértelmezett DTU-kvótát üzemelteti.  SQL data warehouse a visszaállításhoz, ellenőrizze, hogy az az SQL Servernek elég fennmaradó DTU-kvótába a visszaállított adatbázis számára. Szükséges DTU kiszámításához, vagy kérjen további DTU kezelésével kapcsolatos információkért lásd: [DTU-kvóta módosítási kérése][Request a DTU quota change].

# <a name="restore-through-powershell"></a>Állítsa vissza a PowerShell-lel

## <a name="install-powershell"></a>A PowerShell telepítése
Annak érdekében, hogy az Azure PowerShell használata az SQL Data warehouse-ba, szüksége lesz Azure PowerShell 1.0-s vagy újabb verzió telepítéséhez.  A verzió ellenőrzéséhez futtassa **Get-Module - ListAvailable-Name-AzureRM**.  A legújabb verzió telepíthető [Microsoft Webplatform-telepítő][Microsoft Web Platform Installer].  A legújabb verzió telepítésével kapcsolatban lásd: [How to install and configure Azure PowerShell][How to install and configure Azure PowerShell] (Az Azure PowerShell telepítése és konfigurálása).

## <a name="restore-an-active-or-paused-database"></a>Egy aktív vagy szüneteltetett adatbázis visszaállítása
Egy adatbázis visszaállítása egy visszaállítási pont használatát a [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] PowerShell-parancsmagot.

1. Nyissa meg a Windows PowerShellt.

2. Azure-fiókjához csatlakozhat, és a fiókjához társított összes előfizetés listázása.

3. Válassza ki az előfizetést, amely tartalmazza az adatbázist vissza kell állítani.

4. Az adatbázis visszaállítási pontok listája.

5. Válassza ki a kívánt visszaállítási pont a RestorePointCreationDate használatával.

   > [!NOTE]
   > Visszaállításakor, megadhat egy másik ServiceObjectiveName (DWU) vagy egy másik kiszolgálóra, egy másik régióban található.

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

## <a name="copy-your-data-warehouse-with-user-defined-restore-points"></a>Másolja ki és a felhasználó által definiált visszaállítási pontok
Egy adatbázis visszaállítása egy felhasználói visszaállítási pont használatát a [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] PowerShell-parancsmagot.

1. Nyissa meg a Windows PowerShellt.
2. Azure-fiókjához csatlakozhat, és a fiókjához társított összes előfizetés listázása.
3. Válassza ki az előfizetést, amely tartalmazza az adatbázist vissza kell állítani.
4. Az adatbázis egy azonnali másolásához a visszaállítási pont létrehozása
5. Nevezze át az adatbázis egy ideiglenes névre.
5. A megadott RestorePointLabel által a legutóbbi visszaállítási pont beolvasása.
6. A visszaállítás kezdeményezni az adatbázis erőforrás-azonosító beszerzése
6. Állítsa vissza az adatbázist a kívánt helyreállítási pontot.
7. Győződjön meg arról, hogy a visszaállított adatbázis online állapotban.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$TempDatabaseName = "<YourTemporaryDatabaseName>"
$Label = "<YourRestorePointLabel"

Connect-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzureRmSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

# Rename the database to a temporary name
Set-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -NewName $TempDatabaseName

# Get the most recent restore point with the specified label
$LabelledRestorePoint = Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName | where {$_.RestorePointLabel -eq $Label} | sort {$_.RestorePointCreationDate} | select -Last 1

# Get the resource id of the database
$ResourceId = (Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $TempDatabaseName).ResourceId

# Restore the database to its original name from the labelled restore point from the temporary database
$RestoredDatabase = Restore-AzureRmSqlDatabase -FromPointInTimeBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -ResourceId $ResourceId -PointInTime $LabelledRestorePoint.RestorePointCreationDate -TargetDatabaseName $DatabaseName

# Verify the status of restored database
$RestoredDatabase.status

# The original temporary database can be deleted at this point

```

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

## <a name="restore-from-an-azure-geographical-region"></a>Egy Azure-földrajzi régió visszaállítása
Adatbázis helyreállítása, használja a [Restore-AzureRmSqlDatabase] [ Restore-AzureRmSqlDatabase] parancsmagot.

> [!NOTE]
> Gen2-re a geo-visszaállítás elvégzése! Ehhez adjon meg egy Gen2 ServiceObjectiveName (pl. dw1000 értékre**c**), mint egy nem kötelező paraméter.
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

A helyreállított adatbázis TDE-kompatibilis akkor lesz a forrásadatbázis TDE-t.

# <a name="restore-through-the-azure-portal"></a>Állítsa vissza az Azure Portalon keresztül

## <a name="create-a-user-defined-restore-point"></a>Hozzon létre egy felhasználói visszaállítási pont
1. Jelentkezzen be az [Azure Portalra][Azure portal].

2. Keresse meg az SQL data warehouse egy visszaállítási pontot létrehozni kívánt.

3. Válassza ki az áttekintő panel tetején lévő **+ új visszaállítási pont**.

    ![Új visszaállítási pont](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)
    
4. Adjon meg egy nevet a visszaállítási pontot.

    ![Visszaállítási pont nevét](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database"></a>Egy aktív vagy szüneteltetett adatbázis visszaállítása
1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. Keresse meg az SQL data warehouse, a visszaállítani kívánt.
3. Válassza ki az áttekintő panel tetején lévő **visszaállítása**.

    ![ Visszaállítás áttekintése](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)
    
4. Ezek közül bármelyikre **automatikus visszaállítási pontok** vagy **felhasználói visszaállítási pontok**.

    ![Automatikus visszaállítási pontok](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)
    
5. A felhasználó által megadott visszaállítási pontok **jelöljön ki egy visszaállítási pontot** vagy **hozzon létre egy új, felhasználó által definiált visszaállításipont**.

    ![Felhasználó által definiált visszaállítási pontok](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-a-deleted-database"></a>Törölt adatbázis visszaállítása
1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. Keresse meg az SQL server, a törölt adatbázisok tárolt.
3. A tartalomjegyzékből válassza ki a törölt adatbázisok ikonra.

    ![A törölt adatbázisok](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_0.png)
    
4. Válassza ki a visszaállítani kívánt törölt adatbázisok.

    ![Válassza ki a törölt adatbázisok](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_1.png)
    
5. Adjon meg egy új adatbázisnevet.

    ![Adja meg az adatbázis neve](./media/sql-data-warehouse-restore-database-portal/restoring_deleted_2.png)

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
