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
ms.custom: features
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 03/03/2017
ms.author: carlrab; jognanay
translationtype: Human Translation
ms.sourcegitcommit: e851a3e1b0598345dc8bfdd4341eb1dfb9f6fb5d
ms.openlocfilehash: a1ede93b5aacf0d8a5bcf83f208f72be328ee72f
ms.lasthandoff: 04/15/2017


---
# <a name="azure-sql-database-features"></a>Az Azure SQL Database funkciói

Az alábbi táblázatok az Azure SQL Database legfontosabb funkcióit és az SQL Server egyenértékű funkcióit, valamint az egyes funkciók támogatottságára vonatkozó információkat, illetve a funkciók egyes platformokra vonatkozó bővebb információira mutató hivatkozásokat tartalmaznak. A meglévő adatbázis-megoldások áttelepítése során figyelembe veendő Transact-SQL különbségekről lásd: [Transact-SQL különbségek feloldása az SQL Database-be végzett áttelepítések során](sql-database-transact-sql-information.md).

Az Azure SQL Database funkcióinak körét folyamatosan bővítjük. Ezért javasoljuk, hogy látogassa meg az Azure Szolgáltatási hírek webhelyét, és használja a szűrőket:

* Szűrjön [SQL Database szolgáltatásra](https://azure.microsoft.com/updates/?service=sql-database).
* Szűrjön az általános elérhetőséggel kapcsolatos [bejelentésekre](http://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) az SQL Database funkcióira vonatkozóan.

| **Funkció** | **SQL Server** | **Azure SQL Database** | 
| --- | :---: | :---: | 
| Aktív georeplikáció | Nem támogatott – lásd: [AlwaysOn rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Támogatott](sql-database-geo-replication-overview.md)
| Always Encrypted | [Támogatott](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Támogatott – lásd: [Tanúsítványtároló](sql-database-always-encrypted.md) és [Key Vault](sql-database-always-encrypted-azure-key-vault.md)|
| AlwaysOn rendelkezésre állási csoportok | [Támogatott](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | Nem támogatott – lásd [Aktív georeplikáció](sql-database-geo-replication-overview.md) |
| Adatbázis csatolása | [Támogatott](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Nem támogatott |
| Alkalmazás-szerepkörök | [Támogatott](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | [Támogatott](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) |
| Automatikus méretezés | Nem támogatott | Támogatott – lásd: [Szolgáltatásszintek](sql-database-service-tiers.md) |
| Azure Active Directory | Nem támogatott | [Támogatott](sql-database-aad-authentication.md) |
| Azure Data Factory | [Támogatott](../data-factory/data-factory-introduction.md) | [Támogatott](../data-factory/data-factory-introduction.md) |
| Naplózás | [Támogatott](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Támogatott](sql-database-auditing.md) |
| BACPAC-fájl (exportálás) | [Támogatott](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | [Támogatott](sql-database-export.md) |
| BACPAC-fájl (importálás) | [Támogatott](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | [Támogatott](sql-database-import.md) |
| BACKUP | [Támogatott](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Nem támogatott |
| Beépített funkciók | [Támogatott](https://docs.microsoft.com/sql/t-sql/functions/functions) | A legtöbb esetben – lásd [az egyes függvényeket] (https://docs.microsoft.com/sql/t-sql/functions/functions) |
| Adatváltozások rögzítése | [Támogatott](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Nem támogatott |
| Változások követése | [Támogatott](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | [Támogatott](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) |
| Rendezési utasítások | [Támogatott](https://docs.microsoft.com/sql/t-sql/statements/collations) | [Támogatott](https://docs.microsoft.com/sql/t-sql/statements/collations) |
| Oszlopcentrikus indexek | [Támogatott](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | [Csak Premium Edition](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |
| Közös nyelvi futtatókörnyezet (CLR) | [Támogatott](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Nem támogatott |
| Tartalmazott adatbázisok | [Támogatott](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | [Támogatott](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) |
| Tartalmazott felhasználók | [Támogatott](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | [Támogatott](sql-database-manage-logins.md#non-administrator-users) |
| Vezérlőnyelvi kulcsszavak | [Támogatott](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | [Támogatott](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) |
| Adatbázisközi lekérdezések | [Támogatott](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | Részben – lásd: [Rugalmas lekérdezések](sql-database-elastic-query-overview.md) |
| Kurzorok | [Támogatott](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | [Támogatott](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | 
| Adattömörítés | [Támogatott](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | [Támogatott](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) |
| Adatbázisok biztonsági mentése | [Felhasználó által felügyelt](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) | [Az SQL Database szolgáltatás által felügyelt](sql-database-automated-backups.md) |
| Adatbázisbeli levelezés | [Támogatott](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Nem támogatott |
| Adatbázis-tükrözés | [Támogatott](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Nem támogatott |
| Adatbázis konfigurációs beállításai | [Támogatott](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | [Támogatott](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Data Quality Services (DQS) | [Támogatott](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Nem támogatott |
| Adatbázis-pillanatképek | [Támogatott](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Nem támogatott |
| Adattípusok | [Támogatott](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | [Támogatott](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) |  
| DBCC-utasítások | [Támogatott](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | A legtöbb esetben – lásd [az egyes utasításokat](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) |
| DDL-utasítások | [Támogatott](https://docs.microsoft.com/sql/t-sql/statements/statements) | A legtöbb esetben – lásd [az egyes utasításokat](https://docs.microsoft.com/sql/t-sql/statements/statements)
| DDL-eseményindítók | [Támogatott](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | [Csak adatbázis](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) |
| Elosztott tranzakciók | [MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Csak korlátozott SQL Database-beli forgatókönyvek |
| DML-utasítások | [Támogatott](https://docs.microsoft.com/sql/t-sql/queries/queries) | A legtöbb esetben – lásd [az egyes utasításokat]](https://docs.microsoft.com/sql/t-sql/queries/queries) |
| DML-eseményindítók | [Támogatott](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) | [Támogatott](https://docs.microsoft.com/sql/relational-databases/triggers/dml-triggers) |
| DMV-k | [Az összes](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Néhány – lásd [az egyes DMV-ket](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) |
| Rugalmas készletek | Nem támogatott | [Támogatott](sql-database-elastic-pool.md) |
| Rugalmas feladatok | Nem támogatott – lásd [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | [Támogatott](sql-database-elastic-jobs-getting-started.md) | 
| Rugalmas lekérdezések | Nem támogatott – lásd [Adatbázisközi lekérdezések](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/cross-database-queries) | [Támogatott](sql-database-elastic-query-overview.md) |
| Eseményértesítések | [Támogatott](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | [Támogatott](sql-database-insights-alerts-portal.md) |
| Kifejezések | [Támogatott](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) | [Támogatott](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |
| Bővített események | [Támogatott](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Néhány – lásd [az egyes eseményeket](sql-database-xevent-db-diff-from-svr.md) |
| Bővített tárolt eljárások | [Támogatott](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Nem támogatott |
| Fájlok és fájlcsoportok | [Támogatott](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | [Csak az elsődleges](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) |
| Fájlstream | [Támogatott](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Nem támogatott |
| Teljes szöveges keresés | [Támogatott](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) | [A külső fejlesztők szóhatárolói nem támogatottak](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |
| Functions | [Támogatott](https://docs.microsoft.com/sql/t-sql/functions/functions) | A legtöbb esetben – lásd [az egyes függvényeket](https://docs.microsoft.com/sql/t-sql/functions/functions) |
| Memóriabeli optimalizálás | [Támogatott](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | [Csak Premium Edition](sql-database-in-memory.md) |
| Feladatok | Lásd: [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Lásd: [Rugalmas feladatok](sql-database-elastic-jobs-getting-started.md) |
| JSON-adatok támogatása | [Támogatott](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Támogatott](sql-database-json-features.md) |
| Nyelvi elemek | [Támogatott](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | A legtöbb esetben – lásd [az egyes elemeket](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) |  
| Társított kiszolgálók | [Támogatott](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nem támogatott – lásd [Rugalmas lekérdezés](sql-database-elastic-query-horizontal-partitioning.md) |
| Naplóküldés | [Támogatott](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | Nem támogatott – lásd [Aktív georeplikáció](sql-database-geo-replication-overview.md) |
| Master Data Services (MDS) | [Támogatott](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Nem támogatott |
| Minimális naplózás tömeges importálás során | [Támogatott](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Nem támogatott |
| Rendszeradatok módosítása | [Támogatott](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Nem támogatott |
| Online indexműveletek | [Támogatott](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | [Támogatott – a tranzakciók mérete a szolgáltatási szint alapján korlátozott](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) |
| Operátorok | [Támogatott](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | A legtöbb esetben – lásd [az egyes operátorokat](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) |
| Időponthoz kötött adatbázis-visszaállítás | [Támogatott](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | [Támogatott](sql-database-recovery-using-backups.md#point-in-time-restore) |
| PolyBase | [Támogatott](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Nem támogatott
| Házirendalapú felügyelet | [Támogatott](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Nem támogatott |
| Predikátumok | [Támogatott](https://docs.microsoft.com/sql/t-sql/queries/predicates) | A legtöbb esetben – lásd [az egyes predikátumokat](https://docs.microsoft.com/sql/t-sql/queries/predicates)
| R-szolgáltatások | [Támogatott](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services)
| Erőforrás-vezérlő | [Támogatott](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Nem támogatott |
| RESTORE utasítások | [Támogatott](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Nem támogatott | 
| Adatbázis visszaállítása biztonsági mentésből | [Támogatott](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | [Kizárólag beépített biztonsági másolatokból](sql-database-recovery-using-backups.md) |
| Sorszintű biztonság | [Támogatott](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | [Támogatott](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) |
| Szemantikai keresés | [Támogatott](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Nem támogatott |
| Sorozatszámok | [Támogatott](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | [Támogatott](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) |
| Szolgáltatásközvetítő | [Támogatott](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Nem támogatott |
| Kiszolgáló konfigurációs beállításai | [Támogatott](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Nem támogatott – lásd [Adatbázis-konfigurációs beállítások](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) |
| Utasítások megadása | [Támogatott](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | A legtöbb esetben – lásd [az egyes utasításokat](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) 
| Térbeli | [Támogatott](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | [Támogatott](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) |
| SQL Server Agent | [Támogatott](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Nem támogatott – lásd [Rugalmas feladatok](sql-database-elastic-jobs-getting-started.md) |
| SQL Server Analysis Services (SSAS) | [Támogatott](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Nem támogatott – lásd [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| SQL Server Integration Services (SSIS) | [Támogatott](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Nem támogatott – lásd [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) |
| SQL Server PowerShell | [Támogatott](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | [Támogatott](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) |
| SQL Server Profiler | [Támogatott](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Nem támogatott – lásd [Bővített események](sql-database-xevent-db-diff-from-svr.md) |
| SQL Server-replikáció | [Támogatott](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Kizárólag tranzakciós és pillanatkép-replikációs előfizetők](sql-database-cloud-migrate.md) |
| SQL Server Reporting Services (SSRS) | [Támogatott](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Nem támogatott |
| Tárolt eljárások | [Támogatott](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | [Támogatott](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) |
| A rendszer tárolt függvényei | [Támogatott](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Néhány – lásd [az egyes függvényeket](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) |
| A rendszer tárolt eljárásai | [Támogatott](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Néhány – lásd [az egyes tárolt eljárásokat](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) |
| Rendszertáblák | [Támogatott](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Néhány – lásd [az egyes táblákat](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) |
| Rendszerkatalógus-nézetek | [Támogatott](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Néhány – lásd [az egyes nézeteket](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql)
| Tábla particionálása | [Támogatott](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Támogatott – [Csak az elsődleges fájlcsoport](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) |
| Ideiglenes táblák | [Helyi és globális](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) | [Csak helyi](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#temporary-tables) |
| Historikus táblák | [Támogatott](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Támogatott](sql-database-temporal-tables.md) |
| Tranzakciók | [Támogatott](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) | [Támogatott](https://docs.microsoft.com/sql/t-sql/language-elements/transactions-transact-sql) |
| Változók | [Támogatott](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | [Támogatott](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | 
| Transzparens adattitkosítás (TDE)  | [Támogatott](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | [Támogatott](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Windows Server feladatátvételi fürtszolgáltatás | [Támogatott](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | Nem támogatott – lásd [Aktív georeplikáció](sql-database-geo-replication-overview.md) |
| XML-indexek | [Támogatott](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | [Támogatott](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) |

## <a name="next-steps"></a>Következő lépések

- Az Azure SQL Database szolgáltatással kapcsolatos tudnivalók: [Mi az SQL Database?](sql-database-technical-overview.md)
- A Transact-SQL támogatásával és eltéréseivel kapcsolatos tudnivalókat lásd: [Transact-SQL különbségek feloldása az SQL Database-be végzett áttelepítések során](sql-database-transact-sql-information.md).

