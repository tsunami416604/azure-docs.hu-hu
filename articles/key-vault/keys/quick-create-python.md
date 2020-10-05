---
title: Gyors útmutató – Azure Key Vault Python ügyféloldali kódtár – kulcsok kezelése
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet kulcsokat egy Azure Key vaultból a Python ügyféloldali kódtár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 44942067756f82c224decc218de17bf7dbc69734
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482127"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Gyors útmutató: Azure Key Vault kulcsok ügyféloldali kódtára a Pythonhoz

Ismerkedés a Azure Key Vault a Pythonhoz készült ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapszintű feladatokhoz tartozó kódot. A titkosítási kulcsok tárolásához Key Vault használatával elkerülhető az ilyen kulcsok tárolása a kódban, ami növeli az alkalmazás biztonságát.

[API-referenciák dokumentációja](/python/api/overview/azure/keyvault-keys-readme?view=azure-python)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)  |  [Csomag (Python-csomag indexe)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="set-up-your-local-environment"></a>A helyi környezet beállítása

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. A Key Vault kulcsok könyvtárának telepítése:

    ```terminal
    pip install azure-keyvault-keys
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>A szolgáltatás egyszerű hozzáférésének biztosítása a kulcstartóhoz

A következő az kulcstartó [set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) paranccsal engedélyezheti a szolgáltatásnév törlési, lekérési, listázási és létrehozási műveleteit a kulcsokon. 

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --key-permissions delete get list create
```

---

Ez a parancs az `KEY_VAULT_NAME` `AZURE_CLIENT_ID` előző lépésekben létrehozott és környezeti változókra támaszkodik.

További információ: [hozzáférési szabályzatok társítása –](../general/assign-access-policy-cli.md) parancssori felület

## <a name="create-the-sample-code"></a>A mintakód létrehozása

A Pythonhoz készült Azure Key Vault ügyféloldali kódtár lehetővé teszi a titkosítási kulcsok és a kapcsolódó eszközök, például tanúsítványok és titkos kódok kezelését. Az alábbi mintakód bemutatja, hogyan lehet ügyfelet létrehozni, titkos kulcsot beolvasni és titkos kulcsot törölni.

Hozzon létre egy *kv_keys.* -es nevű fájlt, amely tartalmazza ezt a kódot.

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>A kód futtatása

Győződjön meg arról, hogy az előző szakaszban található kód egy *kv_keys.* a. file nevű fájlban van. Ezután futtassa a kódot a következő paranccsal:

```terminal
python kv_keys.py
```

- Ha az engedélyek hibába ütközik, győződjön meg arról, hogy a [ `az keyvault set-policy` parancsot](#give-the-service-principal-access-to-your-key-vault)futtatta.
- Ha újra futtatja a kódot ugyanazzal a kulcsnévvel, a következő hibaüzenet jelenhet meg: "(ütközés) a kulcs <name> jelenleg törölve, de helyreállítható állapotban van." Használjon másik kulcsnévt.

## <a name="code-details"></a>Kód részletei

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Az előző kódban az [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) objektum az egyszerű szolgáltatásnév számára létrehozott környezeti változókat használja. Ezt a hitelesítő adatot akkor adja meg, amikor egy Azure-tárból hoz létre egy ügyféltanúsítványt, például az [`KeyClient`](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python) ügyfélen keresztül használni kívánt erőforrás URI-ját:

```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Kulcs mentése

Miután megszerezte a Key Vault ügyfél-objektumát, a [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-rsa-key-name----kwargs-) metódus használatával tárolhat egy kulcsot: 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

Használhatja [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-key-name--key-type----kwargs-) vagy [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#create-ec-key-name----kwargs-)is.

A metódus meghívása a `create` kulcstartó Azure REST API hívását hívja elő.

A kérelem kezelésekor az Azure hitelesíti a hívó identitását (az egyszerű szolgáltatásnév) az ügyfél számára megadott hitelesítőadat-objektum használatával.

Azt is ellenőrzi, hogy a hívó jogosult-e a kért művelet végrehajtására. Ezt az engedélyt a következő [ `az keyvault set-policy` paranccsal](#give-the-service-principal-access-to-your-key-vault)engedélyezte az egyszerű szolgáltatásnév számára:.

## <a name="retrieve-a-key"></a>Kulcs lekérése

Key Vaultból származó kulcs beolvasásához használja a [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#get-key-name--version-none----kwargs-) metódust:

```python
retrieved_key = client.get_key(keyName)
 ```

Azt is ellenőrizheti, hogy a kulcsot beállította-e az Azure CLI-parancs az [kulcstartó Key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show)paranccsal.

### <a name="delete-a-key"></a>Kulcs törlése

A kulcsok törléséhez használja a [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?view=azure-python#begin-delete-key-name----kwargs-) metódust:

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

A `begin_delete_key` metódus aszinkron, és egy Poller objektumot ad vissza. A Poller metódusának meghívása `result` megvárja a befejezését.

Ellenőrizze, hogy a kulcs törölve lett-e az Azure CLI-parancs az [kulcstartó Key show](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-show)használatával.

A törlés után a kulcsok törölve maradnak, de a helyreállítható állapot egy ideig. Ha újra futtatja a kódot, adjon meg egy másik kulcsot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a [tanúsítványokkal](../certificates/quick-create-python.md) és a [titkokkal](../secrets/quick-create-python.md)is kísérletezni szeretne, a cikkben létrehozott Key Vault is felhasználhatja.

Ellenkező esetben, ha elkészült az ebben a cikkben létrehozott erőforrásokkal, a következő paranccsal törölheti az erőforráscsoportot és a benne foglalt összes erőforrást:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>További lépések

- [Az Azure Key Vault áttekintése](../general/overview.md)
- [Azure Key Vault fejlesztői útmutató](../general/developers-guide.md)
- [Azure Key Vault ajánlott eljárások](../general/best-practices.md)
- [Hitelesítés Key Vault](../general/authentication.md)
