---
title: Inaktív titkosítás az ügyfél által felügyelt kulcsokkal
description: Ismerje meg az Azure-tároló beállításjegyzékének titkosítását, és a rendszerleíró adatbázis titkosítását az Azure Key Vaultban tárolt ügyfél által felügyelt kulccsal
ms.topic: article
ms.date: 03/10/2020
ms.custom: ''
ms.openlocfilehash: fe0736f83db2ba5b872d50bcf1262ca423de9f09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498945"
---
# <a name="encryption-using-customer-managed-keys"></a>Titkosítás ügyfél által kezelt kulcsok használatával

Ha lemezképeket és egyéb összetevőket tárol egy [Azure-tároló](../security/fundamentals/encryption-atrest.md#data-encryption-models)beállításjegyzékében, az Azure szolgáltatás által felügyelt kulcsokkal automatikusan titkosítja a beállításjegyzék tartalmát. Az alapértelmezett titkosítást kiegészítheti egy további titkosítási réteggel az Azure Key Vaultban létrehozott és felügyelt kulcs használatával. Ez a cikk bemutatja az Azure CLI és az Azure Portal lépéseit.

Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítást az [Azure Key Vaultszolgáltatással](../key-vault/key-vault-overview.md)való integráció támogatja. Létrehozhat saját titkosítási kulcsokat, és tárolhatja őket egy key vaultban, vagy használhatja az Azure Key Vault API-kat titkosítási kulcsok létrehozásához. Az Azure Key Vault segítségével is naplózhatja a kulcshasználatot.

Ez a funkció a **prémium szintű** tároló beállításjegyzék-szolgáltatásszintjén érhető el. A beállításjegyzék-szolgáltatási szintekről és -korlátozásokról az [Azure Container Registry ska](container-registry-skus.md)című témakörben talál további információt.

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások](#preview-limitations) érvényesek. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.
>
   
## <a name="preview-limitations"></a>Előnézeti korlátozások 

* Ezt a szolgáltatást jelenleg csak beállításjegyzék létrehozásakor engedélyezheti.
* Miután engedélyezte az ügyfél által felügyelt kulcsot a beállításjegyzékben, nem tilthatja le.
* [A tartalommegbízhatóság](container-registry-content-trust.md) jelenleg nem támogatott az ügyfél által felügyelt kulccsal titkosított beállításjegyzékben.
* Az ügyfél által felügyelt kulccsal titkosított beállításjegyzékben az [ACR-feladatok](container-registry-tasks-overview.md) futtatási naplói jelenleg csak 24 órán keresztül maradnak meg. Ha hosszabb ideig kell megőriznie a naplókat, olvassa el a [feladatfuttatási naplók exportálására és tárolására vonatkozó útmutatást.](container-registry-tasks-logs.md#alternative-log-storage)

## <a name="prerequisites"></a>Előfeltételek

Az Azure CLI ebben a cikkben leírt lépések használatához az Azure CLI 2.2.0-s vagy újabb verziójára van szükség. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Ügyfél által kezelt kulcs engedélyezése - CLI

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Ha szükséges, futtassa az [az csoport létrehozása][az-group-create] parancsot a key vault, a tároló beállításjegyzékés más szükséges erőforrások létrehozásához szükséges erőforráscsoport létrehozásához.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az [Azure-erőforrások hoz](../active-directory/managed-identities-azure-resources/overview.md) létre az az identitás [létrehozása][az-identity-create] paranccsal. Ezt az identitást a beállításjegyzék fogja használni a Key Vault szolgáltatás eléréséhez.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name> 
```

A parancs kimenetében vegye figyelembe a `id` `principalId`következő értékeket: és . Ezekre az értékekre későbbi lépésekben szükség van a rendszerleíró adatbázis hozzáférésének konfigurálásához a key vaulthoz.

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

A kényelem érdekében ezeket az értékeket környezeti változókban tárolja:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Hozzon létre egy key vault [létrehozása][az-keyvault-create] az ügyfél által felügyelt kulcs tárolására beállításjegyzék-titkosítást. 

A véletlen kulcs- vagy kulcstároló-törlések által okozott adatvesztés elkerülése érdekében a következő beállításokat kell engedélyeznie: **Ideiglenes törlés** és **Törlés védelem**. A következő példa a következő beállítások paramétereit tartalmazza: 

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-soft-delete \
  --enable-purge-protection
```

### <a name="add-key-vault-access-policy"></a>Kulcstartó hozzáférési házirendjének hozzáadása

Konfigurálja a key vault házirendjét, hogy az identitás hozzáférhessen. A következő [az keyvault set-policy][az-keyvault-set-policy] parancs, adja át a felügyelt identitás, amely létrehozott, korábban tárolt egy környezeti változó. Kulcsengedélyek **beírásának**, **kicsomagolásikulcsésának**és **burkolókulcsának**beállítása.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey 
```

### <a name="create-key-and-get-key-id"></a>Kulcs létrehozása és kulcsazonosító beírása

Futtassa az [az keyvault key create][az-keyvault-key-create] parancsot egy kulcs létrehozásához a key vaultban.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

A parancs kimenetében vegye figyelembe a kulcs `kid`azonosítóját, . Ezt az azonosítót a következő lépésben használhatja:

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
A kényelem érdekében ezt az értéket egy környezeti változóban tárolja:

```azurecli
keyID=$(az keyvault key show --name <keyname> --vault-name <key-vault-name> --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Rendszerleíró adatbázis létrehozása ügyfél által kezelt kulccsal

Futtassa az [az acr create][az-acr-create] parancsot egy rendszerleíró adatbázis létrehozásához és az ügyfél által felügyelt kulcs engedélyezéséhez. Adja át a felügyelt identitás egyszerű azonosítóját és a korábban környezeti változókban tárolt kulcsazonosítót:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Titkosítási állapot megjelenítése

Ha meg szeretné jelenik, hogy engedélyezve van-e a rendszerleíró adatbázis titkosítása az ügyfél által kezelt kulccsal, futtassa az [az acr encryption show][az-acr-encryption-show] parancsot:

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="enable-customer-managed-key---portal"></a>Ügyfél által felügyelt kulcs engedélyezése - portál

### <a name="create-a-managed-identity"></a>Felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt felügyelt identitást az [Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md) az Azure Portalon. Lépések: [Felhasználó által hozzárendelt identitás létrehozása.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)

Vegye figyelembe a felügyelt identitás **erőforrásnevét.** Erre a névre későbbi lépésekben van szükség.

![Felhasználó által hozzárendelt felügyelt identitás létrehozása az Azure Portalon](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

A kulcstartó létrehozásának lépéseiért olvassa el [a Rövid útmutató: Titkos kulcs beállítása és beolvasása az Azure Key Vaultból az Azure Portalon keresztül című témakört.](../key-vault/quick-create-portal.md)

Amikor egy ügyfél által felügyelt kulcshoz hoz létre kulcstartót, az Alapok lapon a következő védelmi beállításokat kell **engedélyeznie:** **Ideiglenes törlés** és **Védelem kiürítése**. Ezek a beállítások segítenek megakadályozni a véletlen kulcs- vagy kulcstartó-törlések által okozott adatvesztést.

![Kulcstartó létrehozása az Azure Portalon](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Kulcstartó hozzáférési házirendjének hozzáadása

Konfigurálja a key vault házirendjét, hogy az identitás hozzáférhessen.

1. Nyissa meg a kulcstartót.
1. Válassza a **Beállítások** > **hozzáférési házirendek > +Hozzáférési házirend hozzáadása lehetőséget.**
1. Válassza a **Kulcsengedélyek**lehetőséget, majd a **Get**, **A Kulcs kicsomagolása**és **a Körbefolyatási kulcs lehetőséget.**
1. Válassza **az Egyszerű kiválasztása lehetőséget,** és válassza ki a felhasználó által hozzárendelt felügyelt identitás erőforrásnevét.  
1. Válassza **a Hozzáadás**lehetőséget, majd a **Mentés**lehetőséget.

![Kulcstartó-hozzáférési szabályzat létrehozása](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Kulcs létrehozása

1. Nyissa meg a kulcstartót.
1. Válassza a **Beállítások** > **billentyűk lehetőséget**.
1. Válassza a **+Létrehozás/importálás lehetőséget,** és adjon meg egy egyedi nevet a kulcsnak.
1. Fogadja el a fennmaradó alapértelmezett értékeket, és válassza a **Létrehozás lehetőséget.**
1. A létrehozás után jelölje ki a kulcsot, és vegye figyelembe az aktuális kulcsverziót.

### <a name="create-azure-container-registry"></a>Azure Container Registry létrehozása

1. Válassza **az Erőforrástárolók** > **Containers** > **tárolók beállításjegyzékének**létrehozása lehetőséget.
1. Az **Alapok** lapon jelöljön ki vagy hozzon létre egy erőforráscsoportot, és adjon meg egy rendszerleíró adatbázis nevét. A **Termékváltozatban**válassza a **Prémium**lehetőséget.
1. A **Titkosítás** lap **Ügyfél által kezelt kulcs**csoportjában válassza az **Engedélyezve**lehetőséget.
1. Az **Identitás csoportban**válassza ki a létrehozott felügyelt identitást.
1. A **Titkosítási kulcs**csoportban válassza **a Válasszon a kulcstartóból**lehetőséget.
1. Az **Azure Key Vault ból kulcs kiválasztása** ablakban válassza ki az előző szakaszban létrehozott key vaultot, kulcsot és verziót.
1. A **Titkosítás** lapon válassza a **Véleményezés + create**lehetőséget.
1. A Beállításjegyzék-példány telepítéséhez válassza a **Létrehozás** lehetőséget.

![Tárolóbeállításjegyzék létrehozása az Azure Portalon](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

A beállításjegyzék titkosítási állapotának megtekintéséhez keresse meg a rendszerleíró adatbázist. A **Beállítások csoportban**válassza a **Titkosítás (előnézet) lehetőséget.**

## <a name="enable-customer-managed-key---template"></a>Ügyfél által kezelt kulcs engedélyezése - sablon

Az Erőforrás-kezelő sablonnal rendszerleíró adatbázist is létrehozhat, és ügyfél által kezelt kulccsal engedélyezheti a titkosítást. 

A következő sablon létrehoz egy új tároló beállításjegyzéket és egy felhasználó által hozzárendelt felügyelt identitást. Másolja a következő tartalmat egy új fájlba, `CMKtemplate.json`és mentse fájlnévvel, például .

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

Az előző szakaszokban ismertetett lépéseket követve hozza létre a következő erőforrásokat:

* Kulcstartó, név szerint azonosítva
* Kulcstartó kulcsa, kulcsazonosítóval azonosítva

Futtassa a következő [az csoport központi telepítési létrehozási][az-group-deployment-create] parancsát a rendszerleíró adatbázis előző sablonfájl használatával történő létrehozásához. Ha jel, adjon meg egy új rendszerleíró adatbázis nevét és felügyelt identitás nevét, valamint a key vault nevét és a létrehozott kulcsazonosítót. 

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

A rendszerleíró adatbázis titkosításának állapotának megjelenítéséhez futtassa az [az acr encryption show-status][az-acr-encryption-show-status] parancsot:

```azurecli
az acr encryption show-status --name <registry-name> 
```

## <a name="use-the-registry"></a>A beállításjegyzék használata

Miután engedélyezte, hogy a rendszerleíró adatbázis ügyfél által kezelt kulccsal titkosítsa az adatokat, ugyanazokat a beállításjegyzék-műveleteket hajthatja végre, mint amelyeket olyan beállításjegyzékben hajt végre, amely nem ügyfél által kezelt kulccsal van titkosítva. Például hitelesítheti magát a beállításjegyzékkel, és leküldéses Docker-rendszerképek. Példaparancsok megtekintése a [Képek leküldése és lekérése című](container-registry-get-started-docker-cli.md)témakörben.

## <a name="rotate-key"></a>Billentyű elforgatása

Az azure-key vaultban az ügyfél által felügyelt kulcsot a megfelelőségi szabályzatok szerint forgathatja. Hozzon létre egy új kulcsot, majd frissítse a rendszerleíró adatbázist az adatok titkosításához az új kulccsal. Ezeket a lépéseket az Azure CLI vagy a portálon hajthatja végre.

Futtassa például az [az keyvault-kulcs létrehozási][az-keyvault-key-create] parancsát egy új kulcs létrehozásához:

```azurecli
az keyvault key create –-name <new-key-name> --vault-name <key-vault-name> 
```

Ezután futtassa az [az acr encryption rotate-key][az-acr-encryption-rotate-key] parancsot, adja át az új kulcsazonosítót és a korábban konfigurált felügyelt identitás fő azonosítóját:

```azurecli
az acr encryption rotatekey \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity $identityPrincipalID
```

## <a name="revoke-key"></a>Kulcs visszavonása

Vonja vissza az ügyfél által felügyelt titkosítási kulcsot a kulcstartó hozzáférési szabályzatának módosításával vagy a kulcs törlésével. Például az [az keyvault delete-policy][az-keyvault-delete-policy] paranccsal módosíthatja a beállításjegyzék által használt felügyelt identitás hozzáférési házirendjét. Példa:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

A kulcs visszavonása hatékonyan blokkolja az összes rendszerleíró adathoz való hozzáférést, mivel a rendszerleíró adatbázis nem fér hozzá a titkosítási kulcshoz. Ha a kulcshoz való hozzáférés engedélyezve van, vagy a törölt kulcs helyreáll, a rendszerleíró adatbázis kiválasztja a kulcsot, hogy újra hozzáférhessen a titkosított rendszerleíró adatokhoz.

## <a name="next-steps"></a>További lépések

* További információ [az Azure-beli titkosításról.](../security/fundamentals/encryption-atrest.md)
* További információ a hozzáférési szabályzatokról és a [kulcstartóhoz való hozzáférés biztonságossá téválásához.](../key-vault/key-vault-secure-your-key-vault.md)
* Ha visszajelzést szeretne adni az Azure Container Registry ügyfél által felügyelt kulcsairól, keresse fel az [ACR GitHub-webhelyet.](https://aka.ms/acr/issues)


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
