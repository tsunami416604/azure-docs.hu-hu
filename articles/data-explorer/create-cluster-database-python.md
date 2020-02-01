---
title: Azure Adatkezelő-fürt és-adatbázis létrehozása a Python használatával
description: Ismerje meg, hogyan hozhat létre Azure Adatkezelő-fürtöt és-adatbázist a Python használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 8d43965e87ab57d9f0c79c6661a761b06ccb7073
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902097"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Azure Adatkezelő-fürt és-adatbázis létrehozása a Python használatával

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Parancssori felület](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM-sablon](create-cluster-database-resource-manager.md)

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet alkalmazások, webhelyek, IoT-eszközök és egyebek nagy mennyiségű adatfolyamain. Az Azure Adatkezelő használatához először létre kell hoznia egy fürtöt, és létre kell hoznia egy vagy több adatbázist a fürtben. Ezután betöltheti az adatterhelést egy adatbázisba, így lekérdezéseket futtathat. Ebben a cikkben egy fürtöt és egy adatbázist hoz létre a Python használatával.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="install-python-package"></a>Python-csomag telepítése

Az Azure Adatkezelő (Kusto) Python-csomagjának telepítéséhez nyisson meg egy parancssort, amely a Python elérési útját is tartalmazni fogja. Futtassa ezt a parancsot:

```
pip install azure-common
pip install azure-mgmt-kusto
```
## <a name="authentication"></a>Hitelesítés
A cikkben szereplő példák futtatásához szükség van egy Azure AD-alkalmazásra és egy egyszerű szolgáltatásra, amely hozzáférhet az erőforrásokhoz. Az Azure ad- [alkalmazás](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) létrehozásával hozzon létre egy ingyenes Azure ad-alkalmazást, és adja hozzá a szerepkör-hozzárendelést az előfizetési hatókörhöz. Azt is bemutatja, hogyan kérhető le a `Directory (tenant) ID`, a `Application ID`és a `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Adatkezelő-fürt létrehozása

1. Hozza létre a fürtöt a következő parancs használatával:

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
   | sku_name | *Standard_D13_v2* | A fürthöz használni kívánt SKU. |
   | tier | *Standard* | Az SKU-szintet. |
   | capacity | *száma* | A fürt példányainak száma. |
   | resource_group_name | *testrg* | Az erőforráscsoport neve, amelyben a fürt létre lesz hozva. |

    > [!NOTE]
    > **A fürt létrehozása** hosszú ideig futó művelet. A metódus **create_or_update** a LROPoller egy példányát adja vissza: a [LROPoller osztály](/python/api/msrest/msrest.polling.lropoller?view=azure-python) további információkat kaphat.

1. A következő parancs futtatásával győződjön meg arról, hogy a fürt létrehozása sikeres volt-e:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Ha az eredmény `provisioningState`t tartalmaz a `Succeeded` értékkel, akkor a fürt létrehozása sikeresen megtörtént.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Adatkezelő-fürtben

1. Hozza létre az adatbázist a következő parancs használatával:

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
   | cluster_name | *mykustocluster* | Annak a fürtnek a neve, ahová az adatbázist létre kívánja hozni.|
   | database_name | *mykustodatabase* | Az adatbázis neve.|
   | resource_group_name | *testrg* | Az erőforráscsoport neve, amelyben a fürt létre lesz hozva. |
   | soft_delete_period | *3650 nap, 0:00:00* | Az az időtartam, ameddig az adat a lekérdezés számára elérhető marad. |
   | hot_cache_period | *3650 nap, 0:00:00* | Az az időtartam, ameddig az adat a gyorsítótárban lesz tárolva. |

1. Futtassa a következő parancsot a létrehozott adatbázis megtekintéséhez:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Most már rendelkezik egy fürttel és egy adatbázissal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

* Ha azt tervezi, hogy követi a többi cikket, tartsa meg a létrehozott erőforrásokat.
* Az erőforrások törléséhez törölje a fürtöt. Ha töröl egy fürtöt, az az összes adatbázisát is törli. A fürt törléséhez használja a következő parancsot:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Következő lépések

* [Adatbevitel az Azure Adatkezelő Python Library használatával](python-ingest-data.md)
