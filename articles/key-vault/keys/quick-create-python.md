---
title: Gyors útmutató – Azure Key Vault Python ügyféloldali kódtár – kulcsok kezelése
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet kulcsokat egy Azure Key vaultból a Python ügyféloldali kódtár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 1352d3fe4a13202a296b79fe82343c50a9e281d1
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400415"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Gyors útmutató: Azure Key Vault kulcsok ügyféloldali kódtára a Pythonhoz

Ismerkedés a Azure Key Vault a Pythonhoz készült ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapszintű feladatokhoz tartozó kódot.

Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A Pythonhoz készült Key Vault ügyféloldali kódtára a következőre használható:

- Fokozza a biztonságot, és szabályozhatja a kulcsokat és a jelszavakat.
- Percek alatt létrehozhatja és importálhatja a titkosítási kulcsokat.
- Csökkentse a késést a felhő méretezésével és a globális redundanciával.
- Leegyszerűsítheti és automatizálhatja a TLS/SSL-tanúsítványok feladatait.
- Használja az FIPS 140-2 2-es szintű hitelesített HSM.

[API-referenciák dokumentációja](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)  |  [Csomag (Python-csomag indexe)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2,7, 3.5.3 vagy újabb
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/)

Ez a rövid útmutató azt feltételezi, hogy az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) -t egy Linux-terminál ablakban futtatja.

## <a name="setting-up"></a>Beállítás

### <a name="install-the-package"></a>A csomag telepítése

A konzol ablakban telepítse a Pythonhoz készült Azure Key Vault Keys kódtárat.

```console
pip install azure-keyvault-keys
```

Ebben a rövid útmutatóban az Azure. Identity csomagot is telepítenie kell:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

Ez a rövid útmutató egy előre létrehozott Azure Key vaultot használ. Kulcstartó létrehozásához kövesse az [Azure CLI](quick-create-cli.md)gyors üzembe helyezésének lépéseit, [Azure PowerShell](quick-create-powershell.md)a gyors üzembe helyezést, vagy [Azure Portal](quick-create-portal.md)a gyors üzembe helyezést. Azt is megteheti, hogy az alábbi Azure CLI-parancsokat is futtatja.

> [!Important]
> Minden kulcstartónak egyedi névvel kell rendelkeznie. A következő példákban cserélje le a <az egyedi-kulcstartó-Name> a Key Vault nevét.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

A felhőalapú alkalmazások hitelesítésének legegyszerűbb módja a felügyelt identitás; a részletekért lásd: [hitelesítés Azure Key Vault](../general/authentication.md) . 

Az egyszerűség kedvéért azonban ez a rövid útmutató egy asztali alkalmazást hoz létre, amely egy egyszerű szolgáltatásnév és egy hozzáférés-vezérlési házirend használatát igényli. Az egyszerű szolgáltatásnév egyedi nevet igényel a "http:// &lt; My-Unique-Service-principal-name &gt; " formátumban.

Hozzon létre egy egyszerű szolgáltatást az Azure CLI az [ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancs használatával:

```azurecli
az ad sp create-for-rbac -n "http://&lt;my-unique-service-principal-name&gt;" --sdk-auth
```

A művelet a kulcs/érték párok sorozatát fogja visszaadni. 

```console
{
  "clientId": "7da18cae-779c-41fc-992e-0527854c6583",
  "clientSecret": "b421b443-1669-4cd7-b5b1-394d5c945002",
  "subscriptionId": "443e30da-feca-47c4-b68f-1636b75e16b3",
  "tenantId": "35ad10f1-7799-4766-9acf-f2d946161b77",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Jegyezze fel a clientId és a clientSecret, ahogy azokat az alábbi [környezeti változó beállítása](#set-environmental-variables) lépésben fogjuk használni.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>A szolgáltatás egyszerű hozzáférésének biztosítása a kulcstartóhoz

Hozzon létre egy olyan hozzáférési szabályzatot a kulcstartó számára, amely engedélyt ad a szolgáltatásnak a clientId az az a Key Vault [set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) paranccsal. Adja meg a szolgáltatás egyszerű lekérési, listázási és létrehozási engedélyeit a kulcsokhoz.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --key-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>Környezeti változók beállítása

Az alkalmazás DefaultAzureCredential metódusa három környezeti változóra támaszkodik: `AZURE_CLIENT_ID` , `AZURE_CLIENT_SECRET` és `AZURE_TENANT_ID` . Állítsa be ezeket a változókat a clientId, a clientSecret és a tenantId értékre, amelyet az [egyszerű szolgáltatásnév létrehozása](#create-a-service-principal) lépésben jegyezte fel a `export VARNAME=VALUE` formátum használatával. (Ez a módszer csak a rendszerhéjból létrehozott jelenlegi rendszerhéj és folyamatok változóit állítja be, hogy véglegesen hozzáadja ezeket a változókat a környezetéhez, szerkessze a `/etc/environment ` fájlt.) 

A kulcstároló nevét a nevű környezeti változó néven is menteni kell `KEY_VAULT_NAME` .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objektummodell

A Pythonhoz készült Azure Key Vault ügyféloldali kódtár lehetővé teszi a kulcsok és a kapcsolódó eszközök, például tanúsítványok és titkos kódok kezelését. Az alábbi mintakód bemutatja, hogyan hozhat létre egy ügyfelet, hogyan hozhat létre kulcsot, kérhet le egy kulcsot, és törölhet egy kulcsot.

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Irányelvek hozzáadása

Adja hozzá a következő irányelveket a kód elejéhez:

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

A Key Vault hitelesítése és a Key Vault-ügyfél létrehozása a fenti [környezeti változók beállítása](#set-environmental-variables) című lépés környezeti változóktól függ. A kulcstartó neve a Key Vault URI-ra van kibontva, a "https://<your-key-Vault-Name>. vault.azure.net" formátumban.

```python
credential = DefaultAzureCredential()

client = KeyClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-key"></a>Kulcs mentése

Most, hogy az alkalmazás hitelesítése megtörtént, a kulcsot elhelyezheti a kulcstartóban. 

```python
rsa_key = client.create_rsa_key(myKey,size=2048)
```

Ellenőrizze, hogy a kulcs be van-e állítva az az [kulcstartó Key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) paranccsal:

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

### <a name="retrieve-a-key"></a>Kulcs lekérése

Mostantól lekérheti a korábban létrehozott kulcsot

```python
retrieved_key = client.get_key(keyName)
print(retrieve_key.name)

 ```

A rendszer most menti a kulcsot `retrieved_key` .

### <a name="delete-a-key"></a>Kulcs törlése

Végül törölje a kulcsot a Key vaultból

```python
client.delete_key(keyName)
```

Ellenőrizze, hogy a kulcs el lett-e mentve az az [kulcstartó Key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show) paranccsal:

```azurecli
az keyvault key show --vault-name <your-unique-keyvault-name> --name myKey
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja az Azure CLI-t vagy Azure PowerShell a kulcstartó és a hozzá tartozó erőforráscsoport eltávolításához.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Mintakód

```python
import os
from azure.keyvault.key import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = "myKey"

print("Creating a key in " + keyVaultName + " called '" + keyName  + "` ...")

rsa_key = client.create_rsa_key(myKey,size=2048)

print(" done.")

print("Retrieving your key from " + keyVaultName + ".")

retrieved_key = client.get_key(keyName)

print("Key with name '{0}' was found'.".format(retrieved_key.name))
print("Deleting your key from " + keyVaultName + " ...")

client.begin_delete_key(keyName).result()

print(" done.")
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key vaultot, tárolt egy kulcsot, és lekérte a kulcsot. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [Az Azure Key Vault áttekintése](../general/overview.md)
- [Azure Key Vault fejlesztői útmutató](../general/developers-guide.md)
- [Azure Key Vault ajánlott eljárások](../general/best-practices.md)
