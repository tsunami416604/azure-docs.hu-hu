# Áttekintés
## [Mi az SQL Database?](sql-database-technical-overview.md)
## Szolgáltatások
### [Szolgáltatásszintek](sql-database-service-tiers.md)
### [Mi az a DTU?](sql-database-what-is-a-dtu.md)
### [DTU-teljesítményteszt – áttekintés](sql-database-benchmark-overview.md)
### [SQL-adatbázis – tűzfal és a tűzfalszabályok](sql-database-firewall-configure.md)
### [Felügyeleti eszközök](sql-database-manage-overview.md)
## Megfontolások és korlátozások
### [Az SQL Database és a virtuális gépen futó SQL összehasonlítása](sql-database-paas-vs-sql-server-iaas.md)
### [A T-SQL eltérései](sql-database-transact-sql-information.md)
### [Erőforráskorlátok](sql-database-resource-limits.md)
### [Általános korlátozások](sql-database-general-limitations.md)
## [Díjszabás](https://azure.microsoft.com/pricing/details/sql-database/)
## [Újdonságok](https://azure.microsoft.com/updates/?service=sql-database)
## [SQL Database GYIK](sql-database-faq.md)

## Előnyök
### [Tanulás és alkalmazkodás](sql-database-learn-and-adapt.md)
### [Skálázás menet közben](sql-database-scale-on-the-fly.md)
### [Több-bérlős alkalmazások fejlesztése](sql-database-build-multi-tenant-apps.md)
### [Biztonság és védelem](sql-database-helps-secures-and-protects.md)
### [Működés saját környezetben](sql-database-works-in-your-environment.md)

## Forgatókönyvek
### Kiszolgálók, készletek, adatbázisok és tűzfalak létrehozása és kezelése
#### Rugalmas adatbáziskészletek létrehozása és kezelése
#### [Mikor érdemes rugalmas adatbáziskészletet használni?](sql-database-elastic-pool-guidance.md)
#### [Biztonsági irányelvek](sql-database-security-guidelines.md)
#### [Rugalmas adatbáziskészletek](sql-database-elastic-pool.md)
#### [Automatizálás](sql-database-manage-automation.md)
#### Szolgáltatásszintek és teljesítményszintek módosítása
##### [Azure [Portal](sql-database-scale-up.md)
##### [PowerShell](sql-database-scale-up-powershell.md)
### Horizontálisan felskálázott adatbázisok létrehozása és kezelése
#### [Áttekintés](sql-database-elastic-scale-introduction.md)
#### [Skálázható felhőalapú adatbázisok készítése](sql-database-elastic-database-client-library.md)
#### [Horizontális felskálázás a szilánkleképezés-kezelővel](sql-database-elastic-scale-shard-map-management.md)
#### [Meglévő adatbázisok migrálása horizontális felskálázáshoz](sql-database-elastic-convert-to-use-elastic-tools.md)
#### [Adatok mozgatása kiterjesztett felhőalapú adatbázisok között](sql-database-elastic-scale-overview-split-and-merge.md)
#### [Az Entity Framework használata](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [A Dapper használata](sql-database-elastic-scale-working-with-dapper.md)
#### [Teljesítményszámlálók a szilánkleképezés-kezelőhöz](sql-database-elastic-database-perf-counters.md)
#### Adatbázisközi feladatok és lekérdezések
##### [Adatbázisközi lekérdezés](sql-database-elastic-query-overview.md)
##### [Adatbázisközi lekérdezés különböző sémákkal](sql-database-elastic-query-vertical-partitioning.md)
##### [Adatbázisközi jelentéskészítés](sql-database-elastic-query-horizontal-partitioning.md)
##### [Adatbázisközi feladatok](sql-database-elastic-jobs-overview.md)
##### [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md)
##### [Többszilánkos lekérdezés](sql-database-elastic-scale-multishard-querying.md)
##### [Sorszintű biztonság több bérlő esetén](sql-database-elastic-tools-multi-tenant-row-level-security.md)
##### [Elosztott tranzakciók több felhőalapú adatbázisban](sql-database-elastic-transactions-overview.md)
####[Rugalmas adatbáziseszközökkel kapcsolatos kifejezések](sql-database-elastic-scale-glossary.md)
#### [Gyakori kérdések](sql-database-elastic-scale-faq.md)
### Hozzáférés és engedélyek létrehozása és kezelése
#### [Áttekintés](sql-database-security.md)
#### [Biztonsági irányelvek](sql-database-security-guidelines.md)
#### [Bejelentkezések kezelése](sql-database-manage-logins.md)
#### [Azure Security Center az Azure SQL Database-hez](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
#### [SQL Security Center](https://msdn.microsoft.com/library/azure/bb510589)
### [Adatbázis- és alkalmazásfejlesztés](sql-database-develop-overview.md)
### Adatbázis-áttelepítés
#### [Microsoft SQL Server-adatbázis áttelepítése](sql-database-cloud-migrate.md)
### Az üzletmenet folytonosságának biztosítása
#### [Áttekintés](sql-database-business-continuity.md)
#### [Adatbázisok biztonsági mentése](sql-database-automated-backups.md) 
#### [Adatbázis-helyreállítás biztonsági másolatokkal](sql-database-recovery-using-backups.md)
#### [Helyreállítás adatközpont-kimaradás után](sql-database-disaster-recovery.md)
#### [A vészhelyreállítás hitelesítési követelményei](sql-database-geo-replication-security-config.md)
#### [Folyamatos üzletmenet – tervezési forgatókönyvek](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Vészhelyreállítási stratégiák rugalmas készletekkel](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [A működés közbeni frissítés](sql-database-manage-application-rolling-upgrade.md)
### Az adatbázisok figyelése és hangolása
#### [Önálló adatbázisok](sql-database-single-database-monitor.md)
#### [Önálló adatbázisok – útmutató](sql-database-performance-guidance.md)
#### [Számítási feladatok elemzése az Azure Portalon](sql-database-performance.md)

## Ügyfelek implementációi
### [Daxko/CSI Software](sql-database-implementation-daxko.md)
### [GEP](sql-database-implementation-gep.md)
### [SnelStart](sql-database-implementation-snelstart.md)
### [Umbraco](sql-database-implementation-umbraco.md)

# Első lépések
## Kiszolgálók, készletek, adatbázisok és tűzfalak létrehozása
### [Azure Portal](sql-database-get-started.md)
### [PowerShell](sql-database-get-started-powershell.md)
### [C#](sql-database-get-started-csharp.md)
## Adatok lekérdezése
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
## Kiszolgálók, készletek, adatbázisok és tűzfalak kezelése
### [Azure Portal](sql-database-manage-portal.md)
### [SQL Server Management Studio](sql-database-manage-azure-ssms.md)
### [PowerShell](sql-database-manage-powershell.md)
## [Hozzáférés és engedélyek létrehozása és kezelése](sql-database-get-started-security.md)
## Az adatok védelme
### [Naplózás](sql-database-auditing-get-started.md)
### [Fenyegetések észlelése](sql-database-threat-detection-get-started.md)
### Dinamikus adatmaszkolás
#### [Azure Portal](sql-database-dynamic-data-masking-get-started-portal.md)
## Horizontálisan felskálázott adatbázisok létrehozása és kezelése
### [Rugalmas skálázás](sql-database-elastic-scale-get-started.md)
### [Rugalmas feladatok](sql-database-elastic-jobs-getting-started.md)
### Rugalmas lekérdezések
### [Adatbázisközi jelentések](sql-database-elastic-query-getting-started.md)
### [Adatbázisközi lekérdezések](sql-database-elastic-query-getting-started-vertical.md)
## [Memóriabeli optimalizálás](sql-database-in-memory.md)
## [Adatbázisok áthelyezése](sql-database-troubleshoot-moving-data.md)
## [Adatszinkronizálás](sql-database-get-started-sql-data-sync.md)
##Az adatbázisok figyelése és hangolása
### [Az SQL Database Advisor áttekintése](sql-database-advisor.md)
### [Számítási feladatok elemzése az Azure Portalon](sql-database-performance.md)
## [A megoldások használatának első lépései](sql-database-solution-quick-starts.md)
# Útmutató
## [Naplózás](sql-database-auditing-get-started.md)
## Létrehozás
### Erőforráscsoport
#### [PowerShell](sql-database-manage-powershell.md#create-a-resource-group)
### Kiszolgálók
#### [Azure Portal](sql-database-get-started.md)
#### [C#](sql-database-get-started-csharp.md)
#### [PowerShell](sql-database-manage-powershell.md#create-a-sql-database-server)
### Rugalmas adatbáziskészletek
#### [Azure Portal](sql-database-elastic-pool-create-portal.md)
#### [C#](sql-database-elastic-pool-create-csharp.md)
#### [PowerShell](sql-database-elastic-pool-create-powershell.md)
### Adatbázisok
#### Önálló adatbázisok
##### [Azure Portal](sql-database-get-started.md)
##### [C#](sql-database-get-started-csharp.md)
##### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
##### [PowerShell](sql-database-get-started-powershell.md)
#### [SQL Server Management Studio](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
#### Szilánkokra osztott adatbázisok
##### [Szilánkleképezés-kezelő használata](sql-database-elastic-scale-shard-map-management.md)
##### [Biztonsági konfiguráció felosztása és egyesítése](sql-database-elastic-scale-split-merge-security-configuration.md)
##### [A Dapper használata](sql-database-elastic-scale-working-with-dapper.md)
##### [Az Entity Framework használata](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
##### [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md)
##### [Sorszintű biztonság több bérlő esetén](sql-database-elastic-tools-multi-tenant-row-level-security.md)
### Tűzfalszabályok
#### Kiszolgáló
##### [Azure Portal](sql-database-configure-firewall-settings.md)
##### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
##### [REST API](sql-database-configure-firewall-settings-rest.md)
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#server-level-firewall-rules)
#### Adatbázis
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules)
### Feladatok
#### [Szolgáltatástelepítés](sql-database-elastic-jobs-service-installation.md)
#### [Azure Portal](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell](sql-database-elastic-jobs-powershell.md)
### Bejelentkezések
#### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-logins.md)
## Fejlesztés
### [Áttekintés](https://msdn.microsoft.com/library/mt763826.aspx)
### Forgatókönyvek
#### [Több-bérlős SaaS-alkalmazások](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### Historikus táblák
##### [Historikus táblák](sql-database-temporal-tables.md)
##### [Adatmegőrzési házirendek](sql-database-temporal-tables-retention-policy.md)
#### [JSON-adatok](sql-database-json-features.md)
#### [Memóriabeli](sql-database-in-memory.md)
###Bevezetés
#### [Csatlakozási kódtárak](sql-database-libraries.md)
#### Alkalmazások csatlakoztatása
##### [.NET](sql-database-develop-dotnet-simple.md)
##### [Java](sql-database-develop-java-simple.md)
##### [Node.js](sql-database-develop-nodejs-simple.md)
##### [PHP](sql-database-develop-php-simple.md)
##### [Python](sql-database-develop-python-simple.md)
##### [Ruby](sql-database-develop-ruby-simple.md)
##### [Excel](sql-database-connect-excel.md)
#### [Csatlakozás a Visual Studio használatával](sql-database-connect-query.md)
### Útmutató
#### Kiszolgálók létrehozása
##### [PowerShell](sql-database-get-started-powershell.md)
##### [C#](sql-database-get-started-csharp.md)
##### Rugalmas készletek létrehozása
###### [PowerShell](sql-database-elastic-pool-create-powershell.md)
###### [C#](sql-database-elastic-pool-create-csharp.md)
#### Adatbázisok létrehozására
##### [PowerShell](sql-database-get-started-powershell.md)
##### [C#](sql-database-get-started-csharp.md)
##### Tűzfalszabályok létrehozása
###### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
###### [REST API](sql-database-configure-firewall-settings-rest.md)
#### Kiszolgálók, készletek, adatbázisok és tűzfalak kezelése
##### [PowerShell](sql-database-manage-powershell.md)
##### Rugalmas készletek kezelése
###### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
###### [C#](sql-database-elastic-pool-manage-csharp.md)
##### [Szolgáltatásszintek és teljesítményszintek váltása](sql-database-scale-up-powershell.md)
#### Adatok áthelyezése
##### [Adatbázis exportálása BACPAC-fájlba](sql-database-export-powershell.md)
##### [Adatbázis importálása BACPAC-fájlból](sql-database-import-powershell.md)
##### [Adatbázis másolása egy másik Azure-beli helyre](sql-database-copy-powershell.md)
#### [Alkalmazás hitelesítéséhez szükséges értékek beolvasása](sql-database-client-id-keys.md)
#### [Rugalmas feladatok](sql-database-elastic-jobs-powershell.md)
#### Adatbázis visszaállítása és helyreállítása
##### Törölt adatbázis visszaállítása
###### [PowerShell](sql-database-restore-deleted-database-powershell.md)
##### Időponthoz kötött adatbázis-visszaállítás
###### [PowerShell](sql-database-point-in-time-restore-powershell.md)
##### Georedundáns helyreállítás
###### [PowerShell](sql-database-geo-restore-powershell.md)
#### Adatok replikálása aktív georeplikációval
##### Konfigurálás
###### [PowerShell](sql-database-geo-replication-powershell.md)
##### Feladatátvétel
###### [PowerShell](sql-database-geo-replication-failover-powershell.md)
#### [Az 1433-as porton túli portok használata az ADO.NET 4.5 szoftverrel](sql-database-develop-direct-route-ports-adonet-v12.md)
#### [Hibaüzenetek használata](sql-database-develop-error-messages.md)
#### [Kötegelés használata](sql-database-use-batching-to-improve-performance.md)
### Referencia
#### [Transact-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
#### [.NET-keretrendszer adatszolgáltatója az SQL Server rendszerhez (fogalmak)](https://msdn.microsoft.com/library/kb9s9ks0.aspx)
#### [.NET-keretrendszer adatszolgáltatója az SQL Server rendszerhez (API-referencia)](https://msdn.microsoft.com/library/system.data.sqlclient.aspx)
#### SQL PowerShell
##### [Azure SQL Database-parancsmagok (erőforrás-kezelés)](https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
##### [Azure SQL Database-parancsmagok (szolgáltatáskezelés)](https://msdn.microsoft.com/library/azure/dn546723(v=azure.300\).aspx)
##### [SQL Server-parancsmagok](https://msdn.microsoft.com/library/mt740629.aspx)
#### SQL Database REST API
##### [REST API (erőforrás-kezelés)](https://msdn.microsoft.com/library/azure/mt420159)
##### [REST API (szolgáltatáskezelés)](https://msdn.microsoft.com/library/azure/dn505719.aspx)
#### SQL-adatbázisok felügyelete – függvénytár
##### [SQL-adatbázisok felügyelete – függvénytár-referencia](https://msdn.microsoft.com/library/azure/mt349017.aspx)
##### [Az SQL-adatbázisok felügyelete függvénytárcsomag beszerzése](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
#### Entity Framework
##### [Az Entity Framework csomag beszerzése](https://www.nuget.org/packages/EntityFramework/)
#### [SQL Server-illesztőprogramok](https://msdn.microsoft.com/library/mt654049.aspx)
##### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
##### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
##### [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
##### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)
##### [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
##### [Python](https://msdn.microsoft.com/library/mt652092.aspx)
##### [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
#### [Azure SDK (letöltés)](https://www.visualstudio.com/vs/azure-tools/)
#### [Azure SDK (dokumentáció)](https://azure.microsoft.com/documentation/articles/dotnet-sdk/)
### Erőforrások
#### [SQL Server-eszközök](https://msdn.microsoft.com/library/mt238365.aspx)
#### [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
#### [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
#### [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
#### [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
#### [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)
## Törlés
### Adatbázis
#### [PowerShell](sql-database-manage-powershell.md#delete-a-sql-database)
### Kiszolgáló
#### [PowerShell](sql-database-manage-powershell.md#delete-a-sql-database-server)
## Fenyegetésészlelés
### [Fenyegetések észlelése](sql-database-threat-detection-get-started.md)
### [Tűzfal](sql-database-firewall-configure.md)
## Adatok titkosítása
### Always Encrypted
#### [Always Encrypted – áttekintés](sql-database-always-encrypted.md)
#### [Always Encrypted Azure Key Vault](sql-database-always-encrypted-azure-key-vault.md)
### [Transzparens adattitkosítás](https://msdn.microsoft.com/library/azure/dn948096)
### [Oszloptitkosítás](https://msdn.microsoft.com/library/azure/ms179331)
## Kezelés
###  Authentication
#### SQL-hitelesítés
#### [Hitelesítés Azure Active Directory-fiókkal](sql-database-aad-authentication.md)
#### [Többtényezős hitelesítés](sql-database-ssms-mfa-authentication.md)
### Kiszolgálók
### Rugalmas készletek
#### [Azure Portal](sql-database-elastic-pool-manage-portal.md)
#### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [T-SQL](sql-database-elastic-pool-manage-tsql.md)
#### [C#](sql-database-elastic-pool-manage-csharp.md)
### Adatbázisok
#### Önálló adatbázisok
##### [Azure Portal](sql-database-manage-portal.md)
##### [T-SQL](sql-database-manage-azure-ssms.md#create-and-manage-azure-sql-databases)
##### [PowerShell](sql-database-manage-powershell.md#create-a-sql-database-blank)
#### Szolgáltatásszintek és teljesítményszintek váltása
#### [Azure [Portal](sql-database-scale-up.md)
#### [PowerShell](sql-database-manage-powershell.md#change-the-performance-level-of-a-sql-database)
#### Szilánkokra osztott adatbázisok
##### [Meglévő, horizontálisan felskálázott adatbázisok migrálása horizontális felskálázáshoz](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [Hitelesítő adatok kezelése](sql-database-elastic-scale-manage-credentials.md)
##### [Adatok mozgatása kiterjesztett felhőalapú adatbázisok között](sql-database-elastic-scale-overview-split-and-merge.md)
##### [Felosztási-egyesítési szolgáltatás üzembe helyezése](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
##### [Szilánk hozzáadása](sql-database-elastic-scale-add-a-shard.md)
##### [Horizontális skálázási térképek javítása a RecoveryManager osztállyal](sql-database-elastic-database-recovery-manager.md)
### Tűzfalszabályok
#### Kiszolgáló
##### [Azure Portal](sql-database-configure-firewall-settings.md)
##### [PowerShell](sql-database-configure-firewall-settings-powershell.md)
##### [REST API](sql-database-configure-firewall-settings-rest.md)
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#server-level-firewall-rules)
#### Adatbázis
##### [T-SQL](sql-database-configure-firewall-settings-tsql.md#database-level-firewall-rules)
### Feladatok
#### [Szolgáltatástelepítés](sql-database-elastic-jobs-service-installation.md)
#### [Azure Portal](sql-database-elastic-jobs-create-and-manage.md)
#### [PowerShell](sql-database-elastic-jobs-powershell.md)
#### [A klienskódtár frissítése](sql-database-elastic-scale-upgrade-client-library.md)
### Bejelentkezések
#### [Bejelentkezések kezelése](sql-database-manage-logins.md)
## Adatok maszkolása
### Dinamikus adatmaszkolás
#### [Azure Portal](sql-database-dynamic-data-masking-get-started-portal.md)
#### [Régebbi típusú ügyfelek](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
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
### Adatbázis exportálása BACPAC-fájlba
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [SQL Package segédprogram](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)
#### [PowerShell](sql-database-export-powershell.md)
### Adatbázis importálása BACPAC-fájlból
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
#### [SQL Package segédprogram](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
#### [Azure Portal](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)

## Figyelés és hangolás
### [Önálló adatbázisok](sql-database-performance-guidance.md)
### Rugalmas készletek
#### [Azure Portal](sql-database-elastic-pool-manage-portal.md)
#### [PowerShell](sql-database-elastic-pool-manage-powershell.md)
#### [T-SQL](sql-database-elastic-pool-manage-tsql.md)
#### [C#](sql-database-elastic-pool-manage-csharp.md)
### [Lekérdezési terheléselemző](sql-database-query-performance.md)
### SQL Database Advisor
#### [Azure Portal](sql-database-advisor-portal.md)
### Szolgáltatásszintek és teljesítményszintek váltása
#### [Azure Portal](sql-database-scale-up.md)
#### [PowerShell](sql-database-scale-up-powershell.md)
### [Teljesítmény-finomhangolási tippek](sql-database-troubleshoot-performance.md)
### Memóriabeli OLTP
#### [Memóriabeli OLTP beállítása](sql-database-in-memory-oltp-migration.md)
#### [Memóriabeli OLTP-tár figyelése](sql-database-in-memory-oltp-monitoring.md)
### Lekérdezéstár
#### [Teljesítmény figyelése a Lekérdezéstár használatával](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Lekérdezéstár – használati forgatókönyvek](https://msdn.microsoft.com/library/mt614796.aspx)
#### [A Lekérdezéstár működtetése](sql-database-operate-query-store.md)
### [Kompatibilitási szintek](sql-database-compatibility-level-query-performance-130.md)
### [Eseménynaplózás](sql-database-auditing-get-started.md)
### [Teljesítményszámlálók a szilánkleképezés-kezelőhöz](sql-database-elastic-database-perf-counters.md)
### Bővített események
#### [Bővített események](sql-database-xevent-db-diff-from-svr.md)
#### [Eseményfájl célkódja](sql-database-xevent-code-event-file.md)
#### [Gyűrűpuffer célkódja](sql-database-xevent-code-ring-buffer.md)
### DMV-k
#### [DMV-k](sql-database-monitoring-with-dmvs.md)
#### [DMV-k] (sql-database-manage-azure-ssms#monitor-sql-database-using-dynamic-management-views


## Adatok áthelyezése
### SQL-adatbázis másolása
#### [Áttekintés](sql-database-copy.md)
#### [Azure Portal](sql-database-copy-portal.md)
#### [PowerShell](sql-database-copy-powershell.md)
#### [T-SQL](sql-database-copy-transact-sql.md)
### Adatbázis exportálása BACPAC-fájlba
#### [Azure Portal](sql-database-export.md)
#### [SQL Server Management Studio](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
#### [PowerShell](sql-database-export-powershell.md)
### Adatbázis importálása BACPAC-fájlból
#### [Azure Portal](sql-database-import.md)
#### [PowerShell](sql-database-import-powershell.md)
### [Adatszinkronizálás](sql-database-get-started-sql-data-sync.md)
### [Betöltés CSV-fájlból BCP használatával](sql-database-load-from-csv-with-bcp.md)

## Lekérdezés
### [SQL Server Management Studio](sql-database-connect-query-ssms.md)
### [Többszilánkos lekérdezés](sql-database-elastic-scale-multishard-querying.md)
### Adatbázisközi lekérdezések
#### [Adatbázisközi lekérdezés különböző sémákkal](sql-database-elastic-query-vertical-partitioning.md)
#### [Adatbázisközi jelentéskészítés](sql-database-elastic-query-horizontal-partitioning.md)
#### [Elosztott tranzakciók több felhőalapú adatbázisban](sql-database-elastic-transactions-overview.md)
#### [A klienskódtár frissítése](sql-database-elastic-scale-upgrade-client-library.md)
#### [Többszilánkos lekérdezés](sql-database-elastic-scale-multishard-querying.md)

## Visszaállítás
### Törölt adatbázis visszaállítása
### [Azure Portal](sql-database-restore-deleted-database-portal.md)
### [PowerShell](sql-database-restore-deleted-database-powershell.md)
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
### Kapcsolatok
#### [Kapcsolódási problémák](sql-database-troubleshoot-common-connection-issues.md)
#### [Átmeneti kapcsolódási hiba](sql-database-troubleshoot-connection.md)
#### [Diagnosztizálás és megelőzés](sql-database-connectivity-issues.md)
### [Engedélyek](sql-database-troubleshoot-permissions.md)

# Referencia
## [T-SQL](https://msdn.microsoft.com/library/azure/bb510741.aspx)
## SQL PowerShell
### [Azure SQL Database-parancsmagok (erőforrás-kezelés)](https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
### [SQL Server-parancsmagok](https://msdn.microsoft.com/library/mt740629.aspx)
## SQL Database REST API
### [REST API (erőforrás-kezelés)](https://msdn.microsoft.com/library/azure/mt420159)
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
## [SQL Server-eszközök](https://msdn.microsoft.com/library/mt238365.aspx)
## [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)
## [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
## [BCP](https://msdn.microsoft.com/library/ms162802.aspx)
## [SQLCMD](https://msdn.microsoft.com/library/ms162773.aspx)
## [SqlPackage](https://msdn.microsoft.com/hh550080.aspx)

<!--HONumber=Nov16_HO2-->


