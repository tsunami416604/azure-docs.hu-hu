---
title: Teljesítménydiagnosztika a nagy méretezésben
description: Ez a cikk ismerteti, hogyan hárítsa el a nagy kapacitású teljesítmény problémák az Azure SQL Database-ben.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: 26bd6ddb9d8255b8e2510133fc4b6aa645f89f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75615063"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL nagy kapacitású teljesítményhibaelhárítási diagnosztika

A nagy kapacitású adatbázisok teljesítményproblémáinak elhárításához az Azure SQL-adatbázis számítási csomópontjának [általános teljesítményhangolási módszerei](sql-database-monitor-tune-overview.md) a teljesítményvizsgálat kiindulópontja. Azonban, mivel az [elosztott architektúra](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) a nagy kapacitású, további diagnosztika bővült, hogy segítse. Ez a cikk a nagy kapacitás-specifikus diagnosztikai adatokat ismerteti.

## <a name="log-rate-throttling-waits"></a>A naplózási sebesség szabályozása várakozik

Minden Azure SQL Database szolgáltatásszinten naplógenerálási sebességkorlátok vannak érvényben [a naplósebesség-szabályozáson](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)keresztül. A Nagy kapacitású, a napló generálási korlát jelenleg 100 MB/s, függetlenül a szolgáltatási szint. Vannak azonban olyan esetek, amikor az elsődleges számítási replika naplógenerálási sebességét szabályozni kell a helyreállíthatósági SLA-k fenntartása érdekében. Ez a szabályozás akkor történik, ha egy [lapkiszolgáló vagy egy másik számítási replika](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) jelentősen elmarad a Log szolgáltatás új naplórekordjainak alkalmazásától.

A következő várakozási típusok (a [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)fájlban) ismertetik az okat, amiért a naplósebesség szabályozható az elsődleges számítási replikán:

|Várakozás típusa    |Leírás                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Akkor következik be, amikor egy nagykapacitású adatbázis elsődleges számítási csomópont naplójának generálási sebessége a lapkiszolgáló(k)on késleltetett naplófelhasználás miatt szabályozás alatt áll.         |
|RBIO_RG_DESTAGE        | Akkor fordul elő, ha egy nagy méretű adatbázis számítási csomópont-naplózási generálási sebesség szabályozásalatt áll a hosszú távú naplótárolás késleltetett naplófelhasználása miatt.         |
|RBIO_RG_REPLICA        | Akkor fordul elő, amikor egy nagyméretű adatbázis számítási csomópont naplógenerálási sebessége az olvasható másodlagos replika(k) késleltetett naplófelhasználása miatt szabályozás alatt áll.         |
|RBIO_RG_LOCALDESTAGE   | Akkor következik be, amikor egy nagyméretű adatbázis számítási csomópont naplógenerálási sebessége a naplószolgáltatás késleltetett naplófelhasználása miatt szabályozás alatt áll.         |

## <a name="page-server-reads"></a>Lapkiszolgáló olvasása

A számítási replikák nem gyorsítótárazják az adatbázis teljes másolatát helyileg. A számítási replika helyi adatait a pufferkészlet (a memóriában) és a helyi rugalmas pufferkészlet-bővítmény (RBPEX) gyorsítótárában tárolja, amely az adatlapok részleges (nem borító) gyorsítótára. Ez a helyi RBPEX-gyorsítótár a számítási mérettel arányosan van méretezve, és a számítási szint memóriájának háromszorosa. Az RBPEX abban az esetben hasonlít a pufferkészlethez, hogy a leggyakrabban használt adatokkal rendelkezik. Minden oldal szerver, másrészt, van egy kiterjedő RBPEX cache a részét az adatbázis fenntartja.
 
Ha egy számítási kópián ad ki olvasást, ha az adatok nem léteznek a pufferkészletben vagy a helyi RBPEX gyorsítótárban, getPage(pageId, LSN) függvényhívás jön ki, és a lap a megfelelő lapkiszolgálóról kerül lehívásra. Az oldalkiszolgálókról olvasolvasások távoli olvasások, így lassabbak, mint a helyi RBPEX olvasásai. Az IO-val kapcsolatos teljesítményproblémák elhárításakor meg kell tudnunk mondani, hogy hány IOs-t végeztek el viszonylag lassabb távoli lapkiszolgálóolvasásokon keresztül.

Számos DMV-k és kiterjesztett események oszlopok és mezők, amelyek meghatározzák a távoli olvasások száma egy oldal szerver, amely összehasonlítható a teljes olvasás. A lekérdezéstároló a távoli olvasásokat is rögzíti a lekérdezés futási idejének statisztikái részeként.

- A jelentésoldal-kiszolgáló olvasásához hozzáférő oszlopok elérhetők a végrehajtási DMV-kben és katalógusnézetekben, például:
    - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
    - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- A lapkiszolgáló olvasása a következő kiterjesztett eseményekhez lesz hozzáadva:
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - lekérdezés-store_execution_runtime_info
- Az ActualPageServerReads/ActualPageServerReadAheads hozzáadva a lekérdezési terv XML-jéhez a tényleges tervekhez. Példa:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> A lekérdezési terv tulajdonságai ablakban ezeknek az attribútumoknak a megtekintéséhez az SSMS 18.3-as vagy újabb verzió szükséges.

## <a name="virtual-file-stats-and-io-accounting"></a>Virtuális fájlstatisztika és IO-könyvelés

Az Azure SQL Database-ben a [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF az SQL Server IO figyelésének elsődleges módja. Az IO jellemzői a nagy kapacitásban az [elosztott architektúrának](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)köszönhetően eltérőek. Ebben a szakaszban az IO-ra (olvasásés írás) összpontosítunk az adatfájlokra, ahogy az ebben a DMF-ben látható. A Nagy kapacitásban a DMF-ben látható minden adatfájl egy távoli lapkiszolgálónak felel meg. Az itt említett RBPEX gyorsítótár egy helyi SSD-alapú gyorsítótár, amely a számítási replika nem lefedő gyorsítótára.

### <a name="local-rbpex-cache-usage"></a>Helyi RBPEX gyorsítótár-használat

Helyi RBPEX-gyorsítótár található a számítási replika, a helyi SSD-tároló. Így a gyorsítótárhoz képest az IO gyorsabb, mint az IO a távoli lapkiszolgálókon. Jelenleg [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) egy nagy kapacitású adatbázis ban egy speciális sor jelentési az IO a helyi RBPEX cache a számítási replika. Ez a sor értéke 0 `database_id` `file_id` mindkét és az oszlop esetében. Az alábbi lekérdezés például rbpex használati statisztikákat ad vissza az adatbázis indítása óta.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Az RBPEX-en végzett olvasások aránya az összes többi adatfájlon végzett összesített olvasásokhoz biztosítja az RBPEX gyorsítótár találati arányát.

### <a name="data-reads"></a>Adatok olvasása

- Ha az SQL Server motorja egy számítási kópián adja ki az olvasásokat, akkor azokat vagy a helyi RBPEX gyorsítótár, vagy a távoli lapkiszolgálók, vagy a kettő kombinációja szolgálja ki, ha több oldalt olvas.
- Ha a számítási replika beolvas néhány oldalt egy adott fájlból, például 1 file_id, ha ezek az adatok kizárólag a helyi RBPEX gyorsítótárban találhatók, az olvasáshoz tartozó összes i/o file_id 0-val (RBPEX) szemben lesz elszámolva. Ha az adatok egy része a helyi RBPEX gyorsítótárban van, egy része pedig egy távoli oldalkiszolgálón, akkor az IO file_id 0 felé kerül elszámolásra az RBPEX-től kiszolgált rész esetében, és a távoli oldalkiszolgálóról kiszolgált rész file_id 1 felé kerül. 
- Ha egy számítási replika egy lapra kér egy oldalt egy [lapkiszolgálóról,](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) ha az oldalkiszolgáló nem érte el a kért LSN-t, a számítási replika olvasása megvárja, amíg az oldalkiszolgáló utoléri, mielőtt a lap visszakerül a számítási replikához. A számítási replika lapkiszolgálójáról történő olvasáshoz a PAGEIOLATCH_* várjon, ha az i/o-ra vár. A Nagy kapacitású, ez a várakozási idő magában foglalja mind az időt, hogy utolérjék a kért lapot a lapkiszolgálón az LSN szükséges, és a szükséges időt, hogy a lap a lapkiszolgálóról a számítási replika.
- A nagy olvasások, például az előreolvasás gyakran [a "Scatter-Gather" olvasással](/sql/relational-databases/reading-pages/)történik. Ez lehetővé teszi, hogy egyszerre legfeljebb 4 MB lap olvasását tegye lehetővé, amely et egyetlen olvasásnak tekintik az SQL Server motorjában. Ha azonban az olvasott adatok RBPEX-ben vannak, ezek az olvasások több egyedi 8 KB-os olvasásként kerülnek elszámolásra, mivel a pufferkészlet és az RBPEX mindig 8 KB-os oldalakat használ. Ennek eredményeképpen az RBPEX-en látható ikosok száma nagyobb lehet, mint a motor által végzett IOs tényleges száma.

### <a name="data-writes"></a>Adatírások

- Az elsődleges számítási replika nem ír közvetlenül a lapkiszolgálókra. Ehelyett a naplószolgáltatás naplórekordjait a rendszer újra lejátssza a megfelelő lapkiszolgálókon. 
- A számítási replika által bekövetkező írások túlnyomórészt a helyi RBPEX-hez (file_id 0) írnak. A 8 KB-nál nagyobb logikai fájlok, más szóval a [Gather-write](/sql/relational-databases/writing-pages/)használatával végzett írások esetében minden írási művelet több 8 KB-os egyéni írásra van lefordítva rbpex-be, mivel a pufferkészlet és az RBPEX mindig 8 KB-os lapokat használ. Ennek eredményeképpen az RBPEX-en látható írási IOs-ek száma nagyobb lehet, mint a motor által végzett IOs tényleges száma.
- Nem RBPEX fájlokat, vagy adatfájlok eltérő file_id 0, amelyek megfelelnek az oldal szerverek is mutatják írja. A nagy kapacitású szolgáltatási szint, ezek az írási műveletek szimulálva vannak, mert a számítási replikák soha nem ír közvetlenül a lap kiszolgálók. Az írási IOPS és az átviteli képesség a számítási replika során történik, de file_id a 0-tól eltérő adatfájlok késése nem tükrözi a lapkiszolgáló írási adatainak tényleges késését.

### <a name="log-writes"></a>Naplóírások

- Az elsődleges számítási, a napló írási file_id sys.dm_io_virtual_file_stats 2. Az elsődleges számítási napló írása a naplóleszállási zónába írás.
- A naplórekordok nem vannak megerősítve a véglegesítés másodlagos replikáján. A Nagy kapacitású napló a log szolgáltatás alkalmazza a másodlagos replikák aszinkron módon. Mivel a naplóírások valójában nem fordulnak elő másodlagos replikákon, a log IOs-ek másodlagos replikákon történő számlázása csak nyomon követési célokra szolgál.

## <a name="data-io-in-resource-utilization-statistics"></a>Adat I/O-adat az erőforrás-kihasználtsági statisztikában

Egy nem nagy kapacitású adatbázisban az adatokhoz képest az [erőforrás-irányítási](/azure/sql-database/sql-database-resource-limits-database-server#resource-governance) adatok IOPS-korlátjához viszonyított kombinált olvasási és írási IOPS-adatok a [sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) és [a sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) nézetekben vannak jelentve az `avg_data_io_percent` oszlopban. Ugyanazt az értéket jelenti a portálon az _Adat IO százalék ._ 

A nagy kapacitású adatbázisokban ez az oszlop az adatok IOPS-kihasználtságának a csak számítási replika `tempdb`helyi tárolásának, különösen az RBPEX és a . Ebben az oszlopban a 100%-os érték azt jelzi, hogy az erőforrás-szabályozás korlátozza a helyi tárolási IOPS. Ha ez egy teljesítményproblémával korrelál, hangolja be a munkaterhelést úgy, hogy kevesebb i/o-t generáljon, vagy növelje az adatbázis-szolgáltatás célkitűzését az erőforrás-irányítási _max data IOPS-korlát_ [limit](sql-database-vcore-resource-limits-single-databases.md)növeléséhez. Az RBPEX olvasási és írási műveletek erőforrás-szabályozása esetén a rendszer az SQL Server motor által kibocsátható nagyobb IOs-ek helyett az egyes 8 kb IOs-t számolja. 

A távoli lapkiszolgálókon lévő adatok io-ja nem az erőforrás-kihasználtsági nézetekben vagy a portálon jelenik meg, hanem a [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF-ben, ahogy azt korábban említettük.


## <a name="additional-resources"></a>További források

- A virtuálismag-erőforrás-korlátok egy nagy léptékű egyetlen adatbázis lásd: [Nagykapacitású szolgáltatásréteg virtuálismag-korlátok](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)
- Az Azure SQL Database teljesítményének finomhangolása a [Lekérdezés teljesítményének az Azure SQL Database-ben című témakörben](sql-database-performance-guidance.md) található.
- A Query Store használatával történő teljesítményhangolásról a [Teljesítményfigyelés a Lekérdezéstároló használatával című témakörben](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/) találja.
- A DMV figyelési parancsfájlok, [lásd: Teljesítmény figyelése Azure SQL Database dinamikus felügyeleti nézetek használatával](sql-database-monitoring-with-dmvs.md)
