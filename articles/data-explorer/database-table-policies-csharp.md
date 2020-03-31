---
title: Szabályzatok létrehozása az Azure Data Explorer C# SDK használatával
description: Ebből a cikkből megtudhatja, hogyan hozhat létre házirendeket a C#használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 17312840b0081056ad04723f2b2c241c47902021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667293"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>Adatbázis- és táblaházirendek létrehozása az Azure Data Explorer számára a C használatával #

> [!div class="op_single_selector"]
> * [C #](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Ebben a cikkben a C# használatával hoz létre adatbázis- és táblaszabályzatokat az Azure Data Explorerhez.

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio 2019. Ha nem rendelkezik Visual Studio 2019-el, letöltheti és használhatja az *ingyenes* [Visual Studio Community 2019-et.](https://www.visualstudio.com/downloads/) Ügyeljen arra, hogy válassza ki az **Azure-fejlesztés** a Visual Studio beállítása során.
* Azure-előfizetés. Ha szükséges, a kezdés előtt létrehozhat egy [ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)
* [Tesztfürt és adatbázis](create-cluster-database-csharp.md).
* [Egy teszttábla](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

## <a name="install-c-nuget"></a>C# NuGet telepítése

* Telepítse az [Azure Data Explorer (Kusto) NuGet csomagot.](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)
* Telepítse a [Microsoft.Azure.Kusto.Data.NETStandard NuGet csomagot.](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/) (Nem kötelező, táblaházirendek módosításához.)
* Telepítse a [Microsoft.IdentityModel.Clients.ActiveDirectory NuGet csomagot](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)hitelesítéshez.

## <a name="authentication"></a>Hitelesítés
A jelen cikkben szereplő példák futtatásához szüksége van egy Azure Active Directory (Azure AD) alkalmazásra és egyszerű szolgáltatásra, amely képes hozzáférni az erőforrásokhoz. Használhatja ugyanazt az Azure AD-alkalmazást a [tesztfürtből és adatbázisból](create-cluster-database-csharp.md#authentication)történő hitelesítéshez. Ha egy másik Azure AD-alkalmazást szeretne használni, tekintse [meg egy Azure AD-alkalmazás létrehozása](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) egy ingyenes Azure AD-alkalmazás létrehozásához és szerepkör-hozzárendelés hozzáadásához az előfizetéshatókörben. Ez a cikk azt `Directory (tenant) ID`is `Application ID`bemutatja, hogyan szerezheti be a , és `Client secret`a. Előfordulhat, hogy hozzá kell adnia az új Azure AD-alkalmazást az adatbázis ban. További információt az [Azure Data Explorer adatbázis-engedélyei kezelésé](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)című témakörben talál.

## <a name="alter-database-retention-policy"></a>Adatbázis-adatmegőrzési házirend módosítása
10 napos helyreállítható törlési időszakkal rendelkező adatmegőrzési házirendet állít be.
    
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

## <a name="alter-database-cache-policy"></a>Adatbázis-gyorsítótárházirend módosítása
Beállítja az adatbázis gyorsítótár-házirendjeit. Az előző öt nap az adatok lesznek a fürt SSD.

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

## <a name="alter-table-cache-policy"></a>Táblagyorsítótár-házirend módosítása
Gyorsítótár-házirendet állít be a táblához. Az előző öt nap az adatok lesznek a fürt SSD.

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

## <a name="add-a-new-principal-for-the-database"></a>Új egyszerű felhasználó hozzáadása az adatbázishoz
Új Azure AD-alkalmazást ad hozzá az adatbázis felügyeleti főkezelőjeként.

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
## <a name="next-steps"></a>További lépések

* [További információ az adatbázis- és táblaházirendekről](https://docs.microsoft.com/azure/kusto/management/policies)
