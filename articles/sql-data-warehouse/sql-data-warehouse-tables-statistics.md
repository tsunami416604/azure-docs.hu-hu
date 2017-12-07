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
ms.openlocfilehash: 2349708f607364c34926a2ea1baa025201934973
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2017
---
# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>Az SQL Data Warehouse táblák statisztikák kezelése
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

Minél több ismeri az SQL Data Warehouse az adatokat, minél gyorsabban hajtsa végre a lekérdezéseket az adatok alapján.  A úgy, hogy az SQL Data Warehouse szolgál az adatokról, kérje meg, hogy statisztikája az adatok gyűjtése.  Statisztika az adatokról, akkor az a legfontosabb dolog, a lekérdezések optimalizálását is van.  Statisztika SQL Data Warehouse a legoptimálisabb terv a lekérdezések létrehozása érdekében.  Ez azért, mert az SQL Data Warehouse lekérdezésoptimalizáló egy alapján optimalizáló.  Ez azt jelenti, hogy összehasonlítja a különböző lekérdezésterveket költségét, és majd úgy dönt, hogy a tervben a legalacsonyabb költséget, és a terv, amely végrehajtja a leggyorsabb kell.

Statisztika is létrehozható, csak egy oszlop, több oszlop vagy táblázat index.  Statisztika tárolódnak a hisztogram, amely a tartomány- és értékek kiválasztásánál rögzíti.  Ez az különösen fontos, amikor a optimalizáló kell kiértékelni illesztéseket, GROUP BY, HAVING és a WHERE záradék a lekérdezésben.  Például ha a optimalizáló becslése, hogy a jelenleg korlátozza a lekérdezés dátuma 1 sort ad vissza, dönthet úgy, nagyon eltérő megtervezése mint azt, hogy azok szerint megadott dátum, akkor becslése kijelölt lesz 1 millió sort adja vissza.  Míg rendkívül fontos a statisztikák létrehozása, is ugyanilyen fontos, hogy statisztika *pontosan* táblázat aktuális állapotát jeleníti meg.  Naprakész statisztika biztosítja, hogy egy jó terv a optimalizáló van-e kiválasztva.  Az előfizetések hozta létre a optimalizáló csak a következők megegyezik a statisztikai adatait.

A folyamat létrehozásának, és frissítse a statisztikai adatokat jelenleg egy kézi művelet, de ehhez nagyon egyszerű.  Ez nem az SQL Server, mely automatikusan létrehozza, és egyetlen oszlopok és indexek statisztikák frissíti.  Az alábbi információk segítségével nagy mértékben automatizálható a statisztikák felügyeleti az adatokon. 

## <a name="getting-started-with-statistics"></a>Ismerkedés a statisztikák
 Minden egyes oszlophoz a mintában szereplő statisztikák létrehozása egyszerű módja statisztika használatába.  Egyaránt fontos, hogy naprakész állapotban tarthatja az statisztika, mert a konzervatív megközelítés lehet frissíteni a statisztikákat a naponta, vagy minden egyes betöltés után. Mindig érdemes figyelembe venni, hogyan viszonyul egymáshoz a teljesítmény és a statisztikák létrehozásának és frissítésének költségei.  Ha úgy gondolja, hogy túl sokáig tart az összes statisztika karbantartása, lehet, hogy körültekintőbben kell kiválasztania, mely oszlopok rendelkezzenek statisztikákkal vagy melyek igényelnek gyakori frissítést.  Például előfordulhat, hogy szeretné frissíteni a dátumoszlopot, naponta, új értékeket vehetők helyett minden betöltés után. Ebben az esetben érheti el a legtöbb juttatás azzal, hogy a statisztika oszlopokon érintett illesztéseket, GROUP BY, HAVING és a WHERE záradék.  Ha nagy mennyiségű oszlopok a SELECT záradékban csak használt tábla, nem segíthetnek, ezen oszlopokon statisztika, és kissé költségeik további erőfeszítésekre azonosításához csak azokat az oszlopokat, ahol statisztika segít, csökkentheti az időt a statisztika karbantartása.

## <a name="multi-column-statistics"></a>Több oszlop statisztikai adatainak
Mellett statisztikák létrehozása egyetlen oszlopokon, előfordulhat, hogy a lekérdezések ki a előnyeit több oszlop statisztikai adatainak.  Több oszlop statisztikákat statisztikákat létrehozni az oszlopok listája.  A lista első oszlopa egy oszlop statisztikai tartoznak, valamint bizonyos kereszt-oszlop korrelációs adatokat nevű sűrűség.  Például ha egy táblázatot, amelyhez csatlakozik, a két oszlop között, előfordulhat, hogy az SQL Data Warehouse jobban is optimalizálhatja a terv, támogatja a két oszlop közötti kapcsolatot.   Több oszlop statisztikai adatainak javíthatja a lekérdezések teljesítményét az egyes műveletek, például összetett illesztések és a csoportosítás alapját.

## <a name="updating-statistics"></a>Frissítse a statisztikai adatokat
Frissítse a statisztikai adatokat az adatbázis-felügyeleti rutin fontos részét képezi.  Az adatbázis adatai eloszlását megváltozásakor statisztika frissítenie kell.  Elévült statisztikát optimális lekérdezési teljesítmény irányítja.

Egy ajánlott úgy nem frissíthető statisztika dátumoszlopának dátumtulajdonságai minden nap új dátumok hozzáadása.  Minden alkalommal új sorok be vannak töltve az adatraktárba, új betöltése vagy tranzakció kerülnek. Ezek az adatok terjesztési módosítsa, majd ellenőrizze a statisztika elavult. Ezzel szemben egy felhasználói tábla ország oszlop statisztikai előfordulhat, hogy soha nem frissítenie kell, a terjesztési értékek általában nem változik. Feltéve, hogy a terjesztés az ügyfelek közötti állandó, új sorok hozzáadásakor a tábla változat nem fog módosítása az adatok terjesztési. Azonban ha az adatraktár csak tartalmaz egy országon, és az adatok egy új országból kapcsolná, az adatok tárolását, több országokból származó majd mindenképpen szeretné az ország oszlop statisztikai adatainak frissítése.

Az első megválaszolandó kérdések esetén végzett hibaelhárításhoz a lekérdezés egyik, "Are naprakész statisztika?"

Ez a kérdés nem szerepel az adatok korát válaszoló. Lehet, hogy naprakész statisztika objektum nagyon régi, ha az alapul szolgáló adatokhoz nincs lényeges változás történt. Ha a sorok száma jelentősen módosult, vagy nincs az adott oszlop értékeinek eloszlását jelentős változás *majd* ideje statisztikai adatainak frissítése.  

Referenciaként **SQL Server** (nem az SQL Data Warehouse) automatikusan frissíti a statisztikákat ilyen helyzetekben:

* Ha egyetlen sor a tábla sorainak hozzáadásakor, fog kapni a statisztikák automatikus frissítés
* Hozzáadásakor 500-nál több sort egy táblához legfeljebb 500 sorok kezdve (például a start rendelkezik 499, és hozzáadhatja 500 sorok 999 sorok összesen), az automatikus frissítés jelenik meg 
* Ha már több mint 500 sorok meg kell adnia a 500 további sorokat + 20 %-át a tábla méretét a statisztikák a láthatja az automatikus frissítés előtt

Mivel nincs DMV annak meghatározásához, hogy a tábla adatainak módosult-e az utolsó idő statisztika frissítése, a statisztika korát tudatában adja meg a képen látható része.  A következő lekérdezés segítségével határozza meg a legutóbbi a statisztika ahol minden táblában frissített.  

> [!NOTE]
> Ne feledje, hogy az adott oszlop értékeinek eloszlását jelentős változás esetén frissítenie kell a statisztika függetlenül legutóbbi frissítése megtörtént.  
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

Egy adatraktár dátumoszlopának dátumtulajdonságai például általában kell gyakori statisztikai adatok frissítése. Minden alkalommal új sorok be vannak töltve az adatraktárba, új betöltése vagy tranzakció kerülnek. Ezek az adatok terjesztési módosítsa, majd ellenőrizze a statisztika elavult.  Ezzel ellentétben egy felhasználói tábla nemét oszlop statisztikai előfordulhat, hogy soha nem frissítenie kell. Feltéve, hogy a terjesztés az ügyfelek közötti állandó, új sorok hozzáadásakor a tábla változat nem fog módosítása az adatok terjesztési. Azonban ha az adatraktár csak tartalmaz egy nemét, és több genders egy új követelményt eredményezi majd mindenképpen szeretné a nemét oszlop statisztikai adatainak frissítése.

További ismertetése [statisztika] [ Statistics] az MSDN Webhelyén.

## <a name="implementing-statistics-management"></a>Végrehajtási statisztika kezelése
Akkor érdemes gyakran az adatok betöltése a folyamat végrehajtásával biztosítható, hogy a terhelés végén frissíti statisztikáit kiterjeszteni. Az adatok betöltését akkor, ha a táblák leggyakrabban módosítása a méretük és/vagy a terjesztési értékek. Ezért ez a logikai hely valamelyik felügyeleti folyamat végrehajtásához.

Néhány alapelvek a statisztikák frissítése a betöltési folyamat során az alább:

* Győződjön meg arról, hogy rendelkezik-e frissíteni kell legalább egy statisztika objektum minden egyes betöltött táblákon. Ezzel frissíti a táblák (sorok számát és oldalszám) információi a statisztikák frissítés részeként.
* Összpontosítson az ILLESZTÉS, GROUP BY, ORDER BY és DISTINCT záradékban részt vevő oszlopokat
* Fontolja meg "kulcs növekvő" oszlopok például tranzakció dátumok gyakrabban, mivel ezek az értékek nem fog szerepelni a statisztika hisztogram.
* Érdemes lehet, statikus terjesztési oszlopok gyakran frissíteni.
* Ne feledje, hogy minden statisztikai adat objektum sorozat frissül. Egyszerűen végrehajtási `UPDATE STATISTICS <TABLE_NAME>` nem mindig ideális megoldás – különösen a statisztika objektumok sok nagy táblák esetében.

> [!NOTE]
> [Növekvő kulcs] vonatkozó részletes információért tekintse meg az SQL Server 2014 számossága becslés modell tanulmány.
> 
> 

További ismertetése [számossága becslés] [ Cardinality Estimation] az MSDN Webhelyén.

## <a name="examples-create-statistics"></a>Példák: Statisztikák létrehozása
Ezek a példák használatát mutatják be különböző beállítások statisztikák létrehozásához. A beállítások, amelyekkel az egyes oszlopok az adatok és az oszlop a lekérdezésekben használt hogyan jellemzői függ.

### <a name="a-create-single-column-statistics-with-default-options"></a>A. Egy oszlop statisztikák létrehozása az alapértelmezett beállításokkal
Statisztikákat létrehozni egy olyan oszlop, adjon meg egy nevet a statisztika objektum és az oszlop neve.

Ez a szintaxis összes alapértelmezett beállítást használja. Alapértelmezés szerint az SQL Data Warehouse-minták 20 százalékát a tábla statisztikai létrehozásakor.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Példa:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="b-create-single-column-statistics-by-examining-every-row"></a>B. Minden sor megvizsgálásával egyoszlopos statisztikát létrehozása
Az alapértelmezett mintavételi 20 százalékos aránya a legtöbb esetben elegendő. A mintavételi ráta azonban módosíthatja.

Példa a teljes táblázat, használja ezt a szintaxist:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Példa:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="c-create-single-column-statistics-by-specifying-the-sample-size"></a>C. Egy oszlop statisztikák létrehozása a mintaméret megadásával
Azt is megteheti adhatja meg a minta mérete százalékban:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="d-create-single-column-statistics-on-only-some-of-the-rows"></a>D. Egyoszlopos statisztikát csak egy sor létrehozása
Egy másik lehetőség, létrehozhat statisztika egy részét a sorokat a táblában. Ezt nevezik a szűrt statisztikai.

Használhat például szűrt statisztikákat, ha azt tervezi, hogy egy adott partícióra egy nagy particionált tábla lekérdezése. Csak a partíció értékek statisztika létrehozásával a statisztika pontossága fog javításához, illetve ezért javíthatja a lekérdezések teljesítményét.

Ez a Példa statisztika értéktartománya hoz létre. Az értékeket egy partíció értékek megfelelő könnyen kell meghatározni.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> A lekérdezésoptimalizáló figyelembe kell venni a szűrt statisztikákat, ha az elosztott lekérdezés terv úgy dönt, akkor az a lekérdezés kell férnie a statisztika objektum definíciója. Az előző példában a lekérdezés ahol záradékban kell meghatároznia 2000101 és 20001231 közötti Oszlop1 értékek használatával.
> 
> 

### <a name="e-create-single-column-statistics-with-all-the-options"></a>E. Egy oszlop statisztikák létrehozása a beállításokkal
Természetesen kombinálhatja a beállítások együtt. Az alábbi példában egy szűrt statisztikákat objektumot hoz létre egyéni mintájának méretét:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

A teljes referenciáért lásd: [CREATE statistics UTASÍTÁSHOZ] [ CREATE STATISTICS] az MSDN Webhelyén.

### <a name="f-create-multi-column-statistics"></a>F. Több oszlop statisztikai adatainak létrehozása
Hozzon létre egy több oszlopot tartalmazó statisztika, egyszerűen használja az előző példák, de további oszlopok megadása.

> [!NOTE]
> A hisztogram, amely használható a lekérdezés eredményében sorok számának becslése, az első oszlop szerepel a statisztika objektum definíciója csak érhető el.
> 
> 

Ebben a példában a hisztogram van *termék\_kategória*. Kereszt-oszlop statisztikai adatainak kiszámítása *termék\_kategória* és *termék\_sub_c\ategory*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Mivel közötti összefüggés *termék\_kategória* és *termék\_sub\_kategória*, a többoszlopos stat akkor lehet hasznos, ha ezekben az oszlopokban elért egy időben.

### <a name="g-create-statistics-on-all-the-columns-in-a-table"></a>G. Egy táblázat összes oszlopa statisztikák létrehozása
Statisztika létrehozásának egyik módja van problémáinak CREATE statistics UTASÍTÁSHOZ parancsok a következő tábla létrehozása után.

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

### <a name="h-use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>H. Statisztika létrehozásához minden oszlop egy adatbázisban tárolt eljárás használata
Az SQL Data Warehouse az SQL Server nincs egyenértékű [sp_create_stats] [-] rendszerben tárolt eljárást. Ez a tárolt eljárás objektumot hoz létre egy oszlop statisztika minden oszlop, amely még nem rendelkezik statisztikai adatbázis.

Ez segítséget nyújt az adatbázis tervét az első lépései. Nyugodtan igazodjon igényeinek.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

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
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
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

Ezt az eljárást a táblázat összes oszlopa statisztikák létrehozása, egyszerűen hívja meg az eljárást.

```sql
prc_sqldw_create_stats;
```

## <a name="examples-update-statistics"></a>Példák: statisztika frissítése
A statisztikák frissítése, a következőket teheti:

1. Egy statisztikai objektum frissítése. Adja meg a frissíteni kívánt statisztika objektum neve.
2. Egy tábla összes statisztika objektumok frissítése. Adja meg a táblázat helyett egy adott statisztika objektum nevét.

### <a name="a-update-one-specific-statistics-object"></a>A. Egy adott statisztika objektum frissítése
A következő szintaxissal egy adott statisztika objektum frissítése:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Példa:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Adott statisztika objektumok frissítése, minimalizálhatja az idő és a statisztika kezeléséhez szükséges erőforrások. Ehhez szükséges néhány gondolat, azonban a legjobb statisztika objektumokat frissíteni.

### <a name="b-update-all-statistics-on-a-table"></a>B. Egy tábla összes statisztika frissítése
Ez azt jelenti, egy egyszerű módszer a táblán statisztika objektumok frissítése.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Példa:

```sql
UPDATE STATISTICS dbo.table1;
```

Jelen nyilatkozat is könnyen használható. Ne feledje azonban ez a táblázat összes statisztika frissíti, és ezért előfordulhat, hogy hajtsa végre a szükségesnél több munkát. Ha a teljesítmény darabolása nem okoz problémát, ez az egyértelműen a legegyszerűbb és leghatékonyabb módon statisztika naprakészek biztosításához.

> [!NOTE]
> Egy tábla összes statisztika frissítése, az SQL Data Warehouse minta a táblázat minden egyes statistics megvizsgálja hajtja végre. Ha a tábla túl nagy, sok oszlopot, és sok statisztika, előfordulhat, hogy hatékonyabb, ha egyéni igények alapján statisztika frissítése.
> 
> 

Egy végrehajtásához egy `UPDATE STATISTICS` eljárás tekintse meg a [az ideiglenes táblák] [ Temporary] cikk. A végrehajtási módszer kis mértékben eltér, a a `CREATE STATISTICS` a fenti eljárás, de a végeredménynek megegyezik.

Tekintse meg a teljes szintaxissal [Update Statistics] [ Update Statistics] az MSDN Webhelyén.

## <a name="statistics-metadata"></a>Statisztika metaadatok
Több rendszernézet és függvények, amelyek segítségével található információ a statisztikákat is van. Például láthatja, ha egy statisztika objektum elavult lehet a statisztikák-date függvény használatával statisztika volt utoljára létrehozásakor vagy frissítésekor.

### <a name="catalog-views-for-statistics"></a>A statisztika katalógusnézetekre
Ezek a rendszer nézetek statisztika ismertetik:

| Katalógusnézet használatával derítheti ki | Leírás |
|:--- |:--- |
| [sys.Columns][sys.columns] |Egy sor minden egyes oszlophoz. |
| [sys.Objects][sys.objects] |Egy sor minden objektum az adatbázisban. |
| [sys.schemas][sys.schemas] |Egy sor minden séma az adatbázisban. |
| [sys.stats][sys.stats] |Egy sor minden egyes statisztika objektumhoz. |
| [sys.stats_columns][sys.stats_columns] |A statisztika objektum minden egyes oszlopának egy sort. Vissza a sys.columns mutató hivatkozásokat tartalmaz. |
| [sys.Tables][sys.tables] |Egy sor minden táblához (külső táblát tartalmazza). |
| [sys.table_types][sys.table_types] |Egy sor egyes adattípusok esetében. |

### <a name="system-functions-for-statistics"></a>A statisztika rendszer funkciók
A rendszer függvények hasznosak statisztika használata:

| Rendszer-funkció | Leírás |
|:--- |:--- |
| [STATS_DATE][STATS_DATE] |A statisztika objektum utolsó módosításának dátuma. |
| [DBCC SHOW_STATISTICS][DBCC SHOW_STATISTICS] |Értékek terjesztési összefoglaló szintű és részletes információkat biztosít a statisztika objektum azt értelmezni tudja módon. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Statisztika oszlopok és funkciók egyesítése egy nézet
Ez a nézet számos lehetőséget kínál a statisztika kapcsolódó oszlopok, és a [STATS_DATE()] [] függvény együtt annak az eredménye.

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
DBCC SHOW_STATISTICS() statisztika objektumon belül tárolt adatainak megjelenítése. Ezek az adatok származnak három részből áll.

1. Fejléc
2. Sűrűség vektoros
3. Hisztogram

A statisztika fejléc metaadatait. A hisztogram statisztika objektum első kulcsoszlop értékeinek eloszlását jeleníti meg. A sűrűség vektor kereszt-oszlop korrelációs méri. SQLDW kiszámítja a statisztika objektumban adatokat a számosság becsléseket.

### <a name="show-header-density-and-histogram"></a>Fejléc, sűrűség és hisztogram megjelenítése
Az egyszerű példában statisztika objektum összes három részből.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Példa:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>DBCC SHOW_STATISTICS(); egy vagy több részei megjelenítése
Ha érdekli csak megtekintés részét, használja a `WITH` záradék, és adja meg a megjeleníteni kívánt részeket:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Példa:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>DBCC SHOW_STATISTICS() különbségek
DBCC SHOW_STATISTICS() szigorúbban végrehajtani az SQL Data Warehouse az SQL Server képest.

1. Nem dokumentált funkciók nem támogatottak.
2. Nem használható a Stats_stream
3. Nem tudja csatlakoztatni meghatározott fájlcsoportokat statisztikai adatok eredményeit pl. (STAT_HEADER ILLESZTÉSI DENSITY_VECTOR)
4. Üzenet tiltási NO_INFOMSGS nem állítható be
5. Statisztika neveket burkoló szögletes zárójelek között nem használható.
6. Nem használható oszlopnevek statisztika objektumok azonosítása
7. Egyéni hiba 2767 nem támogatott

## <a name="next-steps"></a>Következő lépések
További részletekért lásd: [DBCC SHOW_STATISTICS] [ DBCC SHOW_STATISTICS] az MSDN Webhelyén.  További tudnivalókért tekintse meg a cikkek [tábla áttekintése][Overview], [tábla adattípusok][Data Types], [terjesztése egy tábla][Distribute], [tábla indexelő][Index], [tábla particionáló] [ Partition] és [az ideiglenes táblák][Temporary].  Gyakorlati tanácsok kapcsolatban bővebben lásd: [SQL Data Warehouse gyakorlati tanácsok][SQL Data Warehouse Best Practices].  

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
