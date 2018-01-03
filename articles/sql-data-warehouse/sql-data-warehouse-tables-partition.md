---
title: "Az SQL Data Warehouse Táblák particionálása |} Microsoft Docs"
description: "Első lépések az Azure SQL Data Warehouse táblaparticionálást."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 6cef870c-114f-470c-af10-02300c58885d
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: a28cb1f8a2e48332b344566620dc49b29d9d3c99
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2017
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>Az SQL Data Warehouse Táblák particionálása
> [!div class="op_single_selector"]
> * [– Áttekintés][Overview]
> * [Adattípusok][Data Types]
> * [Terjesztése][Distribute]
> * [Index][Index]
> * [Partíció][Partition]
> * [Statisztika][Statistics]
> * [Ideiglenes][Temporary]
> 
> 

Particionálás támogatott az SQL Data Warehouse tábla összes olyan; beleértve a fürtözött oszlopcentrikus, a fürtözött index és a halommemória.  Minden terjesztési típuson, beleértve a kivonatoló vagy a ciklikus multiplexelés elosztott a particionálás is támogatott.  Particionálás lehetővé teszi, hogy az adatok felosztani a particionálás kisebb csoportokat az adatok és a legtöbb esetben történik, a dátum oszlop.

## <a name="benefits-of-partitioning"></a>Particionálás előnyei
Particionálás adatok karbantartása és lekérdezéseivel kapcsolatos teljesítményt is kihasználhatja.  E előnyöket mindegyikét vagy csak egy függ, hogyan történik az adatok betöltése, és hogy mindkét célokra használhatók ugyanarra az oszlopra óta particionálás csak akkor lehet elvégezni egy oszlop.

### <a name="benefits-to-loads"></a>Terhelések előnyei
Az elsődleges az SQL Data Warehouse particionálás előnye a hatékonyság és az adatok betöltése a partíció törlése használatával, váltás és egyesítése teljesítményének javítása érdekében.  A legtöbb esetben adatok particionálása szorosan kapcsolódik a sorrendet, amelyben az adatok betöltése az adatbázisba dátum oszlopban.  Egyik legnagyobb előnye az adatok karbantartása partíciókra használatával, a tranzakció naplózási elkerülését.  Egyszerűen beszúrni, frissítése vagy törlése az adatok lehetnek egy kis gondolat és annak érdekében, a legegyszerűbb módszer használatával a betöltési folyamat során particionálás jelentősen javíthatja a teljesítményt.

Váltás a partíció segítségével gyorsan távolítsa el vagy cserélje le a szakasz egy tábla.  Azt jelzi, például értékesítési ténytábla tartalmazhat-e az elmúlt 36 hónap csak az adatokat.  Minden hónap végén a legrégebbi hónap értékesítési adatokat töröltek, amelyek a tábla.  Ezek az adatok delete utasítások segítségével az adatok törléséhez a legrégebbi hónaphoz sikerült törölni.  Azonban nagy mennyiségű adat--soronként egy delete utasítás törlése is túl sok időt vehet igénybe, valamint a veszéllyel nagy tranzakciók visszagörgetése hosszú ideig tarthat, ha valamilyen hiba.  A több optimális megoldás, a legrégebbi partíció adatainak elvetése érdekében.  Ha az egyes sorok törléséhez órába is telhet, egy teljes partíció törlése sikerült másodpercre.

### <a name="benefits-to-queries"></a>Lekérdezések előnyei
Particionálás is segítségével javíthatja a lekérdezések teljesítményét.  Egy lekérdezést, amely a szűrő alkalmazása particionált adatokra korlátozhatja a vizsgálatot csak a megfelelő partíciókat. Ez a módszer szűrési teljes táblázatbeolvasás elkerülése, és csak a kisebb részhalmazát adatokat beolvasni. Fürtözött oszlopcentrikus indexek bevezetésével a predikátum eltávolítási teljesítménybeli előnyökben kevesebb előnyös, de egyes esetekben lehet lekérdezések előnyt.  Például ha az értékesítési ténytábla az értékesítési dátum mező 36 hónapokra particionálva van, akkor ez a szűrő lekérdezi az értékesítés időpontjában kihagyhatja megkeresése, amelyek nem egyeznek a szűrő partíciókat.

## <a name="partition-sizing-guidance"></a>Partíció olvasható méretezési útmutató
Particionálás teljesítmény javításához bizonyos esetekben használható, amíg a tábla létrehozása **túl sok** partíciók hátrányosan befolyásolhatja a teljesítményt bizonyos körülmények között.  A problémák különösen akkor igaz, a fürtözött oszloptárindexű táblákat.  Particionálás hasznosak lehetnek, fontos tudni, mikor érdemes használni a particionálás és létrehozásához a partíciók száma.  Nincs rögzített gyors szabály, hogy hány partíció található, a túl sok, attól függ, az adatokat, és hogy hány partíciót, amelyek betöltését egyidejűleg.  Egy sikeres particionálási sémát általában több tíz, száz partíciók, akár több ezer nem rendelkezik.

A partíciók létrehozásakor **fürtözött oszlopcentrikus** táblák, fontos figyelembe kell venni a sorok mindegyik partíció tartozik.  Az optimális tömörítés és a fürtözött oszloptárindexű táblákat teljesítményétől legalább 1 millió sort foglalnak terjesztési és partíciónként szükséges.  Mielőtt partíciók jönnek létre, az SQL Data Warehouse már felosztja minden tábla 60 elosztott adatbázisok.  A particionálás hozzá van a háttérben létrehozott terjesztéseket mellett.  Ebben a példában használata, ha az értékesítési ténytábla található 36 havi partíciók, és mivel, hogy az SQL Data Warehouse 60 azokat a terjesztéseket, majd az értékesítési ténytábla kell tartalmaznia 60 millió sort foglalnak havonta vagy 2.1 egymilliárd sort minden hónap fel van töltve.  Ha a tábla tartalmaz jelentősen kevesebb, mint a sorok partíciónként ajánlott minimális száma, érdemes lehet kevesebb partíciók partíciónként sorok számának növelésére.  Lásd még: a [indexelő] [ Index] cikket, amely tartalmazza az SQL Data Warehouse minőségének fürt oszlopcentrikus indexek futó lekérdezések.

## <a name="syntax-difference-from-sql-server"></a>Az SQL Server szintaktikai különbség
SQL Data Warehouse bevezeti az definiáljon partíciókat, amely kis mértékben eltér az SQL Server egyszerűsített módot.  Particionálási függvény és sémák nem használják az SQL Data Warehouse szerint az SQL Server.  Ehelyett a teendő szüksége a particionált oszlop és a határ pontok azonosítására.  Bár a particionálás szintaxisát némileg eltérő SQL Server, az alapvető fogalmakat azonosak.  SQL Server és az SQL Data Warehouse támogatja táblánként, amely címkiosztási partíció lehet egy partícióoszlop.  Particionálás kapcsolatos további információkért lásd: [particionált táblák és -indexek][Partitioned Tables and Indexes].

A következő példa egy SQL Data warehouse particionálva [CREATE TABLE] [ CREATE TABLE] utasítás, particionálja a FactInternetSales táblának OrderDateKey oszlopon:

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

## <a name="migrating-partitioning-from-sql-server"></a>Az SQL Server particionálás áttelepítése
Egyszerűen csak az SQL Data Warehouse áttelepítése az SQL Server partíció definíciók:

* Az SQL Server kiküszöbölése [partícióséma][partition scheme].
* Adja hozzá a [partíciós függvényei] [ partition function] a CREATE TABLE-definíciót.

Ha egy SQL Server-példányt telepít egy particionált táblához, a következő SQL segítségével mérje fel, a sorok számát, amely minden egyes partícióra.  Ne feledje, hogy ha olyan particionálási részletességgel használatban van az SQL Data Warehouse, partíciónként sorok számának csökkenti egy tényezővel 60.  

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

## <a name="workload-management"></a>Terheléskezelés
A tábla partíciós döntés számításba a egy végső építőelemre szempont, hogy [munkaterhelés felügyeleti][workload management].  Munkaterhelés-kezelés az SQL Data Warehouse az elsősorban a memória és a párhuzamosság management.  A lekérdezés végrehajtása során minden egyes terjesztési számára lefoglalt maximális memória az SQL Data Warehouse erőforrás osztályok szabályozza.  Ideális esetben a partíciók mérete más tényezőktől, például a fürtözött oszlopcentrikus indexek felépítése memóriaigényét figyelembe véve.  Nagy mértékben fürtözött oszlopcentrikus indexek juttatásra, amikor azok több memóriát foglal le.  Ezért érdemes győződjön meg arról, hogy a partíció index újraépítése nem függeszteni memória. A lekérdezés számára elérhető memória mennyiségének növelését naplókról smallrc, az alapértelmezett szerepkör egyik szerepkör largerc például lehet elérni.

A kiosztását) eloszlása feladatonként (memória áll rendelkezésre információ az erőforrás-vezérlő dinamikus felügyeleti nézetekkel lekérdezésével. A valóságban a a memóriaengedély nem éri el a következő ábra. Azonban ez biztosít, amelyekkel a partíciók felügyeleti műveletek osztályozás útmutatás.  Lehetőleg kerülje a partíciók túl az extra nagy erőforrás osztály memóriaengedély méretezése. Ha ez a szám nagyobb legyen a partíciók futtatja a memóriaterhelése, ami viszont kevésbé optimális tömörítési kockázatát.

```sql
SELECT  rp.[name]                                AS [pool_name]
,       rp.[max_memory_kb]                        AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                    AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576                AS [mex_memory_gb]
,       rp.[max_memory_percent]                    AS [max_memory_percent]
,       wg.[name]                                AS [group_name]
,       wg.[importance]                            AS [group_importance]
,       wg.[request_max_memory_grant_percent]    AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups    wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools    rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Váltás a partíció
Az SQL Data Warehouse a felosztás egyesítése és váltás partíció támogatja. Ezek az excuted használatával a [az ALTER TABLE] [ ALTER TABLE] utasítást.

Váltás a partíciók két tábla között gondoskodnia kell arról, hogy a partíciók igazodnak a megfelelő határokon belül, és, hogy megfelel-e a definíciói. Nincsenek elérhető értékek a táblázat kényszerítéséhez ellenőrző korlátozásokat a forrástábla a azonos partícióhatárok, mint a céltábla kell tartalmaznia. Ha nem ez a helyzet, majd a partíció kapcsolójának sikertelen lesz, mivel a partíciós metaadatok nem szinkronizálja.

### <a name="how-to-split-a-partition-that-contains-data"></a>Hogyan leíró adatokat tartalmazza.
A leghatékonyabb módszer leválasztására már tartalmazza az adatokat egy `CTAS` utasítást. Ha a particionált tábla fürtözött oszlopcentrikus majd a tábla partíciós üresnek kell lennie ahhoz lehessen.

Alább egy sor minden partíció tartalmazó minta particionált oszlopcentrikus tábla lesz:

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


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [!NOTE]
> A statisztika-objektum létrehozásával azt győződjön meg arról, hogy a tábla metaadatainak pontosabb. Jelenleg nincs megadva statisztikák létrehozása, ha az SQL Data Warehouse alapértelmezett értékeket fogja használni. A statisztika részletes tekintse át [statisztika][statistics].
> 
> 

Azt is majd kereshet a sor számának használatával a `sys.partitions` katalógus megtekintése:

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

Próbálja meg ossza fel ebben a táblázatban, ha azt egy hiba jelenik meg:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Üzenet 35346, szint 15 állapot 1, az ALTER PARTITION utasítás sor 44 SPLIT záradékának sikertelen volt, mert a partíció nem üres.  Csak üres partíciók oszthatók fel oszlopcentrikus indexet a tábla létezik. Fontolja meg az oszloptárindex letiltását előtt az ALTER PARTITION utasítás kiadása, majd az oszloptárindex újraépítését az ALTER PARTITION utasítás befejeződése után.

Azonban használhatjuk `CTAS` ahhoz, hogy az adatok új tábla létrehozása.

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

A partícióhatárok vannak rendezve, mivel a kapcsoló megengedett. Így marad, hogy a forrástábla egy üres partícióból, azt később fel.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Ehhez marad csak akkor igazíthatók az adatokat az új partíció határokat a `CTAS` és az adatok ismét a fő tábla kapcsoló

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

Az adatok mozgása befejezése után célszerű a céltábla annak érdekében, hogy tükrözik az adatok a megfelelő partícióikba új terjesztési statisztika frissítése:

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>A verziókövetési rendszerrel particionálás tábla
A tábla-definíció elkerülése érdekében **Rozsdás** a vezérlő forrásrendszerben érdemes figyelembe venni a következő módon:

1. Hozzon létre a tábla particionált tábla megegyezik, azonban nincsenek partíció értékek

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
                    ()
                )
)
;
```

1. `SPLIT`a tábla a telepítési folyamat részeként:

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

Ezt a módszert a kódot a verziókövetési statikus marad, és a particionálási tartományhatár-értékek használata engedélyezett a dinamikus; fejlesztik, az adatraktár adott idő alatt.

## <a name="next-steps"></a>További lépések
További tudnivalókért tekintse meg a cikkek [tábla áttekintése][Overview], [tábla adattípusok][Data Types], [terjesztése egy tábla] [ Distribute], [Tábla indexelő][Index], [fenntartása a tábla statisztikai adatainak] [ Statistics] és [Az ideiglenes táblák][Temporary].  Gyakorlati tanácsok kapcsolatban bővebben lásd: [SQL Data Warehouse gyakorlati tanácsok][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[workload management]: ./sql-data-warehouse-develop-concurrency.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Partitioned Tables and Indexes]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[partition function]: https://msdn.microsoft.com/library/ms187802.aspx
[partition scheme]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
