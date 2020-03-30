---
title: 'Eseményrácsos adatkapcsolat létrehozása az Azure Data Explorer számára a C használatával #'
description: Ebben a cikkben megtudhatja, hogyan hozhat létre eventgrid-adatkapcsolatot az Azure Data Explorer számára a C# használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 03963f60cc364dd36ad55c0a28e92e3b585bb38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255080"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Eseményrácsos adatkapcsolat létrehozása az Azure Data Explorer számára a C használatával #

> [!div class="op_single_selector"]
> * [Portál](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-sablon](data-connection-event-grid-resource-manager.md)


Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Data Explorer az Event Hubs, az IoT Hubs és a blobtárolókba írt blobok betöltését (adatbetöltését) kínálja. Ebben a cikkben hozzon létre egy Event Grid adatkapcsolatot az Azure Data Explorer c#használatával.

## <a name="prerequisites"></a>Előfeltételek

* Ha nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition alkalmazást.](https://www.visualstudio.com/downloads/) Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.
* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Fürt és adatbázis](create-cluster-database-csharp.md) létrehozása
* [Táblázat- és oszlopleképezés létrehozása](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* [Adatbázis- és táblaházirendek](database-table-policies-csharp.md) beállítása (nem kötelező)
* Hozzon létre egy [tárfiókot egy Event Grid-előfizetéssel.](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Eseményrács adatkapcsolat ának hozzáadása

A következő példa bemutatja, hogyan adhat hozzá eseményrácsos adatkapcsolatot programozott módon. Tekintse [meg az Event Grid-adatkapcsolat létrehozása az Azure Data Explorerben](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) az Event Grid-adatkapcsolat azure-portálhasználatával történő hozzáadásáról.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub and storage account that are created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var storageAccountResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Beállítás** | **Ajánlott érték** | **Mező leírása**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A bérlőazonosítója. Más néven könyvtárazonosító.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Az erőforrás-létrehozáshoz használt előfizetés-azonosító.|
| ügyfél-azonosító | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Az alkalmazás ügyfélazonosítója, amely hozzáférhet a bérlő erőforrásaihoz.|
| ügyféltitkos | *xxxxxxxxxxxxxx* | Az alkalmazás ügyféltka-tka, amely hozzáférhet a bérlő erőforrásaihoz. |
| resourceGroupName | *testrg* | A fürtöt tartalmazó erőforráscsoport neve.|
| clusterName | *mykustocluster* | A fürt neve.|
| adatbázisneve | *mykustoadatbázis* | A fürtcél-adatbázis neve.|
| dataConnectionName | *myeventhubconnect* | Az adatkapcsolat kívánt neve.|
| tableName | *StormEsemények* | A céltábla neve a céladatbázisban.|
| leképezési szabályneve | *StormEvents_CSV_Mapping* | A céltáblához kapcsolódó oszlopleképezés neve.|
| dataFormat | *Csv* | Az üzenet adatformátuma.|
| eventHubResourceId | *Erőforrás azonosítója* | Az eseményközpont erőforrás-azonosítója, ahol az Eseményrács események küldésére van konfigurálva. |
| storageAccountResourceId | *Erőforrás azonosítója* | A tárfiók erőforrás-azonosítója, amely a betöltési adatokat tartalmazza. |
| consumerGroup (fogyasztói csoport) | *$Default* | Az Event Hub fogyasztói csoportja.|
| location | *USA középső régiója* | Az adatkapcsolati erőforrás helye.|

## <a name="generate-sample-data"></a>Mintaadatok létrehozása

Most, hogy az Azure Data Explorer és a tárfiók csatlakoztatva van, létrehozhat mintaadatokat, és feltöltheti azokkal a blobtárba.

Ez a parancsfájl létrehoz egy új tárolót a tárfiókban, feltölt egy meglévő fájlt (blobként) a tárolóba, majd felsorolja a blobokat a tárolóban.

```csharp
var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIngestionMappingReference", "mapping_v2‬");
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

> [!NOTE]
> Az Azure Data Explorer nem törli a blobok betöltés után. Az [Azure Blob-tárolási életciklussal](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) három-öt napig őrizheti meg a blobokat a blobok törlésének kezeléséhez.

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
