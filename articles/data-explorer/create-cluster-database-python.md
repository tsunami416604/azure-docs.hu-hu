---
title: 'Gyors útmutató: Egy Azure Data Explorer fürt és adatbázis létrehozása a Python használatával'
description: Ismerje meg, hogyan hozhat létre egy Azure Data Explorer fürt és az adatbázis a Python használatával
services: data-explorer
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/17/2019
ms.openlocfilehash: 4f87c5996ea323c26c32c1680ba6f627bf8f95c2
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287526"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Egy Azure Data Explorer fürt és adatbázis létrehozása a Python használatával

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Parancssori felület](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Ez a rövid útmutató ismerteti, hogyan hozhat létre Azure Data Explorer fürt és adatbázis Python használatával.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez szüksége lesz egy Azure-előfizetésre. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.

## <a name="install-python-package"></a>Python-csomag telepítése

Az Azure Data Explorer (Kusto) a Python-csomag telepítéséhez nyisson meg egy parancssort, amelynek az elérési út Python, és futtassa ezt a parancsot:

```
pip install azure-mgmt-kusto
```

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Data Explorer-fürt létrehozása

1. A fürt létrehozásához a következő paranccsal:

    

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | cluster_name | *mykustocluster* | A fürt kívánt nevét.|
   | termékváltozat | *D13_v2* | A Termékváltozat a fürthöz használt. |
   | resource_group_name | *testrg* | Az erőforrás csoport neve, ahol a fürt létrejön. |

    Nincsenek további nem kötelező paraméterek, amelyet használhat, például a fürt kapacitásának.
    
    A hitelesítő adatok beállítása a "hitelesítő adatok" (További információ: https://docs.microsoft.com/python/azure/python-sdk-azure-authenticate?view=azure-python )

2. A következő paranccsal ellenőrizze, hogy a fürt létrehozása sikeresen megtörtént:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Ha az eredmény tartalmazza `provisioningState` együtt a `Succeeded` érték, akkor a fürt sikeresen létrejött.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Data Explorer fürtben

1. Az adatbázis létrehozása a következő paranccsal:

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
   | cluster_name | *mykustocluster* | A fürt, ahol létrejön az adatbázis neve.|
   | database_name | *mykustodatabase* | Az adatbázis neve.|
   | resource_group_name | *testrg* | Az erőforrás csoport neve, ahol a fürt létrejön. |
   | soft_delete_period | *3650 nap 0:00:00* | Mennyi ideig megtartott adatok lekérdezhetők. |
   | hot_cache_period | *3650 nap 0:00:00* | Mennyi ideig megtartott adatok a gyorsítótárban. |

2. A következő paranccsal tekintse meg az Ön által létrehozott adatbázist:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

Most már egy fürt és a egy adatbázist.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

* Ha el szeretné végezni a többi rövid útmutatót és oktatóanyagot, őrizze meg a létrehozott erőforrásokat.
* Erőforrások törléséhez törölje a fürtöt. Ha töröl egy fürtöt, benne az adatbázisokat is törli. A következő paranccsal törölje a fürtöt:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Rövid útmutató: Az Azure Data Explorer Python-kódtár használata az adatok betöltése](python-ingest-data.md)
