---
title: "Az SQL Data Warehouse táblák indexelő |} A Microsoft Azure"
description: "Ismerkedés az Azure SQL Data Warehouse indexelő táblával."
services: sql-data-warehouse
documentationcenter: NA
author: shivaniguptamsft
manager: barbkess
editor: 
ms.assetid: 3e617674-7b62-43ab-9ca2-3f40c41d5a88
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 07/12/2016
ms.author: shigu;barbkess
ms.openlocfilehash: b205ed47833f675286539705e2754d2ea3821b8e
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>Az SQL Data Warehouse táblák indexelő
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

Az SQL Data Warehouse beleértve több indexelési lehetőséget kínál [fürtözött oszlopcentrikus indexek][clustered columnstore indexes], [fürtözött indexek és fürtözetlen indexeire] [ clustered indexes and nonclustered indexes].  Ezenkívül azt is lehetőséget is kínál a Nincs index is [halommemória][heap].  Ez a cikk minden Indextípus, valamint a tipp az első a legtöbb teljesítmény kívül az indexek előnyeit ismerteti. Lásd: [table szintaxis létrehozása] [ create table syntax] létrehoz egy táblát az SQL Data Warehouse kapcsolatos további részletekért.

## <a name="clustered-columnstore-indexes"></a>Fürtözött oszlopcentrikus indexek
Alapértelmezés szerint az SQL Data Warehouse akkor hozza létre fürtözött oszlopcentrikus index, amikor nincsenek index beállítások adhatók meg egy tábla. Fürtözött oszloptárindexű táblákat mind a legmagasabb szintű adattömörítést, valamint a legjobb átfogó lekérdezési teljesítményt nyújtanak.  Fürtözött oszloptárindexű táblákat általában fog outperform fürtözött index vagy halommemória táblát, és általában nagy táblák a legjobb választás.  Ezen okok miatt a fürtözött oszlopcentrikus a legjobb hely a indítása, ha nem biztos fiókadataiban mutatja be az index a táblához.  

Hozzon létre egy fürtözött oszlopcentrikus táblát, egyszerűen adja meg a FÜRTÖZÖTT OSZLOPCENTRIKUS INDEXET a WITH záradékkal, vagy a WITH záradékkal hagyja:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Van néhány olyan forgatókönyvet, ahol fürtözött oszloptárindex nem lehet jó választás:

* Oszloptárindexű táblákat támogatja a varchar(max), nvarchar(max) és varbinary(max).  Fontolja meg inkább halmot vagy fürtözött index.
* Lehet, hogy az átmeneti adatok kevésbé hatékony Oszloptárindexű táblákat.  Vegye figyelembe a halommemória és akár még az ideiglenes táblák.
* Kevesebb mint 100 millió sort foglalnak kis táblákban.  Vegye figyelembe a halommemória táblákat.

## <a name="heap-tables"></a>Halommemória táblák
Amikor ideiglenesen tárol adatokat az SQL Data Warehouse-ban, a halomtábla használata a teljes folyamatot felgyorsíthatja.  Ennek az az oka halommemória terhelések gyorsabb, mint a táblákhoz és egyes esetekben az azt követő olvasás teheti a gyorsítótárból.  Ha csak azért tölt be adatokat, hogy további átalakítások futtatása előtt előkészítse őket, sokkal gyorsabban töltheti a táblát egy halomtáblába, mint egy fürtözött oszlopcentrikus táblába. Ezenkívül az adatok betöltése a [ideiglenes tábla] [ Temporary] is betölti sokkal gyorsabb, mint az állandó tároló tábla betöltésekor.  

A kis keresési táblák, kevesebb mint 100 millió sort foglalnak, gyakran halommemória táblázatokkal logika.  Fürt oszloptárindexű táblákat optimális tömörítési eléréséhez, ha több mint 100 millió sort foglalnak megkezdéséhez.

Hozzon létre egy halommemóriában táblát, egyszerűen adja meg HALOMMEMÓRIA a WITH záradékban:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( HEAP );
```

## <a name="clustered-and-nonclustered-indexes"></a>Fürtözött és fürtözetlen indexeinek
Fürtözött indexek előfordulhat, hogy outperform fürtözött oszloptárindexű táblákat, ha egyetlen sort kell gyorsan kérhető.  A lekérdezések, ahol egyetlen vagy nagyon kevés sor keresési szükség a teljesítményt és rendkívül sebesség az fontolja meg a fürt index vagy a másodlagos fürtözetlen index.  A fürtözött indexszel hátránya, hogy csak olyan lekérdezéseket, amelyek magas szelektív szűrőt használja a fürtözött index oszlop ki előnyeit.  Egyéb oszlopok szűrő javítása érdekében fürtözetlen index más oszlopok is hozzáadhatók.  Azonban minden index táblázat felvett felveszi lemezterület és a feldolgozási idő terhelések.

Hozzon létre egy fürtözött index táblát, egyszerűen adja meg a FÜRTÖZÖTT INDEX a WITH záradékban:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Egy tábla egy nem fürtözött index hozzáadásához egyszerűen csak a következő szintaxissal:

```SQL
CREATE INDEX zipCodeIndex ON t1 (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Fürtözött oszlopcentrikus indexek optimalizálása
Fürtözött oszloptárindexű táblákat történő adatok vannak rendszerezve.  Magas szegmens minőségre fontos a táblán oszloptárindex optimális lekérdezési teljesítmény elérése érdekében.  Szegmens minőségi mérhető egy tömörített sor csoport sorainak száma.  Szegmens minőségi legoptimálisabb, ahol legalább 100K sorok tömörített soronkénti csoport és arra, hogy a teljesítmény, mint a kötegenkénti sorok száma sor megközelítést 1 048 576 sort, amely a legtöbb érték sorszám, amely egy sor csoport állhat.

Az alábbi nézet hozható létre és a rendszer a átlagos sorok soronkénti csoportban, és azonosíthatja a optimális fürt oszlopcentrikus indexekkel kiszámítására használt.  Az utolsó oszlopban az ebben a nézetben, az indexek újraépítésére vonatkozó kérés használható SQL-utasítást hoz létre.

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

Most, hogy létrehozta a nézetet, futtassa a lekérdezést kevesebb mint 100K sorok sorcsoportok rendelkező táblák azonosítására.  Érdemes lehet természetesen 100 KB-os küszöbértéket növekszik, ha a keresett további optimális szegmens minőségét. 

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Miután az adatait, és az eredményeket elemezni megkezdheti a lekérdezés futtatása. Ez a táblázat bemutatjuk a sor csoport elemzés keres.

| Oszlop | Ezek az adatok használata |
| --- | --- |
| [table_partition_count] |Ha a tábla particionálva van, majd előfordulhat, hogy a várt magasabb nyitott sorcsoport száma. Mindegyik partíció elosztása sikerült elméletileg lehet társítva nyitott sor csoport. Ez az elemzéshez kéttényezős. Kis tábla particionálva van sikerült lehet optimalizálni a particionálás teljesen eltávolítja, ez javíthatják a tömörítést. |
| [row_count_total] |A következő táblázatban a sorok teljes számát. Például használhatja ezt az értéket a tömörített állapotban sorok százalékát kiszámításához. |
| [row_count_per_distribution_MAX] |Ha minden sor egyenletesen ezt az értéket a cél kötegenkénti sorok száma terjesztési lenne. Hasonlítsa össze a compressed_rowgroup_count ezt az értéket. |
| [COMPRESSED_rowgroup_rows] |Oszlopcentrikus formátum a tábla sorainak száma. |
| [COMPRESSED_rowgroup_rows_AVG] |Ha a sorok átlagos száma jóval kisebb, mint egy sor csoport sorok maximális száma, majd érdemes a CTAS vagy ALTER INDEX REBUILD újra szeretnénk tömöríteni az adatok |
| [COMPRESSED_rowgroup_count] |Sorcsoportok oszlopcentrikus formátumban száma. Ez a szám a tábla viszonyítva rendkívül nagy, akkor azt jelzi, hogy az oszlopcentrikus sűrűség értéke alacsony. |
| [COMPRESSED_rowgroup_rows_DELETED] |Vannak logikailag törölt sorok oszlopcentrikus formátumban. Ha a szám viszonyítva tábla mérete nagy, fontolja meg újra létrehozni a partíció vagy az index újraépítése, fizikailag ezzel eltávolítja azokat. |
| [COMPRESSED_rowgroup_rows_MIN] |Ezzel együtt az átlagos és maximális oszlopokat tudni, hogy a sor csoportokat az oszlopcentrikus értékek tartományán. A betöltési küszöb (102,400 igazítva partíció eloszlása) keresztül kevés javasol, hogy az adatok betöltését rendelkezésre állnak-e optimalizálás. |
| [COMPRESSED_rowgroup_rows_MAX] |Mivel a fenti |
| [OPEN_rowgroup_count] |Nyissa meg sorcsoportok nem rendellenes. Egy tábla eloszlása (60) megnyitott sorcsoport ésszerűen teheti. Túl sok számok között partíciók Adatbetöltési javaslat. Győződjön meg arról, hogy a jó particionálási stratégia ellenőrizze hang. |
| [OPEN_rowgroup_rows] |Minden egyes sorára csoport lehetnek 1 048 576 sort azt a maximum. Használja ezt az értéket, hogy a nyitott sorcsoportok van jelenleg |
| [OPEN_rowgroup_rows_MIN] |A csoportok menü megnyitása jelezheti adatok betöltése a táblába trickle vagy, hogy az előző terhelés kiömlött sorok fennmaradó a sor csoportba. A minimum, MAXIMUM használja, AVG oszlopban láthatja, hogy mennyi adatot van állt, nyissa meg a sor csoportok. Kis táblákra vonatkozó összes adat 100 %-os lehet! Ebben az esetben az ALTER INDEX REBUILD adat titkosításának kikényszerítése az oszloptárindexet. |
| [OPEN_rowgroup_rows_MAX] |Mivel a fenti |
| [OPEN_rowgroup_rows_AVG] |Mivel a fenti |
| [CLOSED_rowgroup_rows] |Tekintse meg a lezárt sor csoport sorok megerősítést ellenőrzése. |
| [CLOSED_rowgroup_count] |A lezárt sorcsoportok száma nem lehet alacsony, ha bármelyik minden látható. Az ALTER INDEX használatával tömörített rowg roups lezárt sorcsoportok átalakítható... SZERVEZNI a parancsot. Ez azonban nincs szükség. Lezárt csoportokat automatikusan konvertálja oszlopcentrikus sorcsoportok a háttérben "rekord rekordáthelyezőnek" folyamat. |
| [CLOSED_rowgroup_rows_MIN] |Lezárt sorcsoportok kell rendelkeznie a nagyon nagy kitöltés értéket. Ha egy lezárt sorcsoport kitöltési mérték alacsony, majd az oszloptárindexet a további elemzésre szükség. |
| [CLOSED_rowgroup_rows_MAX] |Mivel a fenti |
| [CLOSED_rowgroup_rows_AVG] |Mivel a fenti |
| [Rebuild_Index_SQL] |SQL táblázat oszlopcentrikus index újraépítése |

## <a name="causes-of-poor-columnstore-index-quality"></a>A gyenge oszlopcentrikus index minőségi okok
Ha táblák gyenge szegmens minőségi azonosította, célszerű a legfelső szintű OK azonosítása érdekében.  Az alábbiakban a gyenge szegmens quaility egyéb gyakori oka:

1. Memóriaprobléma, ha készült el index
2. Nagy mennyiségű DML-műveletek
3. Kis vagy trickle terhelés műveletek
4. Túl sok partíciót

Ezek a tényezők okozhatják az optimális 1 millió sort foglalnak sor csoportonként kisebb jelentősen rendelkeznie oszlopcentrikus index.  Ugrás a különbözeti sorcsoport tömörített sor csoport helyett sorok is okozhat. 

### <a name="memory-pressure-when-index-was-built"></a>Memóriaprobléma, ha készült el index
A tömörített sor csoportonként sorok száma közvetlenül kapcsolódnak a sor- és a sor csoport feldolgozásához használható memória mennyisége szélességét.  Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  Ezért az ajánlott eljárás, ahhoz, hogy megkapja a munkamenetből, ezt a lehető legtöbb memória oszlopcentrikus index táblák elérésére ír.  Mivel a memória és feldolgozási, a jobb oldali memóriafoglalás útmutatást között függ az adatfájl egyes soraiban a tábla, a rendszeróra kiosztása DWU-mennyiség, és a párhuzamosság tárolóhelyek biztosíthat a munkamenetet, amely számára a tábla az adatok írásakor.  Ajánlott eljárásként javasoljuk, xlargerc DW300 használata vagy kevésbé largerc használata DW600 és mediumrc DW400 DW1000 használata vagy újabb.

### <a name="high-volume-of-dml-operations"></a>Nagy mennyiségű DML-műveletek
Frissítése és sorokat törölni a DML-műveletek nagy mennyiségű elégtelenség bevezetéséhez is az oszloptárindexet. Ez akkor különösen igaz olyan esetben, amikor a legtöbb, a sorokat egy sor csoport módosítva lett.

* Egy sor törlését a tömörített sorcsoport csak logikailag jelöli meg a sor törlése. A sor a tömörített sorcsoport marad mindaddig, amíg a partíció vagy tábla újraépítésekor.
* A sort beszúrni egy sort ad egy belső sortárindex táblát a különbözeti sorcsoport. A beszúrt sor nem alakul át oszlopcentrikus, amíg a különbözeti sorcsoport megtelt, és a lezártnak jelölt. Sorcsoportok be van zárva, ha eléri a maximális kapacitását 1 048 576 sort. 
* A logikai törlés és Beszúrás feldolgozása oszlopcentrikus formátumban sor frissítése. A beszúrt sor tárolhatók a különbözeti tárolóban.

Frissítés kötegelni és a beszúrási műveletek, mint a tömeges küszöbértéket igazított terjesztési közvetlenül az oszlopcentrikus formátum írandó partíciónként 102 400 sorok. Azonban ha az még akkor is, terjesztési, akkor kellene több mint 6.144 millió sort foglalnak el egy műveletet ehhez meg kell módosítani. Ha egy adott partíció sorok számának egyeztetve terjesztési nem kisebb, mint 102,400 a sorok halad át a különbözeti tárolóban, és nem marad, amíg elegendő sort beszúrni vagy módosítja, hogy a sor csoport zárja be, vagy az index újra lett építve.

### <a name="small-or-trickle-load-operations"></a>Kis vagy trickle terhelés műveletek
Kis tölt be, hogy az SQL Data Warehouse-folyamat is is ismertek, terhelések trickle. A rendszer éppen okozhatnak adatok közel állandó adatfolyam általában képviselik. Azonban mivel ezen az adatfolyamon már majdnem folyamatos azoknak a soroknak a kötet nincs különösen nagy. Gyakran adata jelentősen szükséges oszlopcentrikus formátum közvetlen terhelést a küszöbérték alatti.

Ezekben az esetekben célszerűbb gyakran az adatok először megnyílik az Azure blob Storage tárolóban, és hagyja, hogy betöltés előtt gyűlik össze. Ezzel a technikával gyakran nevezik *micro kötegelés*.

### <a name="too-many-partitions"></a>Túl sok partíciót
Figyelembe kell venni egy másik művelet a fürtözött oszloptárindexű táblákat a particionálás hatását.  Particionálás, mielőtt az SQL Data Warehouse már osztja fel az adatok 60 adatbázisok.  Az adatok további particionálás osztja.  Ha Ön az adatok particionálása, akkor érdemes figyelembe venni, hogy **minden** partíció kell rendelkeznie legalább 1 millió sort foglalnak is kihasználhatják a fürtözött oszlopcentrikus index.  Ha Ön a tábla particionálása 100 partíciókra, akkor a táblában kell rendelkeznie kell legalább a 6 egymilliárd sort is kihasználhatják a fürtözött oszlopcentrikus index (60 terjesztéseket * 100 partíciók * 1 millió sort foglalnak). Ha a 100 partíciós tábla nem rendelkezik a 6 egymilliárd sort, vagy csökkentse a partíciók számát, vagy érdemes inkább egy halommemóriában tábla.

Miután a táblák adatokkal lett betöltve, a következő lépések végrehajtásával azonosíthatja és optimális fürt oszlopcentrikus indexekkel rendelkező táblák újbóli létrehozása.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Szegmens minőségének javítására indexek újraépítése
### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>1. lépés: Azonosítására, vagy a jobb oldali erőforrásosztály használó felhasználó létrehozása
Egy gyors azonnal a szegmens minőségének javítása módja az index újraépítése.  A fenti nézetben által visszaküldött SQL visszaállítja az ALTER INDEX REBUILD utasítás, amely használható a indexek újraépítésére vonatkozó kérés.  Az index újraépítésekor ügyeljen, hogy elegendő memóriát a munkamenetbe, amely újra létrehozza az indexet.  Ehhez az szükséges, növelje meg az erőforrás-osztály egy olyan felhasználó, amely jogosult az ebben a táblázatban az ajánlott minimális az index újraépítése.  Az adatbázis tulajdonosa erőforrásosztály nem lehet módosítani, ezért ha a rendszer nem a felhasználó hozott létre, akkor először tegye.  Az általunk javasolt minimum xlargerc DW300 használata, vagy kevesebb, largerc használata DW600 és mediumrc DW400 DW1000 használata vagy újabb.

Alább példája további memória lefoglalása egy felhasználó számára a erőforrásosztály növelésével.  További információ az erőforrás osztályok és az új felhasználó létrehozása található a [egyidejűségi és munkaterhelés-kezelés] [ Concurrency] cikk.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>2. lépés: A fürtözött oszlopcentrikus indexek újraépítése magasabb erőforrás osztály felhasználóval
Jelentkezzen be a felhasználó nem 1. lépés (pl. LoadUser), amely most már nagyobb erőforrás osztályt használ, és az ALTER INDEX utasítás végrehajtása.  Győződjön meg arról, hogy a felhasználó rendelkezik-e az ALTER engedéllyel a táblákat, ahol az index újraépítése.  Ezek a példák azt szemléltetik, a teljes oszlopcentrikus index újraépítése, illetve egy olyan partíciót, hogy. Nagy méretű táblákra is, hogy további gyakorlati egyetlen partícióra indexeli egyszerre.

Másik lehetőségként helyett az index újraépítése, átmásolhatja a tábla egy új tábla használatával [CTAS][CTAS].  Mely legjobb módja? A nagy mennyiségű adatot [CTAS] [ CTAS] általában gyorsabb, mint [ALTER INDEX][ALTER INDEX]. A kisebb mennyiségű adatot [ALTER INDEX] [ ALTER INDEX] egyszerűbb, és cserélje ki a tábla nem szükséges.  Lásd: **CTAS és a partíció váltás indexek újraépítése** alatt CTAS az indexek újraépítése kapcsolatos további részletekért.

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

Az SQL Data Warehouse az index újraépítése során offline állapotú.  Indexek újraépítésével kapcsolatos további információkért lásd: az ALTER INDEX REBUILD szakasz [Oszlopcentrikus index töredezettségmentesítési] [ Columnstore Indexes Defragmentation] és a szintaxis témakör [ALTER INDEX] [ ALTER INDEX].

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>3. lépés: Ellenőrizze, hogy fürtözött oszlopcentrikus szegmens minőség javult
Futtassa újból a lekérdezést, mely azonosított tábla gyenge minőségi szegmentálni, és ellenőrizze szegmens minőségi javult.  Ha nem javította szegmens minőségének, lehet, hogy nagyon nagy-e a sorokat a táblában.  Az index újraépítésekor a magasabb erőforrásosztály vagy a DWU használata javasolt.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>A CTAS és a partíció váltás indexek újraépítése
Ez a példa [CTAS] [ CTAS] és egy tábla partíció újraépítése a partícióváltás. 

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

-- Step 2: Create a SWITCH out table
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
FROM    [dbo].[FactInternetSales]
WHERE   1=2 -- Note this table will be empty

-- Step 3: Switch OUT the data 
ALTER TABLE [dbo].[FactInternetSales] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales_20000101] PARTITION 2;

-- Step 4: Switch IN the rebuilt data
ALTER TABLE [dbo].[FactInternetSales_20000101_20010101] SWITCH PARTITION 2 TO  [dbo].[FactInternetSales] PARTITION 2;
```

Hozza létre újra a partíciók használatával kapcsolatos további részletekért `CTAS`, tekintse meg a [partíció] [ Partition] cikk.

## <a name="next-steps"></a>Következő lépések
További tudnivalókért tekintse meg a cikkek [tábla áttekintése][Overview], [tábla adattípusok][Data Types], [terjesztése egy tábla] [ Distribute], [Tábla particionáló][Partition], [fenntartása a tábla statisztikai adatainak] [ Statistics] és [Az ideiglenes táblák][Temporary].  Gyakorlati tanácsok kapcsolatos további információkért lásd: [SQL Data Warehouse gyakorlati tanácsok][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[Concurrency]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[ALTER INDEX]: https://msdn.microsoft.com/library/ms188388.aspx
[heap]: https://msdn.microsoft.com/library/hh213609.aspx
[clustered indexes and nonclustered indexes]: https://msdn.microsoft.com/library/ms190457.aspx
[create table syntax]: https://msdn.microsoft.com/library/mt203953.aspx
[Columnstore Indexes Defragmentation]: https://msdn.microsoft.com/library/dn935013.aspx#Anchor_1
[clustered columnstore indexes]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
