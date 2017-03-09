# Áttekintés
## [Mi az SQL Database?](sql-database-technical-overview.md)
### [Szolgáltatásszintek](sql-database-service-tiers.md)
### [DTU-k és eDTU-k](sql-database-what-is-a-dtu.md)
### [DTU-teljesítményteszt – áttekintés](sql-database-benchmark-overview.md)
### [Erőforráskorlátok](sql-database-resource-limits.md)
### [Szolgáltatások](sql-database-features.md)
### [SQL Database GYIK](sql-database-faq.md)
## Összehasonlítások
### [Az SQL Database és a virtuális gépen futó SQL összehasonlítása](sql-database-paas-vs-sql-server-iaas.md)
### [A T-SQL eltérései](sql-database-transact-sql-information.md)
### [Az SQL és a NoSQL összehasonlítása](../documentdb/documentdb-nosql-vs-sql.md)
## [Az SQL Database eszközei](sql-database-manage-overview.md)
## [Az SQL Database oktatóanyagai](sql-database-explore-tutorials.md)
## Biztonság
### [Biztonsági áttekintés](sql-database-security-overview.md)
### [Azure Security Center az Azure SQL Database-hez](https://azure.microsoft.com/documentation/articles/security-center-sql-database/)
### [SQL Security Center](https://msdn.microsoft.com/library/azure/bb510589)
# Első lépések
## Adatbázisok és kiszolgálók
### Tanulás
#### [Kiszolgálók](sql-database-server-overview.md)
#### [Önálló adatbázisok](sql-database-overview.md)
#### [Több különálló](sql-database-elastic-scale-introduction.md)
##### Bérlők társítása
###### [Rugalmas ügyfélkönyvtár](sql-database-elastic-database-client-library.md)
###### [Szilánkleképezés-kezelő](sql-database-elastic-scale-shard-map-management.md)
###### [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md)
###### [Hitelesítő adatok kezelése](sql-database-elastic-scale-manage-credentials.md)
###### [Többszilánkos lekérdezés](sql-database-elastic-scale-multishard-querying.md)
##### Rugalmas készletek (erőforráskészletek)
###### [Mi az a rugalmas készlet?](sql-database-elastic-pool.md)
###### [Mikor érdemes rugalmas készletet használni?](sql-database-elastic-pool-guidance.md)
###### [A rugalmas eszköz díjszabása](sql-database-elastic-pool-price.md)
##### Szilánkokra osztott adatbázisok
###### [Rugalmas eszközökkel kapcsolatos kifejezések](sql-database-elastic-scale-glossary.md)
###### [Adatok mozgatása szilánkok között](sql-database-elastic-scale-overview-split-and-merge.md)
###### [Rugalmas eszközök – gyakori kérdések](sql-database-elastic-scale-faq.md)
##### Rugalmas lekérdezés (adatbázisközi lekérdezések)
###### [Mi az a rugalmas lekérdezés?](sql-database-elastic-query-overview.md)
##### Rugalmas tranzakciók (elosztott tranzakciók)
###### [Tranzakciók több felhőalapú adatbázisban](sql-database-elastic-transactions-overview.md)
##### Rugalmas feladatok (adatbázisközi feladatok)
###### [Mi az a rugalmas feladat?](sql-database-elastic-jobs-overview.md)
#### [Az Azure RemoteApp használata SQL Database-hez való kapcsolódásra](sql-database-ssms-remoteapp.md)
#### [SQL Database adatbázisok kezelése az Azure Automation szolgáltatással](sql-database-manage-automation.md)
### Ajánlott
#### [Önálló adatbázis létrehozása az Azure Portallal](sql-database-get-started.md)
#### [Önálló adatbázis létrehozása PowerShell használatával](sql-database-get-started-powershell.md)
#### [Önálló adatbázis létrehozása C# használatával](sql-database-get-started-csharp.md)
#### [Szilánkos alkalmazás létrehozása](sql-database-elastic-scale-get-started.md)
#### [Adatok mozgatása a szilánkok között](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
#### [Ismerkedés a rugalmas feladatokkal](sql-database-elastic-jobs-getting-started.md)
#### [Ismerkedés a rugalmas lekérdezésekkel](sql-database-elastic-query-getting-started-vertical.md)
#### [Jelentések létrehozása rugalmas lekérdezés használatával](sql-database-elastic-query-getting-started.md)
#### [Adatbázisok lekérdezése különböző sémákkal](sql-database-elastic-query-vertical-partitioning.md)
#### [Horizontálisan felskálázott adatbázisok közötti jelentés](sql-database-elastic-query-horizontal-partitioning.md)
## Adatok migrálása és áthelyezése
### Tanulás
#### [Adatbázisok migrálása](sql-database-cloud-migrate.md)
#### [Adatbázisok exportálása](sql-database-export.md)
#### [Adatszinkronizálás](sql-database-get-started-sql-data-sync.md)
#### [SQL-adatbázis másolása](sql-database-copy.md)
## Tűzfalszabályok, hitelesítés és engedélyezés
### Tanulás
#### [Hozzáférés-vezérlés](sql-database-control-access.md)
#### [Tűzfalszabályok](sql-database-firewall-configure.md)
#### [Bejelentkezések kezelése](sql-database-manage-logins.md)
#### [Azure AD-hitelesítés](sql-database-aad-authentication.md)
#### [Többtényezős hitelesítés](sql-database-ssms-mfa-authentication.md)
### Ajánlott
#### [SQL-hitelesítés és engedélyezés](sql-database-control-access-sql-authentication-get-started.md)
#### [Azure AD-hitelesítés és -engedélyezés](sql-database-control-access-aad-authentication-get-started.md)
## Az adatok védelme
### Tanulás
#### Naplózás
##### [Naplózás](sql-database-auditing.md)
##### [Alacsonyabb szintű ügyfelek támogatása és a naplózás IP-végpontjainak módosítása](sql-database-auditing-and-dynamic-data-masking-downlevel-clients.md)
#### [Fenyegetések észlelése](sql-database-threat-detection.md)
#### Adatok titkosítása
##### [Transzparens adattitkosítás](https://msdn.microsoft.com/library/azure/dn948096)
##### [Oszloptitkosítás](https://msdn.microsoft.com/library/azure/ms179331)
#### Adatok maszkolása
##### [Dinamikus adatmaszkolás](sql-database-dynamic-data-masking-get-started.md)
### Ajánlott
#### [Dinamikus adatmaszkolás az Azure Portal használatával](sql-database-dynamic-data-masking-get-started-portal.md)
#### [Naplózás konfigurálása az Azure Portal használatával](sql-database-auditing-portal.md)
#### [Naplózás konfigurálása a PowerShell használatával](sql-database-auditing-powershell.md)
#### [Naplózás konfigurálása a REST API használatával](sql-database-auditing-rest.md)
#### [Always Encrypted Windows-tanúsítvány használatával](sql-database-always-encrypted.md)
#### [Always Encrypted az Azure Key Vault használatával](sql-database-always-encrypted-azure-key-vault.md)
## Az üzletmenet folytonossága
### Tanulás
#### [Áttekintés](sql-database-business-continuity.md)
#### [Adatbázisok biztonsági mentése](sql-database-automated-backups.md)
#### [Hosszú távú megőrzés](sql-database-long-term-retention.md)
#### [Adatbázis-helyreállítás biztonsági másolatokkal](sql-database-recovery-using-backups.md)
#### [Egyetlen tábla helyreállítása](sql-database-cloud-migrate-restore-single-table-azure-backup.md)
#### [Helyreállítás adatközpont-kimaradás után](sql-database-disaster-recovery.md)
#### [A vészhelyreállítás hitelesítési követelményei](sql-database-geo-replication-security-config.md)
#### [Folyamatos üzletmenet – tervezési forgatókönyvek](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
#### [Vészhelyreállítási stratégiák rugalmas készletekkel](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)
#### [A működés közbeni frissítés](sql-database-manage-application-rolling-upgrade.md)
#### [Vészhelyreállítási próba végrehajtása](sql-database-disaster-recovery-drills.md)
#### [Aktív georeplikáció áttekintése](sql-database-geo-replication-overview.md)
### Ajánlott
#### [Azure Portal: Biztonsági mentés és visszaállítás](sql-database-get-started-backup-recovery-portal.md)
#### [PowerShell: Biztonsági mentés és visszaállítás](sql-database-get-started-backup-recovery-powershell.md)
## Alkalmazás-fejlesztés
### Tanulás
#### [Adatbázisok alkalmazás-fejlesztésének áttekintése](sql-database-develop-overview.md)
#### [Csatlakozási kódtárak](sql-database-libraries.md)
#### [Több-bérlős SaaS-alkalmazások](sql-database-design-patterns-multi-tenancy-saas-applications.md)
#### [Több-bérlős SaaS-alkalmazások méretezése sorszintű biztonság alkalmazásával](sql-database-elastic-tools-multi-tenant-row-level-security.md)
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
#### [Az Entity Framework használata](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md)
#### [Ügyfélkönyvtár használata Dapperrel](sql-database-elastic-scale-working-with-dapper.md)
### Megvalósítás az ügyfeleknél
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
#### [Memóriabeli optimalizálás](sql-database-in-memory.md)
### Ajánlott
#### [SQL Server-fejlesztés](https://msdn.microsoft.com/library/ms179422.aspx)
#### [Memóriabeli OLTP beállítása](sql-database-in-memory-oltp-migration.md)
## Figyelés és hangolás
### Tanulás
#### [Önálló adatbázisok](sql-database-single-database-monitor.md)
#### [Az SQL Database Advisor áttekintése](sql-database-advisor.md)
#### [Önálló adatbázisok – útmutató](sql-database-performance-guidance.md)
#### [Teljesítményelemzés: Azure Portal](sql-database-performance.md)
#### [Kötegelés használata](sql-database-use-batching-to-improve-performance.md)
#### [Bővített események](sql-database-xevent-db-diff-from-svr.md)
## SQL Database V11
### [Webes és Üzleti kiadás – kivonás](sql-database-web-business-sunset-faq.md)
### [Service tier advisor](sql-database-service-tier-advisor.md)
### [Rugalmas készlet felmérőeszköze](sql-database-elastic-pool-database-assessment-powershell.md)
### [Váltás a V12-re](sql-database-v12-plan-prepare-upgrade.md)
#### [Magasabb szintű verzióra váltás az Azure Portal használatával](sql-database-upgrade-server-portal.md)
#### [Magasabb szintű verzióra váltás a PowerShell használatával](sql-database-upgrade-server-powershell.md)
# Útmutató
## Létrehozás és kezelés
### [Kezelés az Azure Portal használatával](sql-database-manage-portal.md)
### [Kezelés a PowerShell használatával](sql-database-manage-powershell.md)
### [Kezelés az SSMS használatával](sql-database-manage-azure-ssms.md)
### Kiszolgálók
#### [Kezelés az Azure Portal használatával](sql-database-manage-servers-portal.md)
#### [Kezelés a PowerShell használatával](sql-database-manage-servers-powershell.md)
### Önálló adatbázisok
#### [Kezelés az Azure Portal használatával](sql-database-manage-single-databases-portal.md)
#### [Kezelés a PowerShell használatával](sql-database-manage-single-databases-powershell.md)
#### [Kezelés a Transact-SQL használatával](sql-database-manage-single-databases-tsql.md)
### Tűzfalszabályok
#### [Létrehozás az Azure Portal használatával](sql-database-configure-firewall-settings.md)
#### [Létrehozás a PowerShell használatával](sql-database-configure-firewall-settings-powershell.md)
#### [Létrehozás a REST API használatával](sql-database-configure-firewall-settings-rest.md)
#### [Létrehozás a T-SQL használatával](sql-database-configure-firewall-settings-tsql.md)
### Több adatbázis
#### [Az ügyfélkönyvtár frissítése ügyfélalkalmazásokban](sql-database-elastic-scale-upgrade-client-library.md)
#### Szilánkokra osztott adatbázisok
##### [Biztonsági konfiguráció](sql-database-elastic-scale-split-merge-security-configuration.md)
##### [Szilánk hozzáadása](sql-database-elastic-scale-add-a-shard.md)
##### [Szilánkleképezési problémák javítása](sql-database-elastic-database-recovery-manager.md)
##### [Migrálás szilánkokra osztott adatbázisokra](sql-database-elastic-convert-to-use-elastic-tools.md)
##### [Teljesítményszámlálók létrehozása a szilánkleképezés-kezelőhöz](sql-database-elastic-database-perf-counters.md)
#### Rugalmas feladatok
##### [Telepítési lépések](sql-database-elastic-jobs-service-installation.md)
##### [Kezelés a PowerShell használatával](sql-database-elastic-jobs-powershell.md) 
##### [Kezelés az Azure Portal használatával](sql-database-elastic-jobs-create-and-manage.md)
##### [Eltávolítási lépések](sql-database-elastic-jobs-uninstall.md)
#### Rugalmas készletek
##### [Kezelés az Azure Portal használatával](sql-database-elastic-pool-manage-portal.md)
##### [Kezelés a PowerShell használatával](sql-database-elastic-pool-manage-powershell.md)
##### [Kezelés a C# használatával](sql-database-elastic-pool-manage-csharp.md)
##### [Kezelés a T-SQL használatával](sql-database-elastic-pool-manage-tsql.md)
## Hitelesítés és engedélyezés
### [Azure AD-hitelesítés konfigurálása](sql-database-aad-authentication-configure.md)
### [Többtényezős hitelesítés konfigurálása](sql-database-ssms-mfa-authentication-configure.md)
## Fenyegetésészlelés konfigurálása
### [Fenyegetésészlelés konfigurálása az Azure Portal használatával](sql-database-threat-detection-portal.md)
## Adatok titkosítása
### [Transzparens adattitkosítás](https://msdn.microsoft.com/library/azure/dn948096)
### [Oszloptitkosítás](https://msdn.microsoft.com/library/azure/ms179331)
## Figyelés és hangolás
### [Lekérdezési terheléselemző](sql-database-query-performance.md)
### [SQL Database Advisor](sql-database-advisor-portal.md)
### [DMV-k](sql-database-monitoring-with-dmvs.md)
### [Kompatibilitási szintek](sql-database-compatibility-level-query-performance-130.md)
### [Teljesítmény-finomhangolási tippek](sql-database-troubleshoot-performance.md)
### [Riasztások létrehozása](sql-database-insights-alerts-portal.md)
### [Memóriabeli OLTP Storage figyelése](sql-database-in-memory-oltp-monitoring.md)
### Lekérdezéstár
#### [Teljesítmény figyelése a Lekérdezéstár használatával](https://msdn.microsoft.com/library/dn817826.aspx)
#### [Lekérdezéstár – használati forgatókönyvek](https://msdn.microsoft.com/library/mt614796.aspx)
#### [A Lekérdezéstár működtetése](sql-database-operate-query-store.md)
### Bővített események
#### [Eseményfájl célkódja](sql-database-xevent-code-event-file.md)
#### [Gyűrűpuffer célkódja](sql-database-xevent-code-ring-buffer.md)
## Adatok áthelyezése
### SQL-adatbázis másolása
#### [Másolás az Azure Portal használatával](sql-database-copy-portal.md)
#### [Másolás a PowerShell használatával](sql-database-copy-powershell.md)
#### [Másolás a T-SQL használatával](sql-database-copy-transact-sql.md)
### Adatbázis exportálása BACPAC-fájlba
#### [Exportálás az Azure Portal használatával](sql-database-export-portal.md)
#### [Exportálás az SQL Package segédprogrammal](sql-database-export-sqlpackage.md)
#### [Exportálás a PowerShell használatával](sql-database-export-powershell.md)
#### [Exportálás az SSMS használatával](sql-database-export-ssms.md)
## Adatok importálása
### [Importálás az Azure Portal használatával](sql-database-import-portal.md)
### [Importálás a PowerShell használatával](sql-database-import-powershell.md)
### [Importálás az SQLPackage használatával](sql-database-import-sqlpackage.md)
### [Betöltés CSV-fájlból BCP használatával](sql-database-load-from-csv-with-bcp.md)
## Lekérdezés
### [Lekérdezés az SSMS használatával](sql-database-connect-query-ssms.md)
## Biztonsági mentés és visszaállítás
### Biztonsági mentés hosszú távú megőrzése
#### [A biztonsági mentések hosszú távú megőrzésének kezelése az Azure Portal használatával](sql-database-manage-long-term-backup-retention-portal.md)
#### [A biztonsági mentések hosszú távú megőrzésének kezelése a PowerShell használatával](sql-database-manage-long-term-backup-retention-powershell.md)
### Törölt adatbázis visszaállítása
#### [Törölt elemek visszaállítása az Azure Portal használatával](sql-database-restore-deleted-database-portal.md)
#### [Törölt elemek visszaállítása a PowerShell használatával](sql-database-restore-deleted-database-powershell.md)
### Időponthoz kötött adatbázis-visszaállítás
#### [Időponthoz kötött visszaállítás az Azure Portal használatával](sql-database-point-in-time-restore-portal.md)
#### [Időponthoz kötött adatbázis-visszaállítás a PowerShell használatával](sql-database-point-in-time-restore-powershell.md)
### Visszaállítás georedundáns biztonsági másolatból
### [Georedundáns helyreállítás az Azure Portal használatával](sql-database-geo-restore-portal.md)
### [Georedundáns helyreállítás a PowerShell használatával](sql-database-geo-restore-powershell.md)
## Aktív georeplikáció
### [Konfigurálás az Azure Portal használatával](sql-database-geo-replication-portal.md)
### [Konfigurálás a PowerShell használatával](sql-database-geo-replication-powershell.md)
### [Konfigurálás a T-SQL használatával](sql-database-geo-replication-transact-sql.md)
### [Feladatátvétel az Azure Portal használatával](sql-database-geo-replication-failover-portal.md)
### [Feladatátvétel a PowerShell használatával](sql-database-geo-replication-failover-powershell.md)
### [Feladatátvétel a T-SQL használatával](sql-database-geo-replication-failover-transact-sql.md)
## Hibaelhárítás
### [Kapcsolódási problémák](sql-database-troubleshoot-common-connection-issues.md)
### [Átmeneti kapcsolódási hiba](sql-database-troubleshoot-connection.md)
### [Diagnosztizálás és megelőzés](sql-database-connectivity-issues.md)
### [Engedélyek](sql-database-troubleshoot-permissions.md)
### [Adatbázisok áthelyezése](sql-database-troubleshoot-moving-data.md)
# Referencia
## [PowerShell](/powershell/resourcemanager/azurerm.sql/v2.3.0/azurerm.sql)
## [PowerShell (Elastic DB)](/powershell/elasticdatabasejobs/v0.8.33/elasticdatabasejobs)
## [Azure CLI 2.0](/cli/azure/appservice)
## [.NET](/dotnet/api/microsoft.azure.management.sql.models)
## [Java](/java/api/com.microsoft.azure.management.sql)
## [Node.js](https://msdn.microsoft.com/library/mt652093.aspx)
## [Python](https://msdn.microsoft.com/library/mt652092.aspx)
## [Ruby](https://msdn.microsoft.com/library/mt691981.aspx)
## [PHP](https://msdn.microsoft.com/library/dn865013.aspx)
## [T-SQL](https://msdn.microsoft.com/library/bb510741.aspx)
## [REST](https://msdn.microsoft.com/library/azure/mt163571.aspx)

# Kapcsolódó
## SQL-adatbázisok felügyelete – függvénytár
### [Az SQL-adatbázisok felügyelete függvénytárcsomag beszerzése](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql)
## [SQL Server-illesztőprogramok](https://msdn.microsoft.com/library/mt654049.aspx)
### [ADO.NET](https://msdn.microsoft.com/library/mt657768.aspx)
### [JDBC](https://msdn.microsoft.com/library/mt484311.aspx)
### [ODBC](https://msdn.microsoft.com/library/mt654048.aspx)

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
