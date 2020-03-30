---
title: Erőforrás-gazdálkodás sűrű rugalmas medencékben | Microsoft dokumentumok
description: Számítási erőforrások kezelése az Azure SQL rugalmas készletek számos adatbázissal.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: carlrab
ms.date: 03/13/2019
ms.openlocfilehash: 014f9edca1706c39930c6e48bb64cd8873bcace9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473727"
---
# <a name="resource-management-in-dense-elastic-pools"></a>Erőforrás-kezelés sűrű rugalmas készletekben

Az Azure SQL Database [rugalmas készletek](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool) költséghatékony megoldás számos különböző erőforrás-használattal rendelkező adatbázis kezelésére. A rugalmas készletben lévő összes adatbázis azonos erőforrásokat foglal el, például a PROCESSZORt, a memóriát, a munkaszálakat, a tárhelyet, a tempdb-t, feltételezve, hogy **a készletben lévő adatbázisoknak csak egy részhalmaza használja a számítási erőforrásokat egy adott időpontban.** Ez a feltételezés lehetővé teszi, hogy a rugalmas készletek költséghatékonyak legyenek. Ahelyett, hogy minden erőforrásért fizetne, az egyes adatbázisoknak esetleg szüksége lehet, az ügyfelek sokkal kisebb erőforráskészletet fizetnek, amely a készlet összes adatbázisa között van megosztva.

## <a name="resource-governance"></a>Erőforrások szabályozása

Az erőforrás-megosztás hoz a rendszer gondosan szabályozni erőforrás-használat minimalizálása érdekében a "zajos szomszéd" hatás, ahol a magas erőforrás-felhasználású adatbázis hatással van más adatbázisok ugyanabban a rugalmas készletben. Ugyanakkor a rendszernek elegendő erőforrást kell biztosítania az olyan funkciókhoz, mint a magas rendelkezésre állás és a vészhelyreállítás (HADR), a biztonsági mentés és helyreállítás, a figyelés, a Lekérdezési tároló, az Automatikus hangolás stb.

Az Azure SQL Database ezeket a célokat több erőforrás-irányítási mechanizmus használatával éri el, beleértve a Windows [feladatobjektumokat](https://docs.microsoft.com/windows/win32/procthread/job-objects) a folyamatszintű erőforrás-szabályozáshoz, a Windows [fájlkiszolgálói erőforrás-kezelőt (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) a tárolási kvóta kezeléséhez, valamint az SQL Server [Erőforrás-szabályozó](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) módosított és kibővített verzióját az erőforrás-szabályozás megvalósításához az Azure SQL Database-ben futó minden SQL Server-példányon belül.

A rugalmas készletek elsődleges tervezési célja, hogy költséghatékony legyen. Emiatt a rendszer szándékosan lehetővé teszi az ügyfelek számára, hogy _sűrű_ készleteket hozzanak létre, azaz az adatbázisok száma közeledik, vagy a maximálisan engedélyezett, de a számítási erőforrások mérsékelt elosztásával. Ugyanennek az oknál fogva a rendszer nem foglalja le az összes potenciálisan szükséges erőforrást a belső folyamatokszámára, de lehetővé teszi az erőforrások megosztását a belső folyamatok és a felhasználói munkaterhelések között.

Ez a megközelítés lehetővé teszi az ügyfelek számára, hogy sűrű rugalmas készleteket használjanak a megfelelő teljesítmény és jelentős költségmegtakarítás elérése érdekében. Azonban ha a számítási feladatok egy sűrű készletben lévő számos adatbázis ellen elég intenzív, erőforrás-versengés jelentőslesz. Az erőforrás-versengés csökkenti a felhasználói munkaterhelés teljesítményét, és negatívan befolyásolhatja a belső folyamatokat.

> [!IMPORTANT]
> A sok aktív adatbázissal rendelkező sűrű készletekben előfordulhat, hogy nem lehet növelni a készletben lévő adatbázisok számát a [DTU](sql-database-dtu-resource-limits-elastic-pools.md) és a [virtuális mag](sql-database-vcore-resource-limits-elastic-pools.md) rugalmas készletei számára dokumentált maximális értékig.
> 
> A sűrű készletekbe helyezhető adatbázisok száma erőforrás-versengési és teljesítményproblémák nélkül függ az egyidejűleg aktív adatbázisok számától és az egyes adatbázisok felhasználói munkaterhelései által a felhasználói munkaterhelések általi erőforrás-felhasználásától. Ez a szám idővel változhat a felhasználói munkaterhelések változásával.

Ha az erőforrások versengése sűrűn csomagolt készletben történik, az ügyfelek az alábbi műveletek közül választhatnak a művelet mérséklése érdekében:
- A lekérdezési munkaterhelés takarása az erőforrás-felhasználás csökkentése érdekében, vagy az erőforrás-felhasználás több adatbázis közötti elosztása az idő múlásával.
- Csökkentse a készlet sűrűségét azáltal, hogy egyes adatbázisokat áthelyez egy másik készletbe, vagy önálló adatbázisokat hoz.
- A készlet felskálázására több erőforrást kaphat.

Az utolsó két művelet megvalósításával kapcsolatos javaslatokért olvassa el a cikk későbbi [működési javaslatok](#operational-recommendations) című témakörét. Az erőforrás-versengés csökkentése mind a felhasználói munkaterhelés, mind a belső folyamatok számára előnyös, és lehetővé teszi a rendszer számára a várható szolgáltatási szint fenntartását.

## <a name="monitoring-resource-consumption"></a>Erőforrás-felhasználás figyelése

Az erőforrás-versengés miatti teljesítménycsökkenés elkerülése érdekében a sűrű rugalmas készleteket használó ügyfelek proaktív módon figyelhetik az erőforrás-felhasználást, és időben kell cselekedniük, ha az erőforrás-versengés növekedése elkezdi befolyásolni a számítási feladatokat. A folyamatos figyelés azért fontos, mert az erőforrás-használat egy készletben idővel változik, a felhasználói munkaterhelés változásai, az adatkötetek és a terjesztési változások, a készlet sűrűségének változása és az Azure SQL Database szolgáltatás változásai miatt. 

Az Azure SQL Database számos metrikát biztosít, amelyek relevánsak az ilyen típusú figyeléshez. Az egyes metrikák ajánlott átlagos értékének túllépése erőforrás-versengést jelez a készletben, és a korábban említett műveletek egyikével kell foglalkozni.

|Metrika neve|Leírás|Ajánlott átlagérték| 
|----------|--------------------------------|------------|
|`avg_instance_cpu_percent`|Az SQL Server folyamatának processzorkihasználtsága egy rugalmas készlethez társítva, az alapul szolgáló operációs rendszer alapján mérve. A [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) nézetben érhető el minden adatbázisban, és a `master` [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetben az adatbázisban. Ez a metrika is kivan adva az Azure Monitor, ahol a [neve](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `sqlserver_process_core_percent`, és megtekinthető az Azure Portalon. Ez az érték megegyezik az azonos rugalmas készletminden adatbázisában.|70% alatt. Alkalmi rövid tüskék akár 90% elfogadható lehet.|
|`max_worker_percent`|[A munkaszál]( https://docs.microsoft.com/sql/relational-databases/thread-and-task-architecture-guide) kihasználtsága. A készlet minden egyes adatbázisához, valamint magához a készlethez is biztosított. Az adatbázis szintjén a munkavégző szálak száma különböző korlátok at, és a készlet szintjén, ezért ez a metrika figyelése mindkét szinten ajánlott. A [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) nézetben érhető el minden adatbázisban, és a `master` [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetben az adatbázisban. Ez a metrika is kivan adva az Azure Monitor, ahol a [neve](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `workers_percent`, és megtekinthető az Azure Portalon.|80% alatt. Az akár 100%-os csúcsok a csatlakozási kísérletek és lekérdezések sikertelensét eredményezik.|
|`avg_data_io_percent`|IOPS-kihasználtság a fizikai IO olvasására és írására. A készlet minden egyes adatbázisához, valamint magához a készlethez is biztosított. Az iops-ok száma az adatbázis szintjén, és a készlet szintjén, ezért ez a metrika figyelése mindkét szinten ajánlott. A [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) nézetben érhető el minden adatbázisban, és a `master` [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetben az adatbázisban. Ez a metrika is kivan adva az Azure Monitor, ahol a [neve](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `physical_data_read_percent`, és megtekinthető az Azure Portalon.|80% alatt. Alkalmi rövid tüskék akár 100% elfogadható lehet.|
|`avg_log_write_percent`|Átviteli kihasználtsága tranzakciós napló írási IO. A készlet minden egyes adatbázisához, valamint magához a készlethez is biztosított. Az adatbázis szintjén a naplóátviteli teljesítmény különböző korlátok vannak, és a készlet szintjén, ezért ez a metrika figyelése mindkét szinten ajánlott. A [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) nézetben érhető el minden adatbázisban, és a `master` [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetben az adatbázisban. Ez a metrika is kivan adva az Azure Monitor, ahol a [neve](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `log_write_percent`, és megtekinthető az Azure Portalon. Ha ez a mutató közel 100%, az összes adatbázis-módosítás (INSERT, UPDATE, DELETE, MERGE utasítások, SELECT ... INTO, BULK INSERT stb.) lassabb lesz.|90% alatt. Alkalmi rövid tüskék akár 100% elfogadható lehet.|
|`oom_per_second`|A memóriaen kívüli (OOM) hibák aránya egy rugalmas készletben, amely a memórianyomás mutatója. Elérhető a [sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database?view=azuresqldb-current) nézetben. Lásd: [Példák](#examples) a minta lekérdezés kiszámításához ezt a metrikát.|0|
|`avg_storage_percent`|A tárolóhely kihasználása a rugalmas készlet szintjén. A [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetben `master` érhető el az adatbázisban. Ez a metrika is kivan adva az Azure Monitor, ahol a [neve](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) `storage_percent`, és megtekinthető az Azure Portalon.|80% alatt. Megközelítheti a 100%-os készlet adatnövekedés nélkül.|
|`tempdb_log_used_percent`|Tranzakciónapló-terület kihasználtsága az `tempdb` adatbázisban. Annak ellenére, hogy az egyik adatbázisban létrehozott ideiglenes objektumok `tempdb` nem láthatók az azonos rugalmas készlet más adatbázisaiban, az ugyanabban a készletben lévő összes adatbázis megosztott erőforrása. A készlet egyik adatbázisából `tempdb` indított hosszú ideig futó vagy tétlen tranzakció a tranzakciós napló nagy részét használhatja fel, és hibákat okozhat az ugyanabban a készletben lévő más adatbázisoklekérdezéseinél. Elérhető a [sys.dm_db_log_space_usage](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-log-space-usage-transact-sql) nézetben. Ez a metrika is kivan adva az Azure Monitor, és megtekinthető az Azure Portalon. Lásd: [Példák](#examples) a minta lekérdezés t a metrika aktuális értékének visszaadására.|50% alatt. Alkalmi tüskék akár 80%, elfogadható.|
|||

Ezeken a metrikákon kívül az Azure SQL Database egy olyan nézetet is biztosít, amely visszaadja a tényleges erőforrás-irányítási korlátokat, valamint további nézeteket, amelyek erőforráskészlet-szinten és a munkaterhelési csoport szintjén erőforrás-kihasználtsági statisztikákat adnak vissza.

|Nézet neve|Leírás|  
|-----------------|--------------------------------|  
|[sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database)|Az aktuális adatbázisban vagy rugalmas készletben az erőforrás-irányítási mechanizmusok által használt tényleges konfigurációs és kapacitásbeállításokat adja vissza.|
|[sys.dm_resource_governor_resource_pools](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-transact-sql)|Információt ad vissza az aktuális erőforráskészlet állapotáról, az erőforráskészletek aktuális konfigurációjáról és a göngyölt erőforráskészlet statisztikáiról.|
|[sys.dm_resource_governor_workload_groups](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-transact-sql)|Összegző számításifeladatok csoportstatisztikáját és a munkaterheléscsoport aktuális konfigurációját adja vissza. Ez a nézet az oszlopban található sys.dm_resource_governor_resource_pools-vel kapcsolható össze az `pool_id` erőforráskészlet adatainak lefelvételéhez.|
|[sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database)|Az elmúlt 32 perc erőforráskészlet-kihasználtsági statisztikáit adja vissza. Minden sor 20 másodperces intervallumot jelöl. Az `delta_` oszlopok az intervallum során az egyes statisztikák változását adják vissza.|
|[sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database)|Az elmúlt 32 perc számítási feladatcsoport-kihasználtsági statisztikáit adja vissza. Minden sor 20 másodperces intervallumot jelöl. Az `delta_` oszlopok az intervallum során az egyes statisztikák változását adják vissza.|
|||

Ezek a nézetek az erőforrás-kihasználtság figyelésére és az erőforrás-versengés közel valós idejű hibaelhárítására használhatók. Az elsődleges és olvasható másodlagos replikák felhasználói munkaterhelése, beleértve a `SloSharedPool1` földrajzi `UserPrimaryGroup.DBId[N]` replikákat `N` is, az erőforráskészletbe és a számítási feladatok csoportjába van besorolva, ahol az adatbázis-azonosító értékét jelenti.

Az aktuális erőforrás-kihasználtság figyelése mellett a sűrű készleteket használó ügyfelek egy külön adattárban is tárolhatják a korábbi erőforrás-kihasználtsági adatokat. Ezek az adatok prediktív elemzésben használhatók az erőforrás-kihasználtság korábbi és szezonális trendek alapján történő proaktív kezeléséhez.

## <a name="operational-recommendations"></a>Operatív ajánlások

**Hagyjon elegendő erőforrás-mozgásteret.** Ha az erőforrások versengése és a teljesítmény romlása történik, a kockázatcsökkentés magában foglalhatja egyes adatbázisok áthelyezése az érintett rugalmas készlet, vagy a készlet skálázása, akorábban említett. Ezek a műveletek azonban további számítási erőforrásokat igényelnek. Különösen a prémium szintű és az üzleti legkritikusabb készletek, ezek a műveletek az áthelyezett adatbázisok összes adatának átvitelét igényli, vagy a rugalmas készletben lévő összes adatbázishoz, ha a készlet fel skálázódik. Az adatátvitel hosszú és erőforrás-igényes művelet. Ha a készlet már nagy erőforrás-nyomás alatt van, maga az enyhítő művelet még tovább rontja a teljesítményt. Szélsőséges esetekben előfordulhat, hogy nem lehet megoldani az erőforrás-versengés adatbázis áthelyezése vagy készlet-scale-up, mert a szükséges erőforrások nem érhetők el. Ebben az esetben az érintett rugalmas készlet lekérdezési munkaterhelésének ideiglenes csökkentése lehet az egyetlen megoldás.

A sűrű készleteket használó ügyfeleknek szorosan figyelniük kell az erőforrás-kihasználtsági trendeket a korábban leírtak szerint, és enyhítő műveletet kell végrehajtaniuk, amíg a metrikák az ajánlott tartományokon belül maradnak, és még mindig elegendő erőforrás van a rugalmas készletben.

Az erőforrások kihasználtsága több tényezőtől függ, amelyek az egyes adatbázisok és rugalmas készletek időbeli változása. Optimális ár/teljesítmény arány elérése sűrű készletek folyamatos figyelést és kiegyensúlyozást igényel, azaz az adatbázisok áthelyezése a több kihasznált készletek kevésbé használt készletek, és új készletek létrehozása szükséges a megnövekedett munkaterhelés.

**Ne helyezze át a "forró" adatbázisokat**. Ha az erőforrás-versengés a készlet szintjén elsősorban a kis számú nagy mértékben kihasznált adatbázisok, csábító lehet áthelyezni ezeket az adatbázisokat egy kevésbé használt készlet, vagy azokat önálló adatbázisok. Ennek során azonban az adatbázis továbbra is nagy mértékben kihasznált, mert az áthelyezési művelet tovább rontja a teljesítményt, mind az áthelyezett adatbázis, mind a teljes készlet. Ehelyett várjon, amíg a magas kihasználtság csökken, vagy helyezze ni kevesebb használt adatbázisok helyett erőforrás-terhelés a készlet szintjén. A nagyon alacsony kihasználtsítású adatbázisok áthelyezése azonban ebben az esetben nem nyújt előnyt, mivel nem csökkenti jelentősen az erőforrás-kihasználtságot a készlet szintjén.

**Hozzon létre új adatbázisokat egy "karantén" készletben.** Olyan esetekben, amikor gyakran hoznak létre új adatbázisokat, például a bérlői adatbázis-modellt használó alkalmazásokat, fennáll annak a veszélye, hogy egy meglévő rugalmas készletbe helyezett új adatbázis váratlanul jelentős erőforrásokat fog felhasználni, és más adatbázisokat is érint. és a belső folyamatok a medencében. A kockázat csökkentése érdekében hozzon létre egy külön "karantén" készletet az erőforrások bőséges elosztásával. Ezt a készletet ismeretlen erőforrás-felhasználási mintákkal rendelkező új adatbázisokhoz használja. Miután egy adatbázis üzleti ciklusban, például egy hétig vagy egy hónapig ebben a készletben maradt, és ismert az erőforrás-felhasználás, áthelyezhető egy olyan készletbe, amely elegendő kapacitással rendelkezik a további erőforrás-használat befogadásához.

**Kerülje a túlságosan sűrű logikai szerverek**. Az Azure SQL Database logikai kiszolgálónként legfeljebb 5000 adatbázist [támogat.](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server) Rugalmas készleteket több ezer adatbázissal használó ügyfelek érdemes lehet több rugalmas készletet elhelyezni egyetlen kiszolgálón, az adatbázisok teljes számát a támogatott korlátig. A több ezer adatbázissal rendelkező logikai kiszolgálók azonban működési kihívásokat teremtenek. A kiszolgálón található összes adatbázis számbavételét igénylő műveletek , például a portál adatbázisainak megtekintése lassabblesz. A működési hibák, például a kiszolgálószintű bejelentkezések vagy a tűzfalszabályok helytelen módosítása nagyobb számú adatbázist érintenek. A logikai kiszolgáló véletlen törléséhez a Microsoft támogatási szolgálatának segítsége szükséges a törölt kiszolgálón lévő adatbázisok helyreállításához, és az összes érintett adatbázis hosszabb kimaradását okozza.

Azt javasoljuk, hogy a logikai kiszolgálónkénti adatbázisok számát a maximálisan támogatottnál alacsonyabb számra korlátozza. Sok esetben a kiszolgálónként akár 1000-2000 adatbázis használata optimális. A kiszolgáló véletlen törlésének valószínűségének csökkentése érdekében javasoljuk, hogy [helyezzen el törlési zárolást](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) a logikai kiszolgálón vagy annak erőforráscsoportján.

A múltban az adatbázisok ugyanazon logikai kiszolgálón lévő rugalmas készletekbe, illetve azok között történő áthelyezését magában foglaló bizonyos forgatókönyvek gyorsabbak voltak, mint az adatbázisok logikai kiszolgálók közötti áthelyezésekor. Jelenleg az összes adatbázis áthelyezése azonos sebességgel történik, függetlenül a forrás- és céllogikai kiszolgálótól.

## <a name="examples"></a>Példák

### <a name="monitoring-memory-utilization"></a>A memória kihasználtságának figyelése

Ez a lekérdezés `oom_per_second` kiszámítja az egyes erőforráskészletek metrikáját az elmúlt 32 percben. Ez a lekérdezés egy rugalmas készlet bármely adatbázisában végrehajtható.

```
SELECT pool_id,
       name AS resource_pool_name,
       IIF(name LIKE 'SloSharedPool%' OR name LIKE 'UserPool%', 'user', 'system') AS resource_pool_type,
       SUM(CAST(delta_out_of_memory_count AS decimal))/(SUM(duration_ms)/1000.) AS oom_per_second
FROM sys.dm_resource_governor_resource_pools_history_ex
GROUP BY pool_id, name
ORDER BY pool_id;
```
### <a name="monitoring-tempdb-log-space-utilization"></a>Naplóterület-kihasználtság figyelése `tempdb`

Ez a lekérdezés a `tempdb_log_used_percent` metrika aktuális értékét adja vissza. Ez a lekérdezés egy rugalmas készlet bármely adatbázisában végrehajtható.

```
SELECT used_log_space_in_percent AS tempdb_log_used_percent
FROM tempdb.sys.dm_db_log_space_usage;
```

## <a name="next-steps"></a>További lépések

* A rugalmas készletek bemutatása: [Rugalmas készletek segítségével kezelheti és skálázhatja több Azure SQL-adatbázisok.](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool)
* A lekérdezési számítási feladatok nak az erőforrás-kihasználtság csökkentése érdekében kapcsolatos további információkért [lásd: Figyelés és hangolás,]( https://docs.microsoft.com/azure/sql-database/sql-database-monitoring-tuning-index) [valamint Figyelés és teljesítményhangolás.](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview)
