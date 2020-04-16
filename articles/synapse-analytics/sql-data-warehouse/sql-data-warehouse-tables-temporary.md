---
title: Ideiglenes táblák
description: Alapvető útmutatás a synapse SQL-készlet ideiglenes tábláinak használatához, kiemelve a munkamenetszintű ideiglenes táblák alapelveit.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 56d8ab81fcf9200fec2cfb4a741724b8f79db820
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408030"
---
# <a name="temporary-tables-in-synapse-sql-pool"></a>Ideiglenes táblák a Synapse SQL-készletben
Ez a cikk alapvető útmutatást tartalmaz az ideiglenes táblák használatával, és kiemeli a munkamenetszintű ideiglenes táblák alapelveit. 

A cikkben található információk használata segíthet a kód modularizálásában, javítva az újrafelhasználhatóságot és a könnyű karbantartást.

## <a name="what-are-temporary-tables"></a>Mik azok az ideiglenes táblák?
Az ideiglenes táblák akkor hasznosak, ha adatokat dolgoz fel, különösen az átalakítás során, ahol a köztes eredmények átmenetiek. Az SQL-készletben ideiglenes táblák léteznek a munkamenet szintjén.  

Az ideiglenes táblák csak azon a munkameneten láthatók, amelyben létrehozták őket, és automatikusan eldobják őket, amikor a munkamenet kijelentkezik.  

Az ideiglenes táblák teljesítménybeli előnyöket biztosítanak, mivel eredményeik a távtároló helyett a helyi tárolóba vannak írva.

Az ideiglenes táblák akkor hasznosak, ha adatokat dolgoz fel, különösen az átalakítás során, ahol a köztes eredmények átmenetiek. Az SQL Analytics szolgáltatással ideiglenes táblák léteznek a munkamenet szintjén.  Csak az on-dikált munkamenet számára láthatók. Így a rendszer automatikusan eldobja őket, amikor a munkamenet kijelentkezik. 

## <a name="temporary-tables-in-sql-pool"></a>Ideiglenes táblák az SQL-készletben

Az SQL-készlet erőforrás ideiglenes táblák teljesítményelőnyt kínálnak, mert az eredmények írása a helyi, nem pedig a távtároló.

### <a name="create-a-temporary-table"></a>Ideiglenes tábla létrehozása

Az ideiglenes táblák úgy jönnek létre, hogy a táblanevét előrögzítik egy `#`.  Például:

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

Ideiglenes táblák is létrehozhatók `CTAS` pontosan ugyanazzal a módszerrel:

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
;
```

> [!NOTE]
> `CTAS`egy erős parancs, és a hozzáadott előnye, hogy hatékony annak használata tranzakciónapló helyet. 
> 
> 

## <a name="dropping-temporary-tables"></a>Ideiglenes táblák eldobása
Új munkamenet létrehozásakor nem létezhetnek ideiglenes táblák.  

Ha ugyanazt a tárolt eljárást hívja meg, amely ideiglenes, azonos nevű `CREATE TABLE` eljárást hoz létre, hogy biztosítsa a `DROP` kimutatások sikeresek legyenek, egy egyszerű, a a-val való létezés előtti ellenőrzés használható a következő példában:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

A konzisztencia kódolásához célszerű ezt a mintát használni a táblák hoz és az ideiglenes táblákhoz.  Érdemes az ideiglenes táblák eltávolításához is használni, `DROP TABLE` ha végzett velük a kódban.  

A tárolt eljárások fejlesztése, gyakori, hogy a csepp parancsok at kötegelt együtt az eljárás végén annak biztosítása érdekében, ezek az objektumok törlődnek.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizáló kód
Mivel az ideiglenes táblák bárhol láthatók a felhasználói munkamenetben, ez a képesség kihasználva segít az alkalmazáskód modularizálásában.  

A következő tárolt eljárás például ddl-t hoz létre az adatbázis összes statisztikájának statisztikai név szerint való frissítéséhez:

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

Ebben a szakaszban az egyetlen végrehajtott művelet egy tárolt eljárás létrehozása, amely egy ddl-utasításokkal rendelkező ideiglenes táblát hoz létre, #stats_ddl.  

Ez a tárolt eljárás elejt egy meglévő #stats_ddl, hogy biztosan ne sikertelenül fusson, ha egy munkameneten belül többször fut.  

Mivel azonban a `DROP TABLE` tárolt eljárás végén nincs, amikor a tárolt eljárás befejeződik, elhagyja a létrehozott táblát, hogy a tárolt eljáráson kívül is olvasható legyen.  

Az SQL-készletben más SQL Server-adatbázisoktól eltérően az ideiglenes tábla is használható a létrehozott eljáráson kívül.  AZ SQL-készlet ideiglenes táblái **a munkameneten** belül bárhol használhatók. Ez a funkció a következő példához képest modulárisabb és kezelhetőbb kódhoz vezethet:

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

## <a name="temporary-table-limitations"></a>Ideiglenes táblakorlátozások
Az SQL-készlet néhány korlátozást ír elő az ideiglenes táblák megvalósításakor.  Jelenleg csak a munkamenet hatókörrel tartozó ideiglenes táblák támogatottak.  A globális ideiglenes táblák nem támogatottak.  

Emellett a nézetek nem hozhatók létre ideiglenes táblákon.  Ideiglenes táblák csak kivonatoló vagy ciklikus multiplexeléssel hozhatók létre.  A replikált ideiglenes táblaterjesztés nem támogatott. 

## <a name="next-steps"></a>További lépések

A táblák fejlesztéséről a [Táblák tervezése az SQL Analytics-erőforrások cikkében](sql-data-warehouse-tables-overview.md) olvashat bővebben.

