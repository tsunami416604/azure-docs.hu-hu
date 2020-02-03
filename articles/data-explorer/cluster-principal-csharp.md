---
title: Fürthöz tartozó rendszerbiztonsági tag hozzáadása az Azure Adatkezelőhoz a használatávalC#
description: Ebből a cikkből megtudhatja, hogyan adhat hozzá fürtöket az Azure Adatkezelőhoz C#a használatával.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: e6c3970890dfe2c669dee1acf631e9dd45ab1085
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965059"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-c"></a>Fürthöz tartozó rendszerbiztonsági tag hozzáadása az Azure Adatkezelőhoz a használatávalC#

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Azure Resource Manager-sablon](cluster-principal-resource-manager.md)

Az Azure Data Explorer egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Ebben a cikkben a használatával C#adja hozzá a fürtöket az Azure Adatkezelőhoz.

## <a name="prerequisites"></a>Előfeltételek

* Ha nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition verziót](https://www.visualstudio.com/downloads/). Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.
* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* [Hozzon létre egy fürtöt](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>A C# NuGet telepítése

* Telepítse a [Microsoft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Telepítse a [Microsoft. Rest. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) hitelesítést a hitelesítéshez.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Fürthöz tartozó rendszerbiztonsági tag hozzáadása

Az alábbi példa azt szemlélteti, hogyan adhat hozzá programozott módon egy fürtöt.

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
| tenantId | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | A bérlő azonosítója. Más néven címtár-azonosító.|
| subscriptionId | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | Az erőforrás-létrehozáshoz használt előfizetés-azonosító.|
| clientId | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | Annak az alkalmazásnak az ügyfél-azonosítója, amely hozzáférhet a bérlő erőforrásaihoz.|
| clientSecret | *XXXXXXXXXXXXXX* | Az alkalmazás ügyfél-titka, amely hozzáférhet a bérlő erőforrásaihoz. |
| resourceGroupName | *testrg* | A fürtöt tartalmazó erőforráscsoport neve.|
| clusterName | *mykustocluster* | A fürt neve.|
| principalAssignmentName | *clusterPrincipalAssignment1* | A fürt elsődleges erőforrásának neve.|
| principalId | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | A résztvevő azonosítója, amely lehet felhasználói e-mail-cím, alkalmazás-azonosító vagy biztonsági csoport neve.|
| szerepkör | *AllDatabasesAdmin* | A fürt elsődleges szerepköre, amely lehet "AllDatabasesAdmin' vagy" AllDatabasesViewer ".|
| tenantIdForPrincipal | *XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX* | A rendszerbiztonsági tag bérlői azonosítója.|
| principalType | *Alkalmazás* | A rendszerbiztonsági tag típusa, amely lehet "user", "app" vagy "Group"|

## <a name="next-steps"></a>Következő lépések

* [Adatbázis-rendszerbiztonsági tag hozzáadása](database-principal-csharp.md)
