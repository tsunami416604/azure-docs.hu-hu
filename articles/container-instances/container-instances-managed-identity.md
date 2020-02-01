---
title: Felügyelt identitás engedélyezése a tároló csoportban
description: Megtudhatja, hogyan engedélyezheti a felügyelt identitást olyan Azure Container Instancesban, amelyek más Azure-szolgáltatásokkal is hitelesíthetők
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 003055d5021dd8ad7c3bab6d2900298ffd13b222
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901927"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Felügyelt identitások használata Azure Container Instances

[Felügyelt identitások használata az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md) olyan Azure Container instances kód futtatásához, amely más Azure-szolgáltatásokkal is együttműködik – anélkül, hogy az összes titkot vagy hitelesítő adatot megtartja a kódban. A szolgáltatás Azure Container Instances központi telepítést biztosít automatikusan felügyelt identitással Azure Active Directoryban.

Ebben a cikkben további információt talál a felügyelt identitásokról Azure Container Instances és:

> [!div class="checklist"]
> * Felhasználó által hozzárendelt vagy rendszerhez rendelt identitás engedélyezése egy tároló csoportban
> * Az identitás hozzáférésének biztosítása egy Azure Key vaulthoz
> * A felügyelt identitás használata egy futó tárolóból származó kulcstartó eléréséhez

A példákkal a többi Azure-szolgáltatás eléréséhez az identitásokat engedélyezheti és használhatja Azure Container Instancesban. Ezek a példák interaktívak. A gyakorlatban azonban a tároló lemezképei programkódot futtatnak az Azure-szolgáltatások eléréséhez.

> [!NOTE]
> Jelenleg nem használhat felügyelt identitást egy virtuális hálózatra központilag telepített tároló csoportba.

## <a name="why-use-a-managed-identity"></a>Miért érdemes felügyelt identitást használni?

Felügyelt identitást használhat egy futó tárolóban bármely olyan szolgáltatás hitelesítéséhez, [amely támogatja az Azure ad-hitelesítést](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) , és nem kezeli a hitelesítő adatokat a tároló kódjában. Az AD-hitelesítést nem támogató szolgáltatások esetében az Azure Key vaultban tárolhatja a titkos kulcsokat, és a felügyelt identitás használatával elérheti a Key vaultot a hitelesítő adatok lekéréséhez. A felügyelt identitás használatával kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt. A Azure Container Instances felügyelt identitások jelenleg csak Linux-tárolókkal és a Windows-tárolókkal még nem támogatottak.
>  

### <a name="enable-a-managed-identity"></a>Felügyelt identitás engedélyezése

 Azure Container Instances az Azure-erőforrások felügyelt identitásai a 2018-10-01-es és a hozzájuk tartozó SDK-k és eszközök REST API-es verziójában támogatottak. Amikor létrehoz egy tároló csoportot, egy vagy több felügyelt identitást egy [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) tulajdonság beállításával engedélyezhet. Emellett engedélyezheti vagy frissítheti a felügyelt identitásokat, miután egy tároló csoport fut – vagy a művelet hatására a tároló csoport újraindul. Az identitások új vagy meglévő YAML való beállításához használja az Azure CLI-t, egy Resource Manager-sablont vagy egy-fájlt. 

Azure Container Instances a felügyelt Azure-identitások mindkét típusát támogatja: felhasználó által hozzárendelt és rendszerhez rendelt. Egy tároló csoporton engedélyezheti a rendszerhez rendelt identitást, egy vagy több felhasználó által hozzárendelt identitást vagy mindkét típusú identitást. 

* A **felhasználó által hozzárendelt** felügyelt identitás önálló Azure-erőforrásként jön létre azon az Azure ad-bérlőn, amelyet a használatban lévő előfizetés megbízhatónak tekint. Az identitás létrehozása után az identitás hozzá lehet rendelni egy vagy több Azure-erőforráshoz (Azure Container Instances vagy más Azure-szolgáltatásban). A felhasználó által hozzárendelt identitás életciklusa külön van kezelve a tároló-csoportok vagy más olyan szolgáltatási erőforrások életciklusa alapján, amelyekhez hozzá van rendelve. Ez a viselkedés különösen Azure Container Instancesban hasznos. Mivel az identitás a tárolói csoportok élettartama után is túlnyúlik, újra felhasználhatja azt más szabványos beállításokkal, hogy a Container Group-példányok könnyen ismételhetők legyenek.

* A **rendszer által hozzárendelt** felügyelt identitások közvetlenül a Azure Container instances lévő tároló csoporton vannak engedélyezve. Ha engedélyezve van, az Azure létrehoz egy identitást a csoport számára az Azure AD-bérlőben, amelyet a példány előfizetése megbízhatónak tekint. Az identitás létrehozása után a hitelesítő adatokat a rendszer a tároló csoport minden tárolójában kiépíti. A rendszerhez rendelt identitás életciklusa közvetlenül ahhoz a tároló csoportjához van kötve, amelyhez engedélyezve van. A csoport törlésekor az Azure automatikusan törli a hitelesítő adatokat és az identitást az Azure AD-ben.

### <a name="use-a-managed-identity"></a>Felügyelt identitás használata

Felügyelt identitás használatához az identitásnak kezdetben hozzáférést kell biztosítania egy vagy több Azure szolgáltatási erőforráshoz (például egy webalkalmazáshoz, egy kulcstartóhoz vagy egy Storage-fiókhoz) az előfizetésben. Egy futó tárolóból származó Azure-erőforrások eléréséhez a kódnak egy Azure AD-végponttól származó *hozzáférési jogkivonatot* kell bekérnie. Ezt követően a kód elküldi a hozzáférési tokent egy olyan szolgáltatás hívására, amely támogatja az Azure AD-hitelesítést. 

Egy futó tárolóban lévő felügyelt identitás lényegében ugyanaz, mint egy Azure-beli virtuális gép identitásának használata. Tekintse meg a virtuális gépekkel kapcsolatos útmutatót [token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure POWERSHELL vagy Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)vagy az [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)-k használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0.49 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása

A cikkben szereplő példák felügyelt identitást használnak Azure Container Instances egy Azure Key Vault-titok eléréséhez. 

Először hozzon létre egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen a következő az [Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) paranccsal:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A Key Vault létrehozásához használja az az kulcstartó [létrehozása](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) parancsot. Ügyeljen arra, hogy egyedi kulcstároló-nevet adjon meg. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Hozzon létre egy minta titkos kulcsot a kulcstartóban az az Key [Vault Secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) paranccsal:

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Folytassa a következő példákkal a Key Vault elérését egy felhasználó által hozzárendelt vagy rendszer által hozzárendelt felügyelt identitás használatával Azure Container Instancesban.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>1\. példa: felhasználó által hozzárendelt identitás használata az Azure Key Vault eléréséhez

### <a name="create-an-identity"></a>Identitás létrehozása

Először hozzon létre egy identitást az előfizetésben az az [Identity Create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) paranccsal. Használhatja ugyanazt az erőforráscsoportot, amely a kulcstartó létrehozásához használatos, vagy egy másikat is használhat.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Ha az identitást a következő lépésekben szeretné használni, használja az az [Identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) parancsot az identitás egyszerű szolgáltatásnév és erőforrás-azonosítójának a változókban való tárolásához.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Felhasználó által hozzárendelt identitás engedélyezése egy tároló csoporton

A Microsoft `azure-cli` rendszerképe alapján hozzon létre egy tároló példányt a következő az [Container Create](/cli/azure/container?view=azure-cli-latest#az-container-create) paranccsal. Ez a példa egy egytárolós csoportot biztosít, amelyet interaktív módon használhat az Azure CLI-vel más Azure-szolgáltatásokhoz való hozzáféréshez. Ebben a szakaszban csak a alap Ubuntu operációs rendszer van használatban. 

A `--assign-identity` paraméter a felhasználó által hozzárendelt felügyelt identitást továbbítja a csoportnak. A hosszan futó parancs megtartja a tárolót. Ez a példa ugyanazt az erőforráscsoportot használja, amelyet a kulcstartó létrehozásához használtak, de megadhat egy másikat is.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Pár másodpercen belül az üzembe helyezés befejezéséről tájékoztató választ kell kapnia az Azure CLI-ről. Az az [Container show](/cli/azure/container?view=azure-cli-latest#az-container-show) paranccsal tekintse meg az állapotát.

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

A kimenet `identity` szakasza a következőhöz hasonlóan néz ki: az identitás beállítása a Container csoportban történik. A `userAssignedIdentities` alatt lévő `principalID` a Azure Active Directory-ben létrehozott identitás egyszerű szolgáltatásnév:

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

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Felhasználó által hozzárendelt identitás elérésének engedélyezése a kulcstartóhoz

A Key vaultra vonatkozó hozzáférési szabályzat beállításához futtassa a következőt az kulcstartó [set-Policy](/cli/azure/keyvault?view=azure-cli-latest) paranccsal. A következő példa lehetővé teszi, hogy a felhasználó által hozzárendelt identitás a Key vaultból kapjon titkos kódokat:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>A Key vaultból a felhasználó által hozzárendelt identitás használatával szerezheti be a titkos kulcsot

Most már használhatja a felügyelt identitást a futó tároló példányán a kulcstartó eléréséhez. Először indítson el egy bash-rendszerhéjat a tárolóban:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Futtassa a következő parancsokat a tárolóban lévő bash-rendszerhéjban. A következő parancs futtatásával kérhet hozzáférési tokent a Key Vault hitelesítéséhez Azure Active Directory használatával:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Kimenet:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Ha a hozzáférési tokent egy változóban szeretné tárolni a következő parancsokban a hitelesítéshez, futtassa a következő parancsot:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Most használja a hozzáférési tokent a Key Vault hitelesítéséhez, és olvassa el a titkos kulcsot. Ügyeljen arra, hogy a Key Vault nevét az URL-ben adja meg ( *https://mykeyvault.vault.azure.net/...* ):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A válasz a következőhöz hasonlóan néz ki, amely a titkos kulcsot mutatja. A kódban elemezheti ezt a kimenetet a titok beszerzéséhez. Ezután egy későbbi művelet titkát használva férhet hozzá egy másik Azure-erőforráshoz.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>2\. példa: a rendszer által hozzárendelt identitás használata az Azure Key Vault eléréséhez

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Rendszer által hozzárendelt identitás engedélyezése egy tároló csoporton

A Microsoft `azure-cli` rendszerképe alapján hozzon létre egy tároló példányt a következő az [Container Create](/cli/azure/container?view=azure-cli-latest#az-container-create) paranccsal. Ez a példa egy egytárolós csoportot biztosít, amelyet interaktív módon használhat az Azure CLI-vel más Azure-szolgáltatásokhoz való hozzáféréshez. 

A `--assign-identity` paraméter, amely nem rendelkezik további értékkel, lehetővé teszi a rendszerhez rendelt felügyelt identitás használatát a csoportban. Az identitás hatóköre a tároló csoport erőforráscsoporthoz tartozik. A hosszan futó parancs megtartja a tárolót. Ez a példa ugyanazt az erőforráscsoportot használja, amelyet a kulcstartó létrehozásához használtak, de megadhat egy másikat is.

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

Pár másodpercen belül az üzembe helyezés befejezéséről tájékoztató választ kell kapnia az Azure CLI-ről. Az az [Container show](/cli/azure/container?view=azure-cli-latest#az-container-show) paranccsal tekintse meg az állapotát.

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

A kimenet `identity` szakasza a következőhöz hasonlóan néz ki, amely azt mutatja, hogy a rendszer által hozzárendelt identitást hozza létre a Azure Active Directory:

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

Állítson be egy változót az identitás `principalId` (a szolgáltatás egyszerű azonosítója) értékére, amelyet a későbbi lépésekben használni szeretne.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Tároló csoport hozzáférésének engedélyezése a kulcstartóhoz

A Key vaultra vonatkozó hozzáférési szabályzat beállításához futtassa a következőt az kulcstartó [set-Policy](/cli/azure/keyvault?view=azure-cli-latest) paranccsal. A következő példa lehetővé teszi, hogy a rendszer által felügyelt identitás a Key vaultból beszerezze a titkos kulcsokat:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>A kulcstároló-identitás használata a Key vaultból való titkos kulcs beszerzéséhez

Mostantól a felügyelt identitás használatával férhet hozzá a kulcstárolóhoz a futó tároló példányán belül. Először indítson el egy bash-rendszerhéjat a tárolóban:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Futtassa a következő parancsokat a tárolóban lévő bash-rendszerhéjban. Először jelentkezzen be az Azure CLI-be a felügyelt identitás használatával:

```bash
az login --identity
```

A futó tárolóból kérje le a titkos kulcsot a Key vaultból:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

A titok értékét a rendszer lekéri:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Felügyelt identitás engedélyezése Resource Manager-sablon használatával

Ha egy [Resource Manager-sablonnal](container-instances-multi-container-group.md)szeretne felügyelt identitást engedélyezni egy tároló csoportban, állítsa be a `Microsoft.ContainerInstance/containerGroups` objektum `identity` tulajdonságát egy `ContainerGroupIdentity` objektummal. A következő kódrészletek a különböző forgatókönyvekhez konfigurált `identity` tulajdonságot mutatják be. Tekintse meg a [Resource Manager-sablonok referenciáját](/azure/templates/microsoft.containerinstance/containergroups). A `2018-10-01`minimális `apiVersion`ának meghatározása.

### <a name="user-assigned-identity"></a>Felhasználó által hozzárendelt identitás

A felhasználó által hozzárendelt identitás az űrlap erőforrás-azonosítója:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Egy vagy több felhasználó által hozzárendelt identitást is engedélyezhet.

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

### <a name="system--and-user-assigned-identities"></a>Rendszer-és felhasználó által hozzárendelt identitások

Egy tároló csoportban engedélyezheti a rendszerhez rendelt identitást és egy vagy több felhasználó által hozzárendelt identitást is.

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

## <a name="enable-managed-identity-using-yaml-file"></a>Felügyelt identitás engedélyezése a YAML-fájllal

Ahhoz, hogy egy [YAML-fájl](container-instances-multi-container-yaml.md)használatával üzembe helyezett tároló csoport felügyelt identitást engedélyezzen, a következő YAML kell megadnia.
A `2018-10-01`minimális `apiVersion`ának meghatározása.

### <a name="user-assigned-identity"></a>Felhasználó által hozzárendelt identitás

A felhasználó által hozzárendelt identitás az űrlap erőforrás-azonosítója. 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Egy vagy több felhasználó által hozzárendelt identitást is engedélyezhet.

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

### <a name="system--and-user-assigned-identities"></a>Rendszer-és felhasználó által hozzárendelt identitások

Egy tároló csoportban engedélyezheti a rendszerhez rendelt identitást és egy vagy több felhasználó által hozzárendelt identitást is.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta a felügyelt identitásokat a Azure Container Instancesban, és a következőket:

> [!div class="checklist"]
> * Felhasználó által hozzárendelt vagy rendszerhez rendelt identitás engedélyezése egy tároló csoportban
> * Az identitás hozzáférésének biztosítása egy Azure Key vaulthoz
> * A felügyelt identitás használata egy futó tárolóból származó kulcstartó eléréséhez

* További információ az [Azure-erőforrások felügyelt identitásáról](/azure/active-directory/managed-identities-azure-resources/).

* Tekintse meg az [Azure go SDK példáját](https://medium.com/@samkreter/c98911206328) , amely felügyelt identitás használatával fér hozzá egy kulcstartóhoz Azure Container Instancesról.
