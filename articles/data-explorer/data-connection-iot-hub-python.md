---
title: IoT Hub adatkapcsolatok létrehozása az Azure Adatkezelőhoz a Python használatával
description: Ebből a cikkből megtudhatja, hogyan hozhat létre IoT Hub adatkapcsolódást az Azure Adatkezelőhoz a Python használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: cfd92546def21972e37781bd8a4b0bfefda9111f
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444213"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>IoT Hub adatkapcsolatok létrehozása az Azure Adatkezelőhoz a Python (előzetes verzió) használatával

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager-sablon](data-connection-iot-hub-resource-manager.md)

Ebben a cikkben a Python használatával hoz létre IoT Hub adatösszekötő Azure Adatkezelő. Az Azure Data Explorer egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Adatkezelő a betöltést, illetve az adatok betöltését Event Hubs, IoT hubokból és blob-tárolóba írt blobokból biztosítja.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4 +](https://www.python.org/downloads/).

* [Egy fürt és egy adatbázis](/create-cluster-database-python.md).

* [Tábla-és oszlop-hozzárendelés](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Adatbázis-és táblázat-házirendek](database-table-policies-python.md) (nem kötelező).

* [Egy IoT hub konfigurált megosztott hozzáférési házirenddel](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>IoT Hub adatkapcsolatok hozzáadása 

Az alábbi példa bemutatja, hogyan adhat hozzá programozott módon egy IoT Hub adatkapcsolódást. További információ: az [Azure adatkezelő Table csatlakoztatása IoT hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) IoT hub-adatkapcsolat hozzáadásához a Azure Portal használatával.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import IotHubDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The IoT hub that is created as part of the Prerequisites
iot_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
shared_access_policy_name = "iothubforread"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=IotHubDataConnection(iot_hub_resource_id=iot_hub_resource_id, shared_access_policy_name=shared_access_policy_name, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Beállítás** | **Ajánlott érték** | **Mező leírása**|
|---|---|---|
| tenant_id | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | A bérlő azonosítója. Más néven címtár-azonosító.|
| subscriptionId | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | Az erőforrás-létrehozáshoz használt előfizetés-azonosító.|
| client_id | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | Annak az alkalmazásnak az ügyfél-azonosítója, amely hozzáférhet a bérlő erőforrásaihoz.|
| client_secret | *XXXXXXXXXXXXXX* | Az alkalmazás ügyfél-titka, amely hozzáférhet a bérlő erőforrásaihoz. |
| resource_group_name | *testrg* | A fürtöt tartalmazó erőforráscsoport neve.|
| cluster_name | *mykustocluster* | A fürt neve.|
| database_name | *mykustodatabase* | A fürtben lévő céladatbázis neve.|
| data_connection_name | *myeventhubconnect* | Az adathálózat kívánt neve.|
| table_name | *StormEvents* | A céladatbázis neve a célként megadott adatbázisban.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | A célként megadott táblához kapcsolódó oszlop-hozzárendelés neve|
| data_format | *CSV* | Az üzenet adatformátuma.|
| iot_hub_resource_id | *Erőforrás-azonosító* | Az IoT hub erőforrás-azonosítója, amely a betöltéshez szükséges adatot tárolja.|
| shared_access_policy_name | *iothubforread* | Annak a megosztott hozzáférési házirendnek a neve, amely a IoT Hubhoz való kapcsolódáshoz szükséges eszközöket és szolgáltatásokat határozza meg. |
| consumer_group | *$Default* | Az Event hub fogyasztói csoportja.|
| location | *USA középső régiója* | Az adatkapcsolatok erőforrásának helye.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]