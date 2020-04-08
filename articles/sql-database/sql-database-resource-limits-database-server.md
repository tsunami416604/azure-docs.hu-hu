---
title: Az Azure SQL Database erőforráskorlátai | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az Azure SQL Database erőforráskorlátok egyetlen adatbázisok és rugalmas készletek. Azt is tájékoztatást nyújt, hogy mi történik, ha ezeket az erőforrás-korlátokat eléri vagy túllépi.
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
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804826"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>SQL Database erőforráskorlátok és erőforrás-szabályozás

Ez a cikk áttekintést nyújt az SQL Database erőforráskorlátairól egy olyan SQL Database-kiszolgálóesetében, amely egyetlen adatbázist és rugalmas készletet kezel. Tájékoztatást nyújt arról, hogy mi történik, ha ezeket az erőforráskorlátokat eléri vagy túllépi, és ismerteti a korlátok érvényesítéséhez használt erőforrás-irányítási mechanizmusokat.

> [!NOTE]
> A felügyelt példányok korlátairól lásd: [SQL Database erőforráskorlátok a felügyelt példányok számára.](sql-database-managed-instance-resource-limits.md)

## <a name="maximum-resource-limits"></a>Maximális erőforrás-korlátok

| Erőforrás | Korlát |
| :--- | :--- |
| Adatbázisok kiszolgálónként | 5000 |
| Előfizetésenkénti kiszolgálók alapértelmezett száma bármely régióban | 20 |
| Kiszolgálónként maximális kiszolgálók száma előfizetésenként bármely régióban | 200 |  
| DTU / eDTU kvóta kiszolgálónként | 54,000 |  
| virtuálismag-kvóta kiszolgálónként/példányonként | 540 |
| Készletek maximális feleskiszolgálónként | DIT-ek vagy virtuális magok száma korlátozza. Ha például minden készlet 1000 DT-, akkor egy kiszolgáló 54 készletet támogathat.|
|||

> [!IMPORTANT]
> Ahogy az adatbázisok száma megközelíti az SQL Database-kiszolgálónkénti korlátot, a következők fordulhatnak elő:
>
> - Növekvő késés a lekérdezések futtatása a fő adatbázisban.  Ez magában foglalja az erőforrás-kihasználtsági statisztikák nézeteit, például a sys.resource_stats.
> - Növekvő késés a felügyeleti műveletekben és a kiszolgálón lévő adatbázisok számbavételét magában foglaló portálnézetek renderelése.

> [!NOTE]
> Ha az alapértelmezett nél több DTU/eDTU-kvótát, virtuálismag-kvótát vagy az alapértelmezett nél több kiszolgálót szeretne beszerezni, küldjön be egy új támogatási kérelmet az Azure Portalon. További információ: [Request kvóta emelése az Azure SQL Database.](quota-increase-request.md)

### <a name="storage-size"></a>Tárhely mérete

Az egyes adatbázisok erőforrás-tárolási méretek, tekintse meg a [DTU-alapú erőforráskorlátok](sql-database-dtu-resource-limits-single-databases.md) vagy [virtuálismag-alapú erőforrás-korlátok](sql-database-vcore-resource-limits-single-databases.md) a tárolási méret korlátok tarifaszinten.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Mi történik az adatbázis-erőforrások korlátainak elérésekor?

### <a name="compute-dtus-and-edtus--vcores"></a>Számítás (DVO-k és eDVO-k / virtuális magok)

Amikor az adatbázis-számítási kihasználtság (DU-kkal és eDVO-kkal vagy virtuális magokkal mérve) magasra válik, a lekérdezéskések száma nő, és a lekérdezések időtúllépésre is képesek. Ilyen körülmények között a lekérdezések várólistára kerülhetnek a szolgáltatás által, és erőforrásokat biztosítanak a végrehajtáshoz, amint az erőforrások szabaddá válnak.
Ha nagy számítási kihasználtsítással találkozik, a kockázatcsökkentési lehetőségek a következők:

- Az adatbázis vagy a rugalmas készlet számítási méretének növelése, hogy az adatbázis több számítási erőforrást biztosítson. Lásd: [Egyetlen adatbázis-erőforrások méretezése](sql-database-single-database-scale.md) és [rugalmas készleterőforrások méretezése.](sql-database-elastic-pool-scale.md)
- A lekérdezések optimalizálása az egyes lekérdezések erőforrás-kihasználtságának csökkentése érdekében. További információ: [Lekérdezéshangolás/Tippelés.](sql-database-performance-guidance.md#query-tuning-and-hinting)

### <a name="storage"></a>Storage

Amikor a felhasznált adatbázisterület eléri a maximális méretkorlátot, az adatméretet növelő adatbázis beszúrása és frissítése sikertelen, és az ügyfelek [hibaüzenetet](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md)kapnak. A SELECT és a DELETE utasítások továbbra is sikeresek.

Nagy helykihasználás esetén a kockázatcsökkentési lehetőségek a következők:

- Az adatbázis vagy a rugalmas készlet maximális méretének növelése vagy további tárhely hozzáadása. Lásd: [Egyetlen adatbázis-erőforrások méretezése](sql-database-single-database-scale.md) és [rugalmas készleterőforrások méretezése.](sql-database-elastic-pool-scale.md)
- Ha az adatbázis egy rugalmas készletben van, akkor az adatbázis áthelyezhető a készleten kívülre, hogy a tárhely ne legyen megosztva más adatbázisokkal.
- Adatbázis zsugorítása a fel nem használt terület visszaszerzéséhez. További információ: [Fájlterület kezelése az Azure SQL Database-ben](sql-database-file-space-management.md)

### <a name="sessions-and-workers-requests"></a>Munkamenetek és dolgozók (kérések)

A munkamenetek és a dolgozók maximális számát a szolgáltatási szint és a számítási méret (DDO/eDDO vagy virtuális magok) határozza meg. Az új kérelmek et a rendszer elutasítja a munkamenet- vagy munkavégző korlátok elérésekor, és az ügyfelek hibaüzenetet kapnak. Bár a rendelkezésre álló kapcsolatok számát az alkalmazás szabályozhatja, az egyidejű dolgozók számát gyakran nehezebb megbecsülni és szabályozni. Ez különösen igaz a csúcsterhelési időszakokban, amikor az adatbázis-erőforrás korlátok elérése kor, és a dolgozók felhalmozódnak a hosszabb ideig futó lekérdezések, nagy blokkoló láncok vagy a túlzott lekérdezési párhuzamosság miatt.

Ha magas munkamenettel vagy dolgozói kihasználtsítással találkozik, a kockázatcsökkentési lehetőségek a következők:

- Az adatbázis vagy a rugalmas készlet szolgáltatási szint vagy számítási méretének növelése. Lásd: [Egyetlen adatbázis-erőforrások méretezése](sql-database-single-database-scale.md) és [rugalmas készleterőforrások méretezése.](sql-database-elastic-pool-scale.md)
- A lekérdezések optimalizálása az egyes lekérdezések erőforrás-kihasználtságának csökkentése érdekében, ha a dolgozói használat növelésének oka a számítási erőforrások versengése. További információ: [Lekérdezéshangolás/Tippelés.](sql-database-performance-guidance.md#query-tuning-and-hinting)
- A [MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (maximális párhuzamossági fok) beállítás csökkentése.
- A lekérdezési munkaterhelés optimalizálása az előfordulások számának és a lekérdezésblokkolás időtartamának csökkentése érdekében.

### <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Erőforrás-felhasználás felhasználói munkaterhelések és belső folyamatok szerint

Az egyes adatbázisok ban a felhasználói munkaterhelések processzor- és memóriafelhasználása a [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) és [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) nézetekben, oszlopokban és `avg_cpu_percent` `avg_memory_usage_percent` oszlopokban található. Rugalmas készletek esetén a készletszintű erőforrás-felhasználás a [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetben jelent. A felhasználói számítási feladatok CPU-felhasználása is jelentett az `cpu_percent` Azure Monitor metrika, az egyes [adatbázisok](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) és rugalmas [készletek](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) a készlet szintjén.

Az Azure SQL Database számítási erőforrásokat igényel az alapvető szolgáltatási funkciók, például a magas rendelkezésre állás és a vészhelyreállítás, az adatbázis biztonsági mentése és visszaállítása, figyelés, Lekérdezési tároló, automatikus hangolás stb. A rendszer az [erőforrás-irányítási](#resource-governance) mechanizmusok használatával a belső folyamatok teljes erőforrásainak egy bizonyos korlátozott részét különíti el, így az erőforrások fennmaradó része elérhetővé válik a felhasználói munkaterhelések számára. Olyan esetekben, amikor a belső folyamatok nem használnak számítási erőforrásokat, a rendszer elérhetővé teszi azokat a felhasználói számítási feladatok számára.

A felhasználói munkaterhelések és belső folyamatok által a felhasználói munkaterhelések és a belső folyamatok teljes processzor- és memóriafelhasználását a [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) és [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) nézetekben, oszlopokban és `avg_instance_cpu_percent` `avg_instance_memory_percent` oszlopokban jelenti a rendszer. Ezeket az adatokat `sqlserver_process_core_percent` `sqlserver_process_memory_percent` is jelenti a és az Azure Monitor metrikák, [az egyes adatbázisok](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) és rugalmas [készletek](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) a készlet szintjén.

A legutóbbi erőforrás-felhasználás felhasználói munkaterhelések és belső folyamatok szerinti részletesebb bontása a [sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) és [sys.dm_resource_governor_workload_groups_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) nézetekben található. Az ezekben a nézetekben hivatkozott erőforráskészletekről és munkaterhelés-csoportokról az [Erőforrás-irányítás.](#resource-governance) Ezek a nézetek jelentést az erőforrás-kihasználtság a felhasználói számítási feladatok és a kapcsolódó erőforrás-készletek és munkaterhelés-csoportok adott belső folyamatok.

A teljesítményfigyelés és a hibaelhárítás összefüggésében fontos figyelembe`avg_cpu_percent`venni `cpu_percent`mind a **felhasználói processzorfogyasztást** , a`avg_instance_cpu_percent``sqlserver_process_core_percent`( , , mind a **teljes CPU-fogyasztást** a felhasználói munkaterhelések és a belső folyamatok ( , , . ) szerint.

**A felhasználói cpu-felhasználás** kiszámítása az egyes szolgáltatási célok felhasználói munkaterhelési korlátainak százalékában történik. A 100%-os **felhasználói cpu-kihasználtság** azt jelzi, hogy a felhasználói munkaterhelés elérte a szolgáltatás célkitűzésének korlátját. Ha azonban a **teljes CPU-felhasználás** eléri a 70-100%-os tartományt, a felhasználói munkaterhelés átviteli átlapítása és a lekérdezési késés növekedése látható, még akkor is, ha a jelentett **felhasználói CPU-felhasználás** jelentősen 100% alatt marad. Ez nagyobb valószínűséggel fordul elő, ha kisebb szolgáltatási célokat használ a számítási erőforrások mérsékelt elosztásával, de viszonylag intenzív felhasználói munkaterhelésekkel, például [sűrű rugalmas készletekben.](sql-database-elastic-pool-resource-management.md) Ez kisebb szolgáltatási célok esetén is előfordulhat, ha a belső folyamatok ideiglenesen további erőforrásokat igényelnek, például az adatbázis új kópiájának létrehozásakor.

Ha a **teljes CPU-fogyasztás** magas, a kockázatcsökkentési lehetőségek megegyeznek a korábban említett, és tartalmazza a szolgáltatás célja növelése és/vagy a felhasználói munkaterhelés optimalizálása.

## <a name="resource-governance"></a>Erőforrások szabályozása

Az erőforrás-korlátok kényszerítése érdekében az Azure SQL Database az SQL Server [Erőforrás-főkiszolgáló izolált](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)implementációján alapuló erőforrás-irányítási implementációt használ, amelyet az Azure-beli SQL Server adatbázis-szolgáltatás futtatásához módosítanak és bővítettek. A szolgáltatás minden SQL Server-példányán több [erőforráskészlet](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) és [munkaterhelés-csoport](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)van, és az erőforráskorlátok készlet- és csoportszinteken is vannak beállítva, hogy [kiegyensúlyozott adatbázis-szolgáltatásként biztosítson.](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/) A felhasználói munkaterhelés és a belső munkaterhelések külön erőforráskészletekbe és munkaterhelési csoportokba vannak besorolva. Az elsődleges és olvasható másodlagos replikák felhasználói munkaterhelése, beleértve a `SloSharedPool1` földrajzi `UserPrimaryGroup.DBId[N]` replikákat `N` is, az erőforráskészletbe és a számítási feladatok csoportjába van besorolva, ahol az adatbázis-azonosító értékét jelenti. Emellett több erőforrás-készletek és számítási feladatok csoportok különböző belső számítási feladatok.

Amellett, hogy az Erőforrás-szabályozót az SQL Server folyamaton belüli erőforrások szabályozására használja, az Azure SQL Database a Windows [feladatobjektumokat](https://docs.microsoft.com/windows/win32/procthread/job-objects) is használja a folyamatszintű erőforrás-szabályozáshoz, és a Windows [fájlkiszolgálói erőforrás-kezelőt (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) a tárolási kvóta kezeléséhez.

Az Azure SQL Database erőforrás-szabályozás hierarchikus jellegű. Fentről lefelé a korlátok az operációs rendszer szintjén és a tárolókötet szintjén az operációs rendszer erőforrás-irányítási mechanizmusai és az erőforrás-szabályozó, majd az erőforráskészlet szintjén az Erőforrás-kormányzó, majd a számítási feladatok csoport szintjén az Erőforrás-kormányzó használatával. Az aktuális adatbázisra vagy rugalmas készletre vonatkozó erőforrás-irányítási korlátok a [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) nézetben kerülnek felszínre. 

### <a name="data-io-governance"></a>Az adatok im-szabályozása

Az adatok i/o-szabályozása egy olyan folyamat az Azure SQL Database-ben, amely az olvasási és írási fizikai IO-adatokat egy adatbázis adatfájljaival korlátozza. Az IOPS-korlátok minden egyes szolgáltatási szinthez úgy vannak beállítva, hogy minimálisra csökkentsék a "zajos szomszéd" hatást, erőforrás-elosztási méltányosságot biztosítsanak a több-bérlős szolgáltatásban, és az alapul szolgáló hardver és tároló képességeiközött maradjanak.

Az egyes adatbázisok esetében a számítási feladatok csoportkorlátai az adatbázison alapuló összes tárolási IO-ra vonatkoznak, míg az erőforráskészlet-korlátok az összes tárolási IO-ra vonatkoznak az ugyanazon SQL Server-példány összes adatbázisa ellen, beleértve az `tempdb` adatbázist is. Rugalmas készletek, számítási feladatok csoport korlátok a készlet minden egyes adatbázisra vonatkoznak, míg `tempdb` az erőforráskészlet-korlát a teljes rugalmas készletre vonatkozik, beleértve az adatbázist is, amely a készlet összes adatbázisa között van megosztva. Általában az erőforráskészlet-korlátok nem érhetők el a számítási feladatok adatbázis ellen (egy- vagy készletezve), mert a számítási feladatok csoport korlátai alacsonyabbak, mint az erőforráskészlet-korlátok és az IOPS/átviteli por hamarabb korlátozza. A készletkorlátokat azonban a kombinált számítási feladatok ugyanazon SQL Server-példányon több adatbázissal is elérhetik.

Ha például egy lekérdezés 1000 IOPS-t hoz létre i/o-erőforrás-szabályozás nélkül, de a számítási feladatok csoport maximális IOPS-korvan beállítva 900 IOPS-értékre, a lekérdezés nem lesz képes 900-nél több IOPS-t generálni. Ha azonban az erőforráskészlet maximális IOPS-korlátja 1500 IOPS-értékre van állítva, és az erőforráskészlethez társított összes számításifeladat-csoport teljes IO-ja meghaladja az 1500 IOPS-t, akkor ugyanannak a lekérdezésnek az I/O-ja a 900 IOPS-os munkacsoport-korlát alá csökkenhet.

A [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) nézet által visszaadott IOPS és átviteli min/max értékek korlátok/nagybetűkként, nem pedig garanciaként működnek. Továbbá az erőforrás-szabályozás nem garantálja a konkrét tárolási késést. A legjobb elérhető késés, IOPS és átviteli egy adott felhasználói számítási feladatok nem csak az IO-erőforrások cégirányítási korlátok, hanem a használt I/O-méretek, valamint az alapul szolgáló tároló képességeit. Az SQL Server 512 KB és 4 MB közötti méretű IOs-t használ. Az IOPS-korlátozások érvényesítése céljából minden I/O-t a méretétől függetlenül figyelembe vesznek, kivéve az Azure Storage-ban adatfájlokkal rendelkező adatbázisokat. Ebben az esetben a 256 KB-nál nagyobb IOs-t több 256 KbI-ként számolják el, hogy igazodjanak az Azure Storage I/n-számlázáshoz.

Alapszintű, standard és általános célú adatbázisok, amelyek adatfájlokat `primary_group_max_io` az Azure Storage-ban, az érték nem érhető el, ha egy adatbázis nem rendelkezik elegendő adatfájlokkal, hogy összegzőmódon adja meg ezt a számú IOPS-t, vagy ha az adatok nem egyenletesen oszlanak el a fájlok között, vagy ha az alapul szolgáló blobok teljesítményszintje korlátozza az IOPS/átviteli kapacitást az erőforrás-irányítási korlát alatt. Hasonlóképpen a gyakori tranzakciós véglegesítés által létrehozott `primary_max_log_rate` kis log IOs esetén előfordulhat, hogy az érték nem érhető el egy számítási feladat miatt az alapul szolgáló Azure storage blob IOPS-korlát.

Az erőforrás-kihasználtsági értékek, `avg_data_io_percent` például a `avg_log_write_percent` [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)és [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) nézetekben jelentett értékek a maximális erőforrás-irányítási korlátok százalékában kerülnek kiszámításra. Ezért ha az erőforrás-szabályozás nem korlátozza az IOPS/átviteli áteresztőátviteli rendszer, akkor az IOPS/átviteli áteresztő átviteli összeolvasztás a munkaterhelés növekedésével növekszik, annak ellenére, hogy a jelentett erőforrás-kihasználtság 100% alatt marad. 

Az olvasási és írási IOPS, átviteli és késés adatbázisfájlonkénti megtekintéséhez használja a [sys.dm_io_virtual_file_stats()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) függvényt. Ez a függvény az összes i/o-t az adatbázishoz, beleértve a háttérben IO, amely nem veszik figyelembe, `avg_data_io_percent`de az IOPS és az alapul szolgáló tároló átviteli, és hatással lehet a megfigyelt tárolási késés. A függvény további késést is tartalmaz, amelyet az Io-erőforrások szabályozása az olvasási és írási műveletekhez vezethet be, a `io_stall_queued_read_ms` és `io_stall_queued_write_ms` az oszlopokban.

### <a name="transaction-log-rate-governance"></a>Tranzakciós naplóárfolyam-szabályozás

A tranzakciós napló díjának szabályozása egy olyan folyamat az Azure SQL Database-ben, amely korlátozza a számítási feladatok magas betöltési arányát, például a tömeges beszúrást, a SELECT INTO-t és az indexbuildeket. Ezeket a korlátokat a rendszer a másodperc alatti szinten követi nyomon és kényszeríti ki a naplórekord-létrehozás irásának sebességére, korlátozva az átviteli sebességet, függetlenül attól, hogy hány IOs adható ki az adatfájlokhoz.  A tranzakciónapló generálási díjai jelenleg lineárisan skáláznak egy hardverfüggő pontig, a maximális naplósebesség pedig 96 MB/s a virtuálismag-vásárlási modellel. 

> [!NOTE]
> A tranzakciós naplófájlok tényleges fizikai IOs-ei nincsenek szabályosan vagy korlátozva.

A naplózási arányok úgy vannak beállítva, hogy különböző esetekben elérhetők és fenntarthatók legyenek, míg a teljes rendszer képes fenntartani a funkcionalitását a felhasználói terhelésre gyakorolt minimális hatás mellett. A naplódíj-szabályozás biztosítja, hogy a tranzakciónapló biztonsági másolatai a közzétett helyreállíthatósági SLOS-okon belül maradjanak.  Ez a vállalatirányítási is megakadályozza a másodlagos replikák túlzott lemaradás.

Naplórekordok létrehozásakor minden művelet et kiértékel, és értékeli, hogy el kell-e halasztani a maximális kívánt naplósebesség (MB/s másodpercenkénti) fenntartása érdekében. A késések nem kerülnek hozzáadásra, amikor a naplórekordok kiürítése a tárolóba, hanem a naplódíj-szabályozás alkalmazása során naplósebesség generálása is.

A futásidőben előírt tényleges naplógenerálási sebességeket a visszacsatolási mechanizmusok is befolyásolhatják, ideiglenesen csökkentve a megengedett naplósebességet, hogy a rendszer stabilizálódhasson. Naplófájlterület-kezelés, elkerülve a naplóterület-feltételek és a rendelkezésre állási csoport replikációs mechanizmusainak futtatását, ideiglenesen csökkentheti a rendszer általános korlátait.

A naplósebesség-szabályozó forgalom alakítása a következő várakozási típusokon keresztül jelenik meg (a [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) és [sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql) nézetekben kitéve):

| Várakozás típusa | Megjegyzések |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Adatbázis korlátozása |
| POOL_LOG_RATE_GOVERNOR | Készlet korlátozása |
| INSTANCE_LOG_RATE_GOVERNOR | Példányszint-korlátozás |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Visszajelzésvezérlés, rendelkezésre állási csoport fizikai replikációja a prémium verzióban/üzleti legkritikusabb esetben nem tart lépést |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Visszacsatolás-vezérlés, a rönkterület-állapot elkerülésének korlátozása |
| HADR_THROTTLE_LOG_RATE_MISMATCHED_SLO | Georeplikációs visszajelzésvezérlés, a naplósebesség korlátozása a magas adatkésés és a geomásodlagos adatok elérhetetlensítésének elkerülése érdekében|
|||

Ha olyan naplósebesség-korlátot tapasztal, amely akadályozza a kívánt méretezhetőséget, vegye figyelembe a következő lehetőségeket:
- A maximális 96 MB/s-os naplózási sebesség eléréséhez vagy egy másik szolgáltatási szintre való váltásérdekében magasabb szolgáltatási szintre skálázható. A [nagy kapacitású](sql-database-service-tier-hyperscale.md) szolgáltatási szint 100 MB/s naplósebességet biztosít a választott szolgáltatási szinttől függetlenül.
- Ha a betöltendő adatok átmeneti, például egy ETL-folyamat átmeneti adatai, akkor betölthető tempdb (amely minimálisan naplózott). 
- Analitikus forgatókönyvek esetén töltse be a fürtözött oszlopcentrikus fedett táblába. Ez csökkenti a tömörítés miatti szükséges naplózási sebességet. Ez a módszer növeli a CPU-kihasználtságot, és csak olyan adatkészletekesetén alkalmazható, amelyek a fürtözött oszlopcentrikus indexek előnyeit élvezik. 

## <a name="next-steps"></a>További lépések

- Az általános Azure-korlátokról az [Azure-előfizetési és szolgáltatáskorlátok, kvóták és korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md)című témakörben talál.
- A DTUS-okról és az eDTO-król a [DTUS-ban és az eDTO-kban talál](sql-database-purchase-models.md#dtu-based-purchasing-model)további információt.
- A tempdb méretkorlátokról a [TempDB az Azure SQL Database ben című témakörben talál](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)további információt.
