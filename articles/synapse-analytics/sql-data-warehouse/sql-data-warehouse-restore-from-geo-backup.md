---
title: Adattárház visszaállítása geobiztonsági mentésből
description: Útmutató egy SQL-készlet geo-visszaállításához.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/12/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 4390ed39c86e041d3fbd776415f0ffbe71f605bd
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350166"
---
# <a name="geo-restore-for-sql-pool"></a>Geo-visszaállítás az SQL-készlethez

Ebben a cikkben megtudhatja, hogy az SQL-készlet visszaállítása az Azure Portalon és a PowerShellen keresztül egy geo-biztonsági mentés.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

**Ellenőrizze a DTU-kapacitást.** Minden SQL-készletet egy SQL-kiszolgáló üzemeltet (például myserver.database.windows.net), amely alapértelmezett DTU-kvótával rendelkezik. Ellenőrizze, hogy az SQL-kiszolgáló rendelkezik-e elegendő dtu kvótával a visszaállító adatbázishoz. A szükséges DTU kiszámításáról vagy további DTU-kérésről a [DTU-kvótamódosítás kérése.Learn](sql-data-warehouse-get-started-create-support-ticket.md)how to calculate DTU needed or to request more DTU, see Request a DTU quota change .

## <a name="restore-from-an-azure-geographical-region-through-powershell"></a>Visszaállítás Azure-földrajzi régióból a PowerShellen keresztül

A visszaállításhoz geo-backup, használja a [Get-AzSqlDatabaseGeoBackup](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasegeobackup) és [restore-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/restore-azsqldatabase) parancsmag.

> [!NOTE]
> A Gen2 geo-visszaállítást is végrehajthatja! Ehhez adjon meg egy Gen2 ServiceObjectiveName (pl. DW1000**c)** paramétert választható paraméterként.
>

1. Mielőtt elkezdené, telepítse [az Azure PowerShellt.](https://docs.microsoft.com/powershell/azure/overview)
2. Nyissa meg a PowerShellt.
2. Csatlakozzon az Azure-fiókjához, és sorolja fel a fiókjához társított összes előfizetést.
3. Válassza ki a visszaállítandó adatraktárt tartalmazó előfizetést.
4. Szerezze be a helyreállítani kívánt adattárházat.
5. Hozza létre az adatraktár helyreállítási kérelmét.
6. Ellenőrizze a geo-alapú adattárház állapotát.
7. Az adattárház konfigurálása a visszaállítás befejezése után, [olvassa el az Adatbázis konfigurálása a helyreállítás után]( ../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)című témakört.

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

A helyreállított adatbázis TDE-kompatibilis lesz, ha a forrásadatbázis TDE-kompatibilis.

## <a name="restore-from-an-azure-geographical-region-through-azure-portal"></a>Visszaállítás Azure-földrajzi régióból az Azure Portalon keresztül

Az alábbi lépéseket követve állítsa vissza az SQL-készletet a geobiztonsági mentésből:

1. Jelentkezzen be az [Azure Portal-fiókjába.](https://portal.azure.com/)
1. Kattintson **a + Erőforrás létrehozása gombra.** 

![Új DW](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new.png)

3. Kattintson **az adatbázisok,** majd **Azure Synapse Analytics (korábban SQL DW) **.

![Új DW 2](./media/sql-data-warehouse-restore-from-geo-backup/georestore-new-02.png)

4. Töltse ki az **Alapok** lapon kért információkat, és kattintson a **Tovább gombra: További beállítások**.

![Alapvető beállítások](./media/sql-data-warehouse-restore-from-geo-backup/georestore-dw-1.png)

5. A **Meglévő adatparaméter használata csoportban** válassza a **Biztonsági mentés** lehetőséget, és válassza ki a megfelelő biztonsági másolatot a lefelé görgetési beállítások közül. Kattintson **a Véleményezés + Létrehozás gombra.**
 
![biztonsági mentés](./media/sql-data-warehouse-restore-from-geo-backup/georestore-select.png)

6. Az adattárház visszaállítása után ellenőrizze, hogy az **Állapot** online állapot-e.

## <a name="next-steps"></a>Következő lépések
- [Meglévő SQL-készlet visszaállítása](sql-data-warehouse-restore-active-paused-dw.md)
- [Törölt SQL-készlet visszaállítása](sql-data-warehouse-restore-deleted-dw.md)