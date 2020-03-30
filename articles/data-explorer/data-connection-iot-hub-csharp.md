---
title: 'IoT Hub-adatkapcsolat létrehozása az Azure Data Explorer számára a C használatával #'
description: Ebben a cikkben megtudhatja, hogyan hozhat létre IoT Hub-adatkapcsolatot az Azure Data Explorer c#használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 0cac03e50bf46910f8430b745803107b60905769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667386"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>IoT Hub-adatkapcsolat létrehozása az Azure Data Explorer számára a C# (előzetes verzió) használatával

> [!div class="op_single_selector"]
> * [Portál](ingest-data-iot-hub.md)
> * [C #](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager-sablon](data-connection-iot-hub-resource-manager.md)

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Data Explorer az Event Hubs, az IoT Hubs és a blobtárolókba írt blobok betöltését (adatbetöltését) kínálja. Ebben a cikkben hozzon létre egy IoT Hub-adatkapcsolatot az Azure Data Explorer c# használatával.

## <a name="prerequisites"></a>Előfeltételek

* Ha nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition alkalmazást.](https://www.visualstudio.com/downloads/) Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.
* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Fürt és adatbázis](create-cluster-database-csharp.md) létrehozása
* [Táblázat- és oszlopleképezés létrehozása](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* [Adatbázis- és táblaházirendek](database-table-policies-csharp.md) beállítása (nem kötelező)
* Hozzon létre egy [IoT Hub ot megosztott hozzáférési házirend-konfigurált.](ingest-data-iot-hub.md#create-an-iot-hub)

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>IoT Hub-adatkapcsolat hozzáadása 

A következő példa bemutatja, hogyan adhat hozzá egy IoT Hub-adatkapcsolatot programozott módon. Tekintse meg [az Azure Data Explorer-tábla csatlakoztatása az IoT Hubhoz](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) az Iot Hub-adatkapcsolat azure-portál használatával való hozzáadásához.

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
| iotHubResourceId | *Erőforrás azonosítója* | Az IoT hub erőforrás-azonosítója, amely a betöltéshez tárolt adatokat tartalmazza. |
| sharedAccessPolicyName | *iothubforread* | A megosztott hozzáférési szabályzat neve, amely meghatározza az eszközök és szolgáltatások IoT Hubhoz való csatlakozásának engedélyeit. |
| consumerGroup (fogyasztói csoport) | *$Default* | Az eseményközpont fogyasztói csoportja.|
| location | *USA középső régiója* | Az adatkapcsolati erőforrás helye.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
