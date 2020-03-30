---
title: Frissítés a legújabb generációra
description: Frissítse az Azure Synapse Analytics SQL-készletét az Azure hardver- és tárolási architektúrájának legújabb generációjára.
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
ms.openlocfilehash: 637e377e469eeb1a82b6c0ad3a845d94ac09c7db
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351201"
---
# <a name="optimize-performance-by-upgrading-azure-synapse-analytics-sql-pool"></a>Az Azure Synapse Analytics SQL-készlet frissítésével optimalizálhatja a teljesítményt

Frissítse az SQL-készletet az Azure hardver- és tárolási architektúrájának legújabb generációjára.

## <a name="why-upgrade"></a>Miért érdemes frissíteni?

Most már zökkenőmentesen frissíthet az SQL pool Compute Optimized Gen2 szintre az Azure Portalon a [támogatott régiók](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)számára. Ha a régió nem támogatja az önfrissítést, frissíthet egy támogatott régióra, vagy megvárhatja, hogy az önfrissítés elérhető legyen a régióban. Frissítsen most, hogy kihasználja az Azure-hardverek legújabb generációját és a továbbfejlesztett tárolási architektúrát, beleértve a gyorsabb teljesítményt, a nagyobb méretezhetőséget és a korlátlan oszlopos tárolást. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>A következőkre vonatkozik:

Ez a frissítés a [támogatott régiókban](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)lévő, számítási optimalizált Gen1 szintű SQL-készletekre vonatkozik.

## <a name="before-you-begin"></a>Előkészületek

1. Ellenőrizze, hogy a [régió](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) támogatja-e a GEN1 és GEN2 között való áttelepítést. Jegyezze fel az automatikus áttelepítési dátumokat. Az automatikus folyamattal való ütközések elkerülése érdekében tervezze meg a manuális áttelepítést az automatikus folyamat kezdő dátuma előtt.
2. Ha olyan régióban van, amely még nem támogatott, ellenőrizze, hogy a régió hozzá kell-e adni, vagy frissíteni kell a támogatott régió [visszaállítása használatával.](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal)
3. Ha a régió támogatott, [frissítsen az Azure Portalon keresztül](#upgrade-in-a-supported-region-using-the-azure-portal)
4. Az alábbi leképezés segítségével válassza ki az SQL-készlet **javasolt teljesítményszintjét** a számítási optimalizált gen1 szint aktuális teljesítményszintje alapján:

   | Optimalizált Gen1 szint számítása | Optimalizált Gen2 szint számítása |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c lehetőséget            |
   |            DW600            |           DW500c lehetőséget            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!Note]
> A javasolt teljesítményszintek nem közvetlen konverziók. Például azt javasoljuk, hogy A DW600-ról DW500c-re menjen.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Frissítés támogatott régióban az Azure Portal használatával

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> A Gen1-ről a Gen2-re az Azure Portalon keresztül történő áttelepítés állandó. Nincs olyan folyamat, amely visszatérne a Gen1-be.  

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

1. Ha a frissítendő Számítási optimalizált Gen1 réteg SQL-készlete szünetel, [folytassa az SQL-készlettel.](pause-and-resume-compute-portal.md)

   > [!NOTE]
   > A Gen2 rendszerre való áttelepítéshez az SQL-készletnek futnia kell.

2. Készüljön fel néhány perc leállásra. 

3. Azonosítsa a számítási optimalizált Gen1 teljesítményszintekre mutató kódhivatkozásokat, és módosítsa azokat az egyenértékű számítási optimalizált gen2 teljesítményszintre. Az alábbiakban két példa látható arra, hogy hol kell frissítenie a kódhivatkozásokat a frissítés előtt:

   Eredeti Gen1 PowerShell parancs:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Módosítva:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName "DW300" a - RequestedServiceObjectiveName "DW300**c"** névre módosult.
   >

   Eredeti Gen1 T-SQL parancs:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Módosítva:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
   > [!NOTE] 
   > SERVICE_OBJECTIVE = a "DW300" SERVICE_OBJECTIVE = "DW300**c"**

## <a name="start-the-upgrade"></a>A frissítés indítása

1. Nyissa meg a számítási optimalizált Gen1 SQL-készletet az Azure Portalon. Ha a frissítendő Számítási optimalizált Gen1 réteg SQL-készlete szünetel, [folytassa az SQL-készlettel.](pause-and-resume-compute-portal.md) 
2. A Feladatok lap: **Frissítés Gen2-kártyára** jelölőnégyzet ![Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)
    
    > [!NOTE]
    > Ha nem látja a **Frissítés gen2-re** kártyát a Feladatok lapon, az előfizetés típusa korlátozott az aktuális régióban.
    > [Küldjön be egy támogatási jegyet](sql-data-warehouse-get-started-create-support-ticket.md) az előfizetés engedélyezési listájának beküldéséhez.

3. Győződjön meg arról, hogy a számítási feladatok befejeződött futás és lemondott frissítés előtt. Néhány percig állásidőt fog tapasztalni, mielőtt az SQL-készlet újra online állapotba kerül, mint számítási optimalizált Gen2 szintű SQL-készlet. **Válassza a Frissítés lehetőséget:**

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. **A frissítés figyelése** az Azure Portal állapotának ellenőrzésével:

   ![Frissítés3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   A frissítési folyamat első lépése a skálázási műveleten ("Frissítés – offline") megy keresztül, ahol az összes munkamenet megszűnik, és a kapcsolatok megszakadnak. 

   A frissítési folyamat második lépése az adatáttelepítés ("Frissítés - Online"). Az adatáttelepítés egy online csepegtető háttérfolyamat. Ez a folyamat lassan áthelyezi az oszlopos adatokat a régi tárolási architektúrából az új tárolási architektúrába egy helyi SSD-gyorsítótár használatával. Ez alatt az idő alatt az SQL-készlet online lesz a lekérdezéshez és a betöltéshez. Az adatok lekérdezhetők lesznek, függetlenül attól, hogy áttelepítették-e vagy sem. Az adatáttelepítés az adatok méretétől, a teljesítményszinttől és az oszlopcentrikus szegmensek számától függően eltérő ütemben történik. 

5. **Választható ajánlás:** A skálázási művelet befejezése után felgyorsíthatja az adatáttelepítés hátterének folyamatát. Az adatok mozgatását úgy kényszerítheti ki, hogy [az Alter Index rebuild-t](sql-data-warehouse-tables-index.md) futtatja az összes olyan elsődleges oszlopcentrikus táblán, amelyet egy nagyobb SLO- és erőforrásosztályban szeretne lekérdezni. Ez a művelet **offline,** mint a csepegtető háttér folyamat, amely órákig is eltarthat a ttól függően, hogy a szám és a méret a táblák. Ha azonban elkészült, az adatok áttelepítése sokkal gyorsabb lesz a kiváló minőségű sorcsoportokkal rendelkező, továbbfejlesztett tárolási architektúrának köszönhetően.
 
> [!NOTE]
> Az Alter Index rebuild egy offline művelet, és a táblák nem lesznek elérhetők, amíg az újraépítés befejeződik.

A következő lekérdezés létrehozza a szükséges Alter Index Rebuild parancsokat az adatáttelepítés felgyorsítása érdekében:

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

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Frissítés Azure-földrajzi régióról visszaállítás sal az Azure Portalon keresztül

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Felhasználó által definiált visszaállítási pont létrehozása az Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

2. Keresse meg azt az SQL-készletet, amelyhez visszaállítási pontot szeretne létrehozni.

3. Az Áttekintés szakasz tetején válassza az **+Új visszaállítási pont lehetőséget.**

    ![Új visszaállítási pont](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. Adja meg a visszaállítási pont nevét.

    ![A visszaállítási pont neve](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Aktív vagy szüneteltetett adatbázis visszaállítása az Azure Portal használatával

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Keresse meg azt az SQL-készletet, amelyből vissza szeretne állítani.
3. Az Áttekintés szakasz tetején válassza a **Visszaállítás lehetőséget.**

    ![ Visszaállítás áttekintése](./media/upgrade-to-latest-generation/restoring_0.png)

4. Válassza **az Automatikus visszaállítási pontok** vagy **a felhasználó által definiált visszaállítási pontok lehetőséget.** Felhasználó által definiált visszaállítási pontok esetén **jelöljön ki egy felhasználó által definiált visszaállítási pontot,** vagy **hozzon létre egy új, felhasználó által definiált visszaállítási pontot.** A kiszolgálóhoz válassza az **Új létrehozása lehetőséget,** és válasszon egy kiszolgálót egy Gen2 által támogatott földrajzi régióban. 

    ![Automatikus visszaállítási pontok](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Visszaállítás Azure-földrajzi régióból a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Adatbázis helyreállításához használja a [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) parancsmagát.

> [!NOTE]
> A Gen2 geo-visszaállítást is végrehajthatja! Ehhez adjon meg egy Gen2 ServiceObjectiveName (pl. DW1000**c)** paramétert választható paraméterként.

1. Nyissa meg a Windows PowerShellt.
2. Csatlakozzon az Azure-fiókjához, és sorolja fel a fiókjához társított összes előfizetést.
3. Válassza ki a visszaállítandó adatbázist tartalmazó előfizetést.
4. Szerezze be a helyreállítani kívánt adatbázist.
5. Hozza létre az adatbázis helyreállítási kérelmét, és adjon meg egy Gen2 ServiceObjectiveName értéket.
6. Ellenőrizze a geo-alapadatbázis állapotát.

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
> Az adatbázis konfigurálása a visszaállítás befejezése után, olvassa [el az Adatbázis konfigurálása a helyreállítás után](../../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery)című témakört.

A helyreállított adatbázis TDE-kompatibilis lesz, ha a forrásadatbázis TDE-kompatibilis.


Ha bármilyen problémát tapasztal az SQL-készlettel kapcsolatban, hozzon létre egy [támogatási kérelmet,](sql-data-warehouse-get-started-create-support-ticket.md) és a lehetséges okként hivatkozzon a "Gen2 frissítés" hivatkozásra.

## <a name="next-steps"></a>További lépések

A frissített SQL-készlet online állapotban van. A továbbfejlesztett architektúra előnyeinek kihasználásához olvassa el [az Erőforrásosztályok a Munkaterhelés-kezeléshez című témakört.](resource-classes-for-workload-management.md)
