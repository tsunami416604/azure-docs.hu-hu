---
title: Meglévő dedikált SQL-készlet visszaállítása az Azure szinapszis Analyticsben
description: Útmutató egy meglévő dedikált SQL-készlet visszaállításához az Azure szinapszis Analyticsben.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 0c3fd0aee0a70743db721f469d91f269b9764e5e
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94577549"
---
# <a name="restore-an-existing-dedicated-sql-pool-in-azure-synapse-analytics"></a>Meglévő dedikált SQL-készlet visszaállítása az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, hogyan állíthatja vissza egy meglévő dedikált SQL-készletet az Azure szinapszis Analyticsben az Azure Portal és a PowerShell használatával.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Ellenőrizze a DTU kapacitását.** Mindegyik készletet egy [logikai SQL-kiszolgáló](../../azure-sql/database/logical-servers.md) (például MyServer.database.Windows.net) üzemelteti, amely rendelkezik alapértelmezett DTU-kvótával. Ellenőrizze, hogy a kiszolgáló rendelkezik-e elegendő fennmaradó DTU-kvótával az adatbázis visszaállításához. A szükséges DTU kiszámításához, illetve további DTU igényléséhez lásd: [DTU-kvóta megváltoztatásának kérése](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="before-you-begin"></a>Előkészületek

1. Győződjön meg arról, hogy a [Azure PowerShell telepítését](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)végzi.
2. Rendelkeznie kell egy meglévő visszaállítási ponttal, amelyet vissza szeretne állítani. Ha új visszaállítást szeretne létrehozni, tekintse meg [az új felhasználó által definiált visszaállítási pont létrehozását ismertető oktatóanyagot](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-dedicated-sql-pool-through-powershell"></a>Meglévő dedikált SQL-készlet visszaállítása a PowerShell-lel

Ha egy meglévő dedikált SQL-készletet szeretne visszaállítani egy visszaállítási pontról, használja a [Restore-AzSqlDatabase PowerShell-](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancsmagot.

1. Nyissa meg a PowerShellt.

2. Kapcsolódjon az Azure-fiókjához, és sorolja fel a fiókjához társított összes előfizetést.

3. Válassza ki azt az előfizetést, amely a visszaállítani kívánt adatbázist tartalmazza.

4. A dedikált SQL-készlet visszaállítási pontjainak listázása.

5. Válassza ki a kívánt visszaállítási pontot a RestorePointCreationDate használatával.

6. Állítsa vissza a dedikált SQL-készletet a kívánt visszaállítási pontra a [Restore-AzSqlDatabase PowerShell-](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancsmag használatával.

    1. Ha egy másik kiszolgálóra szeretné visszaállítani a dedikált SQL-készletet, ügyeljen arra, hogy a másik kiszolgálónevet adja meg.  Ez a kiszolgáló más erőforráscsoport és régió is lehet.
    2. Másik előfizetésre való visszaállításhoz használja az "áthelyezés" gombot a kiszolgáló másik előfizetésre való áthelyezéséhez.

7. Ellenőrizze, hogy a visszaállított dedikált SQL-készlet online állapotban van-e.

8. A visszaállítás befejezését követően a helyreállított dedikált SQL-készletet a helyreállítás után az [adatbázis konfigurálását](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)követően állíthatja be.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
#$TargetResourceGroupName="<YourTargetResourceGroupName>" # uncomment to restore to a different server.
#$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Or list all restore points
Get-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate "xx/xx/xxxx xx:xx:xx xx"
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Use the following command to restore to a different server
#$TargetResourceGroupName = $Database.ResourceGroupName # for restoring to different server in same resourcegroup 
#$RestoredDatabase = Restore-AzSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

## <a name="restore-an-existing-dedicated-sql-pool-through-the-azure-portal"></a>Meglévő dedikált SQL-készlet visszaállítása a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Navigáljon arra a dedikált helyre, amelyet vissza szeretne állítani.
3. Az Áttekintés panel tetején válassza a **visszaállítás** lehetőséget.

    ![ Visszaállítás áttekintése](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Válassza ki az **Automatikus visszaállítási pontokat** vagy a **felhasználó által definiált visszaállítási pontokat**. Ha a dedikált SQL-készlet nem rendelkezik Automatikus visszaállítási ponttal, várjon néhány órát, vagy hozzon létre egy felhasználó által definiált visszaállítási pontot a visszaállítás előtt. User-Defined visszaállítási pontok esetén válasszon ki egy meglévőt, vagy hozzon létre egy újat. A **Server** esetében választhat egy kiszolgálót egy másik erőforráscsoporthoz és régióban, vagy létrehozhat egy újat. Az összes paraméter megadása után kattintson a **felülvizsgálat + visszaállítás** elemre.

    ![Automatikus visszaállítási pontok](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Következő lépések

- [Törölt dedikált SQL-készlet visszaállítása](sql-data-warehouse-restore-deleted-dw.md)
- [Visszaállítás egy földrajzi biztonsági mentéssel dedikált SQL-készletből](sql-data-warehouse-restore-from-geo-backup.md)
