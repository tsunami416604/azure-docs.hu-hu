---
title: Eseményrácsos adatkapcsolat létrehozása az Azure Data Explorer számára a Python használatával
description: Ebben a cikkben megtudhatja, hogyan hozhat létre eventgrid-adatkapcsolatot az Azure Data Explorer python használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1439383598517f57bc77e718d4ded7f53941d3bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444196"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-python"></a>Eseményrácsos adatkapcsolat létrehozása az Azure Data Explorer számára a Python használatával

> [!div class="op_single_selector"]
> * [Portál](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-sablon](data-connection-event-grid-resource-manager.md)

Ebben a cikkben hozzon létre egy Event Grid adatkapcsolatot az Azure Data Explorer python használatával. Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Data Explorer az Event Hubs, az IoT Hubs és a blobtárolókba írt blobok betöltését vagy betöltését kínálja.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.4+](https://www.python.org/downloads/).

* [Fürt és adatbázis](create-cluster-database-python.md).

* [Táblázat- és oszlopleképezés](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Adatbázis- és táblaházirendek](database-table-policies-csharp.md) (nem kötelező).

* [Eseményrács-előfizetéssel rendelkező tárfiók.](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account)

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Eseményrács adatkapcsolat ának hozzáadása

A következő példa bemutatja, hogyan adhat hozzá eseményrácsos adatkapcsolatot programozott módon. Tekintse [meg az Event Grid-adatkapcsolat létrehozása az Azure Data Explorerben](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) az Event Grid-adatkapcsolat azure-portálhasználatával történő hozzáadásáról.


```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection
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
#The event hub and storage account that are created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx"
storage_account_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=EventGridDataConnection(storage_account_resource_id=storage_account_resource_id, event_hub_resource_id=event_hub_resource_id, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```
|**Beállítás** | **Ajánlott érték** | **Mező leírása**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A bérlőazonosítója. Más néven könyvtárazonosító.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Az erőforrás-létrehozáshoz használt előfizetés-azonosító.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Az alkalmazás ügyfélazonosítója, amely hozzáférhet a bérlő erőforrásaihoz.|
| client_secret | *xxxxxxxxxxxxxx* | Az alkalmazás ügyféltka-tka, amely hozzáférhet a bérlő erőforrásaihoz. |
| resource_group_name | *testrg* | A fürtöt tartalmazó erőforráscsoport neve.|
| cluster_name | *mykustocluster* | A fürt neve.|
| database_name | *mykustoadatbázis* | A fürtcél-adatbázis neve.|
| data_connection_name | *myeventhubconnect* | Az adatkapcsolat kívánt neve.|
| table_name | *StormEsemények* | A céltábla neve a céladatbázisban.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | A céltáblához kapcsolódó oszlopleképezés neve.|
| data_format | *Csv* | Az üzenet adatformátuma.|
| event_hub_resource_id | *Erőforrás azonosítója* | Az eseményközpont erőforrás-azonosítója, ahol az Eseményrács események küldésére van konfigurálva. |
| storage_account_resource_id | *Erőforrás azonosítója* | A tárfiók erőforrás-azonosítója, amely a betöltési adatokat tartalmazza. |
| consumer_group | *$Default* | Az Event Hub fogyasztói csoportja.|
| location | *USA középső régiója* | Az adatkapcsolati erőforrás helye.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]