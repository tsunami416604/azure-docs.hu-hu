---
title: Data Factory – .NET API-változási napló
description: A cikk a Azure Data Factory a .NET API adott verziójában ismerteti az adatváltozások, a szolgáltatások kiegészítései és a hibajavítások leírását.
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
ms.openlocfilehash: b7936fcd1e4a629a813c4266920f6c34a15cf9b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89438942"
---
# <a name="azure-data-factory---net-api-change-log"></a>Azure Data Factory – .NET API-változási napló
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. 

Ez a cikk a Azure Data Factory SDK adott verzióban történt módosításaival kapcsolatos információkat tartalmazza. [Itt](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories) találja a Azure Data Factory legújabb NuGet-csomagját

## <a name="version-4110"></a>4.11.0 verziója
Szolgáltatások kiegészítései:

* A következő társított szolgáltatások típusai lettek hozzáadva:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* A következő adatkészlet-típusok lettek hozzáadva:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* A következő típusú másolási források lettek hozzáadva:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>4.10.0 verziója
* A következő opcionális tulajdonságok lettek hozzáadva a Szövegformátum-hez:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* A következő társított szolgáltatások típusai lettek hozzáadva:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* A következő adatkészlet-típusok lettek hozzáadva:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* A következő típusú másolási források lettek hozzáadva:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) tulajdonság hozzáadása a AzureMLBatchExecutionActivity
  * Több webszolgáltatás-bemenet átadásának engedélyezése egy Azure Machine Learning kísérletre

## <a name="version-491"></a>4.9.1 verziója
### <a name="bug-fix"></a>Hibajavítás
* A [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx)WebApi-alapú hitelesítésének elavultnak kell lennie.

## <a name="version-490"></a>4.9.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* Adja hozzá a [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) és a [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) tulajdonságot a CopyActivity. A funkció részleteit a [szakaszos másolás](data-factory-copy-activity-performance.md#staged-copy) című részben tekintheti meg.

### <a name="bug-fix"></a>Hibajavítás
* A [ActivityWindowOperationExtensions. list](https://msdn.microsoft.com/library/mt767915.aspx) metódus túlterhelésének bevezetése, amely egy [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) -példányt vesz igénybe.
* A [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) és a [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) megjelölése opcionálisként a CopySink-ben.

## <a name="version-480"></a>4.8.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* A következő opcionális tulajdonságok lettek hozzáadva a másolási tevékenység típusához a másolási teljesítmény finomhangolásának engedélyezéséhez:
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>4.7.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* Új StorageFormat típusú [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) -típus hozzáadása a fájlok optimalizált, oszlopos (ORK) formátumban való másolásához.
* Adja hozzá a [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) és a PolyBaseSettings tulajdonságot a SqlDWSink.
  * Lehetővé teszi az adatok másolását az Azure szinapszis Analyticsbe (korábban SQL Data Warehouse).

## <a name="version-461"></a>4.6.1-es verzió
### <a name="bug-fixes"></a>Hibajavítások
* Javítások HTTP-kérelem a listázási tevékenységhez Windows.
  * Eltávolítja az erőforráscsoport nevét és a adat-előállító nevét a kérelem hasznos adataiból.

## <a name="version-460"></a>4.6.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* A következő tulajdonságok lettek hozzáadva a [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx)-hez:
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Adatkészletek](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* A következő tulajdonságok lettek hozzáadva a [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx)-hez:
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Új [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) típusú [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) hozzáadásával meghatározhatja azokat az adatkészleteket, amelyekben az adat JSON formátumú.

## <a name="version-450"></a>4.5.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* [A List művelet hozzáadva a tevékenység ablakhoz](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * Metódusok hozzáadásával lekérheti a tevékenységek ablakait az entitások típusai (azaz az adat-előállítók, adatkészletek, folyamatok és tevékenységek) alapján.
* A következő társított szolgáltatások típusai lettek hozzáadva:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* A következő adatkészlet-típusok lettek hozzáadva:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* A következő típusú másolási források lettek hozzáadva:     
  * [Webforrások](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>4.4.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* A következő társított szolgáltatástípus adatforrásként lett hozzáadva a másolási tevékenységekhez:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Az [Azure Storage sas társított szolgáltatásával](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) kapcsolatos fogalmakat és példákat itt tekintheti meg.

## <a name="version-430"></a>4.3.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* A másolási tevékenységek adatforrásaként a következő társított szolgáltatások lettek hozzáadva:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Lásd: [adatok áthelyezése a HDFS a Data Factory használatával](data-factory-hdfs-connector.md) elméleti információk és példák.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Tekintse meg az [adatok áthelyezése az ODBC-adattárakból Azure Data Factory használatával](data-factory-odbc-connector.md) fogalmi információkat és példákat.

## <a name="version-420"></a>4.2.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* A következő új tevékenységtípus lett hozzáadva: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). A tevékenység részletes ismertetését lásd: [Az Azure ml-modellek frissítése az erőforrás frissítése tevékenység használatával](data-factory-azure-ml-batch-execution-activity.md).
* A [AzureMLLinkedService osztályhoz](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx)új, nem kötelező tulajdonság [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) lett hozzáadva.
* A [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) és a [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) tulajdonság hozzá lett adva a [datapipelinemanagementclient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) osztályhoz.
* Az Data Factory szolgáltatásra irányuló ügyfélkapcsolatok időtúllépési beállításainak engedélyezése.

## <a name="version-410"></a>4.1.0 verziója
### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* A következő társított szolgáltatások típusai lettek hozzáadva:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* A következő tevékenységtípusok lettek hozzáadva:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* A következő adatkészlet-típusok lettek hozzáadva:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* A másolási tevékenységhez a következő forrás-és fogadó típusok lettek hozzáadva:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>4.0.1 verzió
### <a name="breaking-changes"></a>Kompatibilitástörő változások
A következő osztályok lettek átnevezve. Az új nevek az osztályok eredeti nevei voltak, mielőtt 4.0.0 a kiadást.

| Név a 4.0.0 | Név a 4.0.1-ban |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>4.0.0-s verzió
### <a name="breaking-changes"></a>Kompatibilitástörő változások
* A következő osztályok/felületek lettek átnevezve.

| Régi név | Új név |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Táblázat |[Adathalmaz](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* A **lista** metódusai mostantól a lapozható eredményeket adják vissza. Ha a válasz nem üres **NextLink** tulajdonságot tartalmaz, az ügyfélalkalmazásnak továbbra is le kell kérnie a következő lapot, amíg az összes oldalt vissza nem adja.  Alább bemutatunk egy példát:

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
* A **List** folyamat API a teljes részletek helyett csak egy folyamat összegzését adja vissza. Például egy folyamat összefoglaló tevékenységei csak a nevet és a típust tartalmazzák.

### <a name="feature-additions"></a>Szolgáltatások kiegészítései
* A [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) osztály két új tulajdonságot ( **SliceIdentifierColumnName** és **SqlWriterCleanupScript**) támogat az Azure Azure szinapszis Analytics idempotens-másolásának támogatásához. Ezen tulajdonságokkal kapcsolatos részletekért tekintse meg az [Azure szinapszis Analytics](data-factory-azure-sql-data-warehouse-connector.md) -cikket.
* A másolási tevékenység részeként mostantól támogatjuk a Azure SQL Database és az Azure szinapszis Analytics-források futtatására szolgáló tárolt eljárást. A [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) és a [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) osztály a következő tulajdonságokkal rendelkezik: **SqlReaderStoredProcedureName** és **StoredProcedureParameters**. Ezekről a tulajdonságokról a Azure.com [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) és az [Azure szinapszis Analytics](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) cikkeiben talál további információkat.  
