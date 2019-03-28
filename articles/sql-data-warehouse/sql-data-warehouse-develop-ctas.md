---
title: Az Azure SQL Data Warehouse létrehozása a TABLE AS SELECT (CTAS) |} A Microsoft Docs
description: A CREATE TABLE AS SELECT (CTAS) utasítás az Azure SQL Data Warehouse-megoldások fejlesztése programozási tippek.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/26/2019
ms.author: mlee3gsd
ms.reviewer: igorstan
ms.openlocfilehash: f791f460efec1b84533379e74add003619dbac6f
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521567"
---
# <a name="using-create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse használatával a CREATE TABLE AS SELECT (CTAS)
A CREATE TABLE AS SELECT (CTAS) T-SQL utasítást az Azure SQL Data Warehouse-megoldások fejlesztése programozási tippek.

## <a name="what-is-create-table-as-select-ctas"></a>Mit jelent a CREATE TABLE AS SELECT (CTAS)?

A [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) , vagy a CTAS utasítás az a legfontosabb érhető el a T-SQL-funkciók egyikét. Egy párhuzamos művelet, amely létrehoz egy új táblát egy kiválasztási utasítás kimenete alapján. A CTAS, a legegyszerűbb módja létrehozásához és adatok beszúrása egy tábla egyetlen paranccsal. 

## <a name="selectinto-vs-ctas"></a>VÁLASSZON... AZ vs. CTAS
Adathozzáférésnek felszámított változata is érdemes lehet CTAS a [kiválasztása... AZ](/sql/t-sql/queries/select-into-clause-transact-sql) utasítást.

Az alábbi, például egy egyszerű SELECT... BE:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

VÁLASSZON... IMPORTÁLÁSA azonban nem teszi lehetővé, hogy módosítja a terjesztési mód vagy az index típusa a művelet részeként. `[dbo].[FactInternetSales_new]` létrejön az alapértelmezett telepítési típus használatával ROUND_ROBIN és az alapértelmezett táblaszerkezet FÜRTÖZÖTT OSZLOPCENTRIKUS INDEXET.

A CTAS használata esetén is tudja mindkét elosztása a tábla adatait, valamint a táblázat szerkezetét típusú adja meg.
Az előző példában a CTAS konvertálása:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
,   CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

 

> [!NOTE]
> Ha csak próbál módosítani az indexet a a `CTAS` művelet és a forrástáblában kivonatterjesztés, majd a `CTAS` művelet legjobban hajtja végre, ha ugyanazon terjesztési és oszlop adattípusát. Így elkerülhető, közötti adatáthelyezés terjesztési a művelet, amely hatékonyabb során.
> 
> 

## <a name="using-ctas-to-copy-a-table"></a>A CTAS használata tábla másolása
Talán az egyik leggyakoribb használja a `CTAS` hoz létre egy táblát egy példányát, hogy a DDL módosíthatja. Ha például eredetileg létrehozta a táblát, `ROUND_ROBIN` , és most szeretné módosítani, hogy egy oszlop alapján elosztott tábla `CTAS` van, hogyan kell módosítania az oszlopot. `CTAS` is használható, particionálás, az indexelés vagy oszlop típusának módosítása.

Tegyük fel, ez a tábla alapértelmezett eloszlási típusát használatával létrehozott `ROUND_ROBIN` elosztott, mivel nincs elosztási oszlop van megadva a `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Most szeretné hozzon létre egy új példányát a tábla fürtözött Oszlopcentrikus indexszel rendelkező, így kihasználhatja a fürtözött Oszlopcentrikus táblák teljesítményének. Is szeretné osztani az ebben a táblában a ProductKey, mivel, amelyek várhatóan ebben az oszlopban való csatlakozások, és szeretné kerülni az adatmozgatás ProductKey való csatlakozások során. Végül is hozzáadandó OrderDateKey a particionálás, hogy a régi adatok régi partíciók elvetésével gyorsan törölheti. Íme a CTAS nyilatkozata is, amely a régi tábla szeretné másolni egy új táblába.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Végül nevezheti át a táblákat az új tábla felcserélése, és ezután dobja el a régi táblát.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="using-ctas-to-work-around-unsupported-features"></a>A CTAS használata a nem támogatott funkciók
A CTAS is használható az alábbi nem támogatott funkciók számos megkerüléséhez. Ezt a módszert gyakran bizonyíthatja win/win helyzet lehet, nem csak a kód megfelelőként fogja, de ez gyakran szerint fog futni gyorsabban az SQL Data warehouse-bA. A teljesítmény a teljes körűen párhuzamos működésű kialakításakor eredménye. A CTAS macről körül forgatókönyvek a következők:

* A frissítések ANSI ILLESZTÉSEK
* Törli az ANSI illesztések
* A MERGE utasítás

> [!NOTE]
> Próbálja meg úgy gondolja, hogy "a CTAS első". Ha úgy véli, hogy meg tudja oldani t `CTAS` majd, amely általában a legjobb megoldás – akkor is, ha emiatt több adatot ír.
> 
> 

## <a name="ansi-join-replacement-for-update-statements"></a>ANSI illesztési helyettesíti a update utasítások
Előfordulhat, hogy egy összetett frissítést, amely több mint két tábla ANSI való csatlakozás szintaxis használatával az UPDATE vagy DELETE végrehajtásához.

Tegyük fel, ez a táblázat frissítése kellett:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(    [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,    [CalendarYear]                    SMALLINT        NOT NULL
,    [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

Az eredeti lekérdezés előfordulhat, hogy rendelkezik kikeresi a következőhöz hasonló:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT    [EnglishProductCategoryName]
        ,        [CalendarYear]
        ,        SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Mivel az SQL Data Warehouse nem támogatja az ANSI társítások (JOIN) az `FROM` záradékában egy `UPDATE` utasítással, nem lehet másolni a kód felett némileg módosítás nélkül.

Kombinációját használhatja egy `CTAS` és a egy implicit illesztési cserélje le ezt a kódot:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,        ISNULL(CAST([CalendarYear] AS SMALLINT),0)                         AS [CalendarYear]
,        ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                        AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,        [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>ANSI illesztési helyettesíti a törlési utasítások
Néha a legjobb módszer az adatok törlése a használandó `CTAS`. Ahelyett, hogy az adatok törlése, válassza ki a megőrizni kívánt adatokat. Ez különösen igaz a `DELETE` ANSI használó utasításokat szintaxis csatlakozzon, mert az SQL Data Warehouse nem támogatja az ANSI illesztések az `FROM` záradékában egy `DELETE` utasítást.

A konvertált DELETE utasítást egy példát alább érhető el:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Cserélje le a merge utasítások
Merge utasítások kicserélhető, legalább a részben, a CTAS használatával. Az INSERT és a frissítés konszolidálhatja az egy utasítás be. A törölt rekordok korlátozni kell a `SELECT` utasítással hagyja el az eredmények közül.

Az alábbi példa egy UPSERT szól:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>A CTAS javaslat: Az adattípus és a kimeneti nullázhatóságával explicit módon állapot
Kód áttelepítésekor találhatja, az ilyen típusú kódolási minta futtatása:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Instinctively előfordulhat, hogy úgy gondolja, hogy át kell telepítenie a CTAS ezt a kódot, és helyes-e. Van azonban egy rejtett probléma.

A következő kódot a ugyanaz az eredmény nem működik:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Figyelje meg, hogy az oszlop, "eredménye" előre végzi az adatok típusát és nullázhatóságának a kifejezésben szereplő érték. Nem elég óvatos, ha ez is vezethet, finom eltérések az értékeket.

Próbálja ki az alábbi példa:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Tárolt eredmény érték eltér. A megőrzött értékét az eredményoszlop más kifejezésekben használja, a hiba még nagyobb jelentőségű tényező válik.

![A CTAS eredmények](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Ez különösen fontos az adatmigrálások. Annak ellenére, hogy a második lekérdezés pontosabb valószínűleg nincs probléma. Az adatok eltérő lenne a forrásrendszerben képest, és integritását a migrálás a kérdéseket, amely vezet. Ez az adott ritka esetekben, amikor a "rossz" választ ténylegesen a megfelelőt egyik!

Láthatjuk, hogy ez a két eredményt eltérése oka implicit típus döntő le. Az első példában a tábla határozza meg az oszlop definíciójában. A sor kerül, ha történik egy típusú implicit konverzió. A második példában nincs nincs típusú implicit konverzió, a kifejezés határozza meg az oszlop adattípusát. Figyelje meg, hogy az oszlop a második példában definiálva van egy nullázható oszlopot, mivel az első példában nem. Az első példában a tábla létrehozásakor a rendszer explicit módon definiálva oszlop nullázhatósági. A második példában azt maradt a kifejezést, és alapértelmezés szerint, egy NULL értékű definíció eredményezne.  

A problémák megoldásához, kifejezetten be kell állítania a típusának átalakítása és nullázhatóságának a CTAS utasítás SELECT részén. A tábla létrehozása részben ezen tulajdonságai nem állíthatók be.

Az alábbi példa bemutatja, hogyan lehet kijavítani a kódot:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Vegye figyelembe a következőket:

* CAST vagy CONVERT lehetett használatban van
* ISNULL használatos nullázhatósági nem COALESCE kényszerítése
* ISNULL, akkor a legkülső függvény
* A második az ISNULL része egy állandó például 0

> [!NOTE]
> Nullázhatóságával megfelelően beállítani, hogy a létfontosságú ISNULL és nem COALESCE használatára. COALESCE nem determinisztikus függvény, ezért a kifejezés eredménye minden esetben üresen hagyható. ISNULL eltér. Célszerű a determinisztikus. Ezért amikor a ISNULL függvény második része egy konstans vagy szövegkonstans, majd az eredményül kapott érték lesz NOT NULL.
> 
> 

Ez tipp hasznos nem csak a számítások integritásának biztosítása érdekében. Fontos továbbá tábla partíciós váltáshoz. Tegyük fel, ez a táblázat a tény definiálva van:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Azonban az érték mező értéke azt nem része a forrásadatok számított kifejezés.

A particionált adatkészlet létrehozásához, érdemes tegye a következőket:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

Tökéletesen finom szeretné futtatni a lekérdezést. A probléma származik, amikor megpróbálja végrehajtani a partíció kapcsolójának. A tábladefiníciókat nem egyeznek. Ahhoz, hogy a megfelelő tábladefiníciókat, a CTAS kell módosítani, hogy adjon hozzá egy `ISNULL` függvény megőrizheti az oszlop nullázhatósági attribútuma.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Láthatja tehát, hogy típus konzisztencia és a CTAS nullázhatósági tulajdonságainak karbantartása mérnöki ajánlott gyakorlat. Ez segít a számításokban integritásának fenntartása és is biztosítja, hogy partíció közötti váltás lehetséges.

Tekintse meg a [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) dokumentációját. A legfontosabb utasításokat az Azure SQL Data Warehouse egyike. Ellenőrizze, hogy alaposan ismertetése.

## <a name="next-steps"></a>További lépések
További fejlesztési tippek: [fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).

