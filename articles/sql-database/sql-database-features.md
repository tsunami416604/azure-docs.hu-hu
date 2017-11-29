---
title: "Az Azure SQL Database funkcióinak áttekintése | Microsoft Docs"
description: "Ez az oldal áttekintést nyújt az Azure SQL Database logikai kiszolgálóiról és adatbázisairól, valamint funkciótámogatási mátrixot is tartalmaz, hivatkozásokkal az egyes felsorolt funkcióhoz."
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d1a46fa4-53d2-4d25-a0a7-92e8f9d70828
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Active
ms.date: 10/25/2017
ms.author: carlrab
ms.openlocfilehash: dd78b1a8703f2e6aaa7854a7b3cb774d014e7270
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2017
---
# <a name="azure-sql-database-features"></a>Az Azure SQL Database funkciói

Az Azure SQL-adatbázis egy közös kódbázis osztja meg az SQL Server és az adatbázis szintjén támogatja a legtöbb a szolgáltatást. Azure SQL Database és SQL Server közötti fő funkció különbségek vannak a példány szintjén. 

Az Azure SQL Database funkcióinak körét folyamatosan bővítjük. Ezért javasoljuk, hogy látogassa meg az Azure Szolgáltatási hírek webhelyét, és használja a szűrőket:

* Szűrjön [SQL Database szolgáltatásra](https://azure.microsoft.com/updates/?service=sql-database).
* Szűrjön az általános elérhetőséggel kapcsolatos [bejelentésekre](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) az SQL Database funkcióira vonatkozóan.

## <a name="sql-server-and-sql-database-feature-support"></a>SQL Server és SQL-adatbázis által nyújtott szolgáltatások támogatásáról

A következő táblázat az SQL Server főbb szolgáltatásokat sorolja fel, és minden adott szolgáltatással támogatja-e, és a szolgáltatásra vonatkozó további információkra mutató hivatkozás nyújt információt. A meglévő adatbázis-megoldások áttelepítése során figyelembe veendő Transact-SQL különbségekről lásd: [Transact-SQL különbségek feloldása az SQL Database-be végzett áttelepítések során](sql-database-transact-sql-information.md).


| **SQL Server szolgáltatás** | **Az Azure SQL-adatbázis támogatott** | 
| --- | --- |  
| [Mindig titkosítja.](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Igen – lásd: [tanúsítványtároló](sql-database-always-encrypted.md) és [kulcstároló](sql-database-always-encrypted-azure-key-vault.md)|
| [AlwaysOn rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | Minden adatbázis magas rendelkezésre állású része. Vész-helyreállítási ismertet [az Azure SQL Database üzletmenet áttekintése](sql-database-geo-replication-overview.md) |
| [Egy adatbázis csatolása](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Nem |
| [Alkalmazási szerepkörök](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Igen |
|[Naplózás](/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Igen](sql-database-auditing.md)|
| [Automatikus hangolása](/sql/relational-databases/automatic-tuning/automatic-tuning)| [Igen](sql-database-automatic-tuning.md)|
| [BACPAC-fájl (Exportálás)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Igen – lásd: [SQL adatbázis exportálása](sql-database-export.md) |
| [BACPAC-fájl (importálás)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Igen – lásd: [SQL adatbázis importálása](sql-database-import.md) |
| [Biztonsági MENTÉSI parancs](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Nem - látható [automatikus biztonsági mentés](sql-database-automated-backups.md) |
| [Beépített funkciók](https://docs.microsoft.com/sql/t-sql/functions/functions) | Most – tekintse meg az egyes funkciók |
| [Adatrögzítés módosítása](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Nem |
| [Változások követése](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Igen |
| [Rendezés utasítások](https://docs.microsoft.com/sql/t-sql/statements/collations) | Igen |
| [Oszlopcentrikus indexek](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Igen – [csak a Premium edition](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| [Közös nyelvi futtatókörnyezet (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Nem |
| [Tartalmazott adatbázisok](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Igen |
| [Tartalmazott felhasználók](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Igen |
| [Attribútumfolyam nyelvi kulcsszavak ellenőrzése](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Igen |
| [Kereszt-adatbázis-lekérdezések](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | Részben – lásd: [Rugalmas lekérdezések](sql-database-elastic-query-overview.md) |
| [A kurzorok](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Igen | 
| [Adatok tömörítése](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Igen |
| [Az adatbázisbeli levelezés](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Nem |
| [Az adatbázis-tükrözés](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Nem |
| [Adatbázis-konfigurációs beállításokat](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Igen |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Nem |
| [Adatbázis-pillanatképek](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Nem |
| [Adattípusok](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Igen |  
| [DBCC utasítások](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Most – tekintse meg az egyes utasítások |
| [DDL-utasításokban](https://docs.microsoft.com/sql/t-sql/statements/statements) | Igen |
| [DDL-eseményindítók](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Csak az adatbázis |
| [Az elosztott tranzakciók – az MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Nem - látható [rugalmas tranzakciók](sql-database-elastic-transactions-overview.md) |
| [A DML-utasítások](https://docs.microsoft.com/sql/t-sql/queries/queries) | Igen |
| [A DML-eseményindítók](https://docs.microsoft.com/en-us/sql/relational-databases/triggers/create-dml-triggers) | Most – tekintse meg az egyes utasítások | 
| [DMV-k](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Néhány - tekintse meg az egyes dinamikus felügyeleti nézetek |
|[Dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking)|[Igen](sql-database-dynamic-data-masking-get-started.md)|
| [Eseményértesítések](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Nem - látható [riasztások](sql-database-insights-alerts-portal.md) |
| [Kifejezések](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Igen |
| [Bővített események](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Egyes - láthatnak [kiterjesztett események az SQL-adatbázis](sql-database-xevent-db-diff-from-svr.md) |
| [Bővített tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Nem |
| [Fájlok és fájlcsoportok](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Csak az elsődleges fájl csoport |
| [A FileStream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Nem |
| [A teljes szöveges keresés](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | Külső szóhatároló nem támogatottak. |
| [Functions](https://docs.microsoft.com/sql/t-sql/functions/functions) | Most – tekintse meg az egyes funkciók |
| [Graph feldolgozása](/sql/relational-databases/graphs/sql-graph-overview) | Igen |
| [Memóriabeli optimalizálás](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Igen – [csak a Premium edition](sql-database-in-memory.md) |
| [JSON-adatok támogatása](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | Igen |
| [Nyelvi elemei](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Most – tekintse meg az egyéni elemekké |  
| [Csatolt kiszolgálók](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nem - látható [rugalmas lekérdezés](sql-database-elastic-query-horizontal-partitioning.md) |
| [Naplóküldés](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | Minden adatbázis magas rendelkezésre állású része. Vész-helyreállítási ismertet [az Azure SQL Database üzletmenet áttekintése](sql-database-geo-replication-overview.md) |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Nem |
| [A tömeges importálással minimális naplózás](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Nem |
| [Rendszeradatok módosítása](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Nem |
| [Online indexműveleteket](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Igen |
| [Operátorok](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Most – tekintse meg az egyes operátorok |
| [Időponthoz kötött adatbázis visszaállítás](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Igen – lásd: [SQL-adatbázis helyreállítása](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [A Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Nem |
| [Csoportházirend-alapú felügyelet](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Nem |
| [Predikátumok](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Igen |
| [R-szolgáltatások](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Előzetes; Lásd: [What's new in gépi tanulás](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  |
| [Erőforrás-vezérlő](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Nem |
| [A RESTORE utasítás](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Nem | 
| [Adatbázis visszaállítása biztonsági másolatból](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Tekintse meg a beépített biztonsági mentésekből csak - [SQL-adatbázis helyreállítása](sql-database-recovery-using-backups.md) |
| [Sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Igen |
| [A szemantikai keresése](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Nem |
| [Sorozatszámok száma](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Igen |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Nem |
| [Kiszolgálók konfigurációs beállításai](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Nem |
| [Set utasítás](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Most – tekintse meg az egyes utasítások 
| [Térbeli](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Igen |
| [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Nem - látható [rugalmas feladatok](sql-database-elastic-jobs-getting-started.md) |
| [Az SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Lásd: [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [SQL Server-naplózás](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Nem - látható [SQL Database auditing](sql-database-auditing.md) |
| [Az SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Igen – lásd: [növekedési és shift SQL Server Integration Services munkaterhelések a felhőbe](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview) |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Igen |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Nem - látható [kiterjesztett események](sql-database-xevent-db-diff-from-svr.md) |
| [SQL Server-replikáció](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Kizárólag tranzakciós és pillanatkép-replikációs előfizetők](sql-database-cloud-migrate.md) |
| [Az SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Nem |
| [Tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Igen |
| [A rendszerszintű tárolt funkciók](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Néhány - funkciók lásd: |
| [A rendszerszintű tárolt eljárásokra](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Néhány - egyéni tárolt eljárásokat lásd: |
| [Rendszertáblák](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Néhány – tekintse meg az egyes táblák |
| [Rendszer katalógusnézetekre](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Egyes - tekintse meg az egyes nézetek |
| [tábla particionálásával](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Igen – csak az elsődleges fájlcsoport |
| [ideiglenes táblákkal](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | Helyi és adatbázis-hatóköre globális ideiglenes táblák csak |
| [Historikus táblák](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | Igen |
| [Változók](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Igen | 
| [Az átlátható adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Igen |
| [Windows Server feladatátvételi fürtszolgáltatás](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Minden adatbázis magas rendelkezésre állású része. Vész-helyreállítási ismertet [az Azure SQL Database üzletmenet áttekintése](sql-database-geo-replication-overview.md) |
| [XML-index](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Igen |

## <a name="next-steps"></a>Következő lépések

- Az Azure SQL Database szolgáltatással kapcsolatos tudnivalók: [Mi az SQL Database?](sql-database-technical-overview.md)
- A Transact-SQL támogatásával és eltéréseivel kapcsolatos tudnivalókat lásd: [Transact-SQL különbségek feloldása az SQL Database-be végzett áttelepítések során](sql-database-transact-sql-information.md).
