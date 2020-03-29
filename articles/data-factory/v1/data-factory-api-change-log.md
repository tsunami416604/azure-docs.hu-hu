---
title: Adatgyár - .NET API változásnaplója
description: A cikk ismerteti a módosítások, a szolgáltatáskiegészítések, a hibajavítások és így tovább, az Azure Data Factory .
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
robots: noindex
ms.date: 01/22/2018
ms.openlocfilehash: dbbbdebdcf1db7afe485166f5744f2291b757d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74979002"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory – .NET API változásnaplója
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. 

Ez a cikk az Azure Data Factory SDK egy adott verzióban történt változásairól tartalmaz információkat. Az Azure Data Factory legújabb NuGet csomagját [itt](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) találja

## <a name="version-4110"></a>4.11.0-s verzió
Funkció kiegészítések:

* A következő csatolt szolgáltatástípusok lettek hozzáadva:
  * [OnPremisesMongoDbLinkedService szolgáltatás](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedSzolgáltatás](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService szolgáltatás](https://msdn.microsoft.com/library/mt765144.aspx)
* A következő adatkészlettípusok lettek hozzáadva:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Adatkészlet](https://msdn.microsoft.com/library/mt765112.aspx)
* A következő másolási forrástípusok lettek hozzáadva:
  * [MongoDbForrás](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>4.10.0-s verzió
* A következő választható tulajdonságok lettek hozzáadva a TextFormat formátumhoz:
  * [SkipLineCount között](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowasfejléc](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* A következő csatolt szolgáltatástípusok lettek hozzáadva:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* A következő adatkészlettípusok lettek hozzáadva:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* A következő másolási forrástípusok lettek hozzáadva:
  * [CassandraForrás](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) tulajdonság hozzáadása az AzureMLBatchExecutionActivity szolgáltatáshoz
  * Több webszolgáltatás-bemenet átadásának engedélyezése egy Azure Machine Learning-kísérletbe

## <a name="version-491"></a>4.9.1-es verzió
### <a name="bug-fix"></a>Hibajavítás
* WebApi-alapú hitelesítés elavulttal a [WebLinkedService szolgáltatáshoz.](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx)

## <a name="version-490"></a>4.9.0-s verzió
### <a name="feature-additions"></a>Szolgáltatás kiegészítések
* Adja hozzá [az EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) és [az StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) tulajdonságokat a CopyActivity tevékenységhez. A funkcióval kapcsolatos részletekért lásd a [Szakaszos másolat című](data-factory-copy-activity-performance.md#staged-copy) témakört.

### <a name="bug-fix"></a>Hibajavítás
* Vezessen be az [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) metódus túlterhelését, amely [Egy ActivityWindowsByActivityListParameters példányt](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) vesz igénybe.
* Jelölje be [a WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) és [a WriteBatchTimeout-ot](https://msdn.microsoft.com/library/dn884245.aspx) választhatóként a CopySink programban.

## <a name="version-480"></a>4.8.0-s verzió
### <a name="feature-additions"></a>Szolgáltatás kiegészítések
* A másolási teljesítmény beállításának engedélyezéséhez a következő választható tulajdonságok lettek hozzáadva a Másolás tevékenységtípusához:
  * [Párhuzamos másolatok](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>4.7.0-s verzió
### <a name="feature-additions"></a>Szolgáltatás kiegészítések
* Új StorageFormat típusú [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) típusú másolásához fájlok at optimalizált sor oszlopos (ORC) formátumban.
* Adja hozzá [az AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) és polyBaseSettings tulajdonságokat az SqlDWSink-hez.
  * Lehetővé teszi a PolyBase használatával az ADATOK MÁSOLÁSÁt az SQL Data Warehouse-ba.

## <a name="version-461"></a>4.6.1-es verzió
### <a name="bug-fixes"></a>Hibajavítások
* Javítja a tevékenységablakok listázására vonatkozó HTTP-kérelmet.
  * Eltávolítja az erőforráscsoport nevét és az adatgyár nevét a kérelem hasznos adatából.

## <a name="version-460"></a>4.6.0-s verzió
### <a name="feature-additions"></a>Szolgáltatás kiegészítések
* A következő tulajdonságok lettek hozzáadva a [PipelineProperties tulajdonsághoz:](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx)
  * [PipelineMode (PipelineMode)](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Adathalmazok](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* A következő tulajdonságok lettek hozzáadva a [PipelineRuntimeInfo-hoz:](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx)
  * [PipelineState között](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Új [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) típusú [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) típusú definiálása adatkészletek, amelyek adatai JSON formátumban.

## <a name="version-450"></a>4.5.0-s verzió
### <a name="feature-additions"></a>Szolgáltatás kiegészítések
* Hozzáadott [lista műveletek tevékenység ablakban](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Módszerek et adott hozzá a tevékenységablakok lekéréséhez az entitástípusok (azaz adatgyárak, adatkészletek, folyamatok és tevékenységek) alapján.
* A következő csatolt szolgáltatástípusok lettek hozzáadva:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* A következő adatkészlettípusok lettek hozzáadva:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* A következő másolási forrástípusok lettek hozzáadva:     
  * [Webforrás](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>4.4.0-s verzió
### <a name="feature-additions"></a>Funkció kiegészítések
* Adatforrásként és fogadóként a következő csatolt szolgáltatástípus került hozzáadásra a másolási tevékenységekhez:
  * [AzureStorageSasLinkedService .](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx) Az [Azure Storage SAS csatolt szolgáltatását](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) az általános információk és példák című témakörben talál.

## <a name="version-430"></a>4.3.0-s verzió
### <a name="feature-additions"></a>Funkció kiegészítések
* A másolási tevékenységek adatforrásaként a következő csatolt szolgáltatástípusok lettek hozzáadva:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Általános tudnivalókért és példákért [lásd: Adatok áthelyezése a HDFS-ből a Data Factory használatával](data-factory-hdfs-connector.md) című témakörben.
  * [OnPremisesOdbcLinkedService .](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx) Az [adatok áthelyezése az ODBC-adattárakból az Azure Data Factory használatával](data-factory-odbc-connector.md) című témakörben általános információkat és példákat talál.

## <a name="version-420"></a>4.2.0-s verzió
### <a name="feature-additions"></a>Funkció kiegészítések
* A következő új tevékenységtípus lett hozzáadva: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). A tevékenységről az [Azure ML-modellek frissítése az Erőforrás-tevékenység frissítése című](data-factory-azure-ml-batch-execution-activity.md)témakörben talál további információt.
* Egy új választható [tulajdonságupdateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) lett hozzáadva az [AzureMLLinkedService osztályhoz.](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx)
* [A LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) és a [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) tulajdonságok hozzá lettek adva a [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) osztályhoz.
* Engedélyezze az időhívások konfigurálását a Data Factory szolgáltatásba irányuló ügyfélhívásokhoz.

## <a name="version-410"></a>4.1.0-s verzió
### <a name="feature-additions"></a>Funkció kiegészítések
* A következő csatolt szolgáltatástípusok lettek hozzáadva:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* A következő tevékenységtípusok lettek hozzáadva:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* A következő adatkészlettípusok lettek hozzáadva:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* A másolási tevékenység hez a következő forrás- és fogadótípusok kerültek hozzáadásra:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>4.0.1-es verzió
### <a name="breaking-changes"></a>Kompatibilitástörő változások
A következő osztályok at nevezték át. Az új nevek voltak az osztályok eredeti nevei a 4.0.0 kiadás előtt.

| Név a 4.0.0-ban | Név a 4.0.1-ben |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| Relációs adatkészlet |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>4.0.0-s verzió
### <a name="breaking-changes"></a>Kompatibilitástörő változások
* A következő osztályok/összeköttetések átnevezve lettek.

| Régi név | Új név |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tábla |[Adatkészlet](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| Táblatulajdonságai |[Adatkészlettulajdonságai](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateorUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[AdatkészletGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* A **Lista** metódusok most lapozható eredményeket adnak vissza. Ha a válasz nem üres **NextLink** tulajdonságot tartalmaz, az ügyfélalkalmazásnak folytatnia kell a következő lap beolvasását, amíg az összes lapot vissza nem adja.  Például:

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **List** pipeline API csak egy folyamat összegzését adja vissza a teljes részletek helyett. A folyamat összegzésében lévő tevékenységek például csak nevet és típust tartalmaznak.

### <a name="feature-additions"></a>Funkció kiegészítések
* Az [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) osztály két új tulajdonságot támogat, a **SliceIdentifierColumnName** és **az SqlWriterCleanupScript**tulajdonságot az Azure SQL Data Warehouse idempotens példányának támogatásához. Tekintse meg az [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) cikket ezeka tulajdonságok részleteit.
* Most már támogatjuk a tárolt eljárás futtatását az Azure SQL Database és az Azure SQL Data Warehouse-források ellen a másolási tevékenység részeként. Az [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) és [az SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) osztályok a következő tulajdonságokkal rendelkeznek: **SqlReaderStoredProcedureName** és **StoredProcedureParameters**. Ezekről a tulajdonságokról az [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) és az Azure SQL Data [Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) cikkeiben Azure.com.  
