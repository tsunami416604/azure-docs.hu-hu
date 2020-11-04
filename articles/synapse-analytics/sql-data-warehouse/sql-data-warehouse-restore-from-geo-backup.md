---
title: Dedikált SQL-készlet visszaállítása geo-biztonsági mentésből
description: Útmutató a dedikált SQL-készlet geo-visszaállításához az Azure szinapszis Analyticsben
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 7496cedd127182482bccf97909cc0a0a4a78253f
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313414"
---
# <a name="geo-restore-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Dedikált SQL-készlet geo visszaállítása az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, hogyan állíthatja vissza a dedikált SQL-készletet egy geo-biztonsági mentésből Azure Portal és a PowerShell használatával.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Ellenőrizze a DTU kapacitását.** Minden dedikált SQL-készletet egy [logikai SQL-kiszolgáló](../../azure-sql/database/logical-servers.md) (például MyServer.database.Windows.net) üzemeltet, amely rendelkezik alapértelmezett DTU-kvótával. Ellenőrizze, hogy az SQL-kiszolgáló rendelkezik-e elegendő fennmaradó DTU-kvótával az adatbázis visszaállításához. A szükséges DTU kiszámításához, illetve további DTU igényléséhez lásd: [DTU-kvóta megváltoztatásának kérése](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Visszaállítás egy Azure földrajzi régióból a PowerShell használatával

A Geo biztonsági mentésből való visszaállításhoz használja a [Get-AzSqlDatabaseGeoBackup](/powershell/module/az.sql/get-azsqldatabasegeobackup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) és a [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancsmagot.

> [!NOTE]
> A Gen2 a Geo-visszaállítást is elvégezheti. Ehhez meg kell adnia egy Gen2-ServiceObjectiveName (például DW1000 **c** ) választható paraméterként.
>

1. Mielőtt elkezdené, győződjön meg arról, hogy a [Azure PowerShell telepítését](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)végzi.
2. Nyissa meg a PowerShellt.
3. Kapcsolódjon az Azure-fiókjához, és sorolja fel a fiókjához társított összes előfizetést.
4. Válassza ki azt az előfizetést, amely a visszaállítani kívánt adatraktárat tartalmazza.
5. Szerezze be a helyreállítani kívánt adatraktárat.
6. Hozza létre az adatraktár helyreállítási kérelmét.
7. Ellenőrizze a Geo-helyreállított adattárház állapotát.
8. Ha az adattárházat a visszaállítás befejeződése után szeretné konfigurálni, tekintse meg [az adatbázis konfigurálása a helyreállítás után]( ../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)című témakört.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different server.
$TargetServerName="<YourtargetServerNameWithoutURLSuffixSeeNote>"  
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"
$TargetServiceObjective="<YourTargetServiceObjective-DWXXXc>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName
Get-AzureSqlDatabase -ServerName $ServerName

# Get the data warehouse you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Recover data warehouse
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName $TargetServiceObjective

# Verify that the geo-restored data warehouse is online
$GeoRestoredDatabase.status
```

A helyreállított adatbázis TDE válik, ha a forrásadatbázis TDE engedélyezve van.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Visszaállítás egy Azure földrajzi régióból Azure Portal

Az alábbi lépéseket követve állíthatja vissza a dedikált SQL-készletet egy földrajzi biztonsági mentésből:

1. Jelentkezzen be [Azure Portal](https://portal.azure.com/) -fiókjába.
2. Kattintson az **+ Erőforrás létrehozása** gombra.

   ![Új DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Kattintson az **adatbázisok** , majd az **Azure szinapszis Analytics (korábban SQL DW)** elemre.

   ![Új DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Adja meg az **alapok** lapon kért információkat, majd kattintson a **Tovább gombra: további beállítások**.

   ![Alapbeállítások](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. A **meglévő** Adatparaméter használata esetén válassza a **biztonsági mentés** lehetőséget, és válassza ki a megfelelő biztonsági mentést a görgetési beállítások közül. Kattintson a **Felülvizsgálat + létrehozás** elemre.

   ![biztonsági mentés](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Az adattárház visszaállítása után győződjön meg arról, hogy az **állapot online állapotban** van.

## <a name="next-steps"></a>Következő lépések

- [Meglévő dedikált SQL-készlet visszaállítása](sql-data-warehouse-restore-active-paused-dw.md)
- [Törölt dedikált SQL-készlet visszaállítása](sql-data-warehouse-restore-deleted-dw.md)
