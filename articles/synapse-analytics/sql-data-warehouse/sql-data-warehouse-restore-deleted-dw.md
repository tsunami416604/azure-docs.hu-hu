---
title: Törölt dedikált SQL-készlet visszaállítása
description: Útmutató egy törölt SQL-készlet visszaállításához az Azure szinapszis Analyticsben.
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
ms.openlocfilehash: 29edf6ebd451bf05fe24249eeacb416a70001d56
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313570"
---
# <a name="restore-a-deleted-dedicated-sql-pool-in-azure-synapse-analytics"></a>Törölt dedikált SQL-készlet visszaállítása az Azure szinapszis Analyticsben

Ebből a cikkből megtudhatja, hogyan állíthatja vissza a dedikált SQL-készleteket a Azure Portal vagy a PowerShell használatával.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Ellenőrizze a DTU kapacitását.** Minden dedikált SQL-készletet egy [logikai SQL-kiszolgáló](../../azure-sql/database/logical-servers.md) (például MyServer.database.Windows.net) üzemeltet, amely rendelkezik alapértelmezett DTU-kvótával.  Ellenőrizze, hogy a kiszolgáló rendelkezik-e elegendő fennmaradó DTU-kvótával a visszaállítani kívánt adatbázishoz. A szükséges DTU kiszámításához, illetve további DTU igényléséhez lásd: [DTU-kvóta megváltoztatásának kérése](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="restore-a-deleted-data-warehouse-through-powershell"></a>Törölt adattárház visszaállítása a PowerShell-lel

A törölt dedikált SQL-készlet visszaállításához használja a [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancsmagot. Ha a megfelelő kiszolgáló törölve lett, az adatraktár nem állítható vissza.

1. Mielőtt elkezdené, győződjön meg arról, hogy a [Azure PowerShell telepítését](/powershell/azure/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)végzi.
2. Nyissa meg a PowerShellt.
3. Kapcsolódjon az Azure-fiókjához, és sorolja fel a fiókjához társított összes előfizetést.
4. Válassza ki azt az előfizetést, amely tartalmazza a törölt dedikált SQL-készletet a visszaállításhoz.
5. A megadott törölt adattárház beolvasása.
6. A törölt dedikált SQL-készlet visszaállítása
    1. Ha vissza szeretné állítani a törölt dedikált SQL-készletet egy másik kiszolgálóra, ügyeljen rá, hogy adja meg a másik kiszolgáló nevét.  Ez a kiszolgáló más erőforráscsoport és régió is lehet.
    1. Másik előfizetésre való visszaállításhoz használja az [Áthelyezés](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#use-the-portal) gombot a kiszolgáló másik előfizetésre való áthelyezéséhez.
7. Ellenőrizze, hogy a visszaállított adattárház online állapotban van-e.
8. A visszaállítás befejezése után a helyreállított adattárházat a [helyreállítás után az adatbázis konfigurálása](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)után állíthatja be.

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

# Get the deleted database to restore
$DeletedDatabase = Get-AzSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Use the following command to restore deleted data warehouse to a different server
#$RestoredDatabase = Restore-AzSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $TargetResourceGroupName -ServerName $TargetServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

## <a name="restore-a-deleted-database-using-the-azure-portal"></a>Törölt adatbázis visszaállítása a Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Navigáljon ahhoz a kiszolgálóhoz, amelyen a törölt adattárház található.
3. Válassza a **törölt adatbázisok** ikont a tartalomjegyzékben.

    ![Törölt adatbázisok](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-01.png)

4. Válassza ki a visszaállítani kívánt Azure szinapszis-elemzést.

    ![A törölt adatbázisok kiválasztása](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-11.png)

5. Adja meg az új **adatbázis nevét** , majd kattintson **az OK** gombra.

    ![Adja meg az adatbázis nevét](./media/sql-data-warehouse-restore-deleted-dw/restoring-deleted-21.png)

## <a name="next-steps"></a>Következő lépések

- [Meglévő dedikált SQL-készlet visszaállítása](sql-data-warehouse-restore-active-paused-dw.md)
- [Visszaállítás egy földrajzi biztonsági mentéssel dedikált SQL-készletből](sql-data-warehouse-restore-from-geo-backup.md)
