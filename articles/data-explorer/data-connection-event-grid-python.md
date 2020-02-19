---
title: Event Grid adatkapcsolatok létrehozása az Azure Adatkezelőhoz a Python használatával
description: Ebből a cikkből megtudhatja, hogyan hozhat létre Event Grid adatkapcsolódást az Azure Adatkezelőhoz a Python használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1439383598517f57bc77e718d4ded7f53941d3bb
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444196"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-python"></a>Event Grid adatkapcsolatok létrehozása az Azure Adatkezelőhoz a Python használatával

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C#](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-sablon](data-connection-event-grid-resource-manager.md)

Ebben a cikkben a Python használatával hoz létre Event Grid adatösszekötő Azure Adatkezelő. Az Azure Data Explorer egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Adatkezelő a betöltést, illetve az adatok betöltését Event Hubs, IoT hubokból és blob-tárolóba írt blobokból biztosítja.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

* [Python 3.4 +](https://www.python.org/downloads/).

* [Egy fürt és egy adatbázis](create-cluster-database-python.md).

* [Tábla-és oszlop-hozzárendelés](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Adatbázis-és táblázat-házirendek](database-table-policies-csharp.md) (nem kötelező).

* [Event Grid-előfizetéssel rendelkező Storage-fiók](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account).

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Event Grid adatkapcsolatok hozzáadása

Az alábbi példa bemutatja, hogyan adhat hozzá programozott módon egy Event Grid adatkapcsolódást. A Azure Portal használatával Event Grid adatkapcsolatok hozzáadásával kapcsolatban lásd: [Event Grid adatkapcsolatok létrehozása az Azure Adatkezelőban](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) .


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
| tenant_id | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | A bérlő azonosítója. Más néven címtár-azonosító.|
| subscription_id | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | Az erőforrás-létrehozáshoz használt előfizetés-azonosító.|
| client_id | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | Annak az alkalmazásnak az ügyfél-azonosítója, amely hozzáférhet a bérlő erőforrásaihoz.|
| client_secret | *XXXXXXXXXXXXXX* | Az alkalmazás ügyfél-titka, amely hozzáférhet a bérlő erőforrásaihoz. |
| resource_group_name | *testrg* | A fürtöt tartalmazó erőforráscsoport neve.|
| cluster_name | *mykustocluster* | A fürt neve.|
| database_name | *mykustodatabase* | A fürtben lévő céladatbázis neve.|
| data_connection_name | *myeventhubconnect* | Az adathálózat kívánt neve.|
| table_name | *StormEvents* | A céladatbázis neve a célként megadott adatbázisban.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | A célként megadott táblához kapcsolódó oszlop-hozzárendelés neve|
| data_format | *CSV* | Az üzenet adatformátuma.|
| event_hub_resource_id | *Erőforrás-azonosító* | Az Event hub erőforrás-azonosítója, amelyben a Event Grid az események küldésére van konfigurálva. |
| storage_account_resource_id | *Erőforrás-azonosító* | A Storage-fiók erőforrás-azonosítója, amely a betöltéshez szükséges adatot tárolja. |
| consumer_group | *$Default* | Az Event hub fogyasztói csoportja.|
| location | *USA középső régiója* | Az adatkapcsolatok erőforrásának helye.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]