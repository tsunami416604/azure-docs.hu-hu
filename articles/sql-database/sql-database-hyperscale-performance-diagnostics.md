---
title: Teljesítmény-diagnosztika a nagy kapacitású-ben
description: Ez a cikk azt ismerteti, hogyan lehet elhárítani a Azure SQL Database nagy kapacitású kapcsolatos problémákat.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: 2e162b30a0227c5f04c74dae01413177d1623235
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901247"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL nagy kapacitású Performance hibaelhárítási diagnosztika


A nagy kapacitású-adatbázisok teljesítményével kapcsolatos problémák elhárításához az Azure SQL Database számítási csomópontjának [általános teljesítmény-hangolási módszerei](sql-database-monitor-tune-overview.md) a teljesítmény vizsgálatának kiindulópontja. A nagy kapacitású [elosztott architektúrája](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) miatt azonban további diagnosztika lett hozzáadva a segítséghez. Ez a cikk a nagy kapacitású-specifikus diagnosztikai információkat ismerteti.


## <a name="log-rate-throttling-waits"></a>A naplózási sebesség szabályozása vár


Minden Azure SQL Database szolgáltatási szinten a naplózási [ráta irányításával](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)kényszerített, a log Generation díjszabási korlátai érvényesek. A nagy kapacitású-ben a log generálási korlát jelenleg 100 MB/s-ra van állítva, a szolgáltatási szinttől függetlenül. Vannak azonban olyan időpontok, amikor az elsődleges számítási replika log generálási sebességét szabályozni kell a helyreállító SLA-kat fenntartásához. Ez a szabályozás akkor fordul elő, ha egy [lapozófájl vagy egy másik számítási replika](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) jelentősen megmarad az új naplóbejegyzések a log szolgáltatásban való alkalmazása mögött.

A következő várakozási típusok (a [sys. dm_os_wait_statsban](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)) azt ismertetik, hogy miért szabályozható a naplózási sebesség az elsődleges számítási replika esetén:

|Várakozás típusa    |Leírás                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Akkor következik be, amikor a nagy kapacitású-adatbázis elsődleges számítási csomópontjának napló-generálási sebessége a kiszolgáló (k) késleltetett naplójának használata miatt van szabályozva.         |
|RBIO_RG_DESTAGE        | Akkor következik be, amikor a nagy kapacitású-adatbázis számítási csomópontjának naplójának generálási arányát a rendszer a hosszú távú napló-tárolás késleltetett naplózása miatt szabályozza.         |
|RBIO_RG_REPLICA        | Akkor következik be, amikor a nagy kapacitású-adatbázis számítási csomópontjának naplójának generálási arányát a rendszer az olvasható másodlagos replika (ok) késleltetett naplójának használata miatt szabályozza.         |
|RBIO_RG_LOCALDESTAGE   | Akkor következik be, amikor a naplózási szolgáltatás késleltetett naplózása miatt a rendszer a nagy kapacitású adatbázis számítási csomópontjának naplójának generálási sebességét szabályozza.         |


## <a name="page-server-reads"></a>Az oldal kiszolgálójának olvasása

A számítási replikák nem gyorsítótárazzák helyileg az adatbázis teljes másolatát. A számítási replikához tartozó adatok tárolása a pufferben (a memóriában) és a helyi rugalmas puffer-tároló bővítmény (RBPEX) gyorsítótárában történik, amely az adatlapok részleges (nem lefedő) gyorsítótára. A helyi RBPEX gyorsítótár mérete arányos a számítási mérettel, és a számítási rétegek memóriája 3-szor van. A RBPEX hasonló ahhoz a puffer-készlethez, amelyben a leggyakrabban használt információ található. A más lapokon található kiszolgálók esetében a RBPEX gyorsítótár található az adatbázis által karbantartott részhez.
 
Ha az olvasás egy számítási replikán van kiadva, ha az adatok nem léteznek a puffer-készletben vagy a helyi RBPEX-gyorsítótárban, a rendszer getPage (pageId, LSN) függvény hívását állítja ki, és a lapot beolvassa a megfelelő oldal-kiszolgálóról. Az egyoldalas kiszolgálókról érkező olvasások távoli olvasások, így lassabbak, mint a helyi RBPEX beolvasása. Az IO-vel kapcsolatos teljesítményproblémák hibaelhárításakor tudnia kell, hogy hány IOs-t tettek elérhetővé viszonylag lassabb távoli oldal-kiszolgáló olvasásával.

Számos DMV és bővített eseménynek van olyan oszlopa és mezője, amely meghatározza, hogy a rendszer milyen számú távoli olvasást végez egy adott lapról, amely összehasonlítható az összes olvasási művelettel. A Query Store a lekérdezés futási idejének statisztikájának részeként rögzíti a távoli olvasásokat is.

- A jelentéskészítő lap kiszolgálójának olvasási oszlopai a végrehajtás DMV és a katalógusok nézeteiben érhetők el, például:
    - [sys. dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys. dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys. dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys. dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
    - [sys. query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- A Page Server olvasások a következő kiterjesztett eseményekhez lesznek hozzáadva:
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - lekérdezés – store_execution_runtime_info
- A rendszer a ActualPageServerReads/ActualPageServerReadAheads adja hozzá a lekérdezési terv XML-kódjához a tényleges tervekhez. Példa:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Ha meg szeretné tekinteni ezeket az attribútumokat a SSMS, akkor a SSMS 18,3-es vagy újabb verziójára lesz szüksége.


## <a name="virtual-file-stats-and-io-accounting"></a>Virtuális fájl statisztikái és IO-nyilvántartása

Azure SQL Database a [sys. dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF az elsődleges módszer a SQL Server IO figyelésére. Az i/o-jellemzők a nagy kapacitású eltérőek az [elosztott architektúrák](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)miatt. Ebben a szakaszban az IO (olvasás és írás) adatfájlokra koncentrálunk, ahogy az ebben a DMF látható. A nagy kapacitású-ben az ebben a DMF látható összes adatfájl egy távoli oldal-kiszolgálónak felel meg. Az itt említett RBPEX cache egy helyi SSD-alapú gyorsítótár, amely a számítási replika nem a gyorsítótára.


### <a name="local-rbpex-cache-usage"></a>Helyi RBPEX cache-használat

Helyi RBPEX gyorsítótár létezik a helyi SSD-tároló számítási csomópontján. Ennek megfelelően a RBPEX gyorsítótárának IO-értéke gyorsabb, mint a távoli oldal-kiszolgálók IO-je. A nagy kapacitású-adatbázisban jelenleg a [sys. dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) egy külön sorral jelent meg, amely a számítási REPLIKA helyi RBPEX gyorsítótárában végzett i/o-t jelenti. Ebben a sorban a 0 érték szerepel a `database_id` és az `file_id` oszlop esetében is. Az alábbi lekérdezés például a RBPEX-használati statisztikát adja vissza az adatbázis indítása óta.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

A RBPEX által az összes többi adatfájlon végzett összesített olvasások aránya a RBPEX gyorsítótárának találati arányát adja meg.


### <a name="data-reads"></a>Adatolvasások

- Ha az olvasásokat a SQL Server motor állítja ki egy számítási replikán, akkor azokat a helyi RBPEX-gyorsítótár vagy a távoli lapok kiszolgálói vagy a kettő kombinációja, ha több oldalt olvas be.
- Ha a számítási replika egy adott fájl egyes lapjait olvassa be, például file_id 1 értéket, ha ezek az adatok kizárólag a helyi RBPEX-gyorsítótárban találhatók, az olvasáshoz tartozó összes IO-t file_id 0 (RBPEX) értékre kell kiszámítani. Ha az adatok némelyike a helyi RBPEX-gyorsítótárban található, és néhány rész egy távoli oldalon található, akkor az i/o-t a rendszer a RBPEX szolgáltatásból kiszolgált rész file_id felé haladva a 0 értékre veszi figyelembe, és a távoli oldal kiszolgálója által kiszolgált rész file_id 1. 
- Ha egy számítási replika egy adott [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) lévő oldalt kér le, ha a lap kiszolgálója nem a kért LSN, a rendszer a számítási replikán lévő olvasás után megvárja, amíg a kiszolgáló felveszi a lapot a számítási replikába. A számítási replikán lévő egyik oldalról beolvasott adatok esetén a PAGEIOLATCH_ * WAIT típus jelenik meg, ha az IO-ra várakozik. Ez a várakozási idő azt is magában foglalja, hogy mennyi ideig kell felvenni a lapon a kért oldalt a LSN, és meg kell adni a lap a lapozófájlból a számítási replikába való átviteléhez szükséges időt.
- A nagyméretű olvasások, például az olvasás előtt gyakran a ["Scatter-gather" olvasások](/sql/relational-databases/reading-pages/)használatával történik. Így egyszerre akár 4 MB-nyi lapot is beolvashat, így egyetlen beolvasható a SQL Server motorban. Ha azonban a beolvasott adatokat RBPEX, a rendszer az olvasásokat több, 8 KB-os olvasási értékként veszi figyelembe, mivel a puffer-készlet és a RBPEX mindig 8 KB-os oldalt használ. Ennek eredményeképpen a RBPEX által látott olvasási IOs-szám nagyobb lehet, mint a motor által elvégzett IOs tényleges száma.


### <a name="data-writes"></a>Adatírások

- Az elsődleges számítási replika nem ír közvetlenül a lapok kiszolgálójára. Ehelyett a naplózási szolgáltatásból származó bejegyzéseket a rendszer visszajátssza a megfelelő kiszolgálókon. 
- A számítási replikán megjelenő írásokat a rendszer túlnyomórészt a helyi RBPEX írja (file_id 0). A 8 KB-nál nagyobb logikai fájlokra vonatkozó írások esetében, azaz a [gather-Write](/sql/relational-databases/writing-pages/)használatával végzett írások esetében a rendszer az egyes írási műveleteket több, 8 KB-os egyéni írásra fordítja le a RBPEX, mivel a puffer-készlet és a RBPEX mindig 8 KB-os oldalt használ. Ennek eredményeképpen a RBPEX által látott írási IOs-szám nagyobb lehet, mint a motor által végrehajtott IOs-szám.
- A nem RBPEX fájlok, illetve a file_id 0-tól eltérő adatfájlok, amelyek a lapozófájloknak felelnek meg, az írásokat is megjelenítik. A nagy kapacitású szolgáltatási szinten ezek az írások szimulálva vannak, mivel a számítási replikák soha nem írnak közvetlenül a lapozófájlokra. Az írási IOPS és az átviteli sebesség a számítási replikán megjelenő módon történik, de a file_id 0-tól eltérő adatfájlok késése nem tükrözi a kiszolgálóoldali írások tényleges késését.

### <a name="log-writes"></a>Naplók írása

- Az elsődleges számítás során a rendszer a napló írását a sys file_id 2. dm_io_virtual_file_stats. Az elsődleges számítási naplóba való írás egy írás a naplózási zónába.
- A naplózási rekordok nincsenek megerősítve a véglegesítő másodlagos replikán. A nagy kapacitású-ben a Xlog szolgáltatás a naplót a távoli replikák számára alkalmazza. Mivel a naplóbeli írások valójában nem fordulnak elő másodlagos replikákban, a log IO-k a másodlagos replikán való könyvelése csak követési célokat szolgál.

## <a name="additional-resources"></a>További források

- A nagy kapacitású-virtuális mag erőforrás-korlátaival kapcsolatban lásd: [nagy kapacitású szolgáltatási réteg virtuális mag korlátai](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)
- A Azure SQL Database teljesítményének finomhangolását lásd: [a lekérdezési teljesítmény a Azure SQL Database](sql-database-performance-guidance.md)
- A Query Store-t használó teljesítmény-hangolással kapcsolatban lásd: [Teljesítményfigyelés a Query Store használatával](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)
- A DMV-figyelési parancsfájlok esetében lásd: [teljesítmény monitorozása Azure SQL Database dinamikus felügyeleti nézetek használatával](sql-database-monitoring-with-dmvs.md)
