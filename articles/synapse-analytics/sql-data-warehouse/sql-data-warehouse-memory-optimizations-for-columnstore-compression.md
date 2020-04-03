---
title: Az oszlopcentrikus index teljesítményének javítása
description: Csökkentse a memóriaigényt, vagy növelje a rendelkezésre álló memóriát az egyes sorcsoportokon belüli sorok számának maximalizálása érdekében.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 81191fd3b654f612f2621757f3006268276477de
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586532"
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>A sorcsoport minőségének maximalizálása oszlopcentrikushoz

A sorcsoport minőségét a sorcsoport sorainak száma határozza meg. A rendelkezésre álló memória növelése maximalizálhatja az oszlopcentrikus index által az egyes sorcsoportokba tömörített sorok számát.  Ezekkel a módszerekkel javíthatja a tömörítési sebességet és a lekérdezési teljesítményt az oszlopcentrikus indexek esetében.

## <a name="why-the-rowgroup-size-matters"></a>Miért fontos a sorcsoport mérete?
Mivel az oszlopcentrikus index az egyes sorcsoportok oszlopszegmenseinek beolvasásával vizsgálja a táblázatot, az egyes sorcsoportok soraiszámának maximalizálása növeli a lekérdezési teljesítményt. Ha a sorcsoportok nak nagy számú sora van, javul az adattömörítés, ami azt jelenti, hogy kevesebb adat olvasható a lemezről.

A sorcsoportokról további információt az [Oszlopcentrikus indexek útmutatója című témakörben talál.](https://msdn.microsoft.com/library/gg492088.aspx)

## <a name="target-size-for-rowgroups"></a>Sorcsoportok célmérete
A legjobb lekérdezési teljesítmény érdekében a cél az oszlopcentrikus indexben sorcsoportonkénti sorok számának maximalizálása. Egy sorcsoport legfeljebb 1 048 576 sort tartalmazhat. Nem baj, ha nem rendelkezik a sorok maximális száma soronként. Az oszlopcentrikus indexek jó teljesítményt érnek el, ha a sorcsoportok legalább 100 000 sort tartalmaznak.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>A sorcsoportok levághatók a tömörítés során

Tömeges betöltés vagy oszlopcentrikus index újraépítése során néha nincs elég memória az egyes sorcsoportokhoz kijelölt összes sor tömörítéséhez. Memórianyomás esetén az oszlopcentrikus indexek levágják a sorcsoport méreteket, hogy az oszlopcentrikusba történő tömörítés sikeres legyen. 

Ha nincs elegendő memória legalább 10 000 sor tömörítéséhez minden sorcsoportba, hiba lép fel.

A tömeges betöltésről további információt a [Fürtözött oszlopcentrikus indexbe való tömeges betöltés című témakörben](https://msdn.microsoft.com/library/dn935008.aspx#Bulk )talál.

## <a name="how-to-monitor-rowgroup-quality"></a>A sorcsoport minőségének figyelése

A DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats ([sys.dm_db_column_store_row_group_physical_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql) tartalmazza az SQL DB-nek megfelelő nézetdefiníciót), amely hasznos információkat tár fel, például a sorok számát a sorcsoportokban, és a vágás okát, ha vágás történt. A következő nézetet a dmv lekérdezésének praktikus módjaként hozhatja létre, hogy információt kapjon a sorcsoport vágásáról.

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

A trim_reason_desc jelzi, hogy a sorcsoportot levágták-e(trim_reason_desc = NO_TRIM azt jelenti, hogy nem volt vágás, és a sorcsoport optimális minőségű). A következő vágási okok jelzik a sorcsoport idő előtti levágását:
- BULKLOAD: Ez a vágási ok akkor használatos, ha a beérkező sorok bejövő kötege a terhelés kevesebb, mint 1 millió sor. A motor tömörített sorcsoportokat hoz létre, ha több mint 100 000 sor van beszúrva (szemben a különbözeti tárolóba való beszúrással), de a trim okát BULKLOAD értékre állítja. Ebben az esetben fontolja meg a kötegelt terhelés növelését, hogy több sort tartalmazzon. Emellett értékelje újra a particionálási sémát annak érdekében, hogy az ne legyen túl részletes, mivel a sorcsoportok nem terjedhetnek át a partícióhatárokon.
- MEMORY_LIMITATION: Az 1 millió sorral rendelkező sorcsoportok létrehozásához a motornak bizonyos mennyiségű munkamemóriára van szüksége. Ha a betöltési munkamenet rendelkezésre álló memóriája kisebb, mint a szükséges munkamemória, a sorcsoportok idő előtt lelesznek vágva. A következő szakaszok bemutatják, hogyan kell megbecsülni a szükséges memóriát, és hogyan lehet több memóriát lefoglalni.
- DICTIONARY_SIZE: Ez a vágási ok azt jelzi, hogy a sorcsoport vágása azért történt, mert legalább egy karakterláncoszlop széles és/vagy magas számosságú karakterláncokkal rendelkezett. A szótár mérete a memóriában legfeljebb 16 MB lehet, és ha ez a korlát eléri a sorcsoportot tömöríti. Ha nem fut bele ebbe a helyzetbe, fontolja meg a problémás oszlop elkülönítése egy külön táblába.

## <a name="how-to-estimate-memory-requirements"></a>A memóriakövetelmények becslése

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Az egyik sorcsoport tömörítéséhez szükséges maximális memória körülbelül

- 72 MB +
- \#sorok \* \# \* oszlopok 8 bájt +
- \#sorok \* \#rövid-string-oszlopok \* 32 byte +
- \#hosszú-string-oszlopok \* 16 MB tömörítési szótár

ahol a rövid karakterlánc-oszlopok <= 32 bájt karakterlánc-adattípusokat, a hosszú karakterlánc-oszlopok pedig > 32 bájtos karakterlánc-adattípusokat használnak.

A hosszú karakterláncok tömörítése szöveg tömörítésére tervezett tömörítési módszerrel történik. Ez a tömörítési módszer *szótárt* használ a szövegminták tárolására. A szótár maximális mérete 16 MB. A sorcsoport minden hosszú karakterláncoszlopához csak egy szótár tartozik.

Az oszlopcentrikus memóriakövetelményeinek részletes áttekintését a [Synapse SQL méretezés: konfiguráció és útmutatás](https://channel9.msdn.com/Events/Ignite/2016/BRK3291)című videóban talál.

## <a name="ways-to-reduce-memory-requirements"></a>A memóriaigény csökkentésének módjai

Az alábbi módszerekkel csökkentheti a sorcsoportok oszlopcentrikus indexekbe tömörítéséhez használt memóriakövetelményeket.

### <a name="use-fewer-columns"></a>Kevesebb oszlop használata
Ha lehetséges, tervezze meg a táblázatot kevesebb oszloppal. Ha egy sorcsoportot tömörít az oszloptárba, az oszlopcentrikus index az egyes oszlopszegmenseket külön tömöríti. Ezért a sorcsoport tömörítéséhez a listaszám növekedésével nő a memóriakövetelmények.


### <a name="use-fewer-string-columns"></a>Kevesebb karakterláncoszlop használata
A karakterlánc-adattípusok oszlopai több memóriát igényelnek, mint a numerikus és dátumadattípusok. A memóriaigény csökkentése érdekében fontolja meg a karakterláncoszlopok eltávolítását a ténytáblákból, és helyezze őket kisebb dimenziótáblákba.

A karakterlánc-tömörítés további memóriakövetelményei:

- A legfeljebb 32 karakterből legfeljebb 32 karakterből áll, és értékenként 32 további bájtot igényelhet.
- A 32 karakternél hosszabb karakterlánc-adattípusokat szótármódszerekkel tömöríti a rendszer.  A sorcsoport minden oszlopa további 16 MB-ot igényelhet a szótár létrehozásához.

### <a name="avoid-over-partitioning"></a>Kerülje a túlparticionálást

Az oszlopcentrikus indexek partíciónként egy vagy több sorcsoportot hoznak létre. Az Azure Synapse Analytics adatraktározási, a partíciók száma gyorsan növekszik, mert az adatok elosztása és minden disztribúció particionált. Ha a tábla túl sok partícióval rendelkezik, előfordulhat, hogy nincs elég sor a sorcsoportok kitöltéséhez. A sorok hiánya nem hoz létre memórianyomást a tömörítés során, de olyan sorcsoportokhoz vezet, amelyek nem érik el a legjobb oszlopcentrikus lekérdezési teljesítményt.

Egy másik ok, hogy elkerüljék a túlzott particionálás van egy memória terhelési sorok betöltése egy oszlopcentrikus index egy particionált tábla. A terhelés során sok partíció fogadhatja a bejövő sorokat, amelyek a memóriában vannak, amíg minden partíció elegendő tömörítendő sort nem tartalmaz. Ha túl sok partíció van, az további memórianyomást eredményez.

### <a name="simplify-the-load-query"></a>A betöltési lekérdezés egyszerűsítése

Az adatbázis megosztja a lekérdezés memóriaátadását a lekérdezés összes operátora között. Ha egy betöltési lekérdezés összetett rendezési és illesztési adatokkal rendelkezik, a tömörítéshez rendelkezésre álló memória csökken.

Úgy tervezze meg a betöltési lekérdezést, hogy csak a lekérdezés betöltésére összpontosítson. Ha átalakításokat kell futtatnia az adatokon, futtassa őket a betöltési lekérdezéstől elkülönítve. Például egy halommemória-táblában rendezze az adatokat, futtassa az átalakításokat, majd töltse be az átmeneti táblát az oszlopcentrikus indexbe. Először az adatokat is betöltheti, majd az MPP-rendszer relazikálhatja az adatokat.

### <a name="adjust-maxdop"></a>MAXDOP beállítása

Minden disztribúció tömöríti sorcsoportok at oszlopcentrikus párhuzamosan, ha van egynél több CPU-mag áll rendelkezésre egy disztribúció. A párhuzamosság további memóriaerőforrásokat igényel, ami memórianyomáshoz és sorcsoport-vágáshoz vezethet.

A memórianyomás csökkentése érdekében a MAXDOP lekérdezési emlékeztetővel kényszerítheti a betöltési műveletet soros módban való futtatásra az egyes disztribúciókon belül.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Több memória lefoglalásának módjai

A DWU-méret és a felhasználói erőforrásosztály együttesen határozza meg, hogy mennyi memória áll rendelkezésre egy felhasználói lekérdezéshez. A betöltési lekérdezés memóriatámogatásának növeléséhez növelheti a DWUs-ok számát, vagy növelheti az erőforrásosztályt.

- A DWUs-ok növeléséről a Teljesítmény méretezése című témakörben [jelenik meg?](quickstart-scale-compute-portal.md)
- A lekérdezés erőforrásosztályának módosításához olvassa el a Felhasználói erőforrásosztály módosítása példa című [témakört.](resource-classes-for-workload-management.md#change-a-users-resource-class)

## <a name="next-steps"></a>További lépések

A Synapse SQL teljesítményének további javítását a [Teljesítmény áttekintése című témakörben](cheat-sheet.md)találja.
