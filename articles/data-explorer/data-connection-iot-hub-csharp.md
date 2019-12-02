---
title: IoT Hub adatkapcsolatok létrehozása az Azure Adatkezelőhoz a használatávalC#
description: Ebből a cikkből megtudhatja, hogyan hozhat létre IoT Hub adatelérést az Azure Adatkezelőhoz a használatával C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 0cac03e50bf46910f8430b745803107b60905769
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667386"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>IoT Hub adatkapcsolatok létrehozása az Azure Adatkezelőhoz az ( C# előzetes verzió) használatával

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager-sablon](data-connection-iot-hub-resource-manager.md)

Az Azure Data Explorer egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Adatkezelő a betöltést (az adatok betöltését) Event Hubs, IoT hubokból és blob-tárolóba írt blobokból biztosítja. Ebben a cikkben a használatával C#hoz létre IoT Hub adatAdatkezelő az Azure-hoz.

## <a name="prerequisites"></a>Előfeltételek

* Ha nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition verziót](https://www.visualstudio.com/downloads/). Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.
* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Fürt és adatbázis](create-cluster-database-csharp.md) létrehozása
* [Tábla-és oszlop-hozzárendelés](net-standard-ingest-data.md#create-a-table-on-your-test-cluster) létrehozása
* [Adatbázis-és táblázat-házirendek](database-table-policies-csharp.md) beállítása (nem kötelező)
* Hozzon létre egy [IoT hub megosztott hozzáférési házirenddel konfigurálva](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>IoT Hub adatkapcsolatok hozzáadása 

Az alábbi példa bemutatja, hogyan adhat hozzá programozott módon egy IoT Hub adatkapcsolódást. További információ: az [Azure adatkezelő Table csatlakoztatása IoT hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) IoT hub-adatkapcsolat hozzáadásához a Azure Portal használatával.

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
//The IoT hub that is created as part of the Prerequisites
var iotHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
var sharedAccessPolicyName = "iothubforread";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdate(resourceGroupName, clusterName, databaseName, dataConnectionName,
            new IotHubDataConnection(iotHubResourceId, consumerGroup, sharedAccessPolicyName, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Beállítás** | **Ajánlott érték** | **Mező leírása**|
|---|---|---|
| TenantId | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | A bérlő azonosítója. Más néven címtár-azonosító.|
| subscriptionId | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | Az erőforrás-létrehozáshoz használt előfizetés-azonosító.|
| clientId | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | Annak az alkalmazásnak az ügyfél-azonosítója, amely hozzáférhet a bérlő erőforrásaihoz.|
| clientSecret | *XXXXXXXXXXXXXX* | Az alkalmazás ügyfél-titka, amely hozzáférhet a bérlő erőforrásaihoz. |
| resourceGroupName | *testrg* | A fürtöt tartalmazó erőforráscsoport neve.|
| clusterName | *mykustocluster* | A fürt neve.|
| databaseName | *mykustodatabase* | A fürtben lévő céladatbázis neve.|
| dataConnectionName | *myeventhubconnect* | Az adathálózat kívánt neve.|
| tableName | *StormEvents* | A céladatbázis neve a célként megadott adatbázisban.|
| mappingRuleName | *StormEvents_CSV_Mapping* | A célként megadott táblához kapcsolódó oszlop-hozzárendelés neve|
| dataFormat | *CSV* | Az üzenet adatformátuma.|
| iotHubResourceId | *Erőforrás-azonosító* | Az IoT hub erőforrás-azonosítója, amely a betöltéshez szükséges adatot tárolja. |
| sharedAccessPolicyName | *iothubforread* | Annak a megosztott hozzáférési házirendnek a neve, amely a IoT Hubhoz való kapcsolódáshoz szükséges eszközöket és szolgáltatásokat határozza meg. |
| consumerGroup | *$Default* | Az Event hub fogyasztói csoportja.|
| location | *USA középső régiója* | Az adatkapcsolatok erőforrásának helye.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
