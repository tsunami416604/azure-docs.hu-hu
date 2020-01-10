---
title: Felügyelt identitások konfigurálása az Azure Adatkezelő-fürthöz
description: Ismerje meg, hogyan konfigurálhatja a felügyelt identitásokat az Azure Adatkezelő-fürthöz.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 3b0bb62de4a96a45d607e05b32a87feec692e4d4
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725974"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Felügyelt identitások konfigurálása az Azure Adatkezelő-fürthöz

[Azure Active Directory felügyelt identitás](/azure/active-directory/managed-identities-azure-resources/overview) lehetővé teszi, hogy a fürt könnyedén HOZZÁFÉRHESSEN más HRE-védelemmel ellátott erőforrásokhoz, például a Azure Key Vaulthoz. Az identitást az Azure platform kezeli, és nincs szükség titkos kódok kiépítésére vagy elforgatására. Ez a cikk bemutatja, hogyan hozhat létre felügyelt identitást az Azure Adatkezelő-fürtökhöz. 

> [!Note]
> Az Azure Adatkezelő felügyelt identitásai nem a várt módon fognak működni, ha az alkalmazást áttelepítik az előfizetések vagy a bérlők között. Az alkalmazásnak új identitást kell beszereznie, amely a szolgáltatás [identitásának eltávolításával](#remove-an-identity)és újbóli engedélyezésével végezhető el. Az alsóbb rétegbeli erőforrások hozzáférési házirendjeit is frissíteni kell az új identitás használatára.

## <a name="add-a-system-assigned-identity"></a>Rendszerhez rendelt identitás hozzáadása

A fürthöz hozzárendelhető egy, a fürthöz kötött rendszerszintű identitás, amelyet a **rendszer** töröl, ha a fürt törölve lett. Egy fürthöz csak egy rendszer által hozzárendelt identitás tartozhat. A rendszer által hozzárendelt identitású fürtök létrehozásához további tulajdonságot kell beállítani a fürtön.

### <a name="add-a-system-assigned-identity-using-c"></a>Rendszerhez rendelt identitás hozzáadása aC#

Felügyelt identitás Azure Adatkezelő C# -ügyfél használatával történő beállításához tegye a következőket:

* Telepítse az [Azure adatkezelő (Kusto) NuGet-csomagot](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Telepítse a [Microsoft. IdentityModel. clients. ActiveDirectory NuGet-csomagot](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) a hitelesítéshez.
* A következő példa futtatásához [hozzon létre egy Azure ad-alkalmazást és egy](/azure/active-directory/develop/howto-create-service-principal-portal) egyszerű szolgáltatásnevet, amely hozzáférhet az erőforrásokhoz. Szerepkör-hozzárendelést az előfizetés hatókörében adhat hozzá, és lekérheti a szükséges `Directory (tenant) ID`, `Application ID`és `Client Secret`.

#### <a name="create-or-update-your-cluster"></a>Fürt létrehozása vagy frissítése

1. Hozza létre vagy frissítse a fürtöt a `Identity` tulajdonság használatával:

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
    var identity = new Identity(IdentityType.SystemAssigned);
    var cluster = new Cluster(location, sku, identity: identity);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```
    
2. A következő parancs futtatásával ellenőrizze, hogy a fürt sikeresen létrejött vagy frissült-e a személyazonossággal:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Ha az eredmény `ProvisioningState`t tartalmaz a `Succeeded` értékkel, akkor a fürt létrehozása vagy frissítése megtörtént, és a következő tulajdonságokkal kell rendelkeznie:
   
    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

    a `PrincipalId` és `TenantId` GUID azonosítókkal lesznek lecserélve. A `TenantId` tulajdonság azonosítja azt a HRE-bérlőt, amelyhez az identitás tartozik. A `PrincipalId` a fürt új identitásának egyedi azonosítója. A HRE belül az egyszerű szolgáltatásnév neve megegyezik a App Service vagy Azure Functions példányával.

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Rendszerhez rendelt identitás hozzáadása Azure Resource Manager sablon használatával

Az Azure-erőforrások üzembe helyezésének automatizálásához Azure Resource Manager sablon használható. Ha többet szeretne megtudni az Azure Adatkezelő üzembe helyezéséről, tekintse meg [az azure adatkezelő-fürt és-adatbázis létrehozása Azure Resource Manager sablon használatával](create-cluster-database-resource-manager.md)című témakört.

A rendszer által hozzárendelt típus hozzáadásával közli az Azure-t, hogy létrehozza és felügyelje a fürt identitását. Minden `Microsoft.Kusto/clusters` típusú erőforrást identitással lehet létrehozni az erőforrás-definícióban a következő tulajdonsággal együtt: 

```json
"identity": {
    "type": "SystemAssigned"
}    
```

Példa:

```json
{
    "apiVersion": "2019-09-07",
    "type": "Microsoft.Kusto/clusters",
    "name": "[variables('clusterName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "trustedExternalTenants": [],
        "virtualNetworkConfiguration": null,
        "optimizedAutoscale": null,
        "enableDiskEncryption": false,
        "enableStreamingIngest": false,
    }
}
```

A fürt létrehozásakor a következő tulajdonságokkal rendelkezik:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

a `<TENANTID>` és `<PRINCIPALID>` GUID azonosítókkal lesznek lecserélve. A `TenantId` tulajdonság azonosítja azt a HRE-bérlőt, amelyhez az identitás tartozik. A `PrincipalId` a fürt új identitásának egyedi azonosítója. A HRE belül az egyszerű szolgáltatásnév neve megegyezik a App Service vagy Azure Functions példányával.

## <a name="remove-an-identity"></a>Identitás eltávolítása

A rendszer által hozzárendelt identitások eltávolítása a HRE-ből is törölve lesz. A rendszer által hozzárendelt identitások is automatikusan törlődnek a HRE-ből, ha a fürterőforrás törölve lett. A szolgáltatás letiltásával a rendszer által hozzárendelt identitások eltávolíthatók:

```json
"identity": {
    "type": "None"
}
```

## <a name="next-steps"></a>Következő lépések

* [Azure Adatkezelő-fürtök védelme az Azure-ban](security.md)
* [A fürt biztonságossá tétele az Azure adatkezelő-Azure Portalban](manage-cluster-security.md) a titkosítás nyugalmi állapotban való engedélyezésével.
 * [Ügyfél által felügyelt kulcsok konfigurálása a használatávalC#](customer-managed-keys-csharp.md)
 * [Az ügyfél által felügyelt kulcsok konfigurálása a Azure Resource Manager sablon használatával](customer-managed-keys-resource-manager.md)
