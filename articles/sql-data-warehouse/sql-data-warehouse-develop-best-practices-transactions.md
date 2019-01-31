---
title: Az Azure SQL Data Warehouse tranzakciók optimalizálása |} A Microsoft Docs
description: Útmutató kockázatát a hosszú visszaállítások minimalizálhatók a tranzakciós kód az Azure SQL Data Warehouse a teljesítmény optimalizálása érdekében.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/19/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: f5e0b2b75ac111f3221108936f84e5883aebfc1a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55476264"
---
# <a name="optimizing-transactions-in-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse tranzakciók optimalizálása
Útmutató kockázatát a hosszú visszaállítások minimalizálhatók a tranzakciós kód az Azure SQL Data Warehouse a teljesítmény optimalizálása érdekében.

## <a name="transactions-and-logging"></a>Tranzakciók és naplózás
Tranzakciók egy relációsadatbázis-motorján fontos része. Az SQL Data Warehouse tranzakciókat alkalmaz az adatok módosítása során. Ezek a tranzakciók explicit vagy implicit is lehet. Egyetlen INSERT, UPDATE és DELETE utasítások példák az összes implicit tranzakciók. Az explicit tranzakciók TRAN MEGKEZDÉSÉHEZ, TRAN VÉGLEGESÍTÉSÉHEZ vagy VISSZAÁLLÍTÁSI TRAN használja. Az explicit tranzakciók jellemzően akkor használják, amikor több módosításának utasítás egyetlen elemi egységben összekapcsolt kell. 

Az Azure SQL Data Warehouse az adatbázis tranzakciós naplók segítségével végzi a módosításokat. Minden egyes terjesztési saját tranzakciónapló rendelkezik. Napló írási tranzakció automatikus. Nem tartozik a szükséges konfiguráció. Bár ez a folyamat garantálja az írási, a rendszer-terhelés bevezetni. A hatás minimalizálhatja a tranzakciós szempontból hatékony kód írásával. Tranzakciós szempontból hatékony kód nagyjából két kategóriába esik.

* Minimális naplózás szerkezeteket, amikor csak lehetséges használata
* Egyetlen hosszú ideig futó tranzakció elkerülése érdekében hatókörön belüli kötegek használatával dolgozza fel az adatokat
* Egy adott partíciókra a nagyméretű módosítások mintája a partícióváltás elfogadása

## <a name="minimal-vs-full-logging"></a>Minimális és teljes körű naplózás
Teljes mértékben naplózott műveleteket, amelyeket a tranzakciónapló segítségével nyomon követheti, minden sor módosítását, eltérően minimálisan naplózott műveleteknek nyomon követheti, mértékben kiosztásokat és csak a metaadatok módosításokat. Minimális naplózás ezért magában foglalja a naplózás csak a szükséges információkat, egy meghibásodás után, vagy egy explicit kérés (VISSZAÁLLÍTÁS TRAN) a tranzakció visszaállítása. Mivel sokkal kevesebb adatot rögzíti a tranzakciónaplóban, egy mezőgyűjteményre naplózott műveletnek jobban, mint egy hasonló méretű teljes naplózott műveletnek hajt végre. Továbbá mivel kevesebb írások a tranzakciós napló, jön létre, naplóadatokat sokkal kisebb mennyiségű és így további i/o hatékony.

A tranzakció biztonsági korlátozások csak a teljes naplózott műveleteknek vonatkoznak.

> [!NOTE]
> Minimálisan naplózott műveleteknek az explicit tranzakciók vehet részt. A foglalási struktúrák összes módosítást a rendszer nyomon követi, mint lehetőség minimálisan naplózott műveletek visszavonásához. 
> 
> 

## <a name="minimally-logged-operations"></a>Minimálisan naplózott műveletek
A következő műveletek legalább a naplózott képesek:

* HOZZON LÉTRE TABLE AS SELECT ([CTAS](sql-data-warehouse-develop-ctas.md))
* INSERT..SELECT
* INDEX LÉTREHOZÁSA
* ALTER INDEX REBUILD
* A DROP INDEX
* TÁBLA CSONKOLÁSA
* DROP TABLE
* ALTER TABLE SWITCH PARTITION

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> Belső az adatátviteli műveletek (például a SZÓRÁSOS és SHUFFLE) nem érinti a tranzakciós biztonsági korlát.
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>Minimális naplózás tömeges betöltés
A CTAS és INSERT... Válassza ki mindkét tömeges betöltési műveletek. Azonban mindkettő befolyásolják a céloldali tábla definícióját, és terhelés forgatókönyvtől függ. A következő táblázat ismerteti, amikor tömeges műveletek teljesen vagy minimálisan bejelentkeznek:  

| Elsődleges Index | Betöltés forgatókönyv | Naplózási mód |
| --- | --- | --- |
| Halommemória |Bármelyik |**Minimal** |
| Fürtözött Index |Üres céltábla |**Minimal** |
| Fürtözött Index |A betöltött sorok ne legyenek átfedésben a meglévő lapokon a célhelyen |**Minimal** |
| Fürtözött Index |A betöltött sorok átfedésben a meglévő lapokon a célhelyen |Korlátlan |
| Fürtözött Oszlopcentrikus Index |Kötegméret > 102,400 igazítva partíció terjesztési száma = |**Minimal** |
| Fürtözött Oszlopcentrikus Index |Batch-méret < 102,400 igazítva partíció terjesztési kiszolgálónként |Korlátlan |

Fontos megjegyezni, hogy a másodlagos vagy nem fürtözött index frissítéséhez írási mindig lesz teljes mértékben naplózott műveleteknek.

> [!IMPORTANT]
> Az SQL Data Warehouse 60 elosztás van. Ezért, feltéve, hogy az összes sor egyenletesen lesznek elosztva, és ugyanazon a partíción üzenetsorokra, a batch kell 6,144,000 sort tartalmazhat vagy nagyobb minimálisan jelentkezzen be egy fürtözött Oszlopcentrikus Index való írás esetén. Ha a tábla particionálva van, és a beszúrt, sorok span partícióhatárok, majd kell 6,144,000 sorok száma partíció határ, feltéve, hogy még az adatok terjesztési. Mindegyik partíció az egyes terjesztési egymástól függetlenül túl kell lépnie a Beszúrás minimálisan kell bejelentkeznie a terjesztési 102 400 sornál kisebb sor küszöbértékét.
> 
> 

Adatok betöltése egy nem üres táblához fürtözött indexszel gyakran tartalmaznak teljes mértékben naplózott és minimálisan naplózott sort, és vegyesen. Egy fürtözött index egy elosztott terhelésű fa (b-fa) lapján. A lap már tartalmaz egy másik tranzakció sorait ír, ha ezután ezek az írások teljes naplóz. Azonban ha a lap üres majd az oldal az írás minimálisan naplóz.

## <a name="optimizing-deletes"></a>Törlések optimalizálása
TÖRLÉS egy teljes körűen naplózott műveletnek.  Ha törli egy nagy mennyiségű adatot táblázat vagy partíció van szüksége, gyakran logikus további `SELECT` szeretné megtartani, az adatokat, amelyek is lehet futtatni egy mezőgyűjteményre naplózott műveletnek.  Jelölje ki az adatokat, hozzon létre egy új tábla, [CTAS](sql-data-warehouse-develop-ctas.md).  Létrehozása után [átnevezése](/sql/t-sql/statements/rename-transact-sql) ki az újonnan létrehozott tábla a régi tábla felcserélni.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT     *
FROM     [dbo].[FactInternetSales]
WHERE    [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Frissítések optimalizálása
FRISSÍTÉS el egy teljes körűen naplózott műveletnek.  Táblázat vagy partíció sorokat nagy számú frissíteni kell, ha gyakran lehet sokkal hatékonyabb, például egy mezőgyűjteményre naplózott műveletnek használandó [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) ennek a végrehajtására.

A példában a teljes tábla frissítés lett konvertálva Noha egy ctas utasítás úgy, hogy minimális naplózás lehetséges.

Ebben az esetben utólag hozzáadtunk egy kedvezmény összege az értékesítések a táblázatban:

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(    CLUSTERED INDEX
,    DISTRIBUTION = HASH([ProductKey])
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,    20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,    20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,    20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,    20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [!NOTE]
> Hozza létre újra nagy táblák milyen előnyei származhatnak az SQL Data Warehouse számítási feladat felügyeleti funkciókat. További információkért lásd: [erőforrásosztályok számítási feladatok kezeléséhez](resource-classes-for-workload-management.md).
> 
> 

## <a name="optimizing-with-partition-switching"></a>Partíció váltás optimalizálása
Ha nagy méretű módosításokkal belül egy [tábla partíciós](sql-data-warehouse-tables-partition.md), majd egy minta a partícióváltás van értelme. Ha az adatok módosítását jelentős, és több partícióra kiterjedő, majd a külön partíciókban léptetés éri el a ugyanazt az eredményt.

Egy partíció kapcsolójának végrehajtásához szükséges lépéseket a következők:

1. Hozzon létre egy partíciót ki üres
2. Hajtsa végre a frissítést, noha egy ctas utasítás
3. Váltson a meglévő adatai a lejárt tábla
4. Váltson az új adatok
5. Az adatok törlése

Váltás a partíciók azonosíthatók, azonban a következő segítő eljárást kell létrehozni.  

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,    @table_name               NVARCHAR(128)
,    @boundary_value           INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
WITH CTE
AS
(
SELECT     s.name                            AS [schema_name]
,        t.name                            AS [table_name]
,         p.partition_number                AS [ptn_nmbr]
,        p.[rows]                        AS [ptn_rows]
,        CAST(r.[value] AS INT)            AS [boundary_value]
FROM        sys.schemas                    AS s
JOIN        sys.tables                    AS t    ON  s.[schema_id]        = t.[schema_id]
JOIN        sys.indexes                    AS i    ON     t.[object_id]        = i.[object_id]
JOIN        sys.partitions                AS p    ON     i.[object_id]        = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes        AS h    ON     i.[data_space_id]    = h.[data_space_id]
JOIN        sys.partition_functions        AS f    ON     h.[function_id]        = f.[function_id]
LEFT JOIN    sys.partition_range_values    AS r     ON     f.[function_id]        = r.[function_id] 
                                                AND r.[boundary_id]        = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT    *
FROM    CTE
WHERE    [schema_name]        = @schema_name
AND        [table_name]        = @table_name
AND        [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Ez az eljárás maximalizálja a kód, és tartja a partícióváltás tömörebb példa.

A következő kód bemutatja egy teljes partícióváltás rutin eléréséhez korábban említett lépéseket.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(    DISTRIBUTION = HASH([ProductKey])
,    CLUSTERED COLUMNSTORE INDEX
,     PARTITION     (    [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES    (    20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,    [OrderDateKey] 
,    [DueDateKey]  
,    [ShipDateKey] 
,    [CustomerKey] 
,    [PromotionKey] 
,    [CurrencyKey] 
,    [SalesTerritoryKey]
,    [SalesOrderNumber]
,    [SalesOrderLineNumber]
,    [RevisionNumber]
,    [OrderQuantity]
,    [UnitPrice]
,    [ExtendedAmount]
,    [UnitPriceDiscountPct]
,    ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,    [ProductStandardCost]
,    [TotalProductCost]
,    ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,    [TaxAmt]
,    [Freight]
,    [CarrierTrackingNumber] 
,    [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE    OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]    SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))    +' TO [dbo].[FactInternetSales_out] PARTITION '    +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))    +' TO [dbo].[FactInternetSales] PARTITION '        +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Kis méret kisebb kötegekben naplózás
A nagy mennyiségű adat módosításának műveletek célszerű, a művelet felosztása adattömbökre vagy munkahelyi mértékegység hatókör-kötegek felhasználójának.

A következő kód egy működő példát. A Köteg mérete kiemelheti a technika triviális számnak van beállítva. A valóságban ez jelentősen nagyobb lenne a Köteg mérete. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (    DISTRIBUTION = ROUND_ROBIN
        ,    HEAP
        )
AS
SELECT    ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,        SalesOrderNumber
,        SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE    [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE    @seq_start        INT = 1
,        @batch_iterator    INT = 1
,        @batch_size        INT = 50
,        @max_seq_nmbr    INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE    @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,        @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE    @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT    1
            FROM    #t t
            WHERE    seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND        FactInternetSales.SalesOrderNumber        = t.SalesOrderNumber
            AND        FactInternetSales.SalesOrderLineNumber    = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Felfüggesztés és méretezés útmutató
Az Azure SQL Data Warehouse lehetővé teszi, hogy [szüneteltetése, folytatása és méretezhet](sql-data-warehouse-manage-compute-overview.md) adattárházát igény szerint. Ha felfüggeszti vagy méretezi az SQL Data Warehouse, fontos megérteni, hogy szükségszerű tranzakciók azonnal; befejeződött-e vissza lesz állítva a nyitott tranzakciók okozza. Ha a számítási feladatok állított ki egy hosszú ideig futó és a hiányos adatok módosítását, a felfüggesztés vagy méretezés művelet előtt, a munkahelyi kell vonható vissza. Ez a visszavonása hatással lehet a felfüggesztéséhez vagy méretezéséhez az Azure SQL Data Warehouse-adatbázis szükséges időt. 

> [!IMPORTANT]
> Mindkét `UPDATE` és `DELETE` teljes naplózott művelet, így ezek visszavonhatja vagy visszaállíthatja a műveletek sokkal hosszabb, mint egyenértékű minimálisan naplózott műveletek. 
> 
> 

A legjobb forgatókönyvet, hogy lehetővé teszik a flight adatmódosítási művelet befejezése előtt felfüggesztés vagy méretezés az SQL Data warehouse-bA. Azonban ez a forgatókönyv nem mindig feltétlenül gyakorlati. Kockázatát, hogy a hosszú visszaállítások, fontolja meg az alábbi lehetőségek közül:

* Módosítsa úgy a hosszú ideig futó műveletek [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)
* A művelet felosztása adattömbökre; egy soraiból álló halmazok működő

## <a name="next-steps"></a>További lépések
Lásd: [SQL Data Warehouse-tranzakciók](sql-data-warehouse-develop-transactions.md) tudhat meg többet elkülönítési szinten és a tranzakciós korlátok.  Egyéb ajánlott eljárások áttekintését lásd: [SQL Data Warehouse – gyakorlati tanácsok](sql-data-warehouse-best-practices.md).

