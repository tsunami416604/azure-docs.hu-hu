---
title: "Az SQL Data Warehouse beállítások csoportosítás |} Microsoft Docs"
description: "Ötletek a csoport beállításai az Azure SQL Data Warehouse adattárházzal történő, megoldások."
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: f95a1e43-768f-4b7b-8a10-8a0509d0c871
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: queries
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: da71cb834c13da5d0f5690f471efc6c696163f30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Az SQL Data Warehouse beállítások szerint kell csoportosítani
A [GROUP BY] [ GROUP BY] záradék használatával sorok összefoglaló készlete összesített adatokat. Azt is meg funkciókat, amelyeket dolgozott körül, mivel azok nem közvetlenül által támogatott Azure SQL Data Warehouse kibővítő néhány lehetőség.

Ezek a beállítások

* A GROUP BY összesítő
* GROUPING SETS
* A GROUP BY ADATKOCKA

## <a name="rollup-and-grouping-sets-options"></a>Rollup és grouping sets beállítások
A legegyszerűbb lehetőség `UNION ALL` inkább a kumulatív frissítést végrehajtásához ahelyett, hogy a explicit szintaxis hagyatkoznia. Eredménye nem pontosan ugyanaz

Az alábbiakban látható egy példa egy csoport utasítás használatával a `ROLLUP` lehetőséget:

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

ÖSSZESÍTŐ használatával a következő összesítések kért azt:

* Ország és régió
* Ország
* Végösszeg

Lecseréli a használatához szüksége lesz `UNION ALL`; adja meg az összesítéseket, explicit módon kell ugyanazt az eredményt adja vissza:

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

A GROUPING SETS összes végrehajtásához igazolnia kell elfogadják ugyanazt az egyszerű szolgáltatásnevet, de csak a UNION ALL szakaszok az összesítési szintjeinek szeretnénk megnézni létrehozása

## <a name="cube-options"></a>Adatkocka-beállítások
Azt is létrehozhat egy csoport által a KOCKA a UNION ALL módszer használatával. A probléma oka, hogy a kód nehézkes és kezelése nehézkessé vehet. Ennek orvoslása ezzel fejlettebb módszert.

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

A CTAS eredményeinek alább látható:

![][1]

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

Végül azt térhet vissza az eredményeket a #Results ideiglenes táblából egyszerűen beolvasásával

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

A kód összeállításának részre, és olyan ismétlési konstrukció generálása a kód lesz kezelhető, és fenntarthatóvá.

## <a name="next-steps"></a>Következő lépések
További fejlesztési tippek, lásd: [fejlesztői áttekintés][development overview].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png

<!--Article references-->
[development overview]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[GROUP BY]: https://msdn.microsoft.com/library/ms177673.aspx


<!--Other Web references-->
