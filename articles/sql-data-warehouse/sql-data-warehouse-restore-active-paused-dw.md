---
title: Meglévő Azure SQL Data Warehouse visszaállítása | Microsoft Docs
description: Útmutató egy meglévő Azure SQL Data Warehouse visszaállításához.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 7470dd96109823057a174d2ecf097583dcb51898
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575270"
---
# <a name="restore-an-existing-azure-sql-data-warehouse"></a>Meglévő Azure SQL Data Warehouse visszaállítása

Ebből a cikkből megtudhatja, hogyan állíthatja vissza egy meglévő SQL Data Warehouset a Azure Portal és a PowerShell használatával:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Ellenőrizze a DTU kapacitását.** Mindegyik SQL Data Warehouse egy SQL-kiszolgáló (például myserver.database.windows.net) üzemelteti, amely rendelkezik alapértelmezett DTU-kvótával. Ellenőrizze, hogy az SQL-kiszolgáló rendelkezik-e elegendő fennmaradó DTU-kvótával az adatbázis visszaállításához. A szükséges DTU kiszámításához, illetve további DTU igényléséhez lásd: [DTU-kvóta][Request a DTU quota change]megváltoztatásának kérése.

## <a name="before-you-begin"></a>Előkészületek

1. Győződjön meg arról, hogy a [Azure PowerShell telepítését][Install Azure PowerShell]végzi.
2. Rendelkeznie kell egy meglévő visszaállítási ponttal, amelyet vissza szeretne állítani. Ha új visszaállítást szeretne létrehozni, tekintse meg [az új felhasználó által definiált visszaállítási pont létrehozását ismertető oktatóanyagot][the tutorial to create a new user-defined restore point].

## <a name="restore-an-existing-data-warehouse-through-powershell"></a>Meglévő adattárház visszaállítása a PowerShell használatával

Egy meglévő adattárház visszaállítási pontról történő visszaállításához használja a [Restore-AzSqlDatabase PowerShell-][Restore-AzSqlDatabase] parancsmagot.

1. Nyissa meg a PowerShellt.

2. Kapcsolódjon az Azure-fiókjához, és sorolja fel a fiókjához társított összes előfizetést.

3. Válassza ki azt az előfizetést, amely a visszaállítani kívánt adatbázist tartalmazza.

4. Az adatraktár visszaállítási pontjainak listázása.

5. Válassza ki a kívánt visszaállítási pontot a RestorePointCreationDate használatával.

6. Állítsa vissza az adatraktárat a kívánt visszaállítási pontra a [Restore-AzSqlDatabase PowerShell-][Restore-AzSqlDatabase] parancsmag használatával.
        1. Ha másik logikai kiszolgálóra szeretné visszaállítani a SQL Data Warehouse, akkor ügyeljen arra, hogy a másik logikai kiszolgáló nevét adja meg.  Ez a logikai kiszolgáló más erőforráscsoport és régió is lehet.
        2. Másik előfizetésre való visszaállításhoz használja az "áthelyezés" gombot a logikai kiszolgáló másik előfizetésre való áthelyezéséhez.

7. Ellenőrizze, hogy a visszaállított adattárház online állapotban van-e.

8. A visszaállítás befejezése után a helyreállított adattárházat a [helyreállítás után az adatbázis konfigurálása][Configure your database after recovery]után állíthatja be.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different logical server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceTargetGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-data-warehouse-through-the-azure-portal"></a>Meglévő adattárház visszaállítása a Azure Portal

1. Jelentkezzen be az [Azure Portalra][Azure portal].
2. Navigáljon ahhoz a SQL Data Warehouse, amelyet vissza szeretne állítani.
3. Az Áttekintés panel tetején válassza a **visszaállítás**lehetőséget.

    ![ Visszaállítás áttekintése](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Válassza ki az **Automatikus visszaállítási pontokat** vagy a **felhasználó által definiált visszaállítási pontokat**. Ha az adatraktár nem rendelkezik Automatikus visszaállítási ponttal, várjon néhány órát, vagy hozzon létre egy felhasználó által definiált visszaállítási pontot a visszaállítás előtt. Felhasználó által definiált visszaállítási pontok esetén válasszon ki egy meglévőt, vagy hozzon létre újat. A **kiszolgáló**esetében választhat egy logikai kiszolgálót egy másik erőforráscsoporthoz és régióban, vagy létrehozhat egy újat. Az összes paraméter megadása után kattintson a **felülvizsgálat + visszaállítás**elemre.

    ![Automatikus visszaállítási pontok](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>További lépések
- [Törölt adattárház visszaállítása][Restore a deleted data warehouse]
- [Visszaállítás földrajzi biztonsági mentési adattárházból][Restore from a geo-backup data warehouse]
 
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
[the tutorial to create a new user-defined restore point]:../sql-data-warehouse/sql-data-warehouse-restore-points.md
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md

<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
