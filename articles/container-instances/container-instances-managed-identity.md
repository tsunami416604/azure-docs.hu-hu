---
title: Felügyelt identitás használata Azure Container Instances
description: Megtudhatja, hogyan használhat felügyelt identitást más Azure-szolgáltatásokkal való hitelesítéshez Azure Container Instances.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 10/22/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 773650e5e5e85d4a5fca0b3755f3730921cc5f2e
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325931"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Felügyelt identitások használata Azure Container Instances

Felügyelt identitások használata az [Azure](../active-directory/managed-identities-azure-resources/overview.md) -erőforrásokhoz olyan Azure Container instances kód futtatásához, amely más Azure-szolgáltatásokkal is együttműködik – anélkül, hogy az összes titkot vagy hitelesítő adatot megtartja a kódban. A szolgáltatás Azure Container Instances központi telepítést biztosít automatikusan felügyelt identitással Azure Active Directoryban.

Ebben a cikkben további információt talál a felügyelt identitásokról Azure Container Instances és:

> [!div class="checklist"]
> * Felhasználó által hozzárendelt vagy rendszerhez rendelt identitás engedélyezése egy tároló csoportban
> * Identitás hozzáférésének biztosítása egy Azure Key Vaulthoz
> * A felügyelt identitás használata egy Key Vault egy futó tárolóból való eléréséhez

A példákkal a többi Azure-szolgáltatás eléréséhez az identitásokat engedélyezheti és használhatja Azure Container Instancesban. Ezek a példák interaktívak. A gyakorlatban azonban a tároló lemezképei programkódot futtatnak az Azure-szolgáltatások eléréséhez.

> [!NOTE]
> Jelenleg nem használhat felügyelt identitást egy virtuális hálózatra központilag telepített tároló csoportba.

## <a name="why-use-a-managed-identity"></a>Miért érdemes felügyelt identitást használni?

Felügyelt identitást használhat egy futó tárolóban bármely olyan szolgáltatás hitelesítéséhez, [amely támogatja az Azure ad-hitelesítést](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) , és nem kezeli a hitelesítő adatokat a tároló kódjában. Az AD-hitelesítést nem támogató szolgáltatások esetén a titkos kulcsokat a Azure Key Vaultban tárolhatja, és a felügyelt identitás használatával elérheti Key Vault a hitelesítő adatok lekéréséhez. A felügyelt identitás használatával kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt. A felügyelt identitások jelenleg csak a Linux Container instances szolgáltatásban támogatottak.
>  

### <a name="enable-a-managed-identity"></a>Felügyelt identitás engedélyezése

 Azure Container Instances az Azure-erőforrások felügyelt identitásai a 2018-10-01-es és a hozzájuk tartozó SDK-k és eszközök REST API-es verziójában támogatottak. Amikor létrehoz egy tároló csoportot, egy vagy több felügyelt identitást egy [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) tulajdonság beállításával engedélyezhet. Engedélyezheti vagy frissítheti a felügyelt identitásokat egy tároló csoport futtatása után is. vagy a művelet hatására a tároló csoport újraindul. Az identitások új vagy meglévő YAML való beállításához használja az Azure CLI-t, egy Resource Manager-sablont vagy egy-fájlt. 

Azure Container Instances a felügyelt Azure-identitások mindkét típusát támogatja: felhasználó által hozzárendelt és rendszerhez rendelt. Egy tároló csoporton engedélyezheti a rendszerhez rendelt identitást, egy vagy több felhasználó által hozzárendelt identitást vagy mindkét típusú identitást. 

* A **felhasználó által hozzárendelt** felügyelt identitás önálló Azure-erőforrásként jön létre azon az Azure ad-bérlőn, amelyet a használatban lévő előfizetés megbízhatónak tekint. Az identitás létrehozása után az identitás hozzá lehet rendelni egy vagy több Azure-erőforráshoz (Azure Container Instances vagy más Azure-szolgáltatásban). A felhasználó által hozzárendelt identitás életciklusa külön van kezelve a tároló-csoportok vagy más olyan szolgáltatási erőforrások életciklusa alapján, amelyekhez hozzá van rendelve. Ez a viselkedés különösen Azure Container Instancesban hasznos. Mivel az identitás a tárolói csoportok élettartama után is túlnyúlik, újra felhasználhatja azt más szabványos beállításokkal, hogy a Container Group-példányok könnyen ismételhetők legyenek.

* A **rendszer által hozzárendelt** felügyelt identitások közvetlenül a Azure Container instances lévő tároló csoporton vannak engedélyezve. Ha engedélyezve van, az Azure létrehoz egy identitást a csoport számára az Azure AD-bérlőben, amelyet a példány előfizetése megbízhatónak tekint. Az identitás létrehozása után a hitelesítő adatokat a rendszer a tároló csoport minden tárolójában kiépíti. A rendszerhez rendelt identitás életciklusa közvetlenül ahhoz a tároló csoportjához van kötve, amelyhez engedélyezve van. A csoport törlésekor az Azure automatikusan törli a hitelesítő adatokat és az identitást az Azure AD-ben.

### <a name="use-a-managed-identity"></a>Felügyelt identitás használata

Felügyelt identitás használatához az identitásnak kezdetben hozzáférést kell biztosítania egy vagy több Azure szolgáltatási erőforráshoz (például egy webalkalmazáshoz, egy Key Vaulthoz vagy egy Storage-fiókhoz) az előfizetésben. Egy futó tárolóból származó Azure-erőforrások eléréséhez a kódnak egy Azure AD-végponttól származó *hozzáférési* jogkivonatot kell bekérnie. Ezt követően a kód elküldi a hozzáférési tokent egy olyan szolgáltatás hívására, amely támogatja az Azure AD-hitelesítést. 

Egy futó tárolóban lévő felügyelt identitás lényegében ugyanaz, mint egy Azure-beli virtuális gép identitásának használata. Tekintse meg a virtuális gépekkel kapcsolatos útmutatót [token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure POWERSHELL vagy Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)vagy az [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)-k használatával.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a cikkhez az Azure CLI 2.0.49 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;

A cikkben szereplő példák felügyelt identitást használnak Azure Container Instances egy Azure Key Vault titkos kulcs eléréséhez. 

Először hozzon létre egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen a következő az [Group Create](/cli/azure/group?view=azure-cli-latest#az-group-create) paranccsal:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Key Vault létrehozásához használja az az kulcstartó [létrehozása](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) parancsot. Ügyeljen arra, hogy egyedi Key Vault nevet adjon meg. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Hozzon létre egy minta titkot a Key Vault az az Key [Vault Secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) paranccsal:

```azurecli-interactive
az keyvault secret set --name SampleSecret --value "Hello Container Instances!" --description ACIsecret  --vault-name mykeyvault
```

Folytassa a következő példákkal, hogy a Key Vault a felhasználó által hozzárendelt vagy a rendszer által hozzárendelt felügyelt identitást használja Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>1\. példa: Felhasználó által hozzárendelt identitás használata a Azure Key Vaulthoz való hozzáféréshez

### <a name="create-an-identity"></a>Identitás létrehozása

Először hozzon létre egy identitást az előfizetésben az az [Identity Create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) paranccsal. Használhatja ugyanazt az erőforráscsoportot, amely a Key Vault létrehozására szolgál, vagy használhat egy másikat is.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACIId
```

Ha az identitást a következő lépésekben szeretné használni, használja az az [Identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) parancsot az identitás egyszerű szolgáltatásnév és erőforrás-azonosítójának a változókban való tárolásához.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Felhasználó által hozzárendelt identitás engedélyezése egy tároló csoporton

Futtassa a következőt az [Container Create](/cli/azure/container?view=azure-cli-latest#az-container-create) paranccsal egy, az Ubuntu Serveren alapuló Container-példány létrehozásához. Ez a példa egy egytárolós csoportot biztosít, amely segítségével interaktív módon férhet hozzá más Azure-szolgáltatásokhoz. A `--assign-identity` paraméter a felhasználó által hozzárendelt felügyelt identitást továbbítja a csoportnak. A hosszan futó parancs megtartja a tárolót. Ez a példa ugyanazt az erőforráscsoportot használja, amely a Key Vault létrehozásához használatos, de megadhat egy másikat.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity $resourceID --command-line "tail -f /dev/null"
```

Pár másodpercen belül az üzembe helyezés befejezéséről tájékoztató választ kell kapnia az Azure CLI-ről. Az az [Container show](/cli/azure/container?view=azure-cli-latest#az-container-show) paranccsal tekintse meg az állapotát.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A kimenetben található szakasz a következőhöz hasonlóan néz ki, ami azt mutatja, hogy az identitás be van állítva a Container csoportban. `identity` A `principalID` alatt`userAssignedIdentities` a Azure Active Directoryban létrehozott identitás egyszerű szolgáltatásnév:

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

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Felhasználó által hozzárendelt identitás elérésének engedélyezése a Key Vaulthoz

Futtassa a következőt az az kulcstartó [set-Policy](/cli/azure/keyvault?view=azure-cli-latest) paranccsal, hogy a hozzáférési szabályzatot a Key Vaulton állítsa be. A következő példa lehetővé teszi, hogy a felhasználó által hozzárendelt identitás megszerezze a Key Vault titkait:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>A felhasználó által hozzárendelt identitás használata a titkos kulcs beszerzéséhez Key Vault

Most már használhatja a felügyelt identitást a futó tároló példányán lévő Key Vault eléréséhez. Ebben a példában először indítson el egy bash-rendszerhéjat a tárolóban:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Futtassa a következő parancsokat a tárolóban lévő bash-rendszerhéjban. A következő parancs futtatásával kérhet hozzáférési jogkivonatot a Azure Active Directory hitelesítéséhez Key Vaulthoz:

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

Most a hozzáférési jogkivonat használatával hitelesítheti Key Vault és beolvashatja a titkos kulcsot. Ügyeljen arra, hogy a Key Vault nevét az URL-ben ( *https://mykeyvault.vault.azure.net/...* ) cserélje le:

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A válasz a következőhöz hasonlóan néz ki, amely a titkos kulcsot mutatja. A kódban elemezheti ezt a kimenetet a titok beszerzéséhez. Ezután egy későbbi művelet titkát használva férhet hozzá egy másik Azure-erőforráshoz.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>2\. példa Rendszerhez rendelt identitás használata a Azure Key Vault eléréséhez

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Rendszer által hozzárendelt identitás engedélyezése egy tároló csoporton

Futtassa a következőt az [Container Create](/cli/azure/container?view=azure-cli-latest#az-container-create) paranccsal egy, az Ubuntu Serveren alapuló Container-példány létrehozásához. Ez a példa egy egytárolós csoportot biztosít, amely segítségével interaktív módon férhet hozzá más Azure-szolgáltatásokhoz. A `--assign-identity` további érték nélküli paraméter lehetővé teszi a rendszer által hozzárendelt felügyelt identitást a csoporton. A hosszan futó parancs megtartja a tárolót. Ez a példa ugyanazt az erőforráscsoportot használja, amely a Key Vault létrehozásához használatos, de megadhat egy másikat.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity --command-line "tail -f /dev/null"
```

Pár másodpercen belül az üzembe helyezés befejezéséről tájékoztató választ kell kapnia az Azure CLI-ről. Az az [Container show](/cli/azure/container?view=azure-cli-latest#az-container-show) paranccsal tekintse meg az állapotát.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

A kimenetben található szakasz a következőhöz hasonlóan néz ki, amely azt mutatja, hogy a rendszerhez hozzárendelt identitást Azure Active Directory hozza létre: `identity`

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

Állítson be egy változót az `principalId` identitás (egyszerű szolgáltatásnév) értékére a későbbi lépésekben való használathoz.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Tároló csoport hozzáférésének megadása a Key Vaulthoz

Futtassa a következőt az az kulcstartó [set-Policy](/cli/azure/keyvault?view=azure-cli-latest) paranccsal, hogy a hozzáférési szabályzatot a Key Vaulton állítsa be. A következő példa lehetővé teszi, hogy a rendszer által felügyelt identitás a Key Vault titkot kapjon:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>A tároló csoport identitásának használata titkos kód beszerzéséhez Key Vault

Most már használhatja a felügyelt identitást a futó tároló példányán lévő Key Vault eléréséhez. Ebben a példában először indítson el egy bash-rendszerhéjat a tárolóban:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Futtassa a következő parancsokat a tárolóban lévő bash-rendszerhéjban. A következő parancs futtatásával kérhet hozzáférési jogkivonatot a Azure Active Directory hitelesítéséhez Key Vaulthoz:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Kimenet:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Ha a hozzáférési tokent egy változóban szeretné tárolni a következő parancsokban a hitelesítéshez, futtassa a következő parancsot:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Most a hozzáférési jogkivonat használatával hitelesítheti Key Vault és beolvashatja a titkos kulcsot. Ügyeljen arra, hogy a Key Vault nevét a következő URL-címben (*https:\//mykeyvault.Vault.Azure.net/..* .) cserélje le:

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A válasz a következőhöz hasonlóan néz ki, amely a titkos kulcsot mutatja. A kódban elemezheti ezt a kimenetet a titok beszerzéséhez. Ezután egy későbbi művelet titkát használva férhet hozzá egy másik Azure-erőforráshoz.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Felügyelt identitás engedélyezése Resource Manager-sablon használatával

Ha egy [Resource Manager-sablonnal](container-instances-multi-container-group.md)szeretne felügyelt identitást engedélyezni egy tároló csoportban, állítsa `identity` az `Microsoft.ContainerInstance/containerGroups` objektum `ContainerGroupIdentity` tulajdonságát objektumra. A következő kódrészletek a `identity` különböző forgatókönyvekhez konfigurált tulajdonságot mutatják be. Tekintse meg a [Resource Manager-sablonok referenciáját](/azure/templates/microsoft.containerinstance/containergroups). Válasszon a- `2018-10-01`ból. `apiVersion`

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
Válasszon a- `2018-10-01`ból. `apiVersion`

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

## <a name="next-steps"></a>További lépések

Ebben a cikkben megtanulta a felügyelt identitásokat a Azure Container Instancesban, és a következőket:

> [!div class="checklist"]
> * Felhasználó által hozzárendelt vagy rendszerhez rendelt identitás engedélyezése egy tároló csoportban
> * Identitás hozzáférésének biztosítása egy Azure Key Vaulthoz
> * A felügyelt identitás használata egy Key Vault egy futó tárolóból való eléréséhez

* További információ az [Azure-erőforrások felügyelt identitásáról](/azure/active-directory/managed-identities-azure-resources/).

* Tekintse meg az [Azure go SDK példáját](https://medium.com/@samkreter/c98911206328) , amely felügyelt identitás használatával fér hozzá egy Key Vault a Azure Container instances.
