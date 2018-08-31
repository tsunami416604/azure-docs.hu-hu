---
title: Csoport használatával az Azure SQL Data Warehouse beállítások |} A Microsoft Docs
description: Ötletek a csoport beállításai az Azure SQL Data Warehouse által használható megoldások fejlesztéséhez.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 1f5723bd160abc164779062f213762751e5875c8
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43303378"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Az SQL Data Warehouse beállítások szerint
Ötletek a csoport beállításai az Azure SQL Data Warehouse által használható megoldások fejlesztéséhez.

## <a name="what-does-group-by-do"></a>Mit jelent a GROUP BY?

A [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL-záradék összesíti az adatokat sorok összefoglaló körét. GROUP BY van néhány lehetőség, amely nem támogatja az SQL Data warehouse-bA. Ezek a beállítások a lehetséges megoldások rendelkezik.

Ezek a lehetőségek

* A GROUP BY összesítő
* GROUPING SETS
* A GROUP BY ADATKOCKA

## <a name="rollup-and-grouping-sets-options"></a>Összesítő és grouping sets beállítások
Itt a legegyszerűbb lehetőség segítségével UNION ALL ehelyett végezze el a kumulatív frissítést egy nem explicit szintaxisa hagyatkozik. Eredménye nem pontosan ugyanaz

Az alábbi példa az ÖSSZESÍTÉSI beállítás a GROUP BY utasítás használatával:
```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

ÖSSZESÍTŐ használatával az az előző példában a következő összesítések kéri:

* Ország és régió
* Ország
* Teljes összeg százalékában

Cserélje le a KUMULATÍV, és ugyanazokat az eredményeket ad vissza, használja a UNION ALL, és explicit módon adja meg a szükséges összesítések:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Cserélje le a GROUPING SETS, a minta elvet a alkalmazni. Csak ki kell létre UNION ALL szakaszok az összesítési szintekhez meg szeretné tekinteni.

## <a name="cube-options"></a>Adatkocka-beállítások
Azt is létrehozhat egy CSOPORTOT a WITH CUBE a UNION ALL megközelítéssel. A probléma, hogy a kód gyorsan válhat nehézkes és nehézkessé. A hiba elhárítása érdekében ez a megközelítés speciális használhatja.

A fenti példa használja.

Az első lépés, hogy a "adatkocka", amely meghatározza, hogy a létrehozni kívánt összesítő minden szintjének megadása. Fontos jegyezze fel a CROSS JOIN a két származtatott tábla. Ez létrehoz minden szintet a számunkra. A kód a többi van valóban formázására.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Az alábbiakban látható a CTAS eredményei:

![Adatkocka csoportosítás](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

A második lépés, hogy a céloldali tábla tárolja a köztes eredményeket adja meg:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

A harmadik lépésben pedig történő végighaladásra az összesítés végrehajtása oszlopok a kocka. A lekérdezés a #Cube ideiglenes tábla minden sorára egyszer futtatja és tárolja az eredményeket az #Results ideiglenes tábla

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Végül lépjen vissza az eredményeket a #Results ideiglenes táblából egyszerűen olvassa el

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

A kód felosztani szakaszokra, és uvozuje konstruktor cyklu generálása, a kód a kezelhetőbbé és fenntartható válik.

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: [fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).

