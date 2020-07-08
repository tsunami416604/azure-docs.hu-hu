---
title: Ügyfél által felügyelt kulccsal történő titkosítás
description: Ismerje meg az Azure Container Registry titkosítását, valamint azt, hogyan titkosíthatja a prémium szintű beállításjegyzéket a Azure Key Vaultban tárolt ügyfél által felügyelt kulccsal.
ms.topic: article
ms.date: 05/01/2020
ms.custom: ''
ms.openlocfilehash: 393e51e687e95c1ff4c6a50429dd342005aad296
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84509542"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Beállításjegyzék titkosítása az ügyfél által felügyelt kulccsal

Ha lemezképeket és egyéb összetevőket tárol egy Azure Container registryben, az Azure automatikusan titkosítja a beállításjegyzék tartalmát a [szolgáltatás által felügyelt kulcsokkal](../security/fundamentals/encryption-atrest.md#data-encryption-models). Egy további titkosítási réteggel kiegészítheti az alapértelmezett titkosítást a Azure Key Vault-ben létrehozott és kezelt kulcs használatával. Ez a cikk végigvezeti az Azure CLI és a Azure Portal használatának lépésein.

Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítást a [Azure Key Vault](../key-vault/general/overview.md)integrációja támogatja. Létrehozhat saját titkosítási kulcsokat, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat a kulcsok létrehozásához. A Azure Key Vault használatával is naplózhatja a kulcshasználat.

Ez a funkció a **prémium** szintű Container Registry szolgáltatási szinten érhető el. További információ a beállításjegyzék szolgáltatási szintjeiről és korlátairól: [Azure Container Registry szolgáltatási szintek](container-registry-skus.md).

   
## <a name="things-to-know"></a>Tudnivalók

* Jelenleg csak a beállításjegyzék létrehozásakor engedélyezheti az ügyfél által felügyelt kulcsokat.
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

A parancs kimenetében jegyezze fel a következő értékeket: `id` és `principalId` . Ezekre az értékekre szüksége lesz a beállításjegyzék hozzáférésének a Key vaulthoz való konfigurálásának későbbi lépéseiben.

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

A parancs kimenetében jegyezze fel a kulcs AZONOSÍTÓját `kid` . Ezt az azonosítót a következő lépésben kell használni:

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
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

### <a name="create-a-registry-with-customer-managed-key"></a>Beállításjegyzék létrehozása az ügyfél által felügyelt kulccsal

Futtassa az az [ACR Create][az-acr-create] parancsot egy beállításjegyzék létrehozásához a prémium szintű szolgáltatási szinten, és engedélyezze az ügyfél által felügyelt kulcsot. Adja át a felügyelt identitás résztvevő-AZONOSÍTÓját és a korábban a környezeti változókban tárolt kulcs AZONOSÍTÓját:

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

A kimenet a következőhöz hasonló:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789..."
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Ügyfél által felügyelt kulcs engedélyezése – portál

### <a name="create-a-managed-identity"></a>Felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt [felügyelt identitást az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md) a Azure Portal. A lépéseket a [felhasználó által hozzárendelt identitás létrehozása](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)című témakörben tekintheti meg.

Az identitás nevét a későbbi lépésekben használhatja.

![Felhasználó által hozzárendelt felügyelt identitás létrehozása a Azure Portalban](./media/container-registry-customer-managed-keys/create-managed-identity.png)

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

A kulcstartó létrehozásának lépéseiért lásd [: gyors üzembe helyezés és a Azure Key Vault titkos kód beolvasása a Azure Portal használatával](../key-vault/secrets/quick-create-portal.md).

Ha az ügyfél által felügyelt kulcshoz kulcstartót hoz létre, az **alapok** lapon engedélyezze a következő védelmi beállításokat: **Soft delete** and **Purge Protection**. Ezek a beállítások segítenek megakadályozni az adatvesztést a véletlen kulcs vagy a kulcstartó törlése miatt.

![Key Vault létrehozása a Azure Portalban](./media/container-registry-customer-managed-keys/create-key-vault.png)

### <a name="add-key-vault-access-policy"></a>Key Vault hozzáférési szabályzat hozzáadása

Konfiguráljon egy házirendet a Key vaulthoz, hogy az identitás hozzáférhessen.

1. Navigáljon a kulcstartóhoz.
1. Válassza a **Beállítások**  >  **hozzáférési szabályzatok > + hozzáférési házirend hozzáadása**lehetőséget.
1. Válassza a **kulcs engedélyei**lehetőséget, majd a **beolvasás**, a **kicsomagolás** **és a**kicsomagolási kulcs elemet.
1. Válassza a **rendszerbiztonsági tag** kiválasztása lehetőséget, és válassza ki a felhasználó által hozzárendelt felügyelt identitás erőforrásának nevét.  
1. Válassza a **Hozzáadás**, majd a **Mentés**lehetőséget.

![Key Vault hozzáférési szabályzat létrehozása](./media/container-registry-customer-managed-keys/add-key-vault-access-policy.png)

### <a name="create-key"></a>Kulcs létrehozása

1. Navigáljon a kulcstartóhoz.
1. Válassza a **Beállítások**  >  **kulcsok**elemet.
1. Válassza a **+ Létrehozás/importálás** lehetőséget, és adjon meg egy egyedi nevet a kulcshoz.
1. Fogadja el a többi alapértelmezett értéket, és válassza a **Létrehozás**lehetőséget.
1. A létrehozás után válassza ki a kulcsot, és jegyezze fel az aktuális kulcs verzióját.

### <a name="create-azure-container-registry"></a>Azure Container Registry létrehozása

1. Válassza **az erőforrás létrehozása**  >  **tárolók**  >  **Container Registry**elemet.
1. Az **alapvető beállítások** lapon válasszon ki vagy hozzon létre egy erőforráscsoportot, és adjon meg egy beállításjegyzék-nevet. Az **SKU**területen válassza a **prémium**lehetőséget.
1. A **titkosítás** lap **ügyfél által felügyelt kulcs**területén válassza az **engedélyezve**lehetőséget.
1. Az **identitás**területen válassza ki a létrehozott felügyelt identitást.
1. A **titkosítás**területen válassza **a kiválasztás a Key Vault lehetőséget**.
1. A **válasszon kulcsot Azure Key Vault** ablakban válassza ki az előző szakaszban létrehozott kulcstárolót, kulcsot és verziót.
1. A **titkosítás** lapon válassza a **felülvizsgálat + létrehozás**elemet.
1. Válassza a **Létrehozás** lehetőséget a beállításjegyzék-példány telepítéséhez.

![Tároló-beállításjegyzék létrehozása a Azure Portal](./media/container-registry-customer-managed-keys/create-encrypted-registry.png)

Ha szeretné megtekinteni a beállításjegyzék titkosítási állapotát a portálon, keresse meg a beállításjegyzéket. A **Beállítások**területen válassza a **titkosítás**lehetőséget.

## <a name="enable-customer-managed-key---template"></a>Ügyfél által felügyelt kulcs – sablon engedélyezése

Egy Resource Manager-sablonnal is létrehozhat egy beállításjegyzéket, és engedélyezheti a titkosítást az ügyfél által felügyelt kulccsal. 

A következő sablon létrehoz egy új tároló-beállításjegyzéket és egy felhasználó által hozzárendelt felügyelt identitást. Másolja az alábbi tartalmat egy új fájlba, és mentse a fájlt egy olyan fájlnév használatával, mint például `CMKtemplate.json` .

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

A beállításjegyzék-titkosítás állapotának megjelenítéséhez futtassa az az [ACR encryption show][az-acr-encryption-show] parancsot:

```azurecli
az acr encryption show --name <registry-name> 
```

## <a name="use-the-registry"></a>A beállításjegyzék használata

Miután engedélyezte az ügyfél által felügyelt kulcsokat a beállításjegyzékben, ugyanazokat a beállításjegyzékbeli műveleteket hajthatja végre, amelyeket egy ügyfél által felügyelt kulccsal nem titkosított beállításjegyzékben hajt végre. Például a hitelesítést a beállításjegyzékben és a Docker-rendszerképek leküldésekor végezheti el. Lásd: példák a [leküldéses és a lekérési](container-registry-get-started-docker-cli.md)parancsokra.

## <a name="rotate-key"></a>Elforgatási kulcs

Az ügyfél által felügyelt kulcs elforgatása a beállításjegyzék-titkosításhoz a megfelelőségi szabályzatokban. Hozzon létre egy új kulcsot, vagy frissítsen egy kulcsot, majd frissítse a beállításjegyzéket, hogy az adatait a kulcs használatával titkosítsa. Ezeket a lépéseket az Azure CLI használatával vagy a portálon végezheti el.

A kulcsok elforgatásakor általában ugyanazt az identitást kell megadnia, amelyet a beállításjegyzék létrehozásakor használ. Szükség esetén új, felhasználó által hozzárendelt identitást konfigurálhat a kulcs eléréséhez, vagy engedélyezheti és megadhatja a beállításjegyzék rendszer által hozzárendelt identitását.

> [!NOTE]
> Győződjön meg arról, hogy a szükséges [Key Vault-hozzáférési házirend](#add-key-vault-access-policy) be van állítva a kulcsokhoz való hozzáféréshez konfigurált identitáshoz. 

### <a name="azure-cli"></a>Azure CLI

A Key Vault-kulcsok létrehozásához és kezeléséhez használja [az az kulcstartó Key][az-keyvault-key] parancsokat. Ha például új kulcs-verziót vagy-kulcsot szeretne létrehozni, futtassa az az [kulcstartó kulcs létrehozása][az-keyvault-key-create] parancsot:

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name> 

# Create new key
az keyvault key create \
  –-name <new-key-name> \
  --vault-name <key-vault-name> 
```

Ezután futtassa az az [ACR encryption forgatni-Key][az-acr-encryption-rotate-key] parancsot, és adja át az új kulcs azonosítóját és a konfigurálni kívánt identitást:

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

### <a name="portal"></a>Portál

A beállításjegyzék **titkosítási** beállításaival frissítheti az ügyfél által felügyelt kulcs verziószámát, kulcsát, kulcstartóját vagy identitásának beállításait. 

Például egy új kulcs verziójának létrehozásához és konfigurálásához:

1. A portálon navigáljon a beállításjegyzékhez. 
1. A **Beállítások**területen válassza a **titkosítási**  >  **kulcs módosítása**elemet.
1. Válassza a **kulcs kiválasztása** lehetőséget.
    
    ![Kulcs elforgatása a Azure Portalban](./media/container-registry-customer-managed-keys/rotate-key.png)
1. A **válasszon kulcsot Azure Key Vault** ablakban válassza ki a korábban konfigurált kulcstartót és kulcsot, majd a **verzió**területen válassza az **új létrehozása**lehetőséget.
1. A **kulcs létrehozása** **ablakban válassza a létrehozás,** majd a **Létrehozás**lehetőséget.
1. Fejezze be a kulcs kijelölését, és válassza a **Mentés**lehetőséget.

## <a name="revoke-key"></a>Kulcs visszavonása

A Key Vault hozzáférési házirendjének módosításával vagy a kulcs törlésével vonja vissza az ügyfél által felügyelt titkosítási kulcsot. Például az az Key [Vault delete-Policy][az-keyvault-delete-policy] paranccsal módosíthatja a beállításjegyzék által használt felügyelt identitás hozzáférési házirendjét:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

A kulcs visszavonása gyakorlatilag blokkolja az összes beállításjegyzék-adattal való hozzáférést, mivel a beállításjegyzék nem fér hozzá a titkosítási kulcshoz. Ha a kulcshoz való hozzáférés engedélyezve van, vagy a törölt kulcs vissza lett állítva, a beállításjegyzékben meg kell választania a kulcsot, hogy újra hozzáférhessen a titkosított beállításjegyzék-adataihoz.

## <a name="advanced-scenarios"></a>Speciális forgatókönyvek

### <a name="system-assigned-identity"></a>Rendszer által hozzárendelt identitás

A beállításjegyzék rendszerhez rendelt felügyelt identitását beállíthatja a titkosítási kulcsok kulcstárolójának eléréséhez. Ha nem ismeri az Azure-erőforrások különböző felügyelt identitásait, tekintse meg az [áttekintést](../active-directory/managed-identities-azure-resources/overview.md).

A beállításjegyzék rendszerhez rendelt identitásának engedélyezése a portálon:

1. A portálon navigáljon a beállításjegyzékhez. 
1. Válassza a **Beállítások**  >   **identitás**lehetőséget.
1. A **rendszer által hozzárendelve**beállításnál állítsa be **a**következőt: **állapot** . Kattintson a **Mentés** gombra.
1. Másolja az identitás **objektum-azonosítóját** .

Az identitás hozzáférésének biztosítása a kulcstartóhoz:

1. Navigáljon a kulcstartóhoz.
1. Válassza a **Beállítások**  >  **hozzáférési szabályzatok > + hozzáférési házirend hozzáadása**lehetőséget.
1. Válassza a **kulcs engedélyei**lehetőséget, majd a **beolvasás**, a **kicsomagolás** **és a**kicsomagolási kulcs elemet.
1. Válassza a **rendszerbiztonsági tag kiválasztása** lehetőséget, és keresse meg a rendszer által hozzárendelt felügyelt identitáshoz tartozó objektumazonosítót, vagy a beállításjegyzék nevét.  
1. Válassza a **Hozzáadás**, majd a **Mentés**lehetőséget.

A beállításjegyzék titkosítási beállításainak frissítése a személyazonosság használatára:

1. A portálon navigáljon a beállításjegyzékhez. 
1. A **Beállítások**területen válassza a **titkosítási**  >  **kulcs módosítása**elemet.
1. Az **identitás**területen válassza ki a **rendszer által hozzárendelt**elemet, majd kattintson a **Mentés**gombra.

### <a name="key-vault-firewall"></a>Key Vault tűzfal

Ha az Azure Key Vault egy Key Vault tűzfallal rendelkező virtuális hálózaton van telepítve, hajtsa végre a következő lépéseket:

1. Konfigurálja a beállításjegyzék titkosítását a beállításjegyzék rendszer által hozzárendelt identitásának használatára. Lásd az előző szakaszt.
2. Konfigurálja a kulcstartót úgy, hogy az engedélyezze a hozzáférést bármely [megbízható szolgáltatás](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)számára. 

Részletes lépések: [Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása](../key-vault/general/network-security.md). 

## <a name="next-steps"></a>További lépések

* További információ [Az Azure-](../security/fundamentals/encryption-atrest.md)beli inaktív adatok titkosításáról.
* További információ a hozzáférési házirendekről és a [kulcstartóhoz való hozzáférés biztonságossá](../key-vault/general/secure-your-key-vault.md)tételéről.


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
