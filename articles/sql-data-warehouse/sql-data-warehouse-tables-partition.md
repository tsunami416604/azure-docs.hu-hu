---
title: Az Azure SQL Data Warehouse Táblák particionálása |} A Microsoft Docs
description: Javaslatok és a példák az Azure SQL Data Warehouse táblapartíciók.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 04e489e6b6841f1038830d0b160e88111be8d838
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301960"
---
# <a name="partitioning-tables-in-sql-data-warehouse"></a>Az SQL Data Warehouse Táblák particionálása
Javaslatok és a példák az Azure SQL Data Warehouse táblapartíciók.

## <a name="what-are-table-partitions"></a>Mik azok a táblapartíciók?
Táblapartíciók lehetővé teszi az adatok felosztása kisebb csoportjainak adatokat. A legtöbb esetben a dátum oszlop táblapartíciók jönnek létre. Particionálás esetében támogatott az SQL Data Warehouse minden táblatípusok; beleértve a fürtözött oszlopcentrikus, fürtözött index és halommemória. Összes terjesztési típusok, beleértve a kivonatoló vagy a ciklikus időszeletelés elosztott is támogatott particionálás.  

Particionálási adatok karbantartási és a lekérdezési teljesítmény előnyeit. E előnyöket csak az egyik vagy mindkét szolgáltatás hogyan adatok betöltése és e ugyanazt az oszlopot is használható mindkét célra óta a particionálást csak teheti egy oszlop függ.

### <a name="benefits-to-loads"></a>A terhelések előnyei
Az elsődleges particionálás az SQL Data Warehouse előnye a hatékonyság és az adatok betöltése a használatával a partíció törlése, váltás és egyesítés teljesítményének javítása érdekében. A legtöbb esetben adatok particionálása a dátum oszlop, amely szorosan kötve a sorrend, amelyben az adatok betöltése az adatbázisba. Egyik legnagyobb előnye az, partíciók használatával az adatok karbantartása, a tranzakciók naplózásának jogosítványokat. Egyszerűen beszúrása, frissítése vagy törlése az adatok lehetnek egy kis gondolkodási és erőfeszítéssel, a legegyszerűbb módszer a betöltési folyamat során a particionálás használata jelentősen javíthatja a teljesítményt.

Váltás a partíció használható gyors távolítsa el vagy cserélje le egy tábla egy szakasz.  Például egy értékesítési ténytábla, csak az adatokat az elmúlt 36 hónapos tartalmazhat. Minden hónap végén a a legrégebbi hónap, értékesítési adatok törlődik a táblából.  Sikerült törölni az adatok delete utasítások használatával adatokat törli a legrégebbi hónapban. Azonban nagy mennyiségű adat sor soronként delete utasítást törlése is túl sok időt vesz igénybe, valamint nagy-tranzakciók visszagörgetése hosszú ideig tarthat, ha valami probléma merül fel a kockázatot. A több optimális megközelítés az dobja el az adatok a legrégebbi partíciót. Ahol az egyes sorok törlése óráig is eltarthat, egy teljes partíció törlése eltarthat a másodperc.

### <a name="benefits-to-queries"></a>Arra, hogy a lekérdezések
A particionálás is használható a lekérdezési teljesítmény javítása érdekében. Egy lekérdezést, amely egy szűrőt érvényes particionált adatok korlátozhatja a vizsgálatot csak a feltételeknek megfelelő partícióra. Ezzel a módszerrel szűrés is elkerülése érdekében a teljes tábla beolvasásával, és csak az adatok kisebb részhalmazát vizsgálata. Fürtözött oszlopcentrikus indexek bevezetésével által nyújtott predikátum eltávolítási kevésbé hasznos, de bizonyos esetekben lehet egyik előnye a lekérdezéseket. Például ha az értékesítés ténytáblában 36 hónapos értékesítési dátum mező használatával van particionálva, akkor a szűrő lekérdezi az értékesítés időpontjában kihagyhatja a szűrő nem egyezik a partíciók keresi.

## <a name="sizing-partitions"></a>A partíciók méretezése
Particionálás a teljesítmény javítása bizonyos helyzetekben használható, miközben a tábla létrehozása **túl sok** partíciók összeállítása hátrányosan befolyásolhatja a teljesítményt bizonyos körülmények között.  Ezek a problémák különösen fürtözött oszlopcentrikus táblák. A particionálás segíthet, fontos, megtudhatja, hogy mikor érdemes használni, a particionálás és a létrehozandó partíciók száma. Nincs rögzített gyors szabály arra nézve, hogy hány partíciók túl sok, attól függ, az adatokat, és hány partíciók, egyidejűleg betöltése. Egy sikeres particionálási sémát általában több tíz, száz partíciók nem több ezer rendelkezik.

A partíciók létrehozásakor **fürtözött oszlopcentrikus** táblákat, fontos, hogy fontolja meg az egyes partíciók tartozik sorok számát. A optimális tömörítési és fürtözött oszlopcentrikus táblák teljesítményét legalább 1 millió sor / terjesztési és a partíció van szükség. Mielőtt partíciót hoz létre, az SQL Data Warehouse már osztja mindegyik tábla 60 elosztott adatbázisok. Bármilyen particionálási vesz fel egy táblába a disztribúciók a háttérben létrehozott mellett van. Ebben a példában használja, ha az értékesítési ténytábla található 36 havi partíciókat, tekintve, hogy az SQL Data Warehouse 60 elosztás van, majd az értékesítés ténytáblában kell tartalmaznia 60 millió sorral havonta vagy 2.1 egymilliárd sort minden hónapban fel van töltve. Ha egy tábla tartalmaz kevesebb, mint a sorok partíciónként ajánlott minimális száma, fontolja meg a partíciók kevesebb partíciónként sorok számának növelésére. További információkért lásd: a [indexelési](sql-data-warehouse-tables-index.md) című cikket, amely tartalmazza a fürtözött oszlopcentrikus indexekbe minőségét is felmérő lekérdezések.

## <a name="syntax-differences-from-sql-server"></a>Különbségek az SQL Serverről
Az SQL Data Warehouse, amely egyszerűbb, mint az SQL Server definiáljon partíciókat módszerét kínálja. Particionálási függvényeket és sémák nem használják az SQL Data Warehouse az SQL Server azok. Ehelyett mást nem kell tennie a particionált oszlopot és a határ pontokat azonosíthatja. Bár a particionálás szintaxisa kissé eltérhetnek az SQL Server, az alapfogalmak azonosak. Az SQL Server és SQL Data Warehouse támogatja egy partícióoszlop táblánként, amely előre partíció is lehet. A particionálás kapcsolatos további információkért lásd: [particionált táblák és indexek](/sql/relational-databases/partitions/partitioned-tables-and-indexes).

Az alábbi példában a [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) OrderDateKey oszlopában a FactInternetSales tábla particionálásához utasítást:

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

## <a name="migrating-partitioning-from-sql-server"></a>Migrálás SQL Serverről particionálás
Az SQL Server partíció definíciók egyszerűen az SQL Data Warehouse áttelepítése:

- Az SQL Server kiküszöbölése [partícióséma](/sql/t-sql/statements/create-partition-scheme-transact-sql).
- Adja hozzá a [partíciós függvényei](/sql/t-sql/statements/create-partition-function-transact-sql) a CREATE TABLE-definíciót.

Ha egy SQL Server-példányról egy particionált táblához telepít át, a következő SQL segíthet azonosítani a sorok száma, amelyek az egyes partíciók. Ne feledje, hogy ha particionálási részletességgel használja az SQL Data warehouse-ba, partíciónként sorok számának csökkenti a 60-szorosára.  

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

## <a name="workload-management"></a>Számítási feladatok kezelése
Egy utolsó szempont a tábla partíciós határozat figyelembe vennie, [számítási feladatok kezeléséhez](resource-classes-for-workload-management.md). Az SQL Data Warehouse a számítási feladatok kezeléséhez az elsősorban a memória és az egyidejűség kezelése. Az SQL Data Warehouse a lekérdezés-végrehajtás során minden egyes terjesztési számára lefoglalt maximális memória erőforrásosztályok szabályozza. Ideális esetben a partíciók mérete más tényezőktől, például a fürtözött oszloptár-indexek létrehozásához memóriaigényét figyelembe véve. Fürtözött oszlopcentrikus indexek benefit jelentősen, több memória kiosztása során. Ezért érdemes győződjön meg arról, hogy a partíció indexkészítés nem fogy ki a memória. A lekérdezés számára elérhető memória mennyiségének növelését érhető el az alapértelmezett szerepkörből, smallrc, egy largerc például a más szerepkörök között.

A lefoglalt memóriát információkat az erőforrás-vezérlő dinamikus felügyeleti nézetek lekérdezésével érhető el. A valóságban a végzett a memóriabeli ideiglenes kisebb, mint a következő lekérdezés eredményeit. Azonban ez a lekérdezés biztosít útmutató osztályozás, a partíciók az adatkezelési műveletekhez használható. Próbálja ki az extra nagy erőforrásosztály által biztosított memóriaengedélyt partíciószám méretezési elkerülése érdekében. A partíciók Ez az ábra túli növekszik, ha futtatja a rendelkezésre álló memória mennyisége, ami viszont kevésbé optimális tömörítés kockázatát.

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

## <a name="partition-switching"></a>Partíció váltása
Az SQL Data Warehouse felosztása, egyesítése és a Váltás partíció támogatja. Ezek közül minden függvény végrehajtása használatával a [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql) utasítást.

Váltás a partíciók két táblázat között, gondoskodnia kell arról, hogy a partíciók igazítása a megfelelő határokon belül, és hogy megegyeznek-e a tábladefiníciókat. Ellenőrzési korlátozások nem érhetők el egy tábla értékek érvényesítéséhez, a forrástábla tartalmaznia kell a azonos partícióhatárok, mint a céloldali tábla. Ha a partícióhatárok nem majd azonos, majd a partíció kapcsolójának sikertelen lesz, mivel a partíciós metaadatok nem lesznek szinkronizálva.

### <a name="how-to-split-a-partition-that-contains-data"></a>Felosztása a adatokat tartalmazó partíció
A leghatékonyabb módszer felosztása a adatokat már tartalmazó partíció egy `CTAS` utasítást. Ha a particionált tábla egy fürtözött oszlopcentrikus, majd a tábla partíciós üresnek kell lennie előtt lehet megosztani.

A következő példában létrehozunk egy particionált oszlopcentrikus táblába. Mindegyik partíció egy sor beszúrása azt:

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
> A statisztika objektum létrehozásával a tábla metaadatainak pontosabb. Statisztikai paraméter kihagyása esetén az SQL Data Warehouse alapértelmezett értékeket fogja használni. A statisztika részletes ismertetéséért tekintse át [statisztika](sql-data-warehouse-tables-statistics.md).
> 
> 

A következő lekérdezés segítségével keresi meg a sorok száma a `sys.partitions` katalógus megtekintése:

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

Felosztott parancs a következő hibaüzenetet kapja:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Msg 35346, 15. szintű, State 1 sor 44 SPLIT záradékának az ALTER PARTITION utasítás sikertelen volt, mert a partíció nem üres. Csak üres partíciók oszthatók az oszlopcentrikus index létezik a táblán. Fontolja meg az oszloptárindex letiltását előtt az ALTER PARTITION utasítás kiadása, majd az oszloptárindex újraépítését az ALTER PARTITION utasítás befejeződése után.

Használhatja azonban `CTAS` hozhat létre egy új táblát, amely tárolja az adatokat.

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

A partícióhatárok vannak igazítva, mivel a kapcsoló számára engedélyezett. Az üres partíció, amely ezt követően akkor is feloszthatja ez hagyja a forrástáblában.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Van hátra való megfelelés érdekében az adatokat a használatával új partícióhatárok `CTAS`, majd váltson az adatokat a fő táblába.

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

Miután befejezte az adatok áthelyezését, célszerű a céltábla a statisztikák frissítése. Frissítse a statisztikai adatokat a statisztikák pontosan tükrözi az adatok a megfelelő partíciók új terjesztési biztosítja.

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Tábla particionálása verziókövetés
A tábla-definíció elkerülése érdekében **Rozsdás** a forrásrendszerben vezérlő érdemes figyelembe venni a következő módon:

1. A tábla létrehozásához, mint egy particionált táblához, de nincs partíció értékekkel

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

1. `SPLIT` a tábla a telepítési folyamat részeként:

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

Ezzel a módszerrel a kódot verziókövetési rendszerben statikus marad, és a particionálási tartományhatár-értékek használata engedélyezett a dinamikus; az adatraktár-fejlődő idővel.

## <a name="next-steps"></a>További lépések
Táblák fejlesztésével kapcsolatos további információkért tekintse meg a cikkeket a [táblák áttekintésével](sql-data-warehouse-tables-overview.md).

