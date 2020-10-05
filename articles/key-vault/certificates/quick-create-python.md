---
title: Gyors útmutató – Azure Key Vault Python ügyféloldali kódtár – tanúsítványok kezelése
description: Ismerje meg, hogyan hozhat létre, kérhet le és törölhet tanúsítványokat egy Azure Key vaultból a Python ügyféloldali kódtár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: b9ff7397ad29ac681e21c32608ade9c6ce557c37
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "89488626"
---
# <a name="quickstart-azure-key-vault-certificates-client-library-for-python"></a>Gyors útmutató: Azure Key Vault tanúsítványok kódtára a Pythonhoz

Ismerkedés a Azure Key Vault a Pythonhoz készült ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapszintű feladatokhoz tartozó kódot. Ha Key Vault használatával tárolja a tanúsítványokat, a kódban nem tárolja a tanúsítványokat, ami növeli az alkalmazás biztonságát.

[API-referenciák dokumentációja](/python/api/overview/azure/keyvault-certificates-readme?view=azure-python)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates)  |  [Csomag (Python-csomag indexe)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="set-up-your-local-environment"></a>A helyi környezet beállítása

[!INCLUDE [Set up your local environment](../../../includes/key-vault-python-qs-setup.md)]

7. Telepítse a Key Vault Certificates könyvtárat:

    ```terminal
    pip install azure-keyvault-certificates
    ```

## <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

## <a name="give-the-service-principal-access-to-your-key-vault"></a>A szolgáltatás egyszerű hozzáférésének biztosítása a kulcstartóhoz

A következő az kulcstartó [set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) paranccsal engedélyezheti a szolgáltatás számára a tanúsítványok lekérési, listázási és létrehozási műveleteit.

# <a name="cmd"></a>[cmd](#tab/cmd)

```azurecli
az keyvault set-policy --name %KEY_VAULT_NAME% --spn %AZURE_CLIENT_ID% --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create
```

# <a name="bash"></a>[bash](#tab/bash)

```azurecli
az keyvault set-policy --name $KEY_VAULT_NAME --spn $AZURE_CLIENT_ID --resource-group KeyVault-PythonQS-rg --certificate-permissions delete get list create 
```

---

Ez a parancs az `KEY_VAULT_NAME` `AZURE_CLIENT_ID` előző lépésekben létrehozott és környezeti változókra támaszkodik.

További információ: [hozzáférési szabályzatok társítása –](../general/assign-access-policy-cli.md) parancssori felület

## <a name="create-the-sample-code"></a>A mintakód létrehozása

A Pythonhoz készült Azure Key Vault ügyféloldali kódtár lehetővé teszi a tanúsítványok és a kapcsolódó eszközök, például a titkok és a titkosítási kulcsok kezelését. Az alábbi mintakód bemutatja, hogyan lehet ügyfelet létrehozni, titkos kulcsot beolvasni és titkos kulcsot törölni.

Hozzon létre egy *kv_certificates.* -es nevű fájlt, amely tartalmazza ezt a kódot.

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>A kód futtatása

Győződjön meg arról, hogy az előző szakaszban található kód egy *kv_certificates.* a. file nevű fájlban van. Ezután futtassa a kódot a következő paranccsal:

```terminal
python kv_certificates.py
```

- Ha az engedélyek hibába ütközik, győződjön meg arról, hogy a [ `az keyvault set-policy` parancsot](#give-the-service-principal-access-to-your-key-vault)futtatta.
- A kód újbóli futtatása ugyanazzal a kulcsnévvel lehet, hogy a (z) "(ütközés)" tanúsítvány <name> jelenleg törölve van, de helyreállítható állapotban van. " Használjon másik kulcsnévt.

## <a name="code-details"></a>Kód részletei

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Az előző kódban az [`DefaultAzureCredential`](/python/api/azure-identity/azure.identity.defaultazurecredential?view=azure-python) objektum az egyszerű szolgáltatásnév számára létrehozott környezeti változókat használja. Ezt a hitelesítő adatot akkor adja meg, amikor egy Azure-tárból hoz létre egy ügyféltanúsítványt, például az [`CertificateClient`](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python) ügyfélen keresztül használni kívánt erőforrás URI-ját:

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Tanúsítvány mentése

Miután megszerezte a Key Vault ügyfél-objektumát, létrehozhat egy tanúsítványt a [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-create-certificate-certificate-name--policy----kwargs-) metódus használatával: 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

Itt a tanúsítványhoz a [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?view=azure-python#get-default--) metódussal kapott szabályzat szükséges.

A metódus meghívásával `begin_create_certificate` aszinkron hívást generál a kulcstartó Azure-REST API. Az aszinkron hívás egy Poller objektumot ad vissza. A művelet eredményének megvárnia a Poller metódusának meghívásához `result` .

A kérelem kezelésekor az Azure hitelesíti a hívó identitását (az egyszerű szolgáltatásnév) az ügyfél számára megadott hitelesítőadat-objektum használatával.

Azt is ellenőrzi, hogy a hívó jogosult-e a kért művelet végrehajtására. Ezt az engedélyt a következő [ `az keyvault set-policy` paranccsal](#give-the-service-principal-access-to-your-key-vault)engedélyezte az egyszerű szolgáltatásnév számára:.

### <a name="retrieve-a-certificate"></a>Tanúsítvány lekérése

Key Vault tanúsítványának beolvasásához használja a [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#get-certificate-certificate-name----kwargs-) metódust:

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Azt is ellenőrizheti, hogy a tanúsítvány be van-e állítva az Azure CLI-paranccsal az Key [Vault Certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show)lehetőséggel.

### <a name="delete-a-certificate"></a>Tanúsítvány törlése

A tanúsítvány törléséhez használja a [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?view=azure-python#begin-delete-certificate-certificate-name----kwargs-) metódust:

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

A `begin_delete_certificate` metódus aszinkron, és egy Poller objektumot ad vissza. A Poller metódusának meghívása `result` megvárja a befejezését.

Ellenőrizze, hogy a tanúsítvány törölve lett-e az Azure CLI-paranccsal az Key [Vault Certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show)parancs használatával.

A törlés után a tanúsítvány törölve marad, de egy ideig is helyreállítható állapotban van. Ha újra futtatja a kódot, használjon másik nevet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a [titkokkal](../secrets/quick-create-python.md) és [kulcsokkal](../keys/quick-create-python.md)is kísérletezni szeretne, a cikkben létrehozott Key Vault is felhasználhatja.

Ellenkező esetben, ha elkészült az ebben a cikkben létrehozott erőforrásokkal, a következő paranccsal törölheti az erőforráscsoportot és a benne foglalt összes erőforrást:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>További lépések

- [Az Azure Key Vault áttekintése](../general/overview.md)
- [Azure Key Vault fejlesztői útmutató](../general/developers-guide.md)
- [Azure Key Vault ajánlott eljárások](../general/best-practices.md)
- [Hitelesítés Key Vault](../general/authentication.md)
