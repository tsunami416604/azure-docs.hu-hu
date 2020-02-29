---
title: Felhasználó által definiált visszaállítási pontok
description: Visszaállítási pont létrehozása az SQL-készlethez.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 72e2535730dc6c814708ef3ff563136235930475
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198268"
---
# <a name="user-defined-restore-points"></a>Felhasználó által definiált visszaállítási pontok

Ebből a cikkből megtudhatja, hogyan hozhat létre egy új, felhasználó által definiált visszaállítási pontot egy SQL-készlethez az Azure szinapszis Analyticsben a PowerShell és a Azure Portal használatával.

## <a name="create-user-defined-restore-points-through-powershell"></a>Felhasználó által definiált visszaállítási pontok létrehozása a PowerShell-lel

Felhasználó által definiált visszaállítási pont létrehozásához használja a [New-AzSqlDatabaseRestorePoint PowerShell-](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0) parancsmagot.

1. Mielőtt elkezdené, győződjön meg arról, hogy a [Azure PowerShell telepítését](https://docs.microsoft.com/powershell/azure/overview)végzi.
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

1. Jelentkezzen be [Azure Portal](https://portal.azure.com/) -fiókjába.

2. Navigáljon arra az SQL-készletre, amelyhez visszaállítási pontot kíván létrehozni.

3. Válassza az **Áttekintés** lehetőséget a bal oldali ablaktáblán, majd válassza az **+ új visszaállítási pont**lehetőséget. Ha az új visszaállítási pont gomb nincs engedélyezve, győződjön meg arról, hogy az SQL-készlet nincs szüneteltetve.

    ![Új visszaállítási pont](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Adja meg a felhasználó által definiált visszaállítási pont nevét, majd kattintson az **alkalmaz**gombra. A felhasználó által definiált visszaállítási pontok alapértelmezett megőrzési időtartama hét nap.

    ![A visszaállítási pont neve](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>További lépések

- [Meglévő SQL-készlet visszaállítása](sql-data-warehouse-restore-active-paused-dw.md)
- [Törölt SQL-készlet visszaállítása](sql-data-warehouse-restore-deleted-dw.md)
- [Visszaállítás egy földrajzi biztonsági mentési SQL-készletből](sql-data-warehouse-restore-from-geo-backup.md)

