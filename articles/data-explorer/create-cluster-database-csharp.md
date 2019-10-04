---
title: Azure Adatkezelő-fürt és-adatbázis létrehozása a használatávalC#
description: Ismerje meg, hogyan hozhat létre Azure Adatkezelő-fürtöt és-adatbázist a használatávalC#
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 4a3f37c232fcd7a0fcbdac051ed36916ef5c2868
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326668"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Azure Adatkezelő-fürt és-adatbázis létrehozása a használatávalC#

> [!div class="op_single_selector"]
> * [Portál](create-cluster-database-portal.md)
> * [Parancssori felület](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [ARM-sablon](create-cluster-database-resource-manager.md)

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet többek között alkalmazások, webhelyek és IoT-eszközök nagy mennyiségű adatfolyamain. Az Azure Adatkezelő használatához először létre kell hoznia egy fürtöt, és létre kell hoznia egy vagy több adatbázist a fürtben. Ezután betöltheti az adatterhelést egy adatbázisba, így lekérdezéseket futtathat. Ebben a cikkben egy fürtöt és egy adatbázist hoz létre a használatával C#.

## <a name="prerequisites"></a>Előfeltételek

* Ha nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition verziót](https://www.visualstudio.com/downloads/). Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="install-c-nuget"></a>A C# Nuget telepítése

1. Telepítse az [Azure adatkezelő (Kusto) nuget-csomagot](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

1. Telepítse a [Microsoft. IdentityModel. clients. ActiveDirectory nuget-csomagot](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) a hitelesítéshez.

## <a name="create-the-azure-data-explorer-cluster"></a>Az Azure Adatkezelő-fürt létrehozása

1. Hozza létre a fürtöt a következő kód használatával:

    ```csharp
    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var sku = new AzureSku("D13_v2", 5);
    var cluster = new Cluster(location, sku);

    var authenticationContext = new AuthenticationContext("https://login.windows.net/{tenantName}");
    var credential = new ClientCredential(clientId: "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx", clientSecret: "xxxxxxxxxxxxxx");
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    };

    kustoManagementClient.Clusters.CreateOrUpdate(resourceGroupName, clusterName, cluster);
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | clusterName | *mykustocluster* | A fürt kívánt neve.|
   | sku | *D13_v2* | A fürthöz használni kívánt SKU. |
   | resourceGroupName | *testrg* | Az erőforráscsoport neve, amelyben a fürt létre lesz hozva. |

    További választható paramétereket is használhat, például a fürt kapacitását.

1. [Hitelesítő adatok](https://docs.microsoft.com/dotnet/azure/dotnet-sdk-azure-authenticate?view=azure-dotnet) beállítása

1. A következő parancs futtatásával győződjön meg arról, hogy a fürt létrehozása sikeres volt-e:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Ha az eredmény tartalmazza `ProvisioningState` az `Succeeded` értéket, a fürt létrehozása sikeresen megtörtént.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Az adatbázis létrehozása az Azure Adatkezelő-fürtben

1. Hozza létre az adatbázist a következő kód használatával:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new Database(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    kustoManagementClient.Databases.CreateOrUpdate(resourceGroupName, clusterName, databaseName, database);
    ```

   |**Beállítás** | **Ajánlott érték** | **Mező leírása**|
   |---|---|---|
   | clusterName | *mykustocluster* | Annak a fürtnek a neve, ahová az adatbázist létre kívánja hozni.|
   | databaseName | *mykustodatabase* | Az adatbázis neve.|
   | resourceGroupName | *testrg* | Az erőforráscsoport neve, amelyben a fürt létre lesz hozva. |
   | softDeletePeriod | *3650:00:00:00* | Az az időtartam, ameddig az adat a lekérdezés számára elérhető marad. |
   | hotCachePeriod | *3650:00:00:00* | Az az időtartam, ameddig az adat a gyorsítótárban lesz tárolva. |

2. Futtassa a következő parancsot a létrehozott adatbázis megtekintéséhez:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName);
    ```

Most már rendelkezik egy fürttel és egy adatbázissal.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

* Ha azt tervezi, hogy követi a többi cikket, tartsa meg a létrehozott erőforrásokat.
* Az erőforrások törléséhez törölje a fürtöt. Ha töröl egy fürtöt, az az összes adatbázisát is törli. A fürt törléséhez használja a következő parancsot:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>További lépések

* [Adatbevitel az Azure Adatkezelő .NET Standard SDK-val (előzetes verzió)](net-standard-ingest-data.md)
