---
title: "Az SQL Data Warehouse táblák statisztikák kezelése |} Microsoft Docs"
description: "Első lépések az Azure SQL Data Warehouse táblákon statisztika."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: faa1034d-314c-4f9d-af81-f5a9aedf33e4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 11/06/2017
ms.author: barbkess
ms.openlocfilehash: 5e7fd3c8790bb9a1a7ae8662f9a7047ae54892d2
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>Az SQL Data Warehouse táblák statisztikák kezelése
> [!div class="op_single_selector"]
> * [– Áttekintés][Overview]
> * [Adattípusok][Data Types]
> * [Terjesztése][Distribute]
> * [Index][Index]
> * [Partition][Partition]
> * [Statistics][Statistics]
> * [Ideiglenes][Temporary]
> 
> 

Minél több ismeri az Azure SQL Data Warehouse az adatokat, minél gyorsabban hajtható végre rajta lekérdezések. Statisztika gyűjtése az adatokról, és majd betöltése az SQL Data Warehouse az egyik legfontosabb, amit a lekérdezések optimalizálását is van. Ez azért, mert az SQL Data Warehouse lekérdezésoptimalizáló költség-alapú optimalizáló. Összehasonlítja a különböző lekérdezésterveket költségét, és majd úgy dönt, hogy a költséghatékonyság, amely a legtöbb esetben a tervet, amely végrehajtja a leggyorsabb a tervet. Például ha a optimalizáló becslése, hogy a dátum jelenleg korlátozza a lekérdezés egy sort ad vissza, azt eldönthetik, másik csomagot, mint ha azt a választott dátum becslése, visszatér 1 millió sort foglalnak.

A folyamat létrehozásának, és frissítse a statisztikai adatokat jelenleg egy kézi művelet, de egyszerű tegye.  Hamarosan lesz automatikusan létrehozásához, és egyetlen oszlopok és indexek statisztikai adatainak frissítése.  Az alábbi információk segítségével nagy mértékben automatizálható a statisztikák felügyeleti az adatokon. 

## <a name="getting-started-with-statistics"></a>Ismerkedés a statisztikák
Minden egyes oszlophoz a mintában szereplő statisztikák létrehozása egyszerű módja a kezdéshez. Elévült statisztikát optimálisnál lekérdezési teljesítmény vezethet. Azonban frissítse a statisztikai adatokat az összes oszlopot, az adatok növekedésével is memóriát használ. 

Különböző alkalmazási helyzetek kapcsolatos ajánlások a következők:
| **Scenario** | Ajánlás |
|:--- |:--- |
| **Első lépések** | Az összes oszlop frissítse az SQL Data Warehouse áttelepítése után |
| **Legfontosabb oszlop vonatkozó statisztikák** | Kivonatfelosztási kulcs |
| **Második legfontosabb oszlopában statisztikák** | Partíciókulcs |
| **Más fontos oszlopok vonatkozó statisztikák** | Dátum, gyakori csatlakozik, a GROUP BY, HAVING, és ahol |
| **Stats frissítési gyakoriságának**  | Konzervatív: naponta <br></br> Betöltésekor, illetve az adatok átalakítása után |
| **Mintavételezés** |  Kevesebb mint 1 egymilliárd sort, használja az alapértelmezett mintavételi (20 %) <br></br> Több mint 1 milliárd sorból 2 %-os-köréről statisztika jó |

## <a name="updating-statistics"></a>Frissítse a statisztikai adatokat

Egy ajánlott úgy nem frissíthető statisztika dátumoszlopának dátumtulajdonságai minden nap új dátumok hozzáadása. Minden alkalommal új sorok be vannak töltve az adatraktárba, új betöltése vagy tranzakció kerülnek. Ezek az adatok terjesztési módosítsa, majd ellenőrizze a statisztika elavult. Ezzel ellentétben egy felhasználói tábla ország oszlop statisztikai előfordulhat, hogy soha nem kell frissíteni kell, értékek terjesztési általában nem módosíthatók. Feltéve, hogy a terjesztés az ügyfelek közötti állandó, új sorok hozzáadásakor a tábla változat nem fog módosítása az adatok terjesztési. Azonban ha az adatraktár csak tartalmaz egy országon, és az adatok egy új országból kapcsolná, az adatok tárolását, több országokból származó majd szeretné az ország oszlop statisztikai adatainak frissítése.

Kérje meg, ha a lekérdezés még hibaelhárítás első kérdések egyike, **"Naprakészek a statisztika?"**

Ez a kérdés nem szerepel az adatok korát válaszoló. Előfordulhat, hogy egy naprakész statisztika objektum régi, ha az alapul szolgáló adatokhoz nincs lényeges változás történt. Ha a sorok száma jelentősen megváltozott, vagy egy oszlop értékei elosztása jelentős változás *majd* ideje statisztikai adatainak frissítése.

Mivel annak meghatározásához, hogy a tábla adatainak módosult-e az utolsó idő statisztika frissítése nem dinamikus kezelési nézetet, a statisztika korát ismerete adja meg a képen látható része.  A következő lekérdezés segítségével határozza meg a legutolsó alkalommal a statisztika frissítése minden táblában.  

> [!NOTE]
> Ne feledje, hogy az oszlop értékeinek eloszlását jelentős változás esetén frissítse statisztika függetlenül legutóbbi frissítése megtörtént.  
> 
> 

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc    
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co    
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty    
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb    
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm    
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

**Oszlopok dátum** az adatraktárban, például általában szükség gyakori statisztikai adatok frissítése. Minden alkalommal új sorok be vannak töltve az adatraktárba, új betöltése vagy tranzakció kerülnek. Ezek az adatok terjesztési módosítsa, majd ellenőrizze a statisztika elavult.  Ezzel ellentétben egy felhasználói tábla nemét oszlop statisztikai előfordulhat, hogy soha nem frissítenie kell. Feltéve, hogy a terjesztés az ügyfelek közötti állandó, új sorok hozzáadásakor a tábla változat nem fog módosítása az adatok terjesztési. Azonban ha az adatraktár tartalmazza, csak egy nemét, és több genders egy új követelményt eredményez, majd szeretné az nemét oszlop statisztikai adatainak frissítése.

További ismertetése [statisztika] [ Statistics] az MSDN Webhelyén.

## <a name="implementing-statistics-management"></a>Végrehajtási statisztika kezelése
Akkor érdemes gyakran az Adatbetöltési folyamat annak érdekében, hogy a terhelés végén frissíti statisztikáit kiterjeszteni. Az adatok betöltését akkor, ha a táblák leggyakrabban módosítása a méretük és/vagy a terjesztési értékek. Ezért ez a logikai hely valamelyik felügyeleti folyamat végrehajtásához.

A statisztika frissítése a betöltési folyamat során a következő alapelvek áll rendelkezésre:

* Győződjön meg arról, hogy rendelkezik-e frissíteni kell legalább egy statisztika objektum minden egyes betöltött táblákon. Ezzel frissíti a statisztikákat frissítés részeként táblázatinformációnak mérete (a sorok számát és a lapok száma).
* Összpontosítson az ILLESZTÉS, GROUP BY, ORDER BY vagy DISTINCT záradékban részt vevő oszlopokat.
* Fontolja meg "kulcs növekvő" oszlopok például tranzakció dátumok gyakrabban, mivel ezek az értékek nem fog szerepelni a statisztika hisztogram.
* Érdemes lehet, statikus terjesztési oszlopok gyakran frissíteni.
* Ne feledje, hogy minden statisztikai adat objektum frissül a feladatütemezési. Egyszerűen végrehajtási `UPDATE STATISTICS <TABLE_NAME>` nem mindig ideális, különösen a statisztika objektumok sok nagy táblák esetében.

További ismertetése [számossága becslés] [ Cardinality Estimation] az MSDN Webhelyén.

## <a name="examples-create-statistics"></a>Példák: Statisztikák létrehozása
Ezek a példák használatát mutatják be különböző beállítások statisztikák létrehozásához. A beállítások, amelyekkel az egyes oszlopok az adatok és az oszlop a lekérdezésekben használt hogyan jellemzői függ.

### <a name="create-single-column-statistics-with-default-options"></a>Egy oszlop statisztikák létrehozása az alapértelmezett beállításokkal
Statisztikákat létrehozni egy olyan oszlop, adjon meg egy nevet a statisztika objektum és az oszlop neve.

Ez a szintaxis összes alapértelmezett beállítást használja. Alapértelmezés szerint az SQL Data Warehouse minták **20 százalékos** a tábla statisztikai létrehozásakor.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Példa:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Minden sor megvizsgálásával egyoszlopos statisztikát létrehozása
Az alapértelmezett mintavételi 20 százalékos aránya a legtöbb esetben elegendő. A mintavételi ráta azonban módosíthatja.

Példa a teljes táblázat, használja ezt a szintaxist:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Példa:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Egy oszlop statisztikák létrehozása a mintaméret megadásával
Azt is megteheti adhatja meg a minta mérete százalékban:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Egyoszlopos statisztikát csak egy sor létrehozása
Létrehozhat statisztika is egy részét a sorokat a tábla. Ezt nevezik a szűrt statisztikai.

Használhatja például szűrt statisztikákat, ha azt tervezi, hogy egy adott partícióra egy nagy particionált tábla lekérdezése. Csak a partíció értékek statisztika létrehozásával a statisztika pontossága fog javításához, illetve ezért javíthatja a lekérdezések teljesítményét.

Ez a Példa statisztika értéktartománya hoz létre. Az értékek is könnyen megfelelően kell megadni egy partíció értékek tartományán.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> A lekérdezésoptimalizáló figyelembe kell venni a szűrt statisztikákat, ha az elosztott lekérdezés terv úgy dönt, akkor az a lekérdezés kell férnie a statisztika objektum definíciója. Az előző példában a lekérdezés ahol záradékban kell meghatároznia 2000101 és 20001231 közötti Oszlop1 értékek használatával.
> 
> 

### <a name="create-single-column-statistics-with-all-the-options"></a>Egy oszlop statisztikák létrehozása a beállításokkal
A beállítások is kombinálhatja együtt. A következő példa egy szűrt statisztikákat objektum egyéni mintaméret hoz létre:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

A teljes referenciáért lásd: [CREATE statistics UTASÍTÁSHOZ] [ CREATE STATISTICS] az MSDN Webhelyén.

### <a name="create-multi-column-statistics"></a>Több oszlop statisztikai adatainak létrehozása
Több oszlop statisztikai adatainak objektum létrehozásához, egyszerűen használja az előző példák, de meg több oszlopot.

> [!NOTE]
> A hisztogram, amely használható a lekérdezés eredményében sorok számának becslése, az első oszlop szerepel a statisztika objektum definíciója csak érhető el.
> 
> 

Ebben a példában a hisztogram van *termék\_kategória*. Kereszt-oszlop statisztikai adatainak kiszámítása *termék\_kategória* és *termék\_sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Mivel közötti összefüggés *termék\_kategória* és *termék\_sub\_kategória*, egy több oszlop statisztikai adatainak objektum lehet hasznos, ha ezek oszlopok egyszerre érhetők el.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Egy táblázat összes oszlopa statisztikák létrehozása
Statisztika létrehozásának egyik módja, hogy a következő tábla létrehozása után adja ki a CREATE statistics UTASÍTÁSHOZ parancsok:

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Statisztika létrehozásához minden oszlop egy adatbázisban tárolt eljárás használata
Az SQL Data Warehouse az SQL Server nincs egyenértékű sp_create_stats rendszerszintű tárolt eljárást. Ez a tárolt eljárás objektumot hoz létre egy oszlop statisztika minden oszlop, amely még nem rendelkezik statisztikai adatbázis.

Az alábbi példa segítséget Ismerkedjen meg az adatbázis tervét. Nyugodtan igazodjon az igényeinek:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type IS NULL
BEGIN
    SET @create_type = 1;
END;

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN    sys.[external_tables] e    ON    e.[object_id]        = t.[object_id]
WHERE       l.[object_id] IS NULL
AND            e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+CONVERT(varchar(4),@sample_pct)+' PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Statisztikák létrehozása az alapértelmezett beállításokat használja a táblázat összes oszlopa, egyszerűen hívja meg az eljárást.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```
Statisztikák létrehozása a fullscan használatával a táblázat összes oszlopa, hívja meg ezt az eljárást:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```
A mintában szereplő statisztikák létrehozása a táblázat összes oszlopa, adja meg a minta százalékos értéke, és 3.  Ezt az eljárást használja a 20 % mintavételi gyakoriság.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```


Létrehozásához minden oszlop statisztikai mintát 

## <a name="examples-update-statistics"></a>Példák: Statisztika frissítése
A statisztikák frissítése, a következőket teheti:

- Egy statisztikai objektum frissítése. Adja meg a frissíteni kívánt statisztika objektum neve.
- Egy tábla összes statisztika objektumok frissítése. Adja meg a táblázat helyett egy adott statisztika objektum nevét.

### <a name="update-one-specific-statistics-object"></a>Egy adott statisztika objektum frissítése
A következő szintaxissal egy adott statisztika objektum frissítése:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Példa:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Adott statisztika objektumok frissítése, minimalizálhatja az idő és a statisztika kezeléséhez szükséges erőforrások. Ehhez szükséges néhány-re a legjobb statisztika objektumokat frissíteni.

### <a name="update-all-statistics-on-a-table"></a>Egy tábla összes statisztika frissítése
Ez azt jelenti, egy egyszerű módszer a táblán statisztika objektumok frissítése:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Példa:

```sql
UPDATE STATISTICS dbo.table1;
```

Jelen nyilatkozat is könnyen használható. Ne feledje azonban, hogy frissíti *összes* a tábla statisztikai, és ezért előfordulhat, hogy végezze el a szükségesnél több munkát. Ha a teljesítmény darabolása nem okoz problémát, ez az a legegyszerűbb és leghatékonyabb módon garantálja, hogy a statisztikákat a naprakészek legyenek.

> [!NOTE]
> Egy tábla összes statisztika frissítése, az SQL Data Warehouse minta a táblázat minden egyes statisztika objektumhoz megvizsgálja hajtja végre. Ha a tábla nagy és sok oszlop és sok statisztika, lehet, hatékonyabb, ha igények alapján egyéni statisztikai adatainak frissítése.
> 
> 

Egy végrehajtásához egy `UPDATE STATISTICS` eljárás, lásd: [az ideiglenes táblák][Temporary]. A végrehajtási módszer kis mértékben eltér az előző `CREATE STATISTICS` eljárás, de az eredmény nem ugyanaz.

Tekintse meg a teljes szintaxissal [Update Statistics] [ Update Statistics] az MSDN Webhelyén.

## <a name="statistics-metadata"></a>Statisztika metaadatok
Több rendszer nézetek és függvények, amelyek segítségével található információ a statisztikákat is van. Például láthatja, ha egy statisztika objektum lehet elavult a statisztikák-date függvény használatával statisztika volt utoljára létrehozásakor vagy frissítésekor.

### <a name="catalog-views-for-statistics"></a>A statisztika katalógusnézetekre
Ezek a rendszer nézetek statisztika ismertetik:

| katalógusnézet használatával derítheti ki | Leírás |
|:--- |:--- |
| [sys.columns][sys.columns] |Egy sor minden egyes oszlophoz. |
| [sys.objects][sys.objects] |Egy sor minden objektum az adatbázisban. |
| [sys.schemas][sys.schemas] |Egy sor minden séma az adatbázisban. |
| [sys.stats][sys.stats] |Egy sor minden egyes statisztika objektumhoz. |
| [sys.stats_columns][sys.stats_columns] |A statisztika objektum minden egyes oszlopának egy sort. Vissza a sys.columns mutató hivatkozásokat tartalmaz. |
| [sys.tables][sys.tables] |Egy sor minden táblához (külső táblát tartalmazza). |
| [sys.table_types][sys.table_types] |Egy sor egyes adattípusok esetében. |

### <a name="system-functions-for-statistics"></a>A statisztika rendszer funkciók
A rendszer függvények hasznosak statisztika használata:

| Rendszer-funkció | Leírás |
|:--- |:--- |
| [STATS_DATE][STATS_DATE] |A statisztika objektum utolsó módosításának dátuma. |
| [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] |Az értékek a statisztika objektum azt értelmezni tudja módon a terjesztési összefoglaló szintű és részletes információk. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Statisztika oszlopok és funkciók egyesítése egy nézet
Ez a nézet számos lehetőséget kínál a statisztika kapcsolódó oszlopok, és annak az eredménye a STATS_DATE() függvény együtt.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>DBCC SHOW_STATISTICS() példák
DBCC SHOW_STATISTICS() statisztika objektumon belül tárolt adatainak megjelenítése. Ezeket az adatokat tartalmaz a három részből áll:

- Fejléc
- Sűrűség vektoros
- Hisztogram

A statisztika fejléc metaadatait. A hisztogram statisztika objektum első kulcsoszlop értékeinek eloszlását jeleníti meg. A sűrűség vektor kereszt-oszlop korrelációs méri. Az SQL Data Warehouse kiszámítja a statisztika objektumban adatokat a számosság becsléseket.

### <a name="show-header-density-and-histogram"></a>Fejléc, sűrűség és hisztogram megjelenítése
A egyszerű példa bemutatja a statisztika objektum három része:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Példa:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Egy vagy több részei DBCC SHOW_STATISTICS() megjelenítése
Ha érdekli csak megtekintés részét, használja a `WITH` záradék, és adja meg a megjeleníteni kívánt részeket:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Példa:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>DBCC SHOW_STATISTICS() különbségek
DBCC SHOW_STATISTICS() szigorúbban végrehajtani az SQL Data Warehouse az SQL Server képest:

- Nem dokumentált funkciók nem támogatottak.
- Nem használható a Stats_stream.
- Nem tudja csatlakoztatni meghatározott fájlcsoportokat statisztikai adatok eredményeit. Ha például (STAT_HEADER csatlakozás DENSITY_VECTOR).
- NO_INFOMSGS üzenetet figyelmen kívül hagyás nem állítható be.
- Statisztika neveket burkoló szögletes zárójelek között nem használható.
- Nem használható oszlopnevek statisztika objektumokat azonosító.
- Egyéni hiba 2767 nem támogatott.

## <a name="next-steps"></a>További lépések
További részletekért lásd: [DBCC SHOW_STATISTICS] [ DBCC SHOW_STATISTICS] az MSDN Webhelyén.

  További tudnivalókért tekintse meg a cikkek [tábla áttekintése][Overview], [tábla adattípusok][Data Types], [terjesztése egy tábla] [ Distribute], [Tábla indexelő][Index], [tábla particionáló][Partition], és [Az ideiglenes táblák][Temporary].
  
   Gyakorlati tanácsok kapcsolatban bővebben lásd: [SQL Data Warehouse gyakorlati tanácsok][SQL Data Warehouse Best Practices].  

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->  
[Cardinality Estimation]: https://msdn.microsoft.com/library/dn600374.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]:https://msdn.microsoft.com/library/ms174384.aspx
[Statistics]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  
