---
title: Rövid útmutató – Azure Key Vault-ügyféltár pythonhoz
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet tanúsítványokat egy Azure-kulcstárolóból a Python-ügyféltár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 3/30/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.openlocfilehash: 726cd76916510f38c7c14cb29e64449fb8fd539f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424705"
---
# <a name="quickstart-azure-key-vault-client-library-for-python"></a>Rövid útmutató: Azure Key Vault-ügyféltár pythonhoz

Ismerkedés az Azure Key Vault ügyféltár pythonhoz. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapvető feladatok példakódját.

Az Azure Key Vault segít a felhőalapú alkalmazások és szolgáltatások által használt titkosítási kulcsok és titkos kulcsok védelmében. Használja a Python Key Vault-ügyfélkönyvtárát a következőkhöz:

- Növelje a biztonságot és szabályozza a kulcsokat és jelszavakat.
- Titkosítási kulcsok létrehozása és importálása percek alatt.
- Csökkentse a késést a felhőalapú skálával és a globális redundanciával.
- Egyszerűsítse és automatizálja a TLS/SSL-tanúsítványok feladatait.
- Használja fips 140-2 Level 2 validált HSMs.

[API-referenciadokumentáció](/python/api/overview/azure/key-vault?view=azure-python) | [könyvtár forráskódjának](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault) | [csomagja (Python-csomagindex)](https://pypi.org/project/azure-keyvault/)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Python 2.7, 3.5.3 vagy újabb
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) vagy [Azure PowerShell](/powershell/azure/overview)

Ez a rövid útmutató feltételezi, hogy [az Azure CLI-t](/cli/azure/install-azure-cli?view=azure-cli-latest) linuxos terminálablakban futtatja.

## <a name="setting-up"></a>Beállítása

### <a name="install-the-package"></a>A csomag telepítése

A konzolablakból telepítse az Azure Key Vault tanúsítványtár python.

```console
pip install azure-keyvault-certificates
```

Ehhez a rövid útmutatóhoz telepítenie kell az azure.identity csomagot is:

```console
pip install azure.identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

Ez a rövid útmutató egy előre létrehozott Azure-kulcstartót használ. Az [Azure CLI gyorsútmutató](quick-create-cli.md), az [Azure PowerShell gyorsindítása](quick-create-powershell.md)vagy az Azure Portal gyorsindításlépéseit követve hozhat létre kulcstrekész [tárolót.](quick-create-portal.md) Másik lehetőségként futtathatja az Azure CLI-parancsokat alább.

> [!Important]
> Minden kulcstartónak egyedi névvel kell rendelkeznie. Cserélje le <egyedi keyvault-neve> a kulcstartó nevét az alábbi példákban.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

A felhőalapú .NET-alkalmazások hitelesítésének legegyszerűbb módja felügyelt identitás; A részleteket az Azure Key Vault eléréséhez az [App Service felügyelt identitásának használata.](../general/managed-identity.md) Az egyszerűség kedvéért azonban ez a rövid útmutató létrehoz egy .NET konzolalkalmazást. Egy asztali alkalmazás azure-ral történő hitelesítéséhez egyszerű szolgáltatásés hozzáférés-vezérlési szabályzat használata szükséges.

Hozzon létre egy szolgáltatási elvet az Azure CLI [az ad sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) paranccsal:

```azurecli
az ad sp create-for-rbac -n "http://mySP" --sdk-auth
```

Ez a művelet kulcs/ értékpárok sorozatát adja vissza. 

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

Vegye figyelembe az ügyfélazonosítót és az ügyféltitkos kulcsot, mivel az alábbi [Környezeti változó beállítása](#set-environmental-variables) lépésben fogjuk használni őket.

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>A szolgáltatásegyszerű hozzáférés a key vaulthoz

Hozzon létre egy hozzáférési szabályzatot a key vault, amely engedélyt ad a szolgáltatásnév átadásával az ügyfélazonosító az [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) parancs. Adja meg a szolgáltatásegyszerű leget, listát és hozzon létre engedélyeket a tanúsítványokhoz.

```azurecli
az keyvault set-policy -n <your-unique-keyvault-name> --spn <clientId-of-your-service-principal> --certificate-permissions delete get list create 
```

#### <a name="set-environmental-variables"></a>Környezeti változók beállítása

Az alkalmazásDefaultAzureCredential metódusa három környezeti változóra `AZURE_CLIENT_ID` `AZURE_CLIENT_SECRET`támaszkodik: , és `AZURE_TENANT_ID`. Állítsa be ezeket a változókat az ügyfélazonosító, az ügyféltitkos és a tenantId értékekre, amelyeket a `export VARNAME=VALUE` formátum használatával egyszerű szolgáltatás [létrehozása](#create-a-service-principal) lépésben észlelt. (Ez a módszer csak az aktuális rendszerhéj és a rendszerhéjból létrehozott folyamatok változóit állítja `/etc/environment ` be; hogy véglegesen hozzáadja ezeket a változókat a környezethez, és szerkesztheti a fájlt.) 

A kulcstartó nevét is mentenie kell egy `KEY_VAULT_NAME`környezeti változóként, amelynek neve .

```console
export AZURE_CLIENT_ID=<your-clientID>

export AZURE_CLIENT_SECRET=<your-clientSecret>

export AZURE_TENANT_ID=<your-tenantId>

export KEY_VAULT_NAME=<your-key-vault-name>
````

## <a name="object-model"></a>Objektummodell

Az Azure Key Vault-ügyféltár python lehetővé teszi a kulcsok és a kapcsolódó eszközök, például a tanúsítványok és a titkos kulcsok kezelését. Az alábbi kódminták bemutatják, hogyan hozhat létre ügyfelet, hozhat létre tanúsítványt, kérhet be egy tanúsítványt, és hogyan törölheti a tanúsítványt.

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Direktívák hozzáadása

Adja hozzá a következő irányelveket a kód tetejéhez:

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

A kulcstartóba való hitelesítés és a key vault-ügyfél létrehozása a környezeti változóktól függ a [fenti környezeti változók](#set-environmental-variables) beállításában. A kulcstartó neve ki van bontva a key vault URI-ra a "https://<your-key-vault-name>.vault.azure.net" formátumban.

```python
credential = DefaultAzureCredential()

client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Tanúsítvány mentése

Most, hogy az alkalmazás hitelesítése befejeződött, önaláírt tanúsítványt helyezhet el a keyvaultban 

```python
certificate_operation_poller = client.begin_create_certificate(
    certificate_name=certificateName, policy=CertificatePolicy.get_default()
)
certificate = certificate_operation_poller.result()
```

Ellenőrizheti, hogy a tanúsítvány be van-e állítva az [az keyvault tanúsítványmegjelenítési](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show) paranccsal:

```azurecli
az keyvault certificate show --vault-name <your-unique-keyvault-name> --name myCertificate
```

### <a name="retrieve-a-certificate"></a>Tanúsítvány beolvasása

Most már bekeresheti a korábban létrehozott tanúsítványt

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

A tanúsítvány most `retrieved_certificate`a program a program részeként kerül mentésre.

### <a name="delete-a-certificate"></a>Tanúsítvány törlése

Végül töröljük a tanúsítványt a kulcstartóból

```python
client.delete_certificate(certificateName)
```

Ellenőrizheti, hogy a tanúsítvány [eltűnt-e az az keyvault tanúsítványmegjelenítési](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-show) paranccsal:

```azurecli
az keyvault certifcate show --vault-name <your-unique-keyvault-name> --name myCertificate
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség, használhatja az Azure CLI vagy az Azure PowerShell a key vault és a megfelelő erőforráscsoport eltávolításához.

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

Ebben a rövid útmutatóban létrehozott egy key vaultot, tárolt egy tanúsítványt, és beolvasni a tanúsítványt. Ha többet szeretne megtudni a Key Vaultról és arról, hogyan integrálhatja azt az alkalmazásokkal, folytassa az alábbi cikkekkel.

- Az [Azure Key Vault áttekintésének elolvasása](../general/overview.md)
- Tekintse meg az [Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- Tekintse át az [Azure Key Vault gyakorlati tanácsait](../general/best-practices.md)
