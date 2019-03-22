---
title: Az Azure SQL Database funkcióinak összehasonlítása |} A Microsoft Docs
description: Ez a cikk összehasonlítja az SQL Server elérhető szolgáltatásokat az Azure SQL Database különböző változataira jellemző.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: bonova, carlrab
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: 82ed8783444a414d4706f97c8c9b6ea18734561e
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58311747"
---
# <a name="feature-comparison-azure-sql-database-versus-sql-server"></a>Szolgáltatások összehasonlítása: Az Azure SQL Database és az SQL Server összehasonlítása

Az Azure SQL Database egy általános kódbázis közös SQL Server. Az Azure SQL Database által támogatott SQL Server funkcióit az Ön által létrehozott Azure SQL database-típustól függnek. Az Azure SQL Database-adatbázis létrehozásához részeként egy [felügyelt példány](sql-database-managed-instance.md), önálló adatbázisként, vagy egy rugalmas készlet részeként.

A Microsoft továbbra is biztosítja a funkciók az Azure SQL Database. Látogasson el a szolgáltatásfrissítések weblap az Azure-hoz a legújabb frissítések ezekkel a szűrőkkel:

- Szűrjön [SQL Database szolgáltatásra](https://azure.microsoft.com/updates/?service=sql-database).
- Szűrjön az általános elérhetőséggel kapcsolatos [bejelentésekre](https://azure.microsoft.com/updates/?service=sql-database&update-type=general-availability) az SQL Database funkcióira vonatkozóan.

## <a name="sql-server-feature-support-in-azure-sql-database"></a>Az Azure SQL Database az SQL Server-funkciók támogatása

A következő táblázat az SQL Server legfontosabb funkcióit, és a szolgáltatás részlegesen vagy teljesen támogatja-e, és a egy a szolgáltatásra vonatkozó bővebb információira mutató hivatkozásokat nyújt információt.

| **Az SQL szolgáltatás** | **Önálló adatbázisok és rugalmas készletek által támogatott** | **Felügyelt példány által támogatott** |
| --- | --- | --- |
| [Aktív georeplikáció](sql-database-active-geo-replication.md) | Igen – az összes szolgáltatáshoz eltérő nagy kapacitású | Nem, lásd: [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md) |
| [Automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md) | Igen – az összes szolgáltatáshoz eltérő nagy kapacitású | Igen, a [nyilvános előzetes verzió](sql-database-auto-failover-group.md)|
| [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) | Igen – lásd: [tanúsítványtár](sql-database-always-encrypted.md) és [Key vault](sql-database-always-encrypted-azure-key-vault.md) | Igen – lásd: [tanúsítványtár](sql-database-always-encrypted.md) és [Key vault](sql-database-always-encrypted-azure-key-vault.md) |
| [Always On rendelkezésre állási csoportok](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) | [Magas rendelkezésre állású](sql-database-high-availability.md) minden adatbázis részét képezi. Vész-helyreállítási a következő cikkben [az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése](sql-database-business-continuity.md) | [Magas rendelkezésre állású](sql-database-high-availability.md) minden adatbázis részét képezi. Vész-helyreállítási a következő cikkben [az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése](sql-database-business-continuity.md) |
| [Adatbázis csatolása](https://docs.microsoft.com/sql/relational-databases/databases/attach-a-database) | Nem | Nem |
| [Alkalmazási szerepkörök](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/application-roles) | Igen | Igen |
|[Naplózás](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | [Igen](sql-database-auditing.md)| [Igen](sql-database-managed-instance-auditing.md) |
| [Az automatikus biztonsági mentés](sql-database-automated-backups.md) | Igen | Igen |
| [Automatikus hangolás (lekérdezésterv)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Igen](sql-database-automatic-tuning.md)| [Igen](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) |
| [Automatikus hangolás (indexek)](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)| [Igen](sql-database-automatic-tuning.md)| Nem |
| [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) | Igen | Igen |
| [BACPAC-fájl (Exportálás)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Igen – lásd: [SQL Database-adatbázis exportálása](sql-database-export.md) | Igen |
| [BACPAC-fájl (importálás)](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Igen – lásd: [SQL-adatbázis importálása](sql-database-import.md) | Igen |
| [Biztonsági MENTÉSI parancs](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql) | Nem, csak rendszer által kezdeményezett automatikus biztonsági mentések - látható [automatizált biztonsági mentések](sql-database-automated-backups.md) | Rendszer által kezdeményezett automatikus biztonsági másolatokat és a felhasználó által kezdeményezett csak másolatot biztonsági mentések – lásd: [különbségek biztonsági mentése](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Beépített függvények](https://docs.microsoft.com/sql/t-sql/functions/functions) | Most – tekintse meg az egyes függvények | Igen – lásd: [tárolt eljárások, függvények, eseményindítók különbségek](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Adatváltozások rögzítése](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server) | Nem | Igen |
| [Változások követése](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) | Igen |Igen |
| [Rendezés - adatbázis](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-database-collation) | Igen | Igen |
| [Rendezés - server-példány](https://docs.microsoft.com/sql/relational-databases/collations/set-or-change-the-server-collation) | Nem | Igen, a [nyilvános előzetes verzió](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)|
| [Az Oszlopcentrikus indexek](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) | Igen – [prémium szintű, a Standard szint – S3-as vagy újabb, általános célú csomagban és a kritikus fontosságú üzleti szint](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-overview) |Igen |
| [Közös nyelvi futtatókörnyezet (CLR)](https://docs.microsoft.com/sql/relational-databases/clr-integration/common-language-runtime-clr-integration-programming-concepts) | Nem | Igen – lásd: [CLR-beli különbségek](sql-database-managed-instance-transact-sql-information.md#clr) |
| [Tartalmazott adatbázisok](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) | Igen | Nem [többek között az időponthoz VISSZAÁLLÍTÁSI VISSZAÁLLÍTÁSI hiba miatt](sql-database-managed-instance-transact-sql-information.md#cannot-restore-contained-database) |
| [Tartalmazott felhasználók](https://docs.microsoft.com/sql/relational-databases/security/contained-database-users-making-your-database-portable) | Igen | Igen |
| [Vezérlőnyelvi kulcsszavak ellenőrzése](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow) | Igen | Igen |
| [Adatbázisközi lekérdezések](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nem – lásd: [rugalmas lekérdezések](sql-database-elastic-query-overview.md) | Igen, valamint [rugalmas lekérdezések](sql-database-elastic-query-overview.md) |
| [Adatbázisközi tranzakciók](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nem | Igen, a példány belül. Lásd: [csatolt kiszolgáló különbségek](sql-database-managed-instance-transact-sql-information.md#linked-servers) cross-példány lekérdezések. |
| [A kurzorok](https://docs.microsoft.com/sql/t-sql/language-elements/cursors-transact-sql) | Igen |Igen |
| [Az adattömörítés](https://docs.microsoft.com/sql/relational-databases/data-compression/data-compression) | Igen |Igen |
| [Az adatbázisbeli levelezés](https://docs.microsoft.com/sql/relational-databases/database-mail/database-mail) | Nem | Igen |
| [Data Migration Service (DMS)](https://docs.microsoft.com/sql/dma/dma-overview) | Igen | Igen |
| [Az adatbázis-tükrözés](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server) | Nem | Nem |
| [Adatbázis-konfigurációs beállítások](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) | Igen | Igen |
| [A Data Quality Services (DQS)](https://docs.microsoft.com/sql/data-quality-services/data-quality-services) | Nem | Nem |
| [Adatbázis-pillanatképek](https://docs.microsoft.com/sql/relational-databases/databases/database-snapshots-sql-server) | Nem | Nem |
| [Adattípusok](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql) | Igen |Igen |
| [DBCC-utasítások](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-transact-sql) | A legtöbb – tekintse meg az egyes utasításokat | Igen – lásd: [DBCC különbségek](sql-database-managed-instance-transact-sql-information.md#dbcc) |
| [DDL-utasítások](https://docs.microsoft.com/sql/t-sql/statements/statements) | A legtöbb – tekintse meg az egyes utasításokat | Igen – lásd: [a T-SQL eltérései](sql-database-managed-instance-transact-sql-information.md) |
| [DDL-triggerek](https://docs.microsoft.com/sql/relational-databases/triggers/ddl-triggers) | Csak adatbázis |  Igen |
| [A partíció az elosztott nézetek](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql#partitioned-views) | Nem | Igen |
| [Elosztott tranzakciók – az MS DTC](https://docs.microsoft.com/sql/relational-databases/native-client-ole-db-transactions/supporting-distributed-transactions) | Nem – lásd: [rugalmas tranzakciók](sql-database-elastic-transactions-overview.md) |  Nem – lásd: [csatolt kiszolgáló különbségek](sql-database-managed-instance-transact-sql-information.md#linked-servers) |
| [DML-utasítások](https://docs.microsoft.com/sql/t-sql/queries/queries) | Igen | Igen |
| [DML-trigger](https://docs.microsoft.com/sql/relational-databases/triggers/create-dml-triggers) | A legtöbb – tekintse meg az egyes utasításokat |  Igen |
| [DMV-k](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) | Most – tekintse meg az egyes DMV-kkel |  Igen – lásd: [a T-SQL eltérései](sql-database-managed-instance-transact-sql-information.md) |
|[Dinamikus adatmaszkolás](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)|[Igen](sql-database-dynamic-data-masking-get-started.md)| [Igen](sql-database-dynamic-data-masking-get-started.md) |
| [Rugalmas készletek](sql-database-elastic-pool.md) | Igen | Beépített – egyetlen felügyelt példány, amelyek ugyanazt a készletet az erőforrások több adatbázis is rendelkezhet. |
| [Eseményértesítések](https://docs.microsoft.com/sql/relational-databases/service-broker/event-notifications) | Nem – lásd: [riasztások](sql-database-insights-alerts-portal.md) | Nem |
| [Kifejezések](https://docs.microsoft.com/sql/t-sql/language-elements/expressions-transact-sql) |Igen | Igen |
| [Bővített események](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events) | Néhány – lásd [bővített események SQL Database-ben](sql-database-xevent-db-diff-from-svr.md) | Igen – lásd: [bővített események különbségek](sql-database-managed-instance-transact-sql-information.md#extended-events) |
| [Bővített tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/extended-stored-procedures-programming/creating-extended-stored-procedures) | Nem | Nem |
[Fájlok és fájlcsoportok](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups) | Elsődleges fájlcsoport | Igen |
| [Filestream](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) | Nem | Nem |
| [Teljes szöveges keresés](https://docs.microsoft.com/sql/relational-databases/search/full-text-search) |  Külső szóhatároló nem támogatottak. |Külső szóhatároló nem támogatottak. |
| [Functions](https://docs.microsoft.com/sql/t-sql/functions/functions) | Most – tekintse meg az egyes függvények | Igen – lásd: [tárolt eljárások, függvények, eseményindítók különbségek](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Geo-restore](sql-database-recovery-using-backups.md#geo-restore) | Igen – az összes szolgáltatáshoz eltérő nagy kapacitású | Nem állíthatja vissza COPY_ONLY tekintse meg a teljes biztonsági mentést, hogy rendszeres időközönként - [biztonsági mentési különbségek](sql-database-managed-instance-transact-sql-information.md#backup) és [különbségek visszaállítása](sql-database-managed-instance-transact-sql-information.md#restore-statement). |
| [Graph-feldolgozás](https://docs.microsoft.com/sql/relational-databases/graphs/sql-graph-overview) | Igen | Igen |
| [Memóriabeli optimalizálás](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) | Igen – [csak a prémium és üzletileg kritikus szintet](sql-database-in-memory.md) | Igen – [üzleti csak a kritikus szintű](sql-database-managed-instance.md) |
| [JSON-adatok támogatása](https://docs.microsoft.com/sql/relational-databases/json/json-data-sql-server) | [Igen](sql-database-json-features.md) | [Igen](sql-database-json-features.md) |
| [Nyelvi elemei](https://docs.microsoft.com/sql/t-sql/language-elements/language-elements-transact-sql) | Most – tekintse meg az egyes elemek |  Igen – lásd: [a T-SQL eltérései](sql-database-managed-instance-transact-sql-information.md) |
| [Társított kiszolgálók](https://docs.microsoft.com/sql/relational-databases/linked-servers/linked-servers-database-engine) | Nem – lásd: [rugalmas lekérdezés](sql-database-elastic-query-horizontal-partitioning.md) | Csak az SQL Server és SQL Database |
| [Naplóküldés](https://docs.microsoft.com/sql/database-engine/log-shipping/about-log-shipping-sql-server) | [Magas rendelkezésre állású](sql-database-high-availability.md) minden adatbázis részét képezi. Vész-helyreállítási a következő cikkben [az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése](sql-database-business-continuity.md) |[Magas rendelkezésre állású](sql-database-high-availability.md) minden adatbázis részét képezi. Vész-helyreállítási a következő cikkben [az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése](sql-database-business-continuity.md) |
| [Master Data Services (MDS)](https://docs.microsoft.com/sql/master-data-services/master-data-services-overview-mds) | Nem | Nem |
| [Minimális naplózás tömeges importálás során](https://docs.microsoft.com/sql/relational-databases/import-export/prerequisites-for-minimal-logging-in-bulk-import) | Nem | Nem |
| [Rendszeradatok módosítása](https://docs.microsoft.com/sql/relational-databases/databases/system-databases) | Nem | Igen |
| [Az OLE-automatizálási](https://docs.microsoft.com/sql/database-engine/configure-windows/ole-automation-procedures-server-configuration-option) | Nem | Nem |
| [Online Indexműveletek](https://docs.microsoft.com/sql/relational-databases/indexes/perform-index-operations-online) | Igen | Igen |
| [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql)|Nem|Igen – lásd: [a T-SQL eltérései](sql-database-managed-instance-transact-sql-information.md)|
| [OPENJSON](https://docs.microsoft.com/sql/t-sql/functions/openjson-transact-sql)|Igen|Igen|
| [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)|Nem|Igen – lásd: [a T-SQL eltérései](sql-database-managed-instance-transact-sql-information.md)|
| [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql)|Nem|Igen – lásd: [a T-SQL eltérései](sql-database-managed-instance-transact-sql-information.md)|
| [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql)|Igen|Igen|
| [Operátorok](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) | A legtöbb – tekintse meg az egyes operátorokat |Igen – lásd: [a T-SQL eltérései](sql-database-managed-instance-transact-sql-information.md) |
| [A particionálás](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes) | Igen | Igen |
| [Időponthoz kötött adatbázis visszaállítása](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-sql-server-database-to-a-point-in-time-full-recovery-model) | Igen – tekintse meg a nagy kapacitású – kivételével az összes szolgáltatási szint [SQL adatbázis-helyreállítás](sql-database-recovery-using-backups.md#point-in-time-restore) | Igen – lásd: [SQL adatbázis-helyreállítás](sql-database-recovery-using-backups.md#point-in-time-restore) |
| [A Polybase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) | Nem | Nem |
| [Házirendalapú felügyelet](https://docs.microsoft.com/sql/relational-databases/policy-based-management/administer-servers-by-using-policy-based-management) | Nem | Nem |
| [Predikátumokat](https://docs.microsoft.com/sql/t-sql/queries/predicates) | Igen | Igen |
| [Lekérdezés értesítések](https://docs.microsoft.com/sql/relational-databases/native-client/features/working-with-query-notifications) | Nem | Igen |
| [Lekérdezési teljesítmény elemzése](sql-database-query-performance.md) | Igen | Nem |
| [R-szolgáltatások](https://docs.microsoft.com/sql/advanced-analytics/r-services/sql-server-r-services) | Igen, a [nyilvános előzetes verzió](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services)  | Nem |
| [Erőforrás-vezérlő](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) | Nem | Igen |
| [RESTORE utasítások](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-for-restoring-recovering-and-managing-backups-transact-sql) | Nem | Igen – lásd: [különbségek visszaállítása](sql-database-managed-instance-transact-sql-information.md#restore-statement) |
| [Adatbázis visszaállítása biztonsági másolatból](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases#restore-data-backups) | Automatikus biztonsági másolatokat csak - látható [SQL adatbázis-helyreállítás](sql-database-recovery-using-backups.md) | Automatikus biztonsági mentésekből – lásd: [SQL-adatbázis helyreállítási](sql-database-recovery-using-backups.md) és a teljes biztonsági mentés – lásd: [különbségek biztonsági mentése](sql-database-managed-instance-transact-sql-information.md#backup) |
| [Sorszintű biztonság](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) | Igen | Igen |
| [Szemantikai keresés](https://docs.microsoft.com/sql/relational-databases/search/semantic-search-sql-server) | Nem | Nem |
| [Sorozatszámok száma](https://docs.microsoft.com/sql/relational-databases/sequence-numbers/sequence-numbers) | Igen | Igen |
| [Service Broker](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-service-broker) | Nem | Igen – lásd: [Szolgáltatásátvitelszervező-eltérések](sql-database-managed-instance-transact-sql-information.md#service-broker) |
| [Kiszolgáló konfigurációs beállításai](https://docs.microsoft.com/sql/database-engine/configure-windows/server-configuration-options-sql-server) | Nem | Igen – lásd: [a T-SQL eltérései](sql-database-managed-instance-transact-sql-information.md) |
| [Utasítások megadása](https://docs.microsoft.com/sql/t-sql/statements/set-statements-transact-sql) | A legtöbb – tekintse meg az egyes utasításokat | Igen – lásd: [a T-SQL eltérései](sql-database-managed-instance-transact-sql-information.md)|
| [SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | Igen | Igen |
| [Spatial](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) | Igen | Igen |
| [SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | Igen | Igen |
| [SQL Data Sync](sql-database-get-started-sql-data-sync.md) | Igen | Nem |
| [SQL Server Agent](https://docs.microsoft.com/sql/ssms/agent/sql-server-agent) | Nem – lásd: [rugalmas feladatok](sql-database-elastic-jobs-getting-started.md) | Igen – lásd: [különbségek az SQL Server Agent](sql-database-managed-instance-transact-sql-information.md#sql-server-agent) |
| [SQL Server Analysis Services (SSAS)](https://docs.microsoft.com/sql/analysis-services/analysis-services) | Nem – lásd: [az Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) | Nem – lásd: [Azure Analysis Services](https://azure.microsoft.com/services/analysis-services/) |
| [SQL Server Auditing](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine) | Nem – lásd: [SQL Database naplózási szolgáltatásával](sql-database-auditing.md) | Igen – lásd: [különbségek naplózása](sql-database-managed-instance-transact-sql-information.md#auditing) |
| [SQL Server Data Tools (SSDT)](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt) | Igen | Igen |
| [SQL Server Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/sql-server-integration-services) | Igen, az Azure Data Factory (ADF) környezetben, ahol csomagok találhatók az SSISDB üzemelteti az Azure SQL Database és az Azure SSIS integrációs modul (IR) végrehajtott, egy felügyelt SSIS lásd [Azure-SSIS integrációs modul létrehozása az ADF-ben](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Hasonlítsa össze az SSIS-funkciók az SQL Database-kiszolgáló és a felügyelt példány, lásd: [hasonlítsa össze az Azure SQL Database önálló adatbázisok és rugalmas készletek és a felügyelt példány](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). | Igen, az Azure Data Factory (ADF) környezetben, ahol csomagok találhatók az SSISDB felügyelt példány által üzemeltetett és az Azure SSIS integrációs modul (IR) végrehajtott, egy felügyelt SSIS lásd [Azure-SSIS integrációs modul létrehozása az ADF-ben](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). <br/><br/>Hasonlítsa össze az SSIS-funkciók az SQL Database felügyelt példány, lásd: [hasonlítsa össze az Azure SQL Database önálló adatbázisok és rugalmas készletek és a felügyelt példány](../data-factory/create-azure-ssis-integration-runtime.md#compare-sql-database-single-databaseelastic-pool-and-sql-database-managed-instance). |
| [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) | Igen | Igen |
| [SQL Server PowerShell](https://docs.microsoft.com/sql/relational-databases/scripting/sql-server-powershell) | Igen | Igen |
| [SQL Server Profiler](https://docs.microsoft.com/sql/tools/sql-server-profiler/sql-server-profiler) | Nem – lásd: [bővített események](sql-database-xevent-db-diff-from-svr.md) | Igen |
| [SQL Server Replication](https://docs.microsoft.com/sql/relational-databases/replication/sql-server-replication) | [Kizárólag tranzakciós és pillanatkép-replikációs előfizetők](sql-database-single-database-migrate.md) | Igen, a [nyilvános előzetes verzió](https://docs.microsoft.com/sql/relational-databases/replication/replication-with-sql-database-managed-instance) |
| [SQL Server Reporting Services (SSRS)](https://docs.microsoft.com/sql/reporting-services/create-deploy-and-manage-mobile-and-paginated-reports) | Nem - [tekintse meg a Power BI](https://docs.microsoft.com/power-bi/) | Nem - [tekintse meg a Power BI](https://docs.microsoft.com/power-bi/) |
| [Tárolt eljárások](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) | Igen | Igen |
| [A rendszer tárolt függvényei](https://docs.microsoft.com/sql/relational-databases/system-functions/system-functions-for-transact-sql) | Most – tekintse meg az egyes függvények | Igen – lásd: [tárolt eljárások, függvények, eseményindítók különbségek](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Rendszerszintű tárolt eljárásokra](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/system-stored-procedures-transact-sql) | Néhány – lásd: az egyes tárolt eljárások | Igen – lásd: [tárolt eljárások, függvények, eseményindítók különbségek](sql-database-managed-instance-transact-sql-information.md#stored-procedures-functions-triggers) |
| [Rendszertáblák](https://docs.microsoft.com/sql/relational-databases/system-tables/system-tables-transact-sql) | Néhány – lásd: az egyes táblák | Igen – lásd: [a T-SQL eltérései](sql-database-managed-instance-transact-sql-information.md) |
| [Rendszerkatalógus-nézetek](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/catalog-views-transact-sql) | Néhány – lásd: az egyes nézetek | Igen – lásd: [a T-SQL eltérései](sql-database-managed-instance-transact-sql-information.md) |
| [ideiglenes táblákkal](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql#database-scoped-global-temporary-tables-azure-sql-database) | A helyi és adatbázishoz kötődő globális ideiglenes táblák | Helyi és a példány hatókörű globális ideiglenes táblák |
| [Historikus táblák](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables) | [Igen](sql-database-temporal-tables.md) | [Igen](sql-database-temporal-tables.md) |
|Fenyegetések észlelése|  [Igen](sql-database-threat-detection.md)|[Igen](sql-database-managed-instance-threat-detection.md)|
| [Nyomkövetési jelzők](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-trace-flags-transact-sql) | Nem | Nem |
| [Változók](https://docs.microsoft.com/sql/t-sql/language-elements/variables-transact-sql) | Igen | Igen |
| [Transzparens adattitkosítás (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-tde) | Igen – általános célú és a kritikus fontosságú üzleti szolgáltatásszintek csak| [Igen](transparent-data-encryption-azure-sql.md) |
[Virtuális hálózat](../virtual-network/virtual-networks-overview.md) | Részben – lásd: [VNet-végpontok](sql-database-vnet-service-endpoint-rule-overview.md) | Igen, csak a Resource Manager-modell |
| [Windows Server feladatátvételi fürtszolgáltatás](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) | [Magas rendelkezésre állású](sql-database-high-availability.md) minden adatbázis részét képezi. Vész-helyreállítási a következő cikkben [az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése](sql-database-business-continuity.md) | [Magas rendelkezésre állású](sql-database-high-availability.md) minden adatbázis részét képezi. Vész-helyreállítási a következő cikkben [az Azure SQL Database üzletmenet-folytonossági funkcióinak áttekintése](sql-database-business-continuity.md) |
| [XML-indexek](https://docs.microsoft.com/sql/t-sql/statements/create-xml-index-transact-sql) | Igen | Igen |

## <a name="next-steps"></a>További lépések

- Az Azure SQL Database szolgáltatással kapcsolatos tudnivalók: [Mi az SQL Database?](sql-database-technical-overview.md)
- A felügyelt példánnyal kapcsolatos további információkért lásd: [mit jelent a felügyelt példány?](sql-database-managed-instance.md).
