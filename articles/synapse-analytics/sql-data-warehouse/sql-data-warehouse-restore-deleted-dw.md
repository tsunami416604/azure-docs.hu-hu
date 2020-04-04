---
title: Törölt SQL-készlet visszaállítása
description: Útmutató a törölt SQL-készlet visszaállításához.
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
ms.openlocfilehash: 251fdb83e848aaac3a5391320df23149ce1bce33
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633051"
---
# <a name="restore-a-deleted-sql-pool-using-azure-synapse-analytics"></a>Törölt SQL-készlet visszaállítása az Azure Synapse Analytics használatával

Ebben a cikkben megtudhatja, hogy az Azure Portalon vagy a PowerShell használatával visszaállíthatja az SQL-t.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Ellenőrizze a DTU-kapacitást.** Minden SQL-készletet egy SQL-kiszolgáló üzemeltet (például myserver.database.windows.net), amely alapértelmezett DTU-kvótával rendelkezik.  Ellenőrizze, hogy az SQL-kiszolgáló rendelkezik-e elegendő dtu kvótával a visszaállító adatbázishoz. A szükséges DTU kiszámításáról vagy további DTU-kérésről a [DTU-kvótamódosítás kérése.Learn](sql-data-warehouse-get-started-create-support-ticket.md)how to calculate DTU needed or to request more DTU, see Request a DTU quota change .

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Törölt adattárház visszaállítása a PowerShellen keresztül

Törölt SQL-készlet visszaállításához használja a [Restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) parancsmagát. Ha a megfelelő logikai kiszolgálót is törölték, az adattárház nem állítható vissza.

1. Mielőtt elkezdené, telepítse [az Azure PowerShellt.](https://docs.microsoft.com/powershell/azure/overview)
2. Nyissa meg a PowerShellt.
3. Csatlakozzon az Azure-fiókjához, és sorolja fel a fiókjához társított összes előfizetést.
4. Válassza ki a visszaállítandó törölt adatraktárt tartalmazó előfizetést.
5. Az adott törölt adattárház beszereznie.
6. A törölt adattárház visszaállítása
    1. A törölt SQL Data Warehouse visszaállításához adjon meg egy másik logikai kiszolgálót.  Ez a logikai kiszolgáló más erőforráscsoportban és régióban is lehet.
    1. Ha egy másik előfizetésre szeretne visszaállítani, az [Áthelyezés](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) gombbal helyezze át a logikai kiszolgálót egy másik előfizetésbe.
7. Ellenőrizze, hogy a visszaállított adattárház online állapotban van-e.
8. A visszaállítás befejezése után konfigurálhatja a helyreállított adattárházat [az adatbázis helyreállítás utáni konfigurálásával.](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)

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

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different logical server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Törölt adatbázis visszaállítása az Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Keresse meg azt az SQL-kiszolgálót, amelyen a törölt adattárházat üzemeltette.
3. Válassza a **Törölt adatbázisok ikont** a tartalomjegyzékben.

    ![Törölt adatbázisok](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Jelölje ki a visszaállítani kívánt törölt SQL-adattárházat.

    ![A törölt adatbázisok kiválasztása](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Adjon meg egy új **adatbázisnevet,** és kattintson **az OK** gombra

    ![Adatbázis nevének megadása](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Következő lépések

- [Meglévő SQL-készlet visszaállítása](sql-data-warehouse-restore-active-paused-dw.md)
- [Visszaállítás geo-backup SQL-készletből](sql-data-warehouse-restore-from-geo-backup.md)
