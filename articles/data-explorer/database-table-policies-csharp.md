---
title: Szabályzatok létrehozása az Azure Adatkezelő C# SDK használatával
description: Ebből a cikkből megtudhatja, hogyan hozhat létre házirendeket C#a használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 17312840b0081056ad04723f2b2c241c47902021
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667293"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>Adatbázis-és táblázat-szabályzatok létrehozása az Azure Adatkezelőhoz a használatávalC#

> [!div class="op_single_selector"]
> * [C#](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Az Azure Data Explorer egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Ebben a cikkben az Azure Adatkezelő adatbázis-és táblázat-házirendjeit fogja létrehozni a C#használatával.

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio 2019. Ha nem rendelkezik a Visual Studio 2019-rel, letöltheti és használhatja az *ingyenes* [visual Studio Community 2019](https://www.visualstudio.com/downloads/)-et. Ügyeljen arra, hogy a Visual Studio telepítése során válassza az **Azure-fejlesztés** lehetőséget.
* Azure-előfizetés. Ha szüksége van a szolgáltatásra, a Kezdés előtt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) .
* [Egy tesztelési fürt és adatbázis](create-cluster-database-csharp.md).
* [Egy teszt tábla](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

## <a name="install-c-nuget"></a>A C# NuGet telepítése

* Telepítse az [Azure adatkezelő (Kusto) NuGet-csomagot](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Telepítse a [Microsoft. Azure. Kusto. NETStandard NuGet-csomagot](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/). (Nem kötelező a táblázatos házirendek módosításához)
* Telepítse a [Microsoft. IdentityModel. clients. ActiveDirectory NuGet-csomagot](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)a hitelesítéshez.

## <a name="authentication"></a>Hitelesítés
A cikkben szereplő példák futtatásához szüksége van egy Azure Active Directory (Azure AD) alkalmazásra és egyszerű szolgáltatásra, amely hozzáférhet az erőforrásokhoz. Ugyanazzal az Azure AD-alkalmazással is használhatja a hitelesítést [egy tesztelési fürtből és adatbázisból](create-cluster-database-csharp.md#authentication). Ha másik Azure AD-alkalmazást szeretne használni, tekintse meg az [Azure ad](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) -alkalmazás létrehozása az ingyenes Azure ad-alkalmazás létrehozásához és a szerepkör-hozzárendelés hozzáadása az előfizetési hatókörben című témakört. A cikk azt is bemutatja, hogyan kérhető le a `Directory (tenant) ID`, a `Application ID`és a `Client secret`. Előfordulhat, hogy az új Azure AD-alkalmazást az adatbázis rendszerbiztonsági tagjának kell felvennie. További információ: az [Azure adatkezelő Database engedélyeinek kezelése](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions).

## <a name="alter-database-retention-policy"></a>Adatbázis adatmegőrzési szabályzatának módosítása
Az adatmegőrzési szabályzatot 10 napos törlési időszakra állítja be.
    
```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(softDeletePeriod: TimeSpan.FromDays(10)));
```

## <a name="alter-database-cache-policy"></a>Az adatbázis-gyorsítótárazási szabályzat módosítása
Beállítja az adatbázishoz tartozó gyorsítótár-házirendet. Az előző öt nap a fürt SSD-lemezén fog megjelenni.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(hotCachePeriod: TimeSpan.FromDays(5)));
```

## <a name="alter-table-cache-policy"></a>A tábla gyorsítótár-házirendjének módosítása
Beállítja a tábla gyorsítótár-szabályzatát. Az előző öt nap a fürt SSD-lemezén fog megjelenni.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var databaseName = "<DatabaseName>";
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var tableName = "<TableName>"

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = databaseName,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    //dataHotSpan and indexHotSpan should have the same value
    var hotSpan = TimeSpan.FromDays(5);
    var command1 = CslCommandGenerator.GenerateAlterTableCachingPolicyCommand(tableName: tableName,
                    dataHotSpan: hotSpan, indexHotSpan: hotSpan);

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="add-a-new-principal-for-the-database"></a>Új rendszerbiztonsági tag hozzáadása az adatbázishoz
Új Azure AD-alkalmazás létrehozása rendszergazdai rendszerbiztonsági tagként az adatbázishoz.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var clientIdToAdd = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.AddPrincipalsAsync(resourceGroupName, clusterName, databaseName,
                new DatabasePrincipalListRequest()
                {
                    Value = new List<DatabasePrincipal>()
                    {
                        new DatabasePrincipal("Admin", "<database_principle_name>", "App", appId: clientIdToAdd, tenantName:tenantId)
                    }
                });
```
## <a name="next-steps"></a>Következő lépések

* [További információ az adatbázis-és táblázat-házirendekről](https://docs.microsoft.com/azure/kusto/management/policies)
