---
title: Csoport használata az Azure SQL Data Warehouse beállítások |} Microsoft Docs
description: Ötletek a csoport beállításai az Azure SQL Data Warehouse adattárházzal történő, megoldások.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0548983df23b158385783ac777b23268b5ac7d01
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526046"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Az SQL Data Warehouse beállítások szerint kell csoportosítani
Ötletek a csoport beállításai az Azure SQL Data Warehouse adattárházzal történő, megoldások.

## <a name="what-does-group-by-do"></a>Mit jelent a GROUP BY?

A [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL záradék összesíti az adatokat sorok összefoglaló készlete. A GROUP BY rendelkezik néhány lehetőség, amely az SQL Data Warehouse nem támogatja. Ezek a beállítások a lehetséges megoldások rendelkeznek.

Ezek a beállítások

* A GROUP BY összesítő
* GROUPING SETS
* A GROUP BY ADATKOCKA

## <a name="rollup-and-grouping-sets-options"></a>Rollup és grouping sets beállítások
A legegyszerűbb lehetőség meg használni kívánt UNION ALL inkább a kumulatív frissítést ahelyett, hogy a explicit szintaxis hagyatkoznia. Eredménye nem pontosan ugyanaz

A következő példa a KUMULATÍV kapcsolóval használja a GROUP BY utasítást:
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

A ROLLUP, az előző példában a következő összesítések kéri:

* Ország és régió
* Ország
* Végösszeg

Cserélje le a ROLLUP, és ugyanazt az eredményt adja vissza, használhatja a UNION ALL és explicit módon adja meg a szükséges összesítések:

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

Lecseréli a GROUPING SETS, a minta elvet. Csak a csoportosítási szintek meg szeretné tekinteni a UNION ALL szakaszok létrehozásához szükséges.

## <a name="cube-options"></a>Adatkocka-beállítások
Azt is létrehozhat egy csoport által a KOCKA a UNION ALL módszer használatával. A probléma oka, hogy a kód nehézkes és kezelése nehézkessé vehet. Ennek orvoslása érdekében ezzel fejlettebb módszert.

Most használja a fenti példa.

Az első lépés, hogy a "adatkocka", amely definiálja azt a létrehozni kívánt összesítés az összes szint határozza meg. Fontos figyelembe veszik a CROSS JOIN a két származtatott tábla. Ezt követően az összes szint nekünk. A kód a többi van valóban formázásához.

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

A következő a CTAS eredményeit jeleníti meg:

![Csoportosítási szempont adatkocka](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

A második lépése, hogy adja meg a céltábla köztes eredmények tárolására:

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

A harmadik lépése, hogy hurokba építhető az összesítés végrehajtása oszlopok a kocka. A lekérdezés egyszer futnak le a #Cube ideiglenes tábla minden sorára és az eredmények tárolásához a #Results ideiglenes tábla

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

Végül térhet vissza az eredményeket a #Results ideiglenes táblából egyszerűen beolvasásával

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

A kód összeállításának szakaszokra, és olyan ismétlési konstrukció létrehozásakor, a kód lesz kezelhető, és fenntarthatóvá.

## <a name="next-steps"></a>További lépések
További fejlesztési tippek, lásd: [fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).

