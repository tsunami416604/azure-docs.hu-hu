---
title: Frissítés a legújabb generációja, az Azure SQL Data Warehouse |} A Microsoft Docs
description: Azure SQL Data Warehouse frissítsen a legújabb generációja, az Azure hardver- és tárolási architektúrát.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.openlocfilehash: a8bd260db7a141ce845ce7fb5b7e10f642907b82
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851095"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Teljesítményoptimalizálás az SQL Data Warehouse frissítésével

Azure SQL Data Warehouse frissítsen a legújabb generációja, az Azure hardver- és tárolási architektúrát.

## <a name="why-upgrade"></a>Miért érdemes frissíteni?

Most már zökkenőmentesen frissítheti az Azure Portalon az SQL Data Warehouse számítási optimalizált Gen2 szintre [támogatott régiók](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Ha a régió nem támogatja a helyi frissítés, váltson egy támogatott régióban, vagy várjon, amíg az Ön régiójában érhető el helyi frissítése. Frissítsen most kihasználhatja az Azure-beli hardveres és továbbfejlesztett tárolási architektúrát, beleértve a gyorsabb teljesítmény, méretezhetőség és korlátlan Oszlopalapú tárolást legújabb generációja. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>A következőkre vonatkozik

Ez a frissítés alkalmazva lesz optimalizált Gen1 számítási szinten található adattárházak [támogatott régiók](gen2-migration-schedule.md#automated-schedule-and-region-availability-table).

## <a name="before-you-begin"></a>Előkészületek

1. Ellenőrizze, hogy a [régió](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) GEN1 GEN2 az áttelepítés támogatott. Megjegyzés: az automatikus áttelepítési dátum. Az automatizált folyamat való ütközések elkerülése érdekében tervezze meg a az automatizált folyamat kezdő dátum előtt manuális áttelepítést.
2. Ha Ön egy régióban, amely még nem támogatott, továbbra is hozzá kell adni a régió kereséséhez vagy [visszaállítással frissítés](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) egy támogatott régióban.
3. Ha a régió, [frissítés az Azure Portalon keresztül](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Válassza ki a javasolt teljesítményszint** az adatraktár alapján a jelenlegi teljesítményszint optimalizált Gen1 számítási rétegben az alábbi leképezés használatával:

   | COMPUTE-optimalizált Gen1 kapacitás | COMPUTE-optimalizált Gen2-kapacitás |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c            |
   |            DW600            |           DW500c            |
   |           DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!Note]
> Javasolt teljesítményszintek közvetlen konverzió nem tartoznak. Ha például DW500c dw600 értékre a ajánlott folyamatban.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Frissítse egy támogatott régióban az Azure portal használatával

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!NOTE]
> Áttelepítés Gen1 Gen2-re az Azure Portalon keresztül nem vonható vissza. Nem áll egy folyamatot Gen1 adnak vissza.  

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Ha a frissítendő optimalizált Gen1 számítási réteg adattárház szüneteltetve van, [folytathatja az adattárház](pause-and-resume-compute-portal.md).

   > [!NOTE]
   > Az Azure SQL Data Warehouse Gen2-re áttelepítése kell futnia.

2. Néhány perc alatt állásidő készíteni. 

3. Azonosítsa a optimalizált Gen1 számítási teljesítményszintek kód vonatkozó hivatkozást, és módosítsa azokat a számítási optimalizált Gen2 egyenértékű teljesítményszintre. Az alábbiakban két példa, amelyben frissítenie kell kód hivatkozásokat a frissítés előtt:

   Eredeti Gen1 PowerShell-parancsot:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   A módosítás:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE] 
   > -RequestedServiceObjectiveName "DW300" változott - RequestedServiceObjectiveName "DW300**c**"
   >

   Eredeti Gen1 T-SQL-parancsot:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   A módosítás:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ; 
   ```
   > [!NOTE] 
   > SERVICE_OBJECTIVE = "DW300" változott SERVICE_OBJECTIVE = ' DW300**c**"

## <a name="start-the-upgrade"></a>A frissítés megkezdése

1. Nyissa meg az optimalizált Gen1 számítási réteg data warehouse az Azure Portalon. Ha a frissítendő optimalizált Gen1 számítási réteg adattárház szüneteltetve van, [folytathatja az adattárház](pause-and-resume-compute-portal.md). 
2. Válassza ki **frissítés Gen2-re** kártya feladatok lapján:  ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
    > [!NOTE]
    > Ha nem látja a **frissítés Gen2-re** feladatok lapján, az előfizetés típusa kártya korlátozva van a jelenlegi régióban.
    > [Küldjön el egy támogatási jegyet](sql-data-warehouse-get-started-create-support-ticket.md) beolvasni az előfizetés szerepel az engedélyezési listán.

3. Győződjön meg arról, a számítási feladat befejeződött fut, és zajlanak a frissítés előtt. Néhány percet, mielőtt újra online állapotba, az optimalizált Gen2 számítási réteg adattárházak kerül az adattárház szoftverkarbantartás lesz. **Válassza a frissítési**:

   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **A frissítés figyelése** által az Azure Portalon az állapot ellenőrzése:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)

   Az első lépés a frissítési folyamat halad végig a skálázási művelet ("Upgrading - kapcsolat nélküli"), ahol az összes munkamenetek meg lesznek szakítva és kapcsolatokat a rendszer eldobja. 

   A frissítési folyamat második lépése adatok migrálása ("frissítés – Online"). Adatok áttelepítése az egy online trickle háttérben futó folyamat. Ez a folyamat lassan áthelyezi Oszlopalapú a régi tároló-architektúra, az új tároló-architektúra használatával a helyi SSD-gyorsítótárat. Ebben az időszakban az adattárház lekérdezésére és betöltése elérhető lesz. Az adatok lekérdezéséhez, függetlenül attól, hogy azt migrálták, vagy nem elérhető lesz. Az adatáttelepítés függően az adatok mérete, a teljesítményszintet és az oszlopcentrikus szegmensek száma különböző ütemben történik. 

5. **Nem kötelező javaslat:** A skálázási művelet befejezése után a felgyorsíthatja az adatok áttelepítési háttérben futó folyamat. Adatáthelyezés futtatásával kényszerítheti [Alter Index rebuild](sql-data-warehouse-tables-index.md) az összes elsődleges oszlopcentrikus táblák, akkor lekérdezése nagyobb slo-t és az erőforrás osztályra. Ez a művelet **offline** függően számát és méretét a táblák órát is igénybe vehet trickle háttérfolyamat képest. Azonban a folyamat végén adatáttelepítés lesz sokkal gyorsabb, az új, továbbfejlesztett tárolási architektúrát miatt a magas színvonalú naplóbájtot.
 
> [!NOTE]
> Az ALTER Index rebuild egy offline művelet, és a táblák nem lesz elérhető az újjáépítést befejeződéséig.

A következő lekérdezést hoz létre a szükséges az Alter Index Rebuild parancsokat gyorsíthatja fel az adatok áttelepítését:

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

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Frissítse az Azure-régióból egy földrajzi visszaállítással az Azure Portalon keresztül

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Hozzon létre egy felhasználói visszaállítási pontot, az Azure portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Keresse meg az SQL data warehouse egy visszaállítási pontot létrehozni kívánt.

3. Az Áttekintés szakasz tetején válassza **+ új visszaállítási pont**.

    ![Új visszaállítási pont](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_0.png)

4. Adjon meg egy nevet a visszaállítási pontot.

    ![Visszaállítási pont nevét](./media/sql-data-warehouse-restore-database-portal/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Az Azure portal használatával egy aktív vagy szüneteltetett adatbázis visszaállítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Keresse meg az SQL data warehouse, a visszaállítani kívánt.
3. Az Áttekintés szakasz tetején válassza **visszaállítása**.

    ![ Visszaállítás áttekintése](./media/sql-data-warehouse-restore-database-portal/restoring_0.png)

4. Ezek közül bármelyikre **automatikus visszaállítási pontok** vagy **felhasználói visszaállítási pontok**.

    ![Automatikus visszaállítási pontok](./media/sql-data-warehouse-restore-database-portal/restoring_1.png)

5. A felhasználó által megadott visszaállítási pontok **jelöljön ki egy visszaállítási pontot** vagy **hozzon létre egy új, felhasználó által definiált visszaállításipont**. Válassza ki a kiszolgáló egy Gen2 támogatott földrajzi régióban. 

    ![Felhasználó által definiált visszaállítási pontok](./media/sql-data-warehouse-restore-database-portal/restoring_2_udrp.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Visszaállítás Azure-régióból egy földrajzi PowerShell-lel

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Adatbázis helyreállítása, használja a [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase) parancsmagot.

> [!NOTE]
> Gen2-re a geo-visszaállítás elvégzése! Ehhez adjon meg egy Gen2 ServiceObjectiveName (pl. dw1000 értékre**c**), mint egy nem kötelező paraméter.

1. Nyissa meg a Windows PowerShellt.
2. Azure-fiókjához csatlakozhat, és a fiókjához társított összes előfizetés listázása.
3. Válassza ki az előfizetést, amely tartalmazza az adatbázist vissza kell állítani.
4. A helyreállítani kívánt adatbázis beolvasása.
5. A helyreállítási kérelem egy Gen2 ServiceObjectiveName megadása az adatbázis létrehozásához.
6. Ellenőrizze a geo-visszaállított adatbázis állapotát.

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
> A visszaállítás befejezése után az adatbázis beállítása: [konfigurálása az adatbázis helyreállítása után](../sql-database/sql-database-disaster-recovery.md#configure-your-database-after-recovery).

A helyreállított adatbázis TDE-kompatibilis akkor lesz a forrásadatbázis TDE-t.


Ha problémákat tapasztal az adatraktárban, hozzon létre egy [támogatási kérelem](sql-data-warehouse-get-started-create-support-ticket.md) és "Frissítés Gen2" lehetséges okaként hivatkoznak.

## <a name="next-steps"></a>További lépések

A frissített adattárház online állapotú. A továbbfejlesztett architektúra előnyeit, lásd: [erőforrásosztályok számítási feladatok kezeléséhez](resource-classes-for-workload-management.md).
