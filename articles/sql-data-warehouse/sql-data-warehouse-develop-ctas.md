---
title: Az Azure SQL Data Warehouse létrehozása a TABLE AS SELECT (CTAS) |} A Microsoft Docs
description: MAGYARÁZAT és a CREATE TABLE AS SELECT (CTAS) utasítás az Azure SQL Data Warehouse-megoldások fejlesztése a példákat.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/26/2019
ms.author: mlee3gsd
ms.reviewer: jrasnick
ms.custom: seoapril2019
ms.openlocfilehash: c8e9f3ccdfaee64f75443f6a4eb89a3df7c48b0e
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680094"
---
# <a name="create-table-as-select-ctas-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse létrehozása a TABLE AS SELECT (CTAS)

Ez a cikk ismerteti a CREATE TABLE AS SELECT (CTAS) T-SQL utasítást az Azure SQL Data Warehouse használható megoldások fejlesztéséhez. A cikk emellett hitelesítésikód-példák.

## <a name="create-table-as-select"></a>TABLE AS SELECT LÉTREHOZÁSA

A [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) (CTAS) utasítás az a legfontosabb érhető el a T-SQL-funkciók egyikét. A CTAS egy párhuzamos művelet, amely létrehoz egy új táblát egy kiválasztási utasítás kimenete alapján. A CTAS, a legegyszerűbb módja létrehozásához és adatok beszúrása egy tábla egyetlen paranccsal.

## <a name="selectinto-vs-ctas"></a>VÁLASSZON... AZ vs. CTAS

A CTAS verziója egy további testre szabható a [kiválasztása... AZ](/sql/t-sql/queries/select-into-clause-transact-sql) utasítást.

Az alábbiakban látható egy példa egy egyszerű VÁLASSZA... BE:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

VÁLASSZON... BE nem teszi lehetővé, hogy módosítja a terjesztési mód vagy az index típusa a művelet részeként. Létrehozhat `[dbo].[FactInternetSales_new]` ROUND_ROBIN alapértelmezett eloszlási típusát, és az alapértelmezett táblaszerkezet FÜRTÖZÖTT OSZLOPCENTRIKUS index használatával.

A CTAS másrészről, megadhat mindkét a tábla adatait, valamint a táblázat szerkezetét írja be a terjesztési. Az előző példában a CTAS konvertálása:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
    DISTRIBUTION = ROUND_ROBIN
   ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [!NOTE]
> Ha csak próbál módosítani az index a CTAS műveletben, és a forrástáblában kivonatterjesztés, karbantartása azonos terjesztési és oszlop adattípusát. Ezzel elkerülhető a terjesztési közötti adatátvitel során a művelet, amely hatékonyabb.

## <a name="use-ctas-to-copy-a-table"></a>A CTAS használata tábla másolása

Talán az egyik leggyakoribb felhasználási a CTAS létrehozása egy tábla egy példányát annak érdekében, hogy módosítsa a DDL. Most tegyük eredetileg létrehozta a táblát, `ROUND_ROBIN`, és most szeretné módosítani, hogy egy oszlop alapján elosztott tábla. A CTAS, hogyan kell módosítania az oszlopot. A CTAS használatával particionálás, az indexelés vagy oszlop típusának módosítása.

Nézzük például: Ez a táblázat hozta létre az alapértelmezett telepítési típusát `ROUND_ROBIN`, nem adja meg az elosztási oszlop a `CREATE TABLE`.

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

Most szeretné ezt a táblázatot az új példányának létrehozása egy `Clustered Columnstore Index`, így kihasználhatja a fürtözött Oszlopcentrikus táblák teljesítményét. Is terjeszteni szeretné ezt a táblát a `ProductKey`, mert éppen várhatóan ebben az oszlopban való csatlakozások, és szeretné kerülni az adatmozgatás illesztések során a `ProductKey`. Végül is hozzáadni kívánt a particionálás `OrderDateKey`, így a régi adatok régi partíciók elvetésével gyorsan törölheti. Íme a CTAS utasítás, amely másolja át a régi tábla egy új táblába.

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

Végül nevezheti át a táblákat, az új tábla felcserélése, és ezután dobja el a régi táblát.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>A CTAS használata nem támogatott funkciók

A CTAS megkerüléséhez az alábbi nem támogatott funkciók számos is használhatja. Ezt a módszert gyakran bizonyíthatja hasznos, mert nem csak a kód megfelelő, de ez gyakran gyorsabban futnak az SQL Data warehouse-bA. A teljesítmény a teljes körűen párhuzamos működésű kialakításakor eredménye. Forgatókönyvek a következők:

* A frissítések ANSI ILLESZTÉSEK
* Törli az ANSI illesztések
* A MERGE utasítás

> [!TIP]
> Próbálja meg úgy gondolja, hogy "a CTAS első." A CTAS a probléma megoldására alapvetően egy jó módszer, még akkor is, ha több adatot eredményeképpen írunk.

## <a name="ansi-join-replacement-for-update-statements"></a>ANSI illesztési helyettesíti a update utasítások

Előfordulhat, hogy egy összetett frissítést. A frissítés több mint két tábla együtt csatlakozik az ANSI illesztési szintaxist az UPDATE vagy DELETE végrehajtásához.

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

Az eredeti lekérdezés előfordulhat, hogy rendelkezik kikeresi következő példához hasonló tartalom:

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

Az SQL Data Warehouse nem támogatja az ANSI illesztések az `FROM` záradékában egy `UPDATE` utasítással, ezért nem használható az előző példával azt módosítása nélkül.

Noha egy ctas utasítás és a egy implicit illesztési együttes használatával az előző példában cserélje le:

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

Néha a legjobb módszer az adatok törlése a CTAS, különösen a használandó `DELETE` ANSI használó utasításokat join szintaxist. Ennek az az oka az SQL Data Warehouse nem támogatja az ANSI illesztések az `FROM` záradékában egy `DELETE` utasítást. Ahelyett, hogy az adatok törlése, válassza ki a megőrizni kívánt adatokat.

Az alábbiakban egy példát a konvertált a `DELETE` utasítást:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
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

Lecserélheti merge utasításokban, legalább a részben, a CTAS használatával. Kombinálhatja a `INSERT` és a `UPDATE` be egy utasítás. A törölt rekordok korlátozni kell a `SELECT` utasítással hagyja el az eredmények közül.

Az alábbi példa egy `UPSERT`:

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

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Az adattípus és a kimeneti nullázhatóságával explicit módon állapot

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

Előfordulhat, hogy úgy gondolja, hogy át kell telepítenie a CTAS ezt a kódot, és lenne megfelelő. Van azonban egy rejtett probléma.

Az alábbi kód nem eddig is számtalan előnyét ugyanaz az eredmény:

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

Figyelje meg, hogy az oszlop, "eredménye" előre végzi az adatok típusát és nullázhatóságának a kifejezésben szereplő érték. Végezzen az adatok forward típust vezethet finom eltérések az értékek nem elég óvatos, ha.

Próbálja ki az ebben a példában:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

Tárolt eredmény érték eltér. A megőrzött értékét az eredményoszlop más kifejezésekben használja, a hiba még nagyobb jelentőségű tényező válik.

![Képernyőkép a CTAS eredmények](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Ez fontos a adatmigrálások. Annak ellenére, hogy a második lekérdezés késései pontosabb, probléma van. Az adatok eltérő lenne a forrásrendszerben képest, és integritását a migrálás a kérdéseket, amelyek vezet. Ez az adott ritka esetekben, amikor a "rossz" választ ténylegesen a megfelelőt egyik!

Láthatjuk, hogy a két eredményt eltérése oka implicit típus döntő miatt. Az első példában a tábla határozza meg az oszlop definíciójában. A sor kerül, ha történik egy típusú implicit konverzió. A második példában nincs nincs típusú implicit konverzió, a kifejezés határozza meg az oszlop adattípusát.

Figyelje meg, hogy az oszlop a második példában definiálva van egy nullázható oszlopot, mivel az első példában nem. Az első példában a tábla létrehozásakor a rendszer explicit módon definiálva oszlop nullázhatósági. A második példában maradt a kifejezést, és alapértelmezés szerint eredményezne definíciója null értékű.

A problémák megoldásához, kifejezetten be kell állítania a típusának átalakítása és nullázhatóságának a CTAS utasítás SELECT részén. Ezek a tulajdonságai nem állíthatók be a következő tábla létrehozása.
A következő példa bemutatja, hogyan háríthatja el a kódot:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Vegye figyelembe a következőket:

* A CAST is használhatja, vagy alakítsa át.
* Használja, nem a COALESCE, ISNULL Nullázhatóságával. Olvassa el a következő megjegyzést.
* ISNULL, akkor a legkülső függvény.
* A második része a ISNULL egy állandó, a 0.

> [!NOTE]
> Nullázhatóságával helyesen kell beállítani, a létfontosságú a szereplőknek, ISNULL és nem COALESCE. COALESCE nem determinisztikus függvényt, és ezért a kifejezés eredménye minden esetben üresen hagyható. ISNULL eltér. Célszerű a determinisztikus. Ezért, ha az ISNULL függvény második része egy állandó szám vagy egy, az eredményül kapott érték lesz NOT NULL.

A számítások sértetlenségének biztosítása esetén is fontos tábla partíciós váltás. Tegyük fel, ez a táblázat egy ténytáblát definiálva van:

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

Azonban az összeg mező értéke számított kifejezés. Nem része a forrásadatokat.

A particionált adatkészlet létrehozásához, érdemes a következő kóddal:

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

Tökéletesen szeretné futtatni a lekérdezést. A probléma áll rendelkezésre, hajtsa végre a partíció kapcsolójának megkísérlésekor. A tábladefiníciókat se neshodují. Hogy a tábladefiníciókat megegyeznek, módosítsa a CTAS hozzáadása egy `ISNULL` függvény megőrizheti az oszlop nullázhatósági attribútuma.

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

Láthatja, hogy típus konzisztencia és a CTAS nullázhatósági tulajdonságainak karbantartása egy mérnöki ajánlott eljárás. Segít a számításokban integritásának fenntartása, és emellett biztosítja, hogy partíció közötti váltás lehetséges.

A CTAS a legfontosabb utasításokat az SQL Data Warehouse egyike. Ellenőrizze, hogy alaposan ismertetése. Tekintse meg a [CTAS dokumentáció](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse).

## <a name="next-steps"></a>További lépések

További fejlesztési tippek: a [fejlesztői áttekintés](sql-data-warehouse-overview-develop.md).

