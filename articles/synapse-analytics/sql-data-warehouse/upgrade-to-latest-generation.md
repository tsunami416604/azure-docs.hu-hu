---
title: Frissítés a legújabb generációra
description: Frissítse az Azure szinapszis Analytics SQL-készletét az Azure hardver-és tárolási architektúrájának legújabb generációjára.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 13b1f33ffe653627bcf45f6c995e82e741de32ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80742753"
---
# <a name="optimize-performance-by-upgrading-azure-synapse-analytics-sql-pool"></a>Teljesítmény optimalizálása az Azure szinapszis Analytics SQL-készletének frissítésével

Frissítse az SQL-készletet az Azure hardver-és tárolási architektúrájának legújabb generációján.

## <a name="why-upgrade"></a>Miért érdemes frissíteni?

Mostantól zökkenőmentesen frissíthet az SQL-készlet számítási optimalizált Gen2 szintjére a [támogatott régiók](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)Azure Portal. Ha a régió nem támogatja az önfrissítést, frissíthet egy támogatott régióra, vagy megvárhatja, hogy az önfrissítés elérhető legyen a régiójában. A frissítés után kihasználhatja az Azure-beli hardverek és a továbbfejlesztett tárolási architektúra legújabb generációjának előnyeit, beleértve a gyorsabb teljesítményt, a magasabb skálázhatóságot és a korlátlan oszlopos tárolást.

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

> [!IMPORTANT]
> Ez a frissítés a [támogatott régiókban](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)található, a számításra optimalizált Gen1-rétegek SQL-készletekre vonatkozik.

## <a name="before-you-begin"></a>Előkészületek

1. Ellenőrizze, hogy a [régiója](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) támogatott-e a GEN1 és a GEN2 áttelepítéséhez. Figyelje meg az automatikus áttelepítési dátumokat. Az automatikus folyamattal való ütközés elkerülése érdekében tervezze meg a manuális áttelepítést az automatizált folyamat kezdési dátuma előtt.
2. Ha olyan régióban van, amely még nem támogatott, folytassa a régió hozzáadásával vagy frissítésével egy támogatott régióra történő [visszaállítással](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) .
3. Ha a régiója támogatott, [frissítsen a Azure Portal](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Válassza ki az** SQL-készlet javasolt teljesítményét az aktuális teljesítményszint alapján a számítási optimalizált Gen1 szintjén az alábbi leképezés használatával:

   | Számításra optimalizált Gen1-szintek | Számításra optimalizált Gen2-szintek |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c lehetőséget            |
   |            KOR DW600            |           DW500c lehetőséget            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!NOTE]
> A javasolt teljesítményszint nem közvetlen konverzió. Javasoljuk például, hogy a kor DW600-ról a DW500c-re kerüljön.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Frissítés támogatott régióban a Azure Portal használatával

- A Gen1-ről Gen2-re történő áttelepítés a Azure Portalon keresztül állandó. Nem található folyamat a Gen1 való visszatéréshez.
- Az SQL-készletnek futnia kell a Gen2 való Migrálás érdekében.

### <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
- Győződjön meg arról, hogy az SQL-készlet fut – át kell térnie a Gen2-be

### <a name="powershell-upgrade-commands"></a>PowerShell-frissítési parancsok

1. Ha a számítási teljesítményre optimalizált Gen1-készlet frissítése szüneteltetve van, az [SQL-készlet folytatása](pause-and-resume-compute-portal.md).

2. Készüljön fel néhány perces állásidőre.

3. Azonosítson minden olyan kódot, amely a számítási optimalizált Gen1 teljesítmény szintjére hivatkozik, és módosítja azokat a megfelelő számítási optimalizált Gen2 teljesítmény szintjére. Az alábbi két példa arra szolgál, hogy a frissítés előtt frissítse a kód hivatkozásait:

   Eredeti Gen1 PowerShell-parancs:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Módosítva:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE]
   > -A RequestedServiceObjectiveName "DW300" értéke a következőre változott:-RequestedServiceObjectiveName "DW300**c**"
   >

   Eredeti Gen1 T-SQL-parancs:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Módosítva:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ;
   ```

   > [!NOTE]
   > SERVICE_OBJECTIVE = "DW300" a következőre módosul: SERVICE_OBJECTIVE = "DW300**c**"

## <a name="start-the-upgrade"></a>A frissítés elindítása

1. Nyissa meg a számítási optimalizált Gen1 SQL-készletet a Azure Portal. Ha a számítási teljesítményre optimalizált Gen1-készlet frissítése szüneteltetve van, az [SQL-készlet folytatása](pause-and-resume-compute-portal.md).
2. Válassza a **frissítés a Gen2** kártyára lehetőséget a feladatok ![lapon: Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)

   > [!NOTE]
   > Ha nem látja a frissítés a **Gen2** kártyát a feladatok lapon, az előfizetés típusa az aktuális régióban korlátozott.
   > [Küldjön be egy támogatási jegyet](sql-data-warehouse-get-started-create-support-ticket.md) az előfizetés engedélyezési listájának beszerzéséhez.

3. A frissítés előtt győződjön meg róla, hogy a számítási feladatok futtatása és Adattisztítás befejeződött. Néhány percen belül leállást tapasztalhat, mielőtt az SQL-készlet újra online állapotba kerül, mint a számítási optimalizált Gen2 szintű SQL-készlet. **Frissítés kiválasztása**:

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. A frissítést a Azure Portal állapotának ellenőrzésével **figyelheti** :

   ![Upgrade3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   A frissítési folyamat első lépése a skálázási műveleten ("frissítés – offline") halad végig, ahol az összes munkamenet le lesz dobva, és a kapcsolatok elvesznek.

   A frissítési folyamat második lépése az adatáttelepítés ("verziófrissítés – online"). Az adatáttelepítés online szivárgási folyamat. Ez a folyamat lassan áthelyezi a régi tárolási architektúra oszlopos adatait az új tárolási architektúrára egy helyi SSD-gyorsítótár használatával. Ebben az időszakban az SQL-készlet lekérdezés és betöltés céljából online állapotba kerül. Az adatai a lekérdezéshez lesznek elérhetők, függetlenül attól, hogy áttelepítette-e vagy sem. Az adatáttelepítés az adatmérettől, a teljesítmény szintjétől és a oszlopcentrikus-szegmensek számától függően eltérő arányban történik.

5. **Választható javaslat:** A skálázási művelet befejezése után felgyorsíthatja az adatáttelepítés hátterének folyamatát. Az adatáthelyezést úgy kényszerítheti, ha az [Alter index rebuildet](sql-data-warehouse-tables-index.md) az összes olyan elsődleges oszlopcentrikus-táblán futtatja, amelyet egy nagyobb slo-és erőforrás-osztályon szeretne lekérdezni. Ez a művelet **Offline állapotú** a csepegtető háttérben futó folyamattal szemben, amely órákig elvégezhető a táblák számától és méretétől függően. Ha azonban elkészült, az adatáttelepítés sokkal gyorsabb lesz az új, magas színvonalú sorcsoportokba való tömörítéséhez rendelkező, továbbfejlesztett tárolási architektúra miatt.

> [!NOTE]
> Az Alter index Rebuild egy offline művelet, és a táblák addig nem lesznek elérhetők, amíg az Újraépítés be nem fejeződik.

Az alábbi lekérdezés a szükséges Alter index-újraépítési parancsokat hozza létre az adatáttelepítés meggyorsításához:

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON ['
       + Schema_name(tbl.schema_id) + '].['
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE
                                                         WHEN (
                                                     (SELECT Count(*)
                                                      FROM   sys.partitions
                                                             part2
                                                      WHERE  part2.index_id
                                                             = idx.index_id
                                                             AND
                                                     idx.object_id =
                                                     part2.object_id)
                                                     > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              ELSE ''
                                                       END ) + '; SELECT ''[' +
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' +
              Object_name(idx.object_id) + '] ' + (
              CASE
                WHEN ( (SELECT Count(*)
                        FROM   sys.partitions
                               part2
                        WHERE
                     part2.index_id =
                     idx.index_id
                     AND idx.object_id
                         = part2.object_id) > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              + ' completed'';'
              ELSE ' completed'';'
                                                    END )
FROM   sys.indexes idx
       INNER JOIN sys.tables tbl
               ON idx.object_id = tbl.object_id
       LEFT OUTER JOIN sys.partitions part
                    ON idx.index_id = part.index_id
                       AND idx.object_id = part.object_id
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE';
```

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Frissítés egy Azure földrajzi régióról a Azure Portal használatával történő visszaállítással

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Felhasználó által definiált visszaállítási pont létrehozása a Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Navigáljon arra az SQL-készletre, amelyhez visszaállítási pontot kíván létrehozni.

3. Az Áttekintés szakasz tetején válassza az **+ új visszaállítási pont**elemet.

    ![Új visszaállítási pont](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. Adja meg a visszaállítási pont nevét.

    ![A visszaállítási pont neve](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Aktív vagy szüneteltetett adatbázis visszaállítása a Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Navigáljon arra az SQL-készletre, amelyet vissza szeretne állítani.
3. Az Áttekintés szakasz tetején válassza a **visszaállítás**lehetőséget.

    ![ Visszaállítás áttekintése](./media/upgrade-to-latest-generation/restoring_0.png)

4. Válassza ki az **Automatikus visszaállítási pontokat** vagy a **felhasználó által definiált visszaállítási pontokat**. Felhasználó által definiált visszaállítási pontok esetén **válasszon egy felhasználó által definiált visszaállítási pontot** , vagy **hozzon létre egy új, felhasználó által definiált visszaállítási pontot**. A-kiszolgáló esetében válassza az **új létrehozása** lehetőséget, és válasszon egy kiszolgálót a Gen2 által támogatott földrajzi régióban.

    ![Automatikus visszaállítási pontok](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Visszaállítás egy Azure földrajzi régióból a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Adatbázis helyreállításához használja a [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) parancsmagot.

> [!NOTE]
> A Gen2 a Geo-visszaállítást is elvégezheti. Ehhez meg kell adnia egy Gen2-ServiceObjectiveName (például DW1000**c**) választható paraméterként.

1. Nyissa meg a Windows PowerShellt.
2. Kapcsolódjon az Azure-fiókjához, és sorolja fel a fiókjához társított összes előfizetést.
3. Válassza ki azt az előfizetést, amely a visszaállítani kívánt adatbázist tartalmazza.
4. Szerezze be a helyreállítani kívánt adatbázist.
5. Hozza létre az adatbázis helyreállítási kérelmét, amely egy Gen2-ServiceObjectiveName ad meg.
6. Ellenőrizze a Geo-helyreállított adatbázis állapotát.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Ha az adatbázist a visszaállítás befejeződése után szeretné konfigurálni, tekintse meg [az adatbázis konfigurálása a helyreállítás után](../../sql-database/sql-database-disaster-recovery.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery)című témakört.

A helyreállított adatbázis TDE válik, ha a forrásadatbázis TDE engedélyezve van.

Ha az SQL-készlettel kapcsolatos problémákat tapasztal, hozzon létre egy [támogatási kérést](sql-data-warehouse-get-started-create-support-ticket.md) , és hivatkozzon a "Gen2 upgrade" kifejezésre a lehetséges okok miatt.

## <a name="next-steps"></a>További lépések

A frissített SQL-készlet online állapotú. A bővített architektúra előnyeinek kihasználásához tekintse meg az [erőforrás-osztályok a számítási feladatok kezeléséhez](resource-classes-for-workload-management.md)című témakört.
