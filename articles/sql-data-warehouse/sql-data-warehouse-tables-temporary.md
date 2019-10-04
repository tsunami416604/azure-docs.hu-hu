---
title: Ideiglenes táblák a SQL Data Warehouseban | Microsoft Docs
description: Az ideiglenes táblák használatának alapvető útmutatója, és kiemeli a munkamenetek szintjének ideiglenes tábláinak alapelveit.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: e43e52e56ec7abbf5d8eb879defef54bd7d50658
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479829"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Ideiglenes táblák a SQL Data Warehouse
Ez a cikk alapvető útmutatást tartalmaz az ideiglenes táblák használatához, és kiemeli a munkamenetek szintjének ideiglenes tábláira vonatkozó alapelveket. A cikkben található információk segítségével modularize a kódot, és javíthatja a kód újrahasználhatóságát és egyszerű karbantartását.

## <a name="what-are-temporary-tables"></a>Mik azok az ideiglenes táblák?
Az ideiglenes táblák hasznosak az adatfeldolgozás során – különösen az átalakítás során, ahol a közbenső eredmények átmenetiek. SQL Data Warehouse az ideiglenes táblák a munkamenet szintjén vannak.  A rendszer csak azt a munkamenetet láthatja, amelyben létrehozták őket, és a munkamenet kilépésekor automatikusan el lesz dobva.  Az ideiglenes táblák teljesítménybeli előnyt kínálnak, mivel a rendszer a távoli tárterület helyett a helyi verzióra ír.

## <a name="create-a-temporary-table"></a>Ideiglenes tábla létrehozása
Az ideiglenes táblákat a táblanév előtaggal való `#`előállításával hozza létre a rendszer.  Példa:

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

Az ideiglenes táblák is létrehozhatók a `CTAS` használatával, pontosan ugyanazzal a módszerrel:

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
> `CTAS`a egy hatékony parancs, és az előnye, hogy hatékony a tranzakciós napló területének használatakor. 
> 
> 

## <a name="dropping-temporary-tables"></a>Ideiglenes táblák eldobása
Új munkamenet létrehozásakor nem létezhet ideiglenes tábla.  Ha azonban ugyanazt a tárolt eljárást hívja meg, amely egy ideiglenest hoz létre ugyanazzal a névvel, annak érdekében, hogy az `CREATE TABLE` utasítások sikeresek legyenek, a következő példához hasonlóan `DROP` használhatja az egyszerű, előzetes létezésű ellenőrzéseket:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

A kódolási konzisztencia esetében érdemes ezt a mintát használni a táblák és az ideiglenes táblák esetében is.  Azt is érdemes használni `DROP TABLE` , hogy az ideiglenes táblákat távolítsa el, ha végzett a kóddal.  A tárolt eljárások fejlesztése során gyakran előfordul, hogy az eljárások végén a legördülő parancsok együttesen jelennek meg az objektumok tisztításának biztosítása érdekében.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizing-kód
Mivel az ideiglenes táblák egy felhasználói munkamenetben bárhol megtekinthetők, ez a megoldás az alkalmazás kódjának modularize segít.  Például a következő tárolt eljárás DDL-et hoz létre az adatbázis összes statisztikájának a statisztikai név alapján történő frissítéséhez.

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

Ebben a szakaszban az egyetlen művelet történt egy olyan tárolt eljárás létrehozásakor, amely létrehoz egy ideiglenes táblát, #stats_ddl, és DDL-utasításokkal.  Ez a tárolt eljárás elveszíti a #stats_ddl, ha az már létezik, így biztosíthatja, hogy nem fog sikerülni, ha egynél többször fut egy munkameneten belül.  Mivel azonban a tárolt eljárás végén `DROP TABLE` nem szerepel a tárolt eljárás, a tárolt eljárás befejezése után elhagyja a létrehozott táblázatot, hogy a tárolt eljáráson kívül is olvasható legyen.  SQL Data Warehouse a többi SQL Server-adatbázistól eltérően az ideiglenes táblázatot is használhatja az azt létrehozó eljáráson kívül.  SQL Data Warehouse ideiglenes táblákat a munkameneten belül **bárhol** lehet használni. Ez további moduláris és felügyelhető kódokat eredményezhet, ahogy az alábbi példában is látható:

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

## <a name="temporary-table-limitations"></a>Ideiglenes táblák korlátozásai
A SQL Data Warehouse az ideiglenes táblák megvalósításának néhány korlátozását alkalmazza.  Jelenleg csak a munkamenet-hatókörű ideiglenes táblák támogatottak.  A globális ideiglenes táblák nem támogatottak.  Emellett a nézetek nem hozhatók létre ideiglenes táblákon.  Ideiglenes táblákat csak kivonattal vagy ciklikus multiplexelés eloszlással lehet létrehozni.  A replikált ideiglenes tábla eloszlása nem támogatott. 

## <a name="next-steps"></a>További lépések
A táblák fejlesztésével kapcsolatos további tudnivalókért tekintse meg a [táblázat áttekintését](sql-data-warehouse-tables-overview.md).

