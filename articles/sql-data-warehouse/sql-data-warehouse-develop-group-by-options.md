---
title: Csoportosítási lehetőségek használata
description: Tippek a csoportok megvalósítási lehetőségeinek végrehajtásához Azure SQL Data Warehouse a megoldások fejlesztéséhez.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 8d5d0f28fa210a56460ced25323b96a68f245895
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685849"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Csoportosítási lehetőségek a SQL Data Warehouseban
Tippek a csoportok megvalósítási lehetőségeinek végrehajtásához Azure SQL Data Warehouse a megoldások fejlesztéséhez.

## <a name="what-does-group-by-do"></a>Mit tesz a GROUP BY do?

A [Group By](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL záradék összesíti az adatokat egy sorok összesítési halmazával. A CSOPORTOSÍTÁSi lehetőségek a SQL Data Warehouse nem támogatottak. Ezek a beállítások megkerülő megoldásokkal rendelkeznek.

Ezek a beállítások

* Csoportosítás ÖSSZESÍTÉSsel
* CSOPORTOSÍTÁSI KÉSZLETEK
* Csoportosítás a KOCKÁval

## <a name="rollup-and-grouping-sets-options"></a>Kumulatív és csoportosítási beállítások
A legegyszerűbb lehetőség az, hogy a UNION ALL utasítást használja az összesítés végrehajtásához ahelyett, hogy az explicit szintaxisra kellene támaszkodnia. Az eredmény pontosan ugyanaz

A következő példa a GROUP BY utasítást használja a kumulatív beállítással:
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

A kumulatív használatával az előző példa a következő összesítéseket kéri:

* Ország és régió
* Ország
* Végösszeg

Ha az ÖSSZESÍTÉSt cserélni szeretné, és ugyanazokat az eredményeket adja vissza, használhatja a UNION ALL utasítást, és explicit módon megadhatja a szükséges összesítéseket:

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

A CSOPORTOSÍTÁSi készletek lecseréléséhez a minta elve érvényes. Csak a megtekinteni kívánt összesítési szintekhez kell létrehoznia a UNION összes szakaszt.

## <a name="cube-options"></a>Adatkocka-beállítások
Létrehozhat egy CSOPORTOT az adatkockával a UNION ALL megközelítés használatával. A probléma az, hogy a kód gyors és nehézkes lehet. Ennek enyhítéséhez ezt a fejlettebb megközelítést használhatja.

A fenti példát fogjuk használni.

Az első lépés a "Cube" meghatározása, amely meghatározza a létrehozni kívánt összesítési szinteket. Fontos, hogy jegyezze fel a két származtatott tábla KERESZTbe ILLESZTÉSét. Ez minden szintet létrehoz. A kód többi része valóban létezik a formázáshoz.

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

A következő CTAS eredményei láthatók:

![Csoportosítás adatkockával](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

A második lépés egy cél tábla meghatározása a közbenső eredmények tárolásához:

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

A harmadik lépés az összesítést végző oszlopok adatkockájának áthurkolása. A lekérdezés egyszer fut a #Cube ideiglenes tábla minden sorában, és az eredményeket a #Results ideiglenes táblában tárolja.

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

Végül visszaállíthatja az eredményeket úgy, hogy egyszerűen az #Results ideiglenes táblából olvassa be a következőt:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

A kód szakaszokra bontásával és a hurkos szerkezet létrehozásával a kód hatékonyabban kezelhető és karbantartható lesz.

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md).

