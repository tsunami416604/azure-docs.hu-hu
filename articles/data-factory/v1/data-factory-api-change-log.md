---
title: A Data Factory – .NET API – módosítási napló |} A Microsoft Docs
description: Ismerteti a kompatibilitástörő változásokat, a szolgáltatás hozzáadása, hibajavításokat tartalmaz, stb... az Azure Data Factory .NET API-val adott verziójában.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 8208271b-7f4c-4214-b665-d2ff503c4470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 863f3500c84eeab1c3dac19141cd334fc6961694
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015916"
---
# <a name="azure-data-factory---net-api-change-log"></a>Az Azure Data Factory – .NET API-Változásnapló
> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. 

Ez a cikk változásairól nyújt információt, az Azure Data Factory SDK egy adott verziójában. Annak a legújabb NuGet-csomagot az Azure Data Factoryhoz [Itt](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>Verzió 4.11.0
Funkció hozzáadása:

* A következő kapcsolódószolgáltatás-típusok lettek hozzáadva:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* A következő adatkészlet-típusok lettek hozzáadva:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* A következő másolási forrástípusok lettek hozzáadva:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>Verzió 4.10.0
* Az alábbi választható tulajdonságokkal TextFormat lettek hozzáadva:
  * [skipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [treatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* A következő kapcsolódószolgáltatás-típusok lettek hozzáadva:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* A következő adatkészlet-típusok lettek hozzáadva:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* A következő másolási forrástípusok lettek hozzáadva:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Adjon hozzá [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) AzureMLBatchExecutionActivity tulajdonság
  * Több webes szolgáltatás bemeneti adatokat egy Azure Machine Learning-kísérlet passing engedélyezése

## <a name="version-491"></a>Verzió 4.9.1.
### <a name="bug-fix"></a>Hibajavítás
* A WebApi alapú hitelesítés kivezetjük [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>Verzió 4.9.0
### <a name="feature-additions"></a>Funkció hozzáadása
* Adjon hozzá [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) és [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) CopyActivity tulajdonságokat. Lásd: [szakaszos Másolás](data-factory-copy-activity-performance.md#staged-copy) a szolgáltatás részleteiért.

### <a name="bug-fix"></a>Hibajavítás
* Vezessen be egy túlterhelésére [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) módszer, amely egy [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) példány.
* Megjelölés [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) és [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) CopySink is kötelező.

## <a name="version-480"></a>Verzió 4.8.0
### <a name="feature-additions"></a>Funkció hozzáadása
* Másolási tevékenység típusa másolási teljesítmény hangolása engedélyezéséhez a következő kötelező tulajdonságok lettek hozzáadva:
  * [parallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>Verzió 4.7.0
### <a name="feature-additions"></a>Funkció hozzáadása
* Hozzáadott új StorageFormat típus [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) írja be a fájlok másolása optimalizált sor Oszlopalapú (ORC formátumban).
* Adjon hozzá [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) és a kapcsolódó PolyBaseSettings tulajdonságok SqlDWSink.
  * Adatok másolása az SQL Data Warehouse-bA a PolyBase használatát teszi lehetővé.

## <a name="version-461"></a>4.6.1-es verziója
### <a name="bug-fixes"></a>Hibajavítások
* HTTP-kérés a tevékenységablakok javít.
  * A kérelem hasznos adatai eltávolítja az erőforráscsoport nevét és az adat-előállító nevét.

## <a name="version-460"></a>Verzió 4.6.0
### <a name="feature-additions"></a>Funkció hozzáadása
* A következő tulajdonságok lettek hozzáadva [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [pipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Adatkészletek](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* A következő tulajdonságok lettek hozzáadva [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Új [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) típus [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) írja be a JSON formátumban vannak, amelyek adatait adatkészletet határoz meg.

## <a name="version-450"></a>Verzió 4.5.0
### <a name="feature-additions"></a>Funkció hozzáadása
* Hozzáadott [listázási művelet tevékenységablakot a](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * A hozzáadott metódusok tevékenységablakok beolvasni a szűrőket az entitástípusok (azt jelenti, adat-előállítók, adatkészleteket, folyamatokat és tevékenységeket) alapján.
* A következő kapcsolódószolgáltatás-típusok lettek hozzáadva:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* A következő adatkészlet-típusok lettek hozzáadva:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* A következő másolási forrástípusok lettek hozzáadva:     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>Verzió 4.4.0
### <a name="feature-additions"></a>Funkció hozzáadása
* A következő kapcsolódószolgáltatás-típus adatforrásként hozzá lett adva, és a fogadók a másolási tevékenységek:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Lásd: [Azure Storage SAS társított szolgáltatás](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) elméleti információk és példák.

## <a name="version-430"></a>Verzió 4.3.0 verzióban
### <a name="feature-additions"></a>Funkció hozzáadása
* A következő kapcsolódószolgáltatás-típusok haven lett hozzáadva a másolási tevékenységek adatforrásként:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Lásd: [helyezhet át adatokat a Data Factory használatával HDFS](data-factory-hdfs-connector.md) elméleti információk és példák.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Lásd: [helyezze át az adatok Azure Data Factory használatával az ODBC-adattárak](data-factory-odbc-connector.md) elméleti információk és példák.

## <a name="version-420"></a>Verzió 4.2.0
### <a name="feature-additions"></a>Funkció hozzáadása
* A következő új tevékenységtípus bővült: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). A tevékenységgel kapcsolatos részletekért lásd: [frissítése az Azure Machine Learning modellek használata az Update-Erőforrástevékenység](data-factory-azure-ml-batch-execution-activity.md).
* Új nem kötelező tulajdonság [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) bővült a [AzureMLLinkedService osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) és [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) tulajdonságok lettek hozzáadva a [Datapipelinemanagementclient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) osztály.
* Az időtúllépések, az ügyfél hívásai a Data Factory szolgáltatásban konfigurálását teszik lehetővé.

## <a name="version-410"></a>4.1.0-s
### <a name="feature-additions"></a>Funkció hozzáadása
* A következő kapcsolódószolgáltatás-típusok lettek hozzáadva:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* A következő típusú tevékenységek érhetők el:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* A következő adatkészlet-típusok lettek hozzáadva:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* A másolási tevékenység a következő forrás- és fogadó típusok lettek hozzáadva:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>Verzió 4.0.1
### <a name="breaking-changes"></a>Kompatibilitástörő változások
A következő osztályok kaptak. Az új nevek voltak osztályok eredeti nevére, 4.0.0 kiadás előtt.

| Neve a 4.0.0 verzióra. | Neve a 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>4.0.0-s verzió
### <a name="breaking-changes"></a>Kompatibilitástörő változások
* A következő osztályok felületek kaptak.

| Régi név | Új név |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| Tábla |[Adatkészlet](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* A **lista** módszerek most már vissza a lapokra bontott eredményeket. Ha a válasz tartalmaz egy nem üres **NextLink** tulajdonság, az ügyfélalkalmazásnak szüksége van a folytatáshoz a következő oldalon beolvasása, amíg az összes visszaadott.  Például:

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
* **Lista** API folyamat csak a folyamat részletes helyett összegzését adja vissza. Például tevékenységet a folyamat összegzése csak tartalmazhat nevét és típusát.

### <a name="feature-additions"></a>Funkció hozzáadása
* A [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) az osztály támogatja a két új tulajdonság **SliceIdentifierColumnName** és **SqlWriterCleanupScript**idempotens másolása az Azure SQL Data támogatásához Az adatraktár. Tekintse meg a [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) részleteivel kapcsolatos ezeket a tulajdonságokat.
* Mostantól támogatott az Azure SQL Database és az Azure SQL Data Warehouse források tárolt eljárást futtat a másolási tevékenység részeként. A [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) és [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) a következő tulajdonságokkal rendelkezik: **SqlReaderStoredProcedureName** és **StoredProcedureParameters**. Tekintse meg a [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) és [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) részletezi ezeket a tulajdonságokat az Azure.com webhelyen cikkeket.  
