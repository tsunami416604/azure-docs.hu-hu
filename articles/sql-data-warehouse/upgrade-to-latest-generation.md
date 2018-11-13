---
title: Frissítés a legújabb generációja, az Azure SQL Data Warehouse |} A Microsoft Docs
description: Azure SQL Data Warehouse frissítsen a legújabb generációja, az Azure hardver- és tárolási architektúrát.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/22/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: fe1f2e026aaa4260d34b9b1cb96064053af1c3c7
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2018
ms.locfileid: "51568012"
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Teljesítményoptimalizálás az SQL Data Warehouse frissítésével
Azure SQL Data Warehouse frissítsen a legújabb generációja, az Azure hardver- és tárolási architektúrát.

## <a name="why-upgrade"></a>Miért érdemes frissíteni?
Most már zökkenőmentesen frissítheti az SQL Data Warehouse számítási optimalizált Gen2 szintre az Azure Portalon. Ha egy optimalizált Gen1 számítási réteg data warehouse-ba, a frissítés javasolt. Frissítse, használhatja az Azure-beli hardveres legújabb generációja, és továbbfejlesztett tárolási architektúrát. Használja ki a gyorsabb teljesítmény, méretezhetőség és korlátlan Oszlopalapú tárolást is igénybe vehet. 

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

## <a name="applies-to"></a>A következőkre vonatkozik
Ez a frissítés optimalizált Gen1 számítási réteg data warehouse-adattárházak vonatkozik.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előkészületek
> [!NOTE]
> Ha a meglévő optimalizált Gen1 Compute csomag adatraktár nem szerepel egy régióban, ahol az optimalizált Gen2 számítási szinten érhető el, akkor az [geo-visszaállítás](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) egy támogatott régióban Powershellen keresztül.
> 
>

1. Ha a frissítendő optimalizált Gen1 számítási réteg adattárház szüneteltetve van, [folytathatja az adattárház](pause-and-resume-compute-portal.md).
2. Néhány perc alatt állásidő készíteni. 



## <a name="start-the-upgrade"></a>A frissítés megkezdése

1. Nyissa meg a számítási optimalizált Gen1 szintű adatforgalom adatraktár-az Azure Portalon, és kattintson a **frissítés Gen2-re** kártya feladatok lapján: ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)
    
> [!NOTE]
> Ha nem látja a **frissítés Gen2-re** feladatok lapján, az előfizetés típusa kártya korlátozva van a jelenlegi régióban. [Küldjön el egy támogatási jegyet](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) beolvasni az előfizetés szerepel az engedélyezési listán.

2. Alapértelmezés szerint **a javasolt teljesítményszintet választ a** az adatraktár alapján a jelenlegi teljesítményszint optimalizált Gen1 számítási rétegben az alábbi leképezés használatával:
    
   | COMPUTE-optimalizált Gen1 kapacitás | COMPUTE-optimalizált Gen2-kapacitás |
   | :----------------------: | :-------------------: |
   |      DW100 – DW600 ÉRTÉKRE       |        DW500c         |
   |          DW1000          |        DW1000c        |
   |          DW1200          |        DW1500c        |
   |          DW1500          |        DW1500c        |
   |          DW2000          |        DW2000c        |
   |          DW3000          |        DW3000c        |
   |          DW6000          |        DW6000c        |

3. Győződjön meg arról, a számítási feladat befejeződött fut, és zajlanak a frissítés előtt. Néhány percet, mielőtt újra online állapotba, az optimalizált Gen2 számítási réteg adattárházak kerül az adattárház tapasztalható üzemszünet. **Kattintson a frissítés**. A Compute optimalizált Gen2 szintű teljesítmény szintjének ára jelenleg félig-kapcsolva az előzetes verzió ideje alatt:
    
   ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **A frissítés figyelése** által az Azure Portalon az állapot ellenőrzése:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   Az első lépés a frissítési folyamat halad végig a skálázási művelet ("Upgrading - kapcsolat nélküli"), ahol az összes munkamenetek meg lesznek szakítva és kapcsolatokat a rendszer eldobja. 
   
   A frissítési folyamat második lépése adatok migrálása ("frissítés – Online"). Adatok migrálása egy online trickle háttérben futó folyamat, amely lassan áthelyezi Oszlopalapú a régi tároló-architektúra az új tároló-architektúra, kihasználva a helyi SSD-gyorsítótárat. Ebben az időszakban az adattárház lekérdezésére és betöltése elérhető lesz. Minden adatát lekérdezhetők, függetlenül attól, hogy azt migrálták, vagy nem lesz. Az adatáttelepítés függően az adatok mérete, a teljesítményszintet és az oszlopcentrikus szegmensek száma különböző ütemben történik. 

5. **Nem kötelező javaslat:** gyorsíthatja fel az adatok áttelepítési háttérfolyamatként, azonnal kényszerítheti az adatmozgatás futtatásával [Alter Index rebuild](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index) kellene lekérdezési nagyobb slo-t, az összes elsődleges oszlopcentrikus táblák és erőforrásosztályhoz. Ez a művelet **offline** képest a trickle rétegzést, amely függően számát és méretét a táblák órát is igénybe vehet; azonban adatáttelepítés fog sokkal gyorsabb, ahol, majd teljes mértékben kihasználhatják az új továbbfejlesztett tárolási architektúrát, a folyamat végén a magas színvonalú naplóbájtot. 

A következő lekérdezést hoz létre a szükséges az Alter Index Rebuild parancsokat gyorsíthatja fel az áttelepítési folyamat:

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



## <a name="next-steps"></a>További lépések
A frissített adattárház online állapotú. A továbbfejlesztett architektúra előnyeit, lásd: [erőforrásosztályok számítási feladatok kezeléséhez](resource-classes-for-workload-management.md).
 
