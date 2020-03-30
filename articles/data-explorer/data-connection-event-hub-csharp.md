---
title: 'Event Hub-adatkapcsolat létrehozása az Azure Data Explorer számára a C használatával #'
description: Ebben a cikkben megtudhatja, hogyan hozhat létre Event Hub-adatkapcsolatot az Azure Data Explorer számára a C# használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: cf2a274b4f48b31739d6abba5cf87fa2a10d4ca1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667695"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>Event Hub-adatkapcsolat létrehozása az Azure Data Explorer számára a C használatával #

> [!div class="op_single_selector"]
> * [Portál](ingest-data-event-hub.md)
> * [C #](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager-sablon](data-connection-event-hub-resource-manager.md)

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Data Explorer az Event Hubs, az IoT Hubs és a blobtárolókba írt blobok betöltését (adatbetöltését) kínálja. Ebben a cikkben hozzon létre egy Event Hub-adatkapcsolatot az Azure Data Explorer c#használatával.

## <a name="prerequisites"></a>Előfeltételek

* Ha nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition alkalmazást.](https://www.visualstudio.com/downloads/) Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.
* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Fürt és adatbázis](create-cluster-database-csharp.md) létrehozása
* [Táblázat- és oszlopleképezés létrehozása](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* [Adatbázis- és táblaházirendek](database-table-policies-csharp.md) beállítása (nem kötelező)
* Hozzon létre egy [eseményközpontot adatokkal a betöltéshez.](ingest-data-event-hub.md#create-an-event-hub) 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Eseményközpont-adatkapcsolat hozzáadása

A következő példa bemutatja, hogyan adhat hozzá egy Event Hub-adatkapcsolatot programozott módon. Tekintse meg [az eseményközponthoz való csatlakozást](ingest-data-event-hub.md#connect-to-the-event-hub) az Event Hub-adatkapcsolat azure-portálon való hozzáadásához.

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
//The event hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName, 
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Beállítás** | **Ajánlott érték** | **Mező leírása**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A bérlőazonosítója. Más néven könyvtárazonosító.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Az erőforrás-létrehozáshoz használt előfizetés-azonosító.|
| ügyfél-azonosító | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Az alkalmazás ügyfélazonosítója, amely hozzáférhet a bérlő erőforrásaihoz.|
| ügyféltitkos | *xxxxxxxxxxxxxx* | Az alkalmazás ügyféltka-tka, amely hozzáférhet a bérlő erőforrásaihoz.|
| resourceGroupName | *testrg* | A fürtöt tartalmazó erőforráscsoport neve.|
| clusterName | *mykustocluster* | A fürt neve.|
| adatbázisneve | *mykustoadatbázis* | A fürtcél-adatbázis neve.|
| dataConnectionName | *myeventhubconnect* | Az adatkapcsolat kívánt neve.|
| tableName | *StormEsemények* | A céltábla neve a céladatbázisban.|
| leképezési szabályneve | *StormEvents_CSV_Mapping* | A céltáblához kapcsolódó oszlopleképezés neve.|
| dataFormat | *Csv* | Az üzenet adatformátuma.|
| eventHubResourceId | *Erőforrás azonosítója* | Az Event Hub erőforrás-azonosítója, amely a betöltéshez adatokat tartalmazza. |
| consumerGroup (fogyasztói csoport) | *$Default* | Az Event Hub fogyasztói csoportja.|
| location | *USA középső régiója* | Az adatkapcsolati erőforrás helye.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
