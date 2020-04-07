---
title: Felhasználó által definiált visszaállítási pontok
description: Visszaállítási pont létrehozása az SQL-készlethez.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/03/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5c9b7eb1b03b6b6e3721c13f9ebf7da25dd2e376
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745057"
---
# <a name="user-defined-restore-points"></a>Felhasználó által definiált visszaállítási pontok

Ebben a cikkben megtudhatja, hogy hozzon létre egy új, felhasználó által definiált visszaállítási pontot egy SQL-készlet hez az Azure Synapse Analytics a PowerShell és az Azure Portal használatával.

## <a name="create-user-defined-restore-points-through-powershell"></a>Felhasználó által definiált visszaállítási pontok létrehozása a PowerShellen keresztül

Felhasználó által definiált visszaállítási pont létrehozásához használja a [New-AzSqlDatabaseRestorePoint](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) PowerShell parancsmag.

1. Mielőtt elkezdené, telepítse [az Azure PowerShellt.](/powershell/azure/overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)
2. Nyissa meg a PowerShellt.
3. Csatlakozzon az Azure-fiókjához, és sorolja fel a fiókjához társított összes előfizetést.
4. Válassza ki a visszaállítandó adatbázist tartalmazó előfizetést.
5. Hozzon létre egy visszaállítási pontot az adatraktár azonnali másolatához.

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

6. Tekintse meg az összes meglévő visszaállítási pontot tartalmazó listát.

```Powershell
# List all restore points
Get-AzSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName
```

## <a name="create-user-defined-restore-points-through-the-azure-portal"></a>Felhasználó által definiált visszaállítási pontok létrehozása az Azure Portalon keresztül

A felhasználó által definiált visszaállítási pontok az Azure Portalon keresztül is létrehozhatók.

1. Jelentkezzen be az [Azure Portal-fiókjába.](https://portal.azure.com/)

2. Keresse meg azt az SQL-készletet, amelyhez visszaállítási pontot szeretne létrehozni.

3. Válassza az **Áttekintés** lehetőséget a bal oldali ablaktáblából, és válassza **a + Új visszaállítási pont**lehetőséget. Ha az Új visszaállítási pont gomb nincs engedélyezve, győződjön meg arról, hogy az SQL-készlet nincs szüneteltetve.

    ![Új visszaállítási pont](./media/sql-data-warehouse-restore-points/creating-restore-point-01.png)

4. Adja meg a felhasználó által definiált visszaállítási pont nevét, majd kattintson **az Alkalmaz gombra.** A felhasználó által definiált visszaállítási pontok alapértelmezett megőrzési ideje hét nap.

    ![A visszaállítási pont neve](./media/sql-data-warehouse-restore-points/creating-restore-point-11.png)

## <a name="next-steps"></a>További lépések

- [Meglévő SQL-készlet visszaállítása](sql-data-warehouse-restore-active-paused-dw.md)
- [Törölt SQL-készlet visszaállítása](sql-data-warehouse-restore-deleted-dw.md)
- [Visszaállítás geo-backup SQL-készletből](sql-data-warehouse-restore-from-geo-backup.md)

