---
title: "Oszlopcentrikus index a teljesítmény - Azure SQL Data Warehouse javítása |} Microsoft Docs"
description: "Csökkentse a, vagy növelje a szabad memória a maximalizálása érdekében egy oszloptárindex tömöríti az egyes sorcsoport sorok száma."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: ef170f39-ae24-4b04-af76-53bb4c4d16d3
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: performance
ms.date: 10/23/2017
ms.author: barbkess
ms.openlocfilehash: 0476afb875616ed0758835aa52fb2334b63959a9
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Az oszlopcentrikus maximalizálva sorcsoport minősége

Sorcsoport minőségi egy sorcsoport sorainak száma határozza meg. Csökkentse a, vagy növelje a szabad memória a maximalizálása érdekében egy oszloptárindex tömöríti az egyes sorcsoport sorok száma.  Ezek a módszerek segítségével növelheti a tömörítési sebességet, és a lekérdezési teljesítmény az oszlopcentrikus indexek.

## <a name="why-the-rowgroup-size-matters"></a>A sorcsoport mérete fontossága
Mert oszlopcentrikus index táblázat egyedi rowgroups oszlop szegmenseinek beolvasásával ellenőrzi, maximalizálva minden sorcsoport sorainak száma fokozza a lekérdezések teljesítményét. Ha rowgroups nagy mennyiségű sort, adattömörítés javítja, ami azt jelenti, hogy kevesebb adat, olvassa el a lemezről.

Rowgroups kapcsolatos további információkért lásd: [Oszlopcentrikus indexek útmutató](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Rowgroups célméretet
A legjobb lekérdezési teljesítmény a célja a kötegenkénti sorok száma az oszlopcentrikus index sorcsoport maximalizálása érdekében. Egy sorcsoport legfeljebb 1 048 576 sort is rendelkezhetnek. Már rendelkezik egy sorcsoport sorok maximális számát. Oszloptárindexek a megfelelő teljesítmény elérése érdekében, ha rowgroups legalább 100 000 sort.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Rowgroups is beolvasása levágja a tömörítés során

A tömeges betöltés vagy oszlopcentrikus index rebuild, során néha nincs elég memória érhető el az egyes sorcsoport kijelölt összes sort tömörítése. Memóriaprobléma esetén oszlopcentrikus indexek sorcsoport mérete vághatja a, az oszloptárindexet a tömörítés sikeres lehet. 

Ha nincs elég memória a legalább 10 000 sorok tömörítése minden sorcsoport, az SQL Data Warehouse hibát generál.

További információ a tömeges betöltés: [fürtözött oszlopcentrikus index a tömeges betöltés](https://msdn.microsoft.com/en-us/library/dn935008.aspx#Bulk load into a clustered columnstore index).

## <a name="how-to-monitor-rowgroup-quality"></a>Sorcsoport minőségi figyelése

Nincs DMV (sys.dm_pdw_nodes_db_column_store_row_group_physical_stats), amely elérhetővé teszi a hasznos információk, például a rowgroups, és ha nem lett díszítésre levágási azért sorainak száma. A következő nézetet, hogy a lekérdezés sorcsoport levágási információt lekérni a DMV szerint hozhat létre.

```sql
create view dbo.vCS_rg_physical_stats
as 
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]                             
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]                                          
)
select *
from cte;
```

A trim_reason_desc azt ismerteti, hogy a sorcsoport lett rövidített (trim_reason_desc = NO_TRIM azt jelenti, nem tisztítás történt, és sorcsoport optimális minőségű). A következő vágással kapcsolatos okok miatt jelzi, hogy a sorcsoport korai karakterek:
- BULKLOAD: A vágás ezért használatos, ha a bejövő a betöltési-sorok kötegét kisebb, mint 1 millió sort foglalnak volt. A motor tömörített sorcsoportok hoz létre, ha nincsenek a nagyobb, mint 100 000 sort beszúrni (ellentétben a különbözeti tároló beszúrása), de a vágás OK BULKLOAD értékűre állítja be. Ebben a forgatókönyvben, érdemes emelni a kötegelt betöltés ablak gyűlik össze a további sorokat. Ezenkívül újra kiértékelje a particionálási sémát, és ellenőrizze, hogy nincs túl részletes, sorcsoportok partícióhatárok nem terjedhet ki.
- MEMORY_LIMITATION: 1 millió sort foglalnak sorcsoportok létrehozásához, egy bizonyos működő szükséges memória mérete van a motor. Amikor a rendelkezésre álló memória, a betöltés munkamenet nem éri el a szükséges munkát memóriával, sorcsoportok túl korán beolvasása levágja. Az alábbi szakaszok ismertetik, hogyan becsléséhez memóriára van szükség, és több memóriát lefoglalni.
- DICTIONARY_SIZE: Vágás ezért azt jelzi, hogy sorcsoport tisztítás történt, mert legalább egy karakterlánc-oszlopnak széles és/vagy nagy számosságot karakterláncok történt. A szótár mérete legfeljebb 16 MB memória, és ez a korlát elérésekor a sorcsoport tömörített. Ha ez a helyzet tapasztal, fontolja meg, a problematikus oszlop azoknak egy külön táblába.

## <a name="how-to-estimate-memory-requirements"></a>Hogyan memória követelményeinek becslése

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

A maximális szükséges memória egy sorcsoport tömörítendő körülbelül van

- 72 MB +
- \#sorok \* \#oszlopok \* 8 bájt +
- \#sorok \* \#rövid-karakterlánc-oszlopok \* 32 bájt +
- \#hosszú-karakterlánc-oszlopok \* tömörítési 16 MB

Ha rövid-karakterlánc-oszlopok használnak, a karakterláncos adattípusokkal < = 32 bájt és karakterláncos adattípusokkal hosszú-karakterlánc-oszlopok használata > 32 bájtban kifejezve.

Hosszú karakterláncok szöveges tömörítés készült tömörítési módszer tömörített. Ez a tömörítés módszer egy *szótár* szövegminták tárolásához. A szótár maximális mérete 16 MB. Nincs a sorcsoport hosszú karakterlánc oszlopainak csak egy szótár.

Részletes ismertető oszlopcentrikus memória követelmények, lásd: a videó [Azure SQL Data Warehouse skálázás: útmutató és konfigurációs](https://myignite.microsoft.com/videos/14822).

## <a name="ways-to-reduce-memory-requirements"></a>Csökkentse a módjai

A következő eljárások használatával rowgroups tömörítés az oszlopcentrikus indexek memóriakövetelményei csökkenthető.

### <a name="use-fewer-columns"></a>Oszlopok
Ha lehetséges tervezze meg kevesebb oszlopot tartalmazó tábla. Amikor egy sorcsoport az oszloptárindexet a tömörített, az oszloptárindexet külön-külön tömöríti minden oszlop szegmensben. Ezért a egy sorcsoport tömörítését memóriakövetelményeknek oszlopok növeli a számának növeléséhez.


### <a name="use-fewer-string-columns"></a>Oszlopok karakterlánc
String adattípusú oszlopok numerikus-nál több memóriát és dátum adattípus van szükség. Csökkentse a, fontolja meg a karakterlánc típusú oszlopokra eltávolítása a ténytáblák és abba a kisebb dimenziótáblák.

További memóriára vonatkozó követelményeknek karakterlánc tömörítési:

- Legfeljebb 32 karakter hosszúságú karakterlánc adattípusok 32 további bájt értéke lehet szükség.
- Karakterláncos adattípusokkal legfeljebb 32 karakter hosszú lehet a tömörített szótár módszerekkel.  A szótár létrehozásához minden egyes oszlopának a sorcsoport akár további 16 MB lehet szükség.

### <a name="avoid-over-partitioning"></a>Kerülje a túlzott particionálás

Oszlopcentrikus indexek létrehozása egy vagy több rowgroups partíciónként. Az SQL Data Warehouse a partíciók száma nő gyorsan, mert az adatok terjeszt, és minden egyes terjesztési particionálva van. Ha a tábla túl sok partíciókkal rendelkezik, a nem feltétlenül a rowgroups adatlehívást. Sorok hiánya nem hoz létre Memóriaterhelést tömörítés során azonban, hogy ne használjon a legjobb oszlopcentrikus lekérdezési teljesítmény rowgroups vezet.

Egy másik túlzott particionálási elkerülése érdekében szükség esetén a particionált tábla oszlopcentrikus index a sorok betöltése a memória. A betöltés során sok partíciót kap a bejövő sorait, amíg mindegyik partíció rendelkezik elég sorok tömöríthetők tartják a memóriában. További memória rendelkező túl sok partíciót hoz létre.

### <a name="simplify-the-load-query"></a>A betöltési lekérdezés egyszerűsítése érdekében

Az adatbázis között a lekérdezésben az operátorok lekérdezés memóriabeli ideiglenes megosztja. Ha egy terhelés-lekérdezés összetett típusú és illesztések rendelkezik, a memória a tömörítési csökken.

Tervezze meg a betöltési lekérdezés csak összpontosíthat betöltené a lekérdezést. Az adatok átalakítások futtatásához szükséges, ha futtatásukhoz külön, a Betöltés lekérdezésből. Például tesztelése a halommemória táblákban tárolt adatokat, az átalakítás futtatásához és az átmeneti tárolási tábla majd betölthető az oszlopcentrikus indexet. Először is az adatok betöltésére, és az MPP rendszer segítségével átalakíthatja az adatokat.

### <a name="adjust-maxdop"></a>MAXDOP beállítása

Minden terjesztési a rowgroups történő párhuzamos oszlopcentrikus tömöríti, ha egynél több Processzormagok eloszlása érhető el. A párhuzamos végrehajtás nagyobb mennyiségű memóriát használ, ami Memóriaterhelést és sorcsoport levágási igényel.

Memória csökkentése érdekében a MAXDOP lekérdezés mutató segítségével a load műveletet belül minden terjesztési soros módban való futásra kényszeríti.

```
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQUota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Több memóriát módjai

DWU mérete és a felhasználó erőforrásosztály együtt határozza meg, mennyi memória érhető el a felhasználó lekérdezése. A memóriabeli ideiglenes terhelés lekérdezés növeléséhez dwu-k számának növelése, vagy növelje a erőforrásosztály.

- A dwu-k növelése érdekében tekintse meg a [hogyan méretezhető teljesítmény?](sql-data-warehouse-manage-compute-overview.md#scale-compute)
- A lekérdezés erőforrásosztály módosításához lásd [módosíthatja a felhasználói erőforrás osztály példa](sql-data-warehouse-develop-concurrency.md#changing-user-resource-class-example).

Például a DWU 100 smallrc erőforrásosztály felhasználójának használhatja 100 MB memóriát minden egyes terjesztési. A részletekért lásd: [az SQL Data Warehouse párhuzamossági](sql-data-warehouse-develop-concurrency.md).

Tegyük fel, hogy van-e 700 MB memória kiváló minőségű sorcsoport méretek beolvasása. A példákból látható, hogyan futtathat a terhelés lekérdezés elegendő memóriával.

- A DWU 1000 és mediumrc, a memóriabeli ideiglenes 800 MB
- Használja a DWU 600 és largerc, a memóriabeli ideiglenes tárat, 800 MB.


## <a name="next-steps"></a>Következő lépések

Az SQL Data Warehouse teljesítmény javítása érdekében további részleteket talál a [teljesítményének áttekintését](sql-data-warehouse-overview-manage-user-queries.md).

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->
