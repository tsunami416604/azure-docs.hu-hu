---
title: Az ideiglenes táblák az SQL Data Warehouse |} A Microsoft Docs
description: Alapvető útmutatás az ideiglenes táblák és kiemelések a munkamenet szintű ideiglenes táblák alapelveit.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/01/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 23a62e28700ad5fd733040c43ea0eec225fd286f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58793101"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Az SQL Data Warehouse az ideiglenes táblák
Ez a cikk alapvető útmutatást nyújt az ideiglenes táblák használatával, és kiemeli a munkamenet szintű ideiglenes táblák alapelveit. Ebben a cikkben szereplő információk segítségével segítségével modularize újrahasznosíthatóság és a könnyű karbantartás a kód a kódba.

## <a name="what-are-temporary-tables"></a>Mik azok az ideiglenes táblák?
Az ideiglenes táblák akkor hasznos, ha a data - feldolgozási különösen hol tárolja a köztes eredményeket a rendszer átmeneti átalakítása során. Az SQL Data Warehouse az ideiglenes táblák létezik, a munkamenet szintjén.  Csak azok a munkamenethez, amelyben létrejöttek, és a rendszer automatikusan elveti a munkamenet kijelentkezésekor látható.  Az ideiglenes táblák teljesítménybeli előnyt kínálnak, mert az eredményeket a távoli tárolás helyett a helyi készültek.

## <a name="create-a-temporary-table"></a>Hozzon létre egy ideiglenes táblát
Az ideiglenes táblák jönnek létre a tábla neve a illesztésével egy `#`.  Példa:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Az ideiglenes táblák is lehet létrehozni egy `CTAS` pontosan ugyanazt a megközelítést használ:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

> [!NOTE]
> `CTAS` egy hatékony parancsot, és előnye, hogy a használt tranzakciónaplók hatékony. 
> 
> 

## <a name="dropping-temporary-tables"></a>Ideiglenes táblák
Új munkamenet létrehozását követően nem ideiglenes táblák léteznie kell.  Azonban ha hívja meg az azonos tárolt eljárás, amely létrehoz egy ideiglenes ugyanazzal a névvel, annak érdekében, hogy a `CREATE TABLE` kimutatásai sikeres egy egyszerű előtti meglétének ellenőrzése a egy `DROP` is használható a következő példához hasonlóan:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Konzisztencia kódolásához, akkor célszerű ezt a mintát használni az táblákat, és az ideiglenes táblák.  Azt is, célszerű használni `DROP TABLE` ideiglenes táblák eltávolítja, ha befejezte a kódban velük.  A tárolt eljárás fejlesztési szokás tekintse meg a drop utasítást összecsomagolja annak érdekében, hogy ezek az objektumok törlődnek eljárás végén található.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Kód modularizing
Az ideiglenes táblák bárhol láthatja a felhasználói munkamenetek, mivel ez segít az alkalmazás kódjában modularize kihasználnák őket.  Például a következő tárolt eljárást az adatbázis összes statisztikák frissítését Statisztikanév DDL állít elő.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

Ezen a ponton az egyetlen művelet történt, amely létrehoz egy ideiglenes táblát, #stats_ddl DDL-utasítások a tárolt eljárás létrehozása.  Ez a tárolt eljárás #stats_ddl csökken, ha már létezik, győződjön meg arról, ha futtassa többször a munkameneten belül nem sikertelen.  Azonban mivel nincs `DROP TABLE` a tárolt eljárás végén található a tárolt eljárás befejeződésekor elhagyják a létrejött táblában, hogy a tárolt eljárás kívül is olvasható.  Az SQL Data Warehouse ellentétben más SQL Server-adatbázisok is lehet használni az ideiglenes tábla kívül az eljárást, amely létrehozta.  Az ideiglenes táblák az SQL Data Warehouse segítségével **bárhol** a munkamenetből. Ezzel a módszerrel több moduláris és felügyelhető kódot az alábbi példában látható módon:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Az ideiglenes tábla korlátozások
Az SQL Data Warehouse kivetett korlátozások néhány, az ideiglenes táblák végrehajtása során.  Jelenleg csak a munkamenet hatókörű ideiglenes táblák támogatottak.  Globális ideiglenes táblák nem támogatottak.  Emellett nézetek ideiglenes táblák nem hozható létre.  Az ideiglenes táblák csak a kivonatoló vagy ciklikus időszeletelés terjesztési hozható létre.  A replikált ideiglenes tábla terjesztési nem támogatott. 

## <a name="next-steps"></a>További lépések
Táblák fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [táblák áttekintésével](sql-data-warehouse-tables-overview.md).

