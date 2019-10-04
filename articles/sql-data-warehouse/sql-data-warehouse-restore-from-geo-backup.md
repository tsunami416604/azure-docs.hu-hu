---
title: Azure SQL Data Warehouse visszaállítása geo-biztonsági mentésből | Microsoft Docs
description: Útmutató a Azure SQL Data Warehouse geo-visszaállításához.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 768646522c2589c302fd7a58031d4ebdb7fcdc12
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68426654"
---
# <a name="geo-restore-azure-sql-data-warehouse"></a>Geo-visszaállítás Azure SQL Data Warehouse

Ebből a cikkből megtudhatja, hogyan állíthatja vissza az adattárházat a Geo biztonsági mentésből Azure Portal és a PowerShell használatával.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**Ellenőrizze a DTU kapacitását.** Mindegyik SQL Data Warehouse egy SQL-kiszolgáló (például myserver.database.windows.net) üzemelteti, amely rendelkezik alapértelmezett DTU-kvótával. Ellenőrizze, hogy az SQL-kiszolgáló rendelkezik-e elegendő fennmaradó DTU-kvótával az adatbázis visszaállításához. A szükséges DTU kiszámításához, illetve további DTU igényléséhez lásd: [DTU-kvóta][Request a DTU quota change]megváltoztatásának kérése.

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Visszaállítás egy Azure földrajzi régióból a PowerShell használatával

A Geo biztonsági mentésből való visszaállításhoz használja a [Get-AzSqlDatabaseGeoBackup][Get-AzSqlDatabaseGeoBackup] és a [Restore-AzSqlDatabase][Restore-AzSqlDatabase] parancsmagot.

> [!NOTE]
> A Gen2 a Geo-visszaállítást is elvégezheti. Ehhez meg kell adnia egy Gen2-ServiceObjectiveName (például DW1000**c**) választható paraméterként.
>

1. Mielőtt elkezdené, győződjön meg arról, hogy a [Azure PowerShell telepítését][Install Azure PowerShell]végzi.
2. Nyissa meg a PowerShellt.
2. Kapcsolódjon az Azure-fiókjához, és sorolja fel a fiókjához társított összes előfizetést.
3. Válassza ki azt az előfizetést, amely a visszaállítani kívánt adatraktárat tartalmazza.
4. Szerezze be a helyreállítani kívánt adatraktárat.
5. Hozza létre az adatraktár helyreállítási kérelmét.
6. Ellenőrizze a Geo-helyreállított adattárház állapotát.
7. Ha az adattárházat a visszaállítás befejeződése után szeretné konfigurálni, tekintse meg [az adatbázis konfigurálása a helyreállítás után][Configure your database after recovery]című témakört.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$TargetResourceGroupName="<YourTargetResourceGroupName>" # Restore to a different logical server.
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

Kövesse az alábbi lépéseket a Azure SQL Data Warehouse geo-biztonsági mentésből történő visszaállításához:

1. Jelentkezzen be [Azure Portal][Azure portal] -fiókjába.
1. Kattintson **az + erőforrás létrehozása** lehetőségre, és keressen rá SQL Data Warehouse és kattintson a **Létrehozás**gombra.

    ![Új DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)
1. Adja meg az **alapok** lapon kért információkat, majd kattintson a **tovább gombra: További beállítások**.

    ![Alapvető beállítások](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)
1. A **meglévő** Adatparaméter használata esetén válassza a **biztonsági mentés** lehetőséget, és válassza ki a megfelelő biztonsági mentést a görgetési beállítások közül. Kattintson a **felülvizsgálat + létrehozás**gombra.
 
   ![biztonsági mentés](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)
2. Az adattárház visszaállítása után győződjön meg arról, hogy az **állapot online állapotban** van.

## <a name="next-steps"></a>További lépések
- [Meglévő adattárház visszaállítása][Restore an existing data warehouse]
- [Törölt adattárház visszaállítása][Restore a deleted data warehouse]

<!--Image references-->

<!--Article references-->
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[How to install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md


<!--MSDN references-->
[Restore-AzSqlDatabase]: https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase
[Get-AzSqlDatabaseGeoBackup]: https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
