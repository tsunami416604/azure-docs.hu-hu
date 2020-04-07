---
title: Statisztika létrehozása, frissítése
description: Javaslatok és példák a synapse SQL-készlet tábláiban lévő lekérdezésoptimalizálási statisztikák létrehozására és frissítésére.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 05/09/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5fae2bba0acc4ab462c91f7272694d032fc6ceaa
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742666"
---
# <a name="table-statistics-in-synapse-sql-pool"></a>Táblastatisztika a Synapse SQL-készletben

Ebben a cikkben javaslatokat és példákat talál az SQL-készletben lévő táblák lekérdezésoptimalizálási statisztikáinak létrehozására és frissítésére.

## <a name="why-use-statistics"></a>Miért érdemes statisztikát használni?

Minél több SQL-készlet ismeri az adatokat, annál gyorsabban tud lekérdezéseket végrehajtani ellene. Az adatok SQL-készletbe való betöltése után az adatokra vonatkozó statisztikák gyűjtése az egyik legfontosabb dolog, amit a lekérdezések optimalizálása érdekében tehet.

Az SQL-készlet lekérdezés-optimalizáló költségalapú optimalizáló. Összehasonlítja a különböző lekérdezési tervek költségét, majd kiválasztja a legalacsonyabb költségű tervet. A legtöbb esetben azt a tervet választja ki, amely a leggyorsabban hajtja végre.

Ha például az optimalizáló úgy becsüli meg, hogy a lekérdezés szűrésének dátuma egy sort ad vissza, akkor egy tervet választ. Ha úgy becsüli, hogy a kiválasztott dátum 1 millió sort ad vissza, akkor egy másik tervet ad vissza.

## <a name="automatic-creation-of-statistic"></a>A statisztika automatikus létrehozása

Ha az adatbázis AUTO_CREATE_STATISTICS beállítás be van kapcsolva, az SQL-készlet elemzi a hiányzó statisztikákra vonatkozó bejövő felhasználói lekérdezéseket.

Ha a statisztika hiányzik, a lekérdezésoptimalizáló statisztikákat hoz létre a lekérdezés egyes oszlopairól, vagy illesztési feltételt hoz létre a lekérdezési terv számossági becsléseinek javítása érdekében.

> [!NOTE]
> A statisztikák automatikus létrehozása alapértelmezés szerint be van kapcsolva.

Az SQL-készlet AUTO_CREATE_STATISTICS konfigurálva a következő parancs futtatásával ellenőrizheti:

```sql
SELECT name, is_auto_create_stats_on
FROM sys.databases
```

Ha az SQL-készlet nem rendelkezik AUTO_CREATE_STATISTICS konfigurálva, javasoljuk, hogy a következő parancs futtatásával engedélyezze ezt a tulajdonságot:

```sql
ALTER DATABASE <yourdatawarehousename>
SET AUTO_CREATE_STATISTICS ON
```

Ezek a kimutatások a statisztikák automatikus létrehozását indítják el:

- SELECT
- BESZÚRÁS-KIJELÖLÉS
- CTAS
- UPDATE
- DELETE
- MAGYARÁZZA EL, ha illesztést vagy predikátum jelenlétét tartalmazza,

> [!NOTE]
> A statisztikák automatikus létrehozása nem ideiglenes vagy külső táblákon jön létre.

A statisztikák automatikus létrehozása szinkron módon történik, így kissé csökkenhet a lekérdezés teljesítménye, ha az oszlopokból hiányoznak a statisztikák. Az egyetlen oszlop statisztikáinak létrehozásához a táblázat méretétől függ.

A mérhető teljesítménycsökkenés elkerülése érdekében győződjön meg arról, hogy a statisztikák először a teljesítményteszt számítási feladatának végrehajtásával jöttek létre a rendszer profilalkotása előtt.

> [!NOTE]
> A statisztika létrehozása a [sys.dm_pdw_exec_requests-ba](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) kerül, más felhasználói környezetben.

Az automatikus statisztikák létrehozásakor a következők lesznek: _WA_Sys_<8 jegyű oszlopazonosító a Hex>_<8 számjegyű táblázatazonosítót a Hex>. Megtekintheti a [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) parancs futtatásával már létrehozott statisztikákat:

```sql
DBCC SHOW_STATISTICS (<table_name>, <target>)
```

A table_name a megjelenítendő statisztikákat tartalmazó tábla neve. Ez a tábla nem lehet külső tábla. A cél annak a célindexnek, statisztikának vagy oszlopnak a neve, amelynek statisztikai adatait meg szeretné jeleníteni.

## <a name="update-statistics"></a>Statisztika frissítése

Az egyik ajánlott eljárás a dátumoszlopok statisztikáinak frissítése minden nap, amikor új dátumokat ad hozzá. Minden alkalommal, amikor új sorok töltődnek be az SQL-készletbe, új betöltési dátumok vagy tranzakciódátumok kerülnek hozzáadásra. Ezek a kiegészítések megváltoztatják az adatok eloszlását, és a statisztikákat elavultá teszik.

Előfordulhat, hogy a vevőtábla ország/régió oszlopára vonatkozó statisztikákat soha nem kell frissíteni, mivel az értékek eloszlása általában nem változik. Feltételezve, hogy a terjesztés állandó a vevők között, új sorok hozzáadása a tábla változatnem fogja megváltoztatni az adatok eloszlását.

Ha azonban az SQL-készlet csak egy országot/régiót tartalmaz, és egy új országból/régióból hoz be adatokat, így több országból/régióból származó adatokat tárol, akkor frissítenie kell az ország/régió oszlop statisztikáit.

A statisztikák at az alábbi ajánlások teszik:

|||
|-|-|
| **A statisztikafrissítések gyakorisága**  | Konzervatív: Napi </br> Az adatok betöltése vagy átalakítása után |
| **Mintavételezés** |  Kevesebb, mint 1 milliárd sor, használja az alapértelmezett mintavételezést (20 százalék). </br> Több mint 1 milliárd sor esetén két százalékos mintavételt használjon. |

A lekérdezés hibaelhárításakor feltett első kérdések egyike a **"Naprakészek a statisztikák?"**

Erre a kérdésre nem lehet választ adni az adatok kora szerint. Egy naprakész statisztikai objektum régi lehet, ha nem történt lényeges változás az alapul szolgáló adatokban.

> [!TIP]
> Ha a sorok száma jelentősen megváltozott, vagy egy oszlop értékeinek eloszlásában lényeges változás történt, *akkor* itt az ideje a statisztikák frissítésének.

Nincs dinamikus felügyeleti nézet annak meghatározására, hogy a táblában lévő adatok megváltoztak-e a statisztika legutóbbi frissítése óta. Ismerve a kor a statisztikák nyújthat egy részét a kép.

A következő lekérdezés segítségével határozhatja meg, hogy mikor frissültek utoljára a statisztikák az egyes táblákon.

> [!NOTE]
> Ha egy oszlop értékeinek eloszlásában lényeges változás történik, a statisztikákat a legutóbbi frissítésüktől függetlenül frissíteni kell.

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

Az SQL-készlet **dátumoszlopai** például általában gyakori statisztikai frissítéseket igényelnek. Minden alkalommal, amikor új sorok töltődnek be az SQL-készletbe, új betöltési dátumok vagy tranzakciódátumok kerülnek hozzáadásra. Ezek a kiegészítések megváltoztatják az adatok eloszlását, és a statisztikákat elavultá teszik.

Ezzel szemben előfordulhat, hogy egy ügyféltábla nemi oszlopára vonatkozó statisztikákat soha nem kell frissíteni. Feltételezve, hogy a terjesztés állandó a vevők között, új sorok hozzáadása a tábla változatnem fogja megváltoztatni az adatok eloszlását.

Ha az SQL-készlet csak egy nemet tartalmaz, és egy új követelmény több nemet eredményez, akkor frissítenie kell a nemek oszlopának statisztikáit.

További információt a Statisztika általános útmutatója című témakörben [talál.](/sql/relational-databases/statistics/statistics?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="implementing-statistics-management"></a>A statisztikák kezelésének megvalósítása

Gyakran érdemes kiterjeszteni az adatbetöltési folyamatot annak érdekében, hogy a statisztikák frissüljenek a terhelés végén.

Az adatterhelés az, amikor a táblák leggyakrabban módosítják méretüket és/vagy az értékek eloszlását. Az adatbetöltés logikus hely bizonyos felügyeleti folyamatok megvalósításához.

A terhelési folyamat során a statisztikák frissítésére vonatkozóan a következő irányadó elvek állnak rendelkezésre:

- Győződjön meg arról, hogy minden betöltött tábla legalább egy statisztikai objektumot frissített. Ez frissíti a tábla méretét (sorszám és oldalszám) információkat a statisztikai frissítés részeként.
- Összpontosítson a JOIN, GROUP BY, ORDER BY és DISTINCT záradékokban részt vevő oszlopokra.
- Fontolja meg a "növekvő kulcs" oszlopok, például a tranzakciódátumok gyakrabban történő frissítését, mivel ezek az értékek nem fognak szerepelni a statisztikai hisztogramban.
- Fontolja meg a statikus terjesztési oszlopok ritkább frissítését.
- Ne feledje, hogy minden statisztikai objektum egymás után frissül. Az `UPDATE STATISTICS <TABLE_NAME>` egyszerű megvalósítás nem mindig ideális, különösen a sok statisztikai objektumot rendelkező széles asztaloknál.

További információ: [Cardinality Estimation](/sql/relational-databases/performance/cardinality-estimation-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="examples-create-statistics"></a>Példák: Statisztikák létrehozása

Ezek a példák azt mutatják be, hogyan használhatók a különböző beállítások a statisztikák létrehozásához. Az egyes oszlopokhoz használt beállítások az adatok jellemzőitől és az oszlop lekérdezésekben való használatának módjától függenek.

### <a name="create-single-column-statistics-with-default-options"></a>Egyoszlopos statisztika létrehozása az alapértelmezett beállításokkal

Ha statisztikát szeretne létrehozni egy oszlopon, adja meg a statisztikai objektum nevét és az oszlop nevét.

Ez a szintaxis az összes alapértelmezett beállítást használja. Alapértelmezés szerint az SQL-készlet a tábla **20 százalékát** mintáteszi, amikor statisztikákat hoz létre.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Példa:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Egyoszlopos statisztika létrehozása minden sor vizsgálatával

Az alapértelmezett 20 százalékos mintavételi arány a legtöbb esetben elegendő. A mintavételi arány azonban módosítható.

A teljes táblázat mintavételéhez használja ezt a szintaxist:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Példa:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>Egyoszlopos statisztika létrehozása a mintaméret megadásával

Azt is megteheti, hogy a minta nagyságát százalékként adja meg:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>Egyoszlopos statisztika létrehozása csak néhány sorra

Statisztikákat is létrehozhat a táblázat sorainak egy részéről. Ezt szűrt statisztikának nevezzük.

Szűrt statisztikákat például használhat, ha egy nagy particionált tábla adott partícióját szeretné lekérdezni. Ha csak a partícióértékekre hoz létre statisztikákat, a statisztikák pontossága javulni fog, és ezáltal javítja a lekérdezési teljesítményt.

Ez a példa egy értéktartománystatisztikát hoz létre. Az értékek könnyen definiálhatók úgy, hogy megfeleljenek a partícióérték-tartományának.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> Ahhoz, hogy a lekérdezésoptimalizáló az elosztott lekérdezési terv kiválasztásakor szűrt statisztikákat használjon, a lekérdezésnek bele kell férnie a statisztikai objektum definíciójába. Az előző példában a lekérdezés WHERE záradékának 2000101 és 20001231 között col1 értékeket kell megadnia.

### <a name="create-single-column-statistics-with-all-the-options"></a>Egyoszlopos statisztika létrehozása az összes beállítással

A beállításokat kombinálhatja is. A következő példa egyéni mintamérettel rendelkező szűrt statisztikai objektumot hoz létre:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

A teljes referencia a STATISZTIKA LÉTREHOZÁSA című [témakörben látható.](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="create-multi-column-statistics"></a>Többoszlopos statisztika létrehozása

Többoszlopos statisztikai objektum létrehozásához használja az előző példákat, de adjon meg további oszlopokat.

> [!NOTE]
> A hisztogram, amely a lekérdezés eredményében szereplő sorok számának becslésére szolgál, csak a statisztikai objektumdefinícióban felsorolt első oszlophoz érhető el.

Ebben a példában a hisztogram a *termékkategóriában\_* van. Az oszlopközi statisztikákat a *termékkategóriára\_* és *a terméksub_category\_* alapján számítják ki:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Mivel korreláció van *a termékkategória\_* és a *termék-alkategória\_\_* között, a többoszlopos statisztikai objektum akkor lehet hasznos, ha ezek az oszlopok egyszerre érhetők el.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Statisztika létrehozása a tábla összes oszlopára vonatkozóan

A statisztikák létrehozásának egyik módja a CREATE STATISTICS parancsok kiadása a tábla létrehozása után:

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

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Az adatbázis összes oszlopára vonatkozó statisztikák létrehozása tárolt eljárással

Az SQL-készlet nem rendelkezik az SQL Server ben sp_create_stats megfelelő rendszertároló eljárással. Ez a tárolt eljárás egyetlen oszlopos statisztikai objektumot hoz létre az adatbázis minden olyan oszlopán, amely még nem rendelkezik statisztikákkal.

A következő példa segítséget nyújt az adatbázis tervezésének megkezdéséhez. Nyugodtan igazítsa az Ön igényeihez.

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

Ha a tábla összes oszlopára vonatkozóan statisztikát szeretne létrehozni az alapértelmezett értékek használatával, hajtsa végre a tárolt eljárást.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```

Ha a táblázat összes oszlopára vonatkozóstatisztikákat fullscan használatával szeretné létrehozni, hívja meg ezt az eljárást.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```

Ha a tábla összes oszlopára vonatkozóan mintavételezett statisztikákat szeretne létrehozni, írja be a 3 értéket és a mintaszázalékot. Ez az eljárás 20 százalékos mintavételi arányt használ.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```

## <a name="examples-update-statistics"></a>Példák: Statisztika frissítése

A statisztikák frissítéséhez a következőket teheti:

- Egy statisztikai objektum frissítése. Adja meg a frissíteni kívánt statisztikai objektum nevét.
- A tábla összes statisztikai objektumának frissítése. Adja meg a tábla nevét egy adott statisztikai objektum helyett.

### <a name="update-one-specific-statistics-object"></a>Egy adott statisztikai objektum frissítése

Adott statisztikai objektum frissítéséhez használja az alábbi szintaxist:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Példa:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Adott statisztikai objektumok frissítésével minimalizálhatja a statisztikák kezeléséhez szükséges időt és erőforrásokat. Ehhez szükség van néhány gondolat, hogy válassza ki a legjobb statisztika objektumok frissíteni.

### <a name="update-all-statistics-on-a-table"></a>Táblázat összes statisztikájának frissítése

A táblázatban szereplő összes statisztikai objektum frissítésének egyszerű módja a következő:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Példa:

```sql
UPDATE STATISTICS dbo.table1;
```

Az UPDATE STATISTICS utasítás könnyen használható. Ne feledje, hogy frissíti az *összes* statisztikát az asztalon, és ezért több munkát végezhet, mint amennyi szükséges. Ha a teljesítmény nem probléma, ez a legegyszerűbb és legteljesebb módja annak, hogy garantálja, hogy a statisztikák naprakészek.

> [!NOTE]
> A tábla összes statisztikájának frissítésekor az SQL-készlet beszkad egy vizsgálaton, hogy mintát vehessaz egyes statisztikai objektumok táblájából. Ha a táblázat nagy, és sok oszlopot és sok statisztikát tartalmaz, hatékonyabb lehet az egyes statisztikák szükség alapján való frissítése.

Az `UPDATE STATISTICS` eljárás végrehajtásáról az [Ideiglenes táblázatok ban](sql-data-warehouse-tables-temporary.md)található. A végrehajtási módszer némileg `CREATE STATISTICS` eltér az előző eljárástól, de az eredmény ugyanaz.

A teljes szintaxisról a [Statisztika frissítése című témakörben van.](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="statistics-metadata"></a>Statisztikai metaadatok

Számos rendszernézet és funkció létezik, amelyek segítségével információkat kereshet a statisztikákról. Ha például egy statisztikai objektum elavult, a statisztikadátum függvénnyel például megtekintheti, hogy mikor hozták létre vagy frissítették utoljára a statisztikákat.

### <a name="catalog-views-for-statistics"></a>Statisztika katalógusnézetei

Ezek a rendszernézetek a statisztikákkal kapcsolatos információkat tartalmaznak:

| Katalógus nézet | Leírás |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Minden oszlophoz egy sor tartozik. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Az adatbázis minden objektumához egy sor tartozik. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Az adatbázis minden sémájához egy sor tartozik. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Minden statisztikai objektumhoz egy sor tartozik. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |A statisztikai objektum minden oszlopához egy sor tartozik. Visszamutat a sys.columns-ra. |
| [sys.tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Minden táblához egy sor tartozik (külső táblákat is tartalmaz). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Minden adattípushoz egy sor tartozik. |

### <a name="system-functions-for-statistics"></a>Rendszerfunkciók a statisztikához

Ezek a rendszerfunkciók a statisztikákkal való együttműködéshez hasznosak:

| Rendszerfüggvény | Leírás |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |A statisztikai objektum utolsó frissítésének dátuma. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Összefoglaló szint és részletes információk az értékek eloszlásáról a statisztikai objektum szerint. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Statisztikai oszlopok és függvények egyesítése egyetlen nézetben

Ez a nézet a STATS_DATE() függvény statisztikáihoz és eredményeihez kapcsolódó oszlopokat együtt hozza létre.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_definition]
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

## <a name="dbcc-show_statistics-examples"></a>DbCC SHOW_STATISTICS() példák

A DBCC SHOW_STATISTICS() a statisztikai objektumban tartott adatokat jeleníti meg. Ezek az adatok három részből állnak:

- Fejléc
- Sűrűségvektor
- Hisztogram

A statisztikák fejlécének metaadatai. A hisztogram a statisztikai objektum első kulcsoszlopában jeleníti meg az értékek eloszlását. A sűrűségvektor több oszlopkorrelációt mér.

> [!NOTE]
> AZ SQL-készlet számossági becsléseket számít ki a statisztikai objektum ban lévő adatok bármelyikével.

### <a name="show-header-density-and-histogram"></a>Fejléc, sűrűség és hisztogram megjelenítése

Ez az egyszerű példa egy statisztikai objektum mindhárom részét mutatja:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Példa:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-show_statistics"></a>A DBCC egy vagy több részének megjelenítése SHOW_STATISTICS()

Ha csak bizonyos alkatrészeket szeretne megtekinteni, használja a `WITH` záradékot, és adja meg, hogy mely részeket szeretné látni:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Példa:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-show_statistics-differences"></a>DBCC SHOW_STATISTICS() különbségek

A DBCC SHOW_STATISTICS() az SQL Server kiszolgálóhoz képest szigorúbban van megvalósítva az SQL készletben:

- A nem dokumentált szolgáltatások nem támogatottak.
- A Stats_stream nem használható.
- A statisztikai adatok adott részhalmazához tartozó eredmények nem illeszthetők össze. Például STAT_HEADER JOIN DENSITY_VECTOR.
- NO_INFOMSGS nem állítható be az üzenetek elnyomására.
- A statisztikai nevek körül nem használhatók szögletes zárójelek.
- Az oszlopnevek nem használhatók statisztikai objektumok azonosítására.
- A 2767-es egyéni hiba nem támogatott.

## <a name="next-steps"></a>További lépések

A lekérdezési teljesítmény további javításáról a [Munkaterhelés figyelése című](sql-data-warehouse-manage-monitor.md) témakörben találja.
