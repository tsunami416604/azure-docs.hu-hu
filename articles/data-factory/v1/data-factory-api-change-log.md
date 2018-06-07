---
title: Adat-előállító - .NET API Változásnapló |} Microsoft Docs
description: Ismerteti, akár jelentős változásokat, szolgáltatás kiegészítéseit, hibajavításokat tartalmaz stb... a .NET API-t az Azure Data Factory a meghatározott verzióját.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.assetid: 8208271b-7f4c-4214-b665-d2ff503c4470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: c5f47379072f4e5d15ffd96c5e45a23d10fff187
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620248"
---
# <a name="azure-data-factory---net-api-change-log"></a>Az Azure Data Factory - .NET API Változásnapló
> [!NOTE]
> Ez a cikk a Data Factory általánosan elérhető 1. verziójára vonatkozik. 

Ez a cikk változásairól nyújt információt az Azure Data Factory SDK egy adott verziójában. A legújabb NuGet-csomagot az Azure Data Factory található [Itt](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)

## <a name="version-4110"></a>4.11.0 verzió
A szolgáltatás kiegészítéseket:

* A következő kapcsolódószolgáltatás-típusok bővült:
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* A következő adatkészlet-típusok bővült:
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* A következő másolási eseményforrás-típusnak bővült:
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>4.10.0 verzió
* A következő választható tulajdonságok szöveges bővült:
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* A következő kapcsolódószolgáltatás-típusok bővült:
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* A következő adatkészlet-típusok bővült:
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* A következő másolási eseményforrás-típusnak bővült:
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* Adja hozzá [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) azuremlbatchexecutionactivity tevékenységnek A(z) tulajdonság
  * Több webkiszolgáló bemenetei az Azure Machine Learning a kísérlet sikeres engedélyezése

## <a name="version-491"></a>Verzió 4.9.1.
### <a name="bug-fix"></a>Hibajavítás
* WebApi-alapú hitelesítés érvényteleníthető [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx).

## <a name="version-490"></a>4.9.0 verzió
### <a name="feature-additions"></a>A szolgáltatás elemek felvétele
* Adja hozzá [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) és [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) alakíthatók tulajdonságokat. Lásd: [másolási előkészített](data-factory-copy-activity-performance.md#staged-copy) a szolgáltatás leírását.

### <a name="bug-fix"></a>Hibajavítás
* Egy túlterhelése bevezetni [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) metódus, amely fogad egy [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) példány.
* Megjelölés [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) és [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) CopySink, nem kötelező.

## <a name="version-480"></a>4.8.0 verzió
### <a name="feature-additions"></a>A szolgáltatás elemek felvétele
* A következő választható tulajdonságok érhetőek el a másolási tevékenység típusa másolási teljesítményének hangolása engedélyezése:
  * [parallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>4.7.0 verzió
### <a name="feature-additions"></a>A szolgáltatás elemek felvétele
* Hozzáadott új StorageFormat típust [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) típus optimalizált sor oszlopos (ORC) formátumú fájlok másolásához.
* Adja hozzá [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) és a kapcsolódó PolyBaseSettings tulajdonságok SqlDWSink.
  * Adatok másolása az SQL Data Warehouse PolyBase használatát teszi lehetővé.

## <a name="version-461"></a>Verzió 4.6.1.
### <a name="bug-fixes"></a>Hibajavítások
* Kijavítja a felsoroló tevékenység windows HTTP-kérelem.
  * Az erőforráscsoport neve és az adat-előállító eltávolítja a kérelem hasznos.

## <a name="version-460"></a>4.6.0 verzió
### <a name="feature-additions"></a>A szolgáltatás elemek felvétele
* A következő tulajdonságok érhetőek el [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx):
  * [pipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [Adatkészletek](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* A következő tulajdonságok érhetőek el [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx):
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* Új hozzáadott [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) típus [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) típus meghatározásához adatkészleteket, amelyek esetén az JSON formátumban kell megadni.

## <a name="version-450"></a>4.5.0 verziója
### <a name="feature-additions"></a>A szolgáltatás elemek felvétele
* Hozzáadott [tevékenység ablakban műveleteinek listázása](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx).
  * A hozzáadott metódusok tevékenység windows beolvasása szűrők alapján az entitástípusok (Ez azt jelenti, hogy adat-előállítók, adathalmazokat, adatcsatornákat és tevékenységek).
* A következő kapcsolódószolgáltatás-típusok bővült:
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx), [WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* A következő adatkészlet-típusok bővült:
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx), [WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* A következő másolási eseményforrás-típusnak bővült:     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>4.4.0 verzió
### <a name="feature-additions"></a>A szolgáltatás elemek felvétele
* A következő kapcsolódószolgáltatás-típus adatforrásaként hozzá lett adva, és a másolási tevékenység fogadók esetében:
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx). Lásd: [Azure Storage SAS társított szolgáltatás](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) kapcsolatos információkat és példákat.

## <a name="version-430"></a>4.3.0 verzió
### <a name="feature-additions"></a>A szolgáltatás elemek felvétele
* A következő kapcsolódószolgáltatás-típusok régen lett fel, mert a másolási tevékenység adatforrások:
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx). Lásd: [tárolt adatok mozgatása használatával a Data Factory HDFS](data-factory-hdfs-connector.md) kapcsolatos információkat és példákat.
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx). Lásd: [helyezze át az adatok Azure Data Factory használatával az ODBC-adattároló](data-factory-odbc-connector.md) kapcsolatos információkat és példákat.

## <a name="version-420"></a>4.2.0 verzió
### <a name="feature-additions"></a>A szolgáltatás elemek felvétele
* A következő új tevékenységtípus hozzá lett adva: [AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx). A tevékenység kapcsolatos részletekért lásd: [frissítése Azure ML modellek használata az Update-Erőforrástevékenység](data-factory-azure-ml-batch-execution-activity.md).
* Egy új kötelező tulajdonság [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) hozzá lett adva a [AzureMLLinkedService osztály](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx).
* [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) és [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) tulajdonságok érhetőek el a [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) osztály.
* Az ügyfél hívások a Data Factory szolgáltatásnak időtúllépések konfigurálásának engedélyezése.

## <a name="version-410"></a>4.1.0 verzió
### <a name="feature-additions"></a>A szolgáltatás elemek felvétele
* A következő kapcsolódószolgáltatás-típusok bővült:
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* A következő típusú tevékenységek bővült:
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* A következő adatkészlet-típusok bővült:
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* A következő forrás és a fogadó típusok másolási tevékenységhez bővült:
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>4.0.1 verziója
### <a name="breaking-changes"></a>Meghibásodást okozó változások
A következő osztályok átnevezték. Az új nevek osztályok eredeti nevei előtti 4.0.0 kiadási.

| Neve a 4.0.0 | Neve a 4.0.1 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>4.0.0-s verzió
### <a name="breaking-changes"></a>Meghibásodást okozó változások
* A következő osztályok felületek átnevezték.

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

* A **lista** módszerek most lapozható eredményeket adjon. Ha a válaszban nem üres **NextLink** tulajdonság, az ügyfélalkalmazás kell folytatni beolvasása a következő oldalon, amíg a rendszer visszairányítja az összes.  Például:

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
* **Lista** csővezeték API csak a teljes leírást helyett adatcsatorna kivonat adja vissza. Például csővezeték összefoglaló tevékenységet csak tartalmazhat nevét és típusát.

### <a name="feature-additions"></a>A szolgáltatás elemek felvétele
* A [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) az osztály támogatja a két új tulajdonságok, **SliceIdentifierColumnName** és **SqlWriterCleanupScript**idempotent másolása az Azure SQL Data Warehouse támogatásához. Tekintse meg a [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md) szóló cikkben olvashat ezeket a tulajdonságokat.
* Most már támogatott a tárolt eljárás futtatott Azure SQL Database és az Azure SQL Data Warehouse források a másolási tevékenység részeként. A [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) és [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) osztályok a következő jellemzőkkel rendelkezik: **SqlReaderStoredProcedureName** és **StoredProcedureParameters**. Tekintse meg a [Azure SQL Database](data-factory-azure-sql-connector.md#sqlsource) és [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource) cikkek az Azure.com-on, ezek a Tulajdonságok vonatkozó további információért.  
