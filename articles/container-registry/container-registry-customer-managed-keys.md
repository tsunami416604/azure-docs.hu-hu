---
title: Titkosítás az ügyfelek által felügyelt kulcsokkal
description: Ismerje meg az Azure Container Registry titkosítását, valamint azt, hogyan titkosíthatja a beállításjegyzéket a Azure Key Vaultban tárolt ügyfél által felügyelt kulccsal.
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: 2d5561998cf0b19698c8059a861a4014a171a7e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81461752"
---
# <a name="encryption-using-customer-managed-keys"></a>Titkosítás az ügyfél által felügyelt kulcsokkal

Ha lemezképeket és egyéb összetevőket tárol egy Azure Container registryben, az Azure automatikusan titkosítja a beállításjegyzék tartalmát a [szolgáltatás által felügyelt kulcsokkal](../security/fundamentals/encryption-atrest.md#data-encryption-models). Egy további titkosítási réteggel kiegészítheti az alapértelmezett titkosítást a Azure Key Vault-ben létrehozott és kezelt kulcs használatával. Ez a cikk végigvezeti az Azure CLI és a Azure Portal használatának lépésein.

Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítást a [Azure Key Vault](../key-vault/general/overview.md)integrációja támogatja. Létrehozhatja saját titkosítási kulcsait, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-jait a titkosítási kulcsok létrehozásához. A Azure Key Vault használatával is naplózhatja a kulcshasználat.

Ez a funkció a **prémium** szintű Container Registry szolgáltatási szinten érhető el. További információ a beállításjegyzék szolgáltatási szintjeiről és korlátairól: [Azure Container Registry SKU](container-registry-skus.md)-i.

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások](#preview-limitations) érvényesek. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.
>
   
## <a name="preview-limitations"></a>Előzetes verzió korlátozásai 

* Jelenleg csak a beállításjegyzék létrehozásakor engedélyezheti ezt a funkciót.
* Az ügyfél által felügyelt kulcs beállításjegyzékbe való engedélyezése után nem lehet letiltani.
* A [tartalom megbízhatósága](container-registry-content-trust.md) jelenleg nem támogatott az ügyfél által felügyelt kulccsal titkosított beállításjegyzékben.
* Az ügyfél által felügyelt kulccsal titkosított beállításjegyzékben az [ACR-feladatokhoz](container-registry-tasks-overview.md) tartozó naplókat jelenleg csak 24 óráig őrzi meg a rendszer. Ha hosszabb ideig kell megőriznie a naplókat, tekintse meg a [feladat-futtatási naplók exportálásával és tárolásával](container-registry-tasks-logs.md#alternative-log-storage)kapcsolatos útmutatót.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő Azure CLI-lépések használatához az Azure CLI-es vagy újabb verziójára lesz szüksége. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Ügyfél által felügyelt kulcs engedélyezése – parancssori felület

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Szükség esetén futtassa az az [Group Create][az-group-create] parancsot egy erőforráscsoport létrehozásához a kulcstartó, a tároló-beállításjegyzék és az egyéb szükséges erőforrások létrehozásához.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt [felügyelt identitást az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md) az az [Identity Create][az-identity-create] paranccsal. Ezt az identitást a beállításjegyzék fogja használni a Key Vault szolgáltatás eléréséhez.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

A parancs kimenetében jegyezze fel a következő értékeket: `id` és. `principalId` Ezekre az értékekre szüksége lesz a beállításjegyzék hozzáférésének a Key vaulthoz való konfigurálásának későbbi lépéseiben.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

A kényelem érdekében tárolja ezeket az értékeket környezeti változókban:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Hozzon létre egy Key vaultot az az Key Vault [létrehozásával][az-keyvault-create] , amely az ügyfél által felügyelt kulcsot tárolja a beállításjegyzék titkosításához. 

Ha meg szeretné akadályozni, hogy a véletlen kulcs vagy a kulcstartó törlése okozza az adatvesztést, akkor a következő beállításokat kell engedélyeznie: **Soft delete** and **Purge Protection**. Az alábbi példa paramétereket tartalmaz a következő beállításokhoz: 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Key Vault hozzáférési szabályzat hozzáadása

Konfiguráljon egy házirendet a Key vaulthoz, hogy az identitás hozzáférhessen. A következő az a kulcstartó [set-Policy][az-keyvault-set-policy] paranccsal megadhatja a korábban létrehozott felügyelt identitás elsődleges azonosítóját, amelyet előzőleg egy környezeti változóban tároltak. Adja meg a **Get**, a **UnwrapKey**és a **wrapKey**kulcs engedélyeit.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Kulcs létrehozása és a kulcs AZONOSÍTÓjának beolvasása

Futtassa az az kulcstartó [kulcs létrehozása][az-keyvault-key-create] parancsot a Key vaultban lévő kulcs létrehozásához.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

A parancs kimenetében jegyezze fel a kulcs AZONOSÍTÓját `kid`. Ezt az azonosítót a következő lépésben kell használni:

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/xxxxxxxxxxxxxxxxxxxxxxxx",
    "kty": "RSA",
[...]
```
A kényelem érdekében tárolja ezt az értéket egy környezeti változóban:

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Beállításjegyzék létrehozása az ügyfél által felügyelt kulccsal

Futtassa az az [ACR Create][az-acr-create] parancsot egy beállításjegyzék létrehozásához és az ügyfél által felügyelt kulcs engedélyezéséhez. Adja át a felügyelt identitás résztvevő-AZONOSÍTÓját és a korábban a környezeti változókban tárolt kulcs AZONOSÍTÓját:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Titkosítási állapot megjelenítése

Annak megjelenítéséhez, hogy engedélyezve van-e az ügyfél által felügyelt kulccsal rendelkező beállításjegyzék-titkosítás, futtassa az az [ACR encryption show][az-acr-encryption-show] parancsot:

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>Ügyfél által felügyelt kulcs engedélyezése – portál

### <a name="create-a-managed-identity"></a>Felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt [felügyelt identitást az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md) a Azure Portal. A lépéseket a [felhasználó által hozzárendelt identitás létrehozása](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)című témakörben tekintheti meg.

Jegyezze fel a felügyelt identitás **erőforrásának nevét** . Ezt a nevet a későbbi lépésekben kell megadnia.

![Felhasználó által hozzárendelt felügyelt identitás létrehozása a Azure Portalban](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

A kulcstartó létrehozásának lépéseiért lásd [: gyors üzembe helyezés és a Azure Key Vault titkos kód beolvasása a Azure Portal használatával](../key-vault/secrets/quick-create-portal.md).

Amikor az ügyfél által felügyelt kulcshoz kulcstartót hoz létre, az **alapvető** beállítások lapon engedélyeznie kell a következő védelmi beállításokat: **Soft delete** és **Purge Protection**. Ezek a beállítások segítenek megakadályozni az adatvesztést a véletlen kulcs vagy a kulcstartó törlése miatt.

![Key Vault létrehozása a Azure Portalban](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Key Vault hozzáférési szabályzat hozzáadása

Konfiguráljon egy házirendet a Key vaulthoz, hogy az identitás hozzáférhessen.

1. Navigáljon a kulcstartóhoz.
1. Válassza a **Beállítások** > **hozzáférési szabályzatok > + hozzáférési házirend hozzáadása**lehetőséget.
1. Válassza a **kulcs engedélyei**lehetőséget, majd a **beolvasás**, a **kicsomagolás** **és a**kicsomagolási kulcs elemet.
1. Válassza a **rendszerbiztonsági tag** kiválasztása lehetőséget, és válassza ki a felhasználó által hozzárendelt felügyelt identitás erőforrásának nevét.  
1. Válassza a **Hozzáadás**, majd a **Mentés**lehetőséget.

![Key Vault hozzáférési szabályzat létrehozása](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Kulcs létrehozása

1. Navigáljon a kulcstartóhoz.
1. Válassza a **Beállítások** > **kulcsok**elemet.
1. Válassza a **+ Létrehozás/importálás** lehetőséget, és adjon meg egy egyedi nevet a kulcshoz.
1. Fogadja el a többi alapértelmezett értéket, és válassza a **Létrehozás**lehetőséget.
1. A létrehozás után válassza ki a kulcsot, és jegyezze fel az aktuális kulcs verzióját.

### <a name="create-azure-container-registry"></a>Azure Container Registry létrehozása

1. Válassza **az erőforrás** > **létrehozása tárolók** > **Container Registry**elemet.
1. Az **alapvető beállítások** lapon válasszon ki vagy hozzon létre egy erőforráscsoportot, és adjon meg egy beállításjegyzék-nevet. Az **SKU**területen válassza a **prémium**lehetőséget.
1. A **titkosítás** lap **ügyfél által felügyelt kulcs**területén válassza az **engedélyezve**lehetőséget.
1. Az **identitás**területen válassza ki a létrehozott felügyelt identitást.
1. A **titkosítási kulcs**területen válassza **a kiválasztás Key Vault**lehetőséget.
1. A **válasszon kulcsot Azure Key Vault** ablakban válassza ki az előző szakaszban létrehozott kulcstárolót, kulcsot és verziót.
1. A **titkosítás** lapon válassza a **felülvizsgálat + létrehozás**elemet.
1. Válassza a **Létrehozás** lehetőséget a beállításjegyzék-példány telepítéséhez.

![Tároló-beállításjegyzék létrehozása a Azure Portal](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Ha szeretné megtekinteni a beállításjegyzék titkosítási állapotát a portálon, keresse meg a beállításjegyzéket. A **Beállítások**területen válassza a **titkosítás (előzetes verzió)** lehetőséget.

## <a name="enable-customer-managed-key---template"></a>Ügyfél által felügyelt kulcs – sablon engedélyezése

Egy Resource Manager-sablonnal is létrehozhat egy beállításjegyzéket, és engedélyezheti a titkosítást az ügyfél által felügyelt kulccsal. 

A következő sablon létrehoz egy új tároló-beállításjegyzéket és egy felhasználó által hozzárendelt felügyelt identitást. Másolja az alábbi tartalmat egy új fájlba, és mentse a fájlt egy olyan fájlnév használatával `CMKtemplate.json`, mint például.

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}

```

Kövesse az előző szakaszokban ismertetett lépéseket a következő erőforrások létrehozásához:

* Key Vault, név alapján azonosítva
* Key Vault-kulcs, kulcs azonosítója alapján azonosítva

Futtassa az alábbi az [Group Deployment Create][az-group-deployment-create] parancsot a beállításjegyzék előző sablonfájl használatával történő létrehozásához. Ha meg van jelölve, adja meg az új beállításjegyzék-nevet és a felügyelt identitás nevét, valamint a létrehozott kulcstároló nevét és AZONOSÍTÓját. 

```bash
az group deployment create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Titkosítási állapot megjelenítése

A beállításjegyzék-titkosítás állapotának megjelenítéséhez futtassa az [az ACR encryption show-status] [az-ACR-encryption-show-status] parancsot:

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>A beállításjegyzék használata

Miután engedélyezte a beállításjegyzék számára az adatok titkosítását az ügyfél által felügyelt kulcs használatával, ugyanazokat a beállításjegyzékbeli műveleteket hajthatja végre, amelyeket olyan beállításjegyzékben hajt végre, amely nem titkosított ügyfél által felügyelt kulccsal. Például a hitelesítést a beállításjegyzékben és a Docker-rendszerképek leküldésekor végezheti el. Lásd: példák a [leküldéses és a lekérési](container-registry-get-started-docker-cli.md)parancsokra.

## <a name="rotate-key"></a>Elforgatási kulcs

A megfelelőségi szabályzatok alapján Azure Key Vault elforgathatja az ügyfél által felügyelt kulcsot. Hozzon létre egy új kulcsot, majd frissítse a beállításjegyzéket az adattitkosításhoz az új kulccsal. Ezeket a lépéseket az Azure CLI használatával vagy a portálon végezheti el.

Például futtassa az az [kulcstartó kulcs létrehozása][az-keyvault-key-create] parancsot egy új kulcs létrehozásához:

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

Ezután futtassa az az [ACR encryption forgatni-Key][az-acr-encryption-rotate-key] parancsot, amely átadja az új kulcs azonosítóját és a korábban konfigurált felügyelt identitás elsődleges azonosítóját:

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>Kulcs visszavonása

A Key Vault hozzáférési házirendjének módosításával vagy a kulcs törlésével vonja vissza az ügyfél által felügyelt titkosítási kulcsot. Például az az Key [Vault delete-Policy][az-keyvault-delete-policy] paranccsal módosíthatja a beállításjegyzék által használt felügyelt identitás hozzáférési házirendjét. Például:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

A kulcs visszavonása gyakorlatilag blokkolja az összes beállításjegyzék-adattal való hozzáférést, mivel a beállításjegyzék nem fér hozzá a titkosítási kulcshoz. Ha a kulcshoz való hozzáférés engedélyezve van, vagy a törölt kulcs vissza lett állítva, a beállításjegyzékben meg kell választania a kulcsot, hogy újra hozzáférhessen a titkosított beállításjegyzék-adataihoz.

## <a name="next-steps"></a>További lépések

* További információ [Az Azure-](../security/fundamentals/encryption-atrest.md)beli inaktív adatok titkosításáról.
* További információ a hozzáférési házirendekről és a [kulcstartóhoz való hozzáférés biztonságossá](../key-vault/general/secure-your-key-vault.md)tételéről.
* Ha visszajelzést szeretne küldeni a Azure Container Registry ügyfél által felügyelt kulcsairól, látogasson el az [ACR GitHub webhelyére](https://aka.ms/acr/issues).


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
