---
title: Tranzakciók optimalizálása
description: Ismerje meg, hogyan optimalizálhatja a tranzakciós kód teljesítményét az SQL Analytics szolgáltatásban, miközben minimalizálhatja a hosszú visszaállítások kockázatát.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/19/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 700f4717db652d678255aaa9fce6ff8b8ff3b52f
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350589"
---
# <a name="optimizing-transactions-in-sql-analytics"></a>Tranzakciók optimalizálása az SQL Analytics szolgáltatásban
Ismerje meg, hogyan optimalizálhatja a tranzakciós kód teljesítményét az SQL Analytics szolgáltatásban, miközben minimalizálhatja a hosszú visszaállítások kockázatát.

## <a name="transactions-and-logging"></a>Tranzakciók és naplózás
A tranzakciók a relációs adatbázis-motor fontos összetevői. Az SQL Analytics tranzakciókat használ az adatok módosítása során. Ezek a tranzakciók lehetnek explicitek vagy implicitek. Az egyszeri INSERT, UPDATE és DELETE utasítások mind példák az implicit tranzakciókra. Explicit transactions use BEGIN TRAN, COMMIT TRAN, or ROLLBACK TRAN. Explicit tranzakciók általában akkor használatosak, ha több módosítási utasítást kell összekötni egyetlen atomi egységben. 

Az SQL Analytics tranzakciós naplók használatával véglegesíti az adatbázis módosításait. Minden disztribúció saját tranzakciónaplóval rendelkezik. A tranzakciónapló írásaautomatikus. Nincs szükség konfigurációra. Azonban, míg ez a folyamat garantálja az írást, mégis többletköltséget jelent a rendszerben. Ezt a hatást a tranzakciós hatékony kód írásával minimalizálhatja. A tranzakciós szempontból hatékony kód nagyjából két kategóriába sorolható.

* Amikor csak lehetséges, használjon minimális naplózási konstrukciókat
* Adatok feldolgozása hatókörrel kötegek használatával, hogy elkerülje az egyes hosszú ideig futó tranzakciókat
* Partícióváltási minta elfogadása egy adott partíció nagy módosításaihoz

## <a name="minimal-vs-full-logging"></a>Minimális és teljes naplózás
A teljesen naplózott műveletekkel ellentétben, amelyek a tranzakciónaplósegítségével követik nyomon az összes sorváltozást, a minimálisan naplózott műveletek csak a mértékallokációk és a metaadat-változások nyomon követését követik nyomon. Ezért a minimális naplózás magában foglalja csak azokat az információkat naplózza, amelyek a tranzakció hiba utáni visszaállításához vagy egy explicit kéréshez (ROLLBACK TRAN) szükségesek. Mivel a tranzakciónaplóban sokkal kevesebb információ kerül nyomon, a minimálisan naplózott művelet jobban teljesít, mint egy hasonló méretű, teljesen naplózott művelet. Továbbá, mivel kevesebb írási művelet megy a tranzakciós naplóba, sokkal kisebb mennyiségű naplóadat jön létre, és így több I/O-hatékony.

A tranzakcióbiztonsági korlátok csak a teljesen naplózott műveletekre vonatkoznak.

> [!NOTE]
> A minimálisan naplózott műveletek explicit tranzakciókban vehetnek részt. Mivel a felosztási struktúrák összes változása nyomon követhető, lehetőség van a minimálisan naplózott műveletek visszaállítására. 
> 
> 

## <a name="minimally-logged-operations"></a>Minimálisan naplózott műveletek
A következő műveletek minimálisan naplózva:

* TÁBLA LÉTREHOZÁSA KIJELÖLÉSKÉNT ([CTA)](sql-data-warehouse-develop-ctas.md)
* Beszúrása.. Válassza ki
* CREATE INDEX
* INDEX-ÚJRAÉPÍTÉS MÓDOSÍTÁSA
* DROP INDEX
* TÁBLÁZAT CSONKOLÁSA
* TÁBLÁZAT LEDOBÁSA
* TÁBLAKAPCSOLÓ PARTÍCIÓ MÓDOSÍTÁSA

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

> [!NOTE]
> A tranzakció biztonsági korlátja nem érinti a belső adatmozgatási műveleteket (például a BROADCAST és a SHUFFLE).
> 
> 

## <a name="minimal-logging-with-bulk-load"></a>Minimális naplózás tömeges betöltéssel
CTAS és INSERT... A SELECT egyaránt tömeges betöltési művelet. Azonban mindkettő befolyásolja a céltábla meghatározása, és függ a terhelési forgatókönyv. Az alábbi táblázat bemutatja, ha a tömeges műveletek teljes vagy minimális naplózása teljes mértékben vagy minimálisan:  

| Elsődleges index | Betöltési forgatókönyv | Naplózási mód |
| --- | --- | --- |
| Halommemória |Bármelyik |**Minimális** |
| Fürtözött index |Üres céltábla |**Minimális** |
| Fürtözött index |A betöltött sorok nem fedik át a célban lévő oldalakat |**Minimális** |
| Fürtözött index |A betöltött sorok átfedésben vannak a célban lévő oldalakkal |Összes |
| Fürtözött oszlopcentrikus index |A kötegméret >= 102 400 partíciónként igazított elosztás |**Minimális** |
| Fürtözött oszlopcentrikus index |A kötegméret < 102 400 partíciónként igazított elosztás |Összes |

Érdemes megjegyezni, hogy a másodlagos vagy nem fürtözött indexek frissítéséhez írt írások mindig teljesen naplózott műveleteket.

> [!IMPORTANT]
> Egy SQL Analytics-adatbázis 60 disztribúcióval rendelkezik. Ezért feltételezve, hogy az összes sor egyenletesen oszlik el, és egyetlen partíción landol, a kötegnek 6 144 000 vagy nagyobb sort kell tartalmaznia ahhoz, hogy minimálisan naplózza a fürtözött oszlopcentrikus indexbe való íráskor. Ha a tábla particionált, és a beszúrt sorok span partíció határokat, majd szüksége lesz 6.144.000 sor partíció határainként, feltételezve, hogy még adateloszlás. Minden partíció minden disztribúció egymástól függetlenül meg kell haladnia a 102.400 sor küszöbértéket a lapka minimálisan bejelentkezett a disztribúcióba.
> 
> 

Az adatok fürtözött indexszel rendelkező nem üres táblába történő betöltése gyakran teljesen naplózott és minimálisan naplózott sorok keverékét tartalmazhatja. A csoportosított index az oldalak kiegyensúlyozott fája (b-fa). Ha a lap írása már tartalmaz sorokat egy másik tranzakció, majd ezek az írások lesz nek teljesen naplózott. Ha azonban az oldal üres, akkor az erre az oldalra írt írás minimálisan naplózva lesz.

## <a name="optimizing-deletes"></a>Törlés optimalizálása
A DELETE egy teljesen naplózott művelet.  Ha nagy mennyiségű adatot kell törölnie egy táblában vagy partíción, `SELECT` gyakran több értelme van a megtartani kívánt adatoknak, amelyek minimálisan naplózott műveletként futtathatók.  Az adatok kijelöléséhez hozzon létre egy új táblát a [CTAS](sql-data-warehouse-develop-ctas.md)segítségével.  A létrehozás után az [ÁTNEVEZÉS](/sql/t-sql/statements/rename-transact-sql) segítségével cserélje ki a régi táblát az újonnan létrehozott táblával.

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
Update egy teljesen naplózott művelet.  Ha egy tábla vagy partíció nagy számú sorát kell frissítenie, gyakran sokkal hatékonyabb lehet egy minimálisan naplózott művelet, például a [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) használata.

Az alábbi példában a teljes tábla frissítése CTAS-sá lett konvertálva, így a minimális naplózás lehetséges.

Ebben az esetben visszamenőlegesen hozzáadjuk az engedmény összegét a táblázatban szereplő eladásokhoz:

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
> Nagy táblák újbóli létrehozása előnyös lehet az SQL Analytics számítási feladatok kezelése funkciók használatával. További információ: [Resource classes for workload management](resource-classes-for-workload-management.md).
> 
> 

## <a name="optimizing-with-partition-switching"></a>Optimalizálás partícióváltással
Ha egy [táblázatpartíción](sql-data-warehouse-tables-partition.md)belül nagy léptékű módosításokkal szembesül, akkor a partícióváltási mintának van értelme. Ha az adatmódosítás jelentős, és több partícióra terjed ki, akkor a partíciókon keresztüli iteráció ugyanazt az eredményt éri el.

A partíciókapcsoló végrehajtásának lépései a következők:

1. Üres partíció létrehozása
2. A "frissítés" végrehajtása CTAS-ként
3. A meglévő adatok kikapcsolása a kimenő táblára
4. Váltás az új adatokban
5. Az adatok karbantartása

A váltáshoz a partíciók azonosításához azonban hozza létre a következő segítő eljárást.  

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

Ez az eljárás maximalizálja a kód újrafelhasználását, és kompaktabbá teszi a partícióváltáspéldáját.

A következő kód bemutatja a korábban említett lépéseket a teljes partíciókapcsolási rutin eléréséhez.

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

## <a name="minimize-logging-with-small-batches"></a>A naplózás kis tételekkel történő minimalizálása
Nagy adatmódosítási műveletek esetén célszerű lehet a műveletet adattömbökre vagy kötegekre osztani a munkaegység hatókörének hatóköre érdekében.

A következő kód egy működő példa. A kötegméretet triviális számra állították be a technika kiemeléséhez. A valóságban a köteg mérete jelentősen nagyobb lenne. 

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

## <a name="pause-and-scaling-guidance"></a>Szüneteltetési és méretezési útmutató
Az SQL Analytics lehetővé teszi az SQL-készlet [szüneteltetését, folytatását és méretezését](sql-data-warehouse-manage-compute-overview.md) igény szerint. Az SQL-készlet szüneteltetésekor vagy méretezésekor fontos megérteni, hogy a repülés közbeni tranzakciók azonnal megszűnnek; a nyitott tranzakciók visszagörgetését. Ha a számítási feladatok hosszú ideig futó és hiányos adatmódosítást adtak ki a szüneteltetési vagy méretezési művelet előtt, akkor ezt a munkát vissza kell vonni. Ez a visszavonás hatással lehet az SQL-készlet szüneteltetéséhez vagy méretezéséhez szükséges időre. 

> [!IMPORTANT]
> `UPDATE` Mindkettő, `DELETE` és teljesen naplózott műveletek, és így ezek a visszavonás/ismétlési műveletek jelentősen hosszabb időt vehet igénybe, mint egyenértékű minimálisan naplózott műveletek. 
> 
> 

A legjobb forgatókönyv az, hogy hagyja, hogy a repülési adatok módosítása tranzakciók befejezése előtt szüneteltetése vagy méretezése SQL-készlet. Ez a forgatókönyv azonban nem mindig praktikus. A hosszú visszaállítás kockázatának csökkentése érdekében vegye figyelembe az alábbi lehetőségek egyikét:

* Hosszú ideig futó műveletek újraírása [CTAS-sel](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)
* A művelet megszakítása darabokra; a sorok egy részhalmazán működik

## <a name="next-steps"></a>További lépések
További információ az elkülönítési szintekről és a tranzakciós korlátokról az [SQL Analytics tranzakciós](sql-data-warehouse-develop-transactions.md) szintjeiről.  Az egyéb gyakorlati tanácsok áttekintését az [SQL Data Warehouse ajánlott eljárások című témakörben találja.](sql-data-warehouse-best-practices.md)

