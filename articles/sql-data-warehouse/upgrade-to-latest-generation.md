---
title: Frissítés az Azure SQL Data Warehouse legújabb generációját |} Microsoft Docs
description: Azure SQL Data Warehouse frissítése a legújabb generációját Azure hardver- és tároló-architektúra lépéseket.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.services: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/02/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 6ea45398b0bf7fca43c75797313b7e683972b1ab
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="optimize-performance-by-upgrading-sql-data-warehouse"></a>Frissítse az SQL Data Warehouse teljesítményének optimalizálása

Mostantól zökkenőmentesen frissítheti a számítási teljesítmény rétegben az Azure portálon optimalizálása. Ha a rugalmasság adatraktár egy optimalizált, ajánlott frissítése a legújabb Azure hardver- és egy speciális tároló-architektúra beszerzéséhez. Jobb teljesítmény, a méretezhetőség és a korlátlan oszlopos tárolási kihasználni fogja. 

## <a name="applies-to"></a>A következőkre vonatkozik
Ez a frissítés a rugalmasság teljesítményszinttel optimalizálása az adatraktárak vonatkozik.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előkészületek

> [!NOTE]
> 3/30-től rendelkeznie kell [kiszolgáló szintű naplózás](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing#subheading-8) ki van kapcsolva a frissítés megkezdése előtt.
> 
>

> [!NOTE]
> Ha a rugalmasság adatraktár nincs optimalizálva a meglévő régióban adott optimalizálva számítási érhető el, akkor [georedundáns helyreállítás a számítási optimalizálása](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-restore-database-powershell#restore-from-an-azure-geographical-region) egy támogatott régióban a Powershellen keresztül.
> 
>

1. Ha a frissítendő rugalmasság adatraktár optimalizálása fel van függesztve, [folytathatja az adatraktár](pause-and-resume-compute-portal.md).
2. Néhány perc leállás mellett készíteni. 



## <a name="start-the-upgrade"></a>A frissítés megkezdése

1. Keresse fel a rugalmasság adatraktárát az Azure portálon, és kattintson a optimalizálása **számítás optimalizált frissítés**: ![Upgrade_1](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_1.png)

2. Alapértelmezés szerint **válassza ki a javasolt teljesítményszintje** az adatraktár alapján a jelenlegi teljesítményszintje a rugalmasság optimalizálása az alábbi leképezés használatával:
    
| Rugalmasságra optimalizált | Számítási teljesítményre optimalizált |
| :----------------------: | :-------------------: |
|      DW100 – DW1000      |        DW1000c        |
|          DW1200          |        DW1500c        |
|          DW1500          |        DW1500c        |
|          DW2000          |        DW2000c        |
|          DW3000          |        DW3000c        |
|          DW6000          |        DW6000c        |


3. Győződjön meg arról, a munkaterhelés befejeződött fut, valamint leépítésére frissítése előtt. Néhány percig, mielőtt az adatraktár újra online állapotba, mint az adatraktár számítási optimalizálása leállásra lesz. **Kattintson a frissítés**. A számítási teljesítmény rétegben optimalizálása ára le tiltva jelenleg félig-a próbaidőszak alatt:
    
    ![Upgrade_2](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_2.png)

4. **A frissítés figyelése** által az Azure portálon állapotának ellenőrzése:

   ![Upgrade3](./media/sql-data-warehouse-upgrade-to-latest-generation/Upgrade_to_Gen2_3.png)
   
   Az első lépés a frissítési folyamat végighalad a méretezési művelet ("a frissítés - kapcsolat nélküli"), ahol az összes munkamenetek meg lesznek szakítva és kapcsolatok törlődnek. 
   
   A frissítési folyamat második lépése az adatáttelepítés ("a frissítés - Online"). Adatok áttelepítése egy online trickle háttérfolyamatként, amely lassan helyez oszlopos a régi Gen1 tároló-architektúra át adatokat az új Gen2 tároló-architektúra ki a helyi SSD-gyorsítótár Gen2. Ebben az időszakban az adatraktár lekérdezésére és betöltése online lesz. Az adatok lekérdezése, függetlenül attól, hogy az áttelepítés vagy nem lesz. Az adatáttelepítés attól függően, hogy az adatok mérete, a teljesítményszintet és a oszlopcentrikus szegmensek száma különböző ütemben történik. 

5. **Nem kötelező javaslat:** az adatok áttelepítési háttér folyamat lebonyolításához, javasoljuk, hogy azonnal kényszerítheti az adatmozgás futtatásával [Alter Index rebuild](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-tables-index) összes oszlopcentrikus táblán egy nagyobb slo-t és az erőforrás az osztály. Ezt a műveletet offline állapotban a trickle háttérfolyamatként; képest azonban adatáttelepítés lesz sokkal gyorsabb, ahol, majd teljes mértékben kihasználhassa a Gen2 tároló-architektúra egyszer kész, de jó minőségű rowgroups. 

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
 
