---
title: Gyors útmutató – Azure Key Vault a Pythonhoz készült ügyféloldali kódtár
description: Ismerje meg, hogyan hozhat létre, kérhet le és törölhet tanúsítványokat egy Azure Key vaultból a Python ügyféloldali kódtár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.openlocfilehash: 726cd76916510f38c7c14cb29e64449fb8fd539f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81424705"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>Rövid útmutató: Azure Key Vault a Pythonhoz készült ügyféloldali kódtár

Ismerkedés a Azure Key Vault a Pythonhoz készült ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapszintű feladatokhoz tartozó kódot.

Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. A Pythonhoz készült Key Vault ügyféloldali kódtára a következőre használható:

- Fokozza a biztonságot, és szabályozhatja a kulcsokat és a jelszavakat.
- Percek alatt létrehozhatja és importálhatja a titkosítási kulcsokat.
- Csökkentse a késést a felhő méretezésével és a globális redundanciával.
- Leegyszerűsítheti és automatizálhatja a TLS/SSL-tanúsítványok feladatait.
- Használja az FIPS 140-2 2-es szintű hitelesített HSM.

[API-referenciák dokumentációs](/python/api/overview/azure/key-vault?view=azure-python) | [könyvtár forráskód](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [-csomagja (Python-csomag indexe)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Python 2,7, 3.5.3 vagy újabb
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/overview)

Ez a rövid útmutató azt feltételezi, hogy az [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) -t egy Linux-terminál ablakban futtatja.

## <a name="setting-up"></a>Beállítás

### <a name="install-the-package"></a>A csomag telepítése

A konzol ablakban telepítse a Pythonhoz készült Azure Key Vault Certificates könyvtárat.

```console
pip install azure-keyvault-certificates
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

A felhőalapú .NET-alkalmazások hitelesítésének legegyszerűbb módja a felügyelt identitás; a részletekért tekintse meg a [app Service felügyelt identitás használata a Azure Key Vault eléréséhez](../general/managed-identity.md) című témakört. Az egyszerűség kedvéért azonban ez a rövid útmutató egy .NET-konzolos alkalmazást hoz létre. Egy asztali alkalmazás Azure-beli hitelesítéséhez egy egyszerű szolgáltatásnevet és egy hozzáférés-vezérlési szabályzatot kell használni.

Hozzon létre egy szolgáltatási elvet az Azure CLI az [ad SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) parancs használatával:

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
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

Hozzon létre egy olyan hozzáférési szabályzatot a kulcstartó számára, amely engedélyt ad a szolgáltatásnak a clientId az az a Key Vault [set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) paranccsal. Adja meg a szolgáltatás egyszerű lekérési, listázási és létrehozási engedélyeit a tanúsítványokhoz.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --certificate-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>Környezeti változók beállítása

Az alkalmazás DefaultAzureCredential metódusa három környezeti változóra támaszkodik: `AZURE_CLIENT_ID`, `AZURE_CLIENT_SECRET`és. `AZURE_TENANT_ID` Állítsa be ezeket a változókat a clientId, a clientSecret és a tenantId értékre, amelyet az [egyszerű szolgáltatásnév létrehozása](#create-a-service-principal) lépésben `export VARNAME=VALUE` jegyezte fel a formátum használatával. (Ez a módszer csak a rendszerhéjból létrehozott jelenlegi rendszerhéj és folyamatok változóit állítja be, hogy véglegesen hozzáadja ezeket a változókat a környezetéhez, `/etc/environment ` szerkessze a fájlt.) 

A kulcstároló nevét a nevű `KEY_VAULT_NAME`környezeti változó néven is menteni kell.

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objektummodell

A Pythonhoz készült Azure Key Vault ügyféloldali kódtár lehetővé teszi a kulcsok és a kapcsolódó eszközök, például tanúsítványok és titkos kódok kezelését. Az alábbi mintakód bemutatja, hogyan hozhat létre ügyfelet, hogyan hozhat létre tanúsítványt, kérhet le egy tanúsítványt, és törölhet egy tanúsítványt.

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Irányelvek hozzáadása

Adja hozzá a következő irányelveket a kód elejéhez:

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

A Key Vault hitelesítése és a Key Vault-ügyfél létrehozása a fenti [környezeti változók beállítása](#set-environmental-variables) című lépés környezeti változóktól függ. A kulcstartó neve a Key Vault URI-ra van kibontva, a "https://<your-key-Vault-Name>. vault.azure.net" formátumban.

```python
credential = DefaultAzureCredential()

client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Tanúsítvány mentése

Most, hogy az alkalmazás hitelesítése megtörtént, egy önaláírt tanúsítványt helyezhet a kulcstartóba. 

```python
certificate_operation_poller = client.begin_create_certificate(
    certificate_name=certificateName, policy=CertificatePolicy.get_default()
)
certificate = certificate_operation_poller.result()
```

Ellenőrizze, hogy a tanúsítvány be van-e állítva az az Key [Vault Certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show) paranccsal:

```azurecli
az keyvault certificate show --vault-name <your-unique-keyvault-name> --name myCertificate
```

### <a name="retrieve-a-certificate"></a>Tanúsítvány lekérése

Mostantól lekérheti a korábban létrehozott tanúsítványt

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

A tanúsítvány mostantól a következőképpen `retrieved_certificate`lett mentve:.

### <a name="delete-a-certificate"></a>Tanúsítvány törlése

Végül töröljük a tanúsítványt a kulcstartóból

```python
client.delete_certificate(certificateName)
```

A tanúsítvány az az Key [Vault Certificate show](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show) paranccsal ellenőrizhető:

```azurecli
az keyvault certifcate show --vault-name <your-unique-keyvault-name> --name myCertificate
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
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = "myCertificate"

print("Creating a certificate in " + keyVaultName + " called '" + certificateName  + "` ...")

certificate_operation_poller = client.begin_create_certificate(
    certificate_name=certificateName, policy=CertificatePolicy.get_default()

certificate = certificate_operation_poller.result()

print(" done.")

print("Retrieving your certificate from " + keyVaultName + ".")

retrieved_certificate = client.get_certificate(certificateName)

print("Certificate with name '{0}' was found'.".format(retrieved_certificate.name))
print("Deleting your certificate from " + keyVaultName + " ...")

client.delete_certificate(certificateName)

print(" done.")
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Key vaultot, tárolt egy tanúsítványt, és lekérte a tanúsítványt. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](../general/overview.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- [Azure Key Vault ajánlott eljárások](../general/best-practices.md) áttekintése
