---
title: 'Ügyfél által kezelt kulcsok konfigurálása C használatával #'
description: Ez a cikk ismerteti, hogyan konfigurálhatja az ügyfél által felügyelt kulcsok titkosítását az adatok az Azure Data Explorerben.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a00b0876c4a188b932032129ed5a394e94198930
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297947"
---
# <a name="configure-customer-managed-keys-using-c"></a>Ügyfél által kezelt kulcsok konfigurálása C használatával #

> [!div class="op_single_selector"]
> * [Portál](customer-managed-keys-portal.md)
> * [C #](customer-managed-keys-csharp.md)
> * [Azure Resource Manager-sablon](customer-managed-keys-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](../../includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Titkosítás konfigurálása ügyfél által kezelt kulcsokkal

Ez a szakasz bemutatja, hogyan konfigurálhatja az ügyfél által felügyelt kulcsok titkosítását az Azure Data Explorer C# ügyfél használatával. 

### <a name="prerequisites"></a>Előfeltételek

* Ha nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition alkalmazást.](https://www.visualstudio.com/downloads/) Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

### <a name="install-c-nuget"></a>C# NuGet telepítése

* Telepítse az [Azure Data Explorer (Kusto) NuGet csomagot.](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)

* Telepítse a [Microsoft.IdentityModel.Clients.ActiveDirectory NuGet csomagot](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) hitelesítésre.

### <a name="authentication"></a>Hitelesítés

A jelen cikkben szereplő példák futtatásához [hozzon létre egy Azure AD-alkalmazást](/azure/active-directory/develop/howto-create-service-principal-portal) és egyszerű szolgáltatást, amely hozzáférhet az erőforrásokhoz. A szerepkör-hozzárendelést hozzáadhatja az előfizetés `Directory (tenant) ID` `Application ID`hatóköréhez, és lekaphatja a szükséges , és `Client Secret`a.

### <a name="configure-cluster"></a>Fürt konfigurálása

Alapértelmezés szerint az Azure Data Explorer titkosítás a Microsoft által felügyelt kulcsokat használja. Konfigurálja az Azure Data Explorer-fürtöt az ügyfél által kezelt kulcsok használatára, és adja meg a fürthöz társítandó kulcsot.

1. Frissítse a fürtöt a következő kóddal:

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
    var keyName = "myKey";
    var keyVersion = "5b52b20e8d8a42e6bd7527211ae32654";
    var keyVaultUri = "https://mykeyvault.vault.azure.net/";
    var keyVaultProperties = new KeyVaultProperties (keyName, keyVersion, keyVaultUri);
    var clusterUpdate = new ClusterUpdate(keyVaultProperties: keyVaultProperties);
    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdate);
    ```

1. Futtassa a következő parancsot annak ellenőrzéséhez, hogy a fürt frissítése sikeresen megtörtént-e:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Ha az `ProvisioningState` eredmény `Succeeded` tartalmazza az értéket, majd a fürt frissítése sikeresen megtörtént.

## <a name="update-the-key-version"></a>A kulcsverzió frissítése

Amikor egy kulcs új verzióját hozza létre, frissítenie kell a fürtöt az új verzió használatához. Először `Get-AzKeyVaultKey` hívja a kulcs legújabb verzióját. Ezután frissítse a fürt key vault tulajdonságainak használatát a kulcs új verziójának használatához, ahogy az a [Fürt konfigurálása](#configure-cluster)című részben látható.

## <a name="next-steps"></a>További lépések

* [Biztonságos Azure Data Explorer-fürtök az Azure-ban](security.md)
* [Felügyelt identitások konfigurálása az Azure Data Explorer-fürthöz](managed-identities.md)
* [Biztonságossá a fürt az Azure Data Explorer – Azure Portal](manage-cluster-security.md) in in-in titkosítás engedélyezésével.
* [Ügyfél által kezelt kulcsok konfigurálása az Azure Resource Manager sablon használatával](customer-managed-keys-resource-manager.md)


