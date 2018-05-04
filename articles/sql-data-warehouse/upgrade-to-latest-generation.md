---
title: Frissítés az Azure SQL Data Warehouse legújabb generációját |} Microsoft Docs
description: Azure SQL Data Warehouse frissítsen az Azure hardver- és tároló-architektúra legújabb generációját.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 8ca8156d98932e0e7313375cadc1ace2a3088881
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Teljesítményoptimalizálás az SQL Data Warehouse frissítésével
Azure SQL Data Warehouse frissítsen az Azure hardver- és tároló-architektúra legújabb generációját.

## <a name="why-upgrade"></a>Miért frissíteni?
Mostantól zökkenőmentesen frissítheti az SQL Data Warehouse Gen2 az Azure portálon. Ha egy Gen1 adatraktár, ajánlott frissítése. Frissítse, használhatja a legújabb generációját Azure hardver, és a bővített tároló-architektúra. Kihasználhatja a gyorsabb teljesítményt, a méretezhetőség és a korlátlan oszlopos tárolás. 

## <a name="applies-to"></a>A következőkre vonatkozik
Ez a frissítés Gen1 adatraktárak vonatkozik.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előkészületek
> [!NOTE]
> Ha a meglévő Gen1 adatraktár nincs olyan régióban, ahol Gen2 lehetőség, akkor [georedundáns helyreállítás való Gen2](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) egy támogatott régióban a Powershellen keresztül.
> 
>

1. Ha a frissítendő Gen1 adatraktár fel van függesztve, [folytathatja az adatraktár](pause-and-resume-compute-portal.md).
2. Néhány perc leállás mellett készíteni. 



## <a name="start-the-upgrade"></a>A frissítés megkezdése

1. Keresse fel a Gen1 adatraktárát az Azure portálon, és kattintson a **Gen2 frissítsen**: ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)

2. Alapértelmezés szerint **válassza ki a javasolt teljesítményszintje** az adatraktár alapján a jelenlegi teljesítményszintje Gen1 az alábbi leképezés használatával:
    
| Gen1 | Gen2 |
| :----------------------: | :-------------------: |
|      DW100 – DW1000      |        DW1000c        |
|          DW1200          |        DW1500c        |
|          DW1500          |        DW1500c        |
|          DW2000          |        DW2000c        |
|          DW3000          |        DW3000c        |
|          DW6000          |        DW6000c        |


3. Győződjön meg arról, a munkaterhelés befejeződött fut, valamint leépítésére frissítése előtt. Néhány percig, mielőtt az adatraktár újra online állapotba, mint egy Gen2 adatraktár leállásra lesz. **Kattintson a frissítés**. A Gen2 teljesítményszinttel ára le tiltva jelenleg félig-a próbaidőszak alatt:
    
    ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **A frissítés figyelése** által az Azure portálon állapotának ellenőrzése:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   Az első lépés a frissítési folyamat végighalad a méretezési művelet ("a frissítés - kapcsolat nélküli"), ahol az összes munkamenetek meg lesznek szakítva és kapcsolatok törlődnek. 
   
   A frissítési folyamat második lépése az adatáttelepítés ("a frissítés - Online"). Adatok áttelepítése egy online trickle háttérfolyamatként, amely lassan a régi tároló-architektúra oszlopos adatokat az új tároló-architektúra, ami a helyi SSD-gyorsítótárba helyezi. Ebben az időszakban az adatraktár lekérdezésére és betöltése online lesz. Az adatok lekérdezése, függetlenül attól, hogy az áttelepítés vagy nem lesz. Az adatáttelepítés attól függően, hogy az adatok mérete, a teljesítményszintet és a oszlopcentrikus szegmensek száma különböző ütemben történik. 

5. **Nem kötelező javaslat:** az adatok áttelepítési háttér folyamat lebonyolításához, javasoljuk, hogy azonnal kényszerítheti az adatmozgás futtatásával [Alter Index rebuild](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-index) összes oszlopcentrikus táblán egy nagyobb slo-t és az erőforrás az osztály. Ezt a műveletet offline állapotban a trickle háttérfolyamatként; képest azonban adatáttelepítés lesz sokkal gyorsabb, ahol, majd teljes mértékben kihasználhassa az új egyszer kész, de jó minőségű rowgroups emelt szintű tároló-architektúra. 

A következő lekérdezés állít elő, az áttelepítési folyamat lebonyolításához szükséges az Alter Index Rebuild parancsokat:

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
A frissített adatraktár online állapotban. A továbbfejlesztett architektúra előnyeit, lásd: [erőforrás-osztályok a munkaterhelés felügyeleti](resource-classes-for-workload-management.md).
 
