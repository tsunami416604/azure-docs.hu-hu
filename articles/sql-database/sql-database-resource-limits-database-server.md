---
title: Erőforrás-korlátok Azure SQL Databasea | Microsoft Docs
description: Ez a cikk áttekintést nyújt az önálló adatbázisok és a rugalmas készletek Azure SQL Database erőforrásának korlátairól. Emellett tájékoztatást nyújt arról is, hogy mi történik, ha az erőforrás korlátai elérik vagy túllépik azokat.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/19/2019
ms.openlocfilehash: afb30a17d7a1450f169402c18f41ce249415e89d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804826"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>Erőforrás-korlátozások és erőforrás-szabályozás SQL Database

Ez a cikk áttekintést nyújt az önálló adatbázisokat és rugalmas készleteket kezelő SQL Database kiszolgálók SQL Database erőforrás-korlátairól. Információkkal szolgál arról, hogy mi történik az erőforrás-korlátok megváltozásakor vagy túllépésekor, és leírja a határértékek betartatásához használt erőforrás-irányítási mechanizmusokat.

> [!NOTE]
> A felügyelt példányok korlátaival kapcsolatban lásd: [SQL Database erőforrás-korlátok a felügyelt példányok számára](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Erőforrás-korlátok maximális száma

| Erőforrás | Korlát |
| :--- | :--- |
| Adatbázisok száma kiszolgálónként | 5000 |
| Az előfizetéshez tartozó kiszolgálók alapértelmezett száma bármely régióban | 20 |
| Kiszolgálók/előfizetés maximális száma bármely régióban | 200 |  
| DTU/eDTU kvóta kiszolgálónkénti bontásban | 54 000 |  
| Virtuális mag-kvóta kiszolgálónkénti/példányon | 540 |
| Készletek maximális száma kiszolgálónként | Korlátozott számú DTU vagy virtuális mag. Ha például az egyes készletek 1000 DTU, akkor a kiszolgáló támogatja az 54-es készleteket.|
|||

> [!IMPORTANT]
> Mivel az adatbázisok száma SQL Database kiszolgálón megközelíti a korlátot, a következő műveletek végezhetők el:
>
> - Növekvő késés a főadatbázison futó lekérdezések futtatásakor.  Ide tartoznak az erőforrás-kihasználtsági statisztikák, például a sys. resource_stats nézetei.
> - Növekvő késés a felügyeleti műveletekben és a portálon olyan nézőpontok, amelyek a kiszolgáló adatbázisainak számbavételét foglalják magukban.

> [!NOTE]
> Ha további DTU/eDTU kvótát, virtuális mag kvótát vagy több kiszolgálót szeretne beszerezni az alapértelmezett értéknél, küldjön egy új támogatási kérelmet a Azure Portal. További információ: [a kérelmek kvótájának növekedése Azure SQL Database](quota-increase-request.md).

### <a name="storage-size"></a>Tárterület mérete

Az önálló adatbázisok erőforrás-tárolási méreteit a [DTU-alapú erőforrás-korlátok](sql-database-dtu-resource-limits-single-databases.md) vagy a [virtuális mag-alapú erőforrás-](sql-database-vcore-resource-limits-single-databases.md) korlátok alapján, a tárterületre vonatkozó korlátok alapján, díjszabási szinten tekintheti meg.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Mi történik az adatbázis-erőforrások korlátainak elérésekor

### <a name="compute-dtus-and-edtus--vcores"></a>Számítás (DTU és Edtu/virtuális mag)

Ha az adatbázis számítási kihasználtsága (a DTU és a Edtu, illetve a virtuális mag alapján mérve) magas, a lekérdezés késése megnő, és a lekérdezések is időtúllépést okozhatnak. Ilyen körülmények között előfordulhat, hogy a szolgáltatás várólistára helyezi a lekérdezéseket, és erőforrásokat biztosít a végrehajtáshoz, mivel az erőforrások ingyenesek lesznek.
Ha magas számítási kihasználtságot tapasztal, a kockázatcsökkentő lehetőségek a következők:

- Az adatbázis vagy a rugalmas készlet számítási méretének növelése az adatbázis további számítási erőforrásokkal való biztosításához. Lásd: [önálló adatbázis-erőforrások méretezése](sql-database-single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése](sql-database-elastic-pool-scale.md).
- Lekérdezések optimalizálása az egyes lekérdezések erőforrás-kihasználtságának csökkentése érdekében. További információ: a [lekérdezés finomhangolása/célzása](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

Ha az adatbázis-terület eléri a maximális méretkorlátot, az adatbázis-beszúrások és az adatméretet növelő frissítések sikertelenek lesznek, és az ügyfelek [hibaüzenetet](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md)kapnak. A SELECT és DELETE utasítások továbbra is sikeresek lesznek.

A magas lemezterület-használat során a megoldás a következőkre terjed ki:

- Az adatbázis vagy a rugalmas készlet maximális méretének növelése, vagy további tárhely hozzáadása. Lásd: [önálló adatbázis-erőforrások méretezése](sql-database-single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése](sql-database-elastic-pool-scale.md).
- Ha az adatbázis egy rugalmas készletben található, akkor azt is megteheti, hogy az adatbázist a készleten kívülre helyezi, hogy a tárolóhelye ne legyen megosztva más adatbázisokkal.
- Adatbázis zsugorítása a nem használt terület felszabadításához. További információ: [a tárterület kezelése a Azure SQL Databaseban](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Munkamenetek és feldolgozók (kérelmek)

A munkamenetek és a feldolgozók maximális számát a szolgáltatási szintek és a számítási méret (DTU/Edtu vagy virtuális mag) határozzák meg. A rendszer elutasítja az új kérelmeket, ha a munkamenet-vagy feldolgozói korlátok elérésekor az ügyfelek hibaüzenetet kapnak. Míg az elérhető kapcsolatok száma az alkalmazás által szabályozható, az egyidejű feldolgozók száma gyakran nehezebb a becsléshez és a szabályozáshoz. Ez különösen igaz a maximális betöltési időszakok során, amikor az adatbázis-erőforrások korlátai elérik, a feldolgozók pedig hosszabb ideig futó lekérdezések, nagyméretű blokkoló láncok vagy túlzott lekérdezés-párhuzamosság miatt halmozódnak fel.

A magas munkamenet vagy munkavégző kihasználtsága esetén a kockázatcsökkentő lehetőségek a következők:

- Az adatbázis vagy a rugalmas készlet szolgáltatási szintjeinek vagy számítási méretének növelése. Lásd: [önálló adatbázis-erőforrások méretezése](sql-database-single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése](sql-database-elastic-pool-scale.md).
- A lekérdezések optimalizálása az egyes lekérdezések erőforrás-kihasználtságának csökkentése érdekében, ha a munkavégzők nagyobb kihasználtságának oka a számítási erőforrások miatti kihasználása. További információ: a [lekérdezés finomhangolása/célzása](sql-database-performance-guidance.md#query-tuning-and-hinting).
- Csökkentse a [MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (maximális párhuzamossági fok) beállítást.
- A lekérdezési munkaterhelés optimalizálása az előfordulások számának csökkentése és a lekérdezés letiltásának időtartama alapján.

### <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Erőforrás-felhasználás felhasználói munkaterhelések és belső folyamatok alapján

A CPU-és a memóriahasználat az egyes adatbázisokban lévő felhasználói munkaterhelések jelentik a [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) és a [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) nézeteit `avg_cpu_percent` és `avg_memory_usage_percent` oszlopait. Rugalmas készletek esetén a rendszer a készlet szintű erőforrás-felhasználást a [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetben jeleníti meg. A felhasználói munkaterhelés CPU-felhasználását a `cpu_percent` Azure monitor metrikán keresztül is jelenteni kell a készlet szintjén található [önálló adatbázisok](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) és [rugalmas készletek](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) esetében.

Azure SQL Database számítási erőforrásokat igényel az olyan alapvető szolgáltatási funkciók megvalósításához, mint a magas rendelkezésre állás és a vész-helyreállítás, az adatbázis biztonsági mentése és visszaállítása, figyelés, lekérdezési tároló, automatikus hangolás stb. A rendszer az [erőforrás-irányítási](#resource-governance) mechanizmusok használatával helyezi el az ezen belső folyamatok általános erőforrásainak egy bizonyos korlátozott részét, így a fennmaradó erőforrásokat elérhetővé teszi a felhasználói munkaterhelések számára. Amikor a belső folyamatok nem használnak számítási erőforrásokat, a rendszer elérhetővé teszi azokat a felhasználói munkaterhelések számára.

A CPU-és a memóriahasználat teljes kihasználtsága a felhasználói munkaterhelések és a belső folyamatok esetében a SQL Server példányban, amely egyetlen adatbázist vagy egy rugalmas készletet üzemeltet, a [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) és `avg_instance_memory_percent` a [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) nézetek, a `avg_instance_cpu_percent` és oszlopok. Ezeket `sqlserver_process_core_percent` az adatokat a és `sqlserver_process_memory_percent` a Azure monitor metrikái is jelentik, [önálló adatbázisok](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) és [rugalmas készletek](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) esetén a készlet szintjén.

A felhasználók munkaterhelései és a belső folyamatok részletes részletezése a [sys. dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) és a [sys. dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) nézetben történik. Az ezekben a nézetekben hivatkozott erőforrás-készletekkel és munkaterhelési csoportokkal kapcsolatos részletekért lásd: [erőforrás-szabályozás](#resource-governance). Ezek a nézetek a felhasználói munkaterhelések és a kapcsolódó erőforráskészlet-és munkaterhelés-csoportok adott belső folyamatai alapján jelentést készítenek az erőforrás-használatról.

A Teljesítményfigyelés és a hibaelhárítás kontextusában fontos figyelembe venni a felhasználói **CPU-fogyasztást** `avg_cpu_percent`( `cpu_percent`,) és a felhasználói munkaterhelések és a belső folyamatok (`avg_instance_cpu_percent`,`sqlserver_process_core_percent`) **teljes CPU-felhasználását** is.

A **felhasználói CPU-felhasználás** kiszámítása a felhasználói munkaterhelés korlátainak százalékában történik az egyes szolgáltatási célkitűzésekben. Az 100%-os **felhasználói CPU-kihasználtság** azt jelzi, hogy a felhasználói munkaterhelés elérte a szolgáltatási cél korlátját. Ha azonban a **teljes CPU-felhasználás** eléri a 70-100%-os tartományt, megtekintheti a felhasználói munkaterhelés átviteli sebességét, és a lekérdezés késése növekszik, még akkor is, ha a jelentett **felhasználói CPU-felhasználás** jelentős mértékben a 100% alá esik. Ez nagyobb valószínűséggel fordul elő, ha kisebb szolgáltatási célkitűzéseket használ a számítási erőforrások mérsékelt kiosztásával, de viszonylag intenzívebb felhasználói munkaterhelésekkel, például [sűrű rugalmas készletekben](sql-database-elastic-pool-resource-management.md). Ez kisebb szolgáltatási célkitűzések esetén is előfordulhatnak, ha a belső folyamatok átmenetileg további erőforrásokat igényelnek, például amikor új replikát hoz létre az adatbázisból.

Ha a **teljes CPU** -használat magas, a kockázatcsökkentő lehetőségek megegyeznek a korábban feljegyzett értékekkel, és tartalmazzák a szolgáltatási célkitűzések növekedését és/vagy a felhasználói munkaterhelés optimalizálását.

## <a name="resource-governance"></a>Erőforrások szabályozása

Az erőforrás-korlátok betartatása érdekében a Azure SQL Database a SQL Server [Resource Governoron](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)alapuló erőforrás-irányítási implementációt használ, amely SQL Server adatbázis-szolgáltatás Azure-beli futtatására lett módosítva és kiterjesztve. A szolgáltatás minden SQL Server példányán több [erőforráskészlet](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) és [munkaterhelési csoport](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)van, és az erőforrás-korlátok mind a készletben, mind a csoportok szintjén vannak beállítva, hogy [kiegyensúlyozott adatbázis-szolgáltatást](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/)nyújtsanak. A felhasználói munkaterhelés és a belső munkaterhelések külön erőforrás-készletbe és munkaterhelési csoportba vannak besorolva. Az elsődleges és olvasható másodlagos replikák (beleértve a földrajzi replikákat is) felhasználói munkaterhelése az `SloSharedPool1` erőforráskészlet és `UserPrimaryGroup.DBId[N]` a munkaterhelés csoportba van besorolva, ahol `N` az adatbázis-azonosító érték áll rendelkezésre. Emellett több erőforráskészlet és munkaterhelés-csoport is található a különböző belső munkaterhelésekhez.

Amellett, hogy a Resource Governor használatával szabályozza az erőforrásokat a SQL Server folyamaton belül, Azure SQL Database a Windows- [feladatok objektumait](https://docs.microsoft.com/windows/win32/procthread/job-objects) is használja a feldolgozási szintű erőforrás-szabályozáshoz, valamint a Windows [Fájlkiszolgálói erőforrás-kezelőt (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) a tárolási kvóta kezeléséhez.

A Azure SQL Database erőforrás-szabályozás hierarchikus jellegű. Fentről lefelé a korlátokat az operációs rendszer erőforrás-irányítási mechanizmusai és Resource Governor, az erőforrás-készlet szintjén, a Resource Governor, majd a munkaterhelés csoport szintjén, a Resource Governor használatával kényszeríti ki. Az aktuális adatbázisra vagy rugalmas készletre vonatkozó erőforrás-irányítási korlátok a [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) nézetben vannak felszínben. 

### <a name="data-io-governance"></a>Adatio-irányítás

Az adatio-szabályozás egy olyan folyamat, Azure SQL Database az olvasási és írási fizikai IO-t az adatbázisok adatfájljaira korlátozza. Az egyes IOPS korlátokat úgy állítjuk be, hogy a lehető legkisebbre csökkentsék a "zajos szomszéd" hatást, hogy az erőforrás-elosztás méltányos legyen a több-bérlős szolgáltatásban, és a mögöttes hardver és tárterület képességein belül maradjon.

Az önálló adatbázisok esetében a munkaterhelési csoportra vonatkozó korlátok a-adatbázisra vonatkozó összes összes tárterületre érvényesek, míg az erőforrás-készletre vonatkozó korlátozások az adott SQL Server-példányon található összes adatbázisra vonatkoznak az összes tárolóra, beleértve az `tempdb` adatbázist is. A rugalmas készletek esetében a munkaterhelési csoport korlátai a készlet minden adatbázisára érvényesek, míg az erőforráskészlet-korlát a teljes rugalmas készletre `tempdb` vonatkozik, beleértve az adatbázist is, amely a készletben lévő összes adatbázis között meg van osztva. Az erőforrás-készlet korlátai általában nem valósíthatók meg egy adatbázison (akár egyetlen, akár készletezett) keresztül, mert a munkaterhelés-csoport korlátai alacsonyabbak az erőforráskészlet korlátainál, és hamarabb korlátozzák a IOPS/átviteli sebességet. A készletre vonatkozó korlátokat azonban a több adatbázisra vonatkozóan is elérheti ugyanazon a SQL Server-példányon.

Ha például egy lekérdezés az i/o-erőforrás szabályozása nélkül 1000 IOPS hoz létre, de a munkaterhelési csoport maximális IOPS értéke 900 IOPS, a lekérdezés nem fog tudni többet létrehozni 900 IOPS. Ha azonban az erőforráskészlet maximális IOPS-korlátja 1500 IOPS, és az erőforráskészlet összes munkaterhelési csoportjának teljes IO-értéke meghaladja az 1500-as IOPS, akkor az azonos lekérdezés IO-értéke a 900 IOPS munkacsoport-korlátja alatt csökkenhet.

A [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) által visszaadott IOPS és átviteli sebesség (perc/maximális érték) korlátok/sapkák, nem pedig garanciák. Az erőforrás-szabályozás továbbá nem garantálja az adott tárolási késést. Egy adott felhasználói munkaterhelések esetében az elérhető legjobb késés, IOPS és teljesítmény nem csupán az IO-erőforrásokra vonatkozó irányítási korlátokra, hanem a felhasznált IO-méretek és a mögöttes tároló képességeire is érvényes. A SQL Server a 512 KB és 4 MB közötti méretekben eltérő IOs-t használ. A IOPS-korlátok betartatása érdekében minden i/o-érték a mérettől függetlenül fiókba kerül, az Azure Storage-ban tárolt adatfájlokkal rendelkező adatbázisok kivételével. Ebben az esetben a 256 KB-nál nagyobb IOs-nél több 256 KB-os IOs-ként van ellátva, hogy illeszkedjen az Azure Storage IO-nyilvántartásához.

Az Azure Storage-ban adatfájlokat használó alapszintű, standard és általános célú adatbázisok esetében `primary_group_max_io` az érték nem valósítható meg, ha egy adatbázis nem rendelkezik elegendő adatfájllal, hogy összesítse a IOPS, vagy ha az adatok nem egyenletesen vannak elosztva a fájlok között, vagy ha az alapul szolgáló Blobok teljesítménye az erőforrás-irányítási korlát alatt korlátozza a IOPS/átviteli sebességet. Hasonlóképpen, a gyakori tranzakció-véglegesítés által generált kis log IOs `primary_max_log_rate` esetében előfordulhat, hogy az érték nem érhető el az alapul szolgáló Azure Storage-blob IOPS-korlátja miatt.

A (z) `avg_data_io_percent` és `avg_log_write_percent`a (z) sys. [dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)és [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetekben jelentett erőforrás-kihasználtsági értékeket a rendszer a maximális erőforrás-irányítási korlátok százalékában számítja ki. Ezért ha az erőforrás-szabályozástól eltérő tényezők IOPS/átviteli sebességre vannak korlátozva, megtekintheti a IOPS/átviteli sebesség simítását és a késések növelését a munkaterhelés növekedésével, bár a jelentett erőforrás-kihasználtság a 100% alatti marad. 

Ha szeretné megtekinteni az IOPS, az átviteli sebességet és a késést az adatbázis-fájlban, használja a [sys. dm_io_virtual_file_stats ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) függvényt. Ez a függvény felfedi az összes IO-t az adatbázison, beleértve a háttérben lévő `avg_data_io_percent`i/o-t, amely nem része a felé, de az alapul szolgáló tárterület IOPS és átviteli sebességét használja, és hatással lehet a megfigyelt tárolási A függvény emellett felfedi a további késéseket is, amelyeket az i/o-erőforrások irányításával lehet `io_stall_queued_read_ms` bevezetni az olvasási és írási műveletekhez a és `io_stall_queued_write_ms` az oszlopokban.

### <a name="transaction-log-rate-governance"></a>Tranzakciós naplók arányának szabályozása

A tranzakciós napló arányának szabályozása Azure SQL Database folyamat, amellyel korlátozható a nagy mennyiségű betöltési arány a számítási feladatok, például a tömeges Beszúrás, a kiválasztás és az index-buildek esetében. Ezeket a korlátokat nyomon követik és érvényesítik a második szinten a naplózási rekordok generálásának arányában, az átviteli sebesség korlátozásával függetlenül attól, hogy hány IOs-t lehet kiadni az adatfájlokban.  A tranzakciónapló-generálási díjak jelenleg lineárisan méretezhetők egy hardvertől függő pontra, és a maximálisan megengedett naplózási sebesség 96 MB/s a virtuális mag beszerzési modellel. 

> [!NOTE]
> A tényleges fizikai IOs – tranzakciós naplófájlok nem szabályozottak vagy korlátozottak.

A naplózási díjszabás úgy van beállítva, hogy különböző forgatókönyvekben legyenek elérhetők és fenntarthatók, míg a teljes rendszer a felhasználói terhelésnek való lehető legkisebbre csökkentheti a funkcionalitását. A naplózási sebesség szabályozása biztosítja, hogy a tranzakciónapló biztonsági mentései a közzétett helyreállító SLA-ban maradjanak.  Ez a szabályozás a másodlagos replikák túlzott lemaradását is megakadályozza.

A naplóbejegyzések létrehozásakor a rendszer minden egyes műveletet kiértékel és értékel ki, hogy késleltetve legyen-e a maximálisan szükséges naplózási sebesség (MB/s/másodperc) fenntartása érdekében. A rendszer nem adja hozzá a késéseket a naplófájlok tárolóba történő kiürítéséhez, hanem a naplózási sebesség szabályozására a naplózási arány létrehozásakor.

A tényleges log-generálási sebességet a futtatási időszakban is befolyásolhatja a visszajelzési mechanizmusok, ami átmenetileg csökkenti az engedélyezett naplók sebességét, így a rendszer képes stabilizálni. A naplófájlok kezelése, amelyekkel elkerülhető, hogy a naplózási terület feltételeit és a rendelkezésre állási csoport replikálási mechanizmusa átmenetileg csökkentse a teljes rendszerkorlátot.

A naplózási arány kormányzója Traffic Shaping a következő várakozási típusoknál (amelyek a [sys. dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) és a [sys. dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) nézetekben vannak kitéve):

| Várakozás típusa | Megjegyzések |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Adatbázis korlátozása |
| POOL_LOG_RATE_GOVERNOR | Készlet korlátozása |
| INSTANCE_LOG_RATE_GOVERNOR | Példány szintjének korlátozása |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Visszajelzés-vezérlés, rendelkezésre állási csoport fizikai replikálása prémium/üzletileg kritikus nem kell tartani |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Visszajelzés-vezérlés, a díjszabás korlátozása, hogy elkerülje a naplón kívüli hely feltételeit |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | Geo-replikálási visszajelzések szabályozása, a naplózási sebesség korlátozása, hogy elkerülje a nagy adatkésést és a Geo-formátumú másodlagos zónák nem rendelkezésre állását|
|||

Ha egy, a kívánt skálázhatóságot akadályozó naplózási sebességre vonatkozó korlátot tapasztal, vegye figyelembe a következő lehetőségeket:
- Akár magasabb szolgáltatási szintet is igénybe veheti, hogy megszerezze a maximális 96 MB/s naplózási sebességet, vagy váltson át egy másik szolgáltatási szintre. A [nagy kapacitású](sql-database-service-tier-hyperscale.md) szolgáltatási szint 100 MB/s naplózási sebességet biztosít a választott szolgáltatási szinttől függetlenül.
- Ha a betöltés alatt álló adatmennyiség átmeneti, például egy ETL-folyamatban lévő átmeneti adatmennyiség, akkor a tempdb-be (amely minimálisan naplózva van). 
- Elemzési forgatókönyvek esetén helyezzen betöltést egy fürtözött oszlopcentrikus-táblázatba. Ez csökkenti a szükséges naplózási sebességet a tömörítés miatt. Ezzel a technikával növelheti a CPU-kihasználtságot, és csak olyan adatkészletekre alkalmazható, amelyek kihasználják a fürtözött oszlopcentrikus indexeket. 

## <a name="next-steps"></a>További lépések

- Az általános Azure-korlátokkal kapcsolatos információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md).
- További információ a DTU és a Edtu: [DTU és edtu](sql-database-purchase-models.md#dtu-based-purchasing-model).
- További információ a tempdb méretéről: [tempdb Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
