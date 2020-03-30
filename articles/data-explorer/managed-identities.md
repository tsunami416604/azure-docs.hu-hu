---
title: Felügyelt identitások konfigurálása az Azure Data Explorer-fürthöz
description: Ismerje meg, hogyan konfigurálhatja a felügyelt identitásokat az Azure Data Explorer-fürthöz.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f9592f5d2666684e0cf5eef687b1e69cfb55066c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065560"
---
# <a name="configure-managed-identities-for-your-azure-data-explorer-cluster"></a>Felügyelt identitások konfigurálása az Azure Data Explorer-fürthöz

Az [Azure Active Directory felügyelt identitása](/azure/active-directory/managed-identities-azure-resources/overview) lehetővé teszi, hogy a fürt könnyen hozzáférjen más AAD-védelemmel ellátott erőforrásokhoz, például az Azure Key Vaulthoz. Az identitást az Azure platform kezeli, és nem követeli meg, hogy kiépítse vagy elforgassa a titkos kulcsokat. Ez a cikk bemutatja, hogyan hozhat létre felügyelt identitást az Azure Data Explorer-fürtökhöz. A felügyelt identitáskonfiguráció jelenleg csak a [fürt ügyfél által felügyelt kulcsainak engedélyezéséhez](/azure/data-explorer/security#customer-managed-keys-with-azure-key-vault)támogatott.

> [!Note]
> Felügyelt identitások az Azure Data Explorer nem fog megfelelően viselkednek, ha az alkalmazás áttelepítése az előfizetések vagy a bérlők között. Az alkalmazásnak új identitást kell beszereznie, amely a funkció [letiltásával](#remove-a-system-assigned-identity) és [újbóli engedélyezésével végezhető](#add-a-system-assigned-identity) el. Az alsóbb rétegbeli erőforrások hozzáférési házirendjeit is frissíteni kell az új identitás használatához.

## <a name="add-a-system-assigned-identity"></a>Rendszerhez rendelt identitás hozzáadása
                                                                                                    
Rendeljen hozzá egy rendszeráltal hozzárendelt identitást, amely a fürthöz van kötve, és törlődik, ha a fürt törlődik. Egy fürt csak egy rendszerhez rendelt identitással rendelkezhet. A rendszer által hozzárendelt identitással rendelkező fürt létrehozásához egy további tulajdonságot kell beállítani a fürtön. A rendszer által hozzárendelt identitás c#, ARM-sablonok vagy az Azure Portal használatával kerül hozzáadásra az alábbiakban részletezett módon.

# <a name="azure-portal"></a>[Azure-portál](#tab/portal)

### <a name="add-a-system-assigned-identity-using-the-azure-portal"></a>Rendszeráltal hozzárendelt identitás hozzáadása az Azure Portalon

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

#### <a name="new-azure-data-explorer-cluster"></a>Új Azure Data Explorer-fürt

1. [Azure Data Explorer-fürt létrehozása](/azure/data-explorer/create-cluster-database-portal#create-a-cluster) 
1. A **Biztonság** lapon > **Rendszer hozzárendelt identitása**lapon válassza **a Be**lehetőséget. A rendszer hozzárendelt identitásának eltávolításához válassza a **Ki**lehetőséget.
2. **Válassza a Tovább:Címkék>** vagy a Véleményezés + **létrehozás** lehetőséget a fürt létrehozásához.

    ![Rendszerhez rendelt identitás hozzáadása új fürthöz](media/managed-identities/system-assigned-identity-new-cluster.png)

#### <a name="existing-azure-data-explorer-cluster"></a>Meglévő Azure Data Explorer-fürt

1. Nyisson meg egy meglévő Azure Data Explorer-fürtöt.
1. Válassza a **Beállítások** > **identitás a** portál bal oldali ablaktáblájában lehetőséget.
1. Az **Identitás** ablaktáblán > **Rendszer hozzárendelt** lapján:
   1. Az **Állapot** csúszka áthelyezése **be állásba.**
   1. **Mentés** kiválasztása
   1. Az előugró ablakban válassza az **Igen** lehetőséget.

    ![Rendszerhez rendelt identitás hozzáadása](media/managed-identities/turn-system-assigned-identity-on.png)

1. Néhány perc múlva a képernyőn látható: 
  * **Objektumazonosító** – ügyfél által kezelt kulcsokhoz használatos 
  * **Szerepkör-hozzárendelések** – kattintson a hivatkozásra a megfelelő szerepkörök hozzárendeléséhez

    ![Rendszerhez rendelt identitás](media/managed-identities/system-assigned-identity-on.png)

# <a name="c"></a>[C #](#tab/c-sharp)

### <a name="add-a-system-assigned-identity-using-c"></a>Rendszeráltal hozzárendelt identitás hozzáadása C használatával #

#### <a name="prerequisites"></a>Előfeltételek

Felügyelt identitás beállítása az Azure Data Explorer C# ügyfélhasználatával:

* Telepítse az [Azure Data Explorer (Kusto) NuGet csomagot.](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)
* Telepítse a [Microsoft.IdentityModel.Clients.ActiveDirectory NuGet csomagot](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) hitelesítésre.
* [Hozzon létre egy Azure AD-alkalmazást](/azure/active-directory/develop/howto-create-service-principal-portal) és egyszerű szolgáltatást, amely képes hozzáférni az erőforrásokhoz. A szerepkör-hozzárendelést az előfizetés hatóköréhez `Application ID`adja `Client Secret`hozzá, és megkapja a szükséges `Directory (tenant) ID`, és a.

#### <a name="create-or-update-your-cluster"></a>A fürt létrehozása vagy frissítése

1. Hozza létre vagy frissítse `Identity` a fürtöt a tulajdonság használatával:

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
    
2. Futtassa a következő parancsot annak ellenőrzéséhez, hogy a fürt sikeresen létrejött-e vagy frissített-e identitással:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    Ha az `ProvisioningState` eredmény `Succeeded` tartalmazza az értéket, akkor a fürt jött létre vagy frissített, és rendelkeznie kell a következő tulajdonságokkal:

    ```csharp
    var principalId = cluster.Identity.PrincipalId;
    var tenantId = cluster.Identity.TenantId;
    ```

`PrincipalId`guid-okkal. `TenantId` A `TenantId` tulajdonság azonosítja azt az AAD-bérlőt, amelyhez az identitás tartozik. Az `PrincipalId` a fürt új identitásának egyedi azonosítója. Az AAD-n belül az egyszerű szolgáltatás ugyanaz a név, amelyet az App Service- vagy az Azure Functions-példánynak adott.

# <a name="arm-template"></a>[ARM sablon](#tab/arm)

### <a name="add-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Rendszeráltal kijelölt identitás hozzáadása Azure Resource Manager-sablon használatával

Az Azure Resource Manager-sablon segítségével automatizálható az Azure-erőforrások üzembe helyezésének. Ha többet szeretne tudni az Azure Data Explorerben való üzembe helyezésről, olvassa [el az Azure Data Explorer-fürt és-adatbázis létrehozása Azure Resource Manager-sablon használatával című témakört.](create-cluster-database-resource-manager.md)

A rendszer által hozzárendelt típus hozzáadása lekéri az Azure-t, hogy hozza létre és kezelje a fürt identitását. Bármilyen típusú `Microsoft.Kusto/clusters` erőforrás létrehozható identitással, ha a következő tulajdonságot is beírja az erőforrás-definícióba: 

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

A fürt létrehozásakor a következő további tulajdonságokkal rendelkezik:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`<TENANTID>`guid-okkal. `<PRINCIPALID>` A `TenantId` tulajdonság azonosítja azt az AAD-bérlőt, amelyhez az identitás tartozik. Az `PrincipalId` a fürt új identitásának egyedi azonosítója. Az AAD-n belül az egyszerű szolgáltatás ugyanaz a név, amelyet az App Service- vagy az Azure Functions-példánynak adott.

---

## <a name="remove-a-system-assigned-identity"></a>Rendszeráltal hozzárendelt identitás eltávolítása

A rendszer által hozzárendelt identitás eltávolítása az AAD-ből is törlődik. A rendszer által hozzárendelt identitások is automatikusan törlődnek az AAD-ből a fürterőforrás törlésekor. A rendszer által hozzárendelt identitás a szolgáltatás letiltásával távolítható el.  A rendszer által hozzárendelt identitás eltávolítása C#, ARM-sablonok vagy az Azure Portal használatával az alábbiakban részletezett használatával.

# <a name="azure-portal"></a>[Azure-portál](#tab/portal)

### <a name="remove-a-system-assigned-identity-using-the-azure-portal"></a>Rendszeráltal hozzárendelt identitás eltávolítása az Azure Portalon

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. Válassza a **Beállítások** > **identitás a** portál bal oldali ablaktáblájában lehetőséget.
1. Az **Identitás** ablaktáblán > **Rendszer hozzárendelt** lapján:
    1. Mozgassa az **Állapot** csúszkát **Ki**állásba.
    1. **Mentés** kiválasztása
    1. Az előugró ablakban válassza az **Igen** lehetőséget a rendszerhez rendelt identitás letiltásához. Az **Identitás** ablaktábla visszaáll ugyanarra a feltételre, mint a rendszer által hozzárendelt identitás hozzáadása előtt.

    ![A rendszer hezrendelt identitása ki van kapcsolva](media/managed-identities/system-assigned-identity.png)

# <a name="c"></a>[C #](#tab/c-sharp)

### <a name="remove-a-system-assigned-identity-using-c"></a>Rendszeráltal hozzárendelt identitás eltávolítása C használatával #

A rendszer által hozzárendelt identitás eltávolításához futtassa az alábbi parancsot:

```csharp
var identity = new Identity(IdentityType.None);
var cluster = new Cluster(location, sku, identity: identity);
await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
```

# <a name="arm-template"></a>[ARM sablon](#tab/arm)

### <a name="remove-a-system-assigned-identity-using-an-azure-resource-manager-template"></a>Rendszeráltal kijelölt identitás eltávolítása Azure Resource Manager-sablon használatával

A rendszer által hozzárendelt identitás eltávolításához futtassa az alábbi parancsot:

```json
"identity": {
    "type": "None"
}
```

---

## <a name="next-steps"></a>További lépések

* [Biztonságos Azure Data Explorer-fürtök az Azure-ban](security.md)
* [Biztonságossá a fürt az Azure Data Explorer – Azure Portal](manage-cluster-security.md) in in-in titkosítás engedélyezésével.
 * [Ügyfél által kezelt kulcsok konfigurálása C használatával #](customer-managed-keys-csharp.md)
 * [Ügyfél által kezelt kulcsok konfigurálása az Azure Resource Manager sablon használatával](customer-managed-keys-resource-manager.md)
