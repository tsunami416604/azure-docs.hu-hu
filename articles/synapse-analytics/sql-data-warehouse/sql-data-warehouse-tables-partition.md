---
title: Particionáló táblák
description: Javaslatok és példák a synapse SQL-készletben lévő táblapartíciók használatára
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/18/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 368276f75128c80b8df326a26acf26c841e9f68a
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742690"
---
# <a name="partitioning-tables-in-synapse-sql-pool"></a>Particionáló táblák a Synapse SQL-készletben

Javaslatok és példák a synapse SQL-készletben lévő táblapartíciók használatára.

## <a name="what-are-table-partitions"></a>Mik azok a táblapartíciók?

A táblapartíciók lehetővé teszik az adatok kisebb adatcsoportokra osztását. A legtöbb esetben a táblapartíciók egy dátumoszlopon jönnek létre. A particionálás minden synapszi SQL-készlettábla-típusesetében támogatott; beleértve a fürtözött oszlopcentrikust, a fürtözött indexet és a halomba. Particionálás is támogatott minden terjesztési típusok, beleértve a kivonatoló vagy ciklikus multiplexelt elosztott.  

Particionálás előnyös lehet az adatok karbantartása és a lekérdezési teljesítmény. Függetlenül attól, hogy mindkettő-e vagy csak egy számára előnyös, attól függ, hogy az adatok betöltése, és hogy ugyanazt az oszlopot mindkét célra fel lehet-e használni, mivel a particionálás csak egy oszlopban végezhető el.

### <a name="benefits-to-loads"></a>Előnyök a rakományok számára

A synapsze-i SQL-készlet particionálásának elsődleges előnye az adatok betöltési hatékonyságának és teljesítményének javítása a partíciók törlésével, kapcsolásával és egyesítésével. A legtöbb esetben az adatok particionálása egy dátumoszlopon történik, amely szorosan kapcsolódik az adatok adatbázisba való betöltésének sorrendjéhez. Az egyik legnagyobb előnye a partíciók használatával az adatok karbantartása, hogy a tranzakció naplózáselkerülése. Míg az adatok egyszerű beszúrása, frissítése vagy törlése lehet a legegyszerűbb megközelítés, egy kis gondolattal és erőfeszítéssel, a particionálás használata a betöltési folyamat során jelentősen javíthatja a teljesítményt.

A partícióváltással gyorsan eltávolíthatja vagy kicserélheti a táblázat egy részét.  Előfordulhat például, hogy egy értékesítési ténytábla csak az elmúlt 36 hónap adatait tartalmazza. Minden hónap végén az értékesítési adatok legrégebbi hónapja törlődik a táblából.  Ezek az adatok törlési utasítással törölhetők a legrégebbi hónap adatainak törléséhez. Azonban a nagy mennyiségű adat soronkénti törlése egy törlési utasítással túl sok időt vehet igénybe, valamint nagy tranzakciók kockázatát, amelyek visszaállítása hosszú időt vesz igénybe, ha valami elromlik. Egy optimálisabb megközelítés az adatok legrégebbi partíciójának eldobása. Ahol az egyes sorok törlése órákba telhet, egy teljes partíció törlése másodperceket is igénybe vehet.

### <a name="benefits-to-queries"></a>Előnyök a lekérdezésekhez

Particionálás is használható a lekérdezésteljesítmény javítása. A particionált adatokra szűrőt alkalmazó lekérdezés csak a feljogosító partíciókra korlátozhatja a vizsgálat átkot. Ez a szűrési módszer elkerülheti a teljes táblabeéset, és csak az adatok kisebb részét tudja beszkad. A fürtözött oszlopcentrikus indexek bevezetésével az alaprendszeri eltávolítási teljesítmény előnyei kevésbé előnyösek, de bizonyos esetekben a lekérdezések előnyösek lehetnek. Ha például az értékesítési ténytábla 36 hónapra van felosztva az eladási dátum mező használatával, akkor az értékesítési dátumra szűrő lekérdezések kihagyhatják a keresést olyan partíciókon, amelyek nem felelnek meg a szűrőnek.

## <a name="sizing-partitions"></a>Partíciók méretezése

Particionálás idãgó teljesítmény javítása bizonyos forgatókönyvek, egy tábla **létrehozása túl sok** partíciót ronthatja a teljesítményt bizonyos körülmények között.  Ezek az aggodalmak különösen igaz a fürtözött oszlopcentrikus táblák. A particionálás, hogy hasznos legyen, fontos megérteni, hogy mikor kell használni particionálás és a létrehozott partíciók száma. Nincs nehéz gyors szabály, hogy hány partíció túl sok, ez attól függ, hogy az adatok és hány partíciót betölt egyszerre. A sikeres particionálási séma általában több tíz-száz partíciót, nem több ezer.

Partíciók létrehozásakor a **fürtözött oszlopcentrikus** táblák, fontos figyelembe venni, hogy hány sor tartozik az egyes partíciókat. A fürtözött oszlopcentrikus táblák optimális tömörítéséhez és teljesítményéhez legalább 1 millió sor szükséges disztribúciónként és partíciónként. A partíciók létrehozása előtt a Synapse SQL-készlet már 60 elosztott adatbázisra osztja az egyes táblát. A táblához hozzáadott particionálás a színfalak mögött létrehozott felosztásokon kívül történik. Ebben a példában, ha az értékesítési ténytábla 36 havi partíciót tartalmazott, és mivel a Synapse SQL-készlet 60 disztribúcióval rendelkezik, majd az értékesítési ténytábla havi 60 millió sort, vagy 2,1 milliárd sort tartalmaz, ha minden hónap ki van töltve. Ha egy tábla partíciónként az ajánlott minimális számú sornál kevesebb sort tartalmaz, fontolja meg kevesebb partíció használatát a partíciónkénti sorok számának növelése érdekében. További információt az [Indexelés](sql-data-warehouse-tables-index.md) című cikkben talál, amely a fürt oszlopcentrikus indexeinek minőségét felmassza.

## <a name="syntax-differences-from-sql-server"></a>Szintaktikai különbségek az SQL Server kiszolgálóval szemben

A Szinapszis SQL-készlet bevezeti az SQL Servernél egyszerűbb partíciók definiálásának módját. A particionálási függvények és sémák nem használatosak a Szinapszis SQL-készletében, mivel az SQL Server ben vannak. Ehelyett mindössze annyit kell tennie, hogy azonosítja a particionált oszlopot és a határpontokat. Bár a particionálás szintaxisa némileg eltérhet az SQL Servertől, az alapfogalmak megegyeznek. Az SQL Server és a Synapse SQL-készlet táblaként egy partícióoszlopot támogat, amely tartományban lévő partíció. A particionálásról a [Particionált táblák és indexek témakörben olvashat bővebben.](/sql/relational-databases/partitions/partitioned-tables-and-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

A következő példa a [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) utasítást használja az OrderDateKey oszlop FactInternetSales táblájának particionálásához:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Particionálás áttelepítése az SQL Server kiszolgálóról

SQL Server partíciódefiníciók áttelepítése a Synapse SQL-készletbe egyszerűen:

- Szüntesse meg az SQL Server [partíciósémát](/sql/t-sql/statements/create-partition-scheme-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).
- Adja hozzá a [partíciófüggvény-definíciót](/sql/t-sql/statements/create-partition-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) a CREATE TABLE függvényhez.

Ha egy particionált táblát egy SQL Server-példányból telepít át, a következő SQL segíthet kitalálni az egyes partíciók sorainak számát. Ne feledje, hogy ha ugyanazt a particionálási granularitást használja a synapse SQL-készlet, a sorok száma partíciónként 60-kal csökken.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="partition-switching"></a>Partícióváltás

A Synapse SQL-készlet támogatja a partíciók felosztását, egyesítését és váltását. Ezen függvények mindegyike az [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) utasításhasználatával történik.

A partíciók két tábla közötti váltásához gondoskodnia kell arról, hogy a partíciók a saját határaikhoz igazodjanak, és hogy a tábladefiníciók egyeznek.To switch partitions between two tables, you must ensure that the partitions align on their their boundaries and that the table definitions match. Mivel az ellenőrzési megkötések nem érhetők el a tábla értéktartományának kényszerítéséhez, a forrástáblának ugyanazokat a partícióhatárokat kell tartalmaznia, mint a céltáblának. Ha a partíció határai nem azonosak, akkor a partíciókapcsoló sikertelen lesz, mivel a partíció metaadatai nem lesznek szinkronizálva.

### <a name="how-to-split-a-partition-that-contains-data"></a>Adatokat tartalmazó partíció felosztása

A már adatokat tartalmazó partíciófelosztás leghatékonyabb módja `CTAS` egy utasítás használata. Ha a particionált tábla egy fürtözött oszlopcentrikus, akkor a táblapartíciónak üresnek kell lennie, mielőtt felosztható lenne.

A következő példa létrehoz egy particionált oszlopcentrikus táblát. Minden partícióba egy sort illeszt be:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);
```

A következő lekérdezés a katalógusnézet `sys.partitions` használatával keresi meg a sorok számát:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

A következő osztott parancs hibaüzenetet kap:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, Level 15, State 1, Line 44 SPLIT záradék a ALTER PARTITION utasítás nem sikerült, mert a partíció nem üres. Csak üres partíciókat lehet felosztani, ha egy oszlopcentrikus index létezik a táblában. Fontolja meg az oszlopcentrikus index letiltását az ALTER PARTITION utasítás kiadása előtt, majd az oszlopcentrikus index újraépítése az ALTER PARTITION befejezése után.

Az adatok `CTAS` tárolására azonban új táblát hozhat létre.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

A partícióhatárok igazításakor egy kapcsoló is megengedett. Így a forrástábla üres partícióval rendelkezik, amelyet később feloszthat.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Már csak annyi van hátra, hogy az adatokat az új partícióhatárokhoz igazítsa a használatával, `CTAS`majd visszakapcsolja az adatokat a főtáblára.

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Miután befejezte az adatok mozgását, célszerű frissíteni a céltáblában lévő statisztikákat. A statisztikák frissítése biztosítja, hogy a statisztikák pontosan tükrözzék az adatok új eloszlását a megfelelő partíciókon.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="load-new-data-into-partitions-that-contain-data-in-one-step"></a>Új adatok betöltése olyan partíciókba, amelyek egy lépésben tartalmaznak adatokat

Adatok betöltése partíciókra partícióváltás sal egy kényelmes módja szakaszban új adatokat egy táblában, amely nem látható a felhasználók számára a kapcsolót az új adatokat.  Ez kihívást jelenthet a forgalmas rendszerek kezelésére a zárolási versengés kapcsolódó partícióváltás.  A partíción `ALTER TABLE` lévő meglévő adatok törléséhez az adatok cseréjéhez szükséges.  Ezután `ALTER TABLE` egy másik kellett váltani az új adatokat.  A Synapse SQL-készletben a `TRUNCATE_TARGET` `ALTER TABLE` parancs támogatja a beállítást.  A `TRUNCATE_TARGET` `ALTER TABLE` parancs felülírja a partíciómeglévő adatokat az új adatokat.  Az alábbi példa `CTAS` egy új tábla létrehozását használja a meglévő adatokkal, új adatokat szúr be, majd az összes adatot visszakapcsolja a céltáblába, felülírva a meglévő adatokat.

```sql
CREATE TABLE [dbo].[FactInternetSales_NewSales]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

INSERT INTO dbo.FactInternetSales_NewSales
VALUES (1,20000101,2,2,2,2,2,2);

ALTER TABLE dbo.FactInternetSales_NewSales SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2 WITH (TRUNCATE_TARGET = ON);  
```

### <a name="table-partitioning-source-control"></a>Táblaparticionálási forrásvezérlő

Annak elkerülése érdekében, hogy a tábladefiníció **rozsdásodjon** a forrásvezérlő rendszerben, érdemes lehet a következő megközelítést alkalmazni:

1. A tábla létrehozása particionált táblaként, partícióértékek nélkül

    ```sql
    CREATE TABLE [dbo].[FactInternetSales]
    (
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
    )
    WITH
    (   CLUSTERED COLUMNSTORE INDEX
    ,   DISTRIBUTION = HASH([ProductKey])
    ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES () )
    )
    ;
    ```

1. `SPLIT`a táblázat a telepítési folyamat részeként:

    ```sql
     -- Create a table containing the partition boundaries

    CREATE TABLE #partitions
    WITH
    (
        LOCATION = USER_DB
    ,   DISTRIBUTION = HASH(ptn_no)
    )
    AS
    SELECT  ptn_no
    ,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
    FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
    ) a
    ;

     -- Iterate over the partition boundaries and split the table

    DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
    ,       @i INT = 1                                 --iterator for while loop
    ,       @q NVARCHAR(4000)                          --query
    ,       @p NVARCHAR(20)     = N''                  --partition_number
    ,       @s NVARCHAR(128)    = N'dbo'               --schema
    ,       @t NVARCHAR(128)    = N'FactInternetSales' --table
    ;

    WHILE @i <= @c
    BEGIN
        SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
        SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

        -- PRINT @q;
        EXECUTE sp_executesql @q;
        SET @i+=1;
    END

     -- Code clean-up

    DROP TABLE #partitions;
    ```

Ezzel a megközelítéssel a kód a forrásvezérlőben statikus marad, és a particionálási határértékek dinamikusak lehetnek; az adatbázissal együtt az idő múlásával.

## <a name="next-steps"></a>További lépések

A táblázatok fejlesztéséről a Táblázat áttekintése című témakörben [olvashat bővebben.](sql-data-warehouse-tables-overview.md)
