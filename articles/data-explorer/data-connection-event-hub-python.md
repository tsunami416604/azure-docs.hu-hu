---
title: Event hub-adatkapcsolat létrehozása az Azure Adatkezelőhoz a Python használatával
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy Event hub-adatkapcsolatot az Azure Adatkezelőhoz a Python használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: b69cdb1ee04e3824bf5fd20a7db2401161fdf6e7
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031668"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-python"></a>Event hub-adatkapcsolat létrehozása az Azure Adatkezelőhoz a Python használatával

> [!div class="op_single_selector"]
> * [Portál](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Adatkezelő a betöltést (az adatok betöltését) Event Hubs, IoT hubokból és blob-tárolóba írt blobokból biztosítja. Ebben a cikkben a Python használatával hoz létre egy Event hub-adatkapcsolatot az Azure Adatkezelőhoz.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

* [Fürt és adatbázis](create-cluster-database-csharp.md) létrehozása

* [Tábla-és oszlop-hozzárendelés](net-standard-ingest-data.md#create-a-table-on-your-test-cluster) létrehozása

* [Adatbázis-és táblázat-házirendek](database-table-policies-csharp.md) beállítása (nem kötelező)

* Hozzon létre egy [Event hub-t a](ingest-data-event-hub.md#create-an-event-hub)betöltéshez szükséges adattal. 

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Event hub-adatkapcsolat hozzáadása

Az alábbi példa bemutatja, hogyan adhat hozzá programozott módon egy Event hub-adatkapcsolatot. Lásd: [Kapcsolódás az Event hubhoz](ingest-data-event-hub.md#connect-to-the-event-hub) az Event hub-adatkapcsolat hozzáadásához a Azure Portal használatával.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventHubDataConnection
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

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";
data_connection_name = "myeventhubconnect";
#The event hub that is created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
consumer_group = "$Default";
location = "Central US";
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents";
mapping_rule_name = "StormEvents_CSV_Mapping";
data_format = "csv";
#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                        parameters=EventHubDataConnection(event_hub_resource_id=event_hub_resource_id, consumer_group=consumer_group, location=location,
                                                                            table_name=table_name, mapping_rule_name=mapping_rule_name, data_format=data_format))
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
| event_hub_resource_id | *Erőforrás-azonosító* | Az Event hub erőforrás-azonosítója, amely a betöltéshez szükséges adatot tárolja. |
| consumer_group | *$Default* | Az Event hub fogyasztói csoportja.|
| location | *USA középső régiója* | Az adatkapcsolatok erőforrásának helye.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]