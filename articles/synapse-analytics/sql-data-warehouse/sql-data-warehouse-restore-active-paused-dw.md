---
title: Meglévő adattárház visszaállítása
description: Útmutató egy meglévő SQL-készlet visszaállításához.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e1d0c5cd850fa0dbacf104d3fcd72d83007df878
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632253"
---
# <a name="restore-an-existing-sql-pool"></a>Meglévő SQL-készlet visszaállítása

Ebben a cikkben megtudhatja, hogyan állíthatja vissza a meglévő SQL-készletet az Azure Synapse Analytics az Azure Portalon és a PowerShell használatával.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Ellenőrizze a DTU-kapacitást.** Minden készletet egy SQL-kiszolgáló üzemeltet (például myserver.database.windows.net), amely alapértelmezett DTU-kvótával rendelkezik. Ellenőrizze, hogy az SQL-kiszolgáló rendelkezik-e elegendő dtu kvótával a visszaállított adatbázishoz. A szükséges DTU kiszámításáról vagy további DTU-kérésről a [DTU-kvótamódosítás kérése.Learn](sql-data-warehouse-get-started-create-support-ticket.md)how to calculate DTU needed or to request more DTU, see Request a DTU quota change .

## <a name="before-you-begin"></a>Előkészületek

1. Győződjön meg arról, hogy [telepíti az Azure PowerShellt.](https://docs.microsoft.com/powershell/azure/overview)
2. Van egy meglévő visszaállítási pontja, amelyből vissza szeretne állítani. Ha új visszaállítást szeretne létrehozni, tekintse meg [az oktatóanyagban, hogy hozzon létre egy új, felhasználó által definiált visszaállítási pontot](sql-data-warehouse-restore-points.md).

## <a name="restore-an-existing-sql-pool-through-powershell"></a>Meglévő SQL-készlet visszaállítása a PowerShellen keresztül

Meglévő SQL-készlet visszaállítása visszaállítási pontból használja a [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) PowerShell-parancsmag.

1. Nyissa meg a PowerShellt.

2. Csatlakozzon az Azure-fiókjához, és sorolja fel a fiókjához társított összes előfizetést.

3. Válassza ki a visszaállítandó adatbázist tartalmazó előfizetést.

4. Sorolja fel az SQL-készlet visszaállítási pontjait.

5. Válassza ki a kívánt visszaállítási pontot a RestorePointCreationDate használatával.

6. Állítsa vissza az SQL-készletet a kívánt visszaállítási pontra a [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) PowerShell parancsmag használatával.
        1. Ha az SQL-készletet egy másik logikai kiszolgálóra szeretné visszaállítani, adjon meg egy másik logikai kiszolgálónevet.  Ez a logikai kiszolgáló más erőforráscsoportban és régióban is lehet.
        2. Ha egy másik előfizetésre szeretne visszaállítani, az "Áthelyezés" gombbal helyezze át a logikai kiszolgálót egy másik előfizetésbe.

7. Ellenőrizze, hogy a visszaállított SQL-készlet online állapotban van-e.

8. A visszaállítás befejezése után konfigurálhatja a helyreállított SQL-készletet [az adatbázis helyreállítás utáni konfigurálásával.](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)

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

## <a name="restore-an-existing-sql-pool-through-the-azure-portal"></a>Meglévő SQL-készlet visszaállítása az Azure Portalon keresztül

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Keresse meg azt az SQL-készletet, amelyből vissza szeretne állítani.
3. Az Áttekintés panel tetején válassza a **Visszaállítás**lehetőséget.

    ![ Visszaállítás áttekintése](./media/sql-data-warehouse-restore-active-paused-dw/restoring-01.png)

4. Válassza az **Automatikus visszaállítási pontok** vagy **a Felhasználó által definiált visszaállítási pontok lehetőséget.** Ha az SQL-készlet nem rendelkezik automatikus visszaállítási pontokkal, várjon néhány órát, vagy hozzon létre egy felhasználó által definiált visszaállítási pontot a visszaállítás előtt. Felhasználó által definiált visszaállítási pontok esetén jelöljön ki egy meglévőt, vagy hozzon létre egy újat. A **Kiszolgáló**esetében választhat logikai kiszolgálót egy másik erőforráscsoportban és régióban, vagy létrehozhat egy újat. Az összes paraméter megadása után kattintson a **Véleményezés + Visszaállítás gombra.**

    ![Automatikus visszaállítási pontok](./media/sql-data-warehouse-restore-active-paused-dw/restoring-11.png)

## <a name="next-steps"></a>Következő lépések

- [Törölt SQL-készlet visszaállítása](sql-data-warehouse-restore-deleted-dw.md)
- [Visszaállítás geo-backup SQL-készletből](sql-data-warehouse-restore-from-geo-backup.md)
