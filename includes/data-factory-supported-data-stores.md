A Data Factory másolási tevékenysége adatokat másol egy forrásadattárból egy fogadó adattárba. A Data Factory a következő adattárakat támogatja. Az adatok bármilyen forrásból bármilyen fogadóba másolhatók. Az adattárra kattintva megtudhatja, hogy az adott tárolóba, illetve tárolóból hogyan másolhat adatokat.

| Kategória | Adattár | Forrásként támogatott | Fogadóként támogatott |
|:--- |:--- |:--- |:--- |
| Azure |[Azure Blob Storage](../articles/data-factory/data-factory-azure-blob-connector.md) <br/> [Azure Data Lake Store](../articles/data-factory/data-factory-azure-datalake-connector.md) <br/> [Azure SQL Database](../articles/data-factory/data-factory-azure-sql-connector.md) <br/> [Azure SQL Data Warehouse](../articles/data-factory/data-factory-azure-sql-data-warehouse-connector.md) <br/> [Azure Table Storage](../articles/data-factory/data-factory-azure-table-connector.md) <br/> [Azure DocumentDB](../articles/data-factory/data-factory-azure-documentdb-connector.md) <br/> |✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ |✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ |
| Adatbázisok |[SQL Server](../articles/data-factory/data-factory-sqlserver-connector.md)\* <br/> [Oracle](../articles/data-factory/data-factory-onprem-oracle-connector.md)\* <br/> [MySQL](../articles/data-factory/data-factory-onprem-mysql-connector.md)\* <br/> [DB2](../articles/data-factory/data-factory-onprem-db2-connector.md)\* <br/> [Teradata](../articles/data-factory/data-factory-onprem-teradata-connector.md)\* <br/> [PostgreSQL](../articles/data-factory/data-factory-onprem-postgresql-connector.md)\* <br/> [Sybase](../articles/data-factory/data-factory-onprem-sybase-connector.md)\* <br/>[Cassandra](../articles/data-factory/data-factory-onprem-cassandra-connector.md)\* <br/>[MongoDB](../articles/data-factory/data-factory-on-premises-mongodb-connector.md)\*<br/>[Amazon Redshift](../articles/data-factory/data-factory-amazon-redshift-connector.md) |✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓<br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ |✓ <br/> ✓ <br/> &nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp;<br/> &nbsp; <br/>&nbsp; |
| Fájl |[Fájlrendszer](../articles/data-factory/data-factory-onprem-file-system-connector.md)\* <br/> [HDFS](../articles/data-factory/data-factory-hdfs-connector.md)\* <br/> [Amazon S3](../articles/data-factory/data-factory-amazon-simple-storage-service-connector.md) |✓ <br/> ✓ <br/> ✓ |✓ <br/> &nbsp;<br/>&nbsp; |
| Egyéb |[Salesforce](../articles/data-factory/data-factory-salesforce-connector.md)<br/> [Általános ODBC](../articles/data-factory/data-factory-odbc-connector.md)\* <br/> [Általános OData](../articles/data-factory/data-factory-odata-connector.md) <br/> [Webtábla (tábla HTML-ből)](../articles/data-factory/data-factory-web-table-connector.md) <br/> [GE Historian](../articles/data-factory/data-factory-odbc-connector.md#ge-historian-store)* |✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ |&nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp; |

> [!NOTE]
> A * jellel ellátott adattárak lehetnek helyszíniek vagy az Azure IaaS részei, és használatukhoz telepíteni kell az [Adatkezelési átjárót](../articles/data-factory/data-factory-data-management-gateway.md) a helyszíni/Azure IaaS gépre.
> 
> 

<!--HONumber=Sep16_HO4-->


