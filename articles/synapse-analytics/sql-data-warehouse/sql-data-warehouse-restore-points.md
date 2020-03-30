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
ms.openlocfilehash: d056b30c44ced5f3e8ce9041e2366290bee485da
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350145"
---
# <a name="user-defined-restore-points"></a>Felhasználó által definiált visszaállítási pontok

Ebben a cikkben megtudhatja, hogy hozzon létre egy új, felhasználó által definiált visszaállítási pontot egy SQL-készlet hez az Azure Synapse Analytics a PowerShell és az Azure Portal használatával.

## <a name="create-user-defined-restore-points-through-powershell"></a>Felhasználó által definiált visszaállítási pontok létrehozása a PowerShellen keresztül

Felhasználó által definiált visszaállítási pont létrehozásához használja a [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint?view=azps-2.4.0) PowerShell parancsmag.

1. Mielőtt elkezdené, telepítse [az Azure PowerShellt.](https://docs.microsoft.com/powershell/azure/overview)
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

