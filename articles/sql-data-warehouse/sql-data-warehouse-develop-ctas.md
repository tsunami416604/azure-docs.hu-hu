---
title: CREATE TABLE A SELECT (CTAS)
description: A megoldások fejlesztéséhez használt SQL Analytics CREATE TABLE AS SELECT (CTAS) utasításának magyarázata és példái.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 09a543ac4b4f77f0c7b7efd2411b962fa9fa2769
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195906"
---
# <a name="create-table-as-select-ctas-in-sql-analytics"></a>CREATE TABLE a SELECT (CTAS) használatával az SQL Analyticsben

Ez a cikk bemutatja az SQL Analytics CREATE TABLE AS SELECT (CTAS) T-SQL-utasítását a megoldások fejlesztéséhez. A cikk emellett példákat is tartalmaz.

## <a name="create-table-as-select"></a>CREATE TABLE VÁLASSZA KI

A [CREATE TABLE as Select](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) (CTAS) utasítás az elérhető legfontosabb T-SQL-funkciók egyike. A CTAS egy párhuzamos művelet, amely egy SELECT utasítás kimenete alapján létrehoz egy új táblázatot. A CTAS az a legegyszerűbb és leggyorsabb módszer, amellyel egyetlen paranccsal hozhatók létre és helyezhetők be az adattábla.

## <a name="selectinto-vs-ctas"></a>Válassza a... lehetőséget. A vs. CTAS

A CTAS a [Select... testreszabható verziója. INTO](/sql/t-sql/queries/select-into-clause-transact-sql) utasítás.

A következő példa egy egyszerű KIJELÖLÉSt mutat be... A

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Válassza a... lehetőséget. A-ben nem teszi lehetővé, hogy a művelet részeként módosítsa a terjesztési módszert vagy az index típusát. A `[dbo].[FactInternetSales_new]` a ROUND_ROBIN alapértelmezett terjesztési típusával, a FÜRTÖZÖTT OSZLOPCENTRIKUS INDEX alapértelmezett struktúrájának használatával hozható létre.

A CTAS segítségével azonban megadhatja a tábla és a tábla szerkezetének eloszlását is. Az előző példa átalakítása CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> Ha csak a CTAS-művelet indexét próbálja módosítani, és a forrástábla kivonatot terjesztett, akkor ugyanazt a terjesztési oszlopot és adattípust kell fenntartania. Ezzel elkerülhető a művelet során felmerülő adatáthelyezés, ami hatékonyabb.

## <a name="use-ctas-to-copy-a-table"></a>Táblázat másolása CTAS használatával

A CTAS egyik leggyakoribb felhasználási célja, hogy a DDL módosításához létrehozza a tábla másolatát. Tegyük fel, hogy eredetileg `ROUND_ROBIN`ként hozta létre a táblázatot, és most módosítani szeretné egy oszlopra terjesztett táblára. A CTAS a terjesztési oszlop módosítása. A CTAS a particionálás, az indexelés vagy az oszlopok típusának módosítására is használhatja.

Tegyük fel, hogy a táblázatot a `ROUND_ROBIN`alapértelmezett terjesztési típusával hozta létre, és nem határoz meg terjesztési oszlopot a `CREATE TABLE`.

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
    CustomerPONumber nvarchar(25));
```

Most létre kell hoznia a táblázat egy új példányát, amely egy `Clustered Columnstore Index`, így kihasználhatja a fürtözött Oszlopcentrikus-táblák teljesítményét. Ezt a táblázatot a `ProductKey`is el szeretné terjeszteni, mert az illesztéseket várhatóan az oszlophoz kívánja használni, és nem szeretné elkerülni az adatáthelyezést `ProductKey`. Végül a particionálást is érdemes `OrderDateKey`, így a régi partíciók eldobásával gyorsan törölheti a régieket. Itt látható a CTAS utasítás, amely egy új táblába másolja a régi táblát.

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

Végül átnevezheti a táblázatokat, és felcserélheti az új táblázatba, majd elhúzhatja a régi táblát.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>A CTAS használata a nem támogatott funkciók megkerülésére

A CTAS használatával az alább felsorolt nem támogatott funkciók körét is megtalálhatja. Ez a módszer gyakran bizonyulhat hasznosnak, mivel nem csak a kódnak megfelelőnek kell lennie, de gyakran gyorsabban futnak az SQL Analytics szolgáltatásban. Ez a teljesítmény teljesen párhuzamos kialakításának eredménye. A forgatókönyvek a következők:

* ANSI-ILLESZTÉSek a frissítésekhez
* A törlésekhez tartozó ANSI-illesztések
* MERGE utasítás

> [!TIP]
> Próbálja ki először a "CTAS" kifejezést. A CTAS használatával történő probléma megoldása általában jó megoldás, még akkor is, ha az eredmény több adattal is rendelkezik.

## <a name="ansi-join-replacement-for-update-statements"></a>ANSI-csatlakozás a frissítési utasításokhoz

Előfordulhat, hogy összetett frissítéssel rendelkezik. A frissítés a frissítés vagy a törlés végrehajtásához a kettőnél több táblát is összekapcsol az ANSI JOIN szintaxis használatával.

Képzelje el, hogy frissítenie kell ezt a táblázatot:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

Lehetséges, hogy az eredeti lekérdezés a következő példához hasonlóan néz ki:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
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
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

Az SQL Analytics nem támogatja az ANSI illesztéseket egy `UPDATE` utasítás `FROM` záradékában, így módosítás nélkül nem használhatja az előző példát.

Az előző példát lecserélve egy CTAS és egy implicit illesztés kombinációját is használhatja:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>ANSI illesztés a DELETE utasításokhoz

Előfordulhat, hogy az adattörlés legjobb módja az CTAS használata, különösen az olyan `DELETE` utasítások esetében, amelyek ANSI illesztési szintaxist használnak. Ennek az az oka, hogy az SQL Analytics nem támogatja a `DELETE` utasítás `FROM` záradékában található ANSI-illesztéseket. Az adattörlés helyett válassza ki a megőrizni kívánt adatértékeket.

A következő példa egy átalakított `DELETE` utasítást mutat be:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT p.ProductKey
, p.EnglishProductName
,  p.Color
FROM  dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON p.ProductKey = s.ProductKey;

RENAME OBJECT dbo.DimProduct TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Egyesítési utasítások cseréje

Az egyesítési utasítások (legalább részben) a CTAS használatával cserélhetők fel. A `INSERT` és a `UPDATE` egyetlen utasítással egyesíthető. A törölt rekordokat a `SELECT` utasításból kell korlátozni, hogy kihagyják az eredményekből.

A következő példa egy `UPSERT`re mutat:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT s.[ProductKey]
, s.[EnglishProductName]
, s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
, p.[EnglishProductName]
, p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
);

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Explicit módon állapot adattípus és a kimenet nullára

A kód áttelepítésekor előfordulhat, hogy az ilyen típusú kódolási mintán keresztül futtatja a következőt:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

Előfordulhat, hogy érdemes áttelepítenie ezt a kódot a CTAS-re, és helyes lenne. Itt azonban rejtett probléma van.

A következő kód nem eredményezi ugyanazt az eredményt:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Figyelje meg, hogy a "result" oszlop továbbítja a kifejezés adattípusát és nullára vonatkozó értékeit. Ha nem vigyáz rá, az adattípusok továbbítása enyhe eltéréseket eredményezhet az értékekben.

Próbálja ki ezt a példát:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

Az eredményként tárolt érték eltér. Mivel az eredmény oszlopban megőrzött érték más kifejezésekben is szerepel, a hiba még jelentősebb lesz.

![Képernyőkép a CTAS eredményeiről](media/sql-data-warehouse-develop-ctas/ctas-results.png)

Ez az adatáttelepítés esetében fontos. Bár a második lekérdezés vitathatatlanul pontosabb, probléma merült fel. Az adatok eltérnek a forrásoldali rendszertől, és ez az áttelepítés során az integritással kapcsolatos kérdésekhez vezet. Ez az egyik ritka eset, amikor a "rossz" válasz valójában a megfelelő.

Ennek az az oka, hogy a két eredmény közötti eltérések a implicit típusú öntvények miatt láthatók. Az első példában a tábla az oszlop definícióját határozza meg. A sor beszúrásakor egy implicit típusú konverzió történik. A második példában nincs implicit típusú átalakítás, mivel a kifejezés az oszlop adattípusát határozza meg.

Figyelje meg azt is, hogy a második példában szereplő oszlop üres oszlopként van definiálva, míg az első példában nem. Ha a tábla az első példában lett létrehozva, az oszlop nullát explicit módon definiálták. A második példában a kifejezést a kifejezésre hagyták, és alapértelmezés szerint NULL definíciót eredményezne.

A problémák megoldásához explicit módon be kell állítania a típus átalakítását és a nullát a CTAS utasítás SELECT részében. Ezek a tulajdonságok nem állíthatók be a következőben: "CREATE TABLE".
A következő példa a kód kijavítását mutatja be:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Vegye figyelembe a következőket:

* A CAST vagy a CONVERT is használható.
* Ne EGYESÍTse a ISNULL, hogy kényszerítse a NULLát. Tekintse meg a következő megjegyzést.
* A ISNULL a legkülső függvény.
* A ISNULL második része állandó, 0.

> [!NOTE]
> Ahhoz, hogy a nullák helyesen legyenek beállítva, elengedhetetlen a ISNULL használata és a nem EGYESÍTÉS. Az EGYESÍTÉS nem determinisztikus-függvény, ezért a kifejezés eredménye mindig üres lesz. A ISNULL eltér. Ez a determinisztikus. Ezért ha a ISNULL függvény második része állandó vagy literál, az eredményül kapott érték nem NULL.

A számítások integritásának biztosítása szintén fontos a tábla partíciós váltásakor. Képzelje el, hogy ezt a táblázatot a tábla határozza meg:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

Az összeg mező azonban számított kifejezés. Nem a forrásadatok részét képezi.

Particionált adatkészlet létrehozásához a következő kódot érdemes használni:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
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
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

A lekérdezés teljesen jól fut. A probléma a partíciós kapcsoló kipróbálásakor jön. A tábla definíciói nem egyeznek. Ahhoz, hogy a táblázat-definíciók megegyezzenek, módosítsa a CTAS egy `ISNULL` függvény hozzáadásával az oszlop nullára vonatkozó attribútumának megőrzése érdekében.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]   
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Láthatja, hogy a típus konzisztenciája és a CTAS tulajdonság fenntartása egy mérnöki ajánlott eljárás. Segít megőrizni az integritást a számításokban, és biztosítja a partíciók váltásának lehetőségét is.

A CTAS az SQL Analytics egyik legfontosabb utasítása. Győződjön meg róla, hogy alaposan megértette. Tekintse meg a [CTAS dokumentációját](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse).

## <a name="next-steps"></a>További lépések

További fejlesztési tippekért tekintse meg a [fejlesztés áttekintése](sql-data-warehouse-overview-develop.md)című témakört.

