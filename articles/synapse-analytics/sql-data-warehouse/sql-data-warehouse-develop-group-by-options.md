---
title: Csoportosítás beállítások szerint
description: Tippek a csoportok megvalósításához az Azure SQL Data Warehouse-ban a megoldások fejlesztéséhez.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f77445e80e701053b7fbfa1aa559248cf505353c
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350513"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Csoportosítás az SQL Data Warehouse beállításai szerint
Tippek a csoportok megvalósításához az Azure SQL Data Warehouse-ban a megoldások fejlesztéséhez.

## <a name="what-does-group-by-do"></a>Mit csinál a GROUP BY?

A [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL záradék összesítő sorokba összesíti az adatokat. A GROUP BY néhány olyan beállítással rendelkezik, amelyet az SQL Data Warehouse nem támogat. Ezek a lehetőségek kerülő megoldásokkal rendelkeznek.

Ezek a lehetőségek

* CSOPORTOSÍTÁS A ROLLUP-MAL
* CSOPORTOSÍTÓ KÉSZLETEK
* CSOPORTOSÍTÁS A KOCKA SEGÍTSÉGÉVEL

## <a name="rollup-and-grouping-sets-options"></a>Összesítő és csoportosítási beállítások
A legegyszerűbb lehetőség itt az UNION ALL használata az összesítés végrehajtásához, nem pedig az explicit szintaxisra támaszkodva. Az eredmény pontosan ugyanaz

A következő példa a GROUP BY utasítást használja a ROLLUP beállítással:
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

Az ÖSSZESÍTŐ használatával az előző példa a következő összesítéseket kéri:

* Ország és régió
* Ország
* Végösszeg

Az ÖSSZESÍTŐ lecseréléséhez és ugyanazt az eredményt adhatja vissza, használhatja az UNION ALL-t, és kifejezetten megadhatja a szükséges összesítéseket:

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

A GROUPING SETS lecseréléséhez a minta elve vonatkozik. Csak létre kell hoznia az UNION ALL szakaszokat a látni kívánt összesítési szintekhez.

## <a name="cube-options"></a>Kocka beállításai
Az UNION ALL megközelítés sel group by with cube-ot hozhat létre. A probléma az, hogy a kód gyorsan válhat nehézkes és nehézkes. Ennek csökkentése érdekében használhatja ezt a fejlettebb megközelítést.

Használjuk a fenti példát.

Az első lépés a "kocka" meghatározása, amely meghatározza a létrehozni kívánt összesítési szintet. Fontos figyelembe venni a két származtatott tábla KERESZTillesztését. Ez generálja az összes szintet számunkra. A többi kód valóban ott van a formázáshoz.

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

A következőkben a CTAS eredményeit mutatják be:

![Csoportosítás kockák szerint](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

A második lépés az időközi eredmények tárolására alkalmas céltábla megadása:

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

A harmadik lépés az összesítést végző oszlopok kockája. A lekérdezés az #Cube ideiglenes tábla minden sorában egyszer fog futni, és az eredményeket a #Results ideiglenes táblában tárolja.

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

Végül, akkor vissza az eredményeket egyszerűen felolvasása a #Results ideiglenes táblázat

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

A kód szakaszokra bontásával és egy hurkolt szerkezet létrehozásával a kód kezelhetőbbé és karbantarthatóbbá válik.

## <a name="next-steps"></a>További lépések
További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](sql-data-warehouse-overview-develop.md)

