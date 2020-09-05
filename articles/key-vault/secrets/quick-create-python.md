---
title: Gyors útmutató – Azure Key Vault Python ügyféloldali kódtár – titkok kezelése
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet titkos kódokat az Azure Key vaultban a Python ügyféloldali kódtár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: cd8a5751c018b9b3b3b2ef96765545f2edab685b
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489204"
---
# <a name="quickstart-azure-key-vault-secrets-client-library-for-python"></a>Gyors útmutató: Azure Key Vault Secrets ügyféloldali kódtár a Pythonhoz

Ismerkedés a Azure Key Vault a Pythonhoz készült ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapszintű feladatokhoz tartozó kódot. A titkok tárolása Key Vault használatával elkerülhető, hogy a kódban ne tárolja a titkokat, ami növeli az alkalmazás biztonságát.

[API-referenciák dokumentációja](/python/api/overview/azure/keyvault-secrets-readme?view=azure-python)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)  |  [Csomag (Python-csomag indexe)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="set-up-your-local-environment"></a>A helyi környezet beállítása

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Telepítse a Key Vault Secrets könyvtárat:

    ```terminal
    pip install azure-keyvault-secrets
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>A szolgáltatás egyszerű hozzáférésének biztosítása a kulcstartóhoz

Futtassa a következőt az a kulcstartó [set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) paranccsal, hogy engedélyezze az egyszerű szolgáltatásnév számára a Get, a List és a Secrets műveletekre vonatkozó beállításokat. Ez a parancs az `KEY_VAULT_NAME` `AZURE_CLIENT_ID` előző lépésekben létrehozott és környezeti változókra támaszkodik.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --secret-permissions delete get list set 
```

---

Ez a parancs az `KEY_VAULT_NAME` `AZURE_CLIENT_ID` előző lépésekben létrehozott és környezeti változókra támaszkodik.

További információ: [hozzáférési szabályzatok társítása –](../general/assign-access-policy-cli.md) parancssori felület

## <a name="create-the-sample-code"></a>A mintakód létrehozása

A Pythonhoz készült Azure Key Vault ügyféloldali kódtár lehetővé teszi a titkok és a kapcsolódó eszközök, például a tanúsítványok és a titkosítási kulcsok kezelését. Az alábbi mintakód bemutatja, hogyan lehet ügyfelet létrehozni, titkos kulcsot beolvasni és titkos kulcsot törölni.

Hozzon létre egy *kv_secrets.* -es nevű fájlt, amely tartalmazza ezt a kódot.

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = input("Input a name for your secret > ")
secretValue = input("Input a value for your secret > ")

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...")

client.set_secret(secretName, secretValue)

print(" done.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>A kód futtatása

Győződjön meg arról, hogy az előző szakaszban található kód egy *kv_secrets.* a. file nevű fájlban van. Ezután futtassa a kódot a következő paranccsal:

```terminal
python kv_secrets.py
```

- Ha az engedélyek hibába ütközik, győződjön meg arról, hogy a [ `az keyvault set-policy` parancsot](#give-the-service-principal-access-to-your-key-vault)futtatta.
- A kód újbóli futtatása ugyanazzal a titkos névvel a következő hibaüzenetet eredményezheti: "(ütközés) a titkos kulcs <name> jelenleg törölve, de helyreállítható állapotban van." Használjon másik titkos nevet.

## <a name="code-details"></a>Kód részletei

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Az előző kódban az [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) objektum az egyszerű szolgáltatásnév számára létrehozott környezeti változókat használja. Ezt a hitelesítő adatot akkor adja meg, amikor egy Azure-tárból hoz létre egy ügyféltanúsítványt, például az [`SecretClient`](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python) ügyfélen keresztül használni kívánt erőforrás URI-ját:

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Titkos kód mentése

Miután megszerezte a Key vaulthoz tartozó ügyfél-objektumot, a [set_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#set-secret-name--value----kwargs-) metódussal tárolhatja a titkos kulcsot: 

```python
client.set_secret(secretName, secretValue)
```

A hívás a `set_secret` kulcstartóhoz tartozó Azure-REST API hívását hívja elő.

A kérelem kezelésekor az Azure hitelesíti a hívó identitását (az egyszerű szolgáltatásnév) az ügyfél számára megadott hitelesítőadat-objektum használatával.

Azt is ellenőrzi, hogy a hívó jogosult-e a kért művelet végrehajtására. Ezt az engedélyt a következő [ `az keyvault set-policy` paranccsal](#give-the-service-principal-access-to-your-key-vault)engedélyezte az egyszerű szolgáltatásnév számára:.

### <a name="retrieve-a-secret"></a>Titkos kód beolvasása

Key Vault titkos kulcsának beolvasásához használja a [get_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#get-secret-name--version-none----kwargs-) metódust:

```python
retrieved_secret = client.get_secret(secretName)
 ```

A titkos érték a ben található `retrieved_secret.value` .

Az Azure CLI-paranccsal is beolvashat egy titkos kulcsot az kulcstartó [Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)parancs használatával.

### <a name="delete-a-secret"></a>Titkos kulcs törlése

A titkos kód törléséhez használja a [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?view=azure-python#begin-delete-secret-name----kwargs-) metódust:

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

A `begin_delete_secret` metódus aszinkron, és egy Poller objektumot ad vissza. A Poller metódusának meghívása `result` megvárja a befejezését.

Ellenőrizheti, hogy a titkos kulcs el lett-e távolítva az Azure CLI-parancs az kulcstartó [Secret show](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show)paranccsal.

A törlés után a titkos kód törölve marad, de egy ideig visszanyerhető állapotban van. Ha újra futtatja a kódot, használjon másik titkos nevet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a [tanúsítványokkal](../certificates/quick-create-python.md) és [kulcsokkal](../keys/quick-create-python.md)is kísérletezni szeretne, a cikkben létrehozott Key Vault is felhasználhatja.

Ellenkező esetben, ha elkészült az ebben a cikkben létrehozott erőforrásokkal, a következő paranccsal törölheti az erőforráscsoportot és a benne foglalt összes erőforrást:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>További lépések

- [Az Azure Key Vault áttekintése](../general/overview.md)
- [Azure Key Vault fejlesztői útmutató](../general/developers-guide.md)
- [Azure Key Vault ajánlott eljárások](../general/best-practices.md)
- [Hitelesítés Key Vault](../general/authentication.md)
