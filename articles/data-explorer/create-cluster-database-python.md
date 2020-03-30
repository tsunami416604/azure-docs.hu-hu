---
title: Azure Data Explorer-fürt & adatbázis létrehozása python használatával
description: Ismerje meg, hogyan hozhat létre egy Azure Data Explorer-fürtöt és -adatbázist a Python használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 8425058c9f6ac5b90c37a99f749a810672b406fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560507"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Azure Data Explorer-fürt és-adatbázis létrehozása python használatával

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [parancssori felület](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM sablon](create-cluster-database-resource-manager.md)

Ebben a cikkben hozzon létre egy Azure Data Explorer-fürt és -adatbázis python használatával. Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet többek között alkalmazások, webhelyek és IoT-eszközök nagy mennyiségű adatfolyamain. Az Azure Data Explorer használatához először hozzon létre egy fürtöt, és hozzon létre egy vagy több adatbázist a fürtben. Ezután betölti vagy betölti az adatokat egy adatbázisba, így lekérdezéseket futtathat ellene.

## <a name="prerequisites"></a>Előfeltételek

* Egy aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.4+](https://www.python.org/downloads/).

* [Egy Azure AD-alkalmazás és egyszerű szolgáltatás, amely képes hozzáférni az erőforrásokhoz.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) A és `Directory (tenant) ID` `Application ID`a `Client Secret`érték értékeinek beszereznie.

## <a name="install-python-package"></a>Python-csomag telepítése

A Python-csomag azure Data Explorer (Kusto) telepítéséhez nyisson meg egy parancssort, amely a Python az útjába. Futtassa ezt a parancsot:

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>Hitelesítés
A jelen cikkben szereplő példák futtatásához szükségünk van egy Azure AD-alkalmazásra és egyszerű szolgáltatásra, amely képes hozzáférni az erőforrásokhoz. Ellenőrizze [az Azure AD-alkalmazást](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) egy ingyenes Azure AD-alkalmazás létrehozásához, és adja hozzá a szerepkör-hozzárendelést az előfizetéshatókörben. Azt is bemutatja, `Directory (tenant) ID` `Application ID`hogyan `Client Secret`juthat el a , és .

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Data Explorer-fürt létrehozása

1. Hozza létre a fürtöt a következő paranccsal:

    ```Python
    from azure.mgmt.kusto import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku
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

    location = 'Central US'
    sku_name = 'Standard_D13_v2'
    capacity = 5
    tier = "Standard"
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku_name, capacity=capacity, tier=tier))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    poller = cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | cluster_name | *mykustocluster* | A fürt kívánt neve.|
   | sku_name | *Standard_D13_v2* | A fürthöz használt termékváltozat. |
   | tier | *Standard* | A Termékváltozat szintje. |
   | capacity | *szám* | A fürt példányainak száma. |
   | resource_group_name | *testrg* | Az erőforráscsoport neve, ahol a fürt létrejön. |

    > [!NOTE]
    > **A fürt létrehozása** hosszú ideig futó művelet. Metódus **create_or_update** az LROPoller egy példányát adja vissza, további információkért lásd: [LROPoller osztály.](/python/api/msrest/msrest.polling.lropoller?view=azure-python)

1. Futtassa a következő parancsot annak ellenőrzéséhez, hogy a fürt sikeresen létrejött-e:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Ha az `provisioningState` eredmény `Succeeded` tartalmazza az értéket, akkor a fürt sikeresen létrejött.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Data Explorer-fürtben

1. Hozza létre az adatbázist a következő paranccsal:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = ReadWriteDatabase(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    #Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
    poller =database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

        [!NOTE]
        If you are using Python version 0.4.0 or below, use Database instead of ReadWriteDatabase.

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | cluster_name | *mykustocluster* | Annak a fürtnek a neve, amelyben az adatbázis létrejön.|
   | database_name | *mykustoadatbázis* | Az adatbázis neve.|
   | resource_group_name | *testrg* | Az erőforráscsoport neve, ahol a fürt létrejön. |
   | soft_delete_period | *3650 nap, 0:00:00* | Az az idő, amerre az adatok lekérdezhetők. |
   | hot_cache_period | *3650 nap, 0:00:00* | Az az idő, amerre az adatok a gyorsítótárban maradnak. |

1. A létrehozott adatbázis megtekintéséhez futtassa a következő parancsot:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Most már rendelkezik egy fürttel és egy adatbázissal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

* Ha azt tervezi, hogy követi a többi cikket, tartsa meg a létrehozott erőforrásokat.
* Erőforrások törléséhez törölje a fürtöt. Fürt törlésekor az összes benne lévő adatbázist is törli. A fürt törléséhez használja a következő parancsot:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>További lépések

* [Adatok betöltése az Azure Data Explorer Python-tár használatával](python-ingest-data.md)
