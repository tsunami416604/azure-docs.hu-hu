# Áttekintés

## [Az SQL Data Warehouse bemutatása](sql-data-warehouse-overview-what-is.md)

# Gyors útmutatók

## [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md)

# Oktatóanyagok
## [1 – Adatok betöltése a blobból](load-data-from-azure-blob-storage-using-polybase.md)

# Alapelvek
## Szolgáltatásjellemzők
### [MPP-architektúra](massively-parallel-processing-mpp-architecture.md)
### [Teljesítményszintek](performance-tiers.md)
### [Adattárházegységek](what-is-a-data-warehouse-unit-dwu-cdwu.md)
### [Adattárházak biztonsági másolatai](sql-data-warehouse-backups.md)
### [Naplózás](sql-data-warehouse-auditing-overview.md)
### [Kapacitási korlátok](sql-data-warehouse-service-capacity-limits.md)
### [Gyakori kérdések](sql-data-warehouse-overview-faq.md)

## Biztonság
### [Áttekintés](sql-data-warehouse-overview-manage-security.md)
### [Hitelesítés](sql-data-warehouse-authentication.md)


## Migrálás az SQL Data Warehouse-ba
### [Áttekintés](sql-data-warehouse-overview-migrate.md)
### [Áttelepítési eszköz](sql-data-warehouse-migrate-migration-utility.md)
### [Séma áttelepítése](sql-data-warehouse-migrate-schema.md)
### [Kód áttelepítése](sql-data-warehouse-migrate-code.md)
### [Adatok áttelepítése](sql-data-warehouse-migrate-data.md)

## Adatok betöltése és áthelyezése
### [Áttekintés](sql-data-warehouse-overview-load.md)
### [PolyBase](sql-data-warehouse-load-polybase-guide.md)


## Integrálás
### [Áttekintés](sql-data-warehouse-overview-integrate.md)


## Monitorozás és hangolás
### [Irányelvek](resource-classes-for-workload-management.md)
### [Oszlopcentrikus tömörítés](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)
### [Figyelés](sql-data-warehouse-manage-monitor.md)
### [Hibaelhárítás](sql-data-warehouse-troubleshoot.md)

## Adattárházak fejlesztése
### [Áttekintés](sql-data-warehouse-overview-develop.md)
### [Adattárház-összetevők](sql-data-warehouse-overview-workload.md)

### Táblák
#### [Áttekintés](sql-data-warehouse-tables-overview.md)
#### [CTAS](sql-data-warehouse-develop-ctas.md)
#### [Adattípusok](sql-data-warehouse-tables-data-types.md)
#### [Elosztott táblák](sql-data-warehouse-tables-distribute.md)
#### [Indexek](sql-data-warehouse-tables-index.md)
#### [Identitáskezelés](sql-data-warehouse-tables-identity.md)
#### [Partíciók](sql-data-warehouse-tables-partition.md)
#### [Replikált táblák](design-guidance-for-replicated-tables.md)
#### [Statisztika](sql-data-warehouse-tables-statistics.md)
#### [Ideiglenes](sql-data-warehouse-tables-temporary.md)

### Lekérdezések
#### [Dinamikus SQL](sql-data-warehouse-develop-dynamic-sql.md)
#### [Csoportosítás beállítások szerint](sql-data-warehouse-develop-group-by-options.md)
#### [Címkék](sql-data-warehouse-develop-label.md)

### A T-SQL nyelvi elemei
#### [Hurkok](sql-data-warehouse-develop-loops.md)
#### [Tárolt eljárások](sql-data-warehouse-develop-stored-procedures.md)
#### [Tranzakciók](sql-data-warehouse-develop-transactions.md)
#### [Tranzakciók – ajánlott eljárások](sql-data-warehouse-develop-best-practices-transactions.md)
#### [Felhasználó által definiált sémák](sql-data-warehouse-develop-user-defined-schemas.md)
#### [Változó-hozzárendelés](sql-data-warehouse-develop-variable-assignment.md)
#### [Nézetek](sql-data-warehouse-develop-views.md)

# Útmutatók
## Szolgáltatásjellemzők
### [Adattárház visszaállítása – portál](sql-data-warehouse-restore-database-portal.md)
### [Adattárház visszaállítása – PowerShell](sql-data-warehouse-restore-database-powershell.md)
### [Adattárház visszaállítása – REST API](sql-data-warehouse-restore-database-rest-api.md)

## Biztonság
### [Titkosítás engedélyezése – portál](sql-data-warehouse-encryption-tde.md)
### [Titkosítás engedélyezése – T-SQL](sql-data-warehouse-encryption-tde-tsql.md)
### [Fenyegetések észlelése](sql-data-warehouse-security-threat-detection.md)


## Adatok betöltése és áthelyezése
### [AdventureWorks](sql-data-warehouse-load-sample-databases.md)
### [Azure Data Lake Store](sql-data-warehouse-load-from-azure-data-lake-store.md)
### [BCP](sql-data-warehouse-load-with-bcp.md)
### [Data Factory](sql-data-warehouse-load-with-data-factory.md)
### [PolyBase Blob Storage-ból](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
### [PolyBase SQL Serverről](sql-data-warehouse-load-from-sql-server-with-polybase.md)
### [RedGate](sql-data-warehouse-load-with-redgate.md)
### [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)


## Integrálás
###  [Rugalmas lekérdezés konfigurálása az SQL Database-ből](tutorial-elastic-query-with-sql-datababase-and-sql-data-warehouse.md)
### [Azure Stream Analytics-feladat hozzáadása](sql-data-warehouse-integrate-azure-stream-analytics.md)
### [Rugalmas lekérdezés konfigurálása](how-to-use-elastic-query-with-sql-data-warehouse.md)
### [Gépi tanulás használata](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
### [Vizualizáció a Power BI használatával](sql-data-warehouse-get-started-visualize-with-power-bi.md)

## Monitorozás és hangolás
### [A számítási feladatok elemzése](analyze-your-workload.md)

## Horizontális felskálázás
### [Számítások kezelése – portál](sql-data-warehouse-manage-compute-portal.md)
### [Számítások kezelése – PowerShell](sql-data-warehouse-manage-compute-powershell.md)
### [Számítások kezelése – REST API](sql-data-warehouse-manage-compute-rest-api.md)
### [Számítási szintek automatizálása](manage-compute-with-azure-functions.md)


# Referencia


## T-SQL
### [Teljes referencia](https://docs.microsoft.com/sql/t-sql/language-reference/)
### [Az SQL DW nyelvi elemei](sql-data-warehouse-reference-tsql-language-elements.md)
### [Az SQL DW utasításai](sql-data-warehouse-reference-tsql-statements.md)
## [Rendszernézetek](sql-data-warehouse-reference-tsql-system-views.md)
## [PowerShell-parancsmagok](sql-data-warehouse-reference-powershell-cmdlets.md)

# Erőforrások
## [Azure-ütemterv](https://azure.microsoft.com/roadmap/?category=databases)
## [Fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureSQLDataWarehouse)
## [Díjszabás](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)
## [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
## [Funkciókérések](https://feedback.azure.com/forums/307516-sql-data-warehouse/)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=sql-data-warehouse)
## [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw/)
## [Támogatás](sql-data-warehouse-get-started-create-support-ticket.md)
## [Videók](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)

## Partnerek
### [Üzleti intelligencia](sql-data-warehouse-partner-business-intelligence.md)
### [Adatintegráció](sql-data-warehouse-partner-data-integration.md)
### [Adatkezelés](sql-data-warehouse-partner-data-management.md)
