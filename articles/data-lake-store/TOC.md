# [A Data Lake Store dokumentációja](index.md)

# Áttekintés
## [Az Azure Data Lake Store áttekintése](data-lake-store-overview.md)
## [Az Azure Data Lake Store és az Azure Storage összehasonlítása](data-lake-store-comparison-with-blob-storage.md)
## [Azure Data Lake Store a big data-feldolgozáshoz](data-lake-store-data-scenarios.md)
## [Az Azure Data Lake Store-ral működő nyílt forráskódú alkalmazások](data-lake-store-compatible-oss-other-applications.md)
## [Data Lake Store használatának ajánlott eljárásai](data-lake-store-best-practices.md)

# Bevezetés
## [A Portal használata](data-lake-store-get-started-portal.md)
## [A PowerShell-lel](data-lake-store-get-started-powershell.md)
## [Az Azure CLI 2.0 használata](data-lake-store-get-started-cli-2.0.md)


# Útmutató
## Adatok betöltése és áthelyezése
### [Az Azure Data Factory használata](../data-factory/load-azure-data-lake-store.md)
### [A Storage Explorer használata](data-lake-store-in-storage-explorer.md)
### [Az AdlCopy használata](data-lake-store-copy-data-azure-storage-blob.md)
### [A DistCp használata](data-lake-store-copy-data-wasb-distcp.md)
### [A Sqoop használata](data-lake-store-data-transfer-sql-sqoop.md)
### [Adatok feltöltése offline forrásokból](data-lake-store-offline-bulk-data-upload.md)
### [Az Azure Data Lake Store régiók közötti migrálása](data-lake-store-migration-cross-region.md)

## Adatok védelme
### [Biztonsági áttekintés](data-lake-store-security-overview.md)
### [Hozzáférés-vezérlés a Data Lake Store-ban](data-lake-store-access-control.md)
### [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
### [Titkosítás](data-lake-store-encryption.md)

## Hitelesítés a Data Lake Store-ral
### [Hitelesítési lehetőségek](data-lakes-store-authentication-using-azure-active-directory.md)
### [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md)
#### [A Java használata](data-lake-store-end-user-authenticate-java-sdk.md)
#### [A .NET SDK használata](data-lake-store-end-user-authenticate-net-sdk.md)
#### [A REST API használata](data-lake-store-end-user-authenticate-rest-api.md)
#### [A Python használata](data-lake-store-end-user-authenticate-python.md)
### [Szolgáltatások közötti hitelesítés](data-lake-store-service-to-service-authenticate-using-active-directory.md)
#### [A Java használata](data-lake-store-service-to-service-authenticate-java.md)
#### [A .NET SDK használata](data-lake-store-service-to-service-authenticate-net-sdk.md)
#### [A REST API használata](data-lake-store-service-to-service-authenticate-rest-api.md)
#### [A Python használata](data-lake-store-service-to-service-authenticate-python.md)

## A Data Lake Store használata
### Fiókkezelési műveletek
#### [A .NET SDK használata](data-lake-store-get-started-net-sdk.md)
#### [A REST API használata](data-lake-store-get-started-rest-api.md)
#### [A Python használata](data-lake-store-get-started-python.md)
### Fájlrendszeri műveletek
#### [A .NET SDK használata](data-lake-store-data-operations-net-sdk.md)
#### [A Java SDK használata](data-lake-store-get-started-java-sdk.md)
#### [A REST API használata](data-lake-store-data-operations-rest-api.md)
#### [A Python használata](data-lake-store-data-operations-python.md)

## Teljesítmény
### [Teljesítmény-finomhangolási útmutató az Azure Data Lake Store-hoz](data-lake-store-performance-tuning-guidance.md)
### [Teljesítmény-finomhangolási útmutató az Azure Data Lake Store-ral használt PowerShellhez](data-lake-store-performance-tuning-powershell.md)
### [Teljesítmény-finomhangolási útmutató a Spark on HDInsighthoz és az Azure Data Lake Store-hoz](data-lake-store-performance-tuning-spark.md)
### [Teljesítmény-finomhangolási útmutató a Hive on HDInsighthoz és az Azure Data Lake Store-hoz](data-lake-store-performance-tuning-hive.md)
### [Teljesítmény-finomhangolási útmutató a MapReduce on HDInsighthoz és az Azure Data Lake Store-hoz](data-lake-store-performance-tuning-mapreduce.md)
### [Teljesítmény-finomhangolási útmutató a Storm on HDInsighthoz és az Azure Data Lake Store-hoz](data-lake-store-performance-tuning-storm.md)

## Integráció az Azure-szolgáltatásokkal
### HDInsighttal
#### [Az Azure Portal használata](data-lake-store-hdinsight-hadoop-use-portal.md)
#### [Az Azure PowerShell használata (alapértelmezett tároló)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
#### [Az Azure PowerShell használata (további tároló)](data-lake-store-hdinsight-hadoop-use-powershell.md)
#### [Az Azure-sablon használata](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
### [Hozzáférés virtuális gépekről az Azure VNET-ben](data-lake-store-connectivity-from-vnets.md)
### [Használat a Data Lake Analytics-szel](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
### [Használat az Azure Event Hubs-eseményközpontokkal](data-lake-store-archive-eventhub-capture.md)
### [Használat a Data Factory-val](../data-factory/load-azure-data-lake-store.md)
### [Használat a Stream Analytics-szel](data-lake-store-stream-analytics.md)
### [Használat a Power BI-jal](data-lake-store-power-bi.md)
### [Használat a Data Cataloggal](data-lake-store-with-data-catalog.md)
### [Használat a PolyBase-zel az SQL Data Warehouse-ban](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)
### [Használat az SQL Server integrációs szolgáltatásaival](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)
### [További Azure-integrálási lehetőségek](data-lake-store-integrate-with-other-services.md)

## Kezelés
### [Diagnosztikai naplók elérése](data-lake-store-diagnostic-logs.md)
### [Tervezés magas rendelkezésre álláshoz](data-lake-store-disaster-recovery-guidance.md)

# Referencia
## [Kódminták](https://azure.microsoft.com/resources/samples/?service=data-lake-store)
## [Azure PowerShell](/powershell/module/azurerm.datalakestore)
## [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)
## [Java](/java/api/com.microsoft.azure.datalake.store)
## [Node.js](https://www.npmjs.com/package/azure-arm-datalake-store)
## [Python (Fiókkezelés)](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)
## [Python (Fájlrendszer-kezelés)](http://azure-datalake-store.readthedocs.io/en/latest)
## [REST](/rest/api/datalakestore)
## [Azure CLI](https://docs.microsoft.com/cli/azure/dls)

# További források
## [Azure-ütemterv](https://azure.microsoft.com/roadmap/)
## [Data Lake Store blog](https://blogs.msdn.microsoft.com/azuredatalake/)
## [Visszajelzés küldése a UserVoice-ról](https://feedback.azure.com/forums/327234-data-lake)
## [MSDN-fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDataLake)
## [Díjszabás](https://azure.microsoft.com/pricing/details/data-lake-store/)
## [Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/)
## [Szolgáltatási hírek](https://azure.microsoft.com/updates/?product=data-lake-store)
## [Stack Overflow fórum](http://stackoverflow.com/questions/tagged/azure-data-lake)
## [Videók](https://azure.microsoft.com/documentation/videos/index/?services=data-lake-store)
