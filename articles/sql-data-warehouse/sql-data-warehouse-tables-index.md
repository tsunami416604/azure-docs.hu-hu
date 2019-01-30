---
title: Az indexelés a táblák az Azure SQL Data Warehouse |} A Microsoft Azure
description: Javaslatok és a példák az indexelés a táblák az Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 2d57097e4d3317bfba5055a6b75ae72dd60f046a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55244691"
---
# <a name="indexing-tables-in-sql-data-warehouse"></a>Az SQL Data Warehouse az indexelő táblák
Javaslatok és a példák az indexelés a táblák az Azure SQL Data Warehouse.

## <a name="what-are-index-choices"></a>Mik azok az index lehetőségek?

SQL Data Warehouse biztosít több indexelési lehetőségeket, ideértve a [fürtözött oszlopcentrikus indexek](/sql/relational-databases/indexes/columnstore-indexes-overview), [fürtözött indexek és fürtözetlen index](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described), és a egy nem index lehetőséget is [halommemória ](/sql/relational-databases/indexes/heaps-tables-without-clustered-indexes).  

Hozzon létre egy táblát egy index, tekintse meg a [CREATE TABLE (Azure SQL Data Warehouse)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse) dokumentációját.

## <a name="clustered-columnstore-indexes"></a>Fürtözött oszloptár-indexek
Alapértelmezés szerint az SQL Data Warehouse nem indexbeállítások táblán megadásakor létrehoz egy fürtözött oszlopcentrikus index. Fürtözött oszlopcentrikus táblák a legmagasabb szintű az adattömörítés és is a legjobb általános lekérdezési teljesítményt nyújtanak.  Fürtözött oszlopcentrikus táblák általában teljesítményben felülmúlják az fürtözött index vagy halommemória táblát, és rendszerint a legjobb választás nagy táblák esetében.  Ebből kifolyólag a fürtözött oszlopcentrikus a legjobb hely a kezdődik, amikor biztos abban, hogy hogyan a tábla indexelése.  

Hozzon létre egy fürtözött oszlopcentrikus táblába, egyszerűen adja meg a WITH záradékkal FÜRTÖZÖTT OSZLOPCENTRIKUS INDEX, vagy hagyja meg a WITH záradékkal:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED COLUMNSTORE INDEX );
```

Van néhány olyan forgatókönyvek, ahol a fürtözött oszlopcentrikus nem jó választás:

- Az Oszlopcentrikus táblák nem támogatják a varchar(max), nvarchar(max) és varbinary(max). Érdemes lehet inkább halmot vagy fürtözött index.
- Lehet, hogy az Oszlopcentrikus táblák kevésbé hatékony, átmeneti adatokhoz. Fontolja meg a halommemória, és akár még az ideiglenes táblák.
- Kevesebb mint 100 millió sornál kisméretű táblák. Érdemes lehet halomtáblák.

## <a name="heap-tables"></a>Halomtáblák
Amikor ideiglenesen tárol adatokat az SQL Data warehouse-ba, előfordulhat, hogy halomtábla használata lehetővé teszi a teljes folyamatot felgyorsíthatja. Ennek az oka terhelések halommemória a rendszer gyorsabban táblákhoz és bizonyos esetekben az azt követő olvasás a gyorsítótárból teheti meg.  Ha csak az adatokat automatikusan, további átalakítások futtatása előtt tölt be, a halomtábla tábla betöltése, sokkal gyorsabb, mint egy fürtözött oszlopcentrikus táblába az adatok betöltése. Emellett az adatok betöltése egy [ideiglenes tábla](sql-data-warehouse-tables-temporary.md) betölti a gyorsabb, mint egy állandó tároló tábla betöltésekor.  

Kis keresési táblák, kevesebb mint 100 millió sorral, gyakran halomtáblák értelme.  Fürt oszlopcentrikus táblák elérése optimális tömörítés után több mint 100 millió sornál kezdődik.

Hozzon létre egy halomtábla, egyszerűen adja meg HALOMMEMÓRIA a WITH záradékkal:

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
Fürtözött indexek előfordulhat, hogy teljesítményben felülmúlják fürtözött oszlopcentrikus táblák, ha egyetlen sor gyorsan kérhető le. Fontolja meg a lekérdezések, ahol egyetlen vagy nagyon kevés sor keresési szükség, a rendkívüli gyorsasággal teljesítmény, a fürt index vagy másodlagos fürtözetlen index. A fürtözött indexszel hátránya, hogy csak azok a lekérdezések, amelyek azok, amelyekre magas szelektív szűrő használata a fürtözött index oszlopában. Szűrőt az egyéb oszlopok javítása érdekében egy fürtözetlen index más oszlopok is hozzáadhatók. Azonban adnak hozzá egy táblázat minden egyes index hozzáadása lemezterület és a feldolgozási idő terheléseket.

Hozzon létre egy fürtözött index táblát, egyszerűen adja meg a FÜRTÖZÖTT INDEX a WITH záradékkal:

```SQL
CREATE TABLE myTable   
  (  
    id int NOT NULL,  
    lastName varchar(20),  
    zipCode varchar(6)  
  )  
WITH ( CLUSTERED INDEX (id) );
```

Egy tábla egy nem fürtözött index hozzáadásához egyszerűen használja a következő szintaxist:

```SQL
CREATE INDEX zipCodeIndex ON myTable (zipCode);
```

## <a name="optimizing-clustered-columnstore-indexes"></a>Fürtözött oszlopcentrikus indexek optimalizálása
Fürtözött oszlopcentrikus táblák vannak szervezve adatokat a szegmensek.  A szegmens magas minőségű, kritikus fontosságú egy oszlopcentrikussal az optimális lekérdezési teljesítmény eléréséhez.  Szegmens minősége a tömörített sorcsoportonként sorok száma alapján mérhető.  Szegmens minősége optimális, ahol nincsenek legalább 100 ezer sorok száma tömörített sor csoportból, és ez a legtöbb sorok tartalmazhatnak sorcsoportokat 1 048 576 sort, a sorok száma sor megközelítést számának teljesítmény kapjanak.

Az alábbi nézet létrehozhatók és a rendszer minden egyes sorára vonatkozóan a átlagos sorok csoportból, és bármely az optimálisnál rosszabb fürtözött oszlopcentrikus indexekbe azonosítása kiszámításához használt.  Az utolsó oszlopban az ebben a nézetben hoz létre egy SQL-utasítást, amely az indexek újraépítése használható.

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

Most, hogy létrehozta a nézetet, futtassa a lekérdezést kevesebb mint 100 ezer sorok sorcsoportok rendelkező táblák azonosítására. Természetesen érdemes 100 KB-os küszöbértéket növekszik, ha további optimális szegmensminőségének keres. 

```sql
SELECT    *
FROM    [dbo].[vColumnstoreDensity]
WHERE    COMPRESSED_rowgroup_rows_AVG < 100000
        OR INVISIBLE_rowgroup_rows_AVG < 100000
```

Miután futtatta a lekérdezés megkezdéséhez tekintse meg az adatokat, és elemezze az eredményeket. Ez a táblázat azt ismerteti, milyen bejegyzésekre kell keresni a sor csoport elemzése.

| Oszlop | Ezek az adatok használata |
| --- | --- |
| [table_partition_count] |Ha a tábla particionálva van, majd előfordulhat, hogy látja a keresett magasabb nyílt sorcsoport száma. Mindegyik partíció terjesztési elméletileg lehet egy megnyitott sorcsoport társítva. Vegye ezt az elemzési be. Kis, amely rendelkezik particionált táblák sikerült lehet optimalizálni a particionálás érvényesítette eltávolításával, ez javíthatják a tömörítést. |
| [row_count_total] |A tábla teljes sorainak számát. Például használhatja ezt az értéket a tömörített állapotban lévő sorok százalékát kiszámításához. |
| [row_count_per_distribution_MAX] |Ha az összes sor egyenletesen oszlanak meg ezt az értéket a sorok száma terjesztési célszáma lenne. Hasonlítsa össze ezt az értéket a compressed_rowgroup_count. |
| [COMPRESSED_rowgroup_rows] |Oszloptár formátumba a tábla sorainak száma. |
| [COMPRESSED_rowgroup_rows_AVG] |Ha a sorok átlagos száma jóval kisebb, mint egy sor csoportot a sorok maximális száma, vegye fontolóra az CTAS vagy ALTER INDEX REBUILD Ha újra szeretnénk tömöríteni az adatok |
| [COMPRESSED_rowgroup_count] |Az oszloptár formátumba sorcsoportok száma. Ez a tábla viszonyítva rendkívül nagy szám esetén azt jelzi, hogy az oszlopcentrikus sűrűségű értéke alacsony. |
| [COMPRESSED_rowgroup_rows_DELETED] |Sorok logikailag törölnek oszloptár formátumba. Szolgáltatástáblázat mérete viszonyítva nagy szám esetén fontolja meg újból létrehozni a partíció vagy az index újraépítése, fizikailag ezzel eltávolítja őket. |
| [COMPRESSED_rowgroup_rows_MIN] |Ezzel együtt az átlagos és maximális oszlop értékeit a sorcsoportok az oszloptárindexet a típusairól. A betöltési küszöb (102,400 igazítva partíció terjesztési száma) alacsony Képszámok javasolja, hogy optimalizálási lehetőségek érhetők el az adatok betöltése |
| [COMPRESSED_rowgroup_rows_MAX] |Ahogy fent |
| [OPEN_rowgroup_count] |Nyitott sorcsoportok nem rendellenes. Az egyik ésszerűen elvárt / (60) a táblaelosztással nyílt sorcsoport. Túl sok javasolni Adatbetöltési partíciók között. Ellenőrizze, hogy a particionálási stratégia hang |
| [OPEN_rowgroup_rows] |Minden egyes sorcsoport lehetnek 1 048 576 sort, a maximum. Itt megtekintheti a teljes a nyitott sorcsoportok jelenleg ez az érték használatával |
| [OPEN_rowgroup_rows_MIN] |A csoportok menü megnyitása jelezheti adatok betöltése a táblába trickle, vagy az, hogy az előző terhelés kiömlött fennmaradó sorok sor ebbe a csoportba. A MIN, MAX használja, AVG oszlopban láthatja, hogy mennyi adatot van állt, nyissa meg a csoportok sor. A kis táblák 100 %-át az összes adatot lehet! Ebben az esetben az ALTER INDEX REBUILD oszlopcentrikus kényszerít ki az adatokat. |
| [OPEN_rowgroup_rows_MAX] |Ahogy fent |
| [OPEN_rowgroup_rows_AVG] |Ahogy fent |
| [CLOSED_rowgroup_rows] |Tekintse meg a lezárt sor sorok csoportosítása megerősítést ellenőrzése. |
| [CLOSED_rowgroup_count] |A lezárt sorcsoportok számát alacsony, ha bármelyik minden látható kell lennie. Az ALTER INDEX használatával tömörített sorcsoport lezárt sorcsoportok konvertálható... A parancs ÁTRENDEZÉSÉHEZ. Azonban ez nem kötelező általában. A lezárt csoportokat automatikusan konvertálja oszlopcentrikus sorcsoportok szerint a "rekord rekordáthelyezőnek" rétegzést. |
| [CLOSED_rowgroup_rows_MIN] |Lezárt sorcsoportok rendelkeznie kell egy nagyon nagy kitöltés sebessége. Ha bezárt sorcsoport kitöltés díjaival alacsony, majd az oszlopcentrikus további elemzésre szükség. |
| [CLOSED_rowgroup_rows_MAX] |Ahogy fent |
| [CLOSED_rowgroup_rows_AVG] |Ahogy fent |
| [Rebuild_Index_SQL] |SQL-tábla oszlopcentrikus index újraépítése |

## <a name="causes-of-poor-columnstore-index-quality"></a>Oszlopcentrikus indexek gyenge minőségének okait
Ha azonosította a táblák a gyenge szegmens minősége, érdemes gyökerének azonosítása.  Az alábbiakban néhány egyéb gyakori okai gyenge szegmens minősége:

1. Memóriaprobléma index elkészítésekor
2. Nagy mennyiségű DML-műveletek
3. Kis vagy trickle betöltési műveletek
4. Túl sok partíció

Ezek a tényezők okozhat az optimális 1 millió sor sor csoportonként kisebb szeretné, hogy jelentős mértékben oszlopcentrikus index. Nyissa meg a különbözeti sor csoportot helyett egy tömörített sorcsoport sorok is okozhat. 

### <a name="memory-pressure-when-index-was-built"></a>Memóriaprobléma index elkészítésekor
Sorok száma tömörített sorcsoport száma közvetlenül kapcsolódnak a sor- és memória rendelkezésre állnak a sorcsoport szélességét.  Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  Ezért az ajánlott eljárás, hogy adjon a munkamenetet, amely a lehető legtöbb memória oszlopcentrikus index táblák hozzáférést ír.  Mivel a memória- és egyidejűségi között, a megfelelő memóriafoglalást útmutatást függ, hogy az adatok a táblázat minden egyes sorára, a rendszer, és a párhuzamos időszeletek lefoglalt adattárházegységek segítségével biztosíthat a munkamenet amely Írja az adatokat a táblában.  Ajánlott eljárásként javasoljuk, hogy az xlargerc DW300 használatakor vagy kevesebb, largerc használatakor dw600 értékre és mediumrc DW400 használata dw1000 értékre, vagy újabb.

### <a name="high-volume-of-dml-operations"></a>Nagy mennyiségű DML-műveletek
Nagy mennyiségű, frissíthet vagy törölhet sorokat DML-műveletek az oszloptárba megjelentetni elégtelenségekkel. Ez különösen igaz a sorokat a sorcsoportonként többsége módosításakor.

- A sor logikailag csak egy sor törlése egy tömörített sorcsoport jelöli meg, mert törölték. A sor a tömörített sorcsoport marad mindaddig, amíg a partíció vagy a táblázatra újraépíti.
- Sor beszúrása a sor hozzáadása egy különbözeti sorcsoportonként nevű belső sortárindex táblát. A beszúrt sor át nem oszlopcentrikus mindaddig, amíg a különbözeti sorcsoport megtelt, és zárva van megjelölve. Sorcsoportok elérnék a maximális kapacitását 1 048 576 sort után bezárul. 
- Az oszloptár formátumba sor frissítése egy logikai törlés és Beszúrás feldolgozása. A beszúrt sor tárolhatók a különbözeti tárolójában.

Kötegelt frissítési és 102 400 sornál kisebb sorok száma illeszkednie terjesztési tömeges küszöbértéket meghaladó beszúrási műveletek lépjen közvetlenül az oszloptár formátumba. Azonban ha az egyenletes eloszlás, kell 6.144 millió sornál nagyobbak ehhez egyetlen művelettel lehet módosítani. Ha egy adott illeszkednie terjesztési sorok száma kevesebb mint 102,400 a sorok folytassa a hiba a különbözeti store andstay mindaddig, amíg elegendő sorok beszúrt vagy módosított gombra kattintva zárja be a sor csoportot, vagy az index újra lett építve.

### <a name="small-or-trickle-load-operations"></a>Kis vagy trickle betöltési műveletek
Kisméretű tölti be, hogy a folyamat az SQL Data Warehouse-ba is is ismertek, terhelések trickle. Folyamatban van a rendszer által betöltött adatok közel állandó adatfolyam általában képviselik. Azonban mivel a stream közel folyamatos sorok lemez nem különösen nagy. Gyakran az adatok a jelentősen oszloptár formátumba való közvetlen betöltés szükséges a küszöbérték alatti.

Ebben az esetben érdemes gyakran az adatok először megnyitja az Azure blob storage-ban, és biztosítani, hogy betöltés előtt összeadódhatnak. Ezzel a technikával gyakran nevezik *micro kötegelés*.

### <a name="too-many-partitions"></a>Túl sok partíció
Egy másik illesztője a fürtözött oszlopcentrikus Táblák particionálása hatását.  A particionálás, mielőtt az SQL Data Warehouse már osztja fel az adatok 60 adatbázisba.  További particionálás osztja fel az adatokat.  Ha particionálja az adatait, majd vegye figyelembe, hogy **egyes** partíciót kell legalább 1 millió sor számára, hogy a fürtözött oszlopcentrikus indexet.  Ha particionálhatja a táblát 100 partíciókra, akkor a táblában kell legalább 6 milliárd sorok számára, hogy a fürtözött oszlopcentrikus index (60 elosztás * 100 partíció * 1 millió sor). Ha a 100-partíciós tábla nem rendelkezik 6 milliárd sorral, vagy csökkentse a partíciók számát, vagy fontolja meg egy halomtábla használata esetén inkább.

Miután adatokkal lettek betöltve. a táblák, a lépések végrehajtásával azonosíthatja, és építse újra az optimálisnál rosszabb rendelkező táblák fürtözött oszloptár-indexekben.

## <a name="rebuilding-indexes-to-improve-segment-quality"></a>Szegmens minőségének javítására indexek újraépítése
### <a name="step-1-identify-or-create-user-which-uses-the-right-resource-class"></a>1. lépés: Azonosítsa, vagy hozzon létre felhasználói, amely használja a megfelelő erőforrás-osztály
Azonnal a szegmens minősége javításának egyik gyors módja, hogy az index újraépítése.  A fenti nézetben által visszaadott SQL egy ALTER INDEX REBUILD utasítás, amely az indexek újraépítése segítségével adja vissza. Ha az indexek újraépítése, mindenképpen lefoglalni elegendő memóriát a munkamenetbe, amely újraépíti az index.  Ehhez a erőforrásosztályhoz építenie az indexet a tábla a javasolt minimális engedéllyel rendelkező felhasználó növelése. Az adatbázis-tulajdonos felhasználó erőforrásosztály nem módosítható, így ha egy felhasználó a rendszeren nem hozott, Önnek kell először tegye. A minimális ajánlott erőforrásosztályhoz xlargerc DW300 használatakor, vagy kevesebb, largerc használatakor dw600 értékre és mediumrc DW400 használata dw1000 értékre, vagy újabb.

Alább egy példát egy felhasználóhoz több memóriát lefoglalni a erőforrásosztály növelésével van. Erőforrásosztályok dolgozni, lásd: [erőforrásosztályok számítási feladatok kezeléséhez](resource-classes-for-workload-management.md).

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

### <a name="step-2-rebuild-clustered-columnstore-indexes-with-higher-resource-class-user"></a>2. lépés: Magasabb szintű erőforrás osztály felhasználóval fürtözött oszlopcentrikus indexek újraépítése
Jelentkezzen be a felhasználó 1. lépésben (pl. LoadUser), amely mostantól egy nagyobb erőforrásosztály használata, és hajtsa végre az ALTER INDEX utasításokat. Győződjön meg arról, hogy a felhasználó rendelkezik-e az ALTER engedéllyel a táblákat, ahol az index újraépítése folyamatban van. Ezek a példák megjelenítése a teljes oszlopcentrikus index újraépítése, illetve egy adott partíció számára. A nagyméretű táblák további gyakorlati újraépítése indexeli ugyanazon a partíción egy időben.

Azt is megteheti, ahelyett, hogy az index újraépítése, sikerült másolni a tábla egy új táblát [CTAS használata](sql-data-warehouse-develop-ctas.md). Melyik legjobb módja? Nagy mennyiségű adatot a CTAS általában gyorsabb, mint a [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql). Kisebb mennyiségű adatot ALTER INDEX használata egyszerűbb, és nem szükséges, hogy a táblázat meg felcserélni. Lásd: **a CTAS és a partíció közötti váltás indexek újraépítése** alább a CTAS indexek újraépítése további részleteiért.

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

Az SQL Data Warehouse az index újraépítése az-offline művelet.  Az indexek újraépítésével kapcsolatos további információkért tekintse meg az ALTER INDEX REBUILD szakasz [Oszlopcentrikus index töredezettségmentesítési](/sql/relational-databases/indexes/columnstore-indexes-defragmentation), és [ALTER INDEX](/sql/t-sql/statements/alter-index-transact-sql).

### <a name="step-3-verify-clustered-columnstore-segment-quality-has-improved"></a>3. lépés: Ellenőrizze a fürtözött oszlopcentrikus szegmens minősége javult.
Futtassa újból a lekérdezést a gyenge azonosított tábla szegmens minősége, és ellenőrizze a szegmens minősége javult.  Ha szegmens minősége nem javította, annak oka az lehet, hogy a sorokat a táblában nagyon nagy.  Fontolja meg egy nagyobb erőforrásosztályhoz és DWU az index újraépítésekor.

## <a name="rebuilding-indexes-with-ctas-and-partition-switching"></a>A CTAS és a partíció közötti váltás indexek újraépítése
Ez a példa a [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) utasítás és a egy table-partition újraépítése a partícióváltás. 

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

Hozza létre újra a partíciók használatával a CTAS kapcsolatos további információkért lásd: [partíciók használatával az SQL Data Warehouse](sql-data-warehouse-tables-partition.md).

## <a name="next-steps"></a>További lépések
Táblák fejlesztésével kapcsolatos további információkért lásd: [fejlesztés táblák](sql-data-warehouse-tables-overview.md).

