---
title: Az Azure Container Instances egy felügyelt identitás használata
description: Ismerje meg, hogy egy felügyelt identitás használata más Azure-szolgáltatások az Azure Container Instances szolgáltatásban való hitelesítéséhez.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 10/22/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: ac0a84aa3121c6ebb91860c96c0f6692827c8a3f
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58336524"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Felügyelt identitások használata Azure Container Instances szolgáltatásban

Használat [felügyelt identitások az Azure-erőforrások](../active-directory/managed-identities-azure-resources/overview.md) kód futtatásához az Azure Container Instances szolgáltatásban, amely együttműködik az egyéb Azure-szolgáltatásokkal – anélkül, hogy minden titkos kódok vagy hitelesítő kód. A szolgáltatás egy Azure Container Instances üzembe helyezés az Azure Active Directoryban egy automatikusan felügyelt identitással biztosít.

Ebben a cikkben, tudjon meg többet az Azure Container Instances szolgáltatásban felügyelt identitások és:

> [!div class="checklist"]
> * A tárolócsoport egy felhasználó által hozzárendelt vagy rendszer által hozzárendelt identitás engedélyezése
> * Az identitás hozzáférést biztosítani az Azure Key Vaultban
> * A futó tárolót a Key Vault elérése érdekében a felügyelt identitás használata

Alkalmazkodjon a példákat, engedélyezése és használata az identitások az Azure Container Instances szolgáltatásban más Azure-szolgáltatások eléréséhez. Ezekben a példákban interaktívak. Azonban a gyakorlatban a tárolólemezképek kód futtatásával az Azure-szolgáltatásokhoz való hozzáférést.

> [!NOTE]
> Jelenleg egy felügyelt identitás nem használhat egy tárolócsoport üzembe helyezni egy virtuális hálózatban.

## <a name="why-use-a-managed-identity"></a>Miért érdemes használni egy felügyelt identitás?

A futó tárolót egy felügyelt identitás használják a hitelesítéshez bármely [szolgáltatás, amely támogatja az Azure AD-hitelesítés](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) tároló kódja a hitelesítő adatok kezelése nélkül. AD-hitelesítést nem támogató szolgáltatások az Azure Key Vaultban titkos kulcsok tárolására, és lekérni a hitelesítő adatokat a Key Vault elérése érdekében a felügyelt identitás használata. Egy felügyelt identitás használatával kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitások?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt. Felügyelt identitások jelenleg csak a Linux tárolópéldányok támogatottak.
>  

### <a name="enable-a-managed-identity"></a>Egy felügyelt identitás engedélyezése

 Az Azure Container Instances szolgáltatásban az Azure-erőforrások felügyelt identitások REST API-verzió 2018-10-01-es és a megfelelő SDK-k és eszközök az újabb verziók támogatják. Amikor egy tárolócsoportot hoz létre, engedélyezze egy vagy több felügyelt identitások beállításával egy [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) tulajdonság. Emellett engedélyezheti vagy felügyelt identitások frissítése után egy tárolócsoport fut; Mindkét művelet hatására a tárolócsoport újraindításához. Az identitások egy új vagy meglévő tárolócsoport létrehozásához használja az Azure CLI, egy Resource Manager-sablon vagy egy YAML-fájlt. 

Az Azure Container Instances támogatja a felügyelt Azure-identitások mindkét típusú: felhasználó által hozzárendelt, és a rendszer által hozzárendelt. A tárolócsoport egy rendszer által hozzárendelt identitással, egy vagy több felhasználó által hozzárendelt identitások vagy mindkét típusú identitások engedélyezheti. 

* A **felhasználó által hozzárendelt** felügyelt identitás egy önálló Azure-erőforrás, amely a használatban levő előfizetésre megbízható Azure AD-bérlő jön létre. Az identitás létrehozása után az identitás rendelhet egy vagy több Azure-erőforrások (az Azure Container Instances vagy más Azure-szolgáltatásokhoz). Egy felhasználó által hozzárendelt identitással életciklusának külön-külön felügyelje a tárolócsoportok vagy más szolgáltatás-erőforrások, amelyekhez hozzárendelte azt életciklusát. Ez a viselkedés különösen hasznos az Azure Container Instances szolgáltatásban. Az identitás túlnyúlik a tárolócsoport élettartamát, mert más szabványos beállításokat, hogy a csoport tárolópéldányokba magas megismételhető együtt felhasználhatja azt.

* A **alapértelmezett** felügyelt identitás közvetlenül az Azure Container Instances szolgáltatásban egy tárolócsoport engedélyezve van. Ha engedélyezve van, az Azure az identitás a csoport az Azure AD-bérlő az előfizetés-példány által megbízhatónak tartott hoz létre. Az identitás létrehozása után minden egyes tárolócsoportban tárolót a hitelesítő adatok vannak kiépítve. Egy rendszer által hozzárendelt identitással életciklusának közvetlenül a tárolócsoport, amely engedélyezve van a van kötve. A csoport törlése esetén az Azure automatikusan törli azokat a hitelesítő adatokat és az identitás az Azure ad-ben.

### <a name="use-a-managed-identity"></a>Felügyelt identitás használata

Egy felügyelt identitás használatára, az identitás kezdetben hozzáférést kell adni egy vagy több Azure-szolgáltatási erőforrások (például egy webalkalmazás, egy Key Vaultot vagy egy Storage-fiók), az előfizetésben. Az Azure-erőforrások eléréséhez a futó tárolót, a kódot kell beszerezni egy *hozzáférési jogkivonat* az Azure ad-ben a végpont. Ezután a kód hívása egy szolgáltatás, amely támogatja az Azure AD-hitelesítést a elküldi a hozzáférési jogkivonat. 

A futó tárolót egy felügyelt identitás használata lényegében megegyezik az identitás használatával egy Azure-beli virtuális gépen. Tekintse meg a virtuális gép útmutató segítségével egy [token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure PowerShell vagy az Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md), vagy a [Azure SDK-k](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyi telepítése és használata a parancssori felület, ez a cikk van szükség, hogy futnak-e az Azure CLI verziója 2.0.49 vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;

Ebben a cikkben szereplő példák egy felügyelt identitás az Azure Container Instances szolgáltatásban az Azure Key Vault titkos kulcsából eléréséhez használja. 

Először is hozzon létre egy erőforráscsoportot *myResourceGroup* a a *eastus* helyet az alábbi [az csoport létrehozása](/cli/azure/group?view=azure-cli-latest#az-group-create) parancsot:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Használja a [az keyvault létrehozása](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) paranccsal hozzon létre egy Key Vaultot. Mindenképpen adjon meg egy egyedi Key Vault. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

A Key Vault használatával a titkos kulcs minta Store a [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) parancsot:

```azurecli-interactive
az keyvault secret set --name SampleSecret --value "Hello Container Instances!" --description ACIsecret  --vault-name mykeyvault
```

Folytassa a következő példák eléréséhez a Key Vault használatával a felhasználó által hozzárendelt vagy rendszer által hozzárendelt felügyelt identitás, az Azure Container Instances szolgáltatásban.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>1. példa: Az Azure Key Vault elérése érdekében a felhasználó által hozzárendelt identitás használatára

### <a name="create-an-identity"></a>Hozzon létre egy azonosítót

Először hozzon létre egy azonosítót az előfizetés használatával a [az identitás létrehozása](/cli/azure/identity?view=azure-cli-latest#az-identity-create) parancsot. Használja a Key Vault létrehozásához használt ugyanazt az erőforráscsoportot, vagy használjon egy másik.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACIId
```

Az alábbi lépéseket az identitást használja, használja a [az identitás show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) parancsot a változókat az identitáshoz tartozó szolgáltatásnév-Azonosítót és erőforrás-azonosító tárolására.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Egy felhasználó által hozzárendelt identitással egy tárolócsoport engedélyezése

Futtassa a következő [az tároló létrehozása](/cli/azure/container?view=azure-cli-latest#az-container-create) parancs létrehoz egy tárolópéldányt, Ubuntu Server alapú. Ebben a példában egy egy tároló-csoportot, amely használatával interaktív módon elérni az egyéb Azure-szolgáltatásokat biztosít. A `--assign-identity` paraméter a csoporthoz adja át a felhasználóhoz felügyelt identitásnak. A hosszú ideig futó parancs biztosítja, hogy a tároló futtatását. Ebben a példában a Key Vault létrehozásához használt ugyanabban az erőforráscsoportban, de megadhat egy másikat.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity $resourceID --command-line "tail -f /dev/null"
```

Pár másodpercen belül az üzembe helyezés befejezéséről tájékoztató választ kell kapnia az Azure CLI-ről. Ellenőrizze az állapotát a [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show) parancsot.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A `identity` hasonlít a kimeneti szakaszának a következőhöz megjelenítése az identitás van beállítva tárolócsoportban. A `principalID` alatt `userAssignedIdentities` van az identitás, az Azure Active Directoryban létrehozott egyszerű szolgáltatás:

```console
...
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
...
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Felhasználó által hozzárendelt identitás-hozzáférési jogot a Key Vault

Futtassa a következő [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) parancsot egy hozzáférési házirendet a Key vaulttal. Az alábbi példa lehetővé teszi, hogy a felhasználó által hozzárendelt identitás a Key vault titkos kódok lekéréséhez:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Felhasználó által hozzárendelt identitás használata a Key vault titkos kulcs lekérése

Most már használhatja a felügyelt identitás belül a futó tároló-példány a Key Vault elérése érdekében. Ebben a példában először indítsa el a bash felületet, a tárolóban:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Futtassa az alábbi parancsokat a bash, a tárolóban. Azure Active Directory használatával hitelesíti a Key Vault hozzáférési jogkivonatot kapjon, futtassa a következő parancsot:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Kimenet:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

A hozzáférési jogkivonat tárolható egy változóban az ezt követő parancsok használatával hitelesíteni, futtassa a következő parancsot:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

A Key Vault hitelesíteni, és olvassa el a titkos kód mostantól használhatja a hozzáférési jogkivonat. Ügyeljen arra, hogy az URL-címben a kulcstartó nevét (*https://mykeyvault.vault.azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A válasz a következőhöz hasonlóan néz ki a titkos kulcs megjelenítése. A kódban, ez a kimenet a titkos kulcs beszerzése szeretné elemezni. Ezt követően az ezt követő műveletet a titkos kulcs használatával egy másik Azure-erőforrás eléréséhez.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>2. példa Azure Key Vault elérése érdekében a rendszer által hozzárendelt identitás használatára

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>A tárolócsoport egy rendszer által hozzárendelt identitás engedélyezése

Futtassa a következő [az tároló létrehozása](/cli/azure/container?view=azure-cli-latest#az-container-create) parancs létrehoz egy tárolópéldányt, Ubuntu Server alapú. Ebben a példában egy egy tároló-csoportot, amely használatával interaktív módon elérni az egyéb Azure-szolgáltatásokat biztosít. A `--assign-identity` további értéket a paraméter lehetővé teszi, hogy a rendszer által hozzárendelt felügyelt identitás a csoportban. A hosszú ideig futó parancs biztosítja, hogy a tároló futtatását. Ebben a példában a Key Vault létrehozásához használt ugyanabban az erőforráscsoportban, de megadhat egy másikat.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity --command-line "tail -f /dev/null"
```

Pár másodpercen belül az üzembe helyezés befejezéséről tájékoztató választ kell kapnia az Azure CLI-ről. Ellenőrizze az állapotát a [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show) parancsot.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A `identity` szakasz a kimenetben az alábbihoz hasonlóan néz ki, hogy a rendszer által hozzárendelt identitás létrehozása az Azure Active Directoryban megjelenítése:

```console
...
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
...
```

Egy változó értékének beállítása `principalId` (a szolgáltatásnév-Azonosítót) az identitás, a későbbi lépésekben használni.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Tároló csoport hozzáférési jogot a Key Vault

Futtassa a következő [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) parancsot egy hozzáférési házirendet a Key vaulttal. Az alábbi példa lehetővé teszi, hogy a rendszer által felügyelt identitás a Key vault titkos kódok lekéréséhez:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Key vault titkos kulcs lekérése a tárolót azonosító használatával

Most már használhatja a felügyelt identitás belül a futó tároló-példány a Key Vault elérése érdekében. Ebben a példában először indítsa el a bash felületet, a tárolóban:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Futtassa az alábbi parancsokat a bash, a tárolóban. Azure Active Directory használatával hitelesíti a Key Vault hozzáférési jogkivonatot kapjon, futtassa a következő parancsot:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Kimenet:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

A hozzáférési jogkivonat tárolható egy változóban az ezt követő parancsok használatával hitelesíteni, futtassa a következő parancsot:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

A Key Vault hitelesíteni, és olvassa el a titkos kód mostantól használhatja a hozzáférési jogkivonat. Ügyeljen arra, hogy az URL-címben a kulcstartó nevét (*https:\//mykeyvault.vault.azure.net/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A válasz a következőhöz hasonlóan néz ki a titkos kulcs megjelenítése. A kódban, ez a kimenet a titkos kulcs beszerzése szeretné elemezni. Ezt követően az ezt követő műveletet a titkos kulcs használatával egy másik Azure-erőforrás eléréséhez.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Resource Manager-sablon használatával felügyelt identitás engedélyezése

A tárolóban található csoport egy felügyelt identitás engedélyezése egy [Resource Manager-sablon](container-instances-multi-container-group.md)állítsa be a `identity` tulajdonságát a `Microsoft.ContainerInstance/containerGroups` rendelkező objektum egy `ContainerGroupIdentity` objektum. A következő kódrészletek megjelenítése a `identity` konfigurálva a különböző helyzetekhez tulajdonság. Tekintse meg a [Resource Manager sablonreferenciája](/azure/templates/microsoft.containerinstance/containergroups). Adjon meg egy `apiVersion` , `2018-10-01`.

### <a name="user-assigned-identity"></a>Felhasználó által hozzárendelt identitás

Egy felhasználó által hozzárendelt identitással egy erőforrás-azonosító a következő formában:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Egy vagy több felhasználó által hozzárendelt identitások engedélyezheti.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Rendszer által hozzárendelt identitás

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Rendszer - és a felhasználó által hozzárendelt identitások

Egy tárolócsoportot a rendszer által hozzárendelt identitás- és a egy vagy több felhasználó által hozzárendelt identitások engedélyezheti.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>YAML-fájllal felügyelt identitás engedélyezése

Ahhoz, hogy a tárolócsoport egy felügyelt identitás használatával üzembe helyezett egy [YAML-fájl](container-instances-multi-container-yaml.md), a következő yaml-kódot tartalmaznak.
Adjon meg egy `apiVersion` , `2018-10-01`.

### <a name="user-assigned-identity"></a>Felhasználó által hozzárendelt identitás

Egy felhasználó által hozzárendelt identitással az űrlap egy erőforrás-azonosító 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Egy vagy több felhasználó által hozzárendelt identitások engedélyezheti.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Rendszer által hozzárendelt identitás

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Rendszer - és a felhasználó által hozzárendelt identitások

Egy tárolócsoportot a rendszer által hozzárendelt identitás- és a egy vagy több felhasználó által hozzárendelt identitások engedélyezheti.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedett az Azure Container Instances szolgáltatásban felügyelt identitások és:

> [!div class="checklist"]
> * A tárolócsoport egy felhasználó által hozzárendelt vagy rendszer által hozzárendelt identitás engedélyezése
> * Az identitás hozzáférést biztosítani az Azure Key Vaultban
> * A futó tárolót a Key Vault elérése érdekében a felügyelt identitás használata

* Tudjon meg többet [felügyelt identitások az Azure-erőforrások](/azure/active-directory/managed-identities-azure-resources/).

* Lásd: egy [Azure Go SDK példa](https://medium.com/@samkreter/c98911206328) egy felügyelt identitás használata a Key Vault elérése az Azure Container Instances szolgáltatásban.
