# Áttekintés
## [Mi az SQL Database?](sql-database-technical-overview.md)
### [Szolgáltatásszintek](sql-database-service-tiers.md)
### [Adatbázisok tranzakciós egységei](sql-database-what-is-a-dtu.md)
### [DTU-teljesítményteszt – áttekintés](sql-database-benchmark-overview.md)
### [Erőforráskorlátok](sql-database-resource-limits.md)
### [Szolgáltatások](sql-database-features.md)
### [SQL Database GYIK](sql-database-faq.md)
## Előnyök
### [Tanulás és alkalmazkodás](sql-database-learn-and-adapt.md)
### [Skálázás menet közben](sql-database-scale-on-the-fly.md)
### [Több-bérlős alkalmazások fejlesztése](sql-database-build-multi-tenant-apps.md)
### [Biztonság és védelem](sql-database-helps-secures-and-protects.md)
### [Működés saját környezetben](sql-database-works-in-your-environment.md)
## Összehasonlítások
### [Az SQL Database és a virtuális gépen futó SQL összehasonlítása](sql-database-paas-vs-sql-server-iaas.md)
### [A T-SQL eltérései](sql-database-transact-sql-information.md)
### [Az SQL és a NoSQL összehasonlítása](../documentdb/documentdb-nosql-vs-sql.md)
## [Az SQL Database eszközei](sql-database-manage-overview.md)
## [Az SQL Database oktatóanyagai](sql-database-explore-tutorials.md)
## [A megoldások használatának első lépései](sql-database-solution-quick-starts.md)
## Biztonság
### [Azure Security Center az Azure SQL Database-hez](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
### [SQL Security Center](https://msdn.microsoft.com/library/azure/bb510589)
# Első lépések
## Adatbázisok és kiszolgálók
### Önálló adatbázisok
#### Tanulás
##### [Azure Portal oktatóanyag: Azure SQL-adatbázisok létrehozása az Azure Portal használatával](sql-database-get-started.md)
#### Ajánlott
##### [Azure Portal oktatóanyag: Azure SQL-adatbázisok létrehozása az Azure Portal használatával](sql-database-get-started.md)
##### [Azure Portal oktatóanyag: Azure SQL-adatbázisok létrehozása a PowerShell használatával](sql-database-get-started-powershell.md)
##### [Azure Portal oktatóanyag: Azure SQL-adatbázisok létrehozása C# használatával](sql-database-get-started-csharp.md)
### Rugalmas adatbáziskészletek
#### Tanulás
##### [Rugalmas adatbáziskészletek](sql-database-elastic-pool.md)
##### [Mikor érdemes rugalmas adatbáziskészletet használni?](sql-database-elastic-pool-guidance.md)
##### [A rugalmas eszköz díjszabása](sql-database-elastic-pool-price.md)
#### Ajánlott
##### [Létrehozás az Azure Portal használatával](sql-database-elastic-pool-create-portal.md)
##### [Létrehozás a PowerShell használatával](sql-database-elastic-pool-create-powershell.md)
##### [Létrehozás C# használatával](sql-database-elastic-pool-create-csharp.md)
### Méretezés
#### Tanulás
##### [Méretezés](sql-database-elastic-scale-introduction.md)
##### [Rugalmas skálázás](sql-database-elastic-scale-get-started.md)
##### [Skálázható felhőalapú adatbázisok készítése](sql-database-elastic-database-client-library.md)
##### [Adatbázisközi feladatok](sql-database-elastic-jobs-overview.md)
##### [Rugalmas eszközök – gyakori kérdések](sql-database-elastic-scale-faq.md)
##### [Rugalmas adatbáziseszközökkel kapcsolatos kifejezések](sql-database-elastic-scale-glossary.md)
##### [Adatok mozgatása kiterjesztett felhőalapú adatbázisok között](sql-database-elastic-scale-overview-split-and-merge.md)
#### Ajánlott
##### [Rugalmas feladatok](sql-database-elastic-jobs-getting-started.md)
##### [Adatbázisközi jelentések](sql-database-elastic-query-getting-started.md)
##### [Adatbázisközi lekérdezések](sql-database-elastic-query-getting-started-vertical.md)
##### [Rugalmas feladatok eltávolítása](sql-database-elastic-jobs-uninstall.md)
## Adatok migrálása és áthelyezése
### Tanulás
#### [Adatbázisok migrálása](sql-database-cloud-migrate.md)
#### [Tranzakciós replikáció](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
#### [Adatszinkronizálás](sql-database-get-started-sql-data-sync.md)
#### [SQL-adatbázis másolása](sql-database-copy.md)
### Ajánlott
#### Adatbázis-kompatibilitás meghatározása
##### [SQL Package segédprogram](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
#### Adatbázis-kompatibilitási problémák megoldása
##### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
##### [SQL Azure áttelepítési varázsló](sql-database-cloud-migrate-fix-compatibility-issues.md)
##### [SQL Server Management Studio Migration Wizard](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
#### Adatbázis másolása
##### [Másolás az Azure Portal használatával](sql-database-copy-portal.md)
##### [Másolás a PowerShell használatával](sql-database-copy-powershell.md)
##### [Másolás a T-SQL használatával](sql-database-copy-transact-sql.md)
#### Adatbázis exportálása BACPAC-fájlba
##### [Azure Portal](sql-database-export.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
##### [SQL Package segédprogram](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
##### [PowerShell](sql-database-export-powershell.md)
#### Adatbázis importálása BACPAC-fájlból
##### [Azure Portal](sql-database-import.md)
##### [PowerShell](sql-database-import-powershell.md)
##### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
##### [SQL Package segédprogram](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
#### [Betöltés CSV-fájlból BCP használatával](sql-database-load-from-csv-with-bcp.md)
## Hitelesítés és hozzáférés biztosítása
### Tanulás
#### [Áttekintés](sql-database-security.md)
#### [Biztonsági irányelvek](sql-database-security-guidelines.md)
#### [Bejelentkezések kezelése](sql-database-manage-logins.md)
### Ajánlott
### [Oktatóanyag: SQL-hitelesítés és -hozzáférés](sql-database-get-started-security.md)
## Az adatok védelme
### Tanulás
#### Naplózás
##### [Naplózás](sql-database-auditing-get-started.md)
##### [Alacsonyabb szintű ügyfelek támogatása és a naplózás IP-végpontjainak módosítása](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [Fenyegetések észlelése](sql-database-threat-detection-get-started.md)
#### Adatok titkosítása
##### [Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)
##### [Windows tanúsítványtároló](sql-database-always-encrypted.md)
##### [Transzparens adattitkosítás](https://msdn.microsoft.com/library/azure/dn948096)
##### [Oszloptitkosítás](https://msdn.microsoft.com/library/azure/ms179331)
#### Adatok maszkolása
##### Dinamikus adatmaszkolás
###### [Azure Portal](sql-database-dynamic-data-masking-get-started.md)
### Ajánlott
#### [Dinamikus adatmaszkolás az Azure Portal használatával](sql-database-dynamic-data-masking-get-started.md)
## Az üzletmenet folytonossága
### Tanulás
#### [Áttekintés](sql-database-business-continuity.md)
#### [Adatbázisok biztonsági mentése](sql-database-automated-backups.md)
#### [Hosszú távú megőrzés](sql-database-long-term-retention.md)
#### [Adatbázis-helyreállítás biztonsági másolatokkal](sql-database-recovery-using-backups.md)
#### [A vészhelyreállítás hitelesítési követelményei](sql-database-geo-replication-security-config.md)
#### [Folyamatos üzletmenet – tervezési forgatókönyvek](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Vészhelyreállítási stratégiák rugalmas készletekkel](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [A működés közbeni frissítés](sql-database-manage-application-rolling-upgrade.md)
### Ajánlott
#### Törölt adatbázis visszaállítása
##### [Azure Portal](sql-database-restore-deleted-database-portal.md)
##### [PowerShell](sql-database-restore-deleted-database-powershell.md)
#### Adott időpontnak megfelelő helyreállítás
##### [Azure Portal](sql-database-point-in-time-restore-portal.md)
##### [PowerShell](sql-database-point-in-time-restore-powershell.md)
#### Georedundáns helyreállítási adatbázisok
##### [Azure Portal](sql-database-geo-restore-portal.md)
##### [PowerShell](sql-database-geo-restore-powershell.md)
#### [Egy táblázat](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [Helyreállítás adatközpont-kimaradás után](sql-database-disaster-recovery.md)
#### [Vészhelyreállítási próba végrehajtása](sql-database-disaster-recovery-drills.md)
### Adatbázisok replikálása
#### [Aktív georeplikáció áttekintése](sql-database-geo-replication-overview.md)
#### Aktív georeplikáció konfigurálása
##### [Azure Portal](sql-database-geo-replication-portal.md)
##### [PowerShell](sql-database-geo-replication-powershell.md)
##### [T-SQL](sql-database-geo-replication-transact-sql.md)
#### Feladatátvétel aktív georeplikációval
##### [Azure Portal](sql-database-geo-replication-failover-portal.md)
##### [PowerShell](sql-database-geo-replication-failover-powershell.md)
##### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)
## Alkalmazás-fejlesztés
### Tanulás
#### [Adatbázisok alkalmazás-fejlesztésének áttekintése](sql-database-develop-overview.md)
#### [Csatlakozási kódtárak](sql-database-libraries.md)
#### [Több-bérlős SaaS-alkalmazások](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Az 1433-as porton túli portok használata az ADO.NET 4.5 szoftverrel](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [Alkalmazás hitelesítéséhez szükséges értékek beolvasása](sql-database-client-id-keys.md)
### Ajánlott
#### Alkalmazások csatlakoztatása
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [C és C++](sql-database-develop-cplusplus-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.js](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [Python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [Csatlakozás a Visual Studio használatával](sql-database-connect-query.md)
#### [Ügyfélalkalmazás létrehozása](https://www.microsoft.com/sql-server/developer-get-started)
#### [Hibaüzenetek használata](sql-database-develop-error-messages.md)
### Az ügyfél által megvalósított szolgáltatott szoftvermegoldások
#### [Daxko/CSI Software](sql-database-implementation-daxko.md)
#### [GEP](sql-database-implementation-gep.md)
#### [SnelStart](sql-database-implementation-snelstart.md)
#### [Umbraco](sql-database-implementation-umbraco.md)
## Adatbázis-fejlesztés
### Tanulás
#### Historikus táblák
##### [Historikus táblák](sql-database-temporal-tables.md)
##### [Adatmegőrzési házirendek](sql-database-temporal-tables-retention-policy.md)
#### [JSON-adatok](sql-database-json-features.md)
#### [Memóriabeli](sql-database-in-memory.md)
### Ajánlott
#### [SQL Server-fejlesztés](https://msdn.microsoft.com/library/ms179422.aspx)
## Megfigyelés és finomhangolás
### Tanulás
#### [Önálló adatbázisok](sql-database-single-database-monitor.md)
#### [Az SQL Database Advisor áttekintése](sql-database-advisor.md)
#### [Önálló adatbázisok – útmutató](sql-database-performance-guidance.md)
#### [Számítási feladatok elemzése az Azure Portalon](sql-database-performance.md)
#### [Kötegelés használata](sql-database-use-batching-to-improve-performance.md)
## SQL Database V11
### [Webes és Üzleti kiadás – kivonás](sql-database-web-business-sunset-faq.md)
### [Service tier advisor](sql-database-service-tier-advisor.md)
### [Rugalmas készlet felmérőeszköze](sql-database-elastic-pool-database-assessment-powershell.md)
### [Váltás a V12-re](sql-database-v12-plan-prepare-upgrade.md)
#### [Magasabb szintű verzióra váltás az Azure Portal használatával](sql-database-upgrade-server-portal.md)
#### [Magasabb szintű verzióra váltás az PowerShell használatával](sql-database-upgrade-server-powershell.md)
#### [Tarifacsomag-javaslatok](sql-database-service-tier-advisor.md)
# Útmutató
## Létrehozás és kezelés
### Kiszolgálók és adatbázisok
#### [Önálló adatbázisok](sql-database-manage-portal.md)
#### [Azure Portal](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### Rugalmas adatbáziskészletek
#### [Létrehozás az Azure Portal használatával](sql-database-elastic-pool-create-portal.md)
#### [Létrehozás a PowerShell használatával](sql-database-elastic-pool-create-powershell.md)
#### [Létrehozás C# használatával](sql-database-elastic-pool-create-csharp.md)
#### [Kezelés az Azure Portal használatával](sql-database-elastic-pool-manage-portal.md)
#### [Kezelés a PowerShell használatával](sql-database-elastic-pool-manage-powershell.md)
#### [Kezelés a C# használatával](sql-database-elastic-pool-manage-csharp.md)
#### [Kezelés a T-SQL használatával](sql-database-elastic-pool-manage-tsql.md)
### Szilánkokra osztott adatbázisok
#### [Szilánkleképezés-kezelő használata](sql-database-elastic-scale-shard-map-management.md)
#### [Biztonsági konfiguráció felosztása és egyesítése](sql-database-elastic-scale-split-merge-security-configuration.md)
#### [A Dapper használata](sql-database-elastic-scale-working-with-dapper.md)
#### [Az Entity Framework használata](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md)
#### [Sorszintű biztonság több bérlő esetén](sql-database-elastic-tools-multi-tenant-row-level-security.md)
#### [Hitelesítő adatok kezelése](sql-database-elastic-scale-manage-credentials.md)
#### [Felosztási-egyesítési szolgáltatás üzembe helyezése](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [Szilánk hozzáadása](sql-database-elastic-scale-add-a-shard.md)
#### [Horizontális skálázási térképek javítása a RecoveryManager osztállyal](sql-database-elastic-database-recovery-manager.md)
###  Authentication
#### [Azure AD-hitelesítés](sql-database-aad-authentication.md)
#### [Többtényezős hitelesítés](sql-database-ssms-mfa-authentication.md)
### Tűzfalszabályok
#### [Azure Portal](sql-database-configure-firewall-settings.md)
#### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
#### [REST API](sql-database-configure-firewall-settings-rest.md)
#### [T-SQL](sql-database-configure-firewall-settings-tsql.md)
### Feladatok és automatizálás
#### [Szolgáltatástelepítés](sql-database-elastic-jobs-service-installation.md)
#### [Rugalmas feladatok létrehozása és kezelése az Azure Portalban](sql-database-elastic-jobs-create-and-manage.md)
#### [Rugalmas feladatok létrehozása és kezelése a PowerShell használatával](sql-database-elastic-jobs-powershell.md)
#### [A klienskódtár frissítése](sql-database-elastic-scale-upgrade-client-library.md)
#### [SQL Database adatbázisok kezelése az Azure Automation szolgáltatással](sql-database-manage-automation.md)
### [Tűzfal](sql-database-firewall-configure.md)
## Adatok titkosítása
### [Mindig titkosított – áttekintés](sql-database-always-encrypted.md)
### [Transzparens adattitkosítás](https://msdn.microsoft.com/library/azure/dn948096)
### [Oszloptitkosítás](https://msdn.microsoft.com/library/azure/ms179331)
## Migrate (Áttelepítés)
### Kompatibilitás meghatározása
#### [SQL Package segédprogram](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-determine-compatibility-ssms.md)
### Kompatibilitási problémák megoldása
#### [SQL Server Data Tools](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)
#### [SQL Azure áttelepítési varázsló](sql-database-cloud-migrate-fix-compatibility-issues.md)
### [Az SQL Server Management Studio áttelepítési varázsló használata](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
### [Tranzakciós replikáció használata](sql-database-cloud-migrate-compatible-using-transactional-replication.md)
### [Meglévő, horizontálisan felskálázott adatbázisok migrálása horizontális felskálázáshoz](sql-database-elastic-convert-to-use-elastic-tools.md)
## Figyelés és hangolás
### [Lekérdezési terheléselemző](sql-database-query-performance.md)
### [SQL Database Advisor](sql-database-advisor-portal.md)
### [DMV-k](sql-database-monitoring-with-dmvs.md)
### [Kompatibilitási szintek](sql-database-compatibility-level-query-performance-130.md)
### [Teljesítményszámlálók a szilánkleképezés-kezelőhöz](sql-database-elastic-database-perf-counters.md)
### [Teljesítmény-finomhangolási tippek](sql-database-troubleshoot-performance.md)
### Szolgáltatásszintek és teljesítményszintek váltása
#### [Az Azure Portal használata](sql-database-scale-up.md)
#### [A PowerShell használata](sql-database-scale-up-powershell.md)
### [Riasztások létrehozása](sql-database-insights-alerts-portal.md)
### Memóriabeli OLTP
#### [Memóriabeli OLTP beállítása](sql-database-in-memory-oltp-migration.md)
#### [Memóriabeli OLTP-tár figyelése](sql-database-in-memory-oltp-monitoring.md)
### Lekérdezéstár
#### [Teljesítmény figyelése a Lekérdezéstár használatával](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Lekérdezéstár – használati forgatókönyvek](https://msdn.microsoft.com/library/mt614796.aspx)
#### [A Lekérdezéstár működtetése](sql-database-operate-query-store.md)
### Bővített események
#### [Bővített események](sql-database-xevent-db-diff-from-svr.md)
#### [Eseményfájl célkódja](sql-database-xevent-code-event-file.md)
#### [Gyűrűpuffer célkódja](sql-database-xevent-code-ring-buffer.md)
## Adatok áthelyezése
### [SQL-adatbázis másolása](sql-database-copy.md)
#### [Azure Portal](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### Adatbázis exportálása BACPAC-fájlba
#### [Azure Portal](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [SQL Package segédprogram](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### Adatbázis importálása BACPAC-fájlból
#### [Azure Portal](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [SQL Package segédprogram](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
### [Betöltés CSV-fájlból BCP használatával](sql-database-load-from-csv-with-bcp.md)
### [Adatok mozgatása kiterjesztett felhőalapú adatbázisok között](sql-database-elastic-scale-overview-split-and-merge.md)
## Lekérdezés
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [Többszilánkos lekérdezés](sql-database-elastic-scale-multishard-querying.md)
### Adatbázisközi lekérdezések
#### [Áttekintés](sql-database-elastic-query-overview.md)
#### [Adatbázisközi lekérdezés különböző sémákkal](sql-database-elastic-query-vertical-partitioning.md)
#### [Adatbázisközi jelentéskészítés](sql-database-elastic-query-horizontal-partitioning.md)
#### [Elosztott tranzakciók több felhőalapú adatbázisban](sql-database-elastic-transactions-overview.md)
## Visszaállítás
### Törölt adatbázis visszaállítása
#### [Azure Portal](sql-database-restore-deleted-database-portal.md)
#### [PowerShell](sql-database-restore-deleted-database-powershell.md)
### Időponthoz kötött visszaállítás
#### [Azure Portal](sql-database-point-in-time-restore-portal.md)
#### [PowerShell](sql-database-point-in-time-restore-powershell.md)
### Georedundáns helyreállítás
#### [Azure Portal](sql-database-geo-restore-portal.md)
#### [PowerShell](sql-database-geo-restore-powershell.md)
#### [Egy táblázat](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
### [Helyreállítás adatközpont-kimaradás után](sql-database-disaster-recovery.md)
### [Vészhelyreállítási próba végrehajtása](sql-database-disaster-recovery-drills.md)
## Replikálás
### [Aktív georeplikáció áttekintése](sql-database-geo-replication-overview.md)
### Konfigurálás
#### [PowerShell](sql-database-geo-replication-powershell.md)
#### [T-SQL](sql-database-geo-replication-transact-sql.md)
### Feladatátvétel
#### [Azure Portal](sql-database-geo-replication-failover-portal.md)
#### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [T-SQL](sql-database-geo-replication-failover-transact-sql.md)

## Hibaelhárítás
### [Kapcsolódási problémák](sql-database-troubleshoot-common-connection-issues.md)
### [Átmeneti kapcsolódási hiba](sql-database-troubleshoot-connection.md)
### [Diagnosztizálás és megelőzés](sql-database-connectivity-issues.md)
### [Engedélyek](sql-database-troubleshoot-permissions.md)
### [Adatbázisok áthelyezése](sql-database-troubleshoot-moving-data.md)


# Referencia
## [PowerShell](/powershell/azureps-cmdlets-docs/)
## [Klasszikus PowerShell](/powershell/servicemanagement/)
## [Java](/java/api/)
## [.NET](/dotnet/api/)
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## [Azure SQL Database-parancsmagok](/powershell/resourcemanager/AzureRM.Sql/v1.0.12/AzureRM.Sql)
## [SQL Server-parancsmagok](https://msdn.microsoft.com/library/mt740629.aspx)
## [REST](/rest/api/sql/)

## SQL-adatbázisok felügyelete – függvénytár
### [SQL-adatbázisok felügyelete – függvénytár-referencia](https://msdn.microsoft.com/library/azure/mt349017.aspx)
### [Az SQL-adatbázisok felügyelete függvénytárcsomag beszerzése](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [SQL Server-illesztőprogramok](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)
### [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
### [Python](https://msdn.microsoft.com/library/mt652092.aspx)
### [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)

# Erőforrások
## [Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/)
## [MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=ssdsgetstarted)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/sql-azure)
## [Videók](https://azure.microsoft.com/documentation/videos/index/?services=sql-database)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?service=sql-database)
## [SQL Server-eszközök](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)


<!--HONumber=Nov16_HO4-->


