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
ms.openlocfilehash: da8c194b7911d2eeda8e0c903cb7412186aacfcb
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638255"
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

> [!NOTE]
> Ha további DTU/eDTU kvótát, virtuális mag kvótát vagy több kiszolgálót szeretne beszerezni az alapértelmezett értéknél, új támogatási kérést küldhet a "kvóta" típusú előfizetéshez a Azure Portalban. A DTU/eDTU kvóta és az adatbázis-korlát kiszolgálónként korlátozza a rugalmas készletek másodpercenkénti számát.

> [!IMPORTANT]
> Mivel az adatbázisok száma SQL Database kiszolgálón megközelíti a korlátot, a következő műveletek végezhetők el:
>
> - Növekvő késés a főadatbázison futó lekérdezések futtatásakor.  Ide tartoznak az erőforrás-kihasználtsági statisztikák, például a sys. resource_stats nézetei.
> - Növekvő késés a felügyeleti műveletekben és a portálon olyan nézőpontok, amelyek a kiszolgáló adatbázisainak számbavételét foglalják magukban.

### <a name="storage-size"></a>Tárterület mérete

Az önálló adatbázisok erőforrás-tárolási méreteit a [DTU-alapú erőforrás-korlátok](sql-database-dtu-resource-limits-single-databases.md) vagy a [virtuális mag-alapú erőforrás-](sql-database-vcore-resource-limits-single-databases.md) korlátok alapján, a tárterületre vonatkozó korlátok alapján, díjszabási szinten tekintheti meg.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Mi történik az adatbázis-erőforrások korlátainak elérésekor

### <a name="compute-dtus-and-edtus--vcores"></a>Számítás (DTU és Edtu/virtuális mag)

Ha az adatbázis számítási kihasználtsága (a DTU és a Edtu, illetve a virtuális mag alapján mérve) magas, a lekérdezés késése megnő, és a lekérdezések is időtúllépést okozhatnak. Ilyen körülmények között előfordulhat, hogy a szolgáltatás várólistára helyezi a lekérdezéseket, és erőforrásokat biztosít a végrehajtáshoz, mivel az erőforrások ingyenesek lesznek.
Ha magas számítási kihasználtságot tapasztal, a kockázatcsökkentő lehetőségek a következők:

- Az adatbázis vagy a rugalmas készlet számítási méretének növelése az adatbázis további számítási erőforrásokkal való biztosításához. Lásd: [önálló adatbázis-erőforrások méretezése](sql-database-single-database-scale.md) és [rugalmas készlet erőforrásainak méretezése](sql-database-elastic-pool-scale.md).
- A lekérdezések optimalizálása az egyes lekérdezések erőforrás-kihasználtságának csökkentése érdekében. További információ: a [lekérdezés finomhangolása/célzása](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Adattárolás

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

## <a name="resource-governance"></a>Erőforrások szabályozása

Az erőforrás-korlátok betartatása érdekében a Azure SQL Database a SQL Server [Resource Governoron](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)alapuló erőforrás-irányítási implementációt használ, amely SQL Server adatbázis-szolgáltatás Azure-beli futtatására lett módosítva és kiterjesztve. A szolgáltatás minden SQL Server példányán több [erőforráskészlet](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) és [munkaterhelési csoport](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)van, és az erőforrás-korlátok mind a készletben, mind a csoportok szintjén vannak beállítva, hogy [kiegyensúlyozott adatbázis-szolgáltatást](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/)nyújtsanak. A felhasználói munkaterhelés és a belső munkaterhelések külön erőforrás-készletbe és munkaterhelési csoportba vannak besorolva. Az elsődleges és olvasható másodlagos replikák (beleértve a földrajzi replikákat is) felhasználói terhelése a `SloSharedPool1` erőforráskészlet és `UserPrimaryGroup.DBId[N]` munkaterhelés-csoportba tartozik, ahol a `N` az adatbázis-azonosító értékének. Emellett több erőforráskészlet és munkaterhelés-csoport is található a különböző belső munkaterhelésekhez.

Amellett, hogy a Resource Governor használatával szabályozza az erőforrásokat a SQL Server folyamaton belül, Azure SQL Database a Windows- [feladatok objektumait](https://docs.microsoft.com/windows/win32/procthread/job-objects) is használja a feldolgozási szintű erőforrás-szabályozáshoz, valamint a Windows [Fájlkiszolgálói erőforrás-kezelőt (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) a tárolási kvóta kezeléséhez.

A Azure SQL Database erőforrás-szabályozás hierarchikus jellegű. Fentről lefelé a korlátokat az operációs rendszer erőforrás-irányítási mechanizmusai és Resource Governor, majd az erőforráskészlet szintjén kell kikényszeríteni az operációsrendszer-szinten és a tárolási kötet szintjén, az Resource Governor használatával, majd a munkaterhelés csoport szintjén. Resource Governor. Az aktuális adatbázisra vagy rugalmas készletre vonatkozó erőforrás-irányítási korlátok a [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) nézetben vannak felszínben. 

### <a name="data-io-governance"></a>Adatio-irányítás

Az adatio-szabályozás egy olyan folyamat, Azure SQL Database az olvasási és írási fizikai IO-t az adatbázisok adatfájljaira korlátozza. Az egyes IOPS korlátokat úgy állítjuk be, hogy a lehető legkisebbre csökkentsék a "zajos szomszéd" hatást, hogy az erőforrás-elosztás méltányos legyen a több-bérlős szolgáltatásban, és a mögöttes hardver és tárterület képességein belül maradjon.

Az önálló adatbázisok esetében a munkaterhelési csoportra vonatkozó korlátokat a rendszer az adatbázisra vonatkozó összes összes tárterületre alkalmazza, az erőforrás-készletre vonatkozó korlátozások pedig az adott SQL Server példányon lévő összes adatbázisra vonatkoznak, beleértve a `tempdb` adatbázist is. A rugalmas készletek esetében a munkaterhelési csoport korlátai a készlet minden egyes adatbázisára érvényesek, míg az erőforráskészlet-korlát a teljes rugalmas készletre vonatkozik, beleértve a `tempdb` adatbázist is, amely a készletben lévő összes adatbázis között meg van osztva. Az erőforrás-készlet korlátai általában nem valósíthatók meg egy adatbázison (akár egyetlen, akár készletezett) keresztül, mert a munkaterhelés-csoport korlátai alacsonyabbak az erőforráskészlet korlátainál, és hamarabb korlátozzák a IOPS/átviteli sebességet. A készletre vonatkozó korlátokat azonban a több adatbázisra vonatkozóan is elérheti ugyanazon a SQL Server-példányon.

Ha például egy lekérdezés az i/o-erőforrás szabályozása nélkül 1000 IOPS hoz létre, de a munkaterhelési csoport maximális IOPS értéke 900 IOPS, a lekérdezés nem fog tudni többet létrehozni 900 IOPS. Ha azonban az erőforráskészlet maximális IOPS-korlátja 1500 IOPS, és az erőforráskészlet összes munkaterhelési csoportjának teljes IO-értéke meghaladja az 1500-as IOPS, akkor az azonos lekérdezés IO-értéke a 900 IOPS munkacsoport-korlátja alatt csökkenhet.

A [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) által visszaadott IOPS és átviteli sebesség (perc/maximális érték) korlátok/sapkák, nem pedig garanciák. Az erőforrás-szabályozás továbbá nem garantálja az adott tárolási késést. Egy adott felhasználói munkaterhelések esetében az elérhető legjobb késés, IOPS és teljesítmény nem csupán az IO-erőforrásokra vonatkozó irányítási korlátokra, hanem a felhasznált IO-méretek és a mögöttes tároló képességeire is érvényes. A SQL Server a 512 KB és 4 MB közötti méretekben eltérő IOs-t használ. A IOPS-korlátok betartatása érdekében minden i/o-érték a mérettől függetlenül fiókba kerül, az Azure Storage-ban tárolt adatfájlokkal rendelkező adatbázisok kivételével. Ebben az esetben a 256 KB-nál nagyobb IOs-nél több 256 KB-os IOs-ként van ellátva, hogy illeszkedjen az Azure Storage IO-nyilvántartásához.

Az Azure Storage-ban adatfájlokat használó alapszintű, standard és általános célú adatbázisok esetében előfordulhat, hogy a `primary_group_max_io` érték nem érhető el, ha egy adatbázis nem rendelkezik elegendő adatfájllal, hogy összesítse a IOPS, vagy ha az adatok nem egyenletesen vannak elosztva a fájlok között, vagy ha az alapul szolgáló Blobok teljesítményi szintje az erőforrás-irányítási korlát alatti IOPS/átviteli sebességet korlátozza. Hasonlóképpen, a gyakori tranzakció-véglegesítés által generált kis log IOs esetén a `primary_max_log_rate` érték nem valósítható meg az alapul szolgáló Azure Storage-blob IOPS-korlátja miatti munkaterheléssel.

A [sys. dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys. resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)és [sys. elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetekben jelentett erőforrás-kihasználtsági értékek (például `avg_data_io_percent` és `avg_log_write_percent`) a maximális erőforrás-irányítási korlátok százalékában vannak kiszámítva. Ezért ha az erőforrás-szabályozástól eltérő tényezők IOPS/átviteli sebességre vannak korlátozva, megtekintheti a IOPS/átviteli sebesség simítását és a késések növelését a munkaterhelés növekedésével, bár a jelentett erőforrás-kihasználtság a 100% alatti marad. 

Ha szeretné megtekinteni az IOPS, az átviteli sebességet és a késést az adatbázis-fájlban, használja a [sys. dm_io_virtual_file_stats ()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) függvényt. Ez a függvény felfedi az összes i/o-t az adatbázison, beleértve a háttér i/o-t, amely nem a `avg_data_io_percent`felé van elfoglalva, de a IOPS és a mögöttes tároló átviteli sebességét használja, és hatással lehet a A függvény emellett felfedi a további késéseket is, amelyeket az i/o-erőforrások irányításával lehet bevezetni az olvasási és írási műveletekhez, a `io_stall_queued_read_ms` és `io_stall_queued_write_ms` oszlopokban.

### <a name="transaction-log-rate-governance"></a>Tranzakciós naplók arányának szabályozása

A tranzakciós napló arányának szabályozása Azure SQL Database folyamat, amellyel korlátozható a nagy mennyiségű betöltési arány a számítási feladatok, például a tömeges Beszúrás, a kiválasztás és az index-buildek esetében. Ezeket a korlátokat nyomon követik és érvényesítik a második szinten a naplózási rekordok generálásának arányában, az átviteli sebesség korlátozásával függetlenül attól, hogy hány IOs-t lehet kiadni az adatfájlokban.  A tranzakciónapló-generálási díjak jelenleg lineárisan méretezhetők egy hardvertől függő pontra, és a maximálisan megengedett naplózási sebesség 96 MB/s a virtuális mag beszerzési modellel. 

> [!NOTE]
> A tényleges fizikai IOs – tranzakciós naplófájlok nem szabályozottak vagy korlátozottak.

A naplózási díjszabás úgy van beállítva, hogy különböző forgatókönyvekben legyenek elérhetők és fenntarthatók, míg a teljes rendszer a felhasználói terhelésnek való lehető legkisebbre csökkentheti a funkcionalitását. A naplózási sebesség szabályozása biztosítja, hogy a tranzakciónapló biztonsági mentései a közzétett helyreállító SLA-ban maradjanak.  Ez a szabályozás a másodlagos replikák túlzott lemaradását is megakadályozza.

A naplóbejegyzések létrehozásakor a rendszer minden egyes műveletet kiértékel és értékel ki, hogy késleltetve legyen-e a maximálisan szükséges naplózási sebesség (MB/s/másodperc) fenntartása érdekében. A rendszer nem adja hozzá a késéseket a naplófájlok tárolóba történő kiürítéséhez, hanem a naplózási sebesség szabályozására a naplózási arány létrehozásakor.

A tényleges log-generálási sebességet a futtatási időszakban is befolyásolhatja a visszajelzési mechanizmusok, ami átmenetileg csökkenti az engedélyezett naplók sebességét, így a rendszer képes stabilizálni. A naplófájlok kezelése, amelyekkel elkerülhető, hogy a naplózási terület feltételeit és a rendelkezésre állási csoport replikálási mechanizmusa átmenetileg csökkentse a teljes rendszerkorlátot.

A log Rate kormányzó Traffic Shaping a következő várakozási típusok (a [sys. dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV) szerint van felszínben:

| Várakozás típusa | Megjegyzések |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Adatbázis korlátozása |
| POOL_LOG_RATE_GOVERNOR | Készlet korlátozása |
| INSTANCE_LOG_RATE_GOVERNOR | Példány szintjének korlátozása |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Visszajelzés-vezérlés, rendelkezésre állási csoport fizikai replikálása prémium/üzletileg kritikus nem kell tartani |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Visszajelzés-vezérlés, a díjszabás korlátozása, hogy elkerülje a naplón kívüli hely feltételeit |
|||

Ha egy, a kívánt skálázhatóságot akadályozó naplózási sebességre vonatkozó korlátot tapasztal, vegye figyelembe a következő lehetőségeket:
- Akár magasabb szolgáltatási szintet is igénybe veheti, hogy a maximális 96 MB/s naplózási sebesség legyen elérhető. 
- Ha a betöltés alatt álló adatmennyiség átmeneti, például egy ETL-folyamatban lévő átmeneti adatmennyiség, akkor a tempdb-be (amely minimálisan naplózva van). 
- Elemzési forgatókönyvek esetén helyezzen betöltést egy fürtözött oszlopcentrikus-táblázatba. Ez csökkenti a szükséges naplózási sebességet a tömörítés miatt. Ezzel a technikával növelheti a CPU-kihasználtságot, és csak olyan adatkészletekre alkalmazható, amelyek kihasználják a fürtözött oszlopcentrikus indexeket. 

## <a name="next-steps"></a>Következő lépések

- Az általános Azure-korlátokkal kapcsolatos információkért lásd: [Azure-előfizetések és-szolgáltatások korlátai, kvótái és megkötései](../azure-resource-manager/management/azure-subscription-service-limits.md).
- További információ a DTU és a Edtu: [DTU és edtu](sql-database-purchase-models.md#dtu-based-purchasing-model).
- További információ a tempdb méretéről: [tempdb Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
