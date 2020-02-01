---
title: Azure Adatkezelő-fürt és-adatbázis létrehozása a használatávalC#
description: Ismerje meg, hogyan hozhat létre Azure Adatkezelő-fürtöt és-adatbázist a használatávalC#
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 2d800dc401b0d85b26a71817a1a70d66539203ae
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902124"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Azure Adatkezelő-fürt és-adatbázis létrehozása a használatávalC#

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Parancssori felület](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Azure Resource Manager-sablon](create-cluster-database-resource-manager.md)

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet alkalmazások, webhelyek, IoT-eszközök és egyebek nagy mennyiségű adatfolyamain. Az Azure Adatkezelő használatához először létre kell hoznia egy fürtöt, és létre kell hoznia egy vagy több adatbázist a fürtben. Ezután betöltheti az adatterhelést egy adatbázisba, így lekérdezéseket futtathat. Ebben a cikkben egy fürtöt és egy adatbázist hoz létre a használatával C#.

## <a name="prerequisites"></a>Előfeltételek

* Ha nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition verziót](https://www.visualstudio.com/downloads/). Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.
* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="install-c-nuget"></a>A C# Nuget telepítése

* Telepítse az [Azure adatkezelő (Kusto) nuget-csomagot](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Telepítse a [Microsoft. IdentityModel. clients. ActiveDirectory nuget-csomagot](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) a hitelesítéshez.

## <a name="authentication"></a>Hitelesítés
A cikkben szereplő példák futtatásához szükség van egy Azure AD-alkalmazásra és egy egyszerű szolgáltatásra, amely hozzáférhet az erőforrásokhoz. Az Azure ad- [alkalmazás](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) létrehozásával hozzon létre egy ingyenes Azure ad-alkalmazást, és adja hozzá a szerepkör-hozzárendelést az előfizetési hatókörhöz. Azt is bemutatja, hogyan kérhető le a `Directory (tenant) ID`, a `Application ID`és a `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Adatkezelő-fürt létrehozása

1. Hozza létre a fürtöt a következő kód használatával:

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
   | skuName | *Standard_D13_v2* | A fürthöz használni kívánt SKU. |
   | tier | *Standard* | Az SKU-szintet. |
   | capacity | *száma* | A fürt példányainak száma. |
   | resourceGroupName | *testrg* | Az erőforráscsoport neve, amelyben a fürt létre lesz hozva. |

    > [!NOTE]
    > A **fürt létrehozása** hosszú ideig futó művelet, ezért erősen ajánlott a CreateOrUpdateAsync használata a CreateOrUpdate helyett. 

1. A következő parancs futtatásával győződjön meg arról, hogy a fürt létrehozása sikeres volt-e:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Ha az eredmény `ProvisioningState`t tartalmaz a `Succeeded` értékkel, akkor a fürt létrehozása sikeresen megtörtént.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Adatkezelő-fürtben

1. Hozza létre az adatbázist a következő kód használatával:

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
   | clusterName | *mykustocluster* | Annak a fürtnek a neve, ahová az adatbázist létre kívánja hozni.|
   | databaseName | *mykustodatabase* | Az adatbázis neve.|
   | resourceGroupName | *testrg* | Az erőforráscsoport neve, amelyben a fürt létre lesz hozva. |
   | SoftDeletePeriod | *3650:00:00:00* | Az az időtartam, ameddig az adat a lekérdezés számára elérhető marad. |
   | HotCachePeriod | *3650:00:00:00* | Az az időtartam, ameddig az adat a gyorsítótárban lesz tárolva. |

2. Futtassa a következő parancsot a létrehozott adatbázis megtekintéséhez:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName) as ReadWriteDatabase;
    ```

Most már rendelkezik egy fürttel és egy adatbázissal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

* Ha azt tervezi, hogy követi a többi cikket, tartsa meg a létrehozott erőforrásokat.
* Az erőforrások törléséhez törölje a fürtöt. Ha töröl egy fürtöt, az az összes adatbázisát is törli. A fürt törléséhez használja a következő parancsot:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Következő lépések

* [Adatbevitel az Azure Adatkezelő .NET Standard SDK-val (előzetes verzió)](net-standard-ingest-data.md)
