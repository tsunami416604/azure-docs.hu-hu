---
title: "Az Azure SQL-adatbázis szolgáltatások összehasonlítása |} Microsoft Docs"
description: "Ez a cikk az Azure SQL Database és a felügyelt példányok funkcióit egymással, és az SQL Server hasonlítja össze."
services: sql-database
author: jovanpop-msft
ms.reviewer: bonova, carlrab
ms.service: sql-database
ms.topic: article
ms.date: 02/28/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: 11439a8bc10e616773068416b5a7def2f4becb36
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Szolgáltatások összehasonlítása: Azure SQL Database és SQL Server 

Az Azure SQL-adatbázis egy közös kódbázis osztja meg az SQL Server. Az Azure SQL Database által támogatott SQL Server funkcióit az Ön által létrehozott Azure SQL adatbázis típusától függ. Az Azure SQL Database, vagy létrehozhat egy adatbázist részeként egy [felügyelt példány](sql-database-managed-instance.md) (jelenleg a nyilvános előzetes verzió), vagy létrehozhat egy adatbázist, amely egy adatbázist vagy egy adatbázist, amely egy rugalmas készlet része. 

A Microsoft továbbra is hozzáadhat szolgáltatásokat az Azure SQL Database. Látogasson el az Azure a szolgáltatásfrissítések weblap a legújabb frissítések, ezek a szűrők használatával:

* Szűrjön [SQL Database szolgáltatásra](https://azure.microsoft.com/updates/?service=sql-database).
* Szűrjön az általános elérhetőséggel kapcsolatos [bejelentésekre](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) az SQL Database funkcióira vonatkozóan.

## <a name="sql-server-and-sql-database-feature-support"></a>SQL Server és SQL-adatbázis által nyújtott szolgáltatások támogatásáról

A következő táblázat az SQL Server főbb szolgáltatásokat sorolja fel, és információt nyújt a szolgáltatás részlegesen vagy teljesen támogatja-e, és a szolgáltatásra vonatkozó további információkra mutató hivatkozás. 

| **SQL Feature** | **Az Azure SQL-adatbázis támogatott** | **Felügyelt példány (előzetes verzió)** |
| --- | --- | --- |
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Igen – lásd: [tanúsítványtároló](sql-database-always-encrypted.md) és [kulcstároló](sql-database-always-encrypted-azure-key-vault.md) | Igen – lásd: [tanúsítványtároló](sql-database-always-encrypted.md) és [kulcstároló](sql-database-always-encrypted-azure-key-vault.md) |
| [AlwaysOn rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Magas rendelkezésre állású](sql-database-high-availability.md) megtalálható minden adatbázist. Vész-helyreállítási ismertet [az Azure SQL Database üzletmenet áttekintése](sql-database-business-continuity.md) | [Magas rendelkezésre állású](sql-database-high-availability.md) megtalálható minden adatbázist. Vész-helyreállítási ismertet [az Azure SQL Database üzletmenet áttekintése](sql-database-business-continuity.md) |
| [Egy adatbázis csatolása](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Nem | Nem |
| [Alkalmazási szerepkörök](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Igen | Igen |
|[Naplózás](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Igen](sql-database-auditing.md)| Igen – lásd: [különbségek naplózás](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [Az automatikus biztonsági mentés](sql-database-automated-backups.md) | Igen | Igen |
| [Automatikus hangolása (kényszerített)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Igen](sql-database-automatic-tuning.md)| [Igen](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [Automatikus hangolása (indexek)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Igen](sql-database-automatic-tuning.md)| Nem |
| [BACPAC-fájl (Exportálás)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Igen – lásd: [SQL adatbázis exportálása](sql-database-export.md) | Igen |
| [BACPAC-fájl (importálás)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Igen – lásd: [SQL adatbázis importálása](sql-database-import.md) | Igen |
| [Biztonsági MENTÉSI parancs](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Nem, csak rendszer által kezdeményezett automatikus biztonsági mentés - látható [automatikus biztonsági mentés](sql-database-automated-backups.md) | Rendszer által kezdeményezett automatikus biztonsági mentés és a felhasználó által kezdeményezett csak másolatot biztonsági mentések – lásd [különbségek biztonsági mentése](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Beépített funkciók](https://docs.microsoft.com/sql/t-sql/functions/functions) | Most – tekintse meg az egyes funkciók | Igen – lásd: [a tárolt eljárások, függvények, eseményindítók különbségek](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Adatrögzítés módosítása](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Nem | Igen |
| [Változások követése](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Igen |Igen |
| [Rendezés utasítások](https://docs.microsoft.com/sql/t-sql/statements/collations) | Igen | Igen |
| [Oszlopcentrikus indexek](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Igen – [csak a Premium edition](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |Igen |
| [Közös nyelvi futtatókörnyezet (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Nem | Igen – lásd: [CLR különbségek](sql-database-managed-instance-transact-sql-information.md#clr) |
| [Tartalmazott adatbázisok](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Igen | Igen |
| [Tartalmazott felhasználók](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Igen | Igen |
| [Attribútumfolyam nyelvi kulcsszavak ellenőrzése](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Igen | Igen |
| [Kereszt-adatbázis-lekérdezések](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nem - látható [rugalmas lekérdezések](sql-database-elastic-query-overview.md) | Igen, valamint [rugalmas lekérdezések](sql-database-elastic-query-overview.md) |
| [Adatbázisközi tranzakciók]((https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine)) | Nem | Igen |
| [A kurzorok](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Igen |Igen | 
| [Adatok tömörítése](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Igen |Igen |
| [Az adatbázisbeli levelezés](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Nem | Igen |
| [Adatok áttelepítése szolgáltatás (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | Igen | Igen |
| [Az adatbázis-tükrözés](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Nem | Nem |
| [Adatbázis-konfigurációs beállításokat](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Igen | Igen |
| [Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Nem | Nem |
| [Adatbázis-pillanatképek](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Nem | Nem |
| [Adattípusok](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Igen |Igen |
| [DBCC utasítások](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | Most – tekintse meg az egyes utasítások | Igen – lásd: [DBCC különbségek](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [DDL-utasításokban](https://docs.microsoft.com/sql/t-sql/statements/statements) | Most – tekintse meg az egyes utasítások | Igen – lásd: [T-SQL különbségek](sql-database-managed-instance-transact-sql-information.md) |
| [DDL-eseményindítók](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Csak az adatbázis |  Igen |
| [A partíció elosztott nézetek](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | Nem | Igen |
| [Az elosztott tranzakciók – az MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Nem - látható [rugalmas tranzakciók](sql-database-elastic-transactions-overview.md) |  Nem - látható [rugalmas tranzakciók](sql-database-elastic-transactions-overview.md) |
| [A DML-utasítások](https://docs.microsoft.com/sql/t-sql/queries/queries) | Igen | Igen |
| [A DML-eseményindítók](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | Most – tekintse meg az egyes utasítások |  Igen |
| [DMV-k](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Most – tekintse meg az egyes dinamikus felügyeleti nézetek |  Igen – lásd: [T-SQL különbségek](sql-database-managed-instance-transact-sql-information.md) |
|[Dinamikus adatmaszkolás](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Igen](sql-database-dynamic-data-masking-get-started.md)| Igen |
| [Rugalmas készletek](sql-database-elastic-pool.md) | Igen | egy felügyelt példányban lehet több adatbázis ugyanazon erőforráskészletet használó |
| [Eseményértesítések](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Nem - látható [riasztások](sql-database-insights-alerts-portal.md) | Igen |
| [Expressions](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Igen | Igen |
| [Bővített események](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Egyes - láthatnak [kiterjesztett események az SQL-adatbázis](sql-database-xevent-db-diff-from-svr.md) | Igen – lásd: [kiterjesztett események különbségek ](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [Bővített tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Nem | Nem |
[Fájlok és fájlcsoportok](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Csak az elsődleges fájl csoport | Igen |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Nem | Nem |
| [A teljes szöveges keresés](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  Külső szóhatároló nem támogatottak. |Külső szóhatároló nem támogatottak. |
| [Functions](https://docs.microsoft.com/sql/t-sql/functions/functions) | Most – tekintse meg az egyes funkciók | Igen – lásd: [a tárolt eljárások, függvények, eseményindítók különbségek](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Geo-restore](sql-database-recovery-using-backups.md#geo-restore) | Igen | Nem – visszaállíthatja COPY_ONLY tekintse meg a teljes biztonsági mentés, amelyet rendszeres időközönként - [különbségek biztonsági mentését](sql-database-managed-instance-transact-sql-information.md#backup) és [különbségek visszaállítása](sql-database-managed-instance-transact-sql-information.md#restore-statement). |
| [Georeplikáció](sql-database-geo-replication-overview.md) | Igen | Nem |
| [Graph feldolgozása](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | Igen | Igen |
| [Memóriabeli optimalizálás](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Igen – [csak a Premium edition](sql-database-in-memory.md) | Nem |
| [JSON-adatok támogatása](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | Igen | Igen |
| [Nyelvi elemei](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Most – tekintse meg az egyéni elemekké |  Igen – lásd: [T-SQL különbségek](sql-database-managed-instance-transact-sql-information.md) |
| [Csatolt kiszolgálók](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nem - látható [rugalmas lekérdezés](sql-database-elastic-query-horizontal-partitioning.md) | Only to SQL Server |
| [Naplóküldés](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [Magas rendelkezésre állású](sql-database-high-availability.md) megtalálható minden adatbázist. Vész-helyreállítási ismertet [az Azure SQL Database üzletmenet áttekintése](sql-database-business-continuity.md) |[Magas rendelkezésre állású](sql-database-high-availability.md) megtalálható minden adatbázist. Vész-helyreállítási ismertet [az Azure SQL Database üzletmenet áttekintése](sql-database-business-continuity.md) |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Nem | Nem |
| [A tömeges importálással minimális naplózás](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Nem | Nem |
| [Rendszeradatok módosítása](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Nem | Igen |
| [Online indexműveleteket](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Igen | Igen |
| [Operátorok](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | Most – tekintse meg az egyes operátorok |Igen – lásd: [T-SQL különbségek](sql-database-managed-instance-transact-sql-information.md) |
| [Particionálás](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Igen | Igen |
| [Időponthoz kötött adatbázis visszaállítás](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Igen – lásd: [SQL-adatbázis helyreállítása](sql-database-recovery-using-backups.md#point-in-time-restore) | Igen – lásd: [SQL-adatbázis helyreállítása](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Nem | Nem |
| [Csoportházirend-alapú felügyelet](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Nem | Nem |
| [Predikátumok](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Igen | Igen |
| [R-szolgáltatások](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Előzetes; Lásd: [What's new in gépi tanulás](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | Nem |
| [Erőforrás-vezérlő](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Nem | Nem |
| [A RESTORE utasítás](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Nem | Igen – lásd: [különbségek visszaállítása](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [Adatbázis visszaállítása biztonsági másolatból](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Az automatikus biztonsági mentés csak - látható [SQL-adatbázis helyreállítása](sql-database-recovery-using-backups.md) | Automatikus biztonsági másolatból - lásd: [SQL-adatbázis helyreállítási](sql-database-recovery-using-backups.md) , és teljes biztonsági másolatból - [különbségek biztonsági mentése](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Igen | Igen |
| [A szemantikai keresése](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Nem | Nem |
| [Sorozatszámok száma](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Igen | Igen |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Nem | Igen – lásd: [Service Broker különbségek](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [Kiszolgálók konfigurációs beállításai](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Nem | Igen – lásd: [T-SQL különbségek](sql-database-managed-instance-transact-sql-information.md) |
| [Set utasítás](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | Most – tekintse meg az egyes utasítások | Igen – lásd: [T-SQL különbségek](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | Igen | Igen |
| [Térbeli](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Igen | Igen |
| [SQL Data Sync](sql-database-get-started-sql-data-sync.md) | Igen | Igen |
| [SQL Operations Studio](https://docs.microsoft.com/sql/sql-operations-studio/what-is) | Igen | Igen |
| [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Nem - látható [rugalmas feladatok](sql-database-elastic-jobs-getting-started.md) | Igen – lásd: [SQL Server Agent különbségek](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Nem - látható [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | Nem - látható [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [SQL Server Auditing](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Nem - látható [SQL Database auditing](sql-database-auditing.md) | Igen – lásd: [különbségek naplózás](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [SQL Server Data Tools (SSDT)] (https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | Igen | Igen |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Részleges – az SQL Server Data Tools csomag fejlesztési nem támogatott. | Nem, nem a nyilvános előzetes kiadásában |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | Igen | Igen |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Igen | Igen |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Nem - látható [kiterjesztett események](sql-database-xevent-db-diff-from-svr.md) | Igen |
| [SQL Server-replikáció](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Kizárólag tranzakciós és pillanatkép-replikációs előfizetők](sql-database-cloud-migrate.md) | Nem |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Nem - [tekintse meg a Power bi-ban](https://docs.microsoft.com/power-bi/) | Nem - [tekintse meg a Power bi-ban](https://docs.microsoft.com/power-bi/) |
| [Tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Igen | Igen |
| [A rendszerszintű tárolt funkciók](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Most – tekintse meg az egyes funkciók | Igen – lásd: [a tárolt eljárások, függvények, eseményindítók különbségek](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [A rendszerszintű tárolt eljárásokra](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Néhány - egyéni tárolt eljárásokat lásd: | Igen – lásd: [a tárolt eljárások, függvények, eseményindítók különbségek](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Rendszertáblák](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Néhány – tekintse meg az egyes táblák | Igen – lásd: [T-SQL különbségek](sql-database-managed-instance-transact-sql-information.md) |
| [Rendszer katalógusnézetekre](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Egyes - tekintse meg az egyes nézetek | Igen – lásd: [T-SQL különbségek](sql-database-managed-instance-transact-sql-information.md) |
| [ideiglenes táblákkal](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | Helyi és adatbázis-hatóköre globális ideiglenes táblák | Helyi és a példány hatóköre globális ideiglenes táblák |
| [Historikus táblák](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | Igen | Igen |
| [Követésjelzői](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | Nem | Nem |
| [Változók](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Igen | Igen |
| [Az átlátható adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Igen | Nem, nem a nyilvános előzetes kiadásában |
[VNet](../virtual-network/virtual-networks-overview.md) | Részleges - lásd [VNETET végpontjai](sql-database-vnet-service-endpoint-rule-overview.md) | Igen, csak a Resource Manager modellt |
| [Windows Server feladatátvételi fürtszolgáltatás](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | [Magas rendelkezésre állású](sql-database-high-availability.md) megtalálható minden adatbázist. Vész-helyreállítási ismertet [az Azure SQL Database üzletmenet áttekintése](sql-database-business-continuity.md) | [Magas rendelkezésre állású](sql-database-high-availability.md) megtalálható minden adatbázist. Vész-helyreállítási ismertet [az Azure SQL Database üzletmenet áttekintése](sql-database-business-continuity.md) |
| [XML-index](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Igen | Igen |

## <a name="next-steps"></a>További lépések

- Az Azure SQL Database szolgáltatással kapcsolatos tudnivalók: [Mi az SQL Database?](sql-database-technical-overview.md)
- A felügyelt példánya kapcsolatos információkért lásd: [Mi az, hogy a felügyelt példánya?](sql-database-managed-instance.md).
