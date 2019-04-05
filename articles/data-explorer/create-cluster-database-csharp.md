---
title: 'Gyors útmutató: Hozzon létre egy Azure Data Explorer fürt és -adatbázis használatávalC#'
description: Ismerje meg, hogyan hozhat létre Azure Data Explorer fürt és -adatbázis használatával aC#
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: quickstart
ms.date: 03/25/2019
ms.openlocfilehash: 1489048619fa8d650b30659a00ae7679c8d5109d
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59050696"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Hozzon létre egy Azure Data Explorer fürt és -adatbázis használatávalC#

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [parancssori felület](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet többek között alkalmazások, webhelyek és IoT-eszközök nagy mennyiségű adatfolyamain. Az Azure Data Explorer használatához, először hozzon létre egy fürtöt, és az adott fürt egy vagy több adatbázis létrehozása. Ezután (betöltés) adatokat az adatbázisba fogadására, így vonatkozóan, lekérdezéseket futtathat. Ebben a rövid útmutatóban létrehozhat egy fürtöt, és a egy adatbázis használatával C#.

## <a name="prerequisites"></a>Előfeltételek

* Ha még nincs telepítve a Visual Studio 2017, letöltheti és használhatja a **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="install-c-nuget"></a>Telepítés C# nuget

1. Telepítse a [Azure Data Explorer (Kusto) nuget-csomag](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

1. Telepítse a [Microsoft.IdentityModel.Clients.ActiveDirectory nuget-csomag](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) a hitelesítéshez.

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Data Explorer-fürt létrehozása

1. A fürt létrehozása a következő kód használatával:

    ```C#-interactive
    string resourceGroupName = "testrg";    
    string clusterName = "mykustocluster";
    string location = "Central US";
    AzureSku sku = new AzureSku("D13_v2", 5);
    Cluster cluster = new Cluster(location, sku);
    
    ar authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantName}");
    var credential = new ClientCredential(clientId: "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx", clientSecret: "xxxxxxxxxxxxxx");
    var result = authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential).Result;
    
    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);
     
    KustoManagementClient KustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    };

    KustoManagementClient.Clusters.CreateOrUpdate(resourceGroupName, clusterName, cluster);
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | clusterName | *mykustocluster* | A fürt kívánt nevét.|
   | termékváltozat | *D13_v2* | A Termékváltozat a fürthöz használt. |
   | resourceGroupName | *testrg* | Az erőforrás csoport neve, ahol a fürt létrejön. |

    Nincsenek további nem kötelező paraméterek, amelyet használhat, például a fürt kapacitásának.

1. Állítsa be [a hitelesítő adatok](https://docs.microsoft.com/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet)

1. A következő paranccsal ellenőrizze, hogy a fürt létrehozása sikeresen megtörtént:

    ```C#-interactive
    KustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Ha az eredmény tartalmazza `ProvisioningState` együtt a `Succeeded` érték, akkor a fürt sikeresen létrejött.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Data Explorer fürtben

1. Az adatbázis létrehozása a következő kód használatával:

    ```c#-interactive
    TimeSpan hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    TimeSpan softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    string databaseName = "mykustodatabase";
    Database database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);
    
    KustoManagementClient.Databases.CreateOrUpdate(resourceGroupName, clusterName, databaseName, database);
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | clusterName | *mykustocluster* | A fürt, ahol létrejön az adatbázis neve.|
   | databaseName | *mykustodatabase* | Az adatbázis neve.|
   | resourceGroupName | *testrg* | Az erőforrás csoport neve, ahol a fürt létrejön. |
   | softDeletePeriod | *3650:00:00:00* | Mennyi ideig megtartott adatok lekérdezhetők. |
   | hotCachePeriod | *3650:00:00:00* | Mennyi ideig megtartott adatok a gyorsítótárban. |

2. A következő paranccsal tekintse meg az Ön által létrehozott adatbázist:

    ```c#-interactive
    KustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

Most már egy fürt és a egy adatbázist.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

* Ha el szeretné végezni a többi rövid útmutatót és oktatóanyagot, őrizze meg a létrehozott erőforrásokat.
* Erőforrások törléséhez törölje a fürtöt. Ha töröl egy fürtöt, benne az adatbázisokat is törli. A következő paranccsal törölje a fürtöt:

    ```C#-interactive
    KustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyors útmutató: Adatokat az Azure SDK-val Data Explorer .NET Standard (előzetes verzió)](net-standard-ingest-data.md)
