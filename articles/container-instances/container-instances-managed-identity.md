---
title: Felügyelt identitás engedélyezése a tárolócsoportban
description: Megtudhatja, hogyan engedélyezheti a felügyelt identitásokat az Azure Container-példányokban, amelyek más Azure-szolgáltatásokkal is hitelesíthetők
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 003055d5021dd8ad7c3bab6d2900298ffd13b222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76901927"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Felügyelt identitások használata az Azure Container Instances használatával

Felügyelt [identitások azure-erőforrások](../active-directory/managed-identities-azure-resources/overview.md) futtatásához kódot Azure Container Instances, amely kölcsönhatásba lép más Azure-szolgáltatásokkal anélkül, hogy titkos kulcsokat vagy hitelesítő adatokat a kódban. A szolgáltatás egy Azure Container Instances központi telepítés egy automatikusan felügyelt identitás az Azure Active Directoryban.

Ebben a cikkben további információ a felügyelt identitásokról az Azure Container-példányokban, és:

> [!div class="checklist"]
> * Felhasználó által hozzárendelt vagy rendszerhez rendelt identitás engedélyezése tárolócsoportban
> * Az identitás-hozzáférés megadása egy Azure-kulcstartóhoz
> * A felügyelt identitás használatával egy futó tárolóból származó kulcstartó eléréséhez

A példák adaptálása az Azure Container Instances identitásainak engedélyezéséhez és használatához más Azure-szolgáltatások eléréséhez. Ezek a példák interaktívak. Azonban a gyakorlatban a tárolórendszerképek en futna kódot az Azure-szolgáltatások eléréséhez.

> [!NOTE]
> Jelenleg nem használható felügyelt identitás egy virtuális hálózatra telepített tárolócsoportban.

## <a name="why-use-a-managed-identity"></a>Miért érdemes felügyelt identitást használni?

Felügyelt identitás használatával egy futó tárolóban hitelesítést minden [olyan szolgáltatás, amely támogatja az Azure AD-hitelesítés](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) nélkül hitelesítő adatok kezelése a tárolókódban. Az AD-hitelesítést nem támogató szolgáltatások esetében titkos kulcsokat tárolhat egy Azure-kulcstartóban, és a felügyelt identitás használatával hozzáférhet a kulcstartóhoz a hitelesítő adatok lekéréséhez. A felügyelt identitások használatával kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitások?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt. Jelenleg az Azure Container Instances felügyelt identitások csak Linux-tárolók, és még nem a Windows-tárolók.
>  

### <a name="enable-a-managed-identity"></a>Felügyelt identitás engedélyezése

 Az Azure Container-példányokban az Azure-erőforrások felügyelt identitásait a REST API 2018-10-10-01-es verziója és a megfelelő SDK-k és eszközök támogatja. Tárolócsoport létrehozásakor engedélyezze egy vagy több felügyelt identitást egy [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) tulajdonság beállításával. A tárolócsoport futása után is engedélyezheti vagy frissítheti a felügyelt identitásokat – vagy a művelet hatására a tárolócsoport újraindul. Az identitások beállítása egy új vagy meglévő tárolócsoport, használja az Azure CLI, egy Resource Manager-sablon vagy egy YAML-fájl. 

Az Azure Container Instances támogatja mindkét típusú felügyelt Azure-identitások: felhasználó által hozzárendelt és a rendszer által hozzárendelt. Egy tárolócsoportban engedélyezheti a rendszer által hozzárendelt identitást, egy vagy több felhasználó által hozzárendelt identitást vagy mindkét típusú identitást. 

* A **felhasználó által kijelölt** felügyelt identitás jön létre, mint egy önálló Azure-erőforrás az Azure AD-bérlő, amely a használatban lévő előfizetés megbízható. Az identitás létrehozása után az identitás hozzárendelhető egy vagy több Azure-erőforráshoz (az Azure Container Instances vagy más Azure-szolgáltatásokban). A felhasználó által hozzárendelt identitás életciklusa a tárolócsoportok vagy más szolgáltatáserőforrások életciklusától elkülönítve történik, amelyekhez hozzá van rendelve. Ez a viselkedés különösen hasznos az Azure Container Instances. Mivel az identitás túlnyúlik egy tárolócsoport élettartamán, újra felhasználhatja azt más szabványos beállításokkal együtt, hogy a tárolócsoport központi telepítések nagymértékben megismételhetőlegyen.

* A **rendszer által hozzárendelt** felügyelt identitás közvetlenül engedélyezve van egy tárolócsoportban az Azure Container Instances. Ha engedélyezve van, az Azure létrehoz egy identitást a csoport számára az Azure AD-bérlőben, amely a példány előfizetése által megbízhatónak van. Az identitás létrehozása után a hitelesítő adatok a tárolócsoport minden egyes tárolójában ki vannak építve. A rendszer által hozzárendelt identitás életciklusa közvetlenül kapcsolódik ahhoz a tárolócsoporthoz, amelyen engedélyezve van. A csoport törlésekor az Azure automatikusan törli a hitelesítő adatokat és az identitást az Azure AD-ben.

### <a name="use-a-managed-identity"></a>Felügyelt identitás használata

Felügyelt identitás használatához az identitásnak kezdetben hozzáférést kell biztosítani egy vagy több Azure-szolgáltatás-erőforrásokhoz (például egy webalkalmazáshoz, egy kulcstartóhoz vagy egy tárfiókhoz) az előfizetésben. Az Azure-erőforrások elérése egy futó tárolóból, a kód be kell szereznie egy *hozzáférési jogkivonatot* egy Azure AD-végpont. Ezután a kód elküldi a hozzáférési jogkivonatot egy hívás egy szolgáltatás, amely támogatja az Azure AD-hitelesítést. 

Felügyelt identitás használata egy futó tárolóban lényegében ugyanaz, mint egy azure-beli virtuális gép identitásának használata. Tekintse meg a virtuális gép útmutató egy [jogkivonat,](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)Az Azure PowerShell vagy az [Azure CLI,](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)vagy az [Azure SDK-k.](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a cikk megköveteli, hogy az Azure CLI 2.0.49-es vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Azure-kulcstartó létrehozása

A példák ebben a cikkben egy felügyelt identitás azure-tárolópéldányok egy Azure-key vault titkos eléréséhez. 

Először hozzon létre egy erőforráscsoportot *myResourceGroup* néven az *eastus* helyen az alábbi [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) paranccsal:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Használja az [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) parancsot egy key vault létrehozásához. Ügyeljen arra, hogy adjon meg egy egyedi kulcstartó nevét. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Az [az keyvault titkoskészlet](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) parancsával egy mintatitkos kulcsot tárolanak a key vault titkostárolójában:

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Folytassa az alábbi példákkal a kulcstároló eléréséhez egy felhasználó által hozzárendelt vagy rendszeráltal hozzárendelt felügyelt identitás használatával az Azure Container Instances használatával.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>1. példa: Az Azure key vault eléréséhez használjon felhasználó által hozzárendelt identitást

### <a name="create-an-identity"></a>Identitás létrehozása

Először hozzon létre egy identitást az előfizetésben az [az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) paranccsal. Használhatja ugyanazt az erőforráscsoportot a key vault létrehozásához, vagy egy másikat.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Az identitás használata a következő lépésekben, használja az [identitásshow-parancs](/cli/azure/identity?view=azure-cli-latest#az-identity-show) az identitás egyszerű szolgáltatásazonosítóját és erőforrás-azonosítóját változókban tárolja.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Felhasználó által hozzárendelt identitás engedélyezése tárolócsoportban

Futtassa a következő [az container create](/cli/azure/container?view=azure-cli-latest#az-container-create) parancsot `azure-cli` a Microsoft lemezképe alapján létrehozott tárolópéldány létrehozásához. Ebben a példában egy egytárolós csoportot biztosít, amely interaktív módon futtathatja az Azure CLI-t más Azure-szolgáltatások eléréséhez. Ebben a szakaszban csak az alap Ubuntu operációs rendszert használják. 

A `--assign-identity` paraméter átadja a felhasználó által hozzárendelt felügyelt identitást a csoportnak. A hosszú ideig futó parancs tartja a tároló fut. Ez a példa ugyanazt az erőforráscsoportot használja a key vault létrehozásához, de megadhat egy másikat.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Pár másodpercen belül az üzembe helyezés befejezéséről tájékoztató választ kell kapnia az Azure CLI-ről. Ellenőrizze az állapotát az [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show) paranccsal.

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

A `identity` kimeneti szakasz a következőhöz hasonlóan néz ki, és az identitás a tárolócsoportban van beállítva. Az `principalID` `userAssignedIdentities` alul az Azure Active Directoryban létrehozott identitás egyszerű szolgáltatása:

```console
[...]
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
[...]
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Felhasználó által hozzárendelt identitás-hozzáférés megadása a key vaulthoz

Futtassa a következő [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) parancsot egy hozzáférési szabályzat beállításához a key vaulton. A következő példa lehetővé teszi, hogy a felhasználó által kijelölt identitás titkos kulcsokat szerezzen be a key vaultból:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Felhasználó által hozzárendelt identitás használata titkos beírása a kulcstartóból

Most már használhatja a felügyelt identitása a futó tárolópéldányon belül a key vault eléréséhez. Először indítson el egy bash shell a tartályban:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Futtassa a következő parancsokat a tárolóban lévő bash rendszerhéjban. Ha egy hozzáférési jogkivonatot szeretne beszerezni az Azure Active Directory kulcstárolón történő hitelesítéséhez, futtassa a következő parancsot:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Kimenet:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Ha a hozzáférési jogkivonatot egy olyan változóban szeretné tárolni, amelyet a hitelesítéshez a későbbi parancsokban kell használni, futtassa a következő parancsot:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Most használja a hozzáférési jogkivonatot a kulcstartó hitelesítéséhez, és egy titkos kulcsot olvas. Ügyeljen arra, hogy helyettesítse a kulcstartó nevét az URL-címben (*https://mykeyvault.vault.azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A válasz a következőhöz hasonlóan néz ki, és a titkos kulcsot mutatja. A kódban ezt a kimenetet a titkos fájl megszerzéséhez elemezné. Ezután használja a titkos kulcsot egy későbbi műveletben egy másik Azure-erőforrás eléréséhez.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>2. példa: Az Azure key vault eléréséhez használjon rendszeráltal hozzárendelt identitást

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Rendszeráltal hozzárendelt identitás engedélyezése tárolócsoportban

Futtassa a következő [az container create](/cli/azure/container?view=azure-cli-latest#az-container-create) parancsot `azure-cli` a Microsoft lemezképe alapján létrehozott tárolópéldány létrehozásához. Ebben a példában egy egytárolós csoportot biztosít, amely interaktív módon futtathatja az Azure CLI-t más Azure-szolgáltatások eléréséhez. 

A `--assign-identity` további érték nélküli paraméter lehetővé teszi a rendszer által hozzárendelt felügyelt identitást a csoporton. Az identitás hatóköre a tárolócsoport erőforráscsoportja. A hosszú ideig futó parancs tartja a tároló fut. Ez a példa ugyanazt az erőforráscsoportot használja a key vault létrehozásához, de megadhat egy másikat.

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

Pár másodpercen belül az üzembe helyezés befejezéséről tájékoztató választ kell kapnia az Azure CLI-ről. Ellenőrizze az állapotát az [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show) paranccsal.

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

A `identity` kimeneti szakasz a következőhöz hasonlóan néz ki, amely azt mutatja, hogy a rendszer által hozzárendelt identitás az Azure Active Directoryban jön létre:

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

Állítson be egy `principalId` változót az identitás (egyszerű szolgáltatásazonosítója) értékére, amelyet későbbi lépésekben kell használni.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Tárolócsoport-hozzáférés megadása a kulcstartóhoz

Futtassa a következő [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) parancsot egy hozzáférési szabályzat beállításához a key vaulton. A következő példa lehetővé teszi, hogy a rendszer által felügyelt identitás titkos kulcsokat szerezzen be a key vaultból:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Tárolócsoport-identitás használata titkos kulcstárolóból való titkos beíráshoz

Most már használhatja a felügyelt identitást a futó tárolópéldányon belüli kulcstartó eléréséhez. Először indítson el egy bash shell a tartályban:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Futtassa a következő parancsokat a tárolóban lévő bash rendszerhéjban. Első bejelentkezés az Azure CLI-be a felügyelt identitás használatával:

```bash
az login --identity
```

A futó tárolóból olvassa be a titkos kulcsot a kulcstárolóból:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

A rendszer lekéri a titkos kulcsot:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Felügyelt identitás engedélyezése az Erőforrás-kezelő sablonnal

Ha egy tárolócsoportban engedélyezni szeretné a felügyelt identitást `identity` egy `Microsoft.ContainerInstance/containerGroups` [Erőforrás-kezelő sablon](container-instances-multi-container-group.md)használatával, állítsa be az objektum tulajdonságát egy `ContainerGroupIdentity` objektummal. A következő kódrészletek `identity` a különböző esetekhez konfigurált tulajdonságot jelenítik meg. Tekintse meg az [Erőforrás-kezelő sablon hivatkozását](/azure/templates/microsoft.containerinstance/containergroups). Adja meg `apiVersion` `2018-10-01`a minimumot.

### <a name="user-assigned-identity"></a>Felhasználó által hozzárendelt identitás

A felhasználó által hozzárendelt identitás az űrlap erőforrásazonosítója:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Egy vagy több felhasználó által hozzárendelt identitást engedélyezhet.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Rendszerhez rendelt identitás

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Rendszer- és felhasználó által hozzárendelt identitások

Egy tárolócsoportban engedélyezheti a rendszer által hozzárendelt identitást és egy vagy több felhasználó által hozzárendelt identitást is.

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

## <a name="enable-managed-identity-using-yaml-file"></a>Felügyelt identitás engedélyezése YAML-fájlhasználatával

Ha engedélyezni szeretné a felügyelt identitást egy [YAML-fájl](container-instances-multi-container-yaml.md)használatával telepített tárolócsoportban, adja meg a következő YAML-fájlt.
Adja meg `apiVersion` `2018-10-01`a minimumot.

### <a name="user-assigned-identity"></a>Felhasználó által hozzárendelt identitás

A felhasználó által hozzárendelt identitás az űrlap erőforrásazonosítója 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Egy vagy több felhasználó által hozzárendelt identitást engedélyezhet.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Rendszerhez rendelt identitás

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Rendszer- és felhasználó által hozzárendelt identitások

Egy tárolócsoportban engedélyezheti a rendszer által hozzárendelt identitást és egy vagy több felhasználó által hozzárendelt identitást is.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben az Azure Container Instances felügyelt identitásairól és a következőkről szerzett tudomást:

> [!div class="checklist"]
> * Felhasználó által hozzárendelt vagy rendszerhez rendelt identitás engedélyezése tárolócsoportban
> * Az identitás-hozzáférés megadása egy Azure-kulcstartóhoz
> * A felügyelt identitás használatával egy futó tárolóból származó kulcstartó eléréséhez

* További információ [az Azure-erőforrások felügyelt identitásairól.](/azure/active-directory/managed-identities-azure-resources/)

* Tekintse meg az [Azure Go SDK példáját](https://medium.com/@samkreter/c98911206328) egy felügyelt identitás használatával az Azure Container Instances kulcstárolóeléréséhez.
