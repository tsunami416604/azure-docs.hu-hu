---
title: Meglévő adattárház visszaállítása
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
ms.custom: seo-lt-2019
ms.openlocfilehash: d0bcf9ca6373984989d24efd2af4ffbbb19c5548
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759686"
---
# <a name="restore-an-existing-azure-sql-data-warehouse"></a>Meglévő Azure SQL Data Warehouse visszaállítása

Ebből a cikkből megtudhatja, hogyan állíthatja vissza egy meglévő SQL Data Warehouset a Azure Portal és a PowerShell használatával:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Ellenőrizze a DTU kapacitását.** Mindegyik SQL Data Warehouse egy SQL-kiszolgáló (például myserver.database.windows.net) üzemelteti, amely rendelkezik alapértelmezett DTU-kvótával. Ellenőrizze, hogy az SQL-kiszolgáló rendelkezik-e elegendő fennmaradó DTU-kvótával az adatbázis visszaállításához. A szükséges DTU kiszámításához, illetve további DTU igényléséhez lásd: [DTU-kvóta megváltoztatásának kérése](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Előzetes teendők

1. Győződjön meg arról, hogy a [Azure PowerShell telepítését](https://docs.microsoft.com/powershell/azure/overview)végzi.
2. Rendelkeznie kell egy meglévő visszaállítási ponttal, amelyet vissza szeretne állítani. Ha új visszaállítást szeretne létrehozni, tekintse meg [az új felhasználó által definiált visszaállítási pont létrehozását ismertető oktatóanyagot](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-data-warehouse-through-powershell"></a>Meglévő adattárház visszaállítása a PowerShell használatával

Egy meglévő adattárház visszaállítási pontról történő visszaállításához használja a [Restore-AzSqlDatabase PowerShell-](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) parancsmagot.

1. Nyissa meg a PowerShellt.

2. Kapcsolódjon az Azure-fiókjához, és sorolja fel a fiókjához társított összes előfizetést.

3. Válassza ki azt az előfizetést, amely a visszaállítani kívánt adatbázist tartalmazza.

4. Az adatraktár visszaállítási pontjainak listázása.

5. Válassza ki a kívánt visszaállítási pontot a RestorePointCreationDate használatával.

6. Állítsa vissza az adatraktárat a kívánt visszaállítási pontra a [Restore-AzSqlDatabase PowerShell-](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) parancsmag használatával.
        1. Ha másik logikai kiszolgálóra szeretné visszaállítani a SQL Data Warehouse, akkor ügyeljen arra, hogy a másik logikai kiszolgáló nevét adja meg.  Ez a logikai kiszolgáló más erőforráscsoport és régió is lehet.
        2. Másik előfizetésre való visszaállításhoz használja az "áthelyezés" gombot a logikai kiszolgáló másik előfizetésre való áthelyezéséhez.

7. Ellenőrizze, hogy a visszaállított adattárház online állapotban van-e.

8. A visszaállítás befejezése után a helyreállított adattárházat a [helyreállítás után az adatbázis konfigurálása](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)után állíthatja be.

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

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Navigáljon ahhoz a SQL Data Warehouse, amelyet vissza szeretne állítani.
3. Az Áttekintés panel tetején válassza a **visszaállítás**lehetőséget.

    ![ Visszaállítás áttekintése](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Válassza ki az **Automatikus visszaállítási pontokat** vagy a **felhasználó által definiált visszaállítási pontokat**. Ha az adatraktár nem rendelkezik Automatikus visszaállítási ponttal, várjon néhány órát, vagy hozzon létre egy felhasználó által definiált visszaállítási pontot a visszaállítás előtt. Felhasználó által definiált visszaállítási pontok esetén válasszon ki egy meglévőt, vagy hozzon létre újat. A **kiszolgáló**esetében választhat egy logikai kiszolgálót egy másik erőforráscsoporthoz és régióban, vagy létrehozhat egy újat. Az összes paraméter megadása után kattintson a **felülvizsgálat + visszaállítás**elemre.

    ![Automatikus visszaállítási pontok](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Következő lépések
- [Törölt adattárház visszaállítása](sql-data-warehouse-restore-deleted-dw.md)
- [Visszaállítás földrajzi biztonsági mentési adattárházból](sql-data-warehouse-restore-from-geo-backup.md)

 