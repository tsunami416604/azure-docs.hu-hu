---
title: Csoportszerint beállítás használata a Synapse SQL-ben
description: A Synapse SQL lehetővé teszi a megoldások fejlesztését különböző GROUP BY beállítások megvalósításával.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 261f75344d250ae8a8d9687f4bcd80535d11716b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429043"
---
# <a name="group-by-options-in-synapse-sql"></a>CSOPORTOSÍTÁSi beállítások a Synapse SQL-ben
A Synapse SQL lehetővé teszi a megoldások fejlesztését különböző GROUP BY beállítások megvalósításával. 

## <a name="what-does-group-by-do"></a>Mit tesz a GROUP BY?

A [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL záradék összesítő sorokba összesíti az adatokat.

Az SQL igény szerinti beállítás a GROUP BY beállítások teljes skáláját támogatja. Az SQL-készlet korlátozott számú GROUP BY beállítást támogat.

## <a name="group-by-options-supported-in-sql-pool"></a>Az SQL-készlet által támogatott GROUP BY beállítások

A GROUP BY néhány olyan beállítással rendelkezik, amelyet az SQL-készlet nem támogat. Ezek a lehetőségek a következő kerülő megoldásokat tartalmaznak:

* CSOPORTOSÍTÁS A ROLLUP-MAL
* CSOPORTOSÍTÓ KÉSZLETEK
* CSOPORTOSÍTÁS A KOCKA SEGÍTSÉGÉVEL

### <a name="rollup-and-grouping-sets-options"></a>Összesítő és csoportosítási beállítások

A legegyszerűbb lehetőség itt az, hogy az UNION ALL használatával hajtsa végre az összesítést, ahelyett, hogy az explicit szintaxisra támaszkodna. Az eredmény pontosan ugyanaz

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

### <a name="cube-options"></a>Kocka beállításai

Az UNION ALL megközelítés sel létrehozhat egy GROUP BY WITH CUBE-ot. A probléma az, hogy a kód gyorsan válhat nehézkes és nehézkes. A probléma enyhítése érdekében használhatja ezt a fejlettebb megközelítést.

Az első lépés a "kocka" meghatározása, amely meghatározza a létrehozni kívánt összesítési szintet. Vegye figyelembe a CROSS JOIN a két származtatott táblák, mert generál minden szinten. A kód többi része formázásra vár.

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

Az alábbi képen a CREATE TABLE AS SELECT eredménye [látható:](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

![Csoportosítás kockák szerint](./media/develop-group-by-options/develop-group-by-cube.png)

A második lépés az időközi eredmények tárolására szolgáló céltábla meghatározása:

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

A harmadik lépés az összesítést végző oszlopok kockája feletti hurok. A lekérdezés az #Cube ideiglenes tábla minden sorának egyszer fog futni. Az eredmények a #Results ideiglenes táblázatban tárolódnak:

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

További fejlesztési tippeket a [fejlesztés áttekintése című témakörben talál.](develop-overview.md)
