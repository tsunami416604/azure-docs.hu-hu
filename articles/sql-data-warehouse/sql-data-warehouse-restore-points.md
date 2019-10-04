---
title: Felhasználó által definiált visszaállítási pontok | Microsoft Docs
description: Visszaállítási pont létrehozása Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 9dcb8b2e9c1b75aac3c195f89777ac9c6eb030d7
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575264"
---
# <a name="user-defined-restore-points"></a>Felhasználó által definiált visszaállítási pontok

Ebből a cikkből megtudhatja, hogyan hozhat létre egy új, felhasználó által definiált visszaállítási pontot a PowerShell és a Azure Portal használatával Azure SQL Data Warehouse.

## <a name="create-user-defined-restore-points-through-powershell"></a>Felhasználó által definiált visszaállítási pontok létrehozása a PowerShell-lel

Felhasználó által definiált visszaállítási pont létrehozásához használja a [New-AzSqlDatabaseRestorePoint PowerShell-][New-AzSqlDatabaseRestorePoint] parancsmagot.

1. Mielőtt elkezdené, győződjön meg arról, hogy a [Azure PowerShell telepítését][Install Azure PowerShell]végzi.
2. Nyissa meg a PowerShellt.
3. Kapcsolódjon az Azure-fiókjához, és sorolja fel a fiókjához társított összes előfizetést.
4. Válassza ki azt az előfizetést, amely a visszaállítani kívánt adatbázist tartalmazza.
5. Hozzon létre egy visszaállítási pontot az adattárház azonnali másolásához.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$Label = "<YourRestorePointLabel>"

Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName $SubscriptionName

# Create a restore point of the original database
New-AzSqlDatabaseRestorePoint -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName -RestorePointLabel $Label

```

6. Tekintse meg a meglévő visszaállítási pontok listáját.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Felhasználó által definiált visszaállítási pontok létrehozása a Azure Portal

A felhasználó által definiált visszaállítási pontok a Azure Portal használatával is létrehozhatók.

1. Jelentkezzen be [Azure Portal][Azure portal] -fiókjába.

2. Navigáljon ahhoz a SQL Data Warehousehoz, amelyhez visszaállítási pontot kíván létrehozni.

3. Válassza az **Áttekintés** lehetőséget a bal oldali ablaktáblán, majd válassza az **+ új visszaállítási pont**lehetőséget. Ha az új visszaállítási pont gomb nincs engedélyezve, győződjön meg arról, hogy az adatraktár nincs szüneteltetve.

    ![Új visszaállítási pont](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Adja meg a felhasználó által definiált visszaállítási pont nevét, majd kattintson az **alkalmaz**gombra. A felhasználó által definiált visszaállítási pontok alapértelmezett megőrzési időtartama hét nap.

    ![A visszaállítási pont neve](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>További lépések

- [Meglévő adattárház visszaállítása][Restore an existing data warehouse]
- [Törölt adattárház visszaállítása][Restore a deleted data warehouse]
- [Visszaállítás földrajzi biztonsági mentési adattárházból][Restore from a geo-backup data warehouse]

<!--Image references-->

<!--Article references-->
[Azure SQL Database business continuity overview]: ../sql-database/sql-database-business-continuity.md
[Request a DTU quota change]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Install Azure PowerShell]: https://docs.microsoft.com/powershell/azure/overview
[Overview]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[REST]: ./sql-data-warehouse-restore-database-rest-api.md
[Configure your database after recovery]: ../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery
[PowerShelldoc]:./sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-powershell
[Restore an existing data warehouse]:./sql-data-warehouse-restore-active-paused-dw.md
[Restore a deleted data warehouse]:./sql-data-warehouse-restore-deleted-dw.md
[Restore from a geo-backup data warehouse]:./sql-data-warehouse-restore-from-geo-backup.md
<!--MSDN references-->
[New-AzSqlDatabaseRestorePoint]: https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
