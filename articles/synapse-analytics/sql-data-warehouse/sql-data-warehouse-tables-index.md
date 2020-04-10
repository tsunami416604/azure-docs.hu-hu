---
title: Indexelő táblák
description: Javaslatok és példák a synapse SQL-készletben lévő táblák indexelése.
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
ms.openlocfilehash: 8cb4af8faccb68c455928c0d3c5405ef2d3e70df
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011021"
---
# <a name="indexing-tables-in-synapse-sql-pool"></a>Indexelési táblák a Synapse SQL-készletben

Javaslatok és példák a synapse SQL-készletben lévő táblák indexelése.

## <a name="index-types"></a>Indextípusok

A Synapse SQL-készlet számos indexelési lehetőséget kínál , beleértve [a fürtözött oszlopcentrikus indexeket](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [a fürtözött indexeket és a nem fürtözött indexeket](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), valamint a [halommemória](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)néven is ismert nem indexopciót .  

Indexet mutatóval rendelkező tábla létrehozásához tekintse meg a [TABLE (Synapse SQL pool) dokumentációját.](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="clustered-columnstore-indexes"></a>Fürtözött oszlopcentrikus indexek

Alapértelmezés szerint a Synapse SQL-készlet fürtözött oszlopcentrikus indexet hoz létre, ha egy táblában nincsenek indexbeállítások. A fürtözött oszlopcentrikus táblák a legmagasabb szintű adattömörítést és a legjobb általános lekérdezési teljesítményt is kínálják.  A fürtözött oszlopcentrikus táblák általában jobban teljesítenek, mint a fürtözött index- vagy halommemória-táblák, és általában a legjobb választás a nagy táblák számára.  Ezen okok miatt a fürtözött oszlopcentrikus a legjobb kiindulópont, ha nem biztos abban, hogyan indexelheti a táblázatot.  

Fürtözött oszlopcentrikus tábla létrehozásához egyszerűen adja meg a FÜRTÖZÖTT COLUMNSTORE INDEX ÉRTÉKET a WITH záradékban, vagy hagyja kikapcsolva a WITH záradékot:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Vannak néhány forgatókönyv, ahol a fürtözött oszlopcentrikus nem lehet jó lehetőség:

- Az oszlopcentrikus táblák nem támogatják a varchar(max), az nvarchar(max) és a varbinary(max) függvényt. Fontolja meg a halommemória vagy fürtözött index helyett.
- Az oszlopcentrikus táblák kevésbé hatékonyak lehetnek az átmeneti adatok esetében. Tekintsük halom, és talán még ideiglenes táblákat.
- Kis táblázatok kevesebb, mint 60 millió sor. Fontolja meg a halomasztalokat.

## <a name="heap-tables"></a>Halommemória-táblák

Ha ideiglenesen adatokat hoz le a Synapse SQL-készletben, előfordulhat, hogy egy halommemória-tábla használatával gyorsabbá teszi a teljes folyamatot. Ennek az az oka, hogy a halomba betöltődések gyorsabbak, mint a táblák indexelése, és bizonyos esetekben a következő olvasás a gyorsítótárból végezhető el.  Ha csak azért tölti be az adatokat, hogy több átalakítás futtatása előtt megrendezze az adatokat, a tábla halommemóriatáblába való betöltése sokkal gyorsabb, mint az adatok betöltése egy fürtözött oszlopcentrikus táblába. Ezenkívül az adatok [ideiglenes táblába](sql-data-warehouse-tables-temporary.md) való betöltése gyorsabban töltődik be, mint egy tábla állandó tárolóba töltése.  Az adatok betöltése után indexeket hozhat létre a táblában a gyorsabb lekérdezési teljesítmény érdekében.  

Fürt oszlopcentrikus táblák kezdenek optimális tömörítést, ha több mint 60 millió sor.  Kis méretű, 60 millió nál kisebb sorok esetén érdemes heap vagy fürtözött index használata a gyorsabb lekérdezési teljesítmény érdekében. 

Halommemória-tábla létrehozásához egyszerűen adja meg a HEAP értéket a WITH záradékban:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Csoportosított és nem fürtözött indexek

A fürtözött indexek jobban teljesítenek a fürtözött oszlopcentrikus tábláknál, ha egyetlen sort gyorsan be kell olvasni. Olyan lekérdezések esetén, ahol egy vagy nagyon kevés sorlekérdezésre van szükség a szélsőséges sebességű műveletekhez, fontolja meg egy fürtindexet vagy a nem fürtözött másodlagos indexet. A fürtözött index használatának hátránya, hogy csak azok a lekérdezések, amelyek hasznot húznak, azok, amelyek a fürtözött indexoszlopban rendkívül szelektív szűrőt használnak. A más oszlopok szűrésének javítása érdekében a nem fürtözött index más oszlopokhoz is hozzáadható. Azonban minden egyes index, amely hozzáadódik egy táblához, helyet és feldolgozási időt is hozzáad a betöltésekhez.

Fürtözött indextábla létrehozásához egyszerűen adja meg a FÜRTÖZÖTT INDEX ÉRTÉKET a WITH záradékban:

```SQL
CREATE TABLE myTable
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Ha nem fürtözött indexet szeretne hozzáadni egy táblához, használja az alábbi szintaxist:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Fürtözött oszlopcentrikus indexek optimalizálása

A fürtözött oszlopcentrikus táblák szegmensekbe vannak rendezve.  A magas szegmensminőség elengedhetetlen az oszlopcentrikus tábla optimális lekérdezési teljesítményének eléréséhez.  A szegmensek minősége a tömörített sorcsoport sorainak számával mérhető.  A szegmensminőség akkor a legoptimálisabb, ha tömörített sorcsoportonként legalább 100 ezer sor van, és a teljesítmény megtérül, mivel a sorok száma soronként 1 048 576 sorhoz közelít, ami a legtöbb sor, amelyet egy sorcsoport tartalmazhat.

Az alábbi nézet hozható létre, és a rendszeren használható az átlagos sorok soronkénti átlagának kiszámításához és az optimálistól elmaradó fürtoszlop-oszlopcentrikus indexek azonosításához.  A nézet utolsó oszlopa egy SQL utasítást hoz létre, amely az indexek újraépítésére használható.

```sql
CREATE VIEW dbo.vColumnstoreDensity
AS
SELECT
        GETDATE()                                                               AS [execution_date]
,       DB_Name()                                                               AS [database_name]
,       s.name                                                                  AS [schema_name]
,       t.name                                                                  AS [table_name]
,    COUNT(DISTINCT rg.[partition_number])                    AS [table_partition_count]
,       SUM(rg.[total_rows])                                                    AS [row_count_total]
,       SUM(rg.[total_rows])/COUNT(DISTINCT rg.[distribution_id])               AS [row_count_per_distribution_MAX]
,    CEILING    ((SUM(rg.[total_rows])*1.0/COUNT(DISTINCT rg.[distribution_id]))/1048576) AS [rowgroup_per_distribution_MAX]
,       SUM(CASE WHEN rg.[State] = 0 THEN 1                   ELSE 0    END)    AS [INVISIBLE_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE 0    END)    AS [INVISIBLE_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 0 THEN rg.[total_rows]     ELSE NULL END)    AS [INVISIBLE_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 1 THEN 1                   ELSE 0    END)    AS [OPEN_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE 0    END)    AS [OPEN_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 1 THEN rg.[total_rows]     ELSE NULL END)    AS [OPEN_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 2 THEN 1                   ELSE 0    END)    AS [CLOSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE 0    END)    AS [CLOSED_rowgroup_rows]
,       MIN(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 2 THEN rg.[total_rows]     ELSE NULL END)    AS [CLOSED_rowgroup_rows_AVG]
,       SUM(CASE WHEN rg.[State] = 3 THEN 1                   ELSE 0    END)    AS [COMPRESSED_rowgroup_count]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE 0    END)    AS [COMPRESSED_rowgroup_rows]
,       SUM(CASE WHEN rg.[State] = 3 THEN rg.[deleted_rows]   ELSE 0    END)    AS [COMPRESSED_rowgroup_rows_DELETED]
,       MIN(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MIN]
,       MAX(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_MAX]
,       AVG(CASE WHEN rg.[State] = 3 THEN rg.[total_rows]     ELSE NULL END)    AS [COMPRESSED_rowgroup_rows_AVG]
,       'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;'             AS [Rebuild_Index_SQL]
FROM    sys.[pdw_nodes_column_store_row_groups] rg
JOIN    sys.[pdw_nodes_tables] nt                   ON  rg.[object_id]          = nt.[object_id]
                                                    AND rg.[pdw_node_id]        = nt.[pdw_node_id]
                                                    AND rg.[distribution_id]    = nt.[distribution_id]
JOIN    sys.[pdw_table_mappings] mp                 ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[tables] t                              ON  mp.[object_id]          = t.[object_id]
JOIN    sys.[schemas] s                             ON t.[schema_id]            = s.[schema_id]
GROUP BY
        s.[name]
,       t.[name]
;
```

Most, hogy létrehozta a nézetet, futtassa ezt a lekérdezést a 100 K-nál kisebb sorokkal rendelkező táblák azonosításához. Természetesen érdemes megemelni a 100 K küszöbértéket, ha optimálisabb szegmensminőséget keres.

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Miután futtatta a lekérdezést, elkezdheti az adatokat és elemezni az eredményeket. Ez a táblázat bemutatja, hogy mit kell keresni a sorcsoport-elemzésben.

| Oszlop | Az adatok felhasználása |
| --- | --- |
| Ez az table_partition_count. |Ha a tábla particionált, akkor előfordulhat, hogy magasabb Open sorcsoport-számok at fog látni. A disztribúció minden partíciója elméletileg rendelkezhet egy nyitott sorcsoporttal. Ezt vegye be az elemzésébe. A particionálás teljes eltávolításával optimalizálható egy particionált kis tábla, mivel ez javítaná a tömörítést. |
| Ez az a row_count_total 100 000 |A táblázat teljes sorszáma. Ezzel az értékkel például kiszámíthatja a sorok százalékos arányát a tömörített állapotban. |
| [row_count_per_distribution_MAX] |Ha az összes sor egyenletesen van elosztva, ez az érték a sorok célszáma eloszlásonként. Hasonlítsa össze ezt az értéket a compressed_rowgroup_count. |
| Nem, nem COMPRESSED_rowgroup_rows, nem, nem, nem, nem |A táblázat oszlopcentrikus formátumában lévő sorok teljes száma. |
| Nem, nem, COMPRESSED_rowgroup_rows_AVG nem, nem, nem, nem |Ha a sorok átlagos száma jelentősen kisebb, mint egy sorcsoport sorainak maximális száma, akkor fontolja meg a CTAS vagy az ALTER INDEX REBUILD használatát az adatok újratömörítéséhez. |
| Ez nem az COMPRESSED_rowgroup_count |Sorcsoportok száma oszlopcentrikus formátumban. Ha ez a szám nagyon magas a táblához képest, az azt jelzi, hogy az oszlopcentrikus sűrűsége alacsony. |
| Ez az COMPRESSED_rowgroup_rows_DELETED. |A sorok logikailag oszlopcentrikus formátumban törlődnek. Ha a szám a tábla méretéhez képest magas, fontolja meg a partíció újbóli létrehozását vagy az index újraépítését, mivel ez fizikailag eltávolítja őket. |
| [COMPRESSED_rowgroup_rows_MIN] |Ezt az AVG és a MAX oszlopokkal együtt használva megismerheti az oszlopcentrikus sorcsoportok értéktartományát. A terhelési küszöbértéket (partíciónként 102 400- en) túljutó alacsony szám azt sugallja, hogy az adatterheléssorán optimalizálások állnak rendelkezésre |
| Nem, nem COMPRESSED_rowgroup_rows_MAX, nem, nem, nem, nem |Mint fent |
| Ez az OPEN_rowgroup_count |A nyitott sorcsoportok normálisak. Az ember ésszerűen elvárná, hogy egy OPEN sorcsoport táblaeloszlásonként (60) egy sorcsoport. A túlzott számok adatbetöltést javasolnak a partíciók között. Ellenőrizze a particionálási stratégiát, hogy megbizonyosodjon arról, hogy egészséges |
| Ez az OPEN_rowgroup_rows. |Minden sorcsoportban legfeljebb 1 048 576 sor lehet. Ezzel az értékkel láthatja, hogy a nyitott sorcsoportok jelenleg mennyire vannak teljesek. |
| Nem, nem OPEN_rowgroup_rows_MIN, nem, nem, nem, nem |A nyitott csoportok azt jelzik, hogy az adatok vagy a táblába kerülnek, vagy az előző terhelés a fennmaradó sorokra ömlött ebbe a sorcsoportba. A MIN, MAX, AVG oszlopokkal megtekintheti, hogy mennyi adat van az OPEN sorcsoportokban. A kis táblák lehet 100%-a az összes adatot! Ebben az esetben az ALTER INDEX REBUILD az adatok oszlopcentrikusra kényszerítéséhez. |
| Nem, nem, OPEN_rowgroup_rows_MAX nem, nem, nem, nem |Mint fent |
| Ez az OPEN_rowgroup_rows_AVG. |Mint fent |
| Ez az a CLOSED_rowgroup_rows két év, amikor az emberek meg: |Nézd meg a zárt sor csoport sorok, mint a józan ész ellenőrzése. |
| Nem, nem, nem CLOSED_rowgroup_count, nem, nem, nem |A zárt sorcsoportok számának alacsonynak kell lennie, ha egyáltalán látható. A zárt sorcsoportok tömörített sorcsoportokká alakíthatók az ALTER INDEX ... Átszervezés parancs. Ez azonban általában nem szükséges. A zárt csoportok automatikusan oszlopcentrikus sorcsoportokká alakulnak át a háttérben lévő "tuple mover" folyamat tal. |
| Ez nem az CLOSED_rowgroup_rows_MIN |A zárt sorcsoportoknak nagyon magas töltési sebességgel kell rendelkezniük. Ha egy zárt sorcsoport kitöltési sebessége alacsony, akkor az oszlopcentrikus további elemzése szükséges. |
| [CLOSED_rowgroup_rows_MAX] |Mint fent |
| Ez nem az én így CLOSED_rowgroup_rows_AVG. |Mint fent |
| Nem, nem Rebuild_Index_SQL, nem, nem, nem, nem |SQL a tábla oszlopcentrikus indexének újraépítéséhez |

## <a name="causes-of-poor-columnstore-index-quality"></a>az oszlopcentrikus indexek gyenge minőségének okait

Ha gyenge szegmensminőségű táblákat azonosított, meg szeretné határozni a kiváltó okot.  Az alábbiakban felsorolunk néhány más gyakori oka a rossz szegmens minőség:

1. Memórianyomás az index létrehozásakor
2. Nagy mennyiségű DML-művelet
3. Kis vagy csepegtető terhelési műveletek
4. Túl sok partíció

Ezek a tényezők azt eredményezhetik, hogy az oszlopcentrikus index jelentősen kevesebb, mint az optimális 1 millió sor soronként csoportonként. Azt is okozhatják, hogy a sorok a különbözeti sorcsoportba kerüljenek a tömörített sorcsoport helyett.

### <a name="memory-pressure-when-index-was-built"></a>Memórianyomás az index létrehozásakor

A sorok száma tömörített sorcsoportonként közvetlenül kapcsolódik a sor szélességéhez és a sorcsoport feldolgozásához rendelkezésre álló memória mennyiségéhez.  Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  Ezért az ajánlott eljárás az, hogy a munkamenet, amely írásban az oszlopcentrikus index táblák hozzáférést a lehető legtöbb memóriát.  Mivel a memória és az egyidejűség között kompromisszum van, a megfelelő memóriafoglalásra vonatkozó útmutatás a tábla egyes sorának adataitól, a rendszerszámára lefoglalt adattárház egységektől, valamint a munkamenetnek, azaz a táblába író munkamenetnek adhatja az egyidejűségi tárolóhelyek számát.

### <a name="high-volume-of-dml-operations"></a>Nagy mennyiségű DML-művelet

A sorokat frissítő és törlő nagy mennyiségű DML-műveletek eredménytelenséget okozhatnak az oszloptárban. Ez különösen akkor igaz, ha egy sorcsoport sorainak többsége módosul.

- Ha egy sor törlése egy tömörített sorcsoportból csak logikailag jelöli meg a sort töröltként. A sor a tömörített sorcsoportban marad, amíg a partíció vagy a tábla újra nem épül.
- Egy sor beszúrása hozzáad egy delta sorcsoportnak nevezett belső sortártáblához. A beszúrt sor nem konvertálódik oszloptárrá, amíg a különbözeti sorcsoport meg nem telt, és zártként van megjelölve. A sorcsoportok akkor záródnak le, ha elérik az 1 048 576 sor maximális kapacitását.
- Az oszlopcentrikus formátumban lévő sorok frissítése logikai törlésként, majd beszúrásként kerül feldolgozásra. A beszúrt sor tárolható a delta tárolóban.

A partícióra igazított terjesztésenként 102 400 sorból álló tömeges küszöbértéket meghaladó kötegelt frissítési és beszúrási műveletek közvetlenül az oszlopcentrikus formátumba kerülnek. Azonban, feltételezve, hogy egy egyenletes eloszlás, akkor módosítania kell több mint 6,144 millió sort egyetlen műveletben, hogy ez megtörténjen. Ha egy adott partícióhoz igazított disztribúció sorainak száma kevesebb, mint 102 400, akkor a sorok a különbözeti tárolóba kerülnek, és ott maradnak, amíg elegendő sort be nem szúrnak vagy nem módosítanak a sorcsoport bezárásához, vagy az indexet újra nem építették.

### <a name="small-or-trickle-load-operations"></a>Kis vagy csepegtető terhelési műveletek

A Synapse SQL-készletbe áramló kis terheléseket néha csepegtető terheléseknek is nevezik. Ezek általában a rendszer által betöltött adatok közel állandó adatfolyamát jelentik. Mivel azonban ez az adatfolyam közel folyamatos, a sorok mennyisége nem különösebben nagy. Gyakrabban, mint nem az adatok jelentősen a küszöbérték alatt szükséges közvetlen terhelés oszlopcentrikus formátumban.

Ezekben a helyzetekben gyakran jobb, ha az adatok először az Azure blob storage-ban, és hagyja, hogy a betöltés előtt felhalmozódnak. Ezt a technikát gyakran *mikrokötegelésnek nevezik.*

### <a name="too-many-partitions"></a>Túl sok partíció

Egy másik dolog, hogy fontolja meg a particionálás hatása a fürtözött oszlopcentrikus táblák.  Particionálás előtt a Synapse SQL-készlet már 60 adatbázisra osztja az adatokat.  A particionálás tovább osztja az adatokat.  Ha particionálja az adatokat, akkor vegye figyelembe, hogy **minden** partíciónak legalább 1 millió sorra van szüksége a fürtözött oszlopcentrikus index előnyeinek kihasználása érdekében.  Ha a tábla particionálása 100 partícióra, majd a tábla szüksége van legalább 6 milliárd sort, hogy részesüljenek a fürtözött oszlopcentrikus index (60 disztribúciók *100 partíciók* 1 millió sor). Ha a 100 partíciós tábla nem rendelkezik 6 milliárd sorral, vagy csökkentse a partíciók számát, vagy fontolja meg egy halommemória-tábla használata helyett.

Miután a táblák betöltöttnéhány adatot, kövesse az alábbi lépéseket, hogy azonosítsa és újraépítse a táblákat az optimálistól elmaradó fürtözött oszlopcentrikus indexek.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Indexek újraépítése a szegmensminőségének javítása érdekében

### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>1. lépés: Azonosítsa vagy hozza létre a megfelelő erőforrásosztályt használó felhasználót

A szegmensek minőségének azonnali javításának egyik gyors módja az index újraépítése.  A fenti nézet által visszaadott SQL egy ALTER INDEX REBUILD utasítást ad vissza, amely az indexek újraépítésére használható. Az indexek újraépítésekor győződjön meg arról, hogy elegendő memóriát foglal le az indexet újraépítő munkamenethez.  Ehhez növelje azon felhasználó erőforrásosztályát, akinek engedélye van a tábla indexének az ajánlott minimumra történő újraépítéséhez.

Az alábbi példa bemutatja, hogyan foglaljon le több memóriát a felhasználó számára az erőforrásosztály növelésével. Az erőforrásosztályok kezeléséhez olvassa el [az Erőforrásosztályok a munkaterhelés kezeléséhez.](resource-classes-for-workload-management.md)

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>2. lépés: A fürtözött oszlopcentrikus indexek újraépítése magasabb erőforrásosztály-felhasználóval

Jelentkezzen be felhasználóként az 1. Győződjön meg arról, hogy ez a felhasználó ALTER engedéllyel rendelkezik azokhoz a táblákhoz, amelyeken az index újraépül. Ezek a példák bemutatják, hogyan lehet újraépíteni a teljes oszlopcentrikus indexet, vagy hogyan lehet újraépíteni egy partíciót. Nagy táblákon célszerűbb egyszerre egyetlen partíciót újraépíteni.

Másik lehetőségként az index újraépítése helyett a táblázatot átmásolhatja egy új táblába a [CTAS használatával.](sql-data-warehouse-develop-ctas.md) Melyik út a legjobb? Nagy mennyiségű adat esetén a CTAS általában gyorsabb, mint az [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Kisebb adatmennyiségek esetén az ALTER INDEX használata egyszerűbb, és nem követeli meg a tábla cseréjét.

```sql
-- Rebuild the entire clustered index
ALTER INDEX ALL ON [dbo].[DimProduct] REBUILD
```

```sql
-- Rebuild a single partition
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5
```

```sql
-- Rebuild a single partition with archival compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE_ARCHIVE)
```

```sql
-- Rebuild a single partition with columnstore compression
ALTER INDEX ALL ON [dbo].[FactInternetSales] REBUILD Partition = 5 WITH (DATA_COMPRESSION = COLUMNSTORE)
```

Az index újraépítése a Synapse SQL-készletben egy offline művelet.  Az indexek újraépítéséről az [Oszlopcentrikus indexek töredezettségmentesítése](/sql/relational-databases/indexes/columnstore-indexes-defragmentation?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)és az [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)című témakör ALTER INDEX REBUILD című szakaszában talál további információt.

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>3. lépés: A fürtözött oszlopcentrikus szegmens minőségének javítása

Futtassa újra azt a lekérdezést, amely gyenge szegmensminőségű táblát azonosított, és ellenőrizze, hogy javult-e a szegmens minősége.  Ha a szegmens minősége nem javult, akkor lehet, hogy a táblázat sorai extra szélesek.  Fontolja meg egy magasabb erőforrásosztály vagy DWU az indexek újraépítésekor.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>Indexek újraépítése CTAS-szal és partícióváltással

Ez a példa a [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) utasítást és a partícióváltást használja a táblapartíció újraépítéséhez.

```sql
-- Step 1: Select the partition of data and write it out to a new table using CTAS
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
FROM    [dbo].[FactInternetSales]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

-- Step 2: Switch IN the rebuilt data with TRUNCATE_TARGET option
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2 WITH (TRUNCATE_TARGET = ON);
```

A partíciók CTAS használatával történő újralétrehozásáról a [Partíciók használata a Synapse SQL-készletben](sql-data-warehouse-tables-partition.md)című témakörben talál további részleteket.

## <a name="next-steps"></a>További lépések

A táblák fejlesztéséről a [Táblák fejlesztése](sql-data-warehouse-tables-overview.md)című témakörben talál további információt.
