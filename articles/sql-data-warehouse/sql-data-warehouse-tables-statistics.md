---
title: Bővítésével, frissítésével statisztikák – Azure SQL Data Warehouse |} A Microsoft Docs
description: Javaslatok és példák a lekérdezés-optimalizálási statisztikák azokon a táblákon, az Azure SQL Data Warehouse létrehozása és frissítése.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 05/09/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 1a7ea00e8bdf4fa1a22dd765e5108dce72e2d380
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2018
ms.locfileid: "43307462"
---
# <a name="creating-updating-statistics-on-tables-in-azure-sql-data-warehouse"></a>Frissíti a statisztikákat a táblák az Azure SQL Data Warehouse létrehozása
Javaslatok és példák a lekérdezés-optimalizálási statisztikák azokon a táblákon, az Azure SQL Data Warehouse létrehozása és frissítése.

## <a name="why-use-statistics"></a>Statisztika miért érdemes használni?
Annál jobban ismeri az Azure SQL Data Warehouse az adatokat, annál gyorsabban hajtsa végre a lekérdezéseket, szemben. Statisztikák gyűjtése az adatokon, és ezután betöltése az SQL Data Warehouse-bA az egyik a legfontosabb műveletet is végezhet a lekérdezések optimalizálásához. Ez azért, mert az SQL Data Warehouse lekérdezésoptimalizáló egy költségalapú optimalizáló. Összehasonlítja különböző lekérdezéstervek költségeit, majd kiválasztja a legalacsonyabb költségű tervet, amely általában a leggyorsabban végrehajtható is egyben. Például az optimalizáló becslése, hogy a dátum, a lekérdezés szűrést végez egy sort ad vissza, ha azt is választhatja, egy másik csomagot, ha azt a választott dátum becslések, amelyek 1 millió sort adja vissza.

## <a name="automatic-creation-of-statistics"></a>A statisztikák automatikus létrehozását
Az automatikus létrehozásakor statisztikakapcsoló a AUTO_CREATE_STATISTICS, az SQL Data Warehouse elemzi a bejövő, ahol egyoszlopos statisztikát jönnek létre oszlopok hiányoznak a statisztika, amely a felhasználói lekérdezések. A lekérdezésoptimalizáló statisztikát hoz létre egyéni oszlopokat a lekérdezés predikátumon vagy illesztési feltétel a lekérdezésterv Számosság becslései javítása érdekében. A statisztikák automatikus létrehozását jelenleg úgy van kapcsolva, alapértelmezés szerint.

Ha az adattárház rendelkezik, ez nincs konfigurálva a következő parancs futtatásával ellenőrizheti:

```sql
SELECT name, is_auto_create_stats_on 
FROM sys.databases
```
Ha az adattárház nincs konfigurálva AUTO_CREATE_STATISTICS, javasoljuk, hogy a tulajdonság engedélyezésével a következő parancs futtatásával:

```sql
ALTER DATABASE <yourdatawarehousename> 
SET AUTO_CREATE_STATISTICS ON
```
A következő utasításokat aktiválják a statisztikák automatikus létrehozását: válassza ki, INSERT-válassza ki, a CTAS, frissítési, törlési és magyarázat amikor illesztést tartalmazó vagy egy predikátum jelenlétét észleli. 

> [!NOTE]
> Ideiglenes vagy külső táblákon nem jönnek létre a statisztikák automatikus létrehozását.
> 

A statisztikák automatikus létrehozását akkor jön létre szinkron módon történik, így egy kismértékű csökkenését lekérdezési teljesítmény is felszámítunk, ha az oszlopok még nem rendelkezik statisztikai jönnek létre számukra. Statisztikák létrehozása eltarthat néhány másodpercig egyetlen oszlop a tábla méretétől függően. Teljesítménycsökkenés, különösen a teljesítménytesztek teljesítmény, a mérés elkerülése érdekében győződjön meg stats hozták létre, először a javasolt számítási feladatok végrehajtása előtt a rendszer a profilkészítés.

> [!NOTE]
> A statisztikák létrehozása is naplózza a rendszer [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=aps-pdw-2016) egy másik felhasználói környezetben.
> 

Az automatikus statisztikákat hoz létre, amikor az űrlap tart: _WA_Sys_< oszlop azonosító 8 számjegyből álló hexadecimális érték > _ < 8 számjegy táblaazonosító hexadecimális >. Megtekintheti a már futó által létrehozott adatok a [DBCC SHOW_STATISTICS](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql?view=sql-server-2017) parancsot:

```sql
DBCC SHOW_STATISTICS (<tablename>, <targetname>)
```
Az első argumentum értéke táblát, amely tartalmazza a statisztikai adatok megjelenítéséhez. Ez a külső tábla nem lehet. A második argumentum értéke a célindex, statisztika vagy a statisztikai adatok megjelenítéséhez használandó oszlop neve.



## <a name="updating-statistics"></a>Statisztika frissítése

Egy ajánlott eljárás, a statisztikák frissítése a dátum oszlop minden nap új dátumok hozzáadásakor. Minden alkalommal új sorok betöltődnek az adattárházba, új terhelés dátumok vagy a tranzakció dátumok kerülnek. Ezek módosítása az adatok terjesztési, és győződjön meg arról, a statisztikai adatok elavultak. Ellentétben egy ország oszlopra egy ügyfél tábla statisztikai előfordulhat, hogy soha nem kell frissíteni, mivel értékek eloszlását általában nem változik. Feltéve, hogy a terjesztés az ügyfelek közötti állandó, új sort ad hozzá a tábla változat nem fogja módosítani az adatok terjesztési. Azonban ha egy új országból származó adatok beolvasása az adattárház csak tartalmaz egy országban, tárolása, több országban adatokat eredményez majd frissítenie statisztikák az ország oszlopra.

Frissítse a statisztikai adatokat ajánlások a következők:

|||
|-|-|
| **Statisztikák frissítések gyakorisága**  | Konzervatív: naponta <br></br> Miután betöltése vagy az adatok átalakítása |
| **Mintavételezés** |  Kevesebb mint 1 milliárd sorral, használja az alapértelmezett mintavételi (20 %-os) <br></br> A több mint 1 milliárd sorral 2: %-os különféle statisztikai jó |

Az első kérdéseket tehet fel, ha a lekérdezés még hibaelhárítási egyik, **"Naprakészek a statisztikák?"**

Ez a kérdés nem szerepel, amely által az adatok korát választ. Előfordulhat, hogy egy naprakész statisztika objektum régi, ha ott nem módosult jelentős a mögöttes adatok. Amikor a sorok számát jelentősen megváltozott, vagy az értékek az oszlopban, a elosztása jelentős változás *majd* ideje a statisztikák frissítése.

Mivel nincs dinamikus felügyeleti nézet határozza meg, ha a táblán belül adatok változtak-e, a legutóbbi alkalommal statisztika frissítése, a statisztikákat korát, hogy adja meg a része a képen látható.  A következő lekérdezés segítségével határozza meg az utolsó időpont, a statisztika frissítésének minden táblában.  

> [!NOTE]
> Ne feledje, hogy ha egy oszlop értékeit a elosztása jelentős változás, frissítenie kell statisztika frissítve, legutóbb függetlenül.  
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

**A dátum oszlop** az adatraktárban, például általában igényelnek gyakori statisztikai adatok frissítése. Minden alkalommal új sorok betöltődnek az adattárházba, új terhelés dátumok vagy a tranzakció dátumok kerülnek. Ezek módosítása az adatok terjesztési, és győződjön meg arról, a statisztikai adatok elavultak.  Ezzel szemben az ügyfél tábla nemek oszlop statisztikai előfordulhat, hogy soha nem kell frissíteni kell. Feltéve, hogy a terjesztés az ügyfelek közötti állandó, új sort ad hozzá a tábla változat nem fogja módosítani az adatok terjesztési. Azonban ha az adattárház csak egy nemek és a egy új követelményt eredményeket több nemek tartalmazza, majd frissíteni szeretné a nemek oszlop statisztikai értéket is.

További információkért lásd: általános útmutatást [statisztika](/sql/relational-databases/statistics/statistics).

## <a name="implementing-statistics-management"></a>Végrehajtási statisztika kezelése
Gyakran egy célszerű bővítése az Adatbetöltési folyamat győződjön meg arról, hogy a betöltés végén frissíti statisztikáit. Az adatok betöltése után a táblák leggyakrabban módosíthatja a méretét és/vagy az értékek azok elosztásának. Ezért ez a logikai hely egyes felügyeleti folyamatok megvalósításához.

A statisztikák frissítése a betöltési folyamat során a következő alapelvek áll rendelkezésre:

* Győződjön meg arról, hogy minden egyes betöltött táblának legalább egy statisztikai objektum frissítése. Ez frissíti a tábla méretét (sorok száma és Magánlapok száma) adatai a statisztikák frissítése során.
* Az ILLESZTÉSI, GROUP BY, ORDER BY vagy DISTINCT záradékban részt vevő oszlopokat összpontosítani.
* Fontolja meg a "kulcs növekvő" oszlopok frissítése például tranzakciós dátumok gyakrabban, mivel ezek az értékek nem szerepelnek a statisztikák hisztogram.
* Vegye figyelembe, hogy kevesebb statikus terjesztési oszlopok gyakran frissítése.
* Ne feledje, hogy minden egyes statisztika objektum frissül a feladatütemezés. Egyszerűen végrehajtási `UPDATE STATISTICS <TABLE_NAME>` nem mindig ideális megoldás, különösen a és statisztikai objektumok számtalan széles táblák esetében.

További információkért lásd: [Számosságbecslés](/sql/relational-databases/performance/cardinality-estimation-sql-server).

## <a name="examples-create-statistics"></a>Példák: Statisztikák létrehozása
Ezek a példák bemutatják, hogyan statisztikák létrehozása a különböző beállítások használata. A beállítások, használja az egyes oszlopok adatait, és hogyan oszlop lesz használható a lekérdezésekben jellemzői függ.

### <a name="create-single-column-statistics-with-default-options"></a>Hozzon létre egyoszlopos statisztikát alapértelmezett beállításokkal
Statisztikák készítése az oszlop, egyszerűen adjon meg a statisztikák objektum nevét, és az oszlop neve.

Ez a szintaxis összes alapértelmezett beállítást használja. Alapértelmezés szerint az SQL Data Warehouse minták szerint **20 %-os** statisztikát hoz létre, ha a tábla.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Példa:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="create-single-column-statistics-by-examining-every-row"></a>Minden sor megvizsgálásával egyoszlopos statisztikát létrehozása
Az alapértelmezett mintavételi ráta 20 %-os kedvezménnyel is megfelel a legtöbb esetben. A mintavételi ráta azonban módosíthatja.

A teljes tábla mintát, használja ezt a szintaxist:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Példa:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="create-single-column-statistics-by-specifying-the-sample-size"></a>A mintanagyság megadásával egyoszlopos statisztikát létrehozása
Azt is megteheti adhatja meg a minta mérete százalékban:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="create-single-column-statistics-on-only-some-of-the-rows"></a>A sor csak egy egyoszlopos statisztikát létrehozása
A tábla a sorokat egy része a is létrehozhat statisztikákat. A szűrt statisztikai ezt nevezik.

Használhatja például szűrt statisztikákat, ha azt tervezi, hogy egy adott partíció egy nagy particionált tábla lekérdezése. Csak a partíció értékek statisztika létrehozásával a statisztikai adatok pontosságát továbbfejlesztjük, és így javíthatja a lekérdezések teljesítményét.

Ez a példa statisztikát hoz létre a kívánt értéktartományt. Az értékeket egy partíció értékek megfelelően egyszerűen lehet definiálni.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [!NOTE]
> A lekérdezésoptimalizáló érdemes megfontolnia a szűrt statisztikákat használatát, ha az elosztott lekérdezési terv úgy dönt, akkor az a lekérdezés el kell férnie a statisztikák objektum definíciója. Az előző példát, a lekérdezés ahol záradékban kell megadnia, col1 értékek 2000101 és 20001231 között.
> 
> 

### <a name="create-single-column-statistics-with-all-the-options"></a>Hozzon létre egyoszlopos statisztikát lehetőségekről
A beállítások együtt is kombinálhatók. Az alábbi példa egy szűrt statisztikákat objektumot hoz létre egy egyéni minta mérete:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

A teljes referenciáért lásd: [CREATE STATISTICS](/sql/t-sql/statements/create-statistics-transact-sql).

### <a name="create-multi-column-statistics"></a>Többoszlopos statisztika létrehozása
Több oszlop statisztikai adatainak objektum létrehozásához egyszerűen használja az előző példák, de további oszlopok megadása.

> [!NOTE]
> A hisztogram, a lekérdezés eredménye a sorok számának becslése szolgál, amely csak az első oszlop szerepel a statisztikák Objektumdefiníció érhető el.
> 
> 

Ebben a példában a hisztogram van *termék\_kategória*. Több oszlopos statisztikák kiszámításához az *termék\_kategória* és *termék\_sub_category*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Mivel nincs között *termék\_kategória* és *termék\_sub\_kategória*, egy több oszlopot statisztika objektum lehet hasznos, ha ezek oszlopok egyszerre érhetők el.

### <a name="create-statistics-on-all-columns-in-a-table"></a>Statisztikák készítése az egy táblázat összes oszlopa
Statisztika létrehozásának egyik módja, hogy a tábla létrehozása után adja ki a CREATE STATISTICS parancsokat:

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

### <a name="use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>Statisztikákat létrehozni minden oszlop egy adatbázisban tárolt eljárás használatával
Az SQL Data Warehouse az SQL Server nem rendelkezik egyenértékű sp_create_stats rendszerszintű tárolt eljárást. Ezt a tárolt eljárást az adatbázis, amely még nem rendelkezik statisztikai minden oszlop egy egyoszlopos statisztikát objektumot hoz létre.

Az alábbi példa segít az adatbázis-tervezésben használatának első lépései. Nyugodtan alkalmazkodni az igényeinek:

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

Statisztikák készítése az összes oszlopot a táblában az alapértelmezett értékekkel, egyszerűen hívja az eljárást.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 1, NULL;
```
Statisztikák készítése az összes oszlopot a táblában a fullscan használatával, hívja meg ezt az eljárást:

```sql
EXEC [dbo].[prc_sqldw_create_stats] 2, NULL;
```
A táblázat összes oszlopa a mintavételezett statisztikákat létrehozni, adja meg a 3, és a minta százalékos aránya.  Ezen eljárások egy 20 %-os mintavételi gyakoriság használ.

```sql
EXEC [dbo].[prc_sqldw_create_stats] 3, 20;
```


A mintavétel összes oszlopának statisztikáit létrehozása 

## <a name="examples-update-statistics"></a>Példák: Statisztika frissítése
A statisztikák frissítése, a következőket teheti:

- Egy statisztikai objektum módosítása. Adja meg a frissíteni kívánt statisztikát objektum neve.
- Egy tábla összes statisztika objektumok frissítése. Adja meg a táblázat helyett egy adott statisztika objektum nevét.

### <a name="update-one-specific-statistics-object"></a>Egy adott statisztika objektum frissítése
Egy adott statisztika objektum frissítéséhez használja a következő szintaxist:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Példa:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Adott statisztika objektumok frissítésével, minimálisra csökkentheti az időt és a statisztika kezeléséhez szükséges erőforrások. Ehhez úgy Gondoltuk, válassza ki a legjobb statisztika objektumokat frissíteni, néhányat.

### <a name="update-all-statistics-on-a-table"></a>Egy tábla összes statisztika frissítése
Ez bemutatja egy egyszerű módszer egy tábla összes statisztika objektumok frissítése:

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Példa:

```sql
UPDATE STATISTICS dbo.table1;
```

Ez az utasítás használata gyerekjáték. Ne feledje, hogy frissíti *összes* statisztika a táblán, és ezért előfordulhat, hogy végezze el a szükségesnél több munkát. Ha a teljesítmény darabolása nem okoz problémát, akkor a legegyszerűbb és leghatékonyabb módja garantálja, hogy a statisztikákat a naprakész.

> [!NOTE]
> Amikor frissíti egy tábla összes statisztika, az SQL Data Warehouse egy minta a táblázat minden egyes statisztika objektumhoz vizsgálattal hajtja végre. Ha a tábla nagy, és sok oszlop és sok statisztika, hatékonyabb, ha igényei alapján egyéni statisztikai adatainak frissítése lehet.
> 
> 

A megvalósítását egy `UPDATE STATISTICS` eljárás, lásd: [ideiglenes táblák](sql-data-warehouse-tables-temporary.md). A megvalósítás módja kissé eltér az előző `CREATE STATISTICS` eljárás, de az eredménye megegyezik.

A teljes szintaxisra, lásd: [Update Statistics](/sql/t-sql/statements/update-statistics-transact-sql).

## <a name="statistics-metadata"></a>Statisztika metaadatok
Több rendszer nézetek és függvények, amelyek segítségével statisztika további információt is van. Láthatja például, ha a statisztikák objektum lehet elavult a statisztikák – dátum függvény használatával megtekintheti a statisztikákat is utolsó létrehozásakor vagy frissítésekor.

### <a name="catalog-views-for-statistics"></a>Rendszerkatalógus-nézetek statisztikák
Ezek a nézetek rendszer statisztika kapcsolatos adatok megadása:

| Katalógus megtekintése | Leírás |
|:--- |:--- |
| [sys.columns](/sql/relational-databases/system-catalog-views/sys-columns-transact-sql) |Egy sor minden oszlophoz. |
| [sys.objects](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Egy sor minden egyes objektum az adatbázisban. |
| [sys.schemas](/sql/relational-databases/system-catalog-views/sys-objects-transact-sql) |Egy sor minden séma az adatbázisban. |
| [sys.stats](/sql/relational-databases/system-catalog-views/sys-stats-transact-sql) |Egy sor minden statisztikai objektum esetén. |
| [sys.stats_columns](/sql/relational-databases/system-catalog-views/sys-stats-columns-transact-sql) |Egy sor minden oszlophoz a statisztikák objektumban. Vissza a sys.columns mutató hivatkozásokat tartalmaz. |
| [sys.Tables](/sql/relational-databases/system-catalog-views/sys-tables-transact-sql) |Egy sor minden táblához (beleértve a külső táblák). |
| [sys.table_types](/sql/relational-databases/system-catalog-views/sys-table-types-transact-sql) |Az egyes adattípusokhoz egy sort. |

### <a name="system-functions-for-statistics"></a>Rendszer funkciók statisztikák
Ezek a függvények rendszer statisztikákkal kezelésénél hasznosak:

| Rendszer-függvény | Leírás |
|:--- |:--- |
| [STATS_DATE](/sql/t-sql/functions/stats-date-transact-sql) |A statisztika objektum utolsó módosításának dátuma. |
| [DBCC SHOW_STATISTICS](/sql/t-sql/database-console-commands/dbcc-show-statistics-transact-sql) |Szint, és részletes kapcsolatos összegző információkat, a statisztikai objektum által ismert értékek eloszlását. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Statisztika oszlopok és a functions egyesítésével egy nézet
Ebben a nézetben számos lehetőséget kínál a statisztikák kapcsolódik az oszlopok, és az eredmények közül a STATS_DATE() függvény együtt.

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
DBCC SHOW_STATISTICS() statisztika objektumon belül tárolt adatokat mutatja. Ezek az adatok három részből érhető el:

- Fejléc
- Sűrűségű vektor
- Hisztogram

A statisztika fejléc metaadatait. A hisztogram a statisztikák objektum első kulcsoszlopa értékek eloszlását jeleníti meg. A sűrűséget vektor több oszlopos korrelációs méri. Az SQL Data Warehouse a statisztika objektumban az adatokat a számosság becsléseket kiszámítja.

### <a name="show-header-density-and-histogram"></a>Fejléc, sűrűség és hisztogram megjelenítése
Ebben az egyszerű példában minden három részből statisztika objektum látható:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Példa:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Egy vagy több részeit DBCC SHOW_STATISTICS() megjelenítése
Ha csak adott részére szeretné megtekinteni, a `WITH` záradékot, és adja meg a megtekinteni kívánt részeket:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Példa:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>DBCC SHOW_STATISTICS() különbségek
DBCC SHOW_STATISTICS() szigorúbban van megvalósítva, az SQL Data Warehouse az SQL Server képest.

- Nem dokumentált funkciók nem támogatottak.
- Stats_stream nem használható.
- Statisztikai adatok bizonyos részhalmazainak eredményei nem tud csatlakozni. Ha például (STAT_HEADER CSATLAKOZZON DENSITY_VECTOR).
- NO_INFOMSGS üzenet figyelmen kívül hagyás nem állítható be.
- Csak akkor használható a statisztika nevét szögletes zárójelek.
- Oszlopnevek nem használhatja a statisztika objektumok azonosítására.
- Egyéni hiba 2767 nem támogatott.

## <a name="next-steps"></a>További lépések
A további lekérdezési teljesítmény javítása, lásd: [a számítási feladat monitorozása](sql-data-warehouse-manage-monitor.md)

