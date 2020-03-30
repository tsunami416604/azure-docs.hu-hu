---
title: 'Azure Data Explorer-fürt & adatbázis létrehozása a C használatával #'
description: 'Ismerje meg, hogyan hozhat létre Azure Data Explorer-fürtöt és -adatbázist a C használatával #'
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 0c32d438ac8551f061343edb747e9fc035b498e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246408"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Azure Data Explorer-fürt és-adatbázis létrehozása c használatával #

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [parancssori felület](create-cluster-database-cli.md)
> * [Powershell](create-cluster-database-powershell.md)
> * [C #](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Azure Resource Manager-sablon](create-cluster-database-resource-manager.md)

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet többek között alkalmazások, webhelyek és IoT-eszközök nagy mennyiségű adatfolyamain. Az Azure Data Explorer használatához először egy fürtöt hozunk létre, majd egy vagy több adatbázist a fürtben. Ezután adatokat töltünk be az adatbázisba, hogy lekérdezéseket futtathassunk rajta. Ebben a cikkben a C# használatával hozhat létre fürtöt és adatbázist.

## <a name="prerequisites"></a>Előfeltételek

* Ha nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition alkalmazást.](https://www.visualstudio.com/downloads/) Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.
* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

## <a name="authentication"></a>Hitelesítés
A jelen cikkben szereplő példák futtatásához szükségünk van egy Azure AD-alkalmazásra és egyszerű szolgáltatásra, amely képes hozzáférni az erőforrásokhoz. Ellenőrizze [az Azure AD-alkalmazást](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) egy ingyenes Azure AD-alkalmazás létrehozásához, és adja hozzá a szerepkör-hozzárendelést az előfizetéshatókörben. Azt is bemutatja, `Directory (tenant) ID` `Application ID`hogyan `Client Secret`juthat el a , és .

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Data Explorer-fürt létrehozása

1. Hozza létre a fürtöt a következő kód dal:

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
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var cluster = new Cluster(location, sku);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | clusterName | *mykustocluster* | A fürt kívánt neve.|
   | skuName | *Standard_D13_v2* | A fürthöz használt termékváltozat. |
   | tier | *Standard* | A Termékváltozat szintje. |
   | capacity | *szám* | A fürt példányainak száma. |
   | resourceGroupName | *testrg* | Az erőforráscsoport neve, ahol a fürt létrejön. |

    > [!NOTE]
    > **Hozzon létre egy fürtegy** hosszú ideig futó művelet, ezért erősen ajánlott a CreateOrUpdate, ahelyett, CreateOrUpdate. 

1. Futtassa a következő parancsot annak ellenőrzéséhez, hogy a fürt sikeresen létrejött-e:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Ha az `ProvisioningState` eredmény `Succeeded` tartalmazza az értéket, akkor a fürt sikeresen létrejött.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Data Explorer-fürtben

1. Hozza létre az adatbázist a következő kód dal:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new ReadWriteDatabase(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

        [!NOTE]
        If you are using C# version 2.0.0 or below, use Database instead of ReadWriteDatabase.

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | clusterName | *mykustocluster* | Annak a fürtnek a neve, amelyben az adatbázis létrejön.|
   | adatbázisneve | *mykustoadatbázis* | Az adatbázis neve.|
   | resourceGroupName | *testrg* | Az erőforráscsoport neve, ahol a fürt létrejön. |
   | softDeletePeriod | *3650:00:00:00* | Az az idő, amerre az adatok lekérdezhetők. |
   | hotCachePeriod | *3650:00:00:00* | Az az idő, amerre az adatok a gyorsítótárban maradnak. |

2. A létrehozott adatbázis megtekintéséhez futtassa a következő parancsot:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName) as ReadWriteDatabase;
    ```

Most már rendelkezik egy fürttel és egy adatbázissal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

* Ha azt tervezi, hogy követi a többi cikket, tartsa meg a létrehozott erőforrásokat.
* Erőforrások törléséhez törölje a fürtöt. Fürt törlésekor az összes benne lévő adatbázist is törli. A fürt törléséhez használja a következő parancsot:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>További lépések

* [Adatok betöltése az Azure Data Explorer .NET Standard SDK (előzetes verzió) használatával](net-standard-ingest-data.md)
