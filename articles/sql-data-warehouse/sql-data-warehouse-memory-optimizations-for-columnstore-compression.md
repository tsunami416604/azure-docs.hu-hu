---
title: Az oszlopcentrikus index teljesítmény – Azure SQL Data Warehouse javítása |} A Microsoft Docs
description: Memóriakövetelményei csökkentse vagy növelje a szabad memória a maximalizálása érdekében az oszlopcentrikus index tömöríti az egyes sorcsoportokba sorok számát.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: de7cc0e67960edf95ace67808ffc677b57a46dab
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55096993"
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Oszlopcentrikus minőségű sorcsoportokba maximalizálása

Minőségű Sorcsoportokba egy sorcsoport sorainak száma határozza meg. Memóriakövetelményei csökkentse vagy növelje a szabad memória a maximalizálása érdekében az oszlopcentrikus index tömöríti az egyes sorcsoportokba sorok számát.  Ezek a módszerek használatával tömörítési sebesség növelése és a lekérdezési teljesítmény az oszlopcentrikus indexek.

## <a name="why-the-rowgroup-size-matters"></a>Miért fontos a sorcsoport mérete
Oszlopcentrikus index megvizsgálja a tábla egyes naplóbájtot oszlop szegmenseinek vizsgálatával, mivel az egyes sorcsoportokba sorainak száma jelentős javítja a lekérdezési teljesítmény. Ha naplóbájtot nagy mennyiségű sort, az adattömörítés javítja a ami azt jelenti, hogy kevesebb lemezről beolvasott adatok.

Naplóbájtot kapcsolatos további információkért lásd: [Oszlopcentrikus indexek áttekintésével](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Cél mérete naplóbájtot
A legjobb lekérdezési teljesítmény a célja a sorok sorcsoportonkénti az oszlopcentrikus index száma maximalizálása érdekében. Egy sorcsoport legfeljebb 1 048 576 sort is rendelkezhet. Már nem rendelkezik a sorok sorcsoportonkénti maximális számát. Az Oszlopcentrikus indexek jó teljesítményt érhet el, ha naplóbájtot legalább 100 000 sorral rendelkezik.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Naplóbájtot is első vágott a tömörítés során

Során egy tömeges betöltési vagy oszlopcentrikus index újraépítése néha nincs elég használható memória tömörítenie az egyes sorcsoportokba kijelölt összes sorát. Ha van rendelkezésre álló memória mennyisége, az oszlopcentrikus indexek az sorcsoport méretek trim, így az oszloptárba tömörítési sikeres. 

Nincs elegendő memória legalább 10 000 sort az egyes sorcsoportokba való tömörítéséhez, amikor az SQL Data Warehouse hibaüzenetet hoz létre.

További információ a tömeges betöltés: [egy fürtözött oszlopcentrikus indexbe a tömeges betöltés](https://msdn.microsoft.com/library/dn935008.aspx#Bulk load into a clustered columnstore index).

## <a name="how-to-monitor-rowgroup-quality"></a>Minőségű sorcsoportokba figyelése

Nincs DMV (sys.dm_pdw_nodes_db_column_store_row_group_physical_stats), amely elérhetővé teszi a hasznos információk, például sorainak naplóbájtot és a tisztítás, ha ott volt vágást okát. A következő nézet létrehozhatja úgy is sablonszolgáltatása segítségével kényelmesen sorcsoport levágási kapcsolatban információért a DMV lekérdezéséhez.

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

A trim_reason_desc arról tájékoztatja, hogy vágott lett-e a sorcsoport (trim_reason_desc = NO_TRIM nincs tisztítás történt, és sorcsoport optimális minőségű jelenti). A következő vágás okokból azt jelzik, hogy a sorcsoport idő előtti előtti:
- BULKLOAD: A vágás OK akkor használható, ha a terhelés bejövő sorköteg kevesebb mint 1 millió sorral rendelkezett. A motor tömörített sorcsoport hoz létre, ha vannak a nagyobb, mint 100 000 sort (ellentétben a különbözeti tároló beszúrása) beszúrt, de a vágás OK BULKLOAD állítja be. Ebben az esetben érdemes megfontolni az kötegelt betöltés ablakot gyűlik össze több sort alkalmazását. Emellett kiértékeli a particionálási sémát, ez a lehetőség nem túl részletes sorcsoportok nem terjedhetnek ki partícióhatárok biztosításához.
- MEMORY_LIMITATION: 1 millió sor sorcsoportok létrehozásához, egy bizonyos mennyiségű memóriát működő a motor által van szükség. Ha a betöltési munkamenet rendelkezésre álló memória kevesebb, mint a szükséges munkát memória, a lekérése túl korán vágott sorcsoportok. Az alábbi szakaszok azt ismertetik, hogyan becslése memóriára van szükség, és több memóriát lefoglalni.
- DICTIONARY_SIZE: Trim ezért azt jelzi, hogy sorcsoport tisztítás történt, mert hiba történt legalább egy karakterláncoszlop széles és/vagy nagy számosságú karakterláncokkal. A szótárkészítéses méret legfeljebb 16 MB-ra a memóriában, és ez a korlát elérésekor a sorcsoport tömörítve van. Ha ez a helyzet, fontolja meg a problémás oszlop elkülönítése egy önálló táblába.

## <a name="how-to-estimate-memory-requirements"></a>Hogyan memória követelményeinek becslése

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Egy sorcsoport tömörítése a maximális szükséges memória mérete körülbelül

- 72 MB +
- \#sorok \* \#oszlopok \* 8 bájt +
- \#sorok \* \#rövid – karakterlánc-oszlopok \* 32 bájt +
- \#a hosszú – karakterlánc-oszlopok \* tömörítési 16 MB

ahol a rövid – karakterlánc-oszlopok használata adattípusokkal, < = 32 bájt és hosszú – karakterlánc-oszlopok használata adattípusokkal > 32 bájt.

Hosszú karakterláncok tömörített készült szöveges tömöríti a tömörítési módszer. A tömörítéses módszer használ egy *szótár* szövegminták tárolásához. Egy szótárban maximális mérete 16 MB. Nincs a sorcsoport hosszú karakterlánc-oszlopok csak egy szótárban.

Egy oszlopcentrikus memóriakövetelményei részletes tárgyalását lásd: a videó [Azure SQL Data Warehouse méretezése: konfigurációs és útmutatás](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Memóriakövetelményei csökkentésére

Az alábbi technikák segítségével csökkentse a memóriakövetelményei naplóbájtot tömöríti az oszlopcentrikus indexek.

### <a name="use-fewer-columns"></a>Kevesebb oszlopot használja.
Ha lehetséges keressen a kevesebb oszlopot tartalmazó táblát. Ha egy sorcsoport az oszloptárba van tömörítve – az oszlopcentrikus index külön-külön tömöríti minden oszlop szegmensben. Ezért egy sorcsoport tömörítendő memóriakövetelményei növekszik oszlopok számának növelése.


### <a name="use-fewer-string-columns"></a>Oszlopok karakterlánc
String adattípusú oszlopok numerikus több memóriát és dátum típusú adatokat igényelnek. Memória követelmények csökkentése érdekében fontolja meg a ténytáblák karakterlánc oszlopok eltávolítása, és kisebb dimenziótáblák helyezné azokat.

Karakterlánc tömörítési további memóriára vonatkozó követelmények:

- Legfeljebb 32 karakter hosszúságú karakterlánc adattípusok 32 további bájtok száma érték lehet szükség.
- Adattípusokkal, legfeljebb 32 karakterből álló dictionary módszerekkel tömörítve vannak.  Minden egyes oszlopának a sorcsoport hozhat létre a szótár legfeljebb további 16 MB lehet szükség.

### <a name="avoid-over-partitioning"></a>Kerülje a fölösleges particionálása

Az Oszlopcentrikus indexek hozzon létre egy vagy több naplóbájtot partíciónként. Az SQL Data Warehouse a partíciók számának gyorsan növekednek, mert az adatok terjesztése, és minden egyes terjesztési particionálva van. Ha a tábla túl sok partíció van, a nem feltétlenül a naplóbájtot adatlehívást. A tömörítés során sorok hiánya rendelkezésre álló memória mennyisége nem hoz létre, de vezet, amely nem érhető el a legjobb lekérdezési teljesítmény oszlopcentrikus naplóbájtot.

Egy másik, kerülje a fölösleges particionálási oka egy többletterhelést okoz, a sorok való egy particionált tábla oszlopcentrikus index betöltésének memóriával. A betöltés során sok partíció kaphassák meg a bejövő szerepelnek, amelyek a memóriában vannak tartva, amíg mindegyik partíció rendelkezik elég sort az tömöríti. Túl sok partíció hoz létre a rendelkezésre álló további memória mennyisége.

### <a name="simplify-the-load-query"></a>A betöltés lekérdezés egyszerűsítése

Az adatbázis között a lekérdezésben minden operátor egy lekérdezés memóriaengedélyt fájlmegosztások. Amikor egy betöltési lekérdezési összetett rendezést és összekapcsolásokról, csökken a tömörítési rendelkezésre álló memória.

Hogy arra koncentrálhasson, csak a lekérdezés betöltése a terhelés lekérdezés tervezéséhez. Ha átalakításokat futtat az adatok van szüksége, futtassa őket külön betöltése a lekérdezésből. Például a halomtábla az adatok előkészítéséhez, az átalakítások futtatása és az átmeneti tárolási tábla majd betöltheti az oszlopcentrikus indexet. Először is az adatok betöltéséhez, és az MPP rendszer segítségével az adatok átalakításához.

### <a name="adjust-maxdop"></a>Adjust MAXDOP

Minden egyes terjesztési tömöríti naplóbájtot párhuzamosan az oszloptárba, ha egynél több processzormaggal érhető el terjesztési száma. A párhuzamosság szükséges további memória-erőforrásokat, amelyek a rendelkezésre álló memória mennyisége és sorcsoport levágási vezethet.

Rendelkezésre álló memória mennyisége csökkentése érdekében a MAXDOP lekérdezési mutató kényszerítése a betöltési művelet belül minden egyes terjesztési soros módban történő futtatására használhatja.

```
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQUota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Több memóriát lefoglalni módjai

DWU méretének és a felhasználó erőforrásosztályhoz együtt határozza meg, mennyi memória érhető el a felhasználói lekérdezés. Egy betöltési lekérdezés memóriaengedélyt növelése érdekében dwu-k számának növelése vagy is növelheti a erőforrásosztályhoz.

- A Dwu növelését, lásd: [hogyan méretezhető teljesítmény?](quickstart-scale-compute-portal.md)
- Ha módosítani szeretné egy lekérdezés erőforrásosztály, lásd: [módosítása egy felhasználói erőforrás osztály példa](resource-classes-for-workload-management.md#change-a-users-resource-class).

Például a DWU 100 egy felhasználó a smallrc erőforrásosztály használhatja 100 MB memóriát minden egyes terjesztési. A részletekért lásd: [az SQL Data Warehouse egyidejűségi](resource-classes-for-workload-management.md).

Tegyük fel, hogy határozhatja meg, hogy kell-e lekérni a kiváló minőségű sorcsoportokba méretek 700 MB memória található. Ezek a példák bemutatják, hogyan futtathat a terhelés lekérdezés elegendő memóriával rendelkező.

- A DWU-1000 és mediumrc használja, a memóriabeli ideiglenes 800 MB
- A DWU 600 és largerc használja, a memóriabeli ideiglenes 800 MB.


## <a name="next-steps"></a>További lépések

Talál további részleteket az SQL Data Warehouse teljesítményének javításához, tekintse meg a [teljesítmény áttekintése](sql-data-warehouse-overview-manage-user-queries.md).

