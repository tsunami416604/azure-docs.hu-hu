---
title: Ügyfél által felügyelt kulcsok konfigurálása a használatávalC#
description: Ez a cikk azt ismerteti, hogyan konfigurálható az ügyfelek által felügyelt kulcsok titkosítása az Azure Adatkezelőban tárolt adataihoz.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: ac37c0e1f1f7d769a881c5965744b29d40468486
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276686"
---
# <a name="configure-customer-managed-keys-using-c"></a>Ügyfél által felügyelt kulcsok konfigurálása a használatávalC#

> [!div class="op_single_selector"]
> * [C#](create-cluster-database-csharp.md)
> * [Azure Resource Manager-sablon](create-cluster-database-resource-manager.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](../../includes/data-explorer-configure-customer-managed-keys.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>Titkosítás konfigurálása az ügyfél által felügyelt kulcsokkal

Ez a szakasz bemutatja, hogyan konfigurálhatja az ügyfél által felügyelt kulcsok titkosítását C# az Azure adatkezelő-ügyfél használatával. 

### <a name="prerequisites"></a>Előfeltételek

* Ha nincs telepítve a Visual Studio 2019, letöltheti és használhatja az **ingyenes** [Visual Studio 2019 Community Edition verziót](https://www.visualstudio.com/downloads/). Ügyeljen arra, hogy engedélyezze az **Azure Development** használatát a Visual Studio telepítése során.

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

### <a name="install-c-nuget"></a>A C# NuGet telepítése

* Telepítse az [Azure adatkezelő (Kusto) NuGet-csomagot](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Telepítse a [Microsoft. IdentityModel. clients. ActiveDirectory NuGet-csomagot](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) a hitelesítéshez.

### <a name="authentication"></a>Hitelesítés

A cikkben szereplő példák futtatásához [hozzon létre egy Azure ad-alkalmazást és egy](/azure/active-directory/develop/howto-create-service-principal-portal) egyszerű szolgáltatásnevet, amely hozzáférhet az erőforrásokhoz. Szerepkör-hozzárendelést az előfizetés hatókörében adhat hozzá, és lekérheti a szükséges `Directory (tenant) ID`, `Application ID`és `Client Secret`.

### <a name="configure-cluster"></a>Fürt konfigurálása

Alapértelmezés szerint az Azure Adatkezelő encryption a Microsoft által felügyelt kulcsokat használja. Konfigurálja az Azure Adatkezelő-fürtöt az ügyfél által felügyelt kulcsok használatára, és adja meg a fürthöz társítandó kulcsot.

1. Frissítse a fürtöt a következő kód használatával:

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

1. A következő parancs futtatásával ellenőrizze, hogy a fürt frissítése sikeresen megtörtént-e:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Ha az eredmény `ProvisioningState`t tartalmaz a `Succeeded` értékkel, akkor a fürt frissítése sikeresen megtörtént.

## <a name="update-the-key-version"></a>A kulcs verziójának frissítése

A kulcsok új verziójának létrehozásakor frissítenie kell a fürtöt az új verzió használatára. Először hívja meg `Get-AzKeyVaultKey` a kulcs legújabb verziójának beszerzéséhez. Ezután frissítse a fürt kulcstartójának tulajdonságait úgy, hogy az a kulcs új verzióját használja, ahogy azt a [fürt konfigurálása](#configure-cluster)című rész mutatja.

## <a name="next-steps"></a>Következő lépések

* [Azure Adatkezelő-fürtök védelme az Azure-ban](security.md)
* [Felügyelt identitások konfigurálása az Azure Adatkezelő-fürthöz](managed-identities.md)
* [A fürt biztonságossá tétele az Azure adatkezelő-Azure Portalban](manage-cluster-security.md) a titkosítás nyugalmi állapotban való engedélyezésével.
* [Az ügyfél által felügyelt kulcsok konfigurálása a Azure Resource Manager sablon használatával](customer-managed-keys-resource-manager.md)


