---
title: Csoportosítás beállítások szerint
description: Tippek a csoport a Synapse SQL-készlet ben beállítások végrehajtása.
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
ms.openlocfilehash: 28ac075d043f7605b6dfdac6879063fbe9308123
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619046"
---
# <a name="group-by-options-in-synapse-sql-pool"></a>Csoportosítás a Synapse SQL-készletben

Ebben a cikkben tippeket talál a csoport sql készletben lévő beállítások szerinti megvalósításához.

## <a name="what-does-group-by-do"></a>Mit csinál a GROUP BY?

A [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL záradék összesítő sorokba összesíti az adatokat. A GROUP BY néhány olyan beállítással rendelkezik, amelyet az SQL-készlet nem támogat. Ezek a lehetőségek a következő kerülő megoldásokat tartalmaznak:

* CSOPORTOSÍTÁS A ROLLUP-MAL
* CSOPORTOSÍTÓ KÉSZLETEK
* CSOPORTOSÍTÁS A KOCKA SEGÍTSÉGÉVEL

## <a name="rollup-and-grouping-sets-options"></a>Összesítő és csoportosítási beállítások

A legegyszerűbb lehetőség itt az, hogy az UNION ALL használatával hajtsa végre az összesítést, ahelyett, hogy az explicit szintaxisra támaszkodna. Az eredmény pontosan ugyanaz.

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
Az UNION ALL megközelítés sel létrehozhat egy GROUP BY WITH CUBE-ot. A probléma az, hogy a kód gyorsan válhat nehézkes és nehézkes. A probléma enyhítése érdekében használhatja ezt a fejlettebb megközelítést.

Az előző példában az első lépés a "kocka" meghatározása, amely meghatározza a létrehozni kívánt összesítési szintet. 

Vegye figyelembe a CROSS JOIN a két származtatott táblák, mivel ez generálja az összes szintet számunkra. A kód többi része formázásra alkalmas:

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

Az alábbi képen a CTAS eredményei láthatók:

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

A harmadik lépés az összesítést végző oszlopok kockája. A lekérdezés az #Cube ideiglenes tábla minden sorának egyszer fog futni. Az eredmények a #Results temp táblázatban tárolódnak:

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

Végül az eredményeket az #Results ideiglenes táblázatból való felolvasással küldheti vissza:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

A kód szakaszokra bontásával és egy hurkolt szerkezet létrehozásával a kód kezelhetőbbé és karbantarthatóbbá válik.

## <a name="next-steps"></a>További lépések
További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](sql-data-warehouse-overview-develop.md)

