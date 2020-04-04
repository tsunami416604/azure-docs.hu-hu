---
title: TÁBLA LÉTREHOZÁSA KIJELÖLÉSKÉNT (CTA)CREATE TABLE AS SELECT (CTAS)
description: Magyarázat és példák a CREATE TABLE AS SELECT (CTAS) utasításra a Synapse SQL-ben a megoldások fejlesztéséhez.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: 8e1b75dfc6a979956ff4a2868027bb769bf7c4ed
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633554"
---
# <a name="create-table-as-select-ctas"></a>TÁBLA LÉTREHOZÁSA KIJELÖLÉSKÉNT (CTA)CREATE TABLE AS SELECT (CTAS)

Ez a cikk ismerteti a CREATE TABLE AS SELECT (CTAS) T-SQL utasítást a Synapse SQL-ben a megoldások fejlesztéséhez. A cikk kódpéldákat is tartalmaz.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

A [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS) utasítás az egyik legfontosabb elérhető T-SQL szolgáltatás. A CTAS egy párhuzamos művelet, amely egy SELECT utasítás kimenetén alapuló új táblát hoz létre. A CTAS a legegyszerűbb és leggyorsabb módja annak, hogy egyetlen paranccsal hozzon létre és szúrjon be adatokat egy táblázatba.

## <a name="selectinto-vs-ctas"></a>Válassza ki... INTO és CTAS

CTAS is a more customizable version of the [SELECT... INTO](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) utasítás.

Az alábbi példa egy egyszerű SELECT... BE:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Válassza ki... Az INTO nem teszi lehetővé a terjesztési módszer vagy az indextípus módosítását a művelet részeként. A `[dbo].[FactInternetSales_new]` létrehozást a ROUND_ROBIN alapértelmezett terjesztési típusával és a FÜRTÖZÖTT COLUMNSTORE INDEX alapértelmezett táblastruktúrájával hozza létre.

A CTAS-szal viszont megadhatja a táblaadatok eloszlását és a táblastruktúra típusát is. Az előző példa CTAS formátumba konvertálása:

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
> Ha csak a CTAS-műveletben próbálja módosítani az indexet, és a forrástábla kivonatoló, tartsa fenn ugyanazt a terjesztési oszlopot és adattípust. Ezzel elkerülhető a keresztelosztási adatok mozgása a művelet során, ami hatékonyabb.

## <a name="use-ctas-to-copy-a-table"></a>Táblázat másolása a CTAS-sel

Talán az egyik leggyakoribb felhasználási CTAS létrehozása egy példányát egy táblázatot annak érdekében, hogy módosítsa a DDL. Tegyük fel, hogy eredetileg `ROUND_ROBIN`úgy hozta létre a táblát, hogy , és most módosítani szeretné egy oszlopon elosztott táblára. A CTAS az elosztási oszlop módosítása. A CTAS segítségével particionálási, indexelési vagy oszloptípusokat is módosíthat.

Tegyük fel, hogy ezt a táblát a `ROUND_ROBIN`tábla alapértelmezett terjesztési típusával `CREATE TABLE`hozta létre, és nem adott meg terjesztési oszlopot a ban.

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

Most létre szeretne hozni egy új példányt `Clustered Columnstore Index`a tábláról egy , így kihasználhatja a fürtözött oszlopcentrikus táblák teljesítményét. Ezt a táblát is `ProductKey`el szeretné osztani a programon, mert ebben az oszlopban az illesztéseket szeretné előre jelsejteni, és el szeretné kerülni az adatok mozgását az illesztések során. `ProductKey` Végül particionálást is szeretne `OrderDateKey`hozzáadni, így a régi partíciók eldobásával gyorsan törölheti a régi adatokat. Itt van a CTAS utasítás, amely átmásolja a régi táblázatot egy új táblába.

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

Végül átnevezheti a táblákat, kicserélheti az új táblát, majd eldobhatja a régi táblát.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>A CTAS használata a nem támogatott szolgáltatások megkerülése

A CTAS segítségével az alább felsorolt nem támogatott szolgáltatások közül több is megkerülhető. Ez a módszer gyakran hasznosnak bizonyulhat, mert nem csak a kód megfelelő lesz, de gyakran gyorsabban fut a Synapse SQL.This method can often prove helpful, because not only will be compliant, but it will often run faster on Synapse SQL. Ez a teljesítmény a teljesen párhuzamos kialakítás eredménye. A forgatókönyvek a következők:

* ANSI CSATLAKOZIK a UPDATEs
* ANSI JOINs a deletes
* EGYESÍTÉSi utasítás

> [!TIP]
> Próbáld meg azt gondolni, hogy "ctas először.". A ctas használatával történő problémamegoldás általában jó megközelítés, még akkor is, ha ennek eredményeképpen több adatot ír.

## <a name="ansi-join-replacement-for-update-statements"></a>ANSI csatlakozás a frissítési utasítások helyettesítése

Előfordulhat, hogy összetett frissítéssel rendelkezik. A frissítés kettőnél több táblát egyesít az ANSI illesztés szintaxisával az UPDATE vagy a DELETE végrehajtásához.

Képzeld el, hogy frissítenie kellett ezt a táblázatot:

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

Az eredeti lekérdezés a következő példához hasonló nak tűnhetett:

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

A Synapse SQL nem támogatja az ANSI-illesztéseket egy `FROM` `UPDATE` utasítás záradékában, így az előző példát nem használhatja módosítás nélkül.

Az előző példa lecseréléséhez ctas és implicit illesztés kombinációjával helyettesítheti:

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

## <a name="ansi-join-replacement-for-delete-statements"></a>ANSI csatlakozás a törlési utasítások helyettesítésére

Néha az adatok törlésének legjobb módszere a CTAS használata, különösen az ANSI illesztési szintaxist használó utasítások esetében. `DELETE` Ennek az az oka, hogy a Synapse `FROM` SQL nem `DELETE` támogatja az ANSI-illesztéseket egy utasítás záradékában. Az adatok törlése helyett jelölje ki a megtartani kívánt adatokat.

Az alábbi példa egy `DELETE` konvertált utasítást mutat be:

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

Az egyesítési utasítások – legalábbis részben – lecserélhetők a CTAS használatával. A `INSERT` és az `UPDATE` egyetlen utasításba egyesítheti. A törölt rekordokat korlátozni `SELECT` kell az utasításból, hogy kihagyják az eredményeket.

A következő példa `UPSERT`a következő:

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

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Explicit állapotadattípus és a kimenet érvényteleníthetősége

A kód áttelepítésekor előfordulhat, hogy az ilyen típusú kódolási mintán fut át:

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

Lehet, hogy úgy gondolja, hogy át kellene telepítenie ezt a kódot a CTAS-ra, és igaza lenne. Azonban van egy rejtett probléma itt.

A következő kód nem ugyanazt az eredményt adja:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Figyelje meg, hogy az "eredmény" oszlop továbbítja a kifejezés adattípusát és nullability értékeit. Az adattípus továbbítása az értékek finom varianciáihoz vezethet, ha nem vigyázunk.

Próbálja ki ezt a példát:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

Az eredményhez tárolt érték eltérő. Mivel az eredményoszlopban a megőrzött értéket más kifejezésekben is használják, a hiba még jelentősebbé válik.

![Képernyőkép a CTAS-eredményekről](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

Ez fontos az adatáttelepítések esetében. Annak ellenére, hogy a második lekérdezés vitathatatlanul pontosabb, van egy probléma. Az adatok eltérőek lennének a forrásrendszerhez képest, és ez az áttelepítés integritásának kérdéseihez vezetne. Ez egyike azoknak a ritka eseteknek, amikor a "rossz" válasz valójában a helyes!

Az ok látjuk a különbség a két eredmény miatt implicit típusú öntés. Az első példában a tábla határozza meg az oszlopdefiníciót. A sor beszúrásakor implicit típusú átalakítás következik be. A második példában nincs implicit típusú átalakítás, mivel a kifejezés határozza meg az oszlop adattípusát.

Figyelje meg azt is, hogy a második példában szereplő oszlop nullable oszlopként lett definiálva, míg az első példában nem. Amikor a tábla az első példában készült, az oszlop nullability-ja explicit módon lett definiálva. A második példában a kifejezésre maradt, és alapértelmezés szerint NULL definíciót eredményezett.

A problémák megoldásához explicit módon be kell állítania a típuskonverziót és a nullázhatóságot a CTAS utasítás SELECT részében. Ezek a tulajdonságok nem állíthatók be a "CREATE TABLE" területen.
A következő példa bemutatja, hogyan lehet kijavítani a kódot:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Vegye figyelembe a következőket:

* Használhatja a CAST vagy a CONVERT.
* Az ISNULL, nem az COALESCE segítségével kényszerítse a nullability-t. Lásd a következő megjegyzést.
* Az ISNULL a legkülső függvény.
* Az ISNULL második része egy állandó, 0.

> [!NOTE]
> Ahhoz, hogy a nullázhatóság megfelelően legyen beállítva, az ISNULL és nem az COALESCE használata létfontosságú. Az COALESCE nem determinisztikus függvény, így a kifejezés eredménye mindig nullable lesz. Az ISNULL más. Ez determinisztikus. Ezért ha az ISNULL függvény második része állandó vagy konstans, az eredményül kapott érték NEM NULL lesz.

A számítások integritásának biztosítása a táblapartíció-váltáshoz is fontos. Képzelje el, hogy ez a tábla ténytáblaként van definiálva:

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

Az összeg mező azonban számított kifejezés. Nem része a forrásadatoknak.

A particionált adatkészlet létrehozásához érdemes lehet a következő kódot használni:

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

A lekérdezés tökéletesen futna. A probléma akkor jelentkezik, amikor megpróbálja a partíció kapcsolót. A tábladefiníciók nem egyeznek. A tábladefiníciók egyezéséhez módosítsa a `ISNULL` CTAS-t úgy, hogy az oszlop nullability attribútumának megőrzéséhez függvényt adjon hozzá.

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

Láthatja, hogy a típuskonzisztencia és a nullelfogadhatósági tulajdonságok karbantartása a CTAS-on a mérnöki szempontból ajánlott eljárás. Segít megőrizni a számítások integritását, és biztosítja, hogy a partícióváltás lehetséges legyen.

A CTAS a Synapse SQL egyik legfontosabb utasítása. Győződjön meg róla, hogy alaposan megértsék. Lásd a [CTAS dokumentációját](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>További lépések

További fejlesztési tippeket a [fejlesztés áttekintésében](sql-data-warehouse-overview-develop.md)talál.
