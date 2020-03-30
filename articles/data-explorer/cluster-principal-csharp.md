---
title: 'Fürtrendszeri tagok hozzáadása az Azure Data Explorer hez a C használatával #'
description: Ebben a cikkben megtudhatja, hogyan adhat hozzá fürtrendszeri tagokat az Azure Data Explorer c#használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: e6c3970890dfe2c669dee1acf631e9dd45ab1085
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965059"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-c"></a>Fürtrendszeri tagok hozzáadása az Azure Data Explorer hez a C használatával #

> [!div class="op_single_selector"]
> * [C #](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Azure Resource Manager-sablon](cluster-principal-resource-manager.md)

Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Ebben a cikkben a C# használatával adja hozzá az Azure Data Explorer fürtrendszeri tagokat.

## <a name="prerequisites"></a>Előfeltételek

* Ha nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition alkalmazást.](https://www.visualstudio.com/downloads/) Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.
* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Fürt létrehozása](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>C# NuGet telepítése

* Telepítse a [Microsoft.Azure.Management.kusto alkalmazást.](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)
* Telepítse a [Microsoft.Rest.ClientRuntime.Azure.Authentication hitelesítést](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) a hitelesítéshez.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Egyszerű fürt hozzáadása

A következő példa bemutatja, hogyan adhat hozzá egy fürtfőt programozott módon.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster that is created as part of the Prerequisites
var clusterName = "mykustocluster";
string principalAssignmentName = "clusterPrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "AllDatabasesAdmin";//AllDatabasesAdmin or AllDatabasesViewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var clusterPrincipalAssignment = new ClusterPrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.ClusterPrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, principalAssignmentName, clusterPrincipalAssignment);
```

|**Beállítás** | **Ajánlott érték** | **Mező leírása**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A bérlőazonosítója. Más néven könyvtárazonosító.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Az erőforrás-létrehozáshoz használt előfizetés-azonosító.|
| ügyfél-azonosító | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Az alkalmazás ügyfélazonosítója, amely hozzáférhet a bérlő erőforrásaihoz.|
| ügyféltitkos | *xxxxxxxxxxxxxx* | Az alkalmazás ügyféltka-tka, amely hozzáférhet a bérlő erőforrásaihoz. |
| resourceGroupName | *testrg* | A fürtöt tartalmazó erőforráscsoport neve.|
| clusterName | *mykustocluster* | A fürt neve.|
| igazgatóhelyettesassignmentname | *clusterPrincipalAssignment1* | A fürt fő erőforrásának neve.|
| principalId között | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A fő azonosító, amely lehet felhasználói e-mail, alkalmazásazonosító vagy biztonsági csoport neve.|
| Szerepet | *Mindenadatbázis-kezelő* | A fürtfőnév szerepköre, amely lehet "AllDatabasesAdmin" vagy "AllDatabasesViewer".|
| tenantIdForPrincipal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | A főnév bérlői azonosítója.|
| principalType típus | *Alkalmazás* | A megbízó típusa, amely lehet "Felhasználó", "Alkalmazás" vagy "Csoport"|

## <a name="next-steps"></a>További lépések

* [Adatbázis-biztonsági tagok hozzáadása](database-principal-csharp.md)
