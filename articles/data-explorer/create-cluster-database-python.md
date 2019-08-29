---
title: Azure Adatkezelő-fürt és-adatbázis létrehozása a Python használatával
description: Ismerje meg, hogyan hozhat létre Azure Adatkezelő-fürtöt és-adatbázist a Python használatával.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: e3f58e596db26c04a8f3be4f87eb129fadf5e328
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141737"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Azure Adatkezelő-fürt és-adatbázis létrehozása a Python használatával

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Parancssori felület](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet többek között alkalmazások, webhelyek és IoT-eszközök nagy mennyiségű adatfolyamain. Az Azure Adatkezelő használatához először létre kell hoznia egy fürtöt, és létre kell hoznia egy vagy több adatbázist a fürtben. Ezután betöltheti az adatterhelést egy adatbázisba, így lekérdezéseket futtathat. Ebben a cikkben egy fürtöt és egy adatbázist hoz létre a Python használatával.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="install-python-package"></a>Python-csomag telepítése

Az Azure Adatkezelő (Kusto) Python-csomagjának telepítéséhez nyisson meg egy parancssort, amely a Python elérési útját is tartalmazni fogja. Futtassa ezt a parancsot:

```
pip install azure-mgmt-kusto
```

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Adatkezelő-fürt létrehozása

1. Hozza létre a fürtöt a következő parancs használatával:

    ```Python
    from azure.mgmt.kusto.kusto_management_client import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku

    credentials = xxxxxxxxxxxxxxx
    
    subscription_id = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx'
    location = 'Central US'
    sku = 'D13_v2'
    capacity = 5
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku, capacity=capacity))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | cluster_name | *mykustocluster* | A fürt kívánt neve.|
   | sku | *D13_v2* | A fürthöz használni kívánt SKU. |
   | resource_group_name | *testrg* | Az erőforráscsoport neve, amelyben a fürt létre lesz hozva. |

    További választható paramétereket is használhat, például a fürt kapacitását.
    
1. [ *Hitelesítő adatok* beállítása](/azure/python/python-sdk-azure-authenticate)

1. A következő parancs futtatásával győződjön meg arról, hogy a fürt létrehozása sikeres volt-e:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Ha az eredmény tartalmazza `provisioningState` az `Succeeded` értéket, a fürt létrehozása sikeresen megtörtént.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Adatkezelő-fürtben

1. Hozza létre az adatbázist a következő parancs használatával:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = Database(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

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

## <a name="next-steps"></a>További lépések

* [Adatbevitel az Azure Adatkezelő Python Library használatával](python-ingest-data.md)
