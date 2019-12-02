---
title: Event hub-adatkapcsolat létrehozása az Azure Adatkezelőhoz a használatávalC#
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy Event hub-adatkapcsolatot az C#Azure Adatkezelőhoz a használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: cf2a274b4f48b31739d6abba5cf87fa2a10d4ca1
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667695"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>Event hub-adatkapcsolat létrehozása az Azure Adatkezelőhoz a használatávalC#

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager-sablon](data-connection-event-hub-resource-manager.md)

Az Azure Data Explorer egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Adatkezelő a betöltést (az adatok betöltését) Event Hubs, IoT hubokból és blob-tárolóba írt blobokból biztosítja. Ebben a cikkben egy Event hub-adatkapcsolatot hoz létre az Azure Adatkezelőhoz C#a használatával.

## <a name="prerequisites"></a>Előfeltételek

* Ha nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition verziót](https://www.visualstudio.com/downloads/). Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.
* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Fürt és adatbázis](create-cluster-database-csharp.md) létrehozása
* [Tábla-és oszlop-hozzárendelés](net-standard-ingest-data.md#create-a-table-on-your-test-cluster) létrehozása
* [Adatbázis-és táblázat-házirendek](database-table-policies-csharp.md) beállítása (nem kötelező)
* Hozzon létre egy [Event hub-t a](ingest-data-event-hub.md#create-an-event-hub)betöltéshez szükséges adattal. 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Event hub-adatkapcsolat hozzáadása

Az alábbi példa bemutatja, hogyan adhat hozzá programozott módon egy Event hub-adatkapcsolatot. Lásd: [Kapcsolódás az Event hubhoz](ingest-data-event-hub.md#connect-to-the-event-hub) az Event hub-adatkapcsolat hozzáadásához a Azure Portal használatával.

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
| TenantId | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | A bérlő azonosítója. Más néven címtár-azonosító.|
| subscriptionId | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | Az erőforrás-létrehozáshoz használt előfizetés-azonosító.|
| clientId | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | Annak az alkalmazásnak az ügyfél-azonosítója, amely hozzáférhet a bérlő erőforrásaihoz.|
| clientSecret | *XXXXXXXXXXXXXX* | Az alkalmazás ügyfél-titka, amely hozzáférhet a bérlő erőforrásaihoz.|
| resourceGroupName | *testrg* | A fürtöt tartalmazó erőforráscsoport neve.|
| clusterName | *mykustocluster* | A fürt neve.|
| databaseName | *mykustodatabase* | A fürtben lévő céladatbázis neve.|
| dataConnectionName | *myeventhubconnect* | Az adathálózat kívánt neve.|
| tableName | *StormEvents* | A céladatbázis neve a célként megadott adatbázisban.|
| mappingRuleName | *StormEvents_CSV_Mapping* | A célként megadott táblához kapcsolódó oszlop-hozzárendelés neve|
| dataFormat | *CSV* | Az üzenet adatformátuma.|
| eventHubResourceId | *Erőforrás-azonosító* | Az Event hub erőforrás-azonosítója, amely a betöltéshez szükséges adatot tárolja. |
| consumerGroup | *$Default* | Az Event hub fogyasztói csoportja.|
| location | *USA középső régiója* | Az adatkapcsolatok erőforrásának helye.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
